# the problem as observed

Running the script that starts dev_appserver from the project directory crashes when the first request is made and the stack is
```bash
Traceback (most recent call last):
  File "C:\Program Files (x86)\Google\Cloud SDK\google-cloud-sdk\platform\google_appengine\python27\sdk\google\appengine\runtime\wsgi.py", line 238, in Handle
    handler = _config_handle.add_wsgi_middleware(self._LoadHandler())
  File "C:\Program Files (x86)\Google\Cloud SDK\google-cloud-sdk\platform\google_appengine\python27\sdk\google\appengine\runtime\wsgi.py", line 309, in _LoadHandler
    handler, path, err = LoadObject(self._handler)
  File "C:\Program Files (x86)\Google\Cloud SDK\google-cloud-sdk\platform\google_appengine\python27\sdk\google\appengine\runtime\wsgi.py", line 83, in LoadObject
    obj = __import__(path[0])
  File "c:\Data\MYPROJECTNAME\gae\main.py", line 46, in <module>
    from api import routes as api_routes
  File "c:\Data\MYPROJECTNAME\gae\api\routes.py", line 18, in <module>
    import recipe_export
INFO     2023-05-01 11:26:57,844 module.py:862] default: "GET / HTTP/1.1" 500 -
  File "c:\Data\MYPROJECTNAME\gae\api\MODULENAME\__init__.py", line 8, in <module>
    from pdf import  RecipeExportPdfHandler
  File "c:\Data\MYPROJECTNAME\gae\api\MODULENAME\pdf.py", line 7, in <module>
    from lib.pdf.render import Render
  File "c:\Data\MYPROJECTNAME\gae\lib\pdf\render.py", line 4, in <module>
    import PIL  # ------------------
```

# my understanding of the immediate couse

Despite what your readme says, and what I have observed running a similar scirpt on Ubuntu 22.04 environment,
dev_appserver had not copied the project over and ran pip install requirements.txt. So sure no relevant packages were installed,
hence the crash.

# what makes me worry

in the crash stack I see my code being run by python27 still. My code is python3, so not sure why.

# my gcloud nvironment
```bash
Google Cloud SDK [428.0.0]

Platform: [Windows, x86_64] uname_result(system='Windows', node='VM23', release='10', version='10.0.19045', machine='AMD64')
Locale: ('en_US', 'cp1252')
Python Version: [3.10.9 (tags/v3.10.9:1dd9be6, Dec  6 2022, 20:01:21) [MSC v.1934 64 bit (AMD64)]]
Python Location: [c:\Program Files\Python310\python.exe]
OpenSSL: [OpenSSL 1.1.1q  5 Jul 2022]
Requests Version: [2.25.1]
urllib3 Version: [1.26.9]
Default CA certs file: [C:\Program Files (x86)\Google\Cloud SDK\google-cloud-sdk\lib\third_party\certifi\cacert.pem]
Site Packages: [Disabled]

Installation Root: [C:\Program Files (x86)\Google\Cloud SDK\google-cloud-sdk]
Installed Components:
  app-engine-python-extras: [1.9.96]
  app-engine-python: [1.9.103]
  beta: [2023.04.25]
  bq: [2.0.91]
  cloud-datastore-emulator: [2.3.0]
  core: [2023.04.25]
  gcloud-crc32c: [1.0.0]
  gsutil: [5.23]
System PATH: [C:\Program Files (x86)\Google\Cloud SDK\google-cloud-sdk\bin\..\bin\sdk;c:\bin;C:\Program Files\Python310\Scripts\;C:\Program Files\Python310\;C:\Program Files\Microsoft\jdk-17.0.5.8-hotspot\bin;C:\WINDOWS\system32;C:\WINDOWS;C:\WINDOWS\System32\Wbem;C:\WINDOWS\System32\WindowsPowerShell\v1.0\;C:\WINDOWS\System32\OpenSSH\;C:\Program Files\Git\cmd;C:\Program Files\Git\mingw64\bin;C:\Program Files\Git\usr\bin;C:\ProgramData\chocolatey\bin;C:\Program Files\Graphviz\bin;c:\Program Files\hugo;;C:\Program Files (x86)\Google\Cloud SDK\google-cloud-sdk\bin;C:\Program Files\nodejs\;C:\Users\MYUSERNAME\AppData\Local\Microsoft\WindowsApps;C:\Program Files\JetBrains\PyCharm 2022.2.3\bin;C:\Program Files\Fiddler;C:\Program Files\JetBrains\PyCharm 2022.3.2\bin;C:\Users\MYUSERNAME\AppData\Roaming\npm]
Python PATH: [C:\Program Files (x86)\Google\Cloud SDK\google-cloud-sdk\lib\third_party;C:\Program Files (x86)\Google\Cloud SDK\google-cloud-sdk\lib;c:\Program Files\Python310\python310.zip;c:\Program Files\Python310\DLLs;c:\Program Files\Python310\lib;c:\Program Files\Python310]
Cloud SDK on PATH: [True]
Kubectl on PATH: [False]

Installation Properties: [C:\Program Files (x86)\Google\Cloud SDK\google-cloud-sdk\properties]
User Config Directory: [C:\Users\MYUSERNAME\AppData\Roaming\gcloud]
Active Configuration Name: [MYPROJECTNAME]
Active Configuration Path: [C:\Users\MYUSERNAME\AppData\Roaming\gcloud\configurations\config_MYPROJECTNAME]

Account: [MYEMAIL]
Project: [MYPROJECTNAME]

Current Properties:
  [accessibility]
    screen_reader: [False] (property file)
  [app]
    promote_by_default: [false] (property file)
  [core]
    account: [MYEMAIL] (property file)
    disable_usage_reporting: [True] (property file)
    project: [MYPROJECTNAME] (property file)

Logs Directory: [C:\Users\MYUSERNAME\AppData\Roaming\gcloud\logs]
Last Log File: [C:\Users\MYUSERNAME\AppData\Roaming\gcloud\logs\2023.05.01\11.33.03.080706.log]

git: [git version 2.38.1.windows.1]
ssh: [OpenSSH_for_Windows_8.1p1, LibreSSL 3.0.2
]
```


# script to run
```bash
set PROJECT_DIR="c:\/Data\/MYPROJECTNAME\/gae"

set PY2=C:\/Program Files (x86)\/Google\/Cloud SDK\/google-cloud-sdk\/platform\/bundledpython2\/python.exe
set PY3=C:\/Program Files\/Python310\/python.exe
set CLOUD_SDK_ROOT=C:\/Program Files (x86)\/Google\/Cloud SDK\/google-cloud-sdk
set APPSERVER_PY2="%CLOUD_SDK_ROOT%\/platform\/google_appengine\/dev_appserver.py"
set APPSERVER_PY3="%CLOUD_SDK_ROOT%\/bin\/dev_appserver.py"
set GDS="%CLOUD_SDK_ROOT%\/platform\/cloud-datastore-emulator\/cloud_datastore_emulator.cmd"

pushd %PROJECT_DIR%

"%PY3%" %APPSERVER_PY3% ^
--skip_sdk_update_check=True ^
--port=8080 ^
--host=0.0.0.0 ^
--enable_host_checking=false ^
--log_level=debug ^
--runtime_python_path="python3=%PY3%" ^
app.yaml

popd
```
