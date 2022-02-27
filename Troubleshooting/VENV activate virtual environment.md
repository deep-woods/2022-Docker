## Issue

Activate virtual environment. 

<br>

## Structure

~/Desktop/fastapi-docker$ tree  
`.`  
└── venv  
ㅤㅤㅤ├── bin  
ㅤㅤㅤ│ㅤㅤ├── `activate`ㅤㅤ👈Activate `venv` using this.    
ㅤㅤㅤ│ㅤㅤ├── activate.csh  
ㅤㅤㅤ│ㅤㅤ├── activate.fish  
ㅤㅤㅤ│ㅤㅤ├── Activate.ps1  
ㅤㅤㅤ│ㅤㅤ├── easy_install  
ㅤㅤㅤ│ㅤㅤ├── easy_install-3.8  
ㅤㅤㅤ│ㅤㅤ├── pip  
ㅤㅤㅤ│ㅤㅤ├── pip3  
ㅤㅤㅤ│ㅤㅤ├── pip3.8  
ㅤㅤㅤ│ㅤㅤ├── python -> python3  
ㅤㅤㅤ│ㅤㅤ└── python3 -> /usr/bin/python3  
ㅤㅤㅤ├── include  
ㅤㅤㅤ├── lib  
ㅤㅤㅤ│ㅤㅤ└── python3.8  
ㅤㅤㅤ│ㅤㅤㅤㅤㅤ└── site-packages  
ㅤㅤㅤ...

    ~/Desktop/fastapi-docker$ cd venv; ls
  
        bin  include  lib  lib64  pyvenv.cfg  share

    $ cd bin;ls

        activate      activate.fish  easy_install      pip   pip3.8  python3
        activate.csh  Activate.ps1   easy_install-3.8  pip3  python

<br>

## Solution

    $ python3 -m venv venv     <---------- Create a venv.
    $ . venv/bin/activate      <---------- Activate it.
    
    (venv) forest:~/Desktop/fastapi-docker$     <---- Now inside the activated venv

Now you can set up your environment by installding dependencies. For instance: 

    $ pip install fastapi 
    $ pip install uvicorn      <---------- Web server. 

<br>

### References 

- N/A
