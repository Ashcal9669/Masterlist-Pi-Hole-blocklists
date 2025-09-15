# Masterlist Pi-hole Blocklists

This repository provides curated Pi-hole blocklists to block ads, trackers, spyware, malware, and other unwanted domains. It includes instructions for installation, whitelist management, chunk handling, and troubleshooting for both Docker and host installations.

---

## Repository Contents

- **blocklistA** – General ad servers and pop-ups (e.g., ad.doubleclick.net, doubleclick.com)  
- **blocklistB** – Social media tracking (e.g., facebook.com, instagram.com, snap-prod.appspot.com)  
- **blocklistC** – Video streaming trackers (e.g., youtube.com, ytimg.com, googlevideo.com)  
- **blocklistD** – Malware, phishing, and dangerous domains  
- **blocklist_chunk_aa/ad** – Split chunks of the main blocklists (due to GitHub size limits)  
- **gravity.sql** – Optional dump of Pi-hole gravity database  

---

## Step 1: Prepare Your Environment

**Docker Pi-hole:** Make sure your Pi-hole container is running. Example: `sudo docker ps`  

**Host Pi-hole:** Ensure Pi-hole is installed and running on the host system.

---

## Step 2: Whitelist Essential Domains

Some domains break websites if blocked. Whitelist them first:  

**Docker:**  
`sudo docker exec -it pihole pihole allow google.com youtube.com facebook.com instagram.com snapchat.com ytimg.com gstatic.com gvt1.com`  

**Host:**  
`pihole allow google.com youtube.com facebook.com instagram.com`

---

## Step 3: Add Blocklists to Pi-hole

**Docker:**  
`sudo docker exec -it pihole pihole adlist add https://raw.githubusercontent.com/Ashcal9669/Masterlist-Pi-Hole-blocklists/main/blocklistA`  
`sudo docker exec -it pihole pihole adlist add https://raw.githubusercontent.com/Ashcal9669/Masterlist-Pi-Hole-blocklists/main/blocklistB`  
`sudo docker exec -it pihole pihole adlist add https://raw.githubusercontent.com/Ashcal9669/Masterlist-Pi-Hole-blocklists/main/blocklistC`  
`sudo docker exec -it pihole pihole adlist add https://raw.githubusercontent.com/Ashcal9669/Masterlist-Pi-Hole-blocklists/main/blocklistD`  
`sudo docker exec -it pihole pihole -g`  

**Host:** Add URLs via Admin → Group Management → Adlists, then click **Update Gravity**.

---

## Step 4: Verify Whitelist Domains

**Docker:**  
`sudo docker cp pihole:/etc/pihole/gravity.db /tmp/gravity.db`  
`sqlite3 /tmp/gravity.db "SELECT domain FROM domainlist WHERE type=0;"`  

**Host:**  
`sqlite3 /etc/pihole/gravity.db "SELECT domain FROM domainlist WHERE type=0;"`

---

## Step 5: Remove Specific Domains from Blocklists

1. Copy raw file locally:  
`wget -O ~/Documents/blocklist_chunk_ad.rtf https://raw.githubusercontent.com/Ashcal9669/Masterlist-Pi-Hole-blocklists/main/blocklist_chunk_ad`  

2. Remove domains using `sed` (example: remove Facebook, Instagram, YouTube):  
`sudo sed -i '/facebook\|instagram\|youtube/d' ~/Documents/blocklist_chunk_ad.rtf`  

3. Re-add to Pi-hole as a custom blocklist if needed.

---

## Step 6: Whitelisting Domains Later

If a site breaks, whitelist it:  

**Docker:**  
`sudo docker exec -it pihole pihole allow <domain>`  

**Host:**  
`pihole allow <domain>`

---

## Step 7: Fix Corrupted Gravity Database (Optional)

1. Backup gravity database:  
`sudo docker cp pihole:/etc/pihole/gravity.db /tmp/gravity.db`  
`cp /tmp/gravity.db /tmp/gravity.db.bak`  

2. Dump to SQL:  
`sqlite3 /tmp/gravity.db ".dump" > /tmp/gravity.sql`  

3. Restore:  
`sqlite3 /tmp/gravity_fixed.db < /tmp/gravity.sql`  
`sudo docker cp /tmp/gravity_fixed.db pihole:/etc/pihole/gravity.db`  
`sudo docker restart pihole`

---

## Step 8: Combining Chunk Files

If you want one full list from chunk files:  
`cat blocklist_chunk_aa blocklist_chunk_ab blocklist_chunk_ac blocklist_chunk_ad > combined_blocklist.txt`  

Use `combined_blocklist.txt` as your master blocklist if preferred.

---

## Step 9: Troubleshooting

- sqlite3 not found in Docker: install it on the host or in container.  
- Sites break: check whitelist first.  
- Chunk files show partial content: use raw URLs or combine chunks.

---

## References

Pi-hole Documentation: https://docs.pi-hole.net/
