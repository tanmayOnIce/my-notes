## What do you mean by Spring security secures everything by default ?

When we add Spring security dependency every endpoint is secured that is it requires username and password (In this case the password that has been generated in console). Even if a endpoint is not defined it will still be unreachable without credential that is it will return status 401.

## How to configure path based authorization (User needs to provide credential for some paths rather than every path) ?

You need to create a bean of type SecurityFilterChain to define it in there rather than using _.antMatcher_ based approach user .

    authorizeRequest( authroizeConfig -> {

    }) 
based approach

## Create a simple Spring security Config class ?

    @Configuration
    @EnableWebSecurity
    public class SecurityConfig {

        @Bean
        public SecurityFilterChain securityFilterChain(HttpSecurity http) {
            return http
                    .authorizeRequests(authorizeConfig -> {
                        authroizeConfig.antMatcher("/private").authenticated();
                        authorizeConfig.anyRequest().permitAll();
                    })
                    .formLogin(withDefault())
                    .build() <---------------------------- This method returns Security Filter chain
        }

    }

## why do we need the bean of type of UserDetailsService ?

It acts a repository for users.

## Give an example of UserDetailsService bean ?

    @Bean
    public UserDetailsService userDetailsService() {
        return new InMemoryUserDetailsManager(
            User.Builder()
                .username("user")
                .password("{noop}password")
                .authorities("ROLE_user")
                .build()
        );
    }

## how to have openId ?
1. oauth-client dependencies.

2.in your SecurityFilterChain bean definition add
.oauth2Login(withDefaults())

3. Add your providers client-id and client-secret

## How to consume authentication ?

In your controller add Authentication parameter.

@GetMapping("/private")
public String privatePage(Authentication authentication) {
    return "Welcome to the VIP room ["+ authentication.getName()+"]";
}

## What is Authentication object ?

It is a bundle of credential that may or may not be authentication (verified by application). will mainly answers the question "who it is" . It is used for two things -
1. Authentication : Who is the user ?
2. Authorization : is the user is allowed to perform XYZ?

> Always use the high level abstraction


## What is SecurityContext ?
Thead local object that stores the Authentication. keep in mind that -
* Thread-local, will not propagate to child threads
* Cleared after requests is 100% processed.

## How spring filter chain works ?

                    client
                      |
                      |                                                we work here
        -----------------------------                  -------------------------------
        |            Filter              |                  |            Security Filter       |
        |              |                 |                  |                  |              |
        |              |                 |                  |                 |               |
        |    DelegatingFilterChain ------------------> |              Filters             |
        |        Bean Filter            |                 |                 |                | 
        |            |                  |                  |                 |               |
        |            |                  |                 |              Security Filter     |
        |          Filter               |                 |                                  |
        |            |                 |                 |                                  |
        |            |                 |                |                                   |
        |          Servlet             |                |                                  |
        ---------------------------                 ------------------------------ 

## Can there be multiple filter chain ?
Yes. If there is multiple filter chain you have to provide order. So the filter chain will be evaluated on order.

So you can do something like - 
http.antMatcher("/api")
    .authorizeRequest(...).build()

So that this FilterChain will only be considered for api starting with /api. If any api starts with /super then this filter chain will not be used. Will look for a less ordered filter chain.

You can define userDetailsService for each FilterChain.


## How a spring security filter looks like ?

    public void doFilter(HttpServletRequest request,HttpServletResponse response, FilterChain chain) {
        0. Should we execute the filter
        1. Before the request proceeds further (e.g. authentication or reject req)
        2. Invoke the rest of the chain
            chain.doFilter(request,response);
        3. Once the request has been fully processed do clean up related stuff (e.g. - cleanup)

    }

## are list of filters processed in loop ?
No they are processed in chain of responsibility pattern. So when a filter has called doFilter it will give execution control to FilterChain. Then it will increment the Filter Counter and call the next Filter.

The clean up part is done once all the request is processed by all the filter and application code. 

## Where can i find this code ?
In filterchainProxy class. Look at doFilterMethod

## what is default filter in spring security ?
GenericFilterBean, more generically OncePerRequestFilter

## how to add of spring security based filter ?

    public class RobotFilter extends OncePerRequestFilter {
        @Override
        protected void doFilterInternal(
            HttpServletRequest request,
            HttpServletResponse response,
            FilterChain filterChain
        ) throws  ServletException, IOException {
                if(!Collections.list(request.getHeaderNames()).contains("x-robot-password")) {
                    filterChain.doFilter(request,response);
                    return;
                }

                var  password = request.getHeader("x-robot-password");
                if("beep-boop".equals(password)) {
                    var newContext = SecurityContextHolder.createEmptyContext();
                    newContext.setAuthentication(new RobotAuthentication());
                    SecurityContextHolder.setContext(newContext);
                    filterChain.doFilter();
                    return;
                } else {
                    response.setStatus(HttpStatus.FORBIDDEN.value());
                    response.setCharacterEncoding("utf-8");
                    response.setHeader("Content-type","text/plain.charset-utf-8");
                    response.getWriter().println("You are not Mrs. Robot");
                    return;
                }

                
        }
    }

In your securityFilterChain bean

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) {
        return http.
                authenticateRequest(authroizeConfig -> {
                    authorizeConfig.antMatchers("/").permitAll();
                    authorizeConfig.anMatchers("/error").permitAll();
                    authorizeConfig.antMatchers("/fabicon.ico").permitAll();
                    authorizeConfig.anyRequest().authenticated();
                })
                .forLogin(withDefault())
                .oauth2Login(withDefault())
                .addFilter(new RobotFilter(),UsernamePasswordAuthenticationFilter.class)
                .build();
    }

## How to create authentication object ?
we need to extends the class with authentication. 

    public class RobotAuthentication implements Authentication {
        @Override
        public String getName() {
            return "Mrs Robot";
        }

        @Override
        public String getPricipal() {
            return "Mrs Robot";
        }

        @Override
        public boolean isAuthenticated() {
            return true;
        }

        @Override
        public void setAuthenticated() {
            throw new IllegalArgumentException("don't");
        }

        @Override
        public Object getDetails() {
            return null;
        }

        @Override
        public Object getCredentials() {
            return null;
        }

        @Override
        public Collection<? extends GrantedAuthority> getAuthorities() {
            return AuthorityUtils.createAuthorityList("ROLE_ROBOT");
        }
    }

    > There is **AuthenticationFilter** that says how to do authentication

## How authentication generally done in spring security ?

UsernamePasswordAuthenticationToken(username,password) -> authenticationManager(ProviderManager) -> UsernamePasswordAuthenticationToken(userDetails,Authorities)

## What is authenticationManager ?
It is responsible for authentication. 

## What is an implementaion of AuthenticationManager ? How it works ?

ProviderManager. It delegates authentication to Authentication providers (A list of Authentication Provider in most case).

## What is AuthenticationProvider ?
Main class responsible for checking authentication. Has two methods - 
1. Authenticate authenticte( Authentication authentication) throws AuthenticationException;

2. boolean supports(Class<?> authentication);

> filter chain has a ProviderManager inside.provider manager may have parent AuthenticationManager 


## How to use AuthenticationProvider ?
1. create a authentication provider.
   
    public class DanielAuthenticationProvider implements AuthenticationProvider {
        @Override
        public Authentication authenticate(Authentication authentication) throws AuthenticationException {
            var username = authentication.getName();
            if("daniel".equals(username)) {
                return new UsernamePasswordAuthenticationToken.authenticated("daniel",null,AuthorityUtils.createAuthority("ROLE_admin"));
            }
            return null;
        }

        @Override
        public boolean supports(Class<?> authentication) {
            return UsernamePasswordAuthenticationToken.class.isAssignableFrom(authentication);
        }
    }

2. Mount on security Filter chain

    @Bean
    public securityFilterChain(HttpSecurity http) throws Exception {
        return http.
            authorizeRequests(authorizeConfig -> {
                authorizeConfig.antMatchers("/").permitAll();
                authorizeConfig.antMatchers("/error").permitAll();
                authorizeConfig.antMatchers("/fabicon.ico").permitAll();
                authorizeConfig.anyRequest().authenticated();
            })
            .formLogin(withDefault())
            oauth2Login(withDefault())
            .addFilterBefor(new RobotFilter(),UsernamePasswordAuthenticationFilter.class)
            .authenticationProviders(new DainelAuthenticationProvider())
            .build();
    }

## How to listen when authentication sucess ?
We need a ApplicationListener.

@Bean
public ApplicationListener<AuthenticationSuccessEvent> successListener() {
    return event -> System.out.println("Authentication Success");
}

## How to do a filter properly ?

public class RobotAccountFilter extends OncePerRequestFilter {
    private final AuthenticationManager authenticationManager;
    
    public RobotAuthenticationFilter(AuthenticationManager authenticationManager) {
        this.authenticationManager = authenticationManager;
    }

    @Override
    protected void doFilterInternal(HttpServletRequest request,HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        if(!Collections.list(request.getHeaders()).contains("x-robot-password")) {
            filterChain.doFilter(request,response);
        }

        var password = request.getHeader("x-robot-password");
        var authRequest = RobotAuthentication.unauthenticated(password);

        try {
            var authentication = authenticationManager.authenticate(authRequest);

            var newContext = SecurityContextHolder.createEmptyContext();
            newContext.setAuthentication(authentication);
            filterChain.doFilter(request,response);
        } catch(AuthenticationException e) {
            response.setStatus(HttpStatus.FORBIDDEN.value());
            response.setCharacterEncoding("utf-8);
            response.setHeader("content-type","text/plain;charset=utf-8");
            response.getWriter().println("you are not mr Robot 🤖");
        }
    }
}

change in authentication object

public class RobotAuthentication implements Authentication {
    private final List<SimpleGrantedAuthority> authorities;
    private final boolean authenticated;
    private final String password;

    private RobotAuthentication(List<SimpleGrantedAuthority> authorities, String password) {
        this.authorities = Collections.unmodifiableList(authorities);
        this.authenticated = !CollectionUtils.isEmpty(authorities);
        this.password = password;
    }

    public static RobotAuthentication authenticated() {
        return new RobotAuthentication(List.of(new SimpleGrantedAuthority("ROLE_robot")),null);
    }

    public static RobotAuthentication authenticationToken(String password) {
        return new RobotAuthentication(Collections.emptyList(),password);
    }

    @Override
    public Object getPrincipal() {
        return "Mr Robot";
    }

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return authorities;
    }

    @Override
    public boolean isAuthenticated() {
        return authenticated;
    }

    @Override
    public Object getCredentials() {
        return password;
    }

    @Override
    public Object getDetails() {
        return null;
    }

    @Override
    public void setAuthenticated(boolean isAuthenticated) {
        throw new RuntimeException("Does not work LOL");
    }
}

Authentication Provider - 

public class RobotAuthenticationProvider implements AuthenticationProvider {

    private final List<String> passwords;

    public RobotAuthentication(List<Password> passwords) {
        this.passwords = passwords;
    }

    @Override
    public Authentication authenticate(Authentication authentication) throws AuthenticationException {
        var authRequest = (RobotAuthentication) authentication;
        var password = authRequest.getPassword();
        if(passwords.contain(password)) {
            return RobotAuthentication.authenticated();
        } else {
            throw new BadCredentialsException("You are not mrs robot 🤖");
        }
    }

    @Override
    public boolean supports(Class<?> authentication) {
        return RobotAuthentication.class.isAssigableFrom(authentication);
    }
}

In your SecurityFilterChain -

@Bean 
public SecurityFilterChain securityFilterChain(HttpSecurity http) {

    var authManager = new ProviderManager(new DanielAuthenticationProvider("beep-boop"));

    return http.
        authorizeRequests(authorizeConfig -> {
            authorizeConfig.antMatchers("/").permitAll();
            authorizeConfig.antMatchers("/error").permitAll();
            authorizeConfig.antMatchers("/fabicon.ico").permitAll();
            authorizeConfig.anyRequests().authenticated();
        })
        .fromLogin(withDefaults())
        .oauth2Login(withDefaults())
        .addFilterBefore(new RobotFilter(authManager),UsernamePasswordAuthenticationManager.class)
        .authenticationProvider(new DainelAuthenticationProvider())
        .build();
}

## why does there is a need for AuthenticationConfigurer ?
So that we do not need to manager ProviderManager,AuthenticationProvider,Filter in different place

## How to make a configurer ?

public class RobotLoginConfigurer extends AbstractHttpConfigurer<RobotLoginConfigurer,HttpSecurity> {
    
    private final List<String> passwords = new ArrayList<>();

    @Override
    public void init(HttpSecurity builder) throws Exception {
        // step 1
        // initializes a bunch of objects
        // authenticationProviders
        http.authenticationProvider(new RobotAuthenticationProvider(passwords));
    }

    @Override
    public void configure(HttpSecurity http) throws Exception {
        // step 2
        // this class initializes object but can use object from step 1
        // filters
        val authenticationManager = http.getSharedObject(AuthenticationManager.class); 
        http.addFilterBefore(new RobotFilter(authenticationManager),UsernamePasswordAuthenticationFilter.class);
    } 

    public RobotConfigurer password(String password) {
        this.passwords.add(password);
        return this;
    }

}

In SecurityFilterChain

@Bean
public SecurityFilterChain securityFilterChain(HttpSecurity httpSecurity) {

    var configurer = new RobotLoginConfigurer()
                        .password("beep-boop")
                        .passwod("boop-beep");
    
    return http.
             ..............
             .apply(configurer) ---> robotLoginConfigurer is being returnd
             .and() ---> then converts it to httpSecurity again
             .authenticationProvider(...)
             .build();

}


## what is postProcessor ? and give an example.

when we want to add any other activity to already existing authentication provider. We have to use composition. And delegate the call to the actual authenticationProvider 

It is used to apply rate-limiting,per-user, for google login.
max 1 login/minute/user


public class RateLimitedAuthenticationProvider implements AuthenticationProvider {
    private final AuthenticationProvider delegate;

    private final ConcurrentHashMap<String,Integer> cache = new ConcurrentHashMap<String,Instant>();

    public RateLimitedAuthenticationProvider(Authentication authentication) {
        this.delegate = authentication;
    }

    @Override
    public Authentication authenticate(Authentication authentication) throws AuthenticationException {
        var parentAuth = delegate.authenticate(authentication);

        if(parentAuth == null) {
            return null;
        }

        if(updateCache(parentAuth)) {
            return parentAuth;
        }
        throw new BadCredentialException("🤠 not so fast");

    }

    private boolean updateCache(Authentication parentAuth) {
        var now = Instant.now();
        var previousInstant = cache.get(parentAuth.getName());
        cache.put(parentAuth.getName(),now);

        return previousAuthTime == null || previousInstant.plus(Duration.ofMinutes(30)).isBefore(now);
    }

    @Override
    public boolean supports(Class<?> authentication) {
        return delegate.supports(authentication);
    }
}

in SecurityFilterChain

update .oauth2Login(withDefault())
to

.oauth2Login(oauth2Configurer -> {
    oauth2Configurer.withObjectPostProcessor(
        new ObjectPostProcessor<AuthenticationProvider>() {
            @Override
            public <o extends AuthenticationProvider> o postProcess(o object) {
                return (o) new RateLimitingAuthenticationProvider(object);
            }
        }
    );
})