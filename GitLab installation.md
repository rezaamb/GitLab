# GitLab Installation 


### Update System
```bash
sudo apt-get update && sudo apt-get upgrade -y
```

### Required Packages
#### Ubuntu/Debian:
```bash
sudo apt-get install -y curl openssh-server ca-certificates tzdata
```
---

## 2. Install GitLab

### Ubuntu/Debian:
```bash
curl -s https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
sudo apt-get install gitlab-ce -y
```


---

## 3. Initial Configuration

### Edit Config:
```bash
sudo vim /etc/gitlab/gitlab.rb
```
Set external URL:
```ruby
external_url 'https://git.nicholding.ir'
```

### Apply Config:
```bash
sudo gitlab-ctl reconfigure
```

### Check Status:
```bash
sudo gitlab-ctl status
```

---


## 4. GitLab CLI Tools

### `gitlab-ctl`
- Start: `sudo gitlab-ctl start`
- Stop: `sudo gitlab-ctl stop`
- Reconfigure: `sudo gitlab-ctl reconfigure`
- Logs: `sudo gitlab-ctl tail`

### `gitlab-rake`
- Health check: `sudo gitlab-rake gitlab:check`
- Backup: `sudo gitlab-rake gitlab:backup:create`
- Password reset: `sudo gitlab-rake "gitlab:password:reset"`

### `gitlab-runner`
- Register: `sudo gitlab-runner register`
- Start: `sudo gitlab-runner start`

### `glab` (GitLab CLI)
- Auth login: `glab auth login`
- Create project: `glab project create`
- MR create: `glab mr create`

### `curl`, `httpie`, `python-gitlab`
- API access via token
- Great for scripting and automation

---
