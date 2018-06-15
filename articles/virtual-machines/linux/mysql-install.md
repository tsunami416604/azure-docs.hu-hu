---
title: Állítsa be a Linux virtuális gép az Azure-ban MySQL |} Microsoft Docs
description: Útmutató a MySQL-készlet telepítése egy Linux virtuális gépen (Ubuntu vagy RedHat termékcsalád operációs rendszer) az Azure-ban
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: d91f8cf8455a60d3e0afb2f209ba07933bcdee1c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30239174"
---
# <a name="how-to-install-mysql-on-azure"></a>A MySQL telepítése Azure-ban
Ön ebből a cikkből megtudhatja, hogyan telepítése és konfigurálása a MySQL Linux operációs rendszert futtató Azure virtuális géphez.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-mysql-on-your-virtual-machine"></a>MySQL telepítése a virtuális gépen
> [!NOTE]
> Már rendelkeznie kell egy Microsoft Azure virtuális gépen futó Linux az oktatóanyag teljesítéséhez. Tekintse meg a [Azure Linux virtuális gép oktatóanyag](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) létrehozásához, és a Linux virtuális gépet `mysqlnode` Virtuálisgép-nevet és `azureuser` felhasználóként a folytatás előtt.
> 
> 

Ebben az esetben használjon 3306 port a MySQL-portjaként működik.  

A putty használatával létrehozott virtuális gép Linux csatlakozni. Ha ez az Azure Linux virtuális gép első használatakor, tekintse meg a putty használatával kapcsolódni a Linux virtuális gép [Itt](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Tárház csomag segítségével MySQL5.6 telepítése ebben a cikkben példaként. MySQL5.6 rendelkezik-e további fokozása mint MySQL5.5 teljesítményét.  További információ [Itt](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).

### <a name="how-to-install-mysql56-on-ubuntu"></a>Ubuntu MySQL5.6 telepítése
Használjuk Linux virtuális gép az Azure-ból Ubuntu itt.

* 1. lépés: Telepítse a MySQL Server 5.6 kapcsolót, hogy `root` felhasználó:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Mysql-kiszolgáló 5.6 telepítése:
  
            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6
  
    A telepítés során megjelenik egy párbeszédpanel ablak poping, kérje meg, meg kell adnia az alábbi MySQL gyökér szintű jelszavát, és meg kell itt beállította az a jelszavát.
  
    ![image](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

    Adjon meg a jelszót újra a megerősítéshez.

    ![image](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

* 2. lépés: Bejelentkezés MySQL-kiszolgáló
  
    MySQL-kiszolgáló telepítésének befejeződése után MySQL-szolgáltatás automatikusan elindul. MySQL kiszolgáló jelentkezhet `root` felhasználó.
    Használja az alábbi parancsot a bejelentkezési és a bemeneti jelszót.
  
             #[root@mysqlnode ~]# mysql -uroot -p
* 3. lépés: A futó MySQL-szolgáltatás kezelése
  
    a MySQL-szolgáltatás állapotának beolvasása
  
             #[root@mysqlnode ~]# service mysql status
  
    (b) a MySQL-szolgáltatás elindítása
  
             #[root@mysqlnode ~]# service mysql start
  
    (c) MySQL-szolgáltatás leállítása
  
             #[root@mysqlnode ~]# service mysql stop
  
    (d) a MySQL-szolgáltatás újraindítása
  
             #[root@mysqlnode ~]# service mysql restart

### <a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>Red Hat operációsrendszer-termékcsalád például a CentOS, Oracle Linux MySQL telepítése
Használjuk Linux virtuális gép CentOS vagy Oracle Linux itt.

* 1. lépés: A MySQL Yum tárház kapcsoló hozzáadása a `root` felhasználó:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Töltse le és telepítse a MySQL-kiadás csomagot:
  
            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm
* 2. lépés: Szerkesztés alatt fájlt a MySQL5.6 csomag MySQL tárháza engedélyezéséhez.
  
            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo
  
    Ez a fájl minden értékének frissítse az alábbi:
  
        \# *Enable to use MySQL 5.6*
  
        [mysql56-community]
        name=MySQL 5.6 Community Server
  
        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
        enabled=1
  
        gpgcheck=1
  
        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
* 3. lépés: A telepítés MySQL MySQL tárházból MySQL telepítése:
  
           #[root@mysqlnode ~]#yum install mysql-community-server
  
    MySQL RPM-csomag és az összes kapcsolódó csomag lesz telepítve.
* 4. lépés: A futó MySQL-szolgáltatás kezelése
  
    (a) a MySQL-kiszolgáló szolgáltatás állapotának ellenőrzése:
  
           #[root@mysqlnode ~]#service mysqld status
  
    (b) ellenőrizze, hogy fut-e az alapértelmezett port a MySQL-kiszolgálóra:
  
           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306

    (c) a MySQL-kiszolgáló indítása:

           #[root@mysqlnode ~]#service mysqld start

    (d) a MySQL-kiszolgáló leállítása:

           #[root@mysqlnode ~]#service mysqld stop

    e set MySQL indítására, amikor a rendszer rendszerindító fel:

           #[root@mysqlnode ~]#chkconfig mysqld on


### <a name="how-to-install-mysql-on-suse-linux"></a>SUSE Linux MySQL telepítése
Használjuk Linux virtuális gép, OpenSUSE itt.

* 1. lépés: Töltse le és telepítse a MySQL-kiszolgáló
  
    Váltás `root` felhasználó keresztül alábbi parancsot:  
  
           #sudo su -
  
    Töltse le és telepítse a MySQL-csomagot:
  
           #[root@mysqlnode ~]# zypper update
  
           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql
* 2. lépés: A futó MySQL-szolgáltatás kezelése
  
    (a) a MySQL-kiszolgáló állapotának ellenőrzése:
  
           #[root@mysqlnode ~]# rcmysql status
  
    (b) ellenőrizze, hogy a MySQL-kiszolgáló az alapértelmezett port:
  
           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306

    (c) a MySQL-kiszolgáló indítása:

           #[root@mysqlnode ~]# rcmysql start

    (d) a MySQL-kiszolgáló leállítása:

           #[root@mysqlnode ~]# rcmysql stop

    e set MySQL indítására, amikor a rendszer rendszerindító fel:

           #[root@mysqlnode ~]# insserv mysql

### <a name="next-step"></a>Következő lépés
További használati és MySQL kapcsolatos információk keresése [Itt](https://www.mysql.com/).

