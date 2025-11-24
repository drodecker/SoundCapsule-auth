# SoundCapsule Auth

Production-ready authentication service using SuperTokens with MySQL database and multiple OAuth providers.

## Features

- 🔐 **Email/Password Authentication** with magic link support
- 🌐 **OAuth Providers**: Google, Apple, Facebook, Twitter (X)
- 🐳 **Docker-based deployment** with docker-compose
- 🔄 **Traefik reverse proxy** for easy local development
- 🗄️ **MySQL database** for persistent storage
- 🛡️ **Production-ready configuration**

## Quick Start

1. **Clone the repository**
   ```bash
   git clone <your-repository-url>
   cd SoundCapsule-auth
   ```

2. **Set up environment variables**
   ```bash
   cp .env.example .env
   # Edit .env with your actual credentials
   ```

3. **Start the services**
   ```bash
   docker-compose up -d
   ```

4. **Access the services**
   - SuperTokens Core: `http://localhost:4001` or `http://auth.localhost`
   - Traefik Dashboard: Disabled by default for security (can enable with `--api.insecure=true` for local dev)

## Configuration

### Environment Variables

Copy `.env.example` to `.env` and configure the following:

- **MySQL**: Database credentials
- **SuperTokens**: API keys for security
- **OAuth Providers**: Client IDs and secrets for each provider

### Architecture

```
┌─────────────────┐
│   Application   │
│  (Port 3000)    │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│    Traefik      │
│   (Port 80)     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐      ┌─────────────────┐
│  SuperTokens    │─────▶│     MySQL       │
│  (Port 4001)    │      │  (Port 3306)    │
└─────────────────┘      └─────────────────┘
```

## OAuth Provider Setup

### Google OAuth Setup

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select an existing one
3. Navigate to **APIs & Services** > **Credentials**
4. Click **Create Credentials** > **OAuth client ID**
5. Configure OAuth consent screen if not done already
6. Select **Web application** as application type
7. Add authorized redirect URIs:
   - `http://localhost:3000/auth/callback/google`
   - `http://auth.localhost/auth/callback/google`
8. Copy the **Client ID** and **Client Secret** to your `.env` file

### Apple Sign In Setup

1. Go to [Apple Developer Portal](https://developer.apple.com/account)
2. Navigate to **Certificates, Identifiers & Profiles**

#### Step 1: Register an App ID
1. Click **Identifiers** > **+** (Add button)
2. Select **App IDs** and click **Continue**
3. Select **App** and click **Continue**
4. Fill in the details:
   - **Description**: Your app name
   - **Bundle ID**: com.yourcompany.yourapp
5. Enable **Sign in with Apple** capability
6. Click **Continue** and **Register**

#### Step 2: Create a Services ID
1. Click **Identifiers** > **+** (Add button)
2. Select **Services IDs** and click **Continue**
3. Fill in the details:
   - **Description**: Your service name
   - **Identifier**: your.apple.service.id (this is your APPLE_CLIENT_ID)
4. Enable **Sign in with Apple**
5. Click **Configure** next to Sign in with Apple
6. Add your domains and redirect URLs:
   - **Domains**: localhost, auth.localhost
   - **Return URLs**: 
     - `http://localhost:3000/auth/callback/apple`
     - `http://auth.localhost/auth/callback/apple`
7. Click **Save**, **Continue**, and **Register**

#### Step 3: Create a Private Key
1. Click **Keys** > **+** (Add button)
2. Enter a **Key Name**
3. Enable **Sign in with Apple**
4. Click **Configure** next to Sign in with Apple
5. Select your **Primary App ID**
6. Click **Save**, **Continue**, and **Register**
7. **Download the key file** (.p8 file) - you can only download it once!
8. Note the **Key ID** shown on the screen

#### Step 4: Get Your Team ID
1. Go to [Membership Details](https://developer.apple.com/account/#/membership)
2. Copy your **Team ID**

#### Step 5: Configure Environment Variables
```bash
APPLE_CLIENT_ID=your.apple.service.id
APPLE_KEY_ID=ABC123XYZ
APPLE_TEAM_ID=DEF456UVW
APPLE_PRIVATE_KEY_PATH=./secrets/AuthKey_ABC123XYZ.p8
```

**Important Notes:**
- Keep your private key (.p8 file) secure
- Never commit the private key to version control
- The private key can only be downloaded once
- Store the key in a `secrets/` directory (already in .gitignore)
- Ensure the key file is readable by your application

### Facebook OAuth Setup

1. Go to [Facebook Developers](https://developers.facebook.com/)
2. Click **My Apps** > **Create App**
3. Select **Consumer** as the app type and click **Next**
4. Fill in your app details and click **Create App**

#### Step 1: Add Facebook Login Product
1. In your app dashboard, find **Facebook Login** and click **Set Up**
2. Select **Web** as the platform
3. Enter your site URL: `http://localhost:3000`
4. Click **Save** and **Continue**

#### Step 2: Configure OAuth Settings
1. In the left sidebar, go to **Facebook Login** > **Settings**
2. Add **Valid OAuth Redirect URIs**:
   - `http://localhost:3000/auth/callback/facebook`
   - `http://auth.localhost/auth/callback/facebook`
3. Click **Save Changes**

#### Step 3: Get App Credentials
1. Go to **Settings** > **Basic**
2. Copy your **App ID** (this is your FACEBOOK_APP_ID)
3. Click **Show** next to **App Secret** and copy it (this is your FACEBOOK_APP_SECRET)

#### Step 4: Configure Environment Variables
```bash
FACEBOOK_APP_ID=1234567890123456
FACEBOOK_APP_SECRET=abcdef1234567890abcdef1234567890
```

#### Step 5: App Review (For Production)
For development, you can use your own account. For production:
1. Add **email** permission to your app
2. Submit your app for **App Review**
3. Complete the review process before going live

**Important Notes:**
- Development mode allows testing with accounts that have a role in the app
- Production mode requires app review approval
- Keep your App Secret secure and never expose it in client-side code

### Twitter (X) OAuth Setup

1. Go to [Twitter Developer Portal](https://developer.twitter.com/en/portal/dashboard)
2. Create a new project or select an existing one
3. Navigate to your app settings
4. Under **User authentication settings**, click **Set up**
5. Enable **OAuth 2.0**
6. Add redirect URIs:
   - `http://localhost:3000/auth/callback/twitter`
   - `http://auth.localhost/auth/callback/twitter`
7. Copy the **Client ID** and **Client Secret** to your `.env` file

### Email/Password with Magic Link

Email/Password authentication with magic link is enabled by default. To configure email sending:

1. Set up an SMTP service (SendGrid, AWS SES, Mailgun, etc.)
2. Configure email settings in your application backend
3. SuperTokens will handle the password hashing and magic link generation

## Integration with Your Application

### Backend Integration

Install the SuperTokens SDK in your backend:

```bash
# Node.js
npm install supertokens-node

# Python
pip install supertokens-python

# Go
go get github.com/supertokens/supertokens-golang
```

### Frontend Integration

Install the SuperTokens SDK in your frontend:

```bash
# React
npm install supertokens-auth-react

# Vanilla JS
npm install supertokens-web-js

# React Native
npm install supertokens-react-native
```

### Example Configuration (Node.js Backend)

```javascript
const supertokens = require("supertokens-node");
const Session = require("supertokens-node/recipe/session");
const ThirdPartyEmailPassword = require("supertokens-node/recipe/thirdpartyemailpassword");
const EmailVerification = require("supertokens-node/recipe/emailverification");
const Passwordless = require("supertokens-node/recipe/passwordless");
const fs = require('fs');

// Load Apple private key at startup (one-time operation)
const loadApplePrivateKey = () => {
    const keyPath = process.env.APPLE_PRIVATE_KEY_PATH;
    if (!keyPath) {
        console.warn('Apple Sign In disabled: APPLE_PRIVATE_KEY_PATH not configured');
        return null;
    }
    try {
        // Check if file exists and is readable
        fs.accessSync(keyPath, fs.constants.R_OK);
        
        // Read the key file
        const key = fs.readFileSync(keyPath, 'utf8');
        
        // Basic validation - Apple keys should start with specific headers
        if (!key.includes('BEGIN PRIVATE KEY')) {
            throw new Error('Invalid Apple private key format');
        }
        
        console.log(`Apple private key loaded successfully from ${keyPath}`);
        return key;
    } catch (error) {
        console.error(`Failed to read Apple private key from ${keyPath}:`, error.message);
        throw new Error(`Apple private key error at ${keyPath}: ${error.message}`);
    }
};

const applePrivateKey = loadApplePrivateKey();

supertokens.init({
    framework: "express",
    supertokens: {
        connectionURI: "http://localhost:4001",
        apiKey: process.env.SUPERTOKENS_API_KEYS,
    },
    appInfo: {
        appName: "SoundCapsule",
        apiDomain: "http://localhost:3001",
        websiteDomain: "http://localhost:3000",
        apiBasePath: "/auth",
        websiteBasePath: "/auth",
    },
    recipeList: [
        ThirdPartyEmailPassword.init({
            providers: [
                {
                    config: {
                        thirdPartyId: "google",
                        clients: [{
                            clientId: process.env.GOOGLE_CLIENT_ID,
                            clientSecret: process.env.GOOGLE_CLIENT_SECRET,
                        }],
                    },
                },
                ...(applePrivateKey ? [{
                    config: {
                        thirdPartyId: "apple",
                        clients: [{
                            clientId: process.env.APPLE_CLIENT_ID,
                            additionalConfig: {
                                keyId: process.env.APPLE_KEY_ID,
                                teamId: process.env.APPLE_TEAM_ID,
                                privateKey: applePrivateKey,
                            },
                        }],
                    },
                }] : []),
                {
                    config: {
                        thirdPartyId: "facebook",
                        clients: [{
                            clientId: process.env.FACEBOOK_APP_ID,
                            clientSecret: process.env.FACEBOOK_APP_SECRET,
                        }],
                    },
                },
                {
                    config: {
                        thirdPartyId: "twitter",
                        clients: [{
                            clientId: process.env.TWITTER_CLIENT_ID,
                            clientSecret: process.env.TWITTER_CLIENT_SECRET,
                        }],
                    },
                },
            ],
        }),
        Passwordless.init({
            flowType: "MAGIC_LINK",
            contactMethod: "EMAIL",
        }),
        EmailVerification.init({
            mode: "REQUIRED",
        }),
        Session.init(),
    ],
});
```

## Monitoring and Health Checks

Health check endpoints:
- SuperTokens: `http://localhost:4001/hello`
- MySQL: Connection check via healthcheck in docker-compose

## Security Best Practices

1. **Change default passwords** in `.env` file
2. **Use strong API keys** for SuperTokens
3. **Enable HTTPS** in production (update Traefik configuration)
4. **Restrict database access** with proper firewall rules
5. **Keep secrets secure** - never commit `.env` file
6. **Regular updates** - keep Docker images up to date
7. **Monitor logs** for suspicious activity

## Troubleshooting

### SuperTokens won't start
- Ensure MySQL is healthy before SuperTokens starts
- Check database credentials in `.env`
- Verify `config.yaml` syntax

### OAuth not working
- Verify redirect URIs match exactly in provider settings
- Check that credentials in `.env` are correct
- Ensure your app is in the correct mode (development vs production)

### Can't access auth.localhost
- Add `127.0.0.1 auth.localhost` to your `/etc/hosts` file (Linux/Mac) or `C:\Windows\System32\drivers\etc\hosts` (Windows)
- Restart Traefik: `docker-compose restart traefik`

### Database connection issues
- Check if MySQL container is running: `docker-compose ps`
- Verify MySQL is healthy: `docker-compose logs mysql`
- Test connection (replace username if different): `docker-compose exec mysql mysql -u supertokens_user -p`

## Development

### Viewing Logs
```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f supertokens
docker-compose logs -f mysql
```

### Restarting Services
```bash
# All services
docker-compose restart

# Specific service
docker-compose restart supertokens
```

### Stopping Services
```bash
docker-compose down

# Remove volumes (careful: deletes all data)
docker-compose down -v
```

## Production Deployment

For production deployment:

1. **Use environment-specific configs**
   - Separate `.env` files for staging/production
   - Use Docker secrets for sensitive data

2. **Enable HTTPS**
   - Update Traefik configuration for SSL/TLS
   - Obtain certificates from Let's Encrypt

3. **Database backups**
   - Set up automated MySQL backups
   - Test restore procedures

4. **Monitoring**
   - Set up application monitoring (DataDog, New Relic, etc.)
   - Configure log aggregation (ELK stack, CloudWatch, etc.)

5. **Scaling**
   - Use managed database services (AWS RDS, Google Cloud SQL)
   - Deploy multiple SuperTokens instances behind a load balancer

## Resources

- [SuperTokens Documentation](https://supertokens.com/docs/guides)
- [SuperTokens GitHub](https://github.com/supertokens/supertokens-core)
- [OAuth 2.0 Specification](https://oauth.net/2/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)

## License

MIT

## Support

For issues and questions:
- Open an issue on GitHub
- Check [SuperTokens Discord](https://supertokens.com/discord)
- Review [SuperTokens Documentation](https://supertokens.com/docs/guides)
