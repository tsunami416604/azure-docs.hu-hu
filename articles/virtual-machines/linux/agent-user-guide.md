---
title: Azure Linux virtuális gép ügynök áttekintése |} Microsoft Docs
description: Megtudhatja, hogyan telepítse és konfigurálja a Linux-ügynök (waagent) a virtuális gép az Azure Fabric Controller kezeléséhez.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: e41de979-6d56-40b0-8916-895bf215ded6
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: szark
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: be3955c670382af1a2b558e8e7d656ca5a1f353d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30839036"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Megismeréséhez és használatához az Azure Linux ügynök
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="introduction"></a>Bevezetés
A Microsoft Azure Linux-ügynök (waagent) a Linux és freebsd rendszerű kiépítés és az Azure Fabric Controller interakcióba VM kezeli. A következő funkciókat biztosít a Linux és freebsd rendszerű infrastruktúra-szolgáltatási központi telepítések:

> [!NOTE]
> Tekintse meg az Azure Linux ügynök [információs](https://github.com/Azure/WALinuxAgent/blob/master/README.md) további részleteket.
> 
> 

* **Kép kiépítése**
  
  * A felhasználói fiók létrehozása
  * SSH hitelesítési típus konfigurálása
  * Az SSH nyilvános kulcsok és kulcspárok központi telepítését
  * Beállítás a gazdagép neve
  * Az állomásnév közzététele a DNS platform
  * SSH-kulcs ujjlenyomat állomás Reporting a platformra
  * Erőforrás Lemezkezelés
  * Formázás és az erőforrás-lemez csatlakoztatása
  * Lapozófájl konfigurálása
* **Hálózat**
  
  * Útvonalak való kompatibilitás érdekében platform DHCP-kiszolgálók kezelése
  * Biztosítja a stabilitását a hálózati kapcsolat neve
* **Kernel**
  
  * Konfigurálja a virtuális NUMA (letiltja a kernel < 2.6.37)
  * Hyper-V entrópia /dev/random a felhasználva
  * Konfigurálja az SCSI-időtúllépések a legfelső szintű eszköz (amely lehet távoli)
* **Diagnosztika**
  
  * A soros portjára segítségével
* **Az SCVMM központi telepítések**
  
  * Észleli és a VMM-ügynök Linux betöltéséhez, ha a System Center Virtual Machine Manager 2012 R2 környezetben futó
* **Virtuálisgép-bővítmény**
  
  * Linux virtuális gép (IaaS) szoftver engedélyezéséhez és a konfigurációs automation Microsoft és a partnerei által készített összetevő beszúrása
  * A Virtuálisgép-bővítmény hivatkozási végrehajtása [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Kommunikáció
Az ügynöknek a platformról információáramlás két csatornákon keresztül történnek:

* A rendszerindítás DVD csatolt IaaS telepítésekhez. A DVD-t egy OVF-kompatibilis konfigurációs fájl, amely tartalmazza a tényleges SSH keypairs kivételével az összes kiépítési információkat tartalmaz.
* A TCP-végpont teszi ki a REST API-t használja a központi telepítés és a topológia konfigurációjával.

## <a name="requirements"></a>Követelmények
A következő rendszerek lettek tesztelve, és ismert, hogy az Azure Linux ügynök használata:

> [!NOTE]
> Ebben a listában a Microsoft Azure platformon támogatott rendszerek hivatalos listája eltérhet a itt leírtak szerint: [http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)
> 
> 

* CoreOS
* CentOS 6.3 +
* Red Hat Enterprise Linux 6.7 +
* Debian 7.0 +
* Ubuntu 12.04 +
* openSUSE 12.3 +
* SLES 11 SP3 +
* Oracle Linux 6.4 +

Egyéb támogatott rendszerek:

* Freebsd rendszerű 10 + (Azure Linux ügynök v2.0.10 +)

A Linux-ügynök megfelelő működéséhez néhány rendszer csomag függ:

* Python 2.6 +
* OpenSSL 1.0 +
* OpenSSH 5.3 +
* Fájlrendszer segédprogramok: sfdisk, fdisk, mkfs, válogatottak
* Jelszó-eszközök: chpasswd, sudo
* Eszközök feldolgozása szöveg: csökkentésének, grep
* A hálózati eszközök: ip-útvonal
* Kernel támogatása UDF fájlrendszerek csatlakoztatni.

## <a name="installation"></a>Telepítés
A telepítési csomag tárházból egy RPM vagy DEB-csomag telepítését a előnyben részesített lehetőség, telepítése és az Azure Linux ügynök frissítése. Minden a [terjesztési szolgáltatók által támogatott](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) az Azure Linux-ügynök csomagja integrálja a képeket és tárházak találhatók.

A dokumentációban találja a [Azure Linux ügynök-tárház a Githubon](https://github.com/Azure/WALinuxAgent) a speciális telepítési lehetőségeket, például a forrás vagy egyéni helyek vagy az előtagok telepítése.

## <a name="command-line-options"></a>Parancssori kapcsolók
### <a name="flags"></a>Jelzők
* részletes: növelése a megadott parancs
* kényszerített: hagyja ki az egyes parancsok interaktív megerősítése

### <a name="commands"></a>Parancsok
* Súgó: a támogatott parancsok és jelzők sorolja fel.
* deprovision: próbálja meg törölni a rendszer, és lehetővé teszi a megfelelő ismételt üzembe helyezéséhez. Ez a művelet törli a következő:
  
  * Az összes SSH állomáskulcsai (ha Provisioning.RegenerateSshHostKeyPair "y", a konfigurációs fájlban)
  * Nameserver configuration in /etc/resolv.conf
  * Gyökér szintű jelszavát a /etc/shadow (ha Provisioning.DeleteRootPassword "y", a konfigurációs fájlban)
  * Gyorsítótárazott DHCP-ügyfél bérletek
  * A localhost.localdomain állomásnév visszaállítása

> [!WARNING]
> Megszüntetés nem garantálható, hogy a lemezkép minden a bizalmas adatok törlődik, és a megfelelő terjesztési.
> 
> 

* deprovision + felhasználói: hajt végre, minden elemet - deprovision (fent) is törli a legutóbbi kiépített felhasználói fiókot (/var/lib/waagent nyert) és az adatok. Ez a paraméter esetén a megszüntetést egy olyanra, amely korábban az Azure-on kiépítés volt, előfordulhat, hogy lehet rögzíteni és újra felhasználni.
* verzió: waagent-verzió
* serialconsole: ttyS0 megjelölni LÁRVAJÁRAT konfigurálása (az első soros port) a rendszerindító konzollal. Ez biztosítja, hogy kernel rendszerindítási naplókat a soros port küldött és elérhetővé tenni a hibakereséshez.
* démon: waagent futtató démon kezeléséhez a platformon. Ennek az argumentumnak a waagent a waagent init parancsfájl van megadva.
* Start: háttérfolyamatként waagent futtatása

## <a name="configuration"></a>Konfiguráció
Egy konfigurációs fájl (/ etc/waagent.conf) waagent műveleteit szabályozza. Alább látható egy példa konfigurációs fájlt:

    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.AllowResetSysUser=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None
    AutoUpdate.Enabled=y

A különböző konfigurációs beállításait ismerteti részletesen. Beállítási lehetőségek állnak a három típusa létezik; Logikai érték, String vagy Integer. "Y" vagy "n" logikai konfigurációs beállításokat adhat meg. A speciális kulcsszó "None" néhány karakterlánc típusú konfigurációs elemet részletes, az alábbi használható.

**Provisioning.Enabled:**  
Típus: logikai  
Alapértelmezett: y

A felhasználó engedélyezhető vagy tiltható le az ügynököt a kiépítési funkciói. Érvényes értékek: "y" vagy "n". Kiépítés le van tiltva, ha a kép SSH-állomás és a felhasználói kulcsok megmaradnak, és a kiépítés API Azure-ban megadott minden beállítás figyelmen kívül hagyja.

> [!NOTE]
> A `Provisioning.Enabled` "n" Ubuntu felhő lemezképeket inicializálás felhőben történő üzembe helyezéséhez használjon a paraméter alapértelmezett értéke.
> 
> 

**Provisioning.DeleteRootPassword:**  
Típus: logikai  
Alapértelmezett: n

Ha be van állítva, a/etc/árnyékmásolat fájlban gyökér szintű jelszavát a telepítési folyamat során elvész.

**Provisioning.RegenerateSshHostKeyPair:**  
Típus: logikai  
Alapértelmezett: y

Ha be van állítva, az összes SSH állomás kulcspárok (ecdsa, dsa és rsa) a rendszer törli az/etc/ssh/a telepítési folyamat során. És egy egyetlen új kulcspár.

A friss kulcspár titkosítási típus a Provisioning.SshHostKeyPairType bejegyzés által konfigurálható. Vegye figyelembe, hogy néhány terjesztési hozza létre SSH-kulcspár bármely hiányzó titkosítási típusok, az SSH démon (például biztonsági újraindításkor) újraindításakor.

**Provisioning.SshHostKeyPairType:**  
Típus: Karakterlánc  
Alapértelmezett: rsa

Ez a virtuális gépen az SSH démon által támogatott titkosítási algoritmus típust állítható be. A általában támogatott értékek: "rsa", "dsa" és "ecdsa". Vegye figyelembe, hogy a "putty.exe" a Windows nem támogatja "ecdsa". Úgy Ha azt tervezi, a Windows putty.exe segítségével csatlakozzon a Linux-környezethez, használja az "rsa" vagy "dsa".

**Provisioning.MonitorHostName:**  
Típus: logikai  
Alapértelmezett: y

Ha beállításához waagent ellenőrzi a Linux virtuális gép állomásnevét módosítások (mivel a parancs által visszaadott "állomásnév") és automatikus frissítése a kép, hogy tükrözze a hálózati beállításokat. Ahhoz, hogy a kiszolgálónév-változás leküldése a DNS-kiszolgálók, hálózat a virtuális gép újraindul. Ekkor röviden a Internet kapcsolat megszakadása.

**Provisioning.DecodeCustomData**  
Típus: logikai  
Alapértelmezett: n

Ha beállításához waagent meg fog dekódolni a Base64 kódolású anyag CustomData.

**Provisioning.ExecuteCustomData**  
Típus: logikai  
Alapértelmezett: n

Ha beállításához waagent végrehajtja a CustomData kiépítése után.

**Provisioning.AllowResetSysUser** típusa: logikai alapértelmezett: n

Ez a beállítás lehetővé teszi, hogy a jelszót a sys felhasználó állítható alaphelyzetbe; alapértelmezett le van tiltva.

**Provisioning.PasswordCryptId**  
Típus: Karakterlánc  
Alapértelmezett: 6

Jelszókivonat létrehozásakor használt titkosítási algoritmus.  
 1 - MD5  
 2a – Blowfish  
 5 - SHA-256  
 6 - SHA-512  

**Provisioning.PasswordCryptSaltLength**  
Típus: Karakterlánc  
Alapértelmezett: 10

Jelszókivonat létrehozásához használt véletlenszerű védőérték hosszát.

**ResourceDisk.Format:**  
Típus: logikai  
Alapértelmezett: y

Ha beállítása, a erőforrás lemez a platform által biztosított fog kell formázni, waagent által csatlakoztatott, ha a fájlrendszer típusát kérte a felhasználónak a "ResourceDisk.Filesystem" nem "ntfs". Egy olyan partíciót, Linux (83) típusú lesz elérhető a lemezen. Vegye figyelembe, hogy ez a partíció nem lesz formázva Ha sikeresen csatlakoztatva.

**ResourceDisk.Filesystem:**  
Típus: Karakterlánc  
Alapértelmezett: ext4

Azt határozza meg az erőforrás-lemez a fájlrendszer típusát. A Linux-disztribúció által támogatott értékek eltérők lehetnek. Ha a karakterlánc X, majd mkfs. X a Linux-lemezkép jelen kell lennie. SLES 11 lemezképeket általában használjon "ext3". Freebsd rendszerű lemezképek itt "ufs2" kell használni.

**ResourceDisk.MountPoint:**  
Típus: Karakterlánc  
Alapértelmezett: / mnt és az erőforrások 

Azt határozza meg az elérési utat, amelyen az erőforrás-lemez csatlakoztatva van. Vegye figyelembe, hogy az erőforrás-lemez egy *ideiglenes* lemezre, és előfordulhat, hogy szerepelnek, ha a virtuális gép van platformelőfizetés.

**ResourceDisk.MountOptions**  
Típus: Karakterlánc  
Alapértelmezett: nincs

A mount -o parancs átadandó lemez csatlakoztatási beállításait adja meg. Például ez az értékek, vesszővel elválasztott listája. "nodev, nosuid". Tekintse meg a részletes mount(8).

**ResourceDisk.EnableSwap:**  
Típus: logikai  
Alapértelmezett: n

Ha beállításához lapozófájl (/ swapfile), az erőforrás lemezen létrehozni, és a rendszer lapozóterület hozzáadni.

**ResourceDisk.SwapSizeMB:**  
Típus: egész szám  
Alapértelmezett: 0

A lapozófájl mérete (MB) mérete.

**Logs.Verbose:**  
Típus: logikai  
Alapértelmezett: n

Ha be van állítva, napló részletességi súlyozott van. Waagent /var/log/waagent.log jelentkezik, és kihasználja a rendszer logrotate elforgatása naplókat.

**OS.EnableRDMA**  
Típus: logikai  
Alapértelmezett: n

Ha állítsa be, az ügynök megpróbálja telepíteni, és töltsön be egy RDMA egy rendszermag-illesztőprogramot, amely ugyanolyan verziójúak, mint az alapul szolgáló hardverben belső vezérlőprogramját.

**OS.RootDeviceScsiTimeout:**  
Típus: egész szám  
Alapértelmezett: 300

Ez konfigurálja az SCSI-időtúllépés másodpercben az operációs rendszer lemez- és meghajtókon. Ha nincs beállítva, a rendszer alapértelmezett értékeket használják.

**AZ OPERÁCIÓS RENDSZER. OpensslPath:**  
Típus: Karakterlánc  
Alapértelmezett: nincs

Ez egy alternatív elérési utat a titkosítási műveletek használandó bináris openssl megadását is használható.

**HttpProxy.Host, HttpProxy.Port**  
Típus: Karakterlánc  
Alapértelmezett: nincs

Ha állítsa be, az ügynök fogja használni a proxykiszolgálót az internet eléréséhez. 

**AutoUpdate.Enabled** típusa: logikai alapértelmezett: y

Engedélyezi vagy letiltja az automatikus frissítés feldolgozása; célt állapothoz Alapértelmezés szerint engedélyezve.



## <a name="ubuntu-cloud-images"></a>Ubuntu felhő lemezképek
Vegye figyelembe, hogy Ubuntu felhő lemezképek használata [felhő inicializálás](https://launchpad.net/ubuntu/+source/cloud-init) számos konfigurációs feladatok végrehajtását, amelyek egyébként volna kezeli az Azure Linux ügynök.  Vegye figyelembe a következő eltérésekkel:

* **Provisioning.Enabled** az alapértelmezett érték "n" a telepítési feladatok végrehajtásához használja a felhő inicializálás Ubuntu felhő lemezképeket.
* A következő konfigurációs paraméterek nem befolyásolják a felhő inicializálás segítségével kezelheti az erőforrás-lemez, és a lapozófájl Ubuntu felhő lemezképek:
  
  * **ResourceDisk.Format**
  * **ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**
* Ellenőrizze a lemez erőforrás csatlakoztatási pontjának konfigurálásával és a lapozófájl Ubuntu felhő képek a kiépítés során a következőket:
  
  * [Ubuntu Wiki: Lapozófájl-kapacitás-partíciók konfigurálása](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Egyéni adatok hogy Azure virtuális géphez](../windows/classic/inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

