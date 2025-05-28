
تغییر http به https 
در مسیر /etc/gitlab/gitlab.rb/ شده و url را تغییر دهید
```bash
external_url 'https://git.nicholding.ir'
```

سپس مقادیر زیر را از حالت کامنت در بیارید یا value آنرا تغییر دهید :

```bash
gitlab_rails['ldap_enabled'] = true
```
دستور بالا برای فعال‌سازی احراز هویت با LDAP است.

توضیح کامل:
وقتی این گزینه روی true تنظیم شود، GitLab تلاش می‌کند از سرور LDAP (مثل Active Directory یا OpenLDAP) برای اعتبارسنجی کاربران استفاده کند.

کاربرد اصلی:
با فعال‌سازی این گزینه، می‌توانید کاربران را از طریق یک دایرکتوری مرکزی (مثل Active Directory) وارد GitLab کنید، بدون اینکه لازم باشد برای آن‌ها حساب جدید در GitLab بسازید.
```bash
gitlab_rails['ldap_servers'] = YAML.load <<-'EOS'
  main:
    label: 'Active Directory'
    host: '172.30.107.10'
    port: 636
    uid: 'sAMAccountName'
    bind_dn: 'CN=Gitlab Service,OU=Services,OU=Nicholding,DC=nicholding,DC=local'
    password: 'Dev@1404!2025'
    encryption: 'simple_tls' # "start_tls" or "simple_tls" or "plain"
    verify_certificates: false
    smartcard_auth: false
    active_directory: true
    allow_username_or_email_login: false
    lowercase_usernames: false
    base: 'DC=nicholding,DC=local'
    user_filter: '(&(objectCategory=user)(objectClass=user)(memberOf=CN=GitlabDevelopers,OU=Services,OU=Nicholding,DC=nicholding,DC=local))'
EOS
```
این قسمت به GitLab می‌گه تنظیمات LDAP رو با استفاده از ساختار YAML بارگذاری کن.

گزینه main: شناسه‌ی کانفیگ LDAP، اگر چندتا LDAP داشته باشی می‌تونی secondary هم تعریف کنی.

گزینه label: نامی که در UI لاگین GitLab نمایش داده می‌شه.

گزینه host: آدرس IP یا DNS سرور Active Directory

گزینه port: پورت 636 یعنی LDAPS (SSL) (برای LDAP از پورت 389 استفاده میکنیم)

گزینه encryption: روش رمزنگاری (در اینجا TLS مستقیم)

گزینه verify_certificates: چون false هست، گواهی SSL بررسی نمی‌شه (برای تست خوبه، اما در محیط واقعی توصیه نمی‌شه)

```bash
nginx['enable'] = true
nginx['listen_port'] = 80
nginx['listen_https'] = false
```

اگر HAProxy فقط SSL رو Terminate می‌کنه و بعدش ترافیک رو به NGINX داخلی می‌ده : true
در این مدل NGINX داخلی هنوز نقش reverse proxy داخلی رو داره، مثلاً به GitLab Workhorse یا API ها پاس می‌ده.
| گزینه                           | توضیح                                                                      |
| ------------------------------- | -------------------------------------------------------------------------- |
| `nginx['listen_port'] = 80`     | GitLab داخلی روی پورت HTTP (نه HTTPS) گوش می‌دهد.                          |
| `nginx['listen_https'] = false` | پشتیبانی از HTTPS در NGINX داخلی **غیرفعال** است. فقط HTTP پذیرفته می‌شود. |


```bash
letsencrypt['auto_renew'] = false

```
این گزینه باعث می‌شود فرآیند تمدید خودکار گواهی SSL که از Let's Encrypt دریافت شده، غیرفعال شود.

📌 موارد استفاده:
✅ وقتی استفاده می‌کنی:
خودت گواهی SSL رو به‌صورت دستی نصب و تمدید می‌کنی.

از HAProxy یا NGINX خارجی استفاده می‌کنی که SSL را مدیریت می‌کنند.

نمی‌خواهی GitLab گواهی بگیرد یا تمدید کند چون در محیط داخلی هستی یا از گواهی شرکتی استفاده می‌کنی.

❌ وقتی استفاده نمی‌کنی:
اگر می‌خواهی GitLab به صورت خودکار گواهی رایگان بگیره و تمدید کنه، باید این مقدار رو بذاری true.


---

حالا تنظیمات رو ذخیره کن و 
```bash
sudo gitlab-ctl reconfigure
sudo gitlab-ctl restart
sudo gitlab-ctl status
```

تست ارتباط سنجی با ldap
```bash
sudo gitlab-rake gitlab:ldap:check
```
