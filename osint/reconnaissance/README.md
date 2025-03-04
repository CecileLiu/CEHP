# Reconnaissance  
  
## What is Reconnaissance?  
  
## What tools are for reconnaissance?  
nmap, gobuster, ffuf  

## nmap  
假設我們的IP是10.0.2.4/24
1. 在同樣的子網路下，查看還有哪些host
    * nmap -sn 10.0.2.*
    * nmap -sn 10.0.2.4/24  
    但是有時候這樣掃不太到東西，所以可以再加其他的參數（-PR, -PU, -PE, -PP, -PM, -PS, -PA, -PO）
2. 這些host有哪些port
    * nmap -Pn 10.0.2.*
    * sudo nmap 10.0.2.5 (某個host)  
    上面的掃描如果掃不到東西，也可以試著加入其他的參數（-sT, -sS, -sX, -sM, -sA）
    * sudo nmap -p- 10.0.2.5 (掃描所有的port)
    * sudo namp -sU 10.0.2.5 -p53, 137-139, 161, 1900, 5353 
3. 從掃描結果看出有沒有防火牆
    ```
    Starting Nmap 7.01 ( https://nmap.org ) at 2021-04-25 10:45 CST
    Note: Host seems down. If it is really up, but blocking our ping probes, try -Pn
    Nmap done: 1 IP address (0 hosts up) scanned in 2.00 seconds
    ```  
    這些訊息表示防火牆阻擋了 Nmap 主機存活的刺探封包。
    Nmap 掃描之前，會向 TCP Port 80 與 443 發送 SYN 封包。  
    並藉由回應的 ACK 封包或 ICMP 封包 (Destination Port unreachable) ，確認主機是否存在。  
    若是主機前端的防火牆直接拒絕回應 TCP Port 80 與 443 的 SYN 封包，就會得到上面訊息的回應。  
    此時，若讀者非常確定主機有上線，可以下 -Pn 參數。直接請 Nmap 跳過存活刺探步驟，直接進行掃描。
4. 從掃描結果看防火牆規則 ([source](https://hack543.com/nmap-tutorial/#penci-%E9%98%B2%E7%81%AB%E7%89%86%E4%BF%9D%E8%AD%B7%E4%B8%8B%E7%9A%84%E9%80%9A%E8%A8%8A%E5%9F%A0%E6%8E%83%E6%8F%8F))  
如果用nmap -Pn -T4 <target-IP>去掃，解讀出來的結果:  
    * 防火牆使用白名單規則
        「Not shown: 998 filtered ports」訊息：表示 998 個 Port 的連線，全部被防火牆過濾了。
        ```
        PORT     STATE  SERVICE
        3306/tcp closed mysql
        3389/tcp closed ms-wbt-server
        ```  
        僅開放 TCP Port 3306、3389，只是現在主機上的這兩個port沒有開。如果顯示open就是有開
    * 防火牆使用黑名單規則  
        「Not shown: 998 closed ports」訊息：預設允許全部，只是現在主機上的這些port都關閉了
        ```
        PORT     STATE    SERVICE
        3306/tcp filtered mysql
        3389/tcp filtered ms-wbt-server
        ```  
        表示 TCP Port 3306 與 TCP Port 3389 的連線，雖然有開，但被防火牆過濾了
    
5. 這些host的port在執行什麼服務、有沒有在運行一些常見的服務，知道服務之後就可以去查詢這些服務有哪些漏洞或弱點可以利用
    * sudo nmap -O 10.0.2.5 (最後IP的部分也可以替換成 10.0.2.* --open) (只列出有open的)
    * sudo nmap -sV 10.0.2.5 (最後IP的部分也可以替換成 10.0.2.* --open)
    * sudo nmap -sC 10.0.2.5 (最後IP的部分也可以替換成 10.0.2.* --open)
    * sudo nmap -sVC -p445, 3389 10.0.2.5 (最後IP的部分也可以替換成 10.0.2.* --open)
    * sudo nmap -sV smb-os-discovery.nse 10.0.2.5 (Determine the OS, computer name, domain, workgroup, and current time over the SMB protocol)
    * sudo nmap -sU -p161,53,137-139,161,1900,5353 10.0.2.5 (最後IP的部分也可以替換成 10.0.2.* --open)
6. 發現這些服務之後可以用nmap的scripts來獲得更多的資訊
    * sudo nmap -p 80,443 --script=http-title <target-ip-or-domain>
    * sudo nmap -p 80,443 --script=http-enum <target-ip-or-domain>  
    [ref1](https://www.stationx.net/nmap-scripts/)  
    [ref2](https://research.securitum.com/nmap-and-12-useful-nse-scripts/)  
    [ref3](https://github.com/ifding/useful-scripts/blob/master/linux/nmap-cheat-sheet.md)
7. 知道服務後，課本上的下一步就是要列出使用者名稱，然後再用hydra來暴力破解


## gobuster  
[GITHUB](https://github.com/OJ/gobuster)  
  
### Is used for  
1. Directory and File Enumeration: Gobuster can brute-force URIs to find hidden directories and files within a website. This is particularly useful for penetration testers looking to uncover sensitive information that may not be directly accessible through the website's navigation. (發現隱藏的目錄及文件)
2. DNS Subdomain Enumeration: The tool can also be used to discover DNS subdomains associated with a target domain. This is crucial for identifying potential attack vectors that may exist on subdomains that are not immediately visible.
3. Virtual Host Enumeration: Gobuster can enumerate virtual host names on target web servers. This is important for identifying misconfigured servers that may expose additional content.  
4. Support for Various Protocols: In addition to standard web directories, Gobuster can be used to enumerate resources on **Amazon S3 buckets**, **Google Cloud buckets**, and **TFTP servers**, expanding its utility in different environments.  
  
### Additional features  
cusomizable wordlist, support HTTP/HTTPS, multi-thread  
  
### Modes of operation  
![gobuster_modes_global_variables](gobuster_modes_global_variables.png)  
img credit: https://blog.csdn.net/B_l_a_nk/article/details/135109164  
#### Example Usage  
1. `dir` mode  
```gobuster dir -u <target_url> -w <wordlist_path> -x <file_extensions>```  
2. `dns` mode  
```gobuster vhost -u <target_ip> -w <wordlist_path>```  
3. `s3` mode  
```gobuster s3 -b <bucket_name>```  
4. `fuzz` mode: allows for fuzzing requests by replacing a specified keyword in the URL, headers, or request body.  
```gobuster fuzz -u <url> -w <wordlist_path> -k```
  
