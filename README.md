🔐 Basic Spring Security Authentication Flow

When a client sends a request to a secured endpoint in a Spring Boot application, Spring Security first intercepts the request using the SecurityFilterChain. This filter chain checks whether the incoming request matches any publicly accessible URL patterns (like /api/public/**) or if it requires authentication. If the endpoint is protected, Spring delegates the authentication process to the AuthenticationManager.

The AuthenticationManager internally uses an AuthenticationProvider, commonly a DaoAuthenticationProvider, to perform authentication. This provider calls the custom UserDetailsService (usually implemented by the developer) to load user details from the database based on the username received from the request.

Once the user is retrieved, Spring Security compares the raw password from the request with the encoded password stored in the database. This check is done inside the DaoAuthenticationProvider using the configured PasswordEncoder (e.g., BCrypt). The method responsible for this comparison is called additionalAuthenticationChecks().

If the credentials match, Spring creates an Authentication object and stores it in the SecurityContextHolder. This object now represents the authenticated user for the rest of the request. If any authorization is applied (such as role-based access with @PreAuthorize or hasRole()), Spring evaluates the roles before allowing the user to access the resource.

This entire flow is handled by Spring Security behind the scenes. As a developer, you only need to provide a SecurityConfiguration class, a UserDetailsService that pulls user data from your database, and a PasswordEncoder bean.

Incoming Request
     │
     ▼
SecurityFilterChain (checks auth rules)
     │
     ▼
AuthenticationManager
     │
     ▼
DaoAuthenticationProvider
     │
     ├──> Calls CustomUserDetailsService.loadUserByUsername()
     │         └──> Fetch user from DB
     │
     └──> Checks password:
              passwordEncoder.matches(rawPassword, encodedPasswordFromDB)
     │
     ▼
If valid:
→ SecurityContext updated
→ Controller executed
