---
title: "Use SSH certificate authentication instead"
seoTitle: "A Guide to Certificate-Based Authentication"
seoDescription: "Discover the power of certificate-based authentication in SSH with my comprehensive guide. Learn how to enhance security and streamline access management."
datePublished: Tue Feb 13 2024 14:19:30 GMT+0000 (Coordinated Universal Time)
cuid: clskg915w000208kzevv32nh4
slug: use-ssh-certificate-authentication-instead
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/4Mw7nkQDByk/upload/c5bd76d83df42daa9d8ed0db10348ed8.jpeg
tags: authentication, security, ssh, certificates, cybersecurity-1, remote-access

---

## Why you need certificate based authentication in SSH?

We all know that the most beginner friendly method to ssh is to use password based authentication. But it is susceptible to a wide range of attacks including Brute Force Attacks, Password reuse, Phishing attacks, Keylogging, .... and the list keeps going on.

Next the most famous authentication method is public key based authentication and is also the recommended method for individual users, but problem arises when there is a need to scale.

Lets say there are 10 users and 5 hosts where some users need to be authenticated to different set of hosts and there is an admin managing the key pairs. It quickly gets messy to handle the set of key pairs across all the different machines, there is a need to copy the key pairs to corresponding hosts and users.

In contrast, certificate-based authentication offers a streamlined solution, centralizing authentication through certificate authorities (CAs) which can be the admin and providing enhanced security, granular access control, and simplified management, making it a preferable choice for securing such environments.

### User, Host and Certificate Authority

**Host** will be the machine that we want to connect using SSH.

**Certificate Authority** is the entity that will sign and issue certificates for both the user and the host. Both the user and the host have to be configured to trust the certificate authority.

User is the machine that will login to the host using SSH.

### Added benefits of certificate authentication

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1707828344247/96401a81-c98b-48ac-93e7-161ae30ff527.png align="center")

While dealing with public key authentication, after the key exchanges we reach this step where we are required to check the fingerprint to verify the authenticity of the host. DON'T LIE How many times have you actually verified that fingerprint instead of just blindly typing "YES".  
This possesses an actual security threat. The use of certificates eliminates this step (though you must do it once for trusting the CA). But after that you can don't have to do it for any other host or users later.  
  
Also in case when the private key of lets say the host is leaked and so they issue new pair of keys. Now when the user tries to ssh into the host they are presented with this warning "IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!"

![Warning: Remote Host Identification Has Changed error and solution -  nixCraft](https://www.cyberciti.biz/media/new/faq/2006/09/ssh-remote-host-identification-has-changed.jpg align="left")

This disrupts the workflow and a lot of time is spent clarifying this issue.

To address these issues you can use Certificate Based Authentication.

## How to do certificate based authentication?

We will consider 3 machines to simplify the process. User machine, Host machine and CA machine (admin). There can be a lot of hosts and users, but they all need to trust the CA for this to work. They may also trust multiple CAs. We will also go through the trusting part.

There may be many variations of this process ,where we have USER CA, HOST CA or single CA with different user and host singing key or even single key for both host and user. We will continue with single CA having two keys one for signing user certificates and other for host certificate.

## To start with the process:

### **CA / Admin machine:**

Generate the host certificate signing key.

```bash
ssh-keygen -t ed25519 -f ca_host_key
```

This generates two files keep the private key safe.

The user need to trust `ca_host_key.pub` . To do so we need to distribute this file to all the users and trust it. Trusting can be done system wide as well as per user basis.

* Per user trusting: edit `~/.ssh/ssh_known_hosts`
    
* System wide trusting for all the users edit `/etc/ssh/ssh_known_hosts`
    

In both the cases add this line

### in the User Machine:

```bash
@cert-authority * <ca_host_key.pub>
```

Replace the &lt;ca\_host\_key.pub&gt; with the contents of that file.

The `*` *wildcard allows us to connect to all hosts. If you want to limit it to your domain then replace it with your domain* `*.example.com`

### **Host Machine:**

We need the host public key of the host. This key is found in `/etc/ssh/ssh_host_ed25519_key.pub` . If not present you can generate new set of host keys using `ssh-keygen -A.` This generates host key for all key types.

We have to copy this file to the CA machine to sign it using the private key that we generated.

After copying `ssh_host_ed25519_key.pub` to the CA machine. Sign it using this command

### in the **CA Machine:**

```bash
ssh-keygen -s ca_host_key -I "Host1" -h -V +52w ssh_host_ed25519_key.pub
```

Here not specifying the principals using -n allows all user to connect to the host.

You can read more about the flags in the man page of `ssh-keygen.`

This generates a file `ssh_host_ed25519_key-cert.pub` .

Copy this file back to the host machine. Place this file in `/etc/ssh/` in the host machine.

### Host Machine:

Host will offer this certificate to all the users trying to connect to it from now after adding this under `/etc/ssh/sshd_config`.

```bash
HostCertificate /etc/ssh/ssh_host_ed25519_key-cert.pub
```

Restart the ssh server.

```bash
sudo systemctl restart ssh
or
sudo systemctl restart sshd
```

### **In CA machine:**

Create user certificate signing key:

```bash
ssh-keygen -t ed25519 -f ca_user_key
```

Set the host to trust the user certificate signing key:

Copy the `ca_user_key.pub` to the host machine at `/etc/ssh/` from the CA.

### In the Host machine

edit the file `/etc/ssh/sshd_config` and add

```bash
TrustedUserCAKeys /etc/ssh/ca_user_key.pub
```

Restart the ssh server

```bash
sudo systemctl restart ssh
or
sudo systemctl restart sshd
```

### User machine

Create a key pair for the user

```bash
ssh-keygen -t ed25519 -f user_key
```

Copy over the `user_key.pub` to the CA machine to issue the user certificate.

### In CA machine:

Sign the public key using

```bash
ssh-keygen -s ca_user_key -I "user_name" -n host-name -V +52w user_key.pub
```

Here you must specify the principals using -n followed by the host name that you want to authenticate to, else it won't work.

### In User machine

Now copy back the created -cert file to the user machine and put it in `~/.ssh/`

Delete the know hosts file or remove the entry of the host from the known\_hosts file in the user machine.

Now when the user tries to ssh to the host, he should not be presented with the TOFU (Trust On First Use) warning, or when they change their keys, they could just issue a new certificate for that key.  
Though any mishap from the CA still causes problem but now we only focus on the CA instead of all the hosts and the users.

Drop a like if this helps you.  
If you need further help you may read these articles:  
[https://thinkingeek.com/2020/06/06/using-ssh-certificates/](https://thinkingeek.com/2020/06/06/using-ssh-certificates/)

[https://access.redhat.com/documentation/en-us/red\_hat\_enterprise\_linux/6/html/deployment\_guide/sec-using\_openssh\_certificate\_authentication](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/sec-using_openssh_certificate_authentication)  
*But do care that above link has an error in signing the key, to list the principals it is not -Z but -n.*

## Conclusion

In conclusion, certificate-based authentication in SSH offers robust security and streamlined management for remote access. Through this guide, we've explored its implementation process and highlighted its benefits over traditional password-based authentication. From enhanced resistance to brute-force attacks to centralized control and improved auditability, certificates provide numerous advantages. Thank you for joining us on this journey to bolster security in your organization. If you found this content valuable, please consider giving it a like and sharing it. Stay tuned for more informative articles. Keep your systems secure, and happy SSHing!