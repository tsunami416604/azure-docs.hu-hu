---
title: Gyakori kérdések az Azure NetApp-fájlokról | Microsoft dokumentumok
description: Gyakori kérdések megválaszolása az Azure NetApp-fájlokkal kapcsolatban.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: b-juche
ms.openlocfilehash: 59453dbb9617f5a2ddbdca08709747b11a688560
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547089"
---
# <a name="faqs-about-azure-netapp-files"></a>Gyakori kérdések az Azure NetApp-fájlokról

Ez a cikk választ ad az Azure NetApp-fájlokkal kapcsolatos gyakori kérdésekre(GYIK). 

## <a name="networking-faqs"></a>Gyakori kérdések a hálózatkezelésről

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Az NFS-adatelérési út az interneten keresztül vezet?  

Nem. Az NFS-adatelérési út nem megy át az interneten. Az Azure NetApp Files egy Azure natív szolgáltatás, amely az Azure virtuális hálózatba (VNet) van telepítve, ahol a szolgáltatás elérhető. Az Azure NetApp Files egy delegált alhálózatot használ, és közvetlenül a virtuális hálózaton egy hálózati adaptert tartalmaz. 

A [részleteket az Azure NetApp Files hálózati tervezésirányelvei](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) című témakörben találja.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Csatlakoztathatok egy már létrehozott virtuális hálózatot az Azure NetApp Files szolgáltatáshoz?

Igen, csatlakoztathatja a létrehozott virtuális hálózatokat a szolgáltatáshoz. 

A [részleteket az Azure NetApp Files hálózati tervezésirányelvei](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) című témakörben találja.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Csatlakoztathatok egy NFS-kötetet az Azure NetApp-fájlokból a DNS FQDN-név használatával?

Igen, ha létrehozza a szükséges DNS-bejegyzéseket. Az Azure NetApp Files biztosítja a szolgáltatás IP a kiosztott kötet. 

> [!NOTE] 
> Az Azure NetApp-fájlok szükség szerint további IP-ket telepíthetnek a szolgáltatáshoz.  Előfordulhat, hogy a DNS-bejegyzéseket rendszeresen frissíteni kell.

## <a name="security-faqs"></a>Biztonsági gyakori kérdések

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Az Azure virtuális gép és a tárterület közötti hálózati forgalom titkosítható?

Adatforgalom (az NFSv3, NFSv4.1 vagy SMBv3 kliens ről az Azure NetApp Files kötetekre irányuló forgalma) nincs titkosítva. Azonban az Azure-beli virtuális gép (NFS- vagy SMB-ügyfelet futtató) és az Azure NetApp-fájlok közötti forgalom ugyanolyan biztonságos, mint bármely más Azure-VM-vm-vm-forgalom. Ez a forgalom helyi az Azure adatközpont-hálózat. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Lehet a tároló tinikben titkosítva?

Az összes Azure NetApp-fájlkötet a FIPS 140-2 szabvány használatával van titkosítva. Az összes kulcsot az Azure NetApp Files szolgáltatás kezeli. 

### <a name="how-are-encryption-keys-managed"></a>Hogyan történik a titkosítási kulcsok kezelése? 

Az Azure NetApp-fájlok kulcskezelését a szolgáltatás kezeli. Minden kötethez egyedi XTS-AES-256 adattitkosítási kulcs jön létre. A titkosítási kulcshierarchia az összes kötetkulcs titkosítására és védelmére szolgál. Ezek a titkosítási kulcsok soha nem jelennek meg vagy nem titkosított formátumban jelennek meg. A titkosítási kulcsok azonnal törlődnek a kötet törlésekor.

A felhasználó által felügyelt kulcsok (Hozd a saját kulcsok) az Azure dedikált HSM érhető el ellenőrzött alapon az USA keleti, USA West2 és az USA déli középső régióiban.  A hozzáférést a. **anffeedback@microsoft.com** A kapacitás rendelkezésre áll, a kérelmek et jóvá kell hagyni.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Konfigurálhatom az NFS exportálási szabályzatszabályait az Azure NetApp Files szolgáltatáscsatlakoztatási célhoz való hozzáférés szabályozására?


Igen, legfeljebb öt szabályt konfigurálhat egyetlen NFS-exportálási házirendben.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Támogatja az Azure NetApp-fájlok a hálózati biztonsági csoportokat?

Nem, jelenleg nem alkalmazhat hálózati biztonsági csoportokat az Azure NetApp-fájlok delegált alhálózatára vagy a szolgáltatás által létrehozott hálózati felületekre.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Használhatom az Azure IAM-et az Azure NetApp-fájlokkal?

Igen, az Azure NetApp Files támogatja az RBAC-funkciókat az Azure IAM szolgáltatással.

## <a name="performance-faqs"></a>Teljesítmény – gyakori kérdések

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Mit kell tennem az Azure NetApp Files teljesítményének optimalizálása vagy finomhangolása érdekében?

A teljesítménykövetelmények szerint a következő műveleteket teheti meg: 
- Győződjön meg arról, hogy a virtuális gép megfelelő méretezése.
- A virtuális gép gyorsított hálózatba írásának engedélyezése.
- Válassza ki a kívánt szolgáltatási szintet és méretet a kapacitáskészlethez.
- Hozzon létre egy kötetet a kapacitás és a teljesítmény kívánt kvótamérettel.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Hogyan konvertálhatom az Azure NetApp-fájlok átviteli sebességű szolgáltatási szintjeit IOPS-ra?

Az MB/s-t IOPS-ra konvertálhatja a következő képlet telje:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Hogyan módosíthatom egy kötet szolgáltatási szintjét?

A kötet szolgáltatásszintjének módosítása jelenleg nem támogatott.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Hogyan figyelhetem az Azure NetApp Files teljesítményét?

Az Azure NetApp Files mennyiségi teljesítménymutatókat biztosít. Az Azure Monitor használatával is figyelheti az Azure NetApp-fájlok használati metrikákat.  Az Azure NetApp-fájlok teljesítménymutatóinak listáját az [Azure NetApp-fájlok metrikák](azure-netapp-files-metrics.md) című témakörében olvassa el.

## <a name="nfs-faqs"></a>NFS – gyakori kérdések

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Azt szeretném, hogy egy kötet automatikusan csatlakoztatva, amikor egy Azure virtuális gép indításakor vagy újraindítása.  Hogyan konfigurálhatom a gazdagépemet az állandó NFS-kötetekhez?

Ha egy NFS-kötet automatikusan csatlakoztatja a virtuális gép `/etc/fstab` indításakor vagy újraindításkor, adjon hozzá egy bejegyzést a fájlhoz az állomáson. 

A részleteket [a Kötet csatlakoztatása vagy leválasztása Windows vagy Linux rendszerű virtuális gépekhez.](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Miért nem jeleníti meg a DF parancs az NFS-ügyfélen a kiosztott kötet méretét?

A DF-ben jelentett kötetmérete az Azure NetApp Files kötet maximális mérete. Az Azure NetApp Files kötet mérete a DF parancsban nem tükrözi a kötet kvótáját vagy méretét.  Az Azure NetApp Files kötetméretét vagy kvótáját az Azure Portalon vagy az API-n keresztül szerezheti be.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Milyen NFS-verziót támogat az Azure NetApp Files?

Azure NetApp Files supports NFSv3 and NFSv4.1. Kötetet az NFS-verzióval is [létrehozhat.](azure-netapp-files-create-volumes.md) 

### <a name="how-do-i-enable-root-squashing"></a>Hogyan engedélyezhetem a gyökérösszenyomódását?

A gyökérösszenyomódás jelenleg nem támogatott.

## <a name="smb-faqs"></a>SMB – gyakori kérdések

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Szükséges az Active Directory-kapcsolat az SMB-hozzáféréshez? 

Igen, az SMB-kötet telepítése előtt létre kell hoznia egy Active Directory-kapcsolatot. A megadott tartományvezérlőknek az Azure NetApp-fájlok delegált alhálózatának kell elérhetőnek lennie a sikeres kapcsolat érdekében.  A részleteket az [SMB-kötet létrehozása](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) című témakörben találja. 

### <a name="how-many-active-directory-connections-are-supported"></a>Hány Active Directory-kapcsolat támogatott?

Az Azure NetApp Files nem támogatja a több Active Directory (AD) kapcsolatok egy *régióban,* még akkor is, ha az AD-kapcsolatok különböző NetApp-fiókok. Egy *előfizetésben*azonban több AD-kapcsolat is lehet, feltéve, hogy az AD-kapcsolatok különböző régiókban találhatók. Ha egy adott régióban több AD-kapcsolatra van szüksége, ehhez külön előfizetéseket is használhat. 

Az AD-kapcsolat NetApp-fiókonként van konfigurálva; az AD-kapcsolat csak azon a NetApp-fiókon keresztül látható, amelyben létrejött.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Támogatja az Azure NetApp-fájlok az Azure Active Directoryt? 

Az [Azure Active Directory (AD) tartományi szolgáltatások](https://docs.microsoft.com/azure/active-directory-domain-services/overview) és az Active Directory tartományi szolgáltatások [(AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) is támogatottak. Meglévő Active Directory-tartományvezérlőket használhat az Azure NetApp-fájlokkal. A tartományvezérlők az Azure-ban virtuális gépként, illetve ExpressRoute-on vagy S2S VPN-en keresztül is elláthatók az Azure-ban. Az Azure NetApp Files jelenleg nem támogatja az AD-csatlakozást az [Azure Active Directoryhoz.](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/)

Ha Az Azure NetApp-fájlokat az Azure Active Directory tartományi `OU=AADDC Computers` szolgáltatásokkal használja, a szervezeti egység elérési útja az Active Directory konfigurálása a NetApp-fiókhoz.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>A Windows Server Active Directory mely verziói támogatottak?

Az Azure NetApp Files támogatja az Active Directory tartományi szolgáltatások Windows Server 2008r2SP1-2019-es verzióit.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Miért nem jeleníti meg az SMB-ügyfélen rendelkezésre álló hely a kiosztott méretet?

Az SMB-ügyfél által jelentett kötetmérete az Azure NetApp Files kötet maximális mérete. Az Azure NetApp Files kötet mérete az SMB-ügyfélen látható módon nem tükrözi a kötet kvótáját vagy méretét. Az Azure NetApp Files kötetméretét vagy kvótáját az Azure Portalon vagy az API-n keresztül szerezheti be.

### <a name="does-azure-netapp-files-support-kerberos-encryption"></a>Támogatja az Azure NetApp-fájlok a Kerberos titkosítást?

Igen, alapértelmezés szerint az Azure NetApp Files támogatja az AES-128 és az AES-256 titkosítást a szolgáltatás és a megcélzott Active Directory tartományvezérlők közötti forgalomhoz. A követelményekért [lásd: SMB-kötet létrehozása az Azure NetApp-fájlokhoz.](azure-netapp-files-create-volumes-smb.md) 

<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="capacity-management-faqs"></a>Kapacitáskezelési gyakori kérdések

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Hogyan figyelhetem a kapacitáskészlet és az Azure NetApp-fájlok mennyiségének használatát? 

Az Azure NetApp Files kapacitáskészletet és kötethasználati metrikákat biztosít. Az Azure NetApp-fájlok használatának figyeléséhez az Azure Monitor használatával is figyelheti. A részletekért tekintse meg [az Azure NetApp-fájlok metrikákat.](azure-netapp-files-metrics.md) 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Kezelhetem az Azure NetApp-fájlokat az Azure Storage Explorer en keresztül?

Nem. Az Azure NetApp-fájlokat az Azure Storage Explorer nem támogatja.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Hogyan állapíthatom meg, hogy egy könyvtár megközelíti-e a korlát méretét?

Az ügyfél `stat` parancsával megállapíthatja, hogy egy könyvtár megközelíti-e a maximális méretkorlátot (320 MB).

320 MB-os könyvtár esetén a blokkok száma 655360, és minden blokkméret 512 bájt.  (Azaz, 320x1024x1024/512.)  

Példák:

    [makam@cycrh6rtp07 ~]$ stat bin
    File: 'bin'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

    [makam@cycrh6rtp07 ~]$ stat tmp
    File: 'tmp'
    Size: 12288           Blocks: 24         IO Block: 65536  directory
 
    [makam@cycrh6rtp07 ~]$ stat tmp1
    File: 'tmp1'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

## <a name="data-migration-and-protection-faqs"></a>Adatmigrációról és -védelemről – gyakori kérdések

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Hogyan telepíthetek át adatokat az Azure NetApp-fájlokba?
Az Azure NetApp Files NFS- és SMB-köteteket biztosít.  Az adatok nak a szolgáltatásba történő áttelepítéséhez bármilyen fájlalapú másolási eszközt használhat. 

A NetApp SaaS-alapú megoldást kínál, [a NetApp Cloud Sync-et.](https://cloud.netapp.com/cloud-sync-service)  A megoldás lehetővé teszi, hogy NFS- vagy SMB-adatokat replikáljon az Azure NetApp Files NFS-exportálására vagy SMB-megosztásokra. 

Az adatok másolásához ingyenes eszközök széles skáláját is használhatja. NFS esetén számítási feladatok eszközeivel, például [az rsync-el](https://rsync.samba.org/examples.html) másolhatja és szinkronizálhatja a forrásadatokat egy Azure NetApp Files kötetbe. Az SMB-hez ugyanúgy használhatja a [robocopy-feladatokat.](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)  Ezek az eszközök fájl- vagy mappaengedélyeket is replikálhatnak. 

A helyszíni adatok Azure NetApp-fájlokba való áttelepítésének követelményei a következők: 

- Győződjön meg arról, hogy az Azure NetApp-fájlok elérhetők a cél Azure-régióban.
- Ellenőrizze a forrás és az Azure NetApp Files célkötet IP-címe közötti hálózati kapcsolatot. A helyszíni és az Azure NetApp-fájlok szolgáltatás közötti adatátvitel az ExpressRoute-on keresztül támogatott.
- Hozza létre a cél Azure NetApp Files kötetet.
- A forrásadatok átvitele a célkötetre a kívánt fájlmásolási eszközzel.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Hogyan hozhatok létre másolatot egy Azure NetApp Files kötetről egy másik Azure-régióban?
    
Az Azure NetApp Files NFS- és SMB-köteteket biztosít.  Bármely fájlalapú másolási eszköz használható az Azure-régiók közötti adatok replikálására. 

A NetApp SaaS alapú megoldást kínál, [a NetApp Cloud Sync-et.](https://cloud.netapp.com/cloud-sync-service)  A megoldás lehetővé teszi, hogy NFS- vagy SMB-adatokat replikáljon az Azure NetApp Files NFS-exportálására vagy SMB-megosztásokra. 

Az adatok másolásához ingyenes eszközök széles skáláját is használhatja. NFS esetén számítási feladatok eszközeivel, például [az rsync-el](https://rsync.samba.org/examples.html) másolhatja és szinkronizálhatja a forrásadatokat egy Azure NetApp Files kötetbe. Az SMB-hez ugyanúgy használhatja a [robocopy-feladatokat.](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)  Ezek az eszközök fájl- vagy mappaengedélyeket is replikálhatnak. 

Az Azure NetApp Files-kötet ek egy másik Azure-régióba történő replikálásának követelményei a következők: 
- Győződjön meg arról, hogy az Azure NetApp-fájlok elérhetők a cél Azure-régióban.
- Ellenőrizze a virtuális hálózatok közötti hálózati kapcsolatot az egyes régiókban. Jelenleg a virtuális hálózatok közötti globális társviszony-létesítés nem támogatott.  A virtuális hálózatok közötti kapcsolatot expressroute-kapcsolattal vagy S2S VPN-kapcsolathasználatával hozhatja létre. 
- Hozza létre a cél Azure NetApp Files kötetet.
- A forrásadatok átvitele a célkötetre a kívánt fájlmásolási eszközzel.

### <a name="is-migration-with-azure-data-box-supported"></a>Támogatott az áttelepítés az Azure Data Box használatával?

Nem. Az Azure Data Box jelenleg nem támogatja az Azure NetApp-fájlokat. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Támogatott az áttelepítés az Azure importálási/exportálási szolgáltatásával?

Nem. Az Azure importálási/exportálási szolgáltatás jelenleg nem támogatja az Azure NetApp-fájlokat.

## <a name="next-steps"></a>További lépések  

- [A Microsoft Azure ExpressRoute – gyakori kérdések](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [A Microsoft Azure virtuális hálózat – gyakori kérdések](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Azure-támogatáskérések létrehozása](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)
- [Gyakori kérdések az Azure NetApp-fájlok SMB-teljesítményéről](azure-netapp-files-smb-performance.md)
