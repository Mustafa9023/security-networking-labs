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
