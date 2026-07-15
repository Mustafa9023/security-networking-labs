### This lab is based on a fictional hospital, **SecureHealth Hospital**. My task in this lab was to set up a secure file server that complies with healthcare data protection standards. For this lab, I used **Kubuntu Linux** as a virtual machine.

The hosptial stracture is as follows:

<img width="357" height="226" alt="image" src="https://github.com/user-attachments/assets/2167c1db-06f1-4d5b-88f6-0f4cf9c979f5" />

- The first thing I did was create the users and groups shown in the picture above. I then used the `passwd` command to set passwords for each user so that I could log in to their accounts later. Finally, I added each user to the corresponding group. and after the creation of the users and the groups i viewd the /etc/passwd and etc/group to make sure that the newly added users and gorups are existed on the system

<img width="465" height="109" alt="image" src="https://github.com/user-attachments/assets/4b5e66dd-2549-41c0-a5a5-2f477c856ad8" />
<img width="502" height="211" alt="image" src="https://github.com/user-attachments/assets/640ad80a-08d5-4ee7-92a6-592c444cdedc" />
<img width="400" height="202" alt="image" src="https://github.com/user-attachments/assets/e6c4e0a0-e6c1-4729-a6aa-2da72cee5217" />
<img width="427" height="402" alt="image" src="https://github.com/user-attachments/assets/93e14e4d-0648-4c18-81e3-befbbc7cfb16" />
<img width="617" height="241" alt="image" src="https://github.com/user-attachments/assets/c72bf675-a6c8-45ce-af77-49674aa09575" />

- I created five directories on the server and configured the access permissions for each directory so that the appropriate users and groups could access them. The directory permissions were configured based on the principle of least privilege, ensuring that users were granted only the minimum level of access required to perform their tasks.

<img width="567" height="300" alt="image" src="https://github.com/user-attachments/assets/e326c311-9315-4f3c-a86f-0eba61c5675a" />


## File Server Requirements:
### 1.  /srv/securehealth/patient_records/
- Doctors full access
- Nurses read only via ACL
- Pharmacy read only via ACL
- Administration NO access
- Files auto-inherit medical group
- Nobody can delete others' files

- I started applying the requirements above by listing the permissions on the patient_records directory. I noticed that the directory group was set to root, so I decided to change it to medical in order to give the medical group full access. After that, I changed the permissions on the directory to 3770. The 3 means that both the sticky bit (1) and SGID (2) will be applied to this directory. The 770 means that the owner has full access, the medical group has full access, and all other groups have zero access. The administration group will have zero access to this directory since it falls into the "others" category. After that, I used setfacl to set up Access Control Lists for the nursing and pharmacy groups. All the commands are shown in the screenshot below.
Notice the difference between the permissions before and after the configuration. Apart from the difference in standard permissions, we can also see the +, T, and s symbols appearing. This confirms that the sticky bit, SGID inheritance, and ACL have all been successfully configured.

<img width="891" height="536" alt="image" src="https://github.com/user-attachments/assets/8fa6dcab-bd94-42cb-9de3-9f7e7ef41ff5" />

### 2. /srv/securehealth/prescriptions/

- Doctors full access
- Pharmacy full access via ACL
- Nurses read only via ACL
- Administration NO access
- Files auto-inherit medical group
- Nobody can delete others' files

- For prescriptions directory i will use the same commands that i used before with patient_records directory. the screen shot from the CLI is showen below:
<img width="850" height="487" alt="image" src="https://github.com/user-attachments/assets/a1ed9934-4502-4b38-9dee-27331321650e" />


### 3./srv/securehealth/nursing_reports/

- Nurses full access
- Doctors read only via ACL
- Administration NO access
- Pharmacy NO access
- Files auto-inherit nursing group
- Nobody can delete others' files

  <img width="751" height="400" alt="image" src="https://github.com/user-attachments/assets/7aa36c85-a74f-4f22-9e8e-11395d955f07" />

### 4. /srv/securehealth/admin_finance/

- Administration full access
- NO access for anyone else
- Files auto-inherit administration group
- Nobody can delete others' files
- All files must be protected with immutable attribute automatically
<img width="922" height="196" alt="image" src="https://github.com/user-attachments/assets/5512779c-a122-4204-ac66-0425cb6420fb" />

### 5. /srv/securehealth/hospital_notices/

- Administration full access
- Everyone else read only via ACL
- Files auto-inherit administration group
- Nobody can delete others' announcement files

<img width="780" height="426" alt="image" src="https://github.com/user-attachments/assets/306d7cb5-8db1-4776-a463-7a3e6e40983b" />


- In the next step, I created a confidential file in the `patient_records` directory using the `dr_smith` account. I then attempted to add the immutable attribute to the file so that it could not be deleted or modified. However, as shown in the command-line interface (CLI), the command failed because it required `sudo` privileges. Since the `dr_smith` user is not a member of the `sudo` group, I switched back to my main account, `mostafa`, which has `sudo` privileges, and used the `sudo chattr +i` command to make the file immutable. After setting the immutable attribute, I verified that the file could no longer be deleted. The complete set of commands is shown in the screenshot below.
 

<img width="977" height="447" alt="image" src="https://github.com/user-attachments/assets/69f251da-854c-4a74-99a7-c05af61cf7b2" />
<img width="805" height="112" alt="image" src="https://github.com/user-attachments/assets/2c5502e5-c4bc-4894-82db-eacf53c7f16c" />

- After completing all the configurations in this lab, I performed several tests to verify that everything had been configured correctly and that the permissions and security settings were working as intended. 

<img width="857" height="621" alt="image" src="https://github.com/user-attachments/assets/1500a1e4-f6de-47e7-a332-395578565b9c" />
<img width="870" height="557" alt="image" src="https://github.com/user-attachments/assets/ce7d961a-1fc3-41d7-82f5-3bef5f0afd8b" />


## The end of the lab


