The flow of Basic Spiring Security
Request comes in with Basic Auth credentials
          ↓
🔐 SecurityFilterChain intercepts the request
          ↓
AuthenticationManager (provided by Spring via your @Bean)
          ↓
DaoAuthenticationProvider is triggered
          ↓
1️⃣ DaoAuthenticationProvider calls your CustomUserDetailsService.loadUserByUsername(username)
    → This fetches the user from DB and returns a UserDetails object
          ↓
2️⃣ DaoAuthenticationProvider calls additionalAuthenticationChecks()
    → This compares:
       - Raw password from the request
       - Encoded password from DB (via UserDetails)
       - Uses PasswordEncoder.matches(raw, encoded)
          ↓
3️⃣ If matched → Authentication successful
    → SecurityContextHolder is updated
    → Controller method is invoked
