# 🚀 Ansible Playbook: Update Packages & Reboot if Needed  

This Ansible playbook updates all installed packages on **Amazon Linux** to the latest version and **performs a reboot** if necessary.  

## 📌 Features  
- Updates all installed packages using `yum`.  
- Checks if a system reboot is required using `needs-restarting -r`.  
- Reboots the system if a kernel update or critical change requires it.  

## 📂 Project Structure  
```
📦 ansible-update-playbook  
 ├── 📜 update.yml      # Ansible playbook  
 ├── 📜 README.md       # Project documentation  
 ├── 📜 inventory       # Hosts file (if needed)  
```

## 🛠️ Prerequisites  
- Ansible installed on the control node.  
- Amazon Linux machine(s) configured as a managed node.  
- SSH access to the target machine.  

## 🔧 Usage  

### 1️⃣ Clone the Repository  
```bash
git clone https://github.com/yourusername/ansible-update-playbook.git  
cd ansible-update-playbook
```

### 2️⃣ Update Your Inventory File  
If using an inventory file (`inventory`), ensure your target Amazon Linux instance is listed.  

Example:  
```
[mn]
your-server-ip ansible_user=ec2-user ansible_ssh_private_key_file=~/.ssh/your-key.pem
```

### 3️⃣ Run the Playbook  
```bash
ansible-playbook -i inventory update.yml --ask-become-pass
```
- `-i inventory` → Specifies the inventory file.  
- `--ask-become-pass` → Prompts for the sudo password if required.  

## 📝 Playbook Breakdown  
```yaml
---
- name: Update all packages to the latest version
  hosts: mn
  become: yes  # Ensures tasks run as root
  tasks:
    - name: Update all packages
      yum:
        name: "*"
        state: latest

    - name: Check if reboot is required
      shell: "needs-restarting -r"
      register: kernel_update_check
      ignore_errors: yes
      changed_when: kernel_update_check.rc == 1

    - name: Perform reboot if needed
      reboot:
      when: kernel_update_check.rc == 1
```

## 📌 Notes  
- **Amazon Linux does not have `firewalld` by default**, so make sure necessary ports (e.g., HTTP, SSH) are open via AWS Security Groups.  
- If running on **AWS EC2**, ensure your **security group** allows SSH and other required services.  

## 📜 License  
This project is **open-source** and free to use under the **MIT License**.  

---

### 🔗 **Contributions & Support**  
Have suggestions or found an issue? Open a pull request or create an issue in the **GitHub repository**. 🚀  

