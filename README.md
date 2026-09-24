Koleksi arahan CLI (Command Line Interface) 


---

## 🛠️ GITHUB 1: Remote + push


```bash

----------------
ACTIVITY 1

gh --version
gh auth status
ls ~/.ssh/id_ed25519*

----------------
ACTIVITY 2

gh auth login

# > GitHub.com
# > SSH
# > ~/.ssh/id_ed25519.pub
# > Title: GitHub CLI
# > Login with a web browser

gh auth status

ssh -T git@github.com

----------------
ACTIVITY 3

cd ~/devops-bootcamp
gh repo create devops-bootcamp --public --source=. --remote=origin --push
git remote -v

----------------
ACTIVITY 4

cd ~/devops-bootcamp

echo "## Diubah dari laptop" >> README.md
git add README.md
git commit -m "Tambah baris dari laptop"

git push

----------------
ACTIVITY 5

cd ~
git clone git@github.com:$USER/devops-bootcamp.git devops-bootcamp-clone

cd devops-bootcamp-clone
git log --oneline

----------------
ACTIVITY 6

cd ~/devops-bootcamp-clone

echo "## Diubah dari salinan kedua" >> README.md
git add README.md
git commit -m "Edit dari salinan clone"
git push

cd ~/devops-bootcamp
git pull
cat README.md

----------------
ACTIVITY 7

cd ~/devops-bootcamp
gh issue create \
  --title "Tambah fail CONTRIBUTING.md" \
  --body "Perlukan panduan sumbangan untuk repo ini"

gh issue list

----------------
ACTIVITY 8

cd ~/devops-bootcamp
git checkout -b tambah-contributing

echo "# Panduan Sumbangan" > CONTRIBUTING.md
git add .
git commit -m "Tambah fail CONTRIBUTING.md"

git push -u origin tambah-contributing

gh pr create \
  --title "Tambah fail CONTRIBUTING.md" \
  --body "Closes #1" --base main

----------------
ACTIVITY 9

cd ~/devops-bootcamp
gh pr merge --merge

git checkout main
git pull

gh issue list

```


---

## 🛠️ GITHUB 2: Fork + PR


```bash

----------------
ACTIVITY 1

cd ~
gh repo fork Infratify/devops-bootcamp-collab --clone

cd devops-bootcamp-collab

git remote -v

----------------
ACTIVITY 2

GH_USER=$(gh api user --jq .login)
git checkout -b tambah-$GH_USER

echo "# Peserta: $GH_USER" > "$GH_USER.md"

git add "$GH_USER.md"
git commit -m "Tambah $GH_USER.md"

git push -u origin tambah-$GH_USER

----------------
ACTIVITY 3

GH_USER=$(gh api user --jq .login)

gh pr create \
  --repo Infratify/devops-bootcamp-collab \
  --base main \
  --title "Tambah $GH_USER" \
  --body "Sumbangan dari $GH_USER"

gh pr list --repo Infratify/devops-bootcamp-collab

----------------
ACTIVITY 4

gh pr view <N> --repo Infratify/devops-bootcamp-collab

gh pr diff <N> --repo Infratify/devops-bootcamp-collab

----------------
ACTIVITY 5
gh pr review <N> --repo Infratify/devops-bootcamp-collab \
  --request-changes --body "Ada typo, sila betulkan"

gh pr review <N> --repo Infratify/devops-bootcamp-collab \
  --approve --body "Dah betul, LGTM"

----------------
ACTIVITY 7

cd ~/laman
GH_USER=$(gh api user --jq .login)

git init
git add index.html && git commit -m "Laman portfolio pertama"

gh repo create $GH_USER.github.io --public --source=. --push

----------------
ACTIVITY 8

cd ~/devops-bootcamp-collab
GH_USER=$(gh api user --jq .login)

git checkout main
git checkout -b url-$GH_USER

echo "Laman: https://$GH_USER.github.io" >> "$GH_USER.md"
git add "$GH_USER.md" && git commit -m "Tambah URL laman"
git push -u origin url-$GH_USER

gh pr create --repo Infratify/devops-bootcamp-collab --fill
```

---

## 🛠️ AWS 1: Account & IAM

Langkah-langkah untuk konfigurasi awal dan pengesahan identiti AWS CLI pada komputer tempatan.

```bash
# Jalankan pada persekitaran: LOCAL

# Semak identiti semasa (sebelum konfigurasi)
aws sts get-caller-identity

# Konfigurasi kredensial AWS (Access Key, Secret Key, Region)
aws configure

# Semak semula identiti selepas konfigurasi selesai
aws sts get-caller-identity

# Simpan maklumat identiti ke dalam fail nota
aws sts get-caller-identity > aws1/identity.txt
```

---

## 🌐 AWS 2: EC2 & S3

Pengurusan akses SSH ke EC2, pemasangan AWS CLI di dalam server, dan operasi asas Amazon S3.

### 1. Akses & Persediaan EC2
```bash
# [LOCAL] Paparkan SSH public key untuk dimasukkan ke EC2
cat ~/.ssh/id_ed25519.pub

# [LOCAL] Log masuk ke EC2 menggunakan SSH
ssh ubuntu@<public-ip>
```

### 2. Pemasangan AWS CLI di EC2 (Melalui SSM / SSH)
```bash
# [SSM/EC2] Tukar ke shell bash dan kemas kini sistem
bash
sudo apt install unzip -y

# [SSM/EC2] Muat turun dan pasang AWS CLI v2
curl -o awscliv2.zip "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip"
unzip awscliv2.zip && sudo ./aws/install

# [SSM/EC2] Sahkan pemasangan CLI
aws sts get-caller-identity
```

### 3. Operasi S3 & Pengurusan Dokumentasi
```bash
# [LOCAL] Cipta S3 Bucket baharu di rantau Singapura
aws s3 mb s3://devops-\$USER-7421 --region ap-southeast-1
aws s3 ls

# [SSM/EC2] Hantar dan ambil fail dari S3 Bucket
aws s3 ls
echo "hello dari \$(hostname)" > server.txt
aws s3 cp server.txt s3://devops-\$USER-7421/
aws s3 ls s3://devops-\$USER-7421/
aws s3 cp s3://devops-\$USER-7421/server.txt server-baru.txt
cat server-baru.txt

# [LOCAL] Simpan ID dokumentasi EC2 & S3, kemudian padam bucket
aws ec2 describe-instances --query "Reservations[].Instances[].InstanceId" > aws2/notes.txt
aws s3 ls >> aws2/notes.txt
aws s3 rm s3://devops-\$USER-7421/ --recursive
aws s3 rb s3://devops-\$USER-7421
```

---

## 🔀 AWS 3: VPC

Semakan IP awam dari dalam rangkaian dan dokumentasi komponen VPC (VPC, Subnet, Route Tables).

```bash
# [SSM/EC2] Semak IP awam (Public IP) yang digunakan oleh instance
curl -s https://checkip.amazonaws.com

# [LOCAL] Dokumentasikan CIDR Block dan ID komponen rangkaian
aws ec2 describe-vpcs --query "Vpcs[].CidrBlock" > aws3/notes.txt
aws ec2 describe-subnets --query "Subnets[].CidrBlock" >> aws3/notes.txt
aws ec2 describe-route-tables --query "RouteTables[].RouteTableId" >> aws3/notes.txt
```

---

## 📦 AWS 4: ECR & Gateway

Semakan status infrastruktur NAT Gateway, Elastic IP (EIP), dan repositori Docker (ECR).

```bash
# [SSM/EC2] Semak IP awam semasa
curl -s https://checkip.amazonaws.com

# [LOCAL] Dokumentasikan komponen NAT, IP Awam, dan URI Repositori ECR
aws ec2 describe-nat-gateways --query "NatGateways[].[NatGatewayId,State]" > aws4/notes.txt
aws ec2 describe-addresses --query "Addresses[].PublicIp" >> aws4/notes.txt
aws ecr describe-repositories --query "repositories[].repositoryUri" >> aws4/notes.txt
```

---

## ☁️ CLOUDFLARE 1
DNS + Workers & Pages

```bash
cd ~/devops-bootcamp
git checkout -b cloudflare1 && mkdir cloudflare1

cat > cloudflare1/notes.txt <<EOF
nameserver: hayes.ns.cloudflare.com, meg.ns.cloudflare.com
portfolio: raznulasri.pages.dev, www.durianciku.my
private-site: devops.durianciku.my
EOF

git add . && git commit -m "Cloudflare 1: DNS + Pages"
git push -u origin cloudflare1

gh pr create --fill && gh pr merge --squash --delete-branch
```

---

## ☁️ CLOUDFLARE 2
Origin + proxy + Tunnel

```bash

cd ~/devops-bootcamp
git checkout -b cloudflare2 && mkdir cloudflare2

cat > cloudflare2/notes.txt <<EOF
elastic-ip: 3.0.32.114
web: web.durianciku.my (A record, proxied)
tunnel: cloudflare2.durianciku.my
ssl-mode: Flexible
EOF

git add . && git commit -m "Cloudflare 2: origin + proxy + tunnel"
git push -u origin cloudflare2
gh pr create --fill && gh pr merge --squash --delete-branch

```
---

## ☁️ DOCKER 1
Docker

```bash


```
---