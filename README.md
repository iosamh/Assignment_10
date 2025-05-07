### 2. Frontend - S3 Static Website Hosting
- Created bucket: `sda1025-blogapp-frontend` in eu-north-1
- Enabled static website hosting with index.html as entry point
- Configured bucket policy for public read access:
  ```json
  {
    "Version": "2012-10-17",
    "Statement": [{
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::sda1025-blogapp-frontend/*"
    }]
  }
  ```

- Public URL:  
  `http://sda1025-blogapp-frontend.s3-website.eu-north-1.amazonaws.com`

### 3. Media Uploads - S3 Bucket
- Created bucket: `sda1025-blogapp-media` in eu-north-1
- Configured CORS policy:
  ```json
  [{
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["GET", "PUT", "POST", "DELETE", "HEAD"],
    "AllowedOrigins": ["*"],
    "ExposeHeaders": ["ETag"]
  }]
  ```


### 4. IAM User for S3 Access
- Created IAM user: `blog-app-user`
- Attached custom policy for media bucket access:
  ```json
  {
    "Version": "2012-10-17",
    "Statement": [{
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:GetObject",
        "s3:DeleteObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::sda1025-blogapp-media",
        "arn:aws:s3:::sda1025-blogapp-media/*"
      ]
    }]
  }
  ```

### 5. Backend - EC2 Setup
- Launched EC2 instance:
  - Type: t3.micro
  - OS: Ubuntu 22.04 LTS
  - Region: eu-north-1
  - Security Group Rules:
    - SSH (22)
    - HTTP (80)
    - HTTPS (443)
    - Custom TCP (5000)
- User Data script installed:
  - git, curl, unzip
  - Node.js via nvm
  - PM2 process manager
  - AWS CLI v2
  - MongoDB Shell
- Cloned repository:
  ```bash
  git clone https://github.com/cw-barry/blog-app-MERN.git /home/ubuntu/blog-app
  ```
- Configured backend `.env`:
  ```env
  PORT=5000
  MONGODB=mongodb+srv://test:qazqwe123@mongodb.txkjsso.mongodb.net/blog-app
  AWS_ACCESS_KEY_ID=AKIAXXXXXXXXXXXXXXXX
  AWS_SECRET_ACCESS_KEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
  AWS_REGION=eu-north-1
  S3_BUCKET=sda1025-blogapp-media
  MEDIA_BASE_URL=https://sda1025-blogapp-media.eu-north-1.amazonaws.com
  ```

### 6. PM2 Process Management
- Installed PM2 globally:
  ```bash
  npm install -g pm2
  ```
- Started backend service:
  ```bash
  cd /home/ubuntu/blog-app/backend
  pm2 start index.js --name "blog-backend"
  ```
- Configured auto-start:
  ```bash
  pm2 startup
  pm2 save
  ```
  