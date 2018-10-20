---
title: Állítsa be a MySQL Azure-beli Linuxos virtuális gépre |} A Microsoft Docs
description: Ismerje meg, a MySQL-verem telepítése Linux rendszerű virtuális gép (Ubuntu vagy Red Hat termékcsalád operációs rendszer) az Azure-ban
services: virtual-machines-linux
documentationcenter: ''
author: zr-msft
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
ms.author: zarhoads
ms.openlocfilehash: f7120decd4a5d43f88b55e7d7e20992af34cadc4
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469571"
---
# <a name="how-to-install-mysql-on-azure"></a>A MySQL telepítése Azure-ban
Ebből a cikkből megismerheti, hogyan telepítése és konfigurálása a MySQL a Linux operációs rendszert futtató Azure virtuális gép lesz.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-mysql-on-your-virtual-machine"></a>A MySQL telepítése a virtuális gépen
> [!NOTE]
> Ez az oktatóanyag elvégzéséhez a Linux operációs rendszert futtató Microsoft Azure virtuális gép már rendelkeznie kell. Tekintse át a [Azure Linux VM-oktatóanyag](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) hozhat létre, és állítsa be a Linux virtuális gép `mysqlnode` Virtuálisgép-nevet és `azureuser` felhasználóként a folytatás előtt.
> 
> 

Ebben az esetben használja a 3306-os portot a MySQL-portjaként működik.  

Csatlakozzon a putty használatával létrehozott virtuális gép Linux. Ha ez az Azure Linux rendszerű virtuális gép első használatakor, tekintse meg a putty használata Linux rendszerű virtuális gép csatlakozni [Itt](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Tárház csomag használjuk példaként ebben a cikkben MySQL5.6 telepítéséhez. Ténylegesen MySQL5.6 MySQL5.5 többre van további javítása.  További információ [Itt](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).

### <a name="how-to-install-mysql56-on-ubuntu"></a>Ubuntu rendszeren MySQL5.6 telepítése
Linux rendszerű virtuális gépek itt lesz használt az Ubuntut, az Azure-ból.

* 1. lépés: Telepítés MySQL 5.6-os kiszolgáló Váltás `root` felhasználói:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    5.6-os mysql-kiszolgáló telepítése:
  
            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6
  
    A telepítés során megjelenik egy párbeszédpanel ablak poping akár kérje meg az alábbi MySQL gyökér szintű jelszó beállítását, és akkor van szükség a jelszó beállítása itt.
  
    ![image](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

    Adjon meg a jelszót újra a megerősítéshez.

    ![image](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

* 2. lépés: Bejelentkezés MySQL-kiszolgáló
  
    Ha a MySQL-kiszolgáló telepítése befejeződött, MySQL-szolgáltatás automatikusan elindul. MySQL-kiszolgálóhoz bejelentkezhet `root` felhasználói.
    Használja az alábbi parancsot a bejelentkezési és a bemeneti jelszót.
  
             #[root@mysqlnode ~]# mysql -uroot -p
* 3. lépés: A futó MySQL szolgáltatás kezelése
  
    (a) a MySQL-szolgáltatás állapotának beolvasása
  
             #[root@mysqlnode ~]# service mysql status
  
    (b) a MySQL-szolgáltatás indítása
  
             #[root@mysqlnode ~]# service mysql start
  
    (c) állítsa le a MySQL-szolgáltatás
  
             #[root@mysqlnode ~]# service mysql stop
  
    (d) a MySQL-szolgáltatás újraindítása
  
             #[root@mysqlnode ~]# service mysql restart

### <a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>MySQL telepítése Red Hat operációsrendszer-termékcsalád például a CentOS, Oracle Linux rendszeren
Linux rendszerű virtuális gépek itt lesz használt a CentOS vagy Oracle Linux.

* 1. lépés: A MySQL Yum adattár kapcsoló hozzáadása a `root` felhasználói:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Töltse le és telepítse a MySQL-kiadási csomagot:
  
            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm
* 2. lépés: Szerkessze a fájlt engedélyezéséhez a MySQL-tárházat a MySQL5.6 csomag letöltésére vonatkozó alábbi.
  
            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo
  
    Módosítsa az értékeket, a fájl az alábbi:
  
        \# *Enable to use MySQL 5.6*
  
        [mysql56-community]
        name=MySQL 5.6 Community Server
  
        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
        enabled=1
  
        gpgcheck=1
  
        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
* 3. lépés: Telepítés MySQL MySQL adattárból MySQL telepítése:
  
           #[root@mysqlnode ~]#yum install mysql-community-server
  
    MySQL RPM-csomagot és minden kapcsolódó csomagok fognak települni.
* 4. lépés: A futó MySQL szolgáltatás kezelése
  
    (a) a MySQL-kiszolgáló szolgáltatás állapotának ellenőrzése:
  
           #[root@mysqlnode ~]#service mysqld status
  
    (b) ellenőrizze, hogy fut-e az alapértelmezett port a MySQL-kiszolgáló:
  
           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306

    (c) indítsa el a MySQL-kiszolgáló:

           #[root@mysqlnode ~]#service mysqld start

    (d) állítsa le a MySQL-kiszolgáló:

           #[root@mysqlnode ~]#service mysqld stop

    (e) set MySQL indítására, amikor a rendszer rendszerindítás:

           #[root@mysqlnode ~]#chkconfig mysqld on


### <a name="how-to-install-mysql-on-suse-linux"></a>SUSE Linux rendszeren a MySQL telepítése
Használjuk Linux rendszerű virtuális gép az OpenSUSE itt.

* 1. lépés: Töltse le és telepítse a MySQL-kiszolgáló
  
    Váltson `root` felhasználó keresztül alábbi parancsot:  
  
           #sudo su -
  
    Töltse le és telepítse a MySQL-csomag:
  
           #[root@mysqlnode ~]# zypper update
  
           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql
* 2. lépés: A futó MySQL szolgáltatás kezelése
  
    (a) a MySQL-kiszolgáló állapotának ellenőrzéséhez:
  
           #[root@mysqlnode ~]# rcmysql status
  
    (b) ellenőrizze-e a MySQL-kiszolgáló az alapértelmezett port:
  
           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306

    (c) indítsa el a MySQL-kiszolgáló:

           #[root@mysqlnode ~]# rcmysql start

    (d) állítsa le a MySQL-kiszolgáló:

           #[root@mysqlnode ~]# rcmysql stop

    (e) set MySQL indítására, amikor a rendszer rendszerindítás:

           #[root@mysqlnode ~]# insserv mysql

### <a name="next-step"></a>Következő lépés
Keresse meg a további használati adatainak kapcsolatos MySQL [Itt](https://www.mysql.com/).

