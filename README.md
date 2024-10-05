# spring-boot-bosh-release

This document explains how to set up the `spring-boot-bosh-release`, create a BOSH release, and deploy the Spring Boot application using the provided manifest.

## Prerequisites

- **BOSH CLI**: Ensure you have BOSH CLI installed.
- **BOSH Director**: Ensure you have access to a BOSH environment with a director deployed.
- **Stemcell**: The deployment will use the `ubuntu-jammy` stemcell

## Steps to Create and Deploy the BOSH Release

### 1. Clone the Repository

First, clone the repository containing your BOSH release:

```bash
git clone https://github.com.nhairlahovic-evoila/spring-boot-bosh-release.git
cd spring-boot-bosh-release
```

### 2. Initialize the Submodule

The `greeting-service` app is referenced as a submodule. Initialize and update the submodule:

```bash
git submodule update --init
```

### 3. Add Java Blob

Add the Java JDK as a blob for the release. The JDK tarball is typically stored inside the `blobs` folder of your BOSH release repository. To download the JDK tarball, you can fetch it from the official Oracle or OpenJDK sources.

In this project, the official Oracle JDK is used. You can download the Oracle JDK 17 from the following location:

- **Oracle JDK**: [Oracle JDK 17 Download](https://www.oracle.com/java/technologies/downloads/#java17?er=221886)

Once downloaded, move the JDK tarball (`jdk-17.0.11_linux-x64_bin.tar.gz`) to the `blobs/java` folder inside your BOSH release repository. Then, add the JDK as a blob:
```bash
bosh add-blob blobs/java/jdk-17.0.11_linux-x64_bin.tar.gz java/jdk-17.0.11_linux-x64_bin.tar.gz
```

Alternatively, you can download the Oracle JDK 17 directly using `wget` with the following command:
```bash
wget -c --header "Cookie: oraclelicense=accept-securebackup-cookie" https://download.oracle.com/java/17/latest/jdk-17_linux-x64_bin.tar.gz
```

Once the JDK tarball (`jdk-17_linux-x64_bin.tar.gz`) is downloaded, move it to the `blobs/java` folder inside your BOSH release repository. Then, add the JDK as a blob using this command:
```bash
bosh add-blob blobs/java/jdk-17_linux-x64_bin.tar.gz java/jdk-17.0.11_linux-x64_bin.tar.gz
```

This command registers the JDK file as a blob for the release, which will be stored in the blobstore when you upload the blobs.


### 4. Create the BOSH Release

Once the blob has been added and the repository is set up, create the BOSH release:
```bosh
bosh create-release --force
```

The `--force` flag is optional and it allows you to create the release even if there are uncommitted changes or if certain checks are not met. This will create a development release in the `dev_releases/spring-boot/` directory.

### 5. Upload the BOSH Release to the Director

Next, upload the newly created release to your BOSH director:
```bash
bosh upload-release
```

### 6. Upload the Stemcell

If you haven't uploaded the required stemcell (Ubuntu Jammy), do so now:
```bash
bosh upload-stemcell <path-to-ubuntu-jammy-stemcell>
```

### 7. Deploy Using the Manifest

Now, deploy your Spring Boot application using your `manifest.yml`:
```bash
bosh -d greeting-app deploy manifest.yml
```

### 8. Access Your Spring Boot Application

Once the deployment is complete, the Spring Boot application will be running on the VM. The application will be listening on port `8081` with the greeting message set to `"Zdravo"`.

You can access the app on the deployed VM's IP and port:
```bash
curl http://<vm-ip>:8081/
```

You can find the VM IP by running:
```bash
bosh -d greeting-app vms
```

### Notes

- You can modify the manifest to change the number of instances, VM type, or any other properties like `port` and `greeting`.
- To update or redeploy the application, modify the manifest as needed and run bosh `deploy again`.







