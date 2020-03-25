# ISC-DEV

[![Gitter](https://img.shields.io/badge/chat-on%20telegram-blue.svg)](https://t.me/joinchat/FoZ4MxGETT0_VsVglVl0DA)
[![Gitter](https://img.shields.io/static/v1?label=Vote%20for%20this%20App&message=InterSystems%20IRIS%20Contest&labelColor=%23333695&color=%2300b2a9)](https://openexchange.intersystems.com/contest/current)

Export/Import source code (classes, macro, routines) and DeepSee artefacts(pivots, dashboards, termlists, pivot variables, shared measures) from and to InterSystems Data Platform products(Caché, Ensemble, IRIS). Support versions from 2016.2

# Installation
Download code and run
```
set dir="/your_download_dir/isc-dev
do $System.OBJ.ImportDir(dir,"*.xml;*.cls;*.mac;*.int;*.inc;*.dfi","cuk",,1)
```
or
import the [release](https://github.com/intersystems-ru/cache-udl/releases) to the namespace.

Map dev package to %All namespace to make it visible in any namespace.

## Docker

To install using Docker. Follow this instructions:

Open terminal and clone the repo into any local directory

```
$ git clone https://github.com/intersystems-community/isc-dev.git
```

Open the terminal in this directory and run:

```
$ docker-compose build
```

Run the IRIS container with your project:
```
$ docker-compose up -d
```

# Usage

## Setup working directory ( optional )
```
NS> w ##class(dev.code).workdir("/path/to/your/working/directory/")
```
## Export to working directory:
```
NS> d ##class(dev.code).export()
```
## Import:
```
NS> d ##class(dev.code).import()
```

## Compile, Release and Patch:

Introduce isc.json file in the source root directory with settings for the code mask, for the name of the project and for get the patch form local git or GitHub. e.g.
```
"git": 0 - files diff from local git (default)
"git": 1 - files diff from GitHub
use below params in case of "git" : 1
"owner":  - name of the github e.g. intersystems-community
"repository": - name of the repo e.g. dc-analytics
 "user": - user and password for private github repo
 "password": 
```


```
isc.json
 "compileList": "Classes*.INC,classes*.CLS,*.DFI",
 "projectName": "myproject",
 "git": 0,
 "owner": "owner",
 "repository": "repository",
 "user": "user",
 "password": "password"
```
Run init method to initialize project settings:
```
NS> d ##class(dev.code).init()
```
Then run release to export all the classes in compileList into one "myproject.xml" release file. It will export it into the default for current Namespace directory.
```
NS> d ##class(dev.code).release()
```
Or compile it whenever you want to compile all the proejct related resources.
```
NS> d ##class(dev.code).compile()
```
Get last changes from github or local git. Run patch to export the classes in compileList into one "patch.xml" patch file. It will export it into the default for current Namespace directory or you can choose where export. By default, makes a patch from the last commit if you do not specify `commitFrom` and `commitTo` e.g.
```
NS> s filename = "c:\patch.xml"
NS> s commitFrom = 1
NS> s commitTo = 5
NS> d ##class(dev.code).patch(filename,commitFrom,commitTo)
```

## Known issues
Be careful with import termlists, pivot variables and shared measures. In current implementation imported artefacts replace those you have in the target namespace. It happens because the utility uses standard global import for globals in XML with $System.OBJ.Import which kills the global first and imports the new one.

#### If after using the export command, git treats unaltered files as modified, the problem may be in the following:
- When moving sources from one OS to another (f.e. from win to mac), the end of the line character was not taken into account. To fix this, you need to specify git to make the end of line character look the same.

  solution: ```git config --global core.autocrlf input```
- File access rights have been changed. In this case, you will need to specify that you do not want to track these changes.

  solution: ```git config core.filemode false```

## Development

To update the module in ZPM do the following:
USER> zpm
zpm: USER>load /opt
zpm: USER>repo -n registry -user USER -pass PASSWORD
1) Filesystem
2) Local Cache
3) Remote Repository

Which sort of repository do you wish to configure? 3
zpm: USER>module-action isc-dev publish




