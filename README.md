
# Django Custom Authentication Backend

This project provides a custom authentication backend for Django, featuring email-based user registration, OTP verification, email activation, password reset, and social authentication (Google login). The backend is built using Django Rest Framework (DRF) and Django's built-in user management system.

## Features

1. **Custom User Model**
   - Users authenticate via email instead of username.
   - The model includes fields for first name, last name, email, password, OTP (One-Time Password), and more.
   - Email verification and OTP authentication are supported.

2. **Registration and Email Verification**
   - Users can register with their email address.
   - Upon registration, an OTP is sent to the user's email for verification (if mandatory verification is enabled).
   - Users can verify their email by submitting the OTP or by clicking on an email activation link (configurable).

3. **Resending OTP and Email Activation**
   - Users can request a new OTP if the previous one expires.
   - The backend includes cooldown management to prevent too many OTP resend attempts.

4. **Password Reset**
   - Users can reset their password by requesting a password reset link via email.
   - The link includes a token for secure password resetting.

5. **JWT Authentication**
   - The project uses JSON Web Tokens (JWT) for secure user authentication.
   - A custom token is generated with additional user data (email) included.

6. **Social Authentication (Google Login)**
   - Google OAuth2 is integrated for social login, allowing users to sign in using their Google account.

7. **Logout and Token Blacklisting**
   - Users can log out and invalidate their JWT tokens.
   - Supports logging out from all sessions by blacklisting all tokens.

## Installation

1. **Clone the Repository:**
   ```bash
   git clone <repository-url>
   cd <repository-directory>
   ```

2. **Create and Activate Virtual Environment:**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install Dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

4. **Configure Environment Variables:**
   Create a `.env` file in your project root with the following variables:

   ```env
   # Database settings
   DATABASE_NAME=your_database_name
   DATABASE_USER=your_database_user
   DATABASE_PASSWORD=your_database_password
   DATABASE_HOST=your_database_host
   DATABASE_PORT=your_database_port
   
   # Django secret key
   SECRET_KEY=your_secret_key
   
   # Debug mode
   DEBUG=False
   
   # Email settings
   EMAIL_HOST=your_email_host
   EMAIL_PORT=your_email_port
   EMAIL_USE_TLS=True
   EMAIL_HOST_USER=your_email_host_user
   EMAIL_HOST_PASSWORD=your_email_host_password
   DEFAULT_FROM_EMAIL=your_default_from_email
   
   # Google OAuth settings
   GOOGLE_OAUTH_CLIENT_ID=your_google_oauth_client_id

   ```

5. **Run Migrations:**
   ```bash
   python manage.py makemigrations
   python manage.py migrate
   ```

6. **Create a Superuser:**
   ```bash
   python manage.py createsuperuser
   ```

7. **Start the Development Server:**
   ```bash
   python manage.py runserver
   ```

## Usage

### Registration and Email Verification
1. **Register a New User:**
   Send a POST request to `/api/register/` with the following payload:
   ```json
   {
     "email": "user@example.com",
     "first_name": "First",
     "last_name": "Last",
     "password": "password123"
   }
   ```

2. **Verify OTP:**
   Once the user registers, an OTP will be sent to their email. Send a POST request to `/api/verify-otp/` to verify the OTP:
   ```json
   {
     "email": "user@example.com",
     "otp": "123456"
   }
   ```

3. **Resend OTP:**
   If the OTP expires, you can resend it by making a POST request to `/api/resend-otp/`:
   ```json
   {
     "email": "user@example.com"
   }
   ```

4. **Activate Account via Email Link:**
   Alternatively, users can activate their account by clicking the link sent in the activation email.

### Password Reset
1. **Request Password Reset:**
   Send a POST request to `/api/password-reset/` with the user's email:
   ```json
   {
     "email": "user@example.com"
   }
   ```

2. **Reset Password:**
   Use the token from the email to reset the password via the `/api/password-reset-confirm/` endpoint.

### JWT Authentication
1. **Login and Obtain JWT Tokens:**
   Send a POST request to `/api/token/` with the user's email and password to get JWT access and refresh tokens.

2. **Logout:**
   Use the `/api/logout/` endpoint to invalidate the current session's JWT tokens.

3. **Logout All Sessions:**
   Use the `/api/logout-all/` endpoint to invalidate all active JWT tokens for the user.

### Social Authentication (Google Login)
1. **Google Login:**
   Send a POST request to `/api/google-login/` with the user's Google OAuth2 access token to authenticate via Google.

## API Endpoints

- 
accounts
- Register a new user.
```http
POST /accounts/api/register/
```
| Parameter    | Type     | Description          | Required  |
| :----------- | :------- | :------------------- | :-------- |
| `email`      | `string` | User email           | **Yes**   |
| `first_name` | `string` | User's first name    | No        |
| `last_name`  | `string` | User's last name     | No        |
| `password`   | `string` | User's password      | **Yes**   |

- Verify the OTP sent to the user's email.
```http
POST /accounts/api/verify-otp/
```
| Parameter | Type     | Description      | Required  |
| :-------- | :------- | :--------------- | :-------- |
| `email`   | `string` | User email       | **Yes**   |
| `otp`     | `string` | One-time password (OTP) | **Yes**   |

- Resend the OTP.
```http
POST /accounts/api/resend-otp/
```
| Parameter | Type     | Description   | Required  |
| :-------- | :------- | :------------ | :-------- |
| `email`   | `string` | User email    | **Yes**   |

- Obtain JWT tokens for login.
```http
POST /accounts/api/token/
```
| Parameter  | Type     | Description   | Required  |
| :--------- | :------- | :------------ | :-------- |
| `email`    | `string` | User email    | **Yes**   |
| `password` | `string` | User password | **Yes**   |

- Obtain JWT  refresh tokens for regenerate access token.
```http
POST /accounts/api/token/refresh/
```
| Parameter  | Type     | Description       | Required  |
| :--------- | :------- | :---------------- | :-------- |
| `refresh`  | `string` | Refresh token     | **Yes**   |

- Authenticate using Google OAuth2.
```http
POST /accounts/api/google/
```
| Parameter     | Type     | Description    | Required  |
| :------------ | :------- | :------------- | :-------- |
| `access_token` | `string` | Access token   | **Yes**   |

- Logout from the current session.
```http
POST /accounts/api/logout/
```
- Logout from all sessions.
```http
POST /accounts/api/logout_all/
```
- Request a password reset.
```http
POST /accounts/password-reset/
```
- Reset password with the token.
```http
POST /accounts/password-reset-confirm/{uidb64}/{token}/
```
- Activate account via email link.
```http
GET /accounts/api/activate/{uidb64}/{token}/
```
- Resend activation email
```http
POST /accounts/api/resend-activation-email/
```
- Get list of users
```http
GET /accounts/api/users/
```
- Get user details
```http
GET /accounts/api/users/{id}/
```

## Customization

- **Email Backend**: Configure the email backend in your `.env` file to set up email sending for OTP and activation emails.
- **JWT Settings**: Adjust the JWT token settings in `settings.py` for token expiration times, etc.
- **OTP Expiration**: You can configure the OTP expiration time through the `OTP_EXPIRATION_TIME` setting.

## Templates

- **Activation Email**: Located at `accounts/templates/accounts/activation_email.html`.
- **OTP Email Template**: Located at `accounts/templates/accounts/otp_email_template.html`.
- **Password Reset Email**: Located at `accounts/templates/accounts/password_reset_email.html`.

## Testing

1. **Run Unit Tests:**
   ```bash
   python manage.py test
   ```

2. **API Testing:**
   Use tools like Postman or cURL to test the API endpoints manually.

## Contributing

Feel free to fork this repository, make improvements, and submit a pull request. Contributions are welcome!

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgements

This project uses the following open-source packages:
- Django Rest Framework
- Django Simple JWT
- Python Decouple
- And more...
