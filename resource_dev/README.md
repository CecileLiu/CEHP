# Supplement  
  
## Linux command  
  
### Read File/Preview File  
Here’s a few commands that can replace “cat” when "cat" or "head" is not allowed: ([source](https://medium.com/@ahmed.hilal/tryhackme-ctf-pickle-rick-walkthrough-1455ffd6863b))
*  `tac Sup3rS3cretPickl3Ingred.txt`
*  `less Sup3rS3cretPickl3Ingred.txt`
*  `strings Sup3rS3cretPickl3Ingred.txt`
*  `grep . Sup3rS3cretPickl3Ingred.txt`
*  `cp Sup3rS3cretPickl3Ingred.txt /dev/stdout`
*  `while read line; do echo $line; done < *  Sup3rS3cretPickl3Ingred.txt`  
  
## Network Knowledge  
  
### Common Port - Services  
1. HTTP: TCP, 80, 
2. HTTPS: TCP, 443
3. FTP: TCP, 20,21
4. SSH: TCP, 22
5. DNS: UDP, 53
6. SMTP: TCP, 25, 用於傳送電子郵件
7. POP3: TCP, 110, 用於接收電子郵件
8. IMAP: TCP, 143, 用於管理電子郵件，並允許在多個設備上同步郵件
9. SNMP: UDP, 161, 162, 用於監控和管理網路設備（可以提供非常多資訊，特別是user資訊）
10. DHCP: UDP, 67, 68
11. RTP: UDP, 5000~65535之間, 用於傳輸實時資料，如音頻和視頻流
12. NTP: UDP, 123, 用於同步計算機的時間
13. 另外值得一提的是SMB，SMB是windows的文件分享協定，Linux的版本稱為NFS。CIFS是SMB的最新版本。SAMBA是SMB/CIFS的實現