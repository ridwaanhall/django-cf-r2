# Django Cloudflare R2 Integration

A Django web application that seamlessly integrates with Cloudflare R2 object storage for handling static files and media uploads. This project demonstrates how to configure Django to use Cloudflare R2 as a cost-effective, S3-compatible storage solution for your web applications.

## 🚀 Features

- **Cloudflare R2 Storage Integration**: Complete configuration for using Cloudflare R2 as your Django storage backend
- **Separate Storage Locations**: Dedicated storage configurations for static files and media uploads
- **Environment-Based Configuration**: Secure configuration management using environment variables
- **S3-Compatible Interface**: Leverages the AWS S3 SDK for seamless integration with Cloudflare R2
- **Production-Ready Setup**: Includes proper security configurations and deployment considerations
- **Django 5.2.6**: Built with the latest stable Django version

## 📋 Prerequisites

- Python 3.8 or higher
- A Cloudflare account with R2 storage enabled
- Basic knowledge of Django and environment variables

## 🛠 Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/ridwaanhall/django-cf-r2.git
   cd django-cf-r2
   ```

2. **Create and activate a virtual environment**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

## ⚙️ Configuration

### 1. Cloudflare R2 Setup

1. **Create an R2 bucket** in your Cloudflare dashboard
2. **Generate API tokens** with the following permissions:
   - Object Read & Write access to your bucket
   - Note down your Account ID, Access Key ID, and Secret Access Key

### 2. Environment Variables

Create a `.env` file in the project root directory with the following variables:

```bash
# Debug mode (set to 1 for development, 0 for production)
DEBUG=1

# Allowed hosts (comma-separated list for production)
ALLOWED_HOSTS=localhost,127.0.0.1

# Cloudflare R2 Configuration
CLOUDFLARE_R2_BUCKET=your-bucket-name
CLOUDFLARE_R2_ACCESS_KEY=your-access-key-id
CLOUDFLARE_R2_SECRET_KEY=your-secret-access-key
CLOUDFLARE_R2_BUCKET_ENDPOINT=https://your-account-id.r2.cloudflarestorage.com
```

### 3. Storage Configuration

The project includes custom storage classes located in `src/helpers/cloudflare/storages.py`:

- **StaticFilesStorage**: Handles CSS, JavaScript, and other static assets
- **MediaFileStorage**: Manages user uploads and media files

Both storage classes are automatically configured in the Django settings to use your Cloudflare R2 bucket.

## 🚀 Usage

### Development

1. **Navigate to the source directory**
   ```bash
   cd src
   ```

2. **Run database migrations**
   ```bash
   python manage.py migrate
   ```

3. **Create a superuser (optional)**
   ```bash
   python manage.py createsuperuser
   ```

4. **Start the development server**
   ```bash
   python manage.py runserver
   ```

5. **Access your application**
   - Local development: http://127.0.0.1:8000/
   - Admin interface: http://127.0.0.1:8000/admin/

### File Uploads

Any files uploaded through Django's file handling system will automatically be stored in your Cloudflare R2 bucket:

- **Static files** (CSS, JS, images) → `static/` directory in R2
- **Media files** (user uploads) → `media/` directory in R2

### Collecting Static Files

For production deployment, collect and upload static files:

```bash
python manage.py collectstatic
```

## 📖 Example Usage

### Using in Django Models

Once configured, you can use Django's standard file handling in your models, and files will automatically be stored in Cloudflare R2:

```python
from django.db import models

class Document(models.Model):
    title = models.CharField(max_length=100)
    file = models.FileField(upload_to='documents/')  # Stored in R2 media/documents/
    created_at = models.DateTimeField(auto_now_add=True)

class UserProfile(models.Model):
    user = models.OneToOneField('auth.User', on_delete=models.CASCADE)
    avatar = models.ImageField(upload_to='avatars/')  # Stored in R2 media/avatars/
```

### File URL Access

Files stored in R2 will have URLs in the format:
```
https://your-account-id.r2.cloudflarestorage.com/your-bucket/media/documents/filename.pdf
https://your-account-id.r2.cloudflarestorage.com/your-bucket/static/css/style.css
```

### Custom Domain (Optional)

For production, you can configure a custom domain for your R2 bucket:
1. Set up a CNAME record pointing to your R2 bucket
2. Configure the domain in your Cloudflare dashboard
3. Update your Django settings to use the custom domain

## 🏗 Project Structure

```
django-cf-r2/
├── src/
│   ├── helpers/
│   │   └── cloudflare/
│   │       ├── __init__.py
│   │       └── storages.py          # Custom R2 storage backends
│   ├── roneha/                      # Main Django project
│   │   ├── __init__.py
│   │   ├── asgi.py
│   │   ├── settings.py              # Main configuration file
│   │   ├── urls.py
│   │   └── wsgi.py
│   └── manage.py
├── requirements.txt                 # Python dependencies
├── .gitignore
└── README.md
```

## 🔧 Key Dependencies

- **Django 5.2.6**: Web framework
- **django-storages 1.14.6**: Provides storage backends for cloud services
- **boto3 1.40.32**: AWS SDK for Python (S3-compatible interface)
- **python-decouple 3.8**: Environment variable management

## 🌐 Deployment

### Environment Variables for Production

```bash
DEBUG=0
ALLOWED_HOSTS=yourdomain.com,www.yourdomain.com
CLOUDFLARE_R2_BUCKET=your-production-bucket
CLOUDFLARE_R2_ACCESS_KEY=your-production-access-key
CLOUDFLARE_R2_SECRET_KEY=your-production-secret-key
CLOUDFLARE_R2_BUCKET_ENDPOINT=https://your-account-id.r2.cloudflarestorage.com
```

### Production Checklist

- [ ] Set `DEBUG=0` in environment variables
- [ ] Configure proper `ALLOWED_HOSTS`
- [ ] Update `SECRET_KEY` to a secure, unique value
- [ ] Set up proper database (PostgreSQL recommended)
- [ ] Configure domain/CDN for R2 bucket access
- [ ] Run `python manage.py collectstatic`
- [ ] Set up proper logging and monitoring

## 🔒 Security Considerations

### API Key Management
- Never commit your `.env` file to version control
- Use different R2 buckets for development and production
- Rotate your API keys regularly
- Use environment-specific credentials

### Bucket Configuration
- Enable appropriate CORS settings for your domain
- Configure bucket policies to restrict access as needed
- Use separate buckets for different environments (dev/staging/prod)
- Consider enabling versioning for important data

### Django Security
- Use a strong, unique `SECRET_KEY` in production
- Set `DEBUG=False` in production
- Configure `ALLOWED_HOSTS` properly
- Use HTTPS in production environments

## 💡 Benefits of Cloudflare R2

1. **Cost-Effective**: No egress fees for data transfer
2. **Global Performance**: Cloudflare's global network ensures fast content delivery
3. **S3 Compatibility**: Use existing AWS S3 tools and libraries
4. **Scalability**: Automatic scaling without infrastructure management
5. **Security**: Built-in DDoS protection and security features

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📝 License

This project is open source and available under the [MIT License](LICENSE).

## 🔍 Troubleshooting

### Common Issues

**1. ImportError: Couldn't import Django**
- Ensure you've activated your virtual environment
- Install dependencies: `pip install -r requirements.txt`

**2. Configuration Errors**
- Verify all environment variables are set correctly in your `.env` file
- Check that your R2 bucket exists and credentials are valid
- Ensure your Cloudflare R2 endpoint URL is correct

**3. File Upload Issues**
- Verify your R2 bucket permissions allow read/write access
- Check that your API tokens have the necessary permissions
- Ensure the bucket endpoint is accessible from your deployment environment

**4. Static Files Not Loading**
- Run `python manage.py collectstatic` to upload static files to R2
- Verify your Cloudflare R2 bucket allows public read access for static files
- Check that your domain/CDN configuration is correct

## 🆘 Support

If you encounter any issues or have questions:

1. Check the [Issues](https://github.com/ridwaanhall/django-cf-r2/issues) section
2. Create a new issue with detailed information about your problem
3. Include your environment details and error messages

## 🔗 Useful Links

- [Django Documentation](https://docs.djangoproject.com/)
- [Cloudflare R2 Documentation](https://developers.cloudflare.com/r2/)
- [django-storages Documentation](https://django-storages.readthedocs.io/)
- [boto3 Documentation](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html)

---

**Built with ❤️ using Django and Cloudflare R2**