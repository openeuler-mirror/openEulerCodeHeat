# RPM Basics

In last task, we have learned how to compile our own software packages in the Linux environment, so what should we do if we want to install software developed by other developers? Or how to install our own project that is much more complicated than "Hello World!"?

## Build From Source

Linux open source software usually has a variety of release forms, the most basic of which is to release it as Tarball: software developers will package all the source code files of the software in tar and then compress it (usually gzip), so tarball files are generally The extension is *.tar.gz or abbreviated as *tgz. However, recently, because bzip2 and xz have better compression rates, their corresponding suffixes are *.tar.bz2 and *.tar.xz.

Therefore, a Tarball is a software package. After unzipping it, the files inside usually include:

- Source code files
- Detection program (may be configure or config)
- Simple instructions and installation instructions for this software (INSTALL or README)

By reading the INSTALL or README document in the decompressed project directory, you can follow the steps to complete the installation. The typical Tarball installation process is shown below.

First we should install required tools:
```
yum install -y make cmake automake
```

Again, make get our codes:
```
curl -L  https://github.com/openeuler-mirror/openEulerCodeHeat/raw/main/Advanced-Issues/angle-0.1.tar.gz
```

Unzip it and get into the folder:
```
tar -xzvf angle-0.1.tar.gz && cd angle
```
Chekout what is in the folder with `ls -l`

```
ls -l

-rw-r--r-- 1 root root  42K  2 14  2022 aclocal.m4
drwxr-xr-x 2 root root 4.0K  2 14  2022 autom4te.cache
lrwxrwxrwx 1 root root   32  2 14  2022 compile -> /usr/share/automake-1.16/compile
-rw-r--r-- 1 root root  625  2 14  2022 config.h.in
-rw-r--r-- 1 root root 3.3K 11 23 06:12 config.log
-rwxr-xr-x 1 root root 143K  2 14  2022 configure
-rw-r--r-- 1 root root  452  2 14  2022 configure.ac
lrwxrwxrwx 1 root root   32  2 14  2022 depcomp -> /usr/share/automake-1.16/depcomp
lrwxrwxrwx 1 root root   35  2 14  2022 install-sh -> /usr/share/automake-1.16/install-sh
-rw-r--r-- 1 root root  131  2 14  2022 Makefile.am
-rw-r--r-- 1 root root  32K  2 14  2022 Makefile.in
lrwxrwxrwx 1 root root   32  2 14  2022 missing -> /usr/share/automake-1.16/missing
-rw-r--r-- 1 root root  220  2 14  2022 README.md
drwxr-xr-x 2 root root 4.0K 11 23 06:17 src
```

Our codes is in `/src`, but besides that, we have more files, those can be used to build our program very simple:

Run `mkdir build && cd build` to create a clean work directory.

Execute `./configure` to configure the project. This operation will start automatic inspection and configuration of system dependencies, configuration, etc., and generate a Makefile for the current host. 

We have already discussed the content of the Makefile in Introduced in previous chapters. Since the actual environments of software developers and users may be inconsistent (different architectures, different software versions, etc.), it is most reasonable for complex projects to use tools to generate Makefiles based on the actual conditions of the user's environment.

After the configuration, we can run `make` to build the project.

If nothing get's wrong, then we can run `make install` to build and install the program to our binary folder.

Test and run our project

`angle`

## Build RPM package

In the previous section, we learned how to install the Tarball downloaded from the Internet into the machine. Although only a few simple lines of commands are needed to complete the configuration and installation, the installation process includes configuration, compilation, etc. The process is still very time-consuming for complex and large projects. At the same time, not everyone can compile source code, so if it can be compiled on the same hardware and operating system before release, the same operating system release can have exactly the same software version, plus simplicity If there are mechanisms for installation, removal, management, etc., software management will be much easier.

Based on this demand, software management programs were born. Well-known software management programs include RPM, DPKG, etc. The following content will be introduced using RPM as an example.

The full name of RPM is RPM Package Manager (http://rpm.org/), which is a management mechanism that installs the software you need into your Linux system in a database record manner.

The biggest feature: Compile the software you want to install first, and achieve the packaging file of the RPM mechanism. Through the default database records in the packaging file, record the dependency attributes that the software must have when it is installed. When it is installed on you When installing a Linux host, RPM will first query whether the dependency attributes of the Linux host are satisfied according to the data in the software. If so, it will be installed. If not, it will not be installed. Then during installation, the entire software information will be written into the RPM database for future query, verification and uninstallation.

### Advantages:

- No need to recompile: since the mutation has been completed and packaged, the software is very convenient to transfer and install.
- Since the software information has been recorded in the database of the Linux host, it is very convenient to query, upgrade and uninstall.

We have packaged the previous trigonometric function calculation software into an rpm software package named `anglerpm-0.1-1.x86_64.rpm`. The naming can reflect the following information:

- File names usually follow the format: {Name}-{Version}-{Release}.{Arch}.rpm
- anglerpm: The software name is anglerpm (the rpm suffix is distinguished from the manual installation method)
- 0.1-1: The software version is 0.1 and the compiled version is 1 (when the main version of the source code does not change, new patches may be added during the compilation process, the compilation script is modified, etc. and when recompiling is required, bump compiled version)
- x86_64: The platform supported by this rpm package is x86_64. rpm versions of different architectures are usually not universal.
Below we will show how to install this rpm package

### Build our RPM

#### Background Knowledge

RPM software packages are produced using the `rpmbuild` tool. After the tool is initialized, a corresponding working directory will be created in the current user's home directory (taking the root user as an example):

- /root/rpmbuild/SPECS/: Configuration file of the software, such as software information parameters, setting items, etc.
- /root/rpmbuild/SOURCES/: The original file of the software (*.tar.gz file) and the config configuration file
- /root/rpmbuild/BUILD/: During the compilation process, some temporary data will be placed in this directory.
- /root/rpmbuild/RPMS/: After compilation and successful compilation, place the packaged files in this directory, including secondary directories for x86_64, noarch... etc.
- /root/rpmbuild/SRPMS/: Similar to the RPMS directory, it contains SRPM encapsulated files
Before starting the production process, what we need to do is:

Place the source code files in the `/root/rpmbuild/SOURCES/` directory
Write a *.spec file and place it in the `/root/rpmbuild/SPECS/` directory

The SPEC file is a configuration file that guides rpmbuild work. It usually contains the name, version, open source agreement and other related information of the software package, as well as configurations such as scripts for each link of the build:

- Summary: Main description of the software
- Name: The name of the software (which will eventually be one of the name components of the RPM file)
- Version: The version of the software (also one of the components of the RPM file name)
- Release: Description of the number of times this version has been packaged (also one of the components of the RPM file name).
- License: The software licensing model, which seems to cover all well-known Open source licenses
- URL: the main official website of the source code
- SourceN: The source of the software. If the software is downloaded from the Internet, there will usually be this information here to tell everyone the source of the original file. In addition, if there are multiple software sources, they will be processed as Source0, Source1... Source code
- PatchN: As a patch file, there can also be multiple patch files.
- BuildRoot: When compiling, which directory should be used to temporarily store intermediate files (if the target file, link file, etc. of the compilation process). The above information is a must-have project, and the following are additional settings that can be used.
- Requires: If this software requires other software support when running, it must be configured. When you make it into an RPM, the system will automatically check it. This is the source of software dependencies.
- BuildRequires: Software required during compilation. Requires refers to the items that need to be checked during installation because it is related to actual operation, while BuildRequires refers to the software required during compilation, and is only checked when SRPM is compiled into RPM.

Script control for software builds is located after the specified macro definition:

- %description: Give a brief description of your software. This is also necessary. Some basic instructions that appear when using rpm -qi software name include the setting information here.

- Before %prep is set up or installed, what the compiled RPM does for you is the abbreviation of prepare. Its main tasks can include:

  - Carry out software patching and other related work
  - Check whether the directory required by the software already exists

- %build: This section configures make to compile a program called an executable. You will find the program code in this part, which is ./configure, make and other commands.

- %install: After the compilation is completed, it needs to be installed, which is similar to make install in Tarball.

- %files: All files installed by this software need to be written here, including directories for verification. In addition, you can also specify the type of each file, including help files (files following %doc) and configuration files following %config.

- %changelog: Mainly records past update records of the software. The asterisk * should be followed by the time, modifier, email and software version as a description, and the minus sign - should be followed by your detailed description.

### Build our RPM

Install `rpm-build` 
```
yum install -y rpm-build rpmdevtools
```

Run the following command to prepare working directories:

```
rpmdev-setuptree
```

Put our codes to the correct directory:

```
cd ~/rpmbuild/SOURCES && curl -L  https://github.com/openeuler-mirror/openEulerCodeHeat/raw/main/Advanced-Issues/angle-0.1.tar.gz
```

Write the script that will be used to build the RPM, here is one example:

```
cd ~/rpmbuild/SPECS && vi angle.spec
```

```
Name: anglerpm
Version: 0.1
Release: 1%{?dist}
Summary: Get your name and show sin and cos value.

Group: Scientific Support
License: GPLv2
URL: http:/playground.openeuler.org/
Source0: angle-0.1.tar.gz

# BuildRequires:

%description
This package will let you input your name and calculate sin cos value

%prep
%setup -q


%build
./configure
make

%install
mkdir -p %{buildroot}/usr/local/bin
install -m 755 anglerpm %{buildroot}/usr/local/bin

%files
/usr/local/bin/anglerpm

%changelog
* Thu Nov 21 2023 openEuler Playground <playground@openeuler.org> - 0.1
- Build the program & Happy Valentine's Day
```

