---
title: Állítsa be a MySQL Azure-beli Linuxos virtuális gépre |} A Microsoft Docs
description: Ismerje meg, a MySQL-verem telepítése Linux rendszerű virtuális gép (Ubuntu vagy Red Hat termékcsalád operációs rendszer) az Azure-ban
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 153bae7c-897b-46b3-bd86-192a6efb94fa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: 21ad3f9baf4b8e117f881d9a36fc606af04e17a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66158441"
---
# <a name="how-to-install-mysql-on-azure"></a>A MySQL telepítése Azure-ban
Ebből a cikkből megismerheti, hogyan telepítése és konfigurálása a MySQL a Linux operációs rendszert futtató Azure virtuális gép lesz.


> [!NOTE]
> Ez az oktatóanyag elvégzéséhez a Linux operációs rendszert futtató Microsoft Azure virtuális gép már rendelkeznie kell. Tekintse át a [Azure Linux VM-oktatóanyag](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) hozhat létre, és állítsa be a Linux virtuális gép `mysqlnode` Virtuálisgép-nevet és `azureuser` felhasználóként a folytatás előtt.
> 
> 

Ebben az esetben használja a 3306-os portot a MySQL-portjaként működik.  

Tárház csomag használjuk példaként ebben a cikkben MySQL5.6 telepítéséhez. Ténylegesen MySQL5.6 MySQL5.5 többre van további javítása.  További információ [Itt](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).

## <a name="install-mysql56-on-ubuntu"></a>Ubuntu rendszeren MySQL5.6 telepítése
Egy Ubuntu rendszert futtató, Linux rendszerű virtuális gép használjuk.


### <a name="install-mysql"></a>A MySQL telepítése

Telepítse a MySQL-kiszolgáló 5.6 átvált a a `root` felhasználói:

```bash  
sudo su -
```

5\.6-os mysql-kiszolgáló telepítése:

```bash  
apt-get update
apt-get -y install mysql-server-5.6
```

  
A telepítés során megjelenik egy párbeszédablak jelenik meg, kérje meg az alábbi MySQL gyökér szintű jelszó beállítását, és a jelszó itt kell beállítani.
  
![image](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

Adjon meg a jelszót újra a megerősítéshez.

![image](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

### <a name="sign-in"></a>Bejelentkezés
  
Ha a MySQL-kiszolgáló telepítése befejeződött, MySQL-szolgáltatás automatikusan elindul. MySQL-kiszolgálóhoz való bejelentkezhet a `root` felhasználó, és adja meg a jelszót.

```bash  
mysql -uroot -p
```


### <a name="manage-the-mysql-service"></a>A MySQL-szolgáltatás kezelése

A MySQL-szolgáltatás állapotának beolvasása

```bash   
service mysql status
```
  
Start MySQL Service

```bash  
service mysql start
```
  
MySQL-szolgáltatás leállítása

```bash  
service mysql stop
```
  
A MySQL-szolgáltatás újraindítása

```bash  
service mysql restart
```

## <a name="install-mysql-on-red-hat-os-centos-oracle-linux"></a>A MySQL telepítése operációs rendszer Red Hat, CentOS, Oracle Linux
Linux rendszerű virtuális gépek itt lesz használt a CentOS vagy Oracle Linux.

### <a name="add-the-mysql-yum-repository"></a>A MySQL yum adattár hozzáadása
    
Váltson `root` felhasználói:

```bash  
sudo su -
```

Töltse le és telepítse a MySQL-kiadási csomagot:

```bash  
wget https://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
yum localinstall -y mysql-community-release-el6-5.noarch.rpm
```

### <a name="enable-the-mysql-repository"></a>A MySQL-tárház engedélyezése
Szerkessze a fájlban az engedélyezéséhez a MySQL-tárházat a MySQL5.6 csomag letöltésére vonatkozó alábbi.

```bash  
vim /etc/yum.repos.d/mysql-community.repo
```

  
Módosítsa az értékeket, a fájl az alábbi:

```  
\# *Enable to use MySQL 5.6*
  
[mysql56-community]
name=MySQL 5.6 Community Server
  
baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
enabled=1
  
gpgcheck=1
  
gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```

### <a name="install-mysql"></a>A MySQL telepítése 

A MySQL telepítése a tárházból.

```bash  
yum install mysql-community-server
```
  
A MySQL RPM-csomagot és minden kapcsolódó csomagok fognak települni.


## <a name="manage-the-mysql-service"></a>A MySQL-szolgáltatás kezelése
  
A MySQL-kiszolgáló szolgáltatás állapotának ellenőrzése:

```bash  
service mysqld status\
```
  
Ellenőrizze, hogy fut-e az alapértelmezett port a MySQL-kiszolgáló:

```bash  
netstat  –tunlp|grep 3306
```

Indítsa el a MySQL-kiszolgáló:

```bash
service mysqld start
```

Állítsa le a MySQL-kiszolgáló:

```bash
service mysqld stop
```

Állítsa be a MySQL, indítható el, ha a rendszer rendszerindítás:

```bash
chkconfig mysqld on
```

## <a name="install-mysql-on-suse-linux"></a>A MySQL telepítése SUSE Linux rendszeren

Használjuk Linux rendszerű virtuális gép az OpenSUSE itt.

### <a name="download-and-install-mysql-server"></a>Töltse le és telepítse a MySQL-kiszolgáló
  
Váltson `root` felhasználó keresztül alábbi parancsot:  

```bash  
sudo su -
```
  
Töltse le és telepítse a MySQL-csomag:

```bash  
zypper update
zypper install mysql-server mysql-devel mysql
```

### <a name="manage-the-mysql-service"></a>A MySQL-szolgáltatás kezelése
  
A MySQL-kiszolgáló állapotának ellenőrzéséhez:

```bash  
rcmysql status
```
  
Ellenőrizze, hogy a MySQL-kiszolgáló az alapértelmezett port:

```bash  
netstat  –tunlp|grep 3306
```

Indítsa el a MySQL-kiszolgáló:

```bash
rcmysql start
```

Állítsa le a MySQL-kiszolgáló:

```bash
rcmysql stop
```

Állítsa be a MySQL, indítható el, ha a rendszer rendszerindítás:

```bash
insserv mysql
```

## <a name="next-step"></a>Következő lépés
További információkért lásd: a [MySQL](https://www.mysql.com/) webhelyén.

