---
title: Azure Linux rendszerű virtuális gép-ügynök – áttekintés |} A Microsoft Docs
description: Megtudhatja, hogyan telepítése és konfigurálása Linux-ügynök (waagent) a virtuális gép az Azure Fabric Controller kezeléséhez.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
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
ms.author: roiyz
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0c19d32f6c6f491a91ba6c2219be9fd016b5ec34
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243879"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Megismeréséhez és használatához az Azure Linux-ügynök

A Microsoft Azure Linux-ügynök (waagent) kezeli a Linux és FreeBSD kiépítés és az Azure Fabric Controller VM-szal. A Linux-ügynök kiépítési funkciókat biztosító Azure cloud-init használata az egyes Linux rendszerű OSE-kre lehetőséget is nyújt. A Linux-ügynök a következő funkciókat tartalmazza a Linux és freebsd rendszerű IaaS központi telepítések:

> [!NOTE]
> További információkért lásd: a [információs](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
> 
> 

* **Lemezképek telepítése**
  
  * A felhasználói fiók létrehozása
  * SSH hitelesítési típusok konfigurálása
  * Nyilvános SSH-kulcsokat és a kulcspárokat központi telepítését
  * A gazdagép nevének beállítása
  * A gazdagép nevét a platform DNS való közzétételéhez
  * A platform a jelentéskészítés SSH gazdagép ujjlenyomata
  * Erőforrás Lemezkezelés
  * Színformázás és az erőforrás-lemez csatlakoztatása
  * Lapozófájl-kapacitás beállítása
* **Hálózat**
  
  * Kezeli az útvonalak platform DHCP-kiszolgálókon való kompatibilitás érdekében
  * Biztosítja a stabilitását a hálózati adapter neve
* **Kernel**
  
  * Konfigurálja a virtuális NUMA-t (letiltja a kernel <`2.6.37`)
  * Hyper-V vysokou a /dev/random használ fel
  * Konfigurálja a SCSI-időtúllépések, a legfelső szintű eszköz (amely lehet távoli)
* **Diagnosztika**
  
  * A soros port konzol-átirányítás
* **SCVMM központi telepítések**
  
  * Észleli, és csatlakoztatja a Linuxhoz készült VMM-ügynököt, amikor a System Center Virtual Machine Manager 2012 R2 környezetben futó
* **Virtuálisgép-bővítmény**
  
  * Linux rendszerű virtuális gép (IaaS) szoftver engedélyezéséhez és a konfigurálás automatizálásához a Microsoft és partnerei által készített összetevők beszúrása
  * A Virtuálisgép-bővítmény referenciaimplementáció [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Kommunikáció
Az ügynök a platformról információáramlás két csatornákon keresztül történik:

* A rendszerindításkor DVD-ről a szolgáltatott infrastruktúra telepített példányainak csatolva. A DVD-t egy OVF-kompatibilis konfigurációs fájlt, amely tartalmazza a tényleges SSH keypairs kivételével az összes kiépítési információkat tartalmaz.
* TCP-végpont adatokhoz hozzáférést biztosító REST API segítségével a telepítési és konfigurációs topológiát.

## <a name="requirements"></a>Követelmények
A következő rendszerek teszteltük, és ismert, hogy az Azure Linux-ügynök használata:

> [!NOTE]
> Ez a lista eltérhet a Microsoft Azure platformon támogatott rendszerek hivatalos listáját itt leírtak szerint: [http://support.microsoft.com/kb/2805216](https://support.microsoft.com/kb/2805216)
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

* FreeBSD 10 + (az Azure Linux-ügynök v2.0.10 +)

A Linux-ügynök megfelelő működéséhez néhány rendszer csomag függ:

* Python 2.6
* OpenSSL 1.0 +
* Az OpenSSH 5.3 +
* Fájlrendszer segédprogramok: sfdisk, fdisk, mkfs, válogatottak
* Jelszó-eszközök: chpasswd, sudo
* Eszközök szövegfeldolgozást: csökkentésének, a grep
* A hálózati eszközök: ip-útvonal
* Kernel csatlakoztatni UDF fájlrendszereit támogatja.

## <a name="installation"></a>Telepítés
A terjesztési csomag adattárból az RPM- vagy DEB-csomag telepítését a telepítése és frissítése az Azure Linux-ügynök előnyben részesített módszer. Az összes a [terjesztési szolgáltatók által támogatott](../linux/endorsed-distros.md) integrálhatja az Azure-beli Linuxos ügynök csomag adattárak, valamint a lemezképek.

A dokumentáció a [Azure Linux-ügynök adattárat a Githubon](https://github.com/Azure/WALinuxAgent) a speciális telepítési lehetőségekről, például a forrás vagy egyéni helyek vagy előtagok telepítése.

## <a name="command-line-options"></a>Parancssori kapcsolók
### <a name="flags"></a>Jelölők
* részletes: növelése a megadott parancs
* kényszerített: hagyja ki az egyes parancsok interaktív megerősítése

### <a name="commands"></a>Parancsok
* Súgó: a támogatott parancsok és jelzők sorolja fel.
* megszüntetési: próbál meg törölni a rendszer, és adja meg a megfelelő reprovisioning. A következő művelet törlése:
  
  * Az összes SSH állomáskulcsok (ha Provisioning.RegenerateSshHostKeyPair "y", a konfigurációs fájlban)
  * Az /etc/resolv.conf névkiszolgáló-konfiguráció
  * Gyökér szintű jelszó a rendszergazda (ha Provisioning.DeleteRootPassword "y", a konfigurációs fájlban)
  * Gyorsítótárazott DHCP-ügyfélbérletek
  * A localhost.localdomain állomásnév visszaállítása

> [!WARNING]
> A megszüntetés nem garantálja, hogy a lemezkép törlődik a bizalmas adatok, és alkalmas terjesztési.
> 
> 

* megszüntetési + felhasználó: hajt végre minden - megszüntetési (feljebb) és is törli az utoljára kiépített felhasználói fiókot (/var/lib/waagent szerzett) és a kapcsolódó adatokat. Ezt a paramétert akkor, ha a megszüntetést olyan lemezképet, amely korábban az Azure-ban kiépítés volt, előfordulhat, hogy lehet rögzíteni és újra felhasználható.
* verzió: waagent verzióját jeleníti meg
* serialconsole: konfigurálja a grub-HIBÁT való megjelöléséhez ttyS0 (az első soros port), a rendszerindító konzol. Ez biztosítja, hogy a rendszermag rendszerindítási naplók küldött a soros port és a hibakereséshez elérhetővé.
* démon: waagent futtató démon a platformmal való kezeléséhez. Ennek az argumentumnak a waagent a waagent init parancsfájl van megadva.
* Indítsa el: waagent futtatása háttérfolyamatként

## <a name="configuration"></a>Konfiguráció
Egy konfigurációs fájl (/ etc/waagent.conf) waagent műveleteket szabályozza. Az alábbiakban látható egy minta konfigurációs fájlt:

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

Az alábbiakban különböző konfigurációs beállításokat a leírtak szerint. Konfigurációs lehetőségeket vannak három típusa létezik; Logikai érték beolvasása, karakterlánc vagy egész szám. A logikai lehetőségek "y" vagy "n" adható meg. A speciális kulcsszó "None" használható bizonyos karakterlánc típusú konfigurációs bejegyzéseket a következő adatokat:

**Provisioning.Enabled:**  
```
Type: Boolean  
Default: y
```
Ez lehetővé teszi a felhasználó engedélyezheti vagy tilthatja le a kiépítési az ügynök-funkciókat. Érvényes értékek: "y" vagy "n". Kiépítés le van tiltva, ha megmaradnak a gazdagép és a felhasználó lévő SSH-kulcsokat a lemezképet, és az Azure-beli üzembe helyezési API megadott minden beállítás figyelmen kívül hagyja.

> [!NOTE]
> A `Provisioning.Enabled` Ubuntu felhő-rendszerképek üzembe helyezés a cloud-init használó "n" paraméter alapértelmezett értéke.
> 
> 

**Provisioning.DeleteRootPassword:**  
```
Type: Boolean  
Default: n
```
Ha a beállított, az/etc/shadow fájlt a gyökér szintű jelszavát a kiépítési folyamat során elvész.

**Provisioning.RegenerateSshHostKeyPair:**  
```
Type: Boolean  
Default: y
```
Ha a beállított, az összes SSH gazdagép kulcspárokat (ecdsa, dsa és rsa) az/etc/ssh/a kiépítési folyamat során törlődnek. És a egy egyetlen új kulcspár.

A friss kulcspár titkosítási típus a Provisioning.SshHostKeyPairType bejegyzés által konfigurálható. Néhány disztribúciók hozza létre újra az összes hiányzó titkosítási típusok SSH-kulcspárok az SSH démon (például egy újraindítás) után újraindításakor.

**Provisioning.SshHostKeyPairType:**  
```
Type: String  
Default: rsa
```
Ez a virtuális gépen az SSH démon által támogatott titkosítási algoritmus típust állítható. Az általában támogatott értékei a következők: "rsa", "dsa" és "ecdsa". "putty.exe" a Windows nem támogatja a "ecdsa". Ezért ha szeretne Windows putty.exe használatával csatlakozhat egy Linux-telepítést, a "rsa" vagy "dsa".

**Provisioning.MonitorHostName:**  
```
Type: Boolean  
Default: y
```
Ha a készlet, waagent figyeli a Linux virtuális gép állomásneve módosítások (mivel a parancs által visszaadott "állomásnév"), és automatikusan frissíti az ábrán a változás tükrözése érdekében hálózati konfigurációját. Annak érdekében, hogy a névmódosítás leküldése a DNS-kiszolgálók, hálózat a virtuális gép újraindul. Az eredmény röviden internetkapcsolat elvesztését.

**Provisioning.DecodeCustomData**  
```
Type: Boolean  
Default: n
```
Ha a készlet, waagent a Base64 kódolású anyag CustomData dekódol.

**Provisioning.ExecuteCustomData**  
```
Type: Boolean  
Default: n
```
Ha a készlet, waagent CustomData kiépítése után hajtja végre.

**Provisioning.AllowResetSysUser**
```
Type: Boolean
Default: n
```
Ez a beállítás lehetővé teszi, hogy az alaphelyzetbe állítandó; a sys-felhasználó jelszavát alapértelmezett le van tiltva.

**Provisioning.PasswordCryptId**  
```
Type: String  
Default: 6
```
Jelszókivonat készítésekor használt titkosítási algoritmus.  
 1 - MD5  
 2a – Blowfish  
 5 – A SHA-256 ALGORITMUST  
 6 – SHA-512  

**Provisioning.PasswordCryptSaltLength**  
```
Type: String  
Default: 10
```
Jelszókivonat létrehozásához használt véletlenszerű Salt érték hossza.

**ResourceDisk.Format:**  
```
Type: Boolean  
Default: y
```
Ha a beállítása, az erőforrás lemez a platform által biztosított formátumú, és waagent által csatlakoztatott, ha a fájlrendszer típusát, a felhasználó a "ResourceDisk.Filesystem" nem "ntfs". A lemez Linux (83) típus egy partíció legyen elérhető. Ez a partíció nem van formázva, ha sikeresen csatlakoztatva.

**ResourceDisk.Filesystem:**  
```
Type: String  
Default: ext4
```
Ez meghatározza a fájlrendszer az erőforrás-lemez. Támogatott értékei eltérőek a Linux-disztribúció. Ha a karakterlánc X, majd mkfs. X a Linux-rendszerképek jelen kell lennie. SLES 11 lemezképeket általában használjon "ext3". FreeBSD lemezképek "ufs2" Itt kell használni.

**ResourceDisk.MountPoint:**  
```
Type: String  
Default: /mnt/resource 
```
Megadja az elérési utat, amelyen az erőforrás-lemez csatlakoztatva van. Az erőforrás-lemez egy *ideiglenes* lemezre, és előfordulhat, hogy ki kell üríteni, ha a virtuális gép.

**ResourceDisk.MountOptions**  
```
Type: String  
Default: None
```
Megadja a lemez csatlakoztatási beállítások történő átadása a mount -o parancsot. Például ez az értékek vesszővel elválasztott listáját. "nodev, nosuid". Tekintse meg a részleteket mount(8).

**ResourceDisk.EnableSwap:**  
```
Type: Boolean  
Default: n
```
Ha a beállítása, a lapozófájl (/ swapfile), az erőforrás-lemezen létrehozni, és a rendszer lapozóterület hozzáadni.

**ResourceDisk.SwapSizeMB:**  
```
Type: Integer  
Default: 0
```
MB-ban a lapozófájl mérete.

**Logs.Verbose:**  
```
Type: Boolean  
Default: n
```
Ha a csoportba, a napló részletességi súlyozott van. Waagent /var/log/waagent.log jelentkezik, és használja a naplók rotálása logrotate rendszerfunkcióit.

**OS.EnableRDMA**  
```
Type: Boolean  
Default: n
```
Ha a beállított, az ügynök megpróbálja telepíteni, és töltsön be egy RDMA kernel-illesztőprogram, amely megfelel az alapul szolgáló hardver belső vezérlőprogramjának verzióját.

**AZ OPERÁCIÓS RENDSZER. RootDeviceScsiTimeout:**  
```
Type: Integer  
Default: 300
```
Ezzel a beállítással az SCSI-időtúllépés másodpercben, az operációs rendszer lemez- és meghajtókon. Ha nincs beállítva, a rendszer alapértelmezett értékek használatát.

**AZ OPERÁCIÓS RENDSZER. OpensslPath:**  
```
Type: String  
Default: None
```
Ez a beállítás adja meg egy alternatív elérési utat a titkosítási műveletek használandó bináris openssl használható.

**HttpProxy.Host, HttpProxy.Port**  
```
Type: String  
Default: None
```
Ha a beállított, az ügynök az internet eléréséhez proxykiszolgáló használ. 

**AutoUpdate.Enabled**
```
Type: Boolean
Default: y
```
Engedélyezi vagy letiltja az automatikus frissítés feldolgozása; cél állapot Alapértelmezés szerint engedélyezve.



## <a name="ubuntu-cloud-images"></a>Ubuntu felhő-rendszerképek
Ubuntu felhő-rendszerképek használatához [a cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) számos, ellenkező esetben lenne az Azure Linux-ügynök által felügyelt konfigurációs feladatok végrehajtásához. Az alábbi eltérések a alkalmazni:

* **Provisioning.Enabled** , az alapértelmezett érték "n" Ubuntu felhő-rendszerképek, amely a cloud-init használatával üzembe helyezési feladatok végrehajtására.
* Az alábbi konfigurációs paramétereket nem befolyásolják a Ubuntu Felhőbeli rendszerkép található, a cloud-init használata kezelheti az erőforrás-lemez és a lapozófájl:
  
  * **ResourceDisk.Format**
  * **ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* További információkért tekintse meg az erőforrás-lemez csatlakoztatási pont konfigurálása és üzembe helyezés során a felhő-rendszerképek Ubuntu lapozófájl az alábbi forrásanyagokat:
  
  * [Ubuntu Wiki: A lapozófájl-kapacitás-partíciók konfigurálása](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Egyéni adatok betöltése az Azure virtuális gépként](../windows/classic/inject-custom-data.md)

