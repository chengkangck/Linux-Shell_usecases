## 1. Linux system send alert email script  
1. Install mailx tool  
2. Edit mailx configuration file  
3. Add the following content  
4. Send mail test  
## 2. Abnormal summary
2.1. Exception information  
2.2. Open authorization code  

## 1. Linux system send alert email script  
Background: external server  
need:  
  1. Install mailx tool  
  yum install mailx -y   
  
  2. Edit mailx configuration file  
  
  vim /etc/mail.rc  

  3. Add sending email information  

set from=gblfyyxz@163.com smtp=smtp.163.com  
set smtp-auth-user=gblfyyxz@163.com smtp-auth-password=Authorization code    
set smtp-auth=login  

  4. Turn on authorization  
  5. Send mail test  
  echo "this is test mail" |mail -s "monitor" Another email address (target email address)  

  
