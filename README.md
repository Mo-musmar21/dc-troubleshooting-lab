## Project Goal: Preparing for Amazon DCT Interviews

### Why this lab matters

In this project, I’m building a Linux server environment on my Windows machine and using it to troubleshoot three simulated outages, so that I can prepare for data center technician interviews by practising real diagnostics and having documented incidents to talk about.

## Building a Linux Server Environment on Windows

### Setting up WSL and Ubuntu

In this step, I’m setting up a Linux environment on Windows using WSL and Ubuntu, along with the network diagnostic tools and a GitHub repository, so that I can practise troubleshooting the way a data center technician does and document every incident as I go.

![Building a Linux Server Environment on Windows](https://nextwork.ai/mohammad-musmar/uploads/2c3fba64-9791-4049-ba83-e18c5fd4b8f8_8ankh0he)

### Verifying the installation

I ran wsl -l -v in PowerShell and saw Ubuntu is running with version 2.

## Running a Network Diagnostic Sweep

### Practicing the Layer 1-2-3 diagnostic sequence

In this step, I’m practising the Layer 1 → 2 → 3 network troubleshooting sequence and checking that services respond on their ports, so that I can explain a structured approach to connectivity problems in interviews and have a baseline of normal readings to compare against when something breaks.

### Interpreting diagnostic command output

I ran commands like ip link show and ip addr show eth0, which showed me that eth0 was UP and had an IP address. ip route showed my default gateway, and pinging 127.0.0.1 and 8.8.8.8 with 0% packet loss confirmed both local and internet connectivity. Traceroute showed the path to 8.8.8.8, including some \* \* \* hops that were just routers not replying. ss -tlnp | grep :80 showed nginx listening on port 80, curl -I http://localhost returned HTTP/1.1 200 OK, and dig google.com resolved to IP addresses. I saved these readings as my baseline so I can compare against them when something breaks.

![Running a Network Diagnostic Sweep](https://nextwork.ai/mohammad-musmar/uploads/2c3fba64-9791-4049-ba83-e18c5fd4b8f8_tvwtaei0)

## Diagnosing and Fixing a Simulated Service Outage

### Intentionally breaking nginx to practice structured troubleshooting

In this step, I’m simulating a web server outage caused by a bad configuration change, so that I can practise diagnosing a “server not responding” problem step by step, from the symptom down to the root cause, and then fixing and verifying it.

![Diagnosing and Fixing a Simulated Service Outage](https://nextwork.ai/mohammad-musmar/uploads/2c3fba64-9791-4049-ba83-e18c5fd4b8f8_fraghb5e)

### Four-step diagnostic sequence: status, restart, logs, config validation

I first checked the service status with systemctl status nginx, which showed it was inactive (dead). Then I tried to start it with sudo systemctl start nginx, but it still wouldn’t run, which pointed to a configuration problem. Then I read the logs with journalctl -u nginx, which showed an \[emerg\] configuration error, and finally I ran sudo nginx -t, which pinpointed the exact problem: an unknown directive “BROKEN” in /etc/nginx/nginx.conf. I removed it, validated the config, restarted nginx and confirmed HTTP 200 OK.

## Responding to a Disk Capacity Alert

### Simulating a real data center environmental alarm

In this step, I’m simulating a disk capacity alert by filling a test filesystem, so that I can practise finding what’s using the space with tools like df and du, freeing it up safely, and documenting the incident the way a technician responds to a “disk at 85%” alarm.

![Responding to a Disk Capacity Alert](https://nextwork.ai/mohammad-musmar/uploads/2c3fba64-9791-4049-ba83-e18c5fd4b8f8_tuu8i1hz)

### Investigating and remediating the disk issue

I used df -h to identify which filesystem was full: /mnt/dc-lab-disk was above the 80% threshold. Then I used du -sh /mnt/dc-lab-disk/\* | sort -rh | head -5 to find the largest files, which showed fill-file taking about 80 MB. I used find to rule out runaway log files and ls -la to check who owned the file and when it was created. After confirming it was just a temporary file, I removed it and ran df -h again to confirm the usage had dropped back to normal.

## Reflections and Takeaways

### Key tools and concepts learned

In this project I used Ubuntu on WSL as a Linux server on my Windows machine, with Git and GitHub to document and version my work. For networking I used ip link, ip addr and ip route to check the interface, IP address and gateway, ping and traceroute to test reachability and find where a path breaks, and dig to check DNS. For services I used systemctl, journalctl, ss -tlnp, curl and nginx -t, and for storage I used df, du, find and ls -la.

The biggest concept I learnt is to troubleshoot in a structured way instead of guessing. For connectivity, that means working bottom-up: link, then IP and route, then gateway, internet and DNS, and finally the service itself. For a failed service, the flow is check the status, try a restart, read the logs, and validate the config, which led me straight to the exact line that broke nginx. I also learnt how useful a baseline of healthy readings is for spotting what changed. With disk alerts, I learnt to find what’s using the space and check what it is before deleting anything. Finally, I learnt that every incident should be documented with the symptom, root cause, fix and verification, so the next shift can follow what happened.

### Time and challenges

This project took me approximately 3–4 hours. The most challenging part was the disk capacity incident, because unlike the nginx outage nothing was obviously broken: the server looked healthy while the disk was quietly filling up. I had to understand the difference between df, which shows how full each filesystem is, and du, which shows which files are using the space, and use them together to go from the alert to the actual file. I also learnt that a 100 MB disk only shows about 93 MB usable because of filesystem overhead. The hardest part was the mindset: before deleting anything, you need to check what the file is, who owns it, and whether something is still writing to it, because deleting the wrong file in a real data center could cause a bigger outage than the full disk.

I did this project today to learn how to troubleshoot real server problems from the Linux command line, working through network, service and disk issues in a structured way, and to document each incident like a data center technician would. Another skill I want to learn is fiber and structured cabling: understanding single-mode and multi-mode fiber, the different connectors and optics, how to clean and test fiber links, and how to run, label and manage cables properly in racks. Many network issues in a data center turn out to be physical, so knowing how to handle cabling well is an important part of the job.
