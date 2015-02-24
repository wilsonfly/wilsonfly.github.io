



3. 小系统仓库：
manifests:
ssh://git@svn.xx3798M-spc030-emmc-kk-manifest.git
apk：
ssh://git@svn.xx/hisi-base-4.4-apk
iptvmw：
ssh://git@svn.xx3798M-spc030-emmc-kk-iptvmw
platform/build:
ssh://git@svn.xx3798M-spc030-emmc-kk-build
ssh://git@svn.xx/build/platform-build
platform/on-project:
ssh://git@svn.xx3798M-spc030-emmc-kk-project
platform/release:
ssh://git@svn.xx3798M-spc030-emmc-kk-release
下载地址：
repo init -u ssh://git@svn.xx3798M-spc030-emmc-kk-manifest.git  -b 8M330-EMMC-BASE

2. 有关user 版本的CI  
项目名称：ANDROID-Hi3798M-SPC033-EMMC-kk-4.4-USER  
版本文件：build_id.mk  
编译脚本：build-hi3798m-spc033-emmc-kk-user.sh  
代码地址：repo init -u ssh://gerrit.xx/hisi3798-kk-20140818/manifest.git -b develop  
user版本存储：ssh://git@svn.xx/hisi-3798M-spc033-emmc-kk-release-user  
fanjiwen@sunniwell.net;sunhuasheng@sunniwell.net;liuwenyao@sunniwell.net;  


1. 有关CI  
项目名称：ANDROID-Hi3798M-SPC030-EMMC-kk-4.4  
版本文件：build_id.mk  
编译脚本：build-hi3798m-spc030-emmc-kk.sh  
代码地址：repo init -u ssh://gerrit.xx/hisi3798-kk-20140718/manifest.git -b develop  
版本存储：ssh://git@svn.xx/hisi-3798M-spc030-emmc-kk-release  
编译通知：  
fanjiwen@sunniwell.net;wangdongliang@sunniwell.net;sunhuasheng@sunniwell.net;jiangkun@sunniwell.net;liuwenyao@sunniwell.net;  
