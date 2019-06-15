---
title: Azure NetApp Files szolgáltatással kapcsolatos gyakori kérdések |} A Microsoft Docs
description: Válaszok az Azure Files-NetApp kapcsolatos gyakori kérdések.
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
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: 6f1ca3398678b59a81e5c22b51b36a1f5505d4c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65806386"
---
# <a name="faqs-about-azure-netapp-files"></a>Azure NetApp Files szolgáltatással kapcsolatos gyakori kérdések

Ebben a cikkben megválaszolunk Azure NetApp fájlokkal kapcsolatos gyakori kérdések (GYIK). 

## <a name="networking-faqs"></a>Hálózatkezelés – gyakori kérdések

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Nem az NFS adatútvonal fogadja az interneten keresztül?  

Nem. Az NFS-adatok elérési útja nem az interneten keresztül halad. NetApp Azure Files az Azure szolgáltatása natív, amely helyezünk üzembe az Azure Virtual Network (VNet) hol érhető el a szolgáltatást. NetApp Azure Files delegált alhálózatot használ, valamint kiosztja a virtuális hálózat közvetlenül a hálózati adapter. 

Lásd: [útmutató az Azure Files-NetApp hálózati tervezési](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) részleteiről.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Összekapcsolhatok egy már létrehozott virtuális hálózatban az Azure NetApp Files szolgáltatásba?

Igen, Ön által létrehozott virtuális hálózatok is kapcsolódni a szolgáltatáshoz. 

Lásd: [útmutató az Azure Files-NetApp hálózati tervezési](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) részleteiről.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Csatlakoztathatok egy NFS-kötet Azure NetApp fájlt DNS-tartománynév használatával?

Igen, akkor is, ha a szükséges DNS-bejegyzéseket létrehozni. NetApp Azure Files a szolgáltatás IP-cím. a kiosztott kötet választékát kínálja. 

> [!NOTE] 
> NetApp Azure Files telepítheti a szolgáltatás további IP-címek, szükség szerint.  DNS-bejegyzéseket kell rendszeresen frissülnek.

## <a name="security-faqs"></a>Biztonság – gyakori kérdések

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Az Azure virtuális gép és a tároló közötti hálózati adatforgalom titkosítható?

(Forgalom NFSv3 vagy SMBv3 ügyfélről Azure NetApp fájlok kötetek) adatforgalom nincs titkosítva. Azonban az Azure NetApp fájlokat az Azure virtuális Gépet (egy NFS- vagy SMB-ügyfél) érkező forgalom is ugyanolyan biztonságosak, mint bármely más Azure VM-Gépeken forgalom. Ezt a forgalmat a helyi az Azure adatközpont-hálózaton. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Inaktív titkosíthatók a tárterületet?

Az összes Azure NetApp fájlok kötetek titkosítottak, a FIPS 140-2 szabvány használatával. Az összes kulcs az Azure Files-NetApp szolgáltatás kezeli. 

### <a name="how-are-encryption-keys-managed"></a>Hogyan kezeli a titkosítási kulcsokat? 

Kulcskezelés Azure NetApp fájlok esetében a szolgáltatás kezeli.  Felhasználó által kezelt kulcsok használata (Bring Your Own kulcsok) jelenleg nem támogatott.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Konfigurálhatja az NFS exportálási szabályok szabályozható a NetApp fájlok Azure service csatlakoztatási célt?


Igen, legfeljebb öt szabályok beállíthatja az NFS exportálási egyetlen házirendet.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Azure Files-NetApp támogatja a hálózati biztonsági csoportok?

Nem, jelenleg nem alkalmazhat hálózati biztonsági csoportok az Azure-NetApp fájlokat vagy a hálózati adapterek, a szolgáltatás által létrehozott delegált alhálózathoz.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Használható az Azure Files-NetApp Azure IAM?

Igen, a NetApp fájlokat az Azure RBAC-funkciók az Azure IAM támogatja.

## <a name="performance-faqs"></a>Teljesítmény – gyakori kérdések

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Mit tegyek optimalizálása, vagy az Azure Files-NetApp teljesítmény hangolása?

/ Teljesítmény-követelmények a következőket teheti: 
- Győződjön meg arról, hogy a virtuális gép a megfelelő a méretezése.
- A virtuális gép gyorsított hálózatkezelés engedélyezéséhez.
- Válassza ki a kívánt szolgáltatási szint és a kapacitás a készlet méretét.
- Hozzon létre egy kötetet a kívánt kvótát mérete, a kapacitás és teljesítmény érdekében.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Hogyan tudok áttérni átviteli-alapú szolgáltatásszintek NetApp Azure-fájlok iops-t?

MB/s átválthat az iops-érték használatával a következő képletre:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Hogyan változtatható meg a szolgáltatási szint egy kötet?

A szolgáltatási szint egy kötet módosítása jelenleg nem támogatott.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Hogyan követhetem figyelemmel az Azure Files-NetApp teljesítmény?

NetApp Azure Files kötet teljesítmény-mérőszámokat biztosít. A használati metrikák Azure NetApp fájlok figyelését is használhatja az Azure Monitor.  Lásd: [Azure NetApp fájlok metrikák](azure-netapp-files-metrics.md) teljesítmény-mérőszámok az Azure NetApp fájlok listáját.

## <a name="nfs-faqs"></a>Az NFS – gyakori kérdések

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Szeretnék egy kötetet, automatikusan csatlakoztatva van egy Azure virtuális gép indításakor vagy újraindítása.  Hogyan konfigurálható a saját gazdagépén állandó NFS-kötetek?

Az NFS-kötet automatikusan csatlakoztassa a virtuális gépek indítása vagy újraindítása, a bejegyzés hozzáadása a `/etc/fstab` fájlt a gazdagépen. 

Például:`$ANFIP:/$FILEPATH      /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

- $ANFIP  
    Az Azure NetApp fájlok kötet a kötet tulajdonságai panelen található IP-címe
- $FILEPATH  
    Az exportálási útvonal az Azure Files-NetApp kötet
- $MOUNTPOINT  
    A könyvtár az NFS-exportálás csatlakoztatásához használt Linux-gazdagépen létrehozott

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Miért nem jelenik meg az NFS-ügyfél DF parancsát a létesített kötet méretének?

A kötet méretét, DF jelentett az Azure Files-NetApp kötet növekedhet maximális méretét. A DF parancs Azure NetApp fájlok kötetének mérete nem a kvóta vagy a kötet tükröző.  Az Azure NetApp kötet mérete vagy a kvóta az Azure Portalon vagy az API-t kaphat.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Milyen NFS-verzió támogatja az Azure Files-NetApp?

NetApp Azure Files jelenleg NFSv3 támogatja.

### <a name="how-do-i-enable-root-squashing"></a>Hogyan engedélyezhetem a legfelső szintű squashing?

Legfelső szintű squashing jelenleg nem támogatott.

## <a name="smb-faqs"></a>SMB – gyakori kérdések

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Támogatja az Azure Files-NetApp Azure Active Directory?

Nem, akkor jelenleg nem támogatott.  NetApp Azure Files támogatja az Active Directory Domain Services (használata a saját AD), amely az Azure Files-NetApp használhatja a meglévő Active Directory-tartományvezérlők. Tartományvezérlők találhatók az Azure-beli virtuális gépként, vagy a helyszínen expressroute-on keresztül.

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Az SMB-hozzáférés szükséges egy Active Directory-kapcsolatot? 

Igen, létre kell hoznia egy Active Directory-kapcsolatot az SMB-kötet üzembe helyezése előtt. A megadott tartományvezérlők Azure NetApp fájlok sikeres kapcsolat esetén a delegált alhálózat által elérhetőnek kell lennie.  Lásd: [az SMB-kötet létrehozása](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes#create-an-smb-volume) részleteiről. 

### <a name="how-many-active-directory-connections-are-supported"></a>Hány Active Directory-kapcsolatok támogatottak?

NetApp Azure Files jelenleg előfizetésenként az Active Directory kapcsolat létesítését támogatja. Az Active Directory-kapcsolatot is adott egyetlen NetApp számlához; nem osztja fiókok között. 

### <a name="what-versions-of-windows-ad-are-supported"></a>Windows AD melyik verziója támogatott?

NetApp Azure Files Active Directory tartományi szolgáltatások a Windows Server 2008r2SP1 – 2016-os verzióját támogatja.

## <a name="capacity-management-faqs"></a>Kapacitáskezelés – gyakori kérdések

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Hogyan figyelhetem a kapacitás a készlet és a kötet Azure NetApp fájlok használatát? 

NetApp Azure Files-készlet és a kötet kapacitás használati metrikák biztosít. Az Azure Monitor használatával figyelheti a NetApp Azure-fájlok. Lásd: [Azure NetApp fájlok metrikák](azure-netapp-files-metrics.md) részleteiről. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Azure Storage Explorer használatával Azure NetApp fájlok lehet kezelni?

Nem. NetApp Azure Files nem támogatja az Azure Storage Explorerben.

## <a name="data-migration-and-protection-faqs"></a>Áttelepítési és védelem – gyakori kérdések

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Hogyan migrálhatok adatokat az Azure Files-NetApp?
NetApp Azure Files NFS, SMB és kötetek biztosít.  Bármely fájl alapú copy eszköz segítségével telepítse át az adatokat a szolgáltatás. 

Egy SaaS-alapú megoldást kínál a NetApp [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  A megoldás lehetővé teszi, hogy replikálja az NFS, vagy SMB-adatok az Azure NetApp fájlok NFS exporthoz vagy SMB-fájlmegosztások. 

Ingyenes eszközök széles választékának használatával másolja az adatokat. Az NFS-, számítási feladatok eszközöket használhat például [rsync](https://rsync.samba.org/examples.html) másolása és a forrásadatok az Azure Files-NetApp köteten történő szinkronizálásához. Az SMB-, használhatja a számítási feladatok [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) azonos módon.  Ezekkel az eszközökkel is replikálhat a fájl vagy mappa engedélyeit. 

Történő adatmigrálás a helyszínen az Azure Files-NetApp követelményei a következők: 

- Győződjön meg arról, Azure NetApp fájlok a cél Azure-régióban érhető el.
- A forrás- és az Azure NetApp fájlok céloldali kötet IP-cím közötti hálózati kapcsolat ellenőrzése. Expressroute-on keresztül támogatott a helyi és a NetApp fájlok Azure service közötti adatátvitelt.
- Hozza létre a cél Azure NetApp fájlok kötet.
- Az adatok átvitele a célkötet az előnyben részesített fájlmásoló eszközt használatával.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Hogyan hozhatok létre egy másolatot az Azure Files-NetApp kötet egy másik Azure-régióban?
    
NetApp Azure Files NFS, SMB és kötetek biztosít.  Bármely alapú fájlmásoló eszközt az adatok replikálása az Azure-régiók között is használható. 

NetApp kínál a SaaS-alapú megoldás [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  A megoldás lehetővé teszi, hogy replikálja az NFS, vagy SMB-adatok az Azure NetApp fájlok NFS exporthoz vagy SMB-fájlmegosztások. 

Ingyenes eszközök széles választékának használatával másolja az adatokat. Az NFS-, számítási feladatok eszközöket használhat például [rsync](https://rsync.samba.org/examples.html) másolása és a forrásadatok az Azure Files-NetApp köteten történő szinkronizálásához. Az SMB-, használhatja a számítási feladatok [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) azonos módon.  Ezekkel az eszközökkel is replikálhat a fájl vagy mappa engedélyeit. 

Az Azure Files-NetApp kötet egy másik Azure-régióban való replikálásához követelményei a következők: 
- Győződjön meg arról, Azure NetApp fájlok a cél Azure-régióban érhető el.
- Minden egyes régióban található virtuális hálózatok közötti hálózati kapcsolat ellenőrzése. Virtuális hálózatok közötti társviszony globális jelenleg nem támogatott.  Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal a, vagy egy S2S VPN-kapcsolat használatával közötti kapcsolatot is létrehozhat. 
- Hozza létre a cél Azure NetApp fájlok kötet.
- Az adatok átvitele a célkötet az előnyben részesített fájlmásoló eszközt használatával.

### <a name="is-migration-with-azure-data-box-supported"></a>Az áttelepítés az Azure Data Box támogatott?

Nem. Az Azure Data Box nem támogatja az Azure Files-NetApp jelenleg. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Áttelepítés az Azure Import/Export szolgáltatással támogatott?

Nem. Az Azure Import/Export szolgáltatás nem támogatja az Azure Files-NetApp jelenleg.

## <a name="next-steps"></a>További lépések  

- [A Microsoft Azure ExpressRoute – gyakori kérdések](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [A Microsoft Azure Virtual Network – gyakori kérdések](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Azure-támogatáskérések létrehozása](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)