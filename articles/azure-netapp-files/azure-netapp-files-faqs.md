---
title: Gyakori kérdések a Azure NetApp Filesról | Microsoft Docs
description: Tekintse át a Azure NetApp Filesokkal kapcsolatos gyakori kérdéseket, például a hálózatkezelést, a biztonságot, a teljesítményt, a kapacitás-kezelést és az adatáttelepítést és-védelmet.
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 2a64e595f0ea07510f416be56a54a3c74294b95d
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653621"
---
# <a name="faqs-about-azure-netapp-files"></a>Gyakori kérdések a Azure NetApp Files

Ez a cikk a Azure NetApp Filesokkal kapcsolatos gyakori kérdéseket (GYIK) válaszol. 

## <a name="networking-faqs"></a>Hálózatkezelés – gyakori kérdések

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Az NFS-adatelérési út az interneten keresztül megy?  

Nem. Az NFS-adatelérési út nem az interneten keresztül halad. A Azure NetApp Files egy Azure-beli natív szolgáltatás, amely az Azure Virtual Network (VNet) szolgáltatásba kerül üzembe, ahol a szolgáltatás elérhető. A Azure NetApp Files delegált alhálózatot használ, és közvetlenül a VNet helyez üzembe egy hálózati adaptert. 

A részletekért tekintse [meg a Azure NetApp Files hálózati tervezésével kapcsolatos útmutatót](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) .  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Kapcsolódhat egy már létrehozott VNet a Azure NetApp Files szolgáltatáshoz?

Igen, összekapcsolhatók a szolgáltatáshoz létrehozott virtuális hálózatok. 

A részletekért tekintse [meg a Azure NetApp Files hálózati tervezésével kapcsolatos útmutatót](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) .  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Csatlakoztatható Azure NetApp Files NFS-kötet a DNS FQDN-név használatával?

Igen, ha létrehozza a szükséges DNS-bejegyzéseket. Azure NetApp Files megadja a kiépített kötet szolgáltatási IP-címét. 

> [!NOTE] 
> A Azure NetApp Files szükség szerint telepíthet további IP-címeket a szolgáltatáshoz.  Előfordulhat, hogy a DNS-bejegyzéseket rendszeresen frissíteni kell.

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Megadhatom vagy kiválaszthatom a saját IP-címüket egy Azure NetApp Files kötethez?  

Nem. A Azure NetApp Files kötetek IP-hozzárendelése dinamikus. A statikus IP-hozzárendelés nem támogatott. 

### <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Támogatja a Azure NetApp Files a kettős verem (IPv4 és IPv6) VNet?

Nem, Azure NetApp Files jelenleg nem támogatja a kettős verem (IPv4 és IPv6) VNet.  
 
## <a name="security-faqs"></a>Biztonsági GYIK

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Titkosítható az Azure-beli virtuális gép és a tároló közötti hálózati forgalom?

Az NFSv 4.1-ügyfelek és a Azure NetApp Files kötetek közötti adatforgalom titkosítható a Kerberos és az AES-256 titkosítás használatával. A részletekért lásd: [nfsv 4.1 Kerberos-titkosítás konfigurálása Azure NetApp Fileshoz](configure-kerberos-encryption.md) .   

A NFSv3 vagy a SMBv3-ügyfelek közötti adatforgalom nincs titkosítva Azure NetApp Files kötetre. Az Azure-beli virtuális gépekről (NFS-vagy SMB-ügyfelet futtató) érkező forgalom azonban Azure NetApp Files biztonságos, mint bármely más Azure-beli virtuális gép – virtuális gép közötti forgalom. Ez a forgalom helyi az Azure-beli adatközpont-hálózat számára. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Titkosítható a tároló a nyugalmi állapotban?

Az összes Azure NetApp Files kötet titkosítása az FIPS 140-2 szabvány használatával történik. Az összes kulcsot a Azure NetApp Files szolgáltatás kezeli. 

### <a name="how-are-encryption-keys-managed"></a>Hogyan történik a titkosítási kulcsok kezelése? 

Azure NetApp Files felügyeletét a szolgáltatás kezeli. Minden kötethez létrejön egy egyedi XTS-AES-256 adattitkosítási kulcs. A titkosítási kulcs hierarchiája az összes kötet kulcsának titkosítására és védelemmel való ellátására szolgál. Ezeket a titkosítási kulcsokat a rendszer soha nem titkosítatlan formában jeleníti meg vagy jelenti. A rendszer azonnal törli a titkosítási kulcsokat a kötetek törlésekor.

Az Azure dedikált HSM-t használó, az ügyfél által felügyelt kulcsok (Bring Your Own Key) támogatása az USA keleti régiójában, az USA déli középső régiójában, az USA 2. nyugati régiójában és a US Gov Virginia régióban ellenőrzött módon érhető el. A következő címen kérhet hozzáférést: [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) . Mivel a kapacitás elérhetővé válik, a rendszer jóváhagyja a kérelmeket.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Megadhatom az NFS-exportálási házirend szabályait a Azure NetApp Files Service Mount-célhoz való hozzáférés szabályozásához?

Igen, legfeljebb öt szabályt állíthat be egyetlen NFS-exportálási házirendben.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Támogatja a Azure NetApp Files a hálózati biztonsági csoportokat?

Nem, jelenleg nem alkalmazhat hálózati biztonsági csoportokat a Azure NetApp Files meghatalmazott alhálózatára vagy a szolgáltatás által létrehozott hálózati adapterekre.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Használhatom az Azure IAM-t a Azure NetApp Files?

Igen, Azure NetApp Files támogatja az Azure IAM RBAC funkcióit.

## <a name="performance-faqs"></a>Gyakori kérdések a teljesítményről

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Mit kell tennem a Azure NetApp Files teljesítményének optimalizálásához vagy finomhangolásához?

A teljesítményre vonatkozó követelmények alapján a következő műveleteket hajthatja végre: 
- Győződjön meg arról, hogy a virtuális gép mérete megfelelő.
- A virtuális gép gyorsított hálózatkezelésének engedélyezése.
- Válassza ki a kapacitási készlet kívánt szolgáltatási szintjét és méretét.
- Hozzon létre egy kötetet a kapacitás és a teljesítmény kívánt kvótájának méretével.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Azure NetApp Files a Hogyan az átviteli sebesség-alapú szolgáltatási szinteket IOPS?

A MB/s IOPS a következő képlet használatával alakíthatja át:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Hogyan módosítja egy kötet szolgáltatási szintjét?

Megváltoztathatja egy meglévő kötet szolgáltatási szintjét úgy, hogy áthelyezi a kötetet egy másik, a kötethez használni kívánt [szolgáltatási szintre](azure-netapp-files-service-levels.md) . Lásd: [kötetek szolgáltatási szintjének dinamikus módosítása](dynamic-change-volume-service-level.md). 

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Hogyan monitor Azure NetApp Files teljesítmény?

A Azure NetApp Files a kötetek teljesítményének mérőszámait biztosítja. Azure Monitor a Azure NetApp Files használati metrikáinak figyelésére is használható.  A Azure NetApp Files teljesítmény-metrikáinak listáját a [Azure NetApp Files mérőszámai](azure-netapp-files-metrics.md) részben tekintheti meg.

### <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>Mi a Kerberos teljesítményére gyakorolt hatás a NFSv 4.1 rendszeren?

Lásd: a [Kerberos teljesítményére gyakorolt hatás a nfsv 4.1](configure-kerberos-encryption.md#kerberos_performance) -ben a nfsv 4.1 biztonsági lehetőségeivel, a tesztelt teljesítmény-vektorokkal és a várható teljesítmény hatásával kapcsolatban. 

## <a name="nfs-faqs"></a>NFS – gyakori kérdések

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Az Azure-beli virtuális gépek indításakor vagy újraindításakor egy kötetet szeretnék automatikusan csatlakoztatni.  Hogyan konfigurálja a gazdagépet az állandó NFS-kötetek számára?

Ahhoz, hogy egy NFS-kötet automatikusan csatlakoztatható legyen a virtuális gép indításakor vagy újraindításakor, adjon hozzá egy bejegyzést a `/etc/fstab` gazdagépen lévő fájlhoz. 

A részletekért lásd: [kötetek csatlakoztatása vagy leválasztása Windows vagy Linux rendszerű virtuális gépekhez](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) .  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Miért nem jelenik meg az NFS-ügyfél DF-parancsa a kiosztott kötet méretének?

A DF által jelentett kötet mérete a maximális méret, amelyet a Azure NetApp Files kötet képes növelni. A Azure NetApp Files kötet mérete a DF parancsban nem tükrözi a kötet kvótáját vagy méretét.  A Azure NetApp Files kötet méretét vagy kvótáját a Azure Portal vagy az API használatával szerezheti be.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Milyen NFS-verziót támogat Azure NetApp Files?

Azure NetApp Files támogatja a NFSv3 és a NFSv 4.1-es verziója. [A köteteket](azure-netapp-files-create-volumes.md) az NFS-verzióval is létrehozhatja. 

### <a name="how-do-i-enable-root-squashing"></a>Hogyan engedélyezi a gyökér leverését?

Megadhatja, hogy a főfiók hozzáférhet-e a kötethez, vagy sem a kötet exportálási házirendjének használatával. További részletek: az [exportálási házirend konfigurálása NFS-kötethez](azure-netapp-files-configure-export-policy.md) .

### <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>Használhatok-e több kötethez ugyanazt a elérési utat (mennyiségi létrehozási tokent)?

Igen, írhat. A fájl elérési útját azonban egy másik előfizetésben vagy egy másik régióban kell használni.   

Létrehozhat például egy nevű kötetet `vol1` . Ezután létrehoz egy másik kötetet is `vol1` , amelyet más kapacitású készletben, de ugyanabban az előfizetésben és régióban is hívnak. Ebben az esetben az azonos nevű kötet használata `vol1` hibát okoz. A fájl elérési útjának használatához a névnek egy másik régióban vagy előfizetésben kell lennie.

## <a name="smb-faqs"></a>SMB – gyakori kérdések

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Mely SMB-verziókat támogatja a Azure NetApp Files?

Azure NetApp Files támogatja az SMB 2,1 és az SMB 3,1 (amely az SMB 3,0 támogatását is támogatja).    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Az SMB-hozzáféréshez Active Directory kapcsolat szükséges? 

Igen, az SMB-kötetek telepítése előtt létre kell hoznia Active Directory-kapcsolatokat. A sikeres kapcsolatok eléréséhez a megadott tartományvezérlőknek a Azure NetApp Files delegált alhálózatának kell elérhetőnek lennie.  További részleteket az [SMB-kötet létrehozása](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) című témakörben talál. 

### <a name="how-many-active-directory-connections-are-supported"></a>Hány Active Directory-kapcsolat támogatott?

A Azure NetApp Files nem támogatja több Active Directory (AD) kapcsolat használatát egyetlen *régióban*, még akkor is, ha az ad-kapcsolatok különböző NetApp-fiókokban vannak. Egyetlen *előfizetéshez*azonban több ad-kapcsolat is tartozhat, ha az ad-kapcsolatok különböző régiókban találhatók. Ha egyetlen régióban több AD-kapcsolatra van szüksége, külön előfizetéseket is használhat. 

Az AD-kapcsolatok egy NetApp-fiókkal vannak konfigurálva; az AD-kapcsolatok csak a ben létrehozott NetApp-fiókon keresztül láthatók.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp Files támogatja Azure Active Directory? 

A [Azure Active Directory (ad) tartományi szolgáltatások](https://docs.microsoft.com/azure/active-directory-domain-services/overview) és a [Active Directory tartományi szolgáltatások (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) egyaránt támogatottak. A meglévő Active Directory tartományvezérlőket Azure NetApp Files használatával használhatja. A tartományvezérlők az Azure-ban virtuális gépekként, illetve ExpressRoute vagy S2S VPN-en keresztül is megtalálhatók a helyszínen. A Azure NetApp Files jelenleg nem támogatja az AD Joint [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) .

Ha a Azure NetApp Filest használja a Azure Active Directory Domain Services, a szervezeti egység elérési útja az `OU=AADDC Computers` Active Directory beállítása a NetApp-fiókhoz.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>A Windows Server Active Directory mely verziói támogatottak?

Azure NetApp Files támogatja a Active Directory tartományi szolgáltatások Windows Server 2008r2SP1-2019-es verzióját.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Miért nem jelenik meg a kiépített méret az SMB-ügyfélen elérhető szabad terület?

Az SMB-ügyfél által jelentett kötet mérete az a maximális méret, ameddig a Azure NetApp Files kötet képes növekedni. Az SMB-ügyfélen látható Azure NetApp Files kötet mérete nem tükrözi a kötet kvótáját vagy méretét. A Azure NetApp Files kötet méretét vagy kvótáját a Azure Portal vagy az API használatával szerezheti be.

<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="dual-protocol-faqs"></a>Dual-Protocol – gyakori kérdések

### <a name="i-tried-to-use-the-root-and-local-users-to-access-a-dual-protocol-volume-with-the-ntfs-security-style-on-a-unix-system-why-did-i-encounter-a-permission-denied-error"></a>Megpróbáltam a "root" és a local Users használatával hozzáférni egy kettős protokollú kötethez az NTFS biztonsági stílussal egy UNIX rendszeren. Miért történt "engedély megtagadva" hibaüzenet?   

Lásd: a [kettős protokollú kötetek hibáinak elhárítása](troubleshoot-dual-protocol-volumes.md) .

### <a name="when-i-try-to-create-a-dual-protocol-volume-why-does-the-creation-process-fail-with-the-error-failed-to-validate-ldap-configuration-try-again-after-correcting-ldap-configuration"></a>Amikor megpróbálok létrehozni egy kettős protokollú kötetet, miért nem sikerült a létrehozási folyamat végrehajtása a következő hibával: "az LDAP-konfiguráció ellenőrzése sikertelen volt, próbálkozzon újra az LDAP-konfiguráció javítása után"?  

Lásd: a [kettős protokollú kötetek hibáinak elhárítása](troubleshoot-dual-protocol-volumes.md) .

## <a name="capacity-management-faqs"></a>A kapacitások kezelésével kapcsolatos gyakori kérdések

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Hogyan figyelni a kapacitási készlet és a Azure NetApp Files mennyiségének használatát? 

A Azure NetApp Files kapacitási készletet és mennyiségi használati metrikákat biztosít. A Azure Monitor a Azure NetApp Files használatának figyelésére is használható. Részletekért tekintse [meg a Azure NetApp Files mérőszámait](azure-netapp-files-metrics.md) . 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Kezelhetem Azure NetApp Files a Azure Storage Explorer használatával?

Nem. Azure Storage Explorer nem támogatja a Azure NetApp Files.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Hogyan megállapítja, hogy egy könyvtár eléri-e a korlát méretét?

A `stat` parancs használatával megtekintheti, hogy egy címtár eléri-e a maximális méretkorlátot a könyvtár metaadatainak számára (320 MB).

320 MB-os könyvtár esetén a blokkok száma 655360, és minden blokk mérete 512 bájt.  (Ez a 320x1024x1024/512.)  

Példák:

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```


## <a name="data-migration-and-protection-faqs"></a>Adatáttelepítés és-védelem – gyakori kérdések

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Hogyan az adatáttelepítés Azure NetApp Filesre?
A Azure NetApp Files NFS-és SMB-köteteket biztosít.  Bármilyen fájl alapú másolási eszköz használatával áttelepítheti az adatátvitelt a szolgáltatásba. 

A NetApp SaaS-alapú megoldást kínál a [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service)szolgáltatással.  A megoldás lehetővé teszi az NFS-vagy SMB-alapú adatreplikálást Azure NetApp Files NFS-exportálás vagy SMB-megosztások számára. 

Az adatmásoláshoz az ingyenes eszközök széles választékát is használhatja. Az NFS esetében olyan számítási feladatokhoz használható eszközöket használhat, mint például az [rsync](https://rsync.samba.org/examples.html) a forrásadatok Azure NetApp Files kötetre másolásához és szinkronizálásához. Az SMB-hez hasonló módon használhat munkaterheléseket a [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) szolgáltatásban.  Ezek az eszközök a fájl-vagy mappaengedélyek replikálására is képesek. 

A helyszínről Azure NetApp Filesre történő adatáttelepítés követelményei a következők: 

- Ellenőrizze, Azure NetApp Files elérhető-e a cél Azure-régióban.
- Ellenőrizze a forrás és a Azure NetApp Files cél kötet IP-címe közötti hálózati kapcsolatot. A helyszíni és a Azure NetApp Files szolgáltatás közötti adatátvitel támogatott a ExpressRoute-en keresztül.
- Hozza létre a cél Azure NetApp Files kötetet.
- Vigye át a forrásadatokat a cél kötetre a kívánt fájlmásolás-eszköz használatával.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Hogyan másolatot készíteni egy Azure NetApp Files kötetről egy másik Azure-régióban?
    
A Azure NetApp Files NFS-és SMB-köteteket biztosít.  Bármilyen fájl alapú másolási eszköz használható az Azure-régiók közötti adatreplikálásra. 

A NetApp SaaS-alapú megoldást kínál a [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service)szolgáltatással.  A megoldás lehetővé teszi az NFS-vagy SMB-alapú adatreplikálást Azure NetApp Files NFS-exportálás vagy SMB-megosztások számára. 

Az adatmásoláshoz az ingyenes eszközök széles választékát is használhatja. Az NFS esetében olyan számítási feladatokhoz használható eszközöket használhat, mint például az [rsync](https://rsync.samba.org/examples.html) a forrásadatok Azure NetApp Files kötetre másolásához és szinkronizálásához. Az SMB-hez hasonló módon használhat munkaterheléseket a [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) szolgáltatásban.  Ezek az eszközök a fájl-vagy mappaengedélyek replikálására is képesek. 

A Azure NetApp Files kötetek egy másik Azure-régióba való replikálásának követelményei a következők: 
- Ellenőrizze, Azure NetApp Files elérhető-e a cél Azure-régióban.
- Ellenőrizze az egyes régiók virtuális hálózatok közötti hálózati kapcsolatot. Jelenleg a virtuális hálózatok közötti globális egyenrangúság nem támogatott.  Kapcsolatot létesíthet a virtuális hálózatok között egy ExpressRoute-áramkör vagy egy S2S VPN-kapcsolat használatával. 
- Hozza létre a cél Azure NetApp Files kötetet.
- Vigye át a forrásadatokat a cél kötetre a kívánt fájlmásolás-eszköz használatával.

### <a name="is-migration-with-azure-data-box-supported"></a>Azure Data Box támogatott a Migrálás?

Nem. A Azure Data Box jelenleg nem támogatja a Azure NetApp Files. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Támogatott az Azure import/export szolgáltatással való áttelepítés?

Nem. Az Azure import/export szolgáltatás jelenleg nem támogatja a Azure NetApp Files.

## <a name="next-steps"></a>Következő lépések  

- [Microsoft Azure ExpressRoute GYIK](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Microsoft Azure Virtual Network GYIK](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Azure-támogatáskérések létrehozása](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox)
- [A Azure NetApp Files SMB-teljesítményével kapcsolatos gyakori kérdések](azure-netapp-files-smb-performance.md)
