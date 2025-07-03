# Coolify Deployment Guide

This guide explains how to deploy the No-Code Architects Toolkit API on Coolify.

## Files for Coolify Deployment

- `docker-compose.coolify.yml` - Coolify-optimized Docker Compose configuration
- `.env.coolify.example` - Environment variables template for Coolify

## Key Differences from Standard Deployment

### Removed Components
- **Traefik service** - Coolify has its own built-in reverse proxy
- **SSL/TLS configuration** - Coolify handles SSL certificates automatically
- **Domain routing labels** - Coolify manages domain routing through its interface
- **Port 80/443 exposure** - Coolify handles external port mapping

### Simplified Configuration
- Single service deployment (just the main application)
- Direct port 8080 exposure for Coolify's reverse proxy
- Health check endpoint for monitoring
- Persistent volumes for storage and logs

## Deployment Steps

### 1. Prepare Your Environment File

Copy the Coolify environment template:
```bash
cp .env.coolify.example .env
```

Edit `.env` and configure:
- `API_KEY` - Set a secure API key for authentication
- Storage provider (S3 or GCP) - Uncomment and configure your preferred storage
- Performance settings - Adjust based on your server resources

### 2. Deploy in Coolify

1. **Create New Resource** in Coolify dashboard
2. **Select "Docker Compose"** as the deployment type
3. **Upload or paste** the contents of `docker-compose.coolify.yml`
4. **Configure Environment Variables** in Coolify's environment section:
   - Set `API_KEY` to your secure API key (REQUIRED)
   - Configure storage variables (S3 or GCP) as needed
   - Adjust performance settings if desired
5. **Set Domain** in Coolify's domain settings
6. **Deploy** the application

**Important**: Do not use `.env` files with Coolify. Instead, set environment variables directly in Coolify's interface.

### 3. Coolify Configuration

#### Domain Settings
- Set your desired domain/subdomain in Coolify's domain configuration
- Coolify will automatically handle SSL certificate generation
- No need to configure SSL_EMAIL or APP_DOMAIN variables

#### Port Configuration
- Coolify will automatically map external traffic to port 8080
- The application exposes port 8080 internally

#### Health Checks
- The configuration includes a health check endpoint: `/v1/toolkit/test`
- Coolify will monitor this endpoint to ensure the service is healthy

## Environment Variables

### Required Variables
```env
API_KEY=your_secure_api_key_here
```

### Storage Configuration (Choose One)

#### For S3-Compatible Storage:
```env
S3_ACCESS_KEY=your_access_key
S3_SECRET_KEY=your_secret_key
S3_ENDPOINT_URL=https://your-endpoint-url
S3_REGION=your-region
S3_BUCKET_NAME=your-bucket-name
```

#### For Google Cloud Storage:
```env
GCP_SA_CREDENTIALS={"your":"service_account_json"}
GCP_BUCKET_NAME=your_gcp_bucket_name
STORAGE_PATH=GCP
```

### Performance Tuning (Optional)
```env
MAX_QUEUE_LENGTH=10
GUNICORN_WORKERS=4
GUNICORN_TIMEOUT=300
LOCAL_STORAGE_PATH=/tmp
```

## Testing Your Deployment

1. **Health Check**: Visit `https://yourdomain.com/v1/toolkit/test`
2. **API Documentation**: Use the [Postman Template](https://bit.ly/49Gkh61)
3. **Configure Postman**:
   - `base_url`: Your Coolify domain (e.g., `https://yourdomain.com`)
   - `x-api-key`: Your configured API_KEY

## Troubleshooting

### Common Issues

1. **API_KEY Environment Variable Error**
   - **Error**: `ValueError: API_KEY environment variable is not set`
   - **Solution**: Ensure `API_KEY` is set in Coolify's environment variables section
   - **Note**: Do NOT use `.env` files with Coolify - set variables directly in the interface
   - **Verification**: Check that the variable appears in Coolify's environment tab

2. **Service Won't Start**
   - Check environment variables are properly set in Coolify's interface
   - Verify storage credentials if using cloud storage
   - Check Coolify logs for detailed error messages
   - Ensure all required variables have values (no empty strings)

3. **Health Check Failing**
   - Ensure the application is binding to port 8080
   - Check if the `/v1/toolkit/test` endpoint is accessible
   - Verify no firewall issues within the container
   - Wait for the application to fully start (40 second start period)

4. **Storage Issues**
   - Verify cloud storage credentials and permissions
   - Check bucket names and regions are correct
   - Ensure service account has proper permissions (for GCP)
   - Test storage connectivity independently if possible

### Monitoring

- Use Coolify's built-in monitoring dashboard
- Check application logs through Coolify's log viewer
- Monitor resource usage (CPU, memory, disk)

## Scaling Considerations

- Adjust `GUNICORN_WORKERS` based on your server's CPU cores
- Increase `GUNICORN_TIMEOUT` for large file processing
- Set `MAX_QUEUE_LENGTH` to prevent resource exhaustion
- Consider using external storage (S3/GCP) for better scalability

## Support

For additional support:
- Join the [No-Code Architects Community](https://www.skool.com/no-code-architects)
- Check the main [README.md](README.md) for API documentation
- Review Coolify's documentation for platform-specific issues
