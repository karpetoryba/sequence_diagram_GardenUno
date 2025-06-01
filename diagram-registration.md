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
  deactivate User
  deactivate Frontend

  %% Country Selection
  User->>Frontend: selectCountry()
  activate Frontend

  %% Send API Request to Backend
  Frontend->>Backend: [HTTP POST] /api/verify-country\nBody: { country }
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
        User->>Frontend: selectEmailMethod()
        activate User
        activate Frontend
        Frontend->>User: showMailForm()
        deactivate User
        deactivate Frontend

        User->>Frontend: fillMailForm()
        activate Frontend
        Frontend->>Backend: validateEmail(email)
        activate Backend
        deactivate Frontend

        Backend->>Database: checkEmailCredibility(email)
        deactivate Backend
        activate Database

        Database-->>Backend: valid/invalid
        deactivate Database
        activate Backend

        Backend-->>Frontend: emailStatus()
        deactivate Backend

        alt Email is invalid

            Frontend->>User: showValidationError("Invalid email")
            activate Frontend
            activate User

            Frontend->>User: showMailFormAgain()
            deactivate Frontend
            deactivate User

        else Email is valid

            Frontend->>User: showCaptcha()
            activate Frontend
            activate User

            User->>Frontend: solveCaptcha()
            deactivate User

            Frontend->>Backend: captchaValidation(response)
            activate Backend
            deactivate Frontend

            Backend->>Database: verifyCaptcha(response)
            activate Database
            Database-->>Backend: captchaValid/captchaInvalid
            deactivate Database
            deactivate Backend

            Backend-->>Frontend: captchaStatus()

            alt CAPTCHA invalid
                Frontend->>User: showError("CAPTCHA failed")
                Frontend->>User: showMailFormAgain()
            else CAPTCHA valid
                Backend->>Database: checkUserExists(email)
                Database-->>Backend: userExists: true/false

                alt User exists
                    Backend->>Database: createLoginCode(email)
                    Database-->>Backend: codeGenerated
                else User does not exist
                    Backend->>Database: createRegistrationCode(email)
                    Database-->>Backend: codeGenerated
                end

                Backend-->>Frontend: promptForCode()
                Frontend->>User: enterVerificationCode()
                User->>Frontend: submitCode()
                Frontend->>Backend: verifyCode(code)
                Backend->>Database: checkCode(code)
                Database-->>Backend: valid/expired/incorrect
                Backend-->>Frontend: codeStatus()

                alt Code expired or incorrect
                    Frontend->>User: showError("Invalid or expired code")
                    Frontend->>User: offerRetryOrChangeMethod()
                else Code is valid
                    Backend->>Database: activateUser(email)
                    Database-->>Backend: userActivated
                    Backend->>Frontend: sendWelcomeMail("Bienvenue")
                    Frontend->>User: showSuccess()
                end
            end
        end

    else User chooses Google
        User->>Frontend: selectGoogleMethod()
        Frontend->>Backend: signupWithGoogle(token)
        Backend->>Database: validateGoogleToken(token)
        Database-->>Backend: valid/invalid
        Backend-->>Frontend: tokenValidationResult()

        alt Google token invalid
            Backend-->>Frontend: registrationFailed("Google signup failed")
            Frontend->>User: showError("Google signup failed")
            Frontend->>User: showRegistrationMethodChoiceAgain()
        else Google token valid
            Backend->>Database: createOrUpdateUser(googleData)
            Database-->>Backend: userCreated/updated
            Backend-->>Frontend: registrationSuccess()
            Frontend->>User: showSuccess()
        end
    end

    User->>Frontend: loginToApplication()

```
