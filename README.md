# Containerized Plex video system running on an EC2 image within Docker Container using S3 as a backing storage for videos

### This project creates a security group, EC2 instance which runs a container, and small EBS volume in case storage is needed


Deployment:

- Deploy CloudFormation Stack which creates the following resources:

    * EC2 Instance
    * EC2 security group allowing 32400 the default Plex port from the Internet (required for external streaming but the port can change) AND SSH (caution)
    * EBS volume attached to EC2


- This deployment uses S3FS which is an opensource means of mounting S3 bucket as a local file system.  This is not recommended for most use cases and can have performance issues but for this use case it works perfectly

- Unfortunately S3FS uses an IAM user credential for S3 access, does not currently support IAM Role which would be preferred

- To configure Plex you must be on the local network, to mock this up over the internet use this command:
```
ssh -i YOURKEY.pem ec2-user@[EIP or public DNS name] -L 3307:127.0.0.1:32400 -N
```

- Then launch a browser locally and browse to http://127.0.0.1:3307/web which will begin the setup

- (this is the magic part) add a library to your Plex server and check the Alerts area on Plex to watch it index your files

- Optionally but recommended:
    * Purchase Plex Pass if you want to allow offline synchronizing of movies for mobile devices
    * Configure remote access - typically manually setting the port to what is already defined works perfectly (TCP 32400)
    * Allow Fallback to Insecure Connections can be configured in General settings
    * Video quality and  Automatically adjust quality (Beta) are in the Quality settings area

- Then launch a browser locally and browse to http://[EIP or DNS]:3307/web which will begin the setup

## Known issues:

- S3FS uses an IAM user, credentials are stored locally on disk in clear text
- Configuration requires using a loopback as Plex needs to think you are connected locally for the initial few screens

