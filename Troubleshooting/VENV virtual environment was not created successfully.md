## Issue
  
        python3 -m venv venv
      The virtual environment was not created successfully because ensurepip is not available.  
      On Debian/Ubuntu systems, you need to install the python3-venv package 
      using the following command.

          apt install python3.8-venv

      You may need to use sudo with that command.  After installing the python3-venv
      package, recreate your virtual environment.

      Failing command: ['/home/forest/Desktop/fastapi-docker/venv/bin/python3', '-Im', 'ensurepip', '--upgrade', '--default-pip']

<br>

## Reason

- `ensurepip` is not available.

<br>

## Solution

    $ sudo apt install python3.8-venv
    
        Reading package lists... Done
        Building dependency tree       
        Reading state information... Done
        The following NEW packages will be installed
          python3.8-venv
        0 to upgrade, 1 to newly install, 0 to remove and 0 not to upgrade.
        Need to get 5,444 B of archives.
        After this operation, 27.6 kB of additional disk space will be used.
        Get:1 http://kr.archive.ubuntu.com/ubuntu focal-updates/universe amd64 python3.8-venv amd64 3.8.10-0ubuntu1~20.04.2 [5,444 B]
        Fetched 5,444 B in 1s (6,516 B/s)                      
        Selecting previously unselected package python3.8-venv.
        (Reading database ... 205197 files and directories currently installed.)
        Preparing to unpack .../python3.8-venv_3.8.10-0ubuntu1~20.04.2_amd64.deb ...
        Unpacking python3.8-venv (3.8.10-0ubuntu1~20.04.2) ...
        Setting up python3.8-venv (3.8.10-0ubuntu1~20.04.2) ...

Now see that `venv` is there. 

    $ python3 -m venv venv
    $ ls
        venv

<br>

### References 

- N/A
