# RustFS — Docker & AWS CLI

## 1. Run RustFS

Pull the image:

```bash
docker pull rustfs/rustfs:latest
```

Create a persistent volume:

```bash
docker volume create rustfs-data
```

Set credentials (local development only):

```bash
export RUSTFS_ACCESS_KEY="rustfsadmin"
export RUSTFS_SECRET_KEY="rustfsadmin"
```

Start RustFS:

```bash
docker run -d --name rustfs --restart unless-stopped -p 127.0.0.1:9000:9000 -p 127.0.0.1:9001:9001 -v rustfs-data:/data -e RUSTFS_ACCESS_KEY="$RUSTFS_ACCESS_KEY" -e RUSTFS_SECRET_KEY="$RUSTFS_SECRET_KEY" -e RUSTFS_ADDRESS=":9000" -e RUSTFS_CONSOLE_ADDRESS=":9001" -e RUSTFS_CONSOLE_ENABLE="true" rustfs/rustfs:latest /data
```

Check container:

```bash
docker ps
```

Check logs:

```bash
docker logs rustfs
```

Check health:

```bash
curl -f http://localhost:9000/health
```

Console: http://localhost:9001

## 2. Install AWS CLI (Linux / WSL x86-64)

Prepare directory:

```bash
mkdir -p ~/tmp
```

Download:

```bash
# Download AWS CLI:
curl -L https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip -o ~/tmp/awscliv2.zip
```

Extract:

```bash
unzip -o ~/tmp/awscliv2.zip -d ~/tmp
```

Install:

```bash
sudo ~/tmp/aws/install --update
```

Verify:

```bash
aws --version
```

## 3. Configure AWS CLI for RustFS

Set access key:

```bash
export AWS_ACCESS_KEY_ID="$RUSTFS_ACCESS_KEY"
export AWS_SECRET_ACCESS_KEY="$RUSTFS_SECRET_KEY"
export AWS_DEFAULT_REGION="us-east-1"
export AWS_ENDPOINT_URL="http://localhost:9000"
export AWS_EC2_METADATA_DISABLED="true"
```

Use path-style bucket URLs:

```bash
aws configure set default.s3.addressing_style path
```

## 4. S3 commands

Create bucket:

```bash
aws s3 mb s3://rustfs-demo
```

List buckets:

```bash
aws s3 ls
```

Create test file:

```bash
echo "Hello RustFS" > test.txt
```

Upload file:

```bash
aws s3 cp test.txt s3://rustfs-demo/
```

List objects:

```bash
aws s3 ls s3://rustfs-demo/ --recursive
```

Download file:

```bash
aws s3 cp s3://rustfs-demo/test.txt ./downloaded.txt
```

Remove file:

```bash
aws s3 rm s3://rustfs-demo/test.txt
```

> **Note:** Environment variables last only for the current shell session. Use strong credentials outside local development; don't commit secrets to Git.
