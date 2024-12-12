# tcpdump Analyzing Network Traffic 1

<h2>Description</h2>
In this task, I used tcpdump to example a pcap for suspicious malware that showed a GET request for a audiodg.exe from vietnam. 


<h2>Languages and Utilities Used</h2>

- <b>tcpdump</b>
- <b>linux CLI</b>


<h2>Environments Used </h2>

- <b>Unbuntu</b> 

<br />
<br />
I used tcpdump to count how many paackets were in the pcap before reading the 2021 pcap with -tt to instructs tcpdump to print an unformatted timestamp on each dump line displayed as the number of seconds and microseconds since January 1, 1970, 00: 00 :00 GMT.

![1) opening example pcap with -tt for UTC time](https://github.com/user-attachments/assets/95d44d44-f8e0-4d25-9d1c-7a0bbbe82eea)

<br />
<br />
I noticed a lot of traffic from host 10.0.0.168 to destination 103.232.55.148. 

![2) a lot of traffic between these two ips](https://github.com/user-attachments/assets/6738337f-c5e1-4007-a08f-653384890c39)

<br />
<br />  
Narrowing the search, I counted the packets filtering only for port 80 traffic which reduced the output by ~1000 packets. I filtered further by including host 10.0.0.168 and piping to grep -E "GET|POST". I used -E to allow grep to interpret "GET|POST" as a pattern that matches lines containing either "GET" or "POST". Without -E, I would need to escape the | character, like this: "GET\|POST".

![3) filtering for port 80 on host ip with get|post grep](https://github.com/user-attachments/assets/4c1156fe-6103-4407-8838-201abb9f20e4)

<br />
<br />
The output shows a GET for an "audiodg.exe" from 103.232.55.148.

![4) noticed a get request for an executable from sus ip](https://github.com/user-attachments/assets/510874d0-d5c3-41e9-8ace-6cf5bc4391b5)

<br />
<br />
Doning some OSINT, the audiodg.exe would normally be a Windows Audio Device file, however, the ip is from Vietnam and the ASN is not associated with Microsoft. 

![5) osint shows exe is for windows audio and whois shows vietnam](https://github.com/user-attachments/assets/4a8c54b5-3cd0-4907-ad5b-3cff599082dc)

<br />
<br />
Running tcpdump to grep specifically for "audiodg.exe" outputs two packets with the first showing a GET request to a suspicious url. 

![6) grep for exe shows a get request from a url](https://github.com/user-attachments/assets/1dc197d2-f89e-447b-8af7-845ed7a2392f)

<br />
<br />
Using the following tcpdump command to read the file in ASCII with -A then piping to grep for "audiodg.exe" along with -A 500 after grep to display the matching line along with the next 500 lines of output after each match.

![7) command to open the ascii of first payload](https://github.com/user-attachments/assets/dedd8b18-a404-4d94-a401-e50d19937458)

<br />
<br />  
The results show the destination ip opf 13.107.5.80 with the GET request for the suspicious http url and a host from api.bing.com.

![8) packet shows dst ip ](https://github.com/user-attachments/assets/e9038798-382b-4757-bb28-410813d7e319)

<br />
<br />
Looking at whoisdomain for the ip 13.107.5.80 shows it is microsoft bing that is being used. This leads me to believe that the request uses bing to forward the request to the suspicious url. 

![9) dst ip is bing to grab url hosting exe](https://github.com/user-attachments/assets/10147c7b-bddc-452f-b162-f8dd9c19344f)

<br />
<br />
Decoding the suspicious url and running it through virustotal shows 7 vendor flags for malicious malware. 

![10) flagged as malicious](https://github.com/user-attachments/assets/97cf47f4-092e-4475-ad6f-f2c701c366c7)

<br />
<br />
