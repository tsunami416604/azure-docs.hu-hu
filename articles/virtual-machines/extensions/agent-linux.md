---
title: Az Azure Linux virtuálisgép-ügynök – áttekintés
description: Ismerje meg, hogyan telepítheti és konfigurálhatja a Linux-ügynököt (waagent) a virtuális gép és az Azure Fabric Controller közötti interakció kezeléséhez.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: e41de979-6d56-40b0-8916-895bf215ded6
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/17/2016
ms.author: akjosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f22fbd77069488e7aaf490f93f42cde747444a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073851"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Az Azure Linux-ügynök ismertetése és használata

A Microsoft Azure Linux Agent (waagent) kezeli a Linux & FreeBSD kiépítését és a Virtuális gép és az Azure Fabric Controller közötti interakciót. A kiépítési funkciót biztosító Linux-ügynök mellett az Azure lehetővé teszi a felhőalapú init használatát is egyes Linux-operációs rendszerekhez. A Linux Agent a következő funkciókat biztosítja linuxos és FreeBSD IaaS telepítésekhez:

> [!NOTE]
> További információt a [README című témakörben talál.](https://github.com/Azure/WALinuxAgent/blob/master/README.md)
> 
> 

* **Kép kiépítés**
  
  * Felhasználói fiók létrehozása
  * SSH-hitelesítési típusok konfigurálása
  * Az SSH nyilvános kulcsainak és kulcspárjainak telepítése
  * Az állomásnév beállítása
  * Az állomásnév közzététele a platform DNS-ében
  * Az SSH-állomás kulcsának ujjlenyomatának jelentése a platformra
  * Erőforráslemez-kezelés
  * Az erőforráslemez formázása és csatlakoztatása
  * Csereterület konfigurálása
* **Hálózat**
  
  * Útvonalak kezelése a platform DHCP-kiszolgálóival való kompatibilitás javítása érdekében
  * Biztosítja a hálózati csatoló nevének stabilitását
* **Kernel**
  
  * Virtuális NUMA konfigurálása (kernel <`2.6.37`letiltása )
  * Hyper-V entrópiát fogyaszt /dev/random
  * SCSI időmegtetések konfigurálása a gyökéreszközhöz (amely távoli lehet)
* **Diagnosztika**
  
  * Konzol átirányítása a soros portra
* **SCVMM telepítések**
  
  * Észleli és rendszerindítással felismeri és rendszerezi a VMM-ügynököt Linuxra, ha system center es 2012 R2 környezetben fut
* **Virtuálisgép-bővítmény**
  
  * A Microsoft és a Partners által készített összetevő befecskendezése linuxos virtuális gépbe (IaaS) a szoftver- és konfigurációautomatizálás engedélyezéséhez
  * Vm-bővítmény referencia-megvalósítása[https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Kommunikáció
Az információáramlás a platformról az ügynökre két csatornán keresztül történik:

* Az IaaS-telepítések hez csatlakoztatott rendszerindítási idejű DVD. Ez a DVD tartalmaz egy OVF-kompatibilis konfigurációs fájlt, amely a tényleges SSH-kulcspárokon kívüli összes létesítési információt tartalmazza.
* Üzembe helyezési és topológia-konfiguráció kitolásához használt REST API-t feltéve TCP-végpont.

## <a name="requirements"></a>Követelmények
A következő rendszereket tesztelték, és ismert, hogy működjön együtt az Azure Linux-ügynök:

> [!NOTE]
> Ez a lista eltérhet a Microsoft Azure platformon található támogatott rendszerek hivatalos listájától, az itt leírtak szerint:[https://support.microsoft.com/kb/2805216](https://support.microsoft.com/kb/2805216)
> 
> 

* CoreOS
* CentOS 6.3+
* Red Hat Enterprise Linux 6.7+
* Debian 7.0+
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP3+
* Oracle Linux 6.4+

Egyéb támogatott rendszerek:

* FreeBSD 10+ (Azure Linux Agent v2.0.10+)

A Linux-ügynök bizonyos rendszercsomagoktól függ a megfelelő működés érdekében:

* Python 2.6+
* OpenSSL 1.0+
* OpenSSH 5.3+
* Fájlrendszer segédprogramok: sfdisk, fdisk, mkfs, parted
* Jelszó eszközök: chpasswd, sudo
* Szövegfeldolgozó eszközök: sed, grep
* Hálózati eszközök: ip-route
* Kernel támogatás az UDF fájlrendszerek csatlakoztatásához.

## <a name="installation"></a>Telepítés
Az Azure Linux-ügynök telepítésének és frissítésének elsődleges módja az RPM vagy deB-csomag használatával történő telepítés a disztribúció csomagtárából. Az összes [jóváhagyott terjesztési szolgáltatók](../linux/endorsed-distros.md) integrálja az Azure Linux-ügynök csomagot a rendszerképek és adattárak.

Tekintse meg a dokumentációt az [Azure Linux-ügynök tárházban a GitHub](https://github.com/Azure/WALinuxAgent) on speciális telepítési lehetőségek, például a telepítés forrásból vagy egyéni helyekre vagy előtagok.

## <a name="command-line-options"></a>Parancssori beállítások
### <a name="flags"></a>Zászlók
* bőbeszédű: A megadott parancs részletességének növelése
* force: Egyes parancsok interaktív megerősítésének kihagyása

### <a name="commands"></a>Parancsok
* súgó: Felsorolja a támogatott parancsokat és jelzőket.
* megszüntetés: Próbálja meg tisztítani a rendszert, és tegye alkalmassá az újraépítésre. A következő művelet törlődik:
  
  * Az összes SSH-állomáskulcs (ha a provisioning.regenerateSshHostKeyPair "y" a konfigurációs fájlban)
  * Nameserver konfiguráció az /etc/resolv.conf fájlban
  * Root jelszó az /etc/shadow könyvtárból (ha a kiépítés.DeleteRootPassword "y" a konfigurációs fájlban)
  * Gyorsítótárazott DHCP-ügyfélbérletek
  * Visszaállítja az állomásnevet a localhost.localdomain tartományra

> [!WARNING]
> A megszüntetés nem garantálja, hogy a rendszerkép törlődik az összes bizalmas információból, és alkalmas az újraelosztásra.
> 
> 

* deprovision+user: Mindent végrehajt a -deprovision (fent) és törli az utolsó kiépített felhasználói fiókot (a /var/lib/waagent-től származik) és a kapcsolódó adatokat. Ez a paraméter, ha egy lemezkép, amely korábban kiépített az Azure-ban, így lehet rögzíteni, és újra fel kell használni.
* verzió: A waagent verzióját jeleníti meg
* serialconsole: A GRUB konfigurálása a ttyS0 (az első soros port) megjelölésére rendszerindító konzolként. Ez biztosítja, hogy a rendszermag rendszerindító naplói a soros portra kerülnek, és hibakeresésre legyenek elérhetők.
* démon: Futtassa a waagent-t démonként a platformmal való interakció kezeléséhez. Ez az argumentum a waagent init parancsfájlban van megadva.
* start: A waagent futtatása háttérfolyamatként

## <a name="configuration"></a>Konfiguráció
A konfigurációs fájl (/etc/waagent.conf) szabályozza a waagent műveleteket. Az alábbiakban egy mintakonfigurációs fájlt mutat be:

    ```
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
    ```

A következő különböző konfigurációs lehetőségek et ismerteti. A konfigurációs beállítások három féleek; Logikai, karakterlánc vagy egész. A logikai konfigurációs beállítások "y" vagy "n" (y) megadhatók. A "Nincs" speciális kulcsszó egyes karakterlánctípus-konfigurációs bejegyzésekhez a következő részletekként használható:

**Provisioning.Enabled:**  
```
Type: Boolean  
Default: y
```
Ez lehetővé teszi, hogy a felhasználó engedélyezze vagy tiltsa le a kiépítési funkciót az ügynökben. Az érvényes értékek "y" vagy "n". Ha a kiépítés le van tiltva, az SSH-állomás és a felhasználói kulcsok a rendszerkép megmaradnak, és az Azure-létesítési API-ban megadott konfigurációk figyelmen kívül maradnak.

> [!NOTE]
> A `Provisioning.Enabled` paraméter alapértelmezés szerint "n" az Ubuntu Cloud Images, amelyek a felhő-init kiépítése.
> 
> 

**Provisioning.DeleteRootPassword:**  
```
Type: Boolean  
Default: n
```
Ha be van állítva, az /etc/shadow fájlban lévő gyökérjelszó törlődik a kiépítési folyamat során.

**Provisioning.RegenerateSshHostKeyPair:**  
```
Type: Boolean  
Default: y
```
Ha be van állítva, az összes SSH állomáskulcs-pár (ecdsa, dsa és rsa) törlődik a létesítési folyamat során az /etc/ssh/.If set, all SSH host key pairs (ecdsa, dsa, and rsa) are deleted during the provisioning process from /etc/ssh/. És egy friss kulcs pár jön létre.

A friss kulcspár titkosítási típusát a Provisioning.SshHostKeyPairType bejegyzés konfigurálhatja. Egyes disztribúciók újra létre SSH kulcspár minden hiányzó titkosítási típusok, amikor az SSH démon újraindítása (például újraindítás).

**Provisioning.SshHostKeyPairType:**  
```
Type: String  
Default: rsa
```
Ez beállítható egy titkosítási algoritmus típus, amely támogatja az SSH démon a virtuális gépen. A jellemzően támogatott értékek az "rsa", a "dsa" és az "ecdsa". A "putty.exe" a Windows rendszeren nem támogatja az "ecdsa"-t. Ha tehát a Putty.exe-t a Windows rendszeren linuxos telepítéshez kíván használni, használja az "rsa" vagy a "dsa" (rsa) vagy "dsa" (rsa) vagy "dsa" (rsa) vagy "dsa" (rsa) vagy "dsa" (rsa) vagy "dsa" (Rsa) vagy "dsa" (rsa) vagy "ds

**Provisioning.MonitorHostName:**  
```
Type: Boolean  
Default: y
```
Ha be van állítva, a waagent figyeli a Linux virtuális gépet a gazdanév változásaihoz (ahogy azt a "hostname" parancs visszaadta), és automatikusan frissíti a hálózati konfigurációt a lemezképben, hogy tükrözze a változást. Annak érdekében, hogy a névváltoztatást a DNS-kiszolgálókra szeretné átvinni, a hálózatkezelés újraindul a virtuális gépen. Ez az internetkapcsolat rövid elvesztését eredményezi.

**Provisioning.DecodeCustomData**  
```
Type: Boolean  
Default: n
```
Ha be van állítva, a waagent dekódolja a CustomData-t a Base64-ből.

**Provisioning.ExecuteCustomData**  
```
Type: Boolean  
Default: n
```
Ha be van állítva, a waagent a kiépítés után végrehajtja a CustomData-adatokat.

**Provisioning.AllowResetSysUser**
```
Type: Boolean
Default: n
```
Ez a beállítás lehetővé teszi a sys felhasználó jelszavának alaphelyzetbe állítását; az alapértelmezett beállítás le van tiltva.

**Provisioning.PasswordCryptId**  
```
Type: String  
Default: 6
```
A kripta által használt algoritmus jelszókivonat létrehozásakor.  
 1 - MD5  
 2a - Gömbhal  
 5 - SHA-256  
 6 - SHA-512  

**Provisioning.PasswordCryptSaltLength**  
```
Type: String  
Default: 10
```
A jelszókivonat létrehozásához használt véletlenszerű só hossza.

**ResourceDisk.Format:**  
```
Type: Boolean  
Default: y
```
Ha be van állítva, a platform által biztosított erőforráslemez tágítésre és a waagent által imitált, ha a felhasználó által a "ResourceDisk.Filesystem" fájlrendszerben kért fájlrendszer-típus nem "ntfs". A lemezen egyetlen Linux (83) típusú partíció érhető el. Ez a partíció nincs formázva, ha sikeresen csatlakoztatható.

**ResourceDisk.Filesystem:**  
```
Type: String  
Default: ext4
```
Ez adja meg az erőforráslemez fájlrendszertípusát. A támogatott értékek linuxos disztribúciótól függően változnak. Ha a karakterlánc X, akkor mkfs. X jelen kell lennie a Linux képet. Az SLES 11 képekáltalában "ext3" -ot használnak. A FreeBSD-képeken itt az "ufs2" szó nak kell lennie.

**ResourceDisk.MountPoint:**  
```
Type: String  
Default: /mnt/resource 
```
Ez határozza meg azt az elérési utat, amelyre az erőforráslemez csatlakoztatva van. Az erőforráslemez *egy ideiglenes* lemez, és lehet, hogy kiüríti, ha a virtuális gép kiürül.

**ResourceDisk.MountOptions**  
```
Type: String  
Default: None
```
Megadja a csatlakoztatási -o parancsnak átadandó lemezcsatlakoztatási beállításokat. Ez egy vesszővel tagolt értéklista, pl. 'Nodev,nosuid'. A részleteket lásd a (8) szerelvényben.

**ResourceDisk.EnableSwap:**  
```
Type: Boolean  
Default: n
```
Ha be van állítva, az erőforráslemezen létrejön egy lapozófájl (/swapfájl), amely hozzáadódik a rendszercsere-területhez.

**ResourceDisk.SwapSizeMB:**  
```
Type: Integer  
Default: 0
```
A lapozófájl mérete megabájtban.

**Logs.Verbose:**  
```
Type: Boolean  
Default: n
```
Ha be van állítva, a napló részletessége kivan nagyítva. A waagent a /var/log/waagent.log fájlba jelentkezik, és a rendszer logrotate funkcióját használja a naplók elforgatásához.

**operációs rendszer. EnableRDMA**  
```
Type: Boolean  
Default: n
```
Ha be van állítva, az ügynök megkísérli telepíteni, majd betölteni egy RDMA kernel illesztőprogramot, amely megfelel az alapul szolgáló hardver belső vezérlőprogramjának verziójának.

**operációs rendszer. RootDeviceScsiTimeout:**  
```
Type: Integer  
Default: 300
```
Ez a beállítás az SCSI időhamását másodpercek alatt konfigurálja az operációs rendszer lemezén és adatmeghajtóin. Ha nincs beállítva, a rendszer a rendszer alapértelmezéseit használja.

**operációs rendszer. Megnyitja a görbét:**  
```
Type: String  
Default: None
```
Ezzel a beállítással alternatív elérési utat adhat meg az openssl binárishoz, amelyet kriptográfiai műveletekhez használhat.

**HttpProxy.Host, HttpProxy.Port**  
```
Type: String  
Default: None
```
Ha be van állítva, az ügynök ezt a proxykiszolgálót használja az internet eléréséhez. 

**Automatikus frissítés.Engedélyezve**
```
Type: Boolean
Default: y
```
Engedélyezze vagy tiltsa le az automatikus frissítést a célállapot feldolgozásához; az alapértelmezett érték engedélyezve van.



## <a name="ubuntu-cloud-images"></a>Ubuntu cloud képek
Az Ubuntu Cloud Images [számos olyan](https://launchpad.net/ubuntu/+source/cloud-init) konfigurációs feladatot hajt végre, amelyeket egyébként az Azure Linux-ügynök kezelne. A következő különbségek érvényesek:

* **A provisioning.Enabled** alapértelmezés szerint "n" lesz az Ubuntu Cloud Images szolgáltatásban, amely felhőalapú alkalmazásokat használ a létesítési feladatok végrehajtásához.
* A következő konfigurációs paraméterek nincsenek hatással az Ubuntu Cloud Images-re, amelyek felhőalapú init használatával kezelik az erőforráslemezt és a felcserélési területet:
  
  * **ResourceDisk.Formátum**
  * **ResourceDisk.Filesystem fájlrendszer**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* További információt az alábbi erőforrásokban talál az erőforráslemez-csatlakoztatási pont és a lapozóterület konfigurálásához az Ubuntu Cloud Images rendszerén a kiépítés során:
  
  * [Ubuntu Wiki: Swap partíciók konfigurálása](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Egyéni adatok befecskendezése egy Azure virtuális gépbe](../windows/classic/inject-custom-data.md)

