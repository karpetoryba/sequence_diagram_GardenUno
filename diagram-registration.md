```mermaid

sequenceDiagram
  actor User
  participant Frontend
  participant Backend
  participant Database

  %% Application Launch
  User->>Frontend: openApplication()
  activate User
  activate Frontend
  Frontend->>User: showCountrySelector()

  deactivate Frontend

  %% Country Selection
  User->>Frontend: selectCountry()
  activate Frontend
  deactivate User

  %% Send API Request to Backend
  Frontend->>Backend: [HTTP POST] /api/verify-country\nBody: { country }
  deactivate Frontend
  activate Backend

  %% Query the Database
  Backend->>Database: isCountryAllowed(country)
  activate Database
  Database-->>Backend: true / false
  deactivate Database

  %% Backend Response
  Backend-->>Frontend: [HTTP 200 OK]\nBody: { allowed: true, locale: "fr" }
  deactivate Backend

  %% Frontend Handling
  activate Frontend
  Frontend->>Frontend: changeLanguage("fr")
  Frontend-->>User: showResultInLanguage("Bienvenue", ...)
  deactivate Frontend


    alt User chooses Email
    %% Email method selection
        User->>Frontend: selectEmailMethod()
        activate User
        activate Frontend
        Frontend->>User: showMailForm()
        deactivate User
        deactivate Frontend

     %% Filling and submitting email form
        User->>Frontend: fillMailForm()
        activate Frontend

    %% Send request to backend
        Frontend->>Backend: [HTTP POST] /api/verify-email\nBody: { email }
        activate Backend
        deactivate Frontend

    %% Query database for email credibility
         Backend->>Database: checkEmailCredibility(email)
        deactivate Backend
        activate Database

        Database-->>Backend: valid/invalid
        deactivate Database
        activate Backend

    %% Return result to frontend
        Backend-->>Frontend: [HTTP 200 OK]\nBody: { status: "valid" | "invalid" }
  deactivate Backend

 %% Show result to user
      activate Frontend
      Frontend-->>User: showEmailStatus("Valid" or "Invalid")
      deactivate Frontend

        alt Email is invalid

            Frontend->>User: showValidationError("Invalid email")
            activate Frontend
            activate User

            Frontend->>User: showMailFormAgain()
            deactivate Frontend
            deactivate User

        else Email is valid

%% Show captcha form
            Frontend->>User: showCaptcha()
            activate Frontend
            activate User

%% User solves captcha
            User->>Frontend: solveCaptcha()
            deactivate User

            %% Send captcha response to backend
            Frontend->>Backend: [HTTP POST] /api/validate-captcha\nBody: { response }
            activate Backend
            deactivate Frontend

            %% Backend validates captcha via DB or service
            Backend->>Database: verifyCaptcha(response)
            activate Database
            Database-->>Backend: captchaValid / captchaInvalid
            deactivate Database


            %% Return captcha validation status to frontend
            Backend-->>Frontend: [HTTP 200 OK]\nBody: { captchaStatus: "valid" | "invalid" }
            deactivate Backend
            activate Frontend
             Frontend-->>User: showCaptchaResult("Valid" or "Invalid")
             deactivate Frontend



            alt CAPTCHA invalid

                Frontend->>User: showError("CAPTCHA failed")
                activate Frontend
                Frontend->>User: showMailFormAgain()
                deactivate Frontend

            else CAPTCHA valid
                 activate Frontend
                 Frontend->>Backend: GET /api/users/exists?email=email
                 deactivate Frontend
                 activate Backend
                 Backend->>Database: SELECT * FROM users WHERE email=email
                 activate Database
                 Database-->>Backend: { userExists: true/false }

                alt User exists
                Backend->>Database: INSERT INTO login_codes ...
                Database-->>Backend: { codeGenerated }
                else User does not exist
                Backend->>Database: INSERT INTO registration_codes ...
                Database-->>Backend: { codeGenerated }
             end
                deactivate Database

                Backend-->>Frontend: 200 OK { message: "Enter verification code" }
                deactivate Backend

                activate Frontend
                Frontend->>User: enterVerificationCode()

                activate User
                User->>Frontend: submitCode()
                deactivate User

                Frontend->>Backend: POST /api/auth/check-code { code }
                activate Backend
                deactivate Frontend

                Backend->>Database: SELECT * FROM verification_codes WHERE code=...
                activate Database
                Database-->>Backend: { status: valid/expired/incorrect }
                deactivate Database

                Backend-->>Frontend: { codeStatus }
                deactivate Backend



                alt Code expired or incorrect
                    Backend-->>Frontend: 400 Bad Request { error: "Invalid or expired code" }
                    activate Frontend
                    activate Backend
                    Frontend->>User: showError("Invalid or expired code")
                    deactivate Backend
                    Frontend->>User: offerRetryOrChangeMethod()



                else Code is valid
                     Backend-->>Frontend: 200 OK { message: "Code valid" }
                     activate Backend
                     deactivate Frontend
                    Frontend->>Backend: PUT /api/users/activate { email }
                    deactivate Backend
                    activate Frontend
                    activate Backend
                    Backend->>Database: UPDATE users SET active = true WHERE email=email
                    deactivate Frontend
                    activate Database
                    Database-->>Backend: { userActivated: true }
                    deactivate Database
                    Backend-->>Frontend: 200 OK { message: "User activated. Welcome mail sent." }
                    activate Frontend
                    deactivate Backend
                    Frontend->>User: showSuccess()
                    deactivate Frontend
                end
            end
        end

end

```
