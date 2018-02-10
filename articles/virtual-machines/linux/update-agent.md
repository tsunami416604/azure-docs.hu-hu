---
title: "A Githubból Azure Linux ügynök frissítése |} Microsoft Docs"
description: "Útmutató: Azure Linux ügynök frissítése a Linux virtuális gép az Azure-ban a legújabb verzióra a Githubról"
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: mingzhan
ms.openlocfilehash: 455de7bc0bca86ad542b6606181b0daf146a5e6a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>A virtuális gép az Azure Linux ügynök frissítése

Frissítése a [Azure Linux ügynök](https://github.com/Azure/WALinuxAgent) a Linux virtuális gép az Azure-ban, már rendelkeznie kell:

- A Linux virtuális gép az Azure-ban.
- A Linux virtuális gép SSH kapcsolatot.

Mindig ellenőrizni kell a csomag a Linux distro tárházban először. A csomag rendelkezésre nem lehet a legújabb verzióra, azonban automatikus frissítés engedélyezésével biztosíthatja, hogy a Linux-ügynök mindig legújabb frissítésének letöltése lehetőség. Kell a csomag kezelők történő telepítés problémák, a distro szállítótól támogatási kell keresni.

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Minimális virtuális gépek ügynök támogatása az Azure-ban
Ellenőrizze a [az Azure virtuális gép ügynökök által támogatott minimális verzió](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) a folytatás előtt.

## <a name="updating-the-azure-linux-agent"></a>Az Azure Linux ügynök frissítése

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>Ellenőrizze a csomag aktuális verziója

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Frissítési csomag gyorsítótár

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Telepítse a legújabb verziója

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Győződjön meg róla, engedélyezve van az automatikus frissítés

Először ellenőrizze, hogy ha engedélyezve van:

```bash
cat /etc/waagent.conf
```

"AutoUpdate.Enabled" található. A kimenet jelenik meg, ha engedélyezve van:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Futtatás engedélyezése:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>A waagent-szolgáltatás újraindítása

#### <a name="restart-agent-for-1404"></a>Indítsa újra az ügynököt a 14.04

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Indítsa újra az ügynököt a 16.04 / 17.04

```bash
systemctl restart walinuxagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-wheezy"></a>Debian 7 "Wheezy"

#### <a name="check-your-current-package-version"></a>Ellenőrizze a csomag aktuális verziója

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Frissítési csomag gyorsítótár

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Telepítse a legújabb verziója

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Ügynök automatikus frissítésének engedélyezése
Debian ezen verziója nem rendelkezik egy verzió > = 2.0.16, ezért nem áll rendelkezésre az automatikus frissítés. A fenti parancs kimenetében láthatja, hogy naprakész állapotban-e a csomag.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie" / "Stretch" Debian 9

#### <a name="check-your-current-package-version"></a>Ellenőrizze a csomag aktuális verziója

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Frissítési csomag gyorsítótár

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Telepítse a legújabb verziója

```bash
sudo apt-get install waagent
```
#### <a name="ensure-auto-update-is-enabled"></a>Győződjön meg róla, engedélyezve van az automatikus frissítés 

Először ellenőrizze, hogy ha engedélyezve van:

```bash
cat /etc/waagent.conf
```

"AutoUpdate.Enabled" található. A kimenet jelenik meg, ha engedélyezve van:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Futtatás engedélyezése:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>A waagent-szolgáltatás újraindítása

```
sudo systemctl restart walinuxagent.service
```

## <a name="redhat--centos"></a>Redhat vagy CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>Ellenőrizze a csomag aktuális verziója

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Elérhető frissítések ellenőrzése

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Telepítse a legújabb verziója

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Győződjön meg róla, engedélyezve van az automatikus frissítés 

Először ellenőrizze, hogy ha engedélyezve van:

```bash
cat /etc/waagent.conf
```

"AutoUpdate.Enabled" található. A kimenet jelenik meg, ha engedélyezve van:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Futtatás engedélyezése:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>A waagent-szolgáltatás újraindítása

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>Ellenőrizze a csomag aktuális verziója

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Elérhető frissítések ellenőrzése

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Telepítse a legújabb verziója

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Győződjön meg róla, engedélyezve van az automatikus frissítés 

Először ellenőrizze, hogy ha engedélyezve van:

```bash
cat /etc/waagent.conf
```

"AutoUpdate.Enabled" található. A kimenet jelenik meg, ha engedélyezve van:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Futtatás engedélyezése:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>A waagent-szolgáltatás újraindítása

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>Ellenőrizze a csomag aktuális verziója

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Elérhető frissítések ellenőrzése

A fenti kimeneti megtudhatja, ha a csomag folyamatosan naprakész adatokat tartalmazzon.

#### <a name="install-the-latest-package-version"></a>Telepítse a legújabb verziója

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Győződjön meg róla, engedélyezve van az automatikus frissítés 

Először ellenőrizze, hogy ha engedélyezve van:

```bash
cat /etc/waagent.conf
```

"AutoUpdate.Enabled" található. A kimenet jelenik meg, ha engedélyezve van:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Futtatás engedélyezése:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>A waagent-szolgáltatás újraindítása

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>Ellenőrizze a csomag aktuális verziója

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Elérhető frissítések ellenőrzése

A kimenet ez megtudhatja, hogy-e a csomag legfeljebb dátum.

#### <a name="install-the-latest-package-version"></a>Telepítse a legújabb verziója

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Győződjön meg róla, engedélyezve van az automatikus frissítés 

Először ellenőrizze, hogy ha engedélyezve van:

```bash
cat /etc/waagent.conf
```

"AutoUpdate.Enabled" található. A kimenet jelenik meg, ha engedélyezve van:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Futtatás engedélyezése:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>A waagent-szolgáltatás újraindítása

```bash
sudo systemctl restart waagent.service
```

## <a name="oracle-6-and-7"></a>Oracle 6 és 7

Oracle Linux, győződjön meg arról, hogy a `Addons` tárház engedélyezve van. Válassza ki a fájl szerkesztése `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) vagy `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux), és módosítsa a sor `enabled=0` való `enabled=1` alatt **[ol6_addons]** vagy **[ol7_addons]** ebben a fájlban.

Ezt követően az Azure Linux ügynök legújabb verziójának telepítéséhez írja be:

```bash
sudo yum install WALinuxAgent
```

Ha nem találja a bővítményt tárház egyszerűen hozzá ezeket a sorokat a .repo fájl végén Oracle Linux kiadási megfelelően:

Oracle Linux 6 virtuális gépek:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Oracle Linux 7 virtuális gépek:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0
```

Írja be:

```bash
sudo yum update WALinuxAgent
```

Ez általában az összes szükséges, de ha valamilyen okból közvetlenül, telepítse azt https://github.com kell kövesse az alábbi lépéseket.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>A Linux-ügynök frissítése, amikor nincs ügynök csomag létezik-e a terjesztési

Írja be a wget (van néhány disztribúciókkal, amely nem az alapértelmezés szerint telepítésre kerülnek, például a CentOS, Oracle Linux és a Redhat 6.4 és 6.5-ös verzió) telepítése `sudo yum install wget` a parancssorban.

### <a name="1-download-the-latest-version"></a>1. A legújabb verzió letöltéséhez
Nyissa meg [Azure Linux ügynök a Githubon történő kiadása](https://github.com/Azure/WALinuxAgent/releases) egy weblapot, és megtudhatja, a legújabb verziószámot. (Beírásával keresheti meg az aktuális verzió `waagent --version`.)

#### <a name="for-version-22x-or-later-type"></a>A verzió 2.2.x vagy újabb, írja be:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip.zip
cd WALinuxAgent-2.2.x
```

A következő sor verzió 2.2.0 használja, mint például:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Az Azure Linux ügynök telepítése

#### <a name="for-version-22x-use"></a>A verzió 2.2.x, használja:
A csomag telepítéséhez szükség lehet `setuptools` először – lásd: [Itt](https://pypi.python.org/pypi/setuptools). Ezután futtassa:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Győződjön meg róla, engedélyezve van az automatikus frissítés

Először ellenőrizze, hogy ha engedélyezve van:

```bash
cat /etc/waagent.conf
```

"AutoUpdate.Enabled" található. A kimenet jelenik meg, ha engedélyezve van:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Futtatás engedélyezése:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. A waagent-szolgáltatás újraindítása
A legtöbb Linux disztribúciókkal:

```bash
sudo service waagent restart
```

Ubuntu használja:

```bash
sudo service walinuxagent restart
```

A CoreOS használjon:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Erősítse meg az Azure Linux ügynök verziója
    
```bash
waagent -version
```

A CoreOS a fenti parancs nem működnek.

Látni fogja, hogy az Azure Linux ügynök verziója frissült az új verzióra.

Az Azure Linux ügynök kapcsolatos további információkért lásd: [Azure Linux ügynök információs](https://github.com/Azure/WALinuxAgent).
