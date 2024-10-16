
# Terraform S3 Project - README 🚀

## 📝 개요

이 프로젝트는 **Terraform**을 사용하여 AWS S3 버킷을 생성하고, 여러 파일(GIF, HTML 등)을 업로드하는 예제입니다. 이를 통해 AWS 클라우드에서 S3 버킷을 자동으로 구성하고, 파일을 퍼블릭하게 접근할 수 있도록 설정하는 방법을 보여줍니다.

### 🎯 주요 목표
- 1. S3 버킷 생성
- 2. 퍼블릭 읽기 권한 설정
- 3. GIF 및 HTML 파일 업로드
- 4. Terraform을 사용한 자동화 구축

---

## 🛠️ 구축 과정

### 1. Terraform 및 AWS CLI 설치 ⚙️

Terraform을 사용하기 위해서는 AWS CLI와 Terraform이 설치되어 있어야 합니다. 아래의 명령어를 사용하여 Ubuntu 서버에 Terraform을 설치할 수 있습니다.

```bash
$ sudo su -

# wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

# echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

# apt-get update && apt-get install terraform -y

# terraform -version
```


---

## 📜 Terraform 코드 설명

### `bucket.tf` - S3 버킷 및 정책 설정

```hcl
# AWS Provider 설정
provider "aws" {
  region = "ap-northeast-2"
}

# S3 버킷 생성
resource "aws_s3_bucket" "bucket1" {
  bucket = "YOUR_BUCKET_NAME"
}

# S3 버킷의 public access block 설정
resource "aws_s3_bucket_public_access_block" "bucket1_public_access_block" {
  bucket = aws_s3_bucket.bucket1.id

  block_public_acls       = false
  block_public_policy     = false
  ignore_public_acls      = false
  restrict_public_buckets = false
}

# S3 버킷의 퍼블릭 읽기 정책 설정
resource "aws_s3_bucket_policy" "public_read_access" {
  bucket = aws_s3_bucket.bucket1.id

  policy = <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::YOUR_BUCKET_NAME/*"
    }
  ]
}
EOF
}

```

### `gif_upload.tf` - GIF 파일 업로드 설정

```hcl
# GIF 파일을 S3 버킷에 업로드 
resource "aws_s3_object" "gif_upload" {
  bucket        = aws_s3_bucket.bucket1.id
  key           = "김정은.gif"
  source        = "${path.module}/gif/김정은.gif"  # 실제 파일 경로로 수정
  content_type  = "image/gif"
}
```

### `upload_index.tf` - index.html 파일 업로드 설정

```hcl
# index.html 파일을 S3 버킷에 업로드 
resource "aws_s3_object" "index_html" {
  bucket        = aws_s3_bucket.bucket1.id
  key           = "index.html"
  source        = "${path.module}/index.html"  # 실제 파일 경로로 수정
  content_type  = "text/html"
}
```

### `upload_main.tf` - main.html 파일 업로드 설정

```hcl
# main.html 파일을 S3 버킷에 업로드 
resource "aws_s3_object" "main_html" {
  bucket        = aws_s3_bucket.bucket1.id
  key           = "main.html"
  source        = "${path.module}/main.html"  # 실제 파일 경로로 수정
  content_type  = "text/html"
}
```

## 사용 명령어
### 1. Terraform 초기화 🔧

Terraform 구성을 설정한 후, S3 버킷을 생성하기 전에 반드시 초기화해야 합니다.

```bash
terraform init
```

### 2. S3 버킷 생성 🪣

Terraform을 사용하여 S3 버킷을 생성하고 퍼블릭 접근을 설정합니다.

```bash
terraform apply -target=aws_s3_bucket.bucket1
```

### 3. GIF 파일 업로드 🖼️

준비된 `김정은.gif` 파일을 S3 버킷에 업로드합니다.

```bash
terraform apply -target=aws_s3_object.gif_upload
```

### 4. index.html 파일 업로드 🌐

S3 버킷에 `index.html` 파일을 업로드합니다.

```bash
terraform apply -target=aws_s3_object.index_html
```

### 5. main.html 파일 업로드 📄

S3 버킷에 `main.html` 파일을 업로드합니다.

```bash
terraform apply -target=aws_s3_object.main_html
```
![image](https://github.com/user-attachments/assets/9c5fdc3e-c643-4ec5-881b-9eae6a83fd2e)

![image](https://github.com/user-attachments/assets/f8e5fd6e-e323-4aa3-b757-91f58d8217f9)

![image](https://github.com/user-attachments/assets/3a8f1a72-7420-4926-9eee-3804f77e853f)

---

## ✅ 결론

Terraform을 사용하여 AWS S3 버킷을 생성하고, 파일을 퍼블릭하게 업로드하는 방법을 배웠습니다. Terraform을 사용하면 인프라를 코드로 관리할 수 있어 반복적이고 안정적인 구성을 보장할 수 있습니다. 또한, S3 버킷 정책을 통해 객체의 퍼블릭 접근 권한을 관리할 수 있으며, 다양한 파일 형식(GIF, HTML 등)을 S3에 업로드하여 외부에서 접근할 수 있습니다.

