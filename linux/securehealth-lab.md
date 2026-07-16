# SecureHealth Hospital — Secure File Server Lab

This lab is based on a fictional hospital, **SecureHealth Hospital**. My task was to set up a secure file server that complies with healthcare data protection standards. For this lab, I used **Kubuntu Linux** running in a virtual machine.

## Hospital Structure

The hospital's organizational structure — the users, groups, and departments used throughout this lab — is shown below:

<img width="300" height="112" alt="image" src="https://github.com/user-attachments/assets/0de695e1-a335-42af-9a65-11f8eaca2da9" />

## Step 1: Creating Users and Groups

The first thing I did was create the users and groups shown in the diagram above. I used the `passwd` command to set a password for each user so I could log in to their accounts later, and added each user to their corresponding group. After creating the users and groups, I checked `/etc/passwd` and `/etc/group` to confirm that the new users and groups had been created successfully.

<img width="465" height="109" alt="image" src="https://github.com/user-attachments/assets/4b5e66dd-2549-41c0-a5a5-2f477c856ad8" />
<img width="502" height="211" alt="image" src="https://github.com/user-attachments/assets/640ad80a-08d5-4ee7-92a6-592c444cdedc" />
<img width="400" height="202" alt="image" src="https://github.com/user-attachments/assets/e6c4e0a0-e6c1-4729-a6aa-2da72cee5217" />
<img width="427" height="402" alt="image" src="https://github.com/user-attachments/assets/93e14e4d-0648-4c18-81e3-befbbc7cfb16" />
<img width="617" height="241" alt="image" src="https://github.com/user-attachments/assets/c72bf675-a6c8-45ce-af77-49674aa09575" />

## Step 2: Creating Directories

I created five directories on the server and configured access permissions for each one so that the appropriate users and groups could access them. Permissions were configured based on the principle of least privilege, ensuring that users were granted only the minimum level of access required to perform their tasks.

<img width="567" height="300" alt="image" src="https://github.com/user-attachments/assets/e326c311-9315-4f3c-a86f-0eba61c5675a" />

## File Server Requirements and Configuration

### 1. Patient Records — `/srv/securehealth/patient_records/`

- Doctors: full access
- Nurses: read-only via ACL
- Pharmacy: read-only via ACL
- Administration: no access
- Files auto-inherit the medical group
- Nobody can delete other users' files

I began by listing the current permissions on the `patient_records` directory and noticed the group owner was set to `root`, so I changed it to `medical` to give the medical group full access. I then set the directory permissions to `3770`. The leading `3` applies both the sticky bit (`1`) and the SGID bit (`2`), and the `770` gives the owner and the medical group full access while denying access to everyone else. Since administration falls under "others" here, it has zero access to this directory. Finally, I used `setfacl` to configure ACLs granting read-only access to the nursing and pharmacy groups. The full set of commands is shown below.

Notice the difference between the permissions before and after the configuration. Beyond the standard permission bits, you can also see the `+`, `T`, and `s` symbols appear — confirming that the sticky bit, SGID inheritance, and ACLs were all applied successfully.

<img width="891" height="536" alt="image" src="https://github.com/user-attachments/assets/8fa6dcab-bd94-42cb-9de3-9f7e7ef41ff5" />

### 2. Prescriptions — `/srv/securehealth/prescriptions/`

- Doctors: full access
- Pharmacy: full access via ACL
- Nurses: read-only via ACL
- Administration: no access
- Files auto-inherit the medical group
- Nobody can delete other users' files

For the prescriptions directory, I applied the same approach used for `patient_records`. The commands and CLI output are shown below:

<img width="850" height="487" alt="image" src="https://github.com/user-attachments/assets/a1ed9934-4502-4b38-9dee-27331321650e" />

### 3. Nursing Reports — `/srv/securehealth/nursing_reports/`

- Nurses: full access
- Doctors: read-only via ACL
- Administration: no access
- Pharmacy: no access
- Files auto-inherit the nursing group
- Nobody can delete other users' files

<img width="751" height="400" alt="image" src="https://github.com/user-attachments/assets/7aa36c85-a74f-4f22-9e8e-11395d955f07" />

### 4. Admin Finance — `/srv/securehealth/admin_finance/`

- Administration: full access
- No access for anyone else
- Files auto-inherit the administration group
- Nobody can delete other users' files
- All files must automatically be protected with the immutable attribute

<img width="922" height="196" alt="image" src="https://github.com/user-attachments/assets/5512779c-a122-4204-ac66-0425cb6420fb" />

### 5. Hospital Notices — `/srv/securehealth/hospital_notices/`

- Administration: full access
- Everyone else: read-only via ACL
- Files auto-inherit the administration group
- Nobody can delete other users' announcement files

<img width="780" height="426" alt="image" src="https://github.com/user-attachments/assets/306d7cb5-8db1-4776-a463-7a3e6e40983b" />

## Testing File Immutability

Next, I created a confidential file in the `patient_records` directory using the `dr_smith` account, then attempted to set the immutable attribute on it so it couldn't be deleted or modified. As shown in the CLI output, the command failed because it required `sudo` privileges, and `dr_smith` is not a member of the `sudo` group. I switched back to my main account, `mostafa`, which has `sudo` privileges, and ran `sudo chattr +i` to make the file immutable. I then verified that the file could no longer be deleted. The full set of commands is shown below.

<img width="977" height="447" alt="image" src="https://github.com/user-attachments/assets/69f251da-854c-4a74-99a7-c05af61cf7b2" />
<img width="805" height="112" alt="image" src="https://github.com/user-attachments/assets/2c5502e5-c4bc-4894-82db-eacf53c7f16c" />

## Verification Testing

After completing all the configurations in this lab, I performed several tests to verify that everything had been set up correctly and that the permissions and security settings were working as intended.

<img width="857" height="621" alt="image" src="https://github.com/user-attachments/assets/1500a1e4-f6de-47e7-a332-395578565b9c" />
<img width="870" height="557" alt="image" src="https://github.com/user-attachments/assets/ce7d961a-1fc3-41d7-82f5-3bef5f0afd8b" />

## Conclusion

This lab covered user and group management, directory permission design based on least privilege, ACLs for fine-grained access control, SGID and sticky bit inheritance, and file immutability using `chattr`. Together, these controls enforce role-based access across hospital departments while meeting the confidentiality and integrity requirements expected of healthcare data.
