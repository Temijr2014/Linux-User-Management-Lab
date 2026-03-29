# Linux-User-Management-Lab
“A hands-on Linux lab to create users, assign groups, manage file and directory permissions, and test access control.”
Goal: Learn to manage users, groups, and permissions confidently.
# Step 1: Create users
We’ll make 3 users: admin, analyst, guest
```
# Create admin user with home directory
sudo useradd -m admin
sudo passwd admin

# Create analyst user
sudo useradd -m analyst
sudo passwd analyst

# Create guest user
sudo useradd -m guest
sudo passwd guest

# ✅ Check users exist
getent passwd | grep -E 'admin|analyst|guest'
```
<img width="2044" height="1216" alt="vlcsnap-2026-03-30-00h09m02s495" src="https://github.com/user-attachments/assets/b6754e8e-1ccd-4483-b26d-7a424eab025d" />

# Step 2: Create groups
We’ll make 2 groups: SOC and IT
```

sudo groupadd SOC
sudo groupadd IT

# ✅ Check groups
getent group | grep -E 'SOC|IT'
```
<img width="1882" height="1196" alt="vlcsnap-2026-03-30-00h09m18s404" src="https://github.com/user-attachments/assets/b28b39ba-deb3-426c-8387-58e9a792dafe" />

# Step 3: Assign users to groups
User plan:
| User    | Groups   |
|---------|----------|
| admin   | SOC, IT  |
| analyst | SOC      |
| guest   | IT       |
```
# Add admin to SOC and IT
sudo usermod -aG SOC,IT admin

# Add analyst to SOC
sudo usermod -aG SOC analyst

# Add guest to IT
sudo usermod -aG IT guest

# ✅ Verify group membership
groups admin
groups analyst
groups guest
```
<img width="1873" height="244" alt="vlcsnap-2026-03-30-00h09m50s603" src="https://github.com/user-attachments/assets/cebc4eaa-8ac5-46f6-a617-55ab654d33c3" />

# Step 4: Create directories for groups
```
# /srv/SOC → only SOC members
# /srv/IT → only IT members
sudo mkdir -p /srv/SOC
sudo mkdir -p /srv/IT
```
# Step 5: Change ownership of directories
```
# Owner = root, Group = SOC or IT
sudo chown root:SOC /srv/SOC
sudo chown root:IT /srv/IT
```
<img width="2043" height="407" alt="vlcsnap-2026-03-30-00h10m15s680" src="https://github.com/user-attachments/assets/bba71f66-fd00-4e11-bccc-7fc72bc8b01f" />

# Step 6: Set permissions
Only group members can read/write. Others have no access.
```
# SOC directory
sudo chmod 770 /srv/SOC

# IT directory
sudo chmod 770 /srv/IT
```
Permission explanation:
| Directory | Owner | Group | Permissions | Meaning |
|-----------|-------|-------|------------|---------|
| /srv/SOC  | root  | SOC   | 770        | Owner & group full, others none |
| /srv/IT   | root  | IT    | 770        | Owner & group full, others none |
# Step 7: Test access
Switch to each user:
```
su - admin
su - analyst
su - guest
```
Try accessing directories:
```
ls /srv/SOC
ls /srv/IT
```
.✅ Only users in the group should succeed
.❌ Others should get “Permission denied”
# Step 8: Optional – Restrict files inside directories
```
# Create a file for SOC team
sudo touch /srv/SOC/secret.txt
sudo chown root:SOC /srv/SOC/secret.txt
sudo chmod 660 /srv/SOC/secret.txt

# Create a file for IT team
sudo touch /srv/IT/config.txt
sudo chown root:IT /srv/IT/config.txt
sudo chmod 660 /srv/IT/config.txt
```
✅ Test:
.Users in the group → read/write ✅
.Others → cannot access ❌
# Step 9: Explore the system files
./etc/passwd → user accounts
./etc/shadow → passwords (locked or hashed)
./etc/group → group membership
```
cat /etc/passwd | grep admin
cat /etc/group | grep SOC
```
<img width="2066" height="1210" alt="vlcsnap-2026-03-30-00h10m49s812" src="https://github.com/user-attachments/assets/c5f92573-9ecd-4727-8918-0caa8ac3bf7a" />

# Step 10: Bonus – Add sudo privileges
Give admin sudo rights:
```
sudo usermod -aG sudo admin
```
Test as admin:
```
su - admin
sudo whoami
# Should return root
```
✅ Skills Learned
.Creating users & groups
.Assigning users to groups
.File and directory ownership (chown)
.Permissions (chmod)
.Reading /etc/passwd, /etc/group, /etc/shadow
.Using sudo for privilege escalation
.Testing user access
