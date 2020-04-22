---
title: Az Azure Linux-ügynök frissítése a GitHubról
description: Ismerje meg, hogyan frissítheti az Azure Linux-ügynökét linuxos virtuális gépéhez az Azure-ban
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: e3b2819b33feba52c3b02f0e2104d4106bd04cbb
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770064"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Az Azure Linux-ügynök frissítése virtuális gépen

Az [Azure Linux-ügynök](https://github.com/Azure/WALinuxAgent) frissítéséhez linuxos virtuális gépen az Azure-ban, már rendelkeznie kell:

- Egy linuxos virtuális gép az Azure-ban.
- A kapcsolat, hogy a Linux virtuális gép ssh használatával.

Először mindig ellenőrizze, hogy van-e csomag a Linux disztribúciós tárházban. Lehetséges, hogy a rendelkezésre álló csomag nem a legújabb verzió, azonban az automatikus frissítés engedélyezése biztosítja, hogy a Linux Ügynök mindig megkapja a legújabb frissítést. Ha problémái vannak a csomagkezelők telepítésével, kérjen támogatást a distro szállítójától.

> ! - Nem, nem, nem, nem, nem, További információkért [lásd: Jóváhagyott Linux disztribúciók az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>A virtuálisgép-ügynök minimális támogatása az Azure-ban
A folytatás előtt ellenőrizze a [virtuálisgép-ügynökök minimális verziótámogatását az Azure-ban.](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

## <a name="updating-the-azure-linux-agent"></a>Az Azure Linux-ügynök frissítése

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>A csomag jelenlegi verziójának ellenőrzése

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Csomaggyorsítótár frissítése

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>A legújabb csomagverzió telepítése

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Az automatikus frissítés engedélyezésének ellenőrzése

Először ellenőrizze, hogy engedélyezve van-e:

```bash
cat /etc/waagent.conf
```

Keresse meg az "AutoUpdate.Enabled" Ha ezt a kimenetet látja, akkor engedélyezve van:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

A futtatás engedélyezése:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Indítsa újra a waagent szolgáltatást

#### <a name="restart-agent-for-1404"></a>A 14.04-es eszköz újraindítása

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Újrakezdés ügynök 16.04 / 17.04

```bash
systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Piros sapka / CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS, 6.

#### <a name="check-your-current-package-version"></a>A csomag jelenlegi verziójának ellenőrzése

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Az elérhető frissítések ellenőrzése

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>A legújabb csomagverzió telepítése

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Az automatikus frissítés engedélyezésének ellenőrzése 

Először ellenőrizze, hogy engedélyezve van-e:

```bash
cat /etc/waagent.conf
```

Keresse meg az "AutoUpdate.Enabled" Ha ezt a kimenetet látja, akkor engedélyezve van:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

A futtatás engedélyezése:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Indítsa újra a waagent szolgáltatást

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS, 7.

#### <a name="check-your-current-package-version"></a>A csomag jelenlegi verziójának ellenőrzése

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Az elérhető frissítések ellenőrzése

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>A legújabb csomagverzió telepítése

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Az automatikus frissítés engedélyezésének ellenőrzése 

Először ellenőrizze, hogy engedélyezve van-e:

```bash
cat /etc/waagent.conf
```

Keresse meg az "AutoUpdate.Enabled" Ha ezt a kimenetet látja, akkor engedélyezve van:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

A futtatás engedélyezése:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Indítsa újra a waagent szolgáltatást

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>A csomag jelenlegi verziójának ellenőrzése

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Az elérhető frissítések ellenőrzése

A fenti kimenet megmutatja, hogy a csomag naprakész-e.

#### <a name="install-the-latest-package-version"></a>A legújabb csomagverzió telepítése

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Az automatikus frissítés engedélyezésének ellenőrzése 

Először ellenőrizze, hogy engedélyezve van-e:

```bash
cat /etc/waagent.conf
```

Keresse meg az "AutoUpdate.Enabled" Ha ezt a kimenetet látja, akkor engedélyezve van:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

A futtatás engedélyezése:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Indítsa újra a waagent szolgáltatást

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>A csomag jelenlegi verziójának ellenőrzése

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Az elérhető frissítések ellenőrzése

A fenti kimenetben ez megmutatja, hogy a csomag naprakész-e.

#### <a name="install-the-latest-package-version"></a>A legújabb csomagverzió telepítése

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Az automatikus frissítés engedélyezésének ellenőrzése 

Először ellenőrizze, hogy engedélyezve van-e:

```bash
cat /etc/waagent.conf
```

Keresse meg az "AutoUpdate.Enabled" Ha ezt a kimenetet látja, akkor engedélyezve van:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

A futtatás engedélyezése:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Indítsa újra a waagent szolgáltatást

```bash
sudo systemctl restart waagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-jesse-debian-7-stretch"></a>Debian 7 "Jesse" / Debian 7 "Stretch"

#### <a name="check-your-current-package-version"></a>A csomag jelenlegi verziójának ellenőrzése

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Csomaggyorsítótár frissítése

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>A legújabb csomagverzió telepítése

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Ügynök automatikus frissítésének engedélyezése
A Debian ezen verziójában nincs >= 2.0.16 verzió, ezért az automatikus frissítés nem érhető el hozzá. A fenti parancs kimenete megmutatja, hogy a csomag naprakész-e.



### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie" / Debian 9 "Stretch"

#### <a name="check-your-current-package-version"></a>A csomag jelenlegi verziójának ellenőrzése

```bash
apt list --installed | grep waagent
```

#### <a name="update-package-cache"></a>Csomaggyorsítótár frissítése

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>A legújabb csomagverzió telepítése

```bash
sudo apt-get install waagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Az automatikus frissítés engedélyezésének ellenőrzése
Először ellenőrizze, hogy engedélyezve van-e:

```bash
cat /etc/waagent.conf
```

Keresse meg az "AutoUpdate.Enabled" Ha ezt a kimenetet látja, akkor engedélyezve van:

```bash
AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

A futtatás engedélyezése:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
Restart the waagent service
sudo systemctl restart walinuxagent.service
```

## <a name="oracle-linux-6-and-oracle-linux-7"></a>Oracle Linux 6 és Oracle Linux 7

Az Oracle Linux esetében `Addons` győződjön meg arról, hogy a tárház engedélyezve van. Válassza ki a `/etc/yum.repos.d/public-yum-ol6.repo`fájl (Oracle Linux `/etc/yum.repos.d/public-yum-ol7.repo`6) vagy (Oracle `enabled=0` `enabled=1` Linux) szerkesztését, és módosítsa a sort **a fájlban a [ol6_addons]** vagy **a [ol7_addons]** alatt.

Ezután az Azure Linux-ügynök legújabb verziójának telepítéséhez írja be a következőt:

```bash
sudo yum install WALinuxAgent
```

Ha nem találja a kiegészítő tárházat, egyszerűen hozzáadhatja ezeket a sorokat a .repo fájl végén az Oracle Linux kiadásának megfelelően:

Oracle Linux 6 virtuális gépek esetén:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=https://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=https://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Oracle Linux 7 virtuális gépek esetén:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1
```

Ezután írja be a következőt:

```bash
sudo yum update WALinuxAgent
```

Általában ez minden amire szüksége van, de ha valamilyen https://github.com okból közvetlenül kell telepítenie, kövesse a következő lépéseket.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>A Linux-ügynök frissítése, ha nincs ügynökcsomag a terjesztéshez

Telepítse a wget-t (vannak olyan disztribúciók, amelyek alapértelmezés szerint nem telepítik, mint például a Red `sudo yum install wget` Hat, a CentOS és az Oracle Linux 6.4 és 6.5 verziói) a parancssorba való beírással.

### <a name="1-download-the-latest-version"></a>1. Töltse le a legújabb verziót
Nyissa meg [az Azure Linux-ügynök kiadását a GitHubon](https://github.com/Azure/WALinuxAgent/releases) egy weboldalon, és ismerje meg a legújabb verziószámot. (Az aktuális verziót a `waagent --version`beírással keresheti meg.)

#### <a name="for-version-22x-or-later-type"></a>A 2.2.x-es vagy újabb verzióhoz írja be a következőt:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

A következő sor a 2.2.0-s verziót használja példaként:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Az Azure Linux-ügynök telepítése

#### <a name="for-version-22x-use"></a>A 2.2.x verzióhoz használja a következőket:
Lehet, hogy először `setuptools` telepítenie kell a csomagot - lásd [itt.](https://pypi.python.org/pypi/setuptools) Majd futtassa ezt:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Az automatikus frissítés engedélyezésének ellenőrzése

Először ellenőrizze, hogy engedélyezve van-e:

```bash
cat /etc/waagent.conf
```

Keresse meg az "AutoUpdate.Enabled" Ha ezt a kimenetet látja, akkor engedélyezve van:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

A futtatás engedélyezése:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Indítsa újra a waagent szolgáltatást
A legtöbb Linux disztribúciók:

```bash
sudo service waagent restart
```

Ubuntu esetén használja:

```bash
sudo service walinuxagent restart
```

CoreOS esetén használja a következőket:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Az Azure Linux Agent verziójának megerősítése
    
```bash
waagent -version
```

A CoreOS esetében előfordulhat, hogy a fenti parancs nem működik.

Látni fogja, hogy az Azure Linux-ügynök verziója frissült az új verzióra.

Az Azure Linux-ügynökkel kapcsolatos további információkért lásd: [Azure Linux Agent README](https://github.com/Azure/WALinuxAgent).
