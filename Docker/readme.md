# Tech 258 - Docker

## Installing Docker on Windows
### Prerequisites
1) Make sure `Windows Subsystem for Linux` is enabled on your Machine. <br>
To do this:
- Search for `Turn Windows features on or off` on Search bar.
- Scroll down to `Windows Subsystem for Linux` and Tick it if not already ticked. Example: <br>
![](images/enable_windows_subsystem_for_linux.png)
2) Click `OK` and Restart your machine to enable it!
### Steps to Install Docker
1) Download the installer using the download button at the top of this page [here](https://docs.docker.com/desktop/install/windows-install/).
2) Double-click `Docker Desktop Installer.exe` to run the installer.
3) When prompted, use `WSL 2` instead of `Hyper-V` option on the configuration page. (Make sure it's ticked)
4) Follow the instructions on the installation wizard to proceed with the install.
5) When the installation is successful, select `Close` to complete the installation process.
6) Restart your machine to make sure Docker is active and running from now on.
7) Check the docker version using the command:
```
docker --version
```
Example output: <br>
![docker_version.png]](images/docker_version.png)
