---
title: Frissítse az Azure Linux-ügynök a Githubról |} A Microsoft Docs
description: Ismerje meg, az Azure-beli Linuxos virtuális gép az Azure Linux-ügynök frissítése
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: roiyz
ms.openlocfilehash: 7f1b84f62bae3640e7ac20c2e4a395033d1d687c
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54319354"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>A virtuális gép az Azure Linux-ügynök frissítése

Frissítése az [Azure Linux-ügynök](https://github.com/Azure/WALinuxAgent) egy Linux rendszerű virtuális gépen az Azure-ban, hogy rendelkezzen:

- Futó Linux rendszerű virtuális gépek az Azure-ban.
- Egy kapcsolat a Linux rendszerű virtuális Gépre SSH-val.

Mindig ellenőrizni kell a csomag a Linux-disztribúció tárházban először. A rendelkezésre álló csomagot nem lehet a legújabb verzió viszont automatikus frissítés engedélyezésével biztosíthatja a Linux-ügynök mindig legújabb frissítésének letöltése lehetőség. Kell-e rendelkeznie alapján a problémát, a disztribúció szállítói támogatás kell kérnie.

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Minimális virtuális gépek ügynök támogatása az Azure-ban
Ellenőrizze a [minimális verzió támogatása az Azure-beli virtuálisgép-ügynökök](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) a folytatás előtt.

## <a name="updating-the-azure-linux-agent"></a>Az Azure Linux-ügynök frissítése

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>Ellenőrizze a jelenlegi csomag verziója

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Frissítési csomag gyorsítótárát

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>A csomag legújabb verziójának telepítése

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Győződjön meg arról, engedélyezve van az automatikus frissítés

Először ellenőrizze, hogy ha engedélyezve van:

```bash
cat /etc/waagent.conf
```

Keresse meg a "AutoUpdate.Enabled". A következő kimenet látható, ha engedélyezve van:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Futtatás engedélyezése:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Indítsa újra a waagent szolgáltatást

#### <a name="restart-agent-for-1404"></a>Indítsa újra 14.04-ügynököt

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Indítsa újra 16.04-ügynököt / 17.04

```bash
systemctl restart walinuxagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-wheezy"></a>Debian 7 "Wheezy"

#### <a name="check-your-current-package-version"></a>Ellenőrizze a jelenlegi csomag verziója

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Frissítési csomag gyorsítótárát

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>A csomag legújabb verziójának telepítése

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Az ügynök az automatikus frissítés engedélyezése
Debian ezen verziója nem rendelkezik egy verzió > = 2.0.16-os, ezért AutoUpdate nem érhető el hozzá. A fenti parancs kimenete bemutatja, ha a csomag naprakész állapotban.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 “Jessie” / Debian 9 “Stretch”

#### <a name="check-your-current-package-version"></a>Ellenőrizze a jelenlegi csomag verziója

```bash
apt list --installed | grep waagent
```

#### <a name="update-package-cache"></a>Frissítési csomag gyorsítótárát

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>A csomag legújabb verziójának telepítése

```bash
sudo apt-get install waagent
```
#### <a name="ensure-auto-update-is-enabled"></a>Győződjön meg arról, engedélyezve van az automatikus frissítés 

Először ellenőrizze, hogy ha engedélyezve van:

```bash
cat /etc/waagent.conf
```

Keresse meg a "AutoUpdate.Enabled". A következő kimenet látható, ha engedélyezve van:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Futtatás engedélyezése:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Indítsa újra a waagent szolgáltatást

```
sudo systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Red Hat / CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>Ellenőrizze a jelenlegi csomag verziója

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Ellenőrizze az elérhető frissítések

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>A csomag legújabb verziójának telepítése

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Győződjön meg arról, engedélyezve van az automatikus frissítés 

Először ellenőrizze, hogy ha engedélyezve van:

```bash
cat /etc/waagent.conf
```

Keresse meg a "AutoUpdate.Enabled". A következő kimenet látható, ha engedélyezve van:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Futtatás engedélyezése:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Indítsa újra a waagent szolgáltatást

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>Ellenőrizze a jelenlegi csomag verziója

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Ellenőrizze az elérhető frissítések

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>A csomag legújabb verziójának telepítése

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Győződjön meg arról, engedélyezve van az automatikus frissítés 

Először ellenőrizze, hogy ha engedélyezve van:

```bash
cat /etc/waagent.conf
```

Keresse meg a "AutoUpdate.Enabled". A következő kimenet látható, ha engedélyezve van:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Futtatás engedélyezése:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Indítsa újra a waagent szolgáltatást

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>Ellenőrizze a jelenlegi csomag verziója

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Ellenőrizze az elérhető frissítések

A fenti kimeneti bemutatja, ha a csomag nincs mindig naprakészek.

#### <a name="install-the-latest-package-version"></a>A csomag legújabb verziójának telepítése

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Győződjön meg arról, engedélyezve van az automatikus frissítés 

Először ellenőrizze, hogy ha engedélyezve van:

```bash
cat /etc/waagent.conf
```

Keresse meg a "AutoUpdate.Enabled". A következő kimenet látható, ha engedélyezve van:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Futtatás engedélyezése:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Indítsa újra a waagent szolgáltatást

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>Ellenőrizze a jelenlegi csomag verziója

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Ellenőrizze az elérhető frissítések

A fenti kimenetében meg fogja jeleníteni, ha a csomag naprakész állapotban.

#### <a name="install-the-latest-package-version"></a>A csomag legújabb verziójának telepítése

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Győződjön meg arról, engedélyezve van az automatikus frissítés 

Először ellenőrizze, hogy ha engedélyezve van:

```bash
cat /etc/waagent.conf
```

Keresse meg a "AutoUpdate.Enabled". A következő kimenet látható, ha engedélyezve van:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Futtatás engedélyezése:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Indítsa újra a waagent szolgáltatást

```bash
sudo systemctl restart waagent.service
```

## <a name="oracle-6-and-7"></a>6. és 7 Oracle

Oracle Linux rendszeren, győződjön meg arról, hogy a `Addons` adattár engedélyezve van. A fájl szerkesztéséhez válassza `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) vagy `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux), és módosítsa a sor `enabled=0` való `enabled=1` alatt **[ol6_addons]** vagy **[ol7_addons]** a jelen a fájl.

Ezt követően az Azure Linux-ügynök legújabb verziójának telepítéséhez írja be:

```bash
sudo yum install WALinuxAgent
```

Ha nem találja a bővítményt tárház adhat hozzá egyszerűen ezeket a sorokat a .repo fájl végén az Oracle Linux kiadásban megfelelően:

A virtuális gépek Oracle Linux 6:

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
enabled=1
```

Írja be:

```bash
sudo yum update WALinuxAgent
```

Általában ez ennyire szüksége, de ha valamilyen okból telepíteni szeretné a https://github.com közvetlenül, kövesse az alábbi lépéseket.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>A Linux-ügynök frissítése, amikor nincs ügynök a csomag terjesztési létezik

Telepítse a wget (vannak bizonyos disztribúciókhoz, amelyek nem telepítik azt, például a Red Hat, CentOS és Oracle Linux 6.4, 6.5-ös és verziók alapértelmezés szerint) írja be `sudo yum install wget` a parancssoron.

### <a name="1-download-the-latest-version"></a>1. Töltse le a legújabb verzióra
Nyissa meg [az Azure Linux-ügynök a GitHub kiadását](https://github.com/Azure/WALinuxAgent/releases) egy weblap, és ismerje meg a legújabb verziószámot. (Írja be aktuális verziójának megkereséséhez `waagent --version`.)

#### <a name="for-version-22x-or-later-type"></a>Verzió 2.2.x vagy újabb, írja be:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

A következő sort 2.2.0-ás verzió használja példaként:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Az Azure Linux-ügynök telepítése

#### <a name="for-version-22x-use"></a>Verzió 2.2.x, használja:
A csomag telepítéséhez szükség lehet `setuptools` először – lásd: [Itt](https://pypi.python.org/pypi/setuptools). Majd futtassa ezt:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Győződjön meg arról, engedélyezve van az automatikus frissítés

Először ellenőrizze, hogy ha engedélyezve van:

```bash
cat /etc/waagent.conf
```

Keresse meg a "AutoUpdate.Enabled". A következő kimenet látható, ha engedélyezve van:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Futtatás engedélyezése:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Indítsa újra a waagent szolgáltatást
A legtöbb Linux-disztribúciók:

```bash
sudo service waagent restart
```

Ubuntu rendszeren használja:

```bash
sudo service walinuxagent restart
```

A CoreOS használhatja:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Erősítse meg az Azure Linux-ügynök verziója
    
```bash
waagent -version
```

A CoreOS a fenti parancs nem működik.

Látni fogja, hogy az Azure Linux-ügynök verziója frissítve lett az új verzióra.

Az Azure Linux-ügynökkel kapcsolatos további információkért lásd: [Azure Linux Agent információs](https://github.com/Azure/WALinuxAgent).
