---
title: VMware-elemzésekhez és migráláshoz az Azure Migrate támogatási mátrix
description: Támogatási beállításainak és az elemzésekhez és migráláshoz az Azure-bA az Azure Migrate szolgáltatás használatával a VMware virtuális gépek korlátozások foglalja össze.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: raynew
ms.openlocfilehash: 567a6582e193208a7ff4aa37bafefe1dec4f4e8f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811582"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>VMware-elemzésekhez és migráláshoz támogatási mátrix

Használhatja a [Azure Migrate szolgáltatás](migrate-overview.md) segítségével mérheti fel, és a gépek áttelepítése a Microsoft Azure felhőbe. Ez a cikk a támogatási beállításait és korlátozásai és migrálnak a helyszíni VMware virtuális gépeket foglalja össze.


## <a name="vmware-scenarios"></a>VMware-forgatókönyvek

A táblázat összefoglalja a támogatott forgatókönyveket a VMware virtuális gépekhez.

**Üzembe helyezés** | **Részletek** 
--- | --- 
**A helyszíni VMware virtuális gépek értékelése** | [Állítsa be a](tutorial-prepare-vmware.md) az első értékelést.<br/><br/> [Futtatás](scale-vmware-assessment.md) a nagy léptékű értékelést.
**VMware virtuális gépek áttelepítése** | Áttelepítés ügynök nélküli migrálást, csak bizonyos korlátozásokkal, vagy egy ügynök-alapú áttelepítést használ. [További információ](server-migrate-overview.md)


    


## <a name="azure-migrate-projects"></a>Az Azure Migrate-projekt

**Támogatás** | **Részletek**
--- | ---
Azure-engedélyeket | Az Azure Migrate-projekt létrehozása az előfizetés közreműködő vagy tulajdonos engedélyek szükségesek.
VMware-korlátozások  | Mérje fel, akár 35,000 VMware virtuális gépek, egyetlen projekt.

A projekt tartalmazhatnak VMware virtuális gépek és a Hyper-V virtuális gépek, egészen az értékelés korlátjáig.

## <a name="assessment-vmware-server-requirements"></a>Értékelés – VMware-kiszolgáló követelményei

Ez a táblázat összefoglalja az értékelés támogatása és a VMware virtualizálási kiszolgálók korlátozásai.

**Támogatás** | **Részletek**
--- | ---
**vCenter server** | VMware virtuális gépek az értékelni kívánt egy vagy több vCenter-kiszolgálók egy 5.5-ös, 6.0-s, 6.5-ös vagy 6.7 kell felügyelnie.

## <a name="assessment-vcenter-server-permissions"></a>Értékelés-vCenter Server engedélyei

Értékelés csak szüksége van egy csak olvasható fiók a vcenter-kiszolgálóhoz.

## <a name="assessment-appliance-requirements"></a>Értékelés-berendezés követelmények

**Támogatás** | **Részletek**
--- | ---
**ESXi** | A berendezés virtuális Gépet egy 5.5-ös vagy újabb verzióját futtató ESXi-gazdagépen telepíteni kell.
**Az Azure Migrate-projekt** | Egy készülék egyetlen projekt társítható.
**vCenter Server** | Egy készülék felfedezheti a vCenter-kiszolgáló legfeljebb 10 000 VMware virtuális gépeket.<br/> Egy készülék egy vCenter-kiszolgálóhoz csatlakozhat.


## <a name="assessment-url-access-requirements"></a>Értékelési URL-címet a hozzáférési követelmények

Az Azure Migrate berendezés az internethez internetkapcsolattal kell rendelkeznie.

- A berendezés telepítésekor az Azure Migrate nem egy kapcsolat ellenőrzése az URL-címeket az alábbi táblázat foglalja össze.
- Egy URL-alapú firewall.proxy használja, ha engedélyezi a hozzáférést ezen URL-EK, megakadályozhatja, hogy a proxy oldja fel az URL-címek keresése során kapott minden olyan CNAME-rekordokat.

**URL-cím** | **Részletek**  
--- | --- |
*.portal.azure.com  | Keresse meg az Azure Migrate az Azure Portalon.
*.windows.net | Jelentkezzen be az Azure-előfizetésébe.
*.microsoftonline.com | Active Directory-alkalmazások az Azure Migrate szolgáltatással való kommunikációra a berendezés létrehozásához.
management.azure.com | Active Directory-alkalmazások az Azure Migrate szolgáltatással való kommunikációra a berendezés létrehozásához.
dc.services.visualstudio.com | Belső a figyeléshez használt alkalmazás-naplók feltöltése.
*.vault.azure.net | Az Azure Key Vault titkos kulcsainak kezelése.
*.servicebus.windows.net | A berendezés és az Azure Migrate szolgáltatás közötti kommunikáció során.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Csatlakozás az Azure Migrate szolgáltatás URL-címeit.
*.blob.core.windows.net | Adatok feltöltése a storage-fiókok.


## <a name="assessment-port-requirements"></a>Értékelés-port követelményei

**Device** | **kapcsolat**
--- | --- 
Készülék | A berendezés távoli asztali kapcsolatok engedélyezése a 3389-es TCP-porton bejövő kapcsolatokat.<br/> Bejövő kapcsolatok 44368 távoli eléréséhez a készülék felügyeleti alkalmazás, az URL-porton: https://<appliance-ip-or-name>:44368 <br/>Az Azure Migrate felderítési és teljesítményére vonatkozó metaadatok küldendő 443-as porton a kimenő kapcsolatokat.
vCenter-kiszolgáló | Bejövő kapcsolatok, hogy a berendezés konfigurációs és teljesítményére vonatkozó metaadatok értékelések gyűjtése a 443-as TCP-porton. <br/> A berendezés alapértelmezés szerint a 443-as port a vCenter csatlakozik. Ha a vCenter-kiszolgáló egy másik porton figyel, módosíthatja a port, felderítési beállításakor.


## <a name="agentless-migration-vmware-server-requirements"></a>Ügynök nélküli kivételfigyelés áttelepítési – VMware-kiszolgáló követelményei

Ez a táblázat összefoglalja az értékelés támogatása és a VMware virtualizálási kiszolgálók korlátozásai.

**Támogatás** | **Részletek**
--- | ---
**vCenter server** | VMware virtuális gépeket telepít át, az ügynök nélküli áttelepítéssel legalább egy vCenter-kiszolgálók egy 5.5-ös, 6.0-s, 6.5-ös vagy 6.7 kell felügyelnie.

## <a name="agentless-migration-vcenter-server-permissions"></a>Ügynök nélküli kivételfigyelés áttelepítési-vCenter Server engedélyei

**Engedélyek** | **Részletek**
--- | --- 
Datastore.Browse | Lehetővé teszi a böngészés a virtuális gép naplófájlok hibaelhárítása a pillanatkép létrehozását és törlését.
Datastore.LowLevelFileOperations | Átnevezése olvasási/írási és törlési műveletek engedélyezése a adattárolója böngészőben hibaelhárítása a pillanatkép létrehozását és törlését.
VirtualMachine.Configuration.DiskChangeTracking | Enable engedélyezésére vagy letiltására szolgál a change tracking a Virtuálisgép-lemezek, a módosult blokkokat-pillanatképek közötti adatok lekérése
VirtualMachine.Configuration.DiskLease | Engedélyezi a lemez bérleti műveletek egy virtuális géphez, olvassa el a lemezt, a VMware vSphere virtuális lemez Development Kit (VDDK) használatával.
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | Lehetővé teszi egy lemezt a virtuális gép, olvassa el a lemezt a VDDK használatával való megnyitásakor.
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | Lehetővé teszi az olvasási műveletek egy virtuális Gépet, hogy hiba esetén a naplók letöltéséhez és társított fájlokat. 
VirtualMachine.SnapshotManagement.* | Lehetővé teszi a virtuális gép replikációs pillanatképeinek-létrehozása és kezelése. 
Virtuális Machine.Interaction.Power kikapcsolása | A virtuális gép az Azure-ba való migráláskor kikapcsolásának engedélyezése.


## <a name="agentless-migration-vmware-vm-requirements"></a>Ügynök nélküli kivételfigyelés áttelepítési – VMware Virtuálisgép-követelmények

**Támogatás** | **Részletek**
--- | ---
**Támogatott operációs rendszerek** | [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) és [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) Azure által támogatott operációs rendszerek ügynök nélküli kivételfigyelés áttelepítés használatával telepíthetők át.
**Az Azure-hoz szükséges módosításokat** | Néhány virtuális gépet módosításait igénylik, hogy az Azure rendszerben futtatnak. Az Azure Migrate szolgáltatással automatikusan a következő operációs rendszerekhez ezeket a módosításokat:<br/> – A Red Hat Enterprise Linux 6.5-ös + 7.0 +<br/> - CentOS 6.5+, 7.0+</br> – SUSE Linux Enterprise Server 12 SP1 +<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7,8<br/><br/> Más operációs rendszerek esetén hajtsa végre az esetlegesen manuálisan az áttelepítés előtt kell. Az ide tartozó cikkekre ehhez útmutatást tartalmaz.
**Linux-rendszerindítás** | Ha gyökérpartíció dedikált partíción, az operációsrendszer-lemez a kell tárolni, és nem oszlanak meg több lemezre kiterjedő.<br/> Gyökérpartíció része a gyökér (/) partíció, ha ezután a "/" partíció kell az operációsrendszer-lemez lehet, és nem terjed ki a többi lemezen.
**UEFI-rendszerindítás** | Az UEFI-rendszerindítást virtuális gépek nem telepíthetők át.
**Titkosított lemezek vagy kötetek** | Titkosított lemezek vagy kötetek rendelkező virtuális gépek áttelepítése nem támogatott.
**RDM-/ lemezei** | Ha a virtuális gép RDM vagy csatlakoztatott lemezzel rendelkezik, ezeket a lemezeket az Azure-ba nem replikálható.
**NFS** | NFS-kötetek kötetek fürtkötetként a virtuális gépeken nem replikálható.
**Céllemez** | Virtuális gépek csak a managed Disks szolgáltatásba (standard HHD, prémium szintű SSD) az Azure-ban telepíthető át.


## <a name="agentless-migration-appliance-requirements"></a>Ügynök nélküli kivételfigyelés áttelepítési-berendezés követelmények


**Támogatás** | **Részletek**
--- | ---
**ESXi** | A berendezés virtuális Gépet egy 5.5-ös vagy újabb verzióját futtató ESXi-gazdagépen telepíteni kell.
**Az Azure Migrate-projekt** | Egy készülék egyetlen projekt társítható.
**vCenter Server** | Egy készülék felfedezheti a vCenter-kiszolgáló legfeljebb 10 000 VMware virtuális gépeket.<br/> Egy készülék egy vCenter-kiszolgálóhoz csatlakozhat.
**VDDK** | Ha egy ügynök nélküli migrálást az Azure Migrate-kiszolgáló áttelepítése futtat, a VMware vSphere virtuális lemez Development Kit (VDDK) kell telepíteni a virtuális berendezés.

## <a name="agentless-migration-url-access-requirements"></a>Ügynök nélküli kivételfigyelés áttelepítés-URL-Címének a hozzáférési követelmények

Az Azure Migrate berendezés az internethez internetkapcsolattal kell rendelkeznie.

- A berendezés telepítésekor az Azure Migrate nem egy kapcsolat ellenőrzése az URL-címeket az alábbi táblázat foglalja össze.
- Egy URL-alapú firewall.proxy használja, ha engedélyezi a hozzáférést ezen URL-EK, megakadályozhatja, hogy a proxy oldja fel az URL-címek keresése során kapott minden olyan CNAME-rekordokat.

**URL-cím** | **Részletek**  
--- | --- 
*.portal.azure.com | Keresse meg az Azure Migrate az Azure Portalon.
*.windows.net | Jelentkezzen be az Azure-előfizetésébe.
*.microsoftonline.com | Active Directory-alkalmazások az Azure Migrate szolgáltatással való kommunikációra a berendezés létrehozásához.
management.azure.com | Active Directory-alkalmazások az Azure Migrate szolgáltatással való kommunikációra a berendezés létrehozásához.
dc.services.visualstudio.com | Belső a figyeléshez használt alkalmazás-naplók feltöltése.
*.vault.azure.net | Az Azure Key Vault titkos kulcsainak kezelése.
*.servicebus.windows.net | A berendezés és az Azure Migrate szolgáltatás közötti kommunikáció során.
*.discoverysrv.windowsazure.com<br/> *.migration.windowsazure.com<br/> *.hypervrecoverymanager.windowsazure.com | Csatlakozás az Azure Migrate szolgáltatás URL-címeit.
*.blob.core.windows.net | Adatok feltöltése a storage-fiókok.


## <a name="agentless-migration-port-requirements"></a>Ügynök nélküli kivételfigyelés áttelepítési-port követelményei

**Device** | **kapcsolat**
--- | --- 
Készülék | Kimenő TCP-port 3389-es, az Azure-bA replikált adatok feltöltése és a replikáció és migrálás az Azure Migrate folytatott kommunikációhoz.
vCenter-kiszolgáló | Koordinálhatja a replikálást – hozzon létre pillanatképek, az adatok másolása, a készülék engedélyezése a 443-as TCP-porton bejövő kapcsolatokat kiadási pillanatképek
a vSphere-/ ESXI-gazdagép | Bejövő pillanatképekből replikálja az adatokat a berendezés 902-es TCP-porton. 


## <a name="agent-based-migration-vmware-server-requirements"></a>Ügynök-alapú áttelepítés – VMware-kiszolgáló követelményei

Ez a táblázat összefoglalja az értékelés támogatása és a VMware virtualizálási kiszolgálók korlátozásai.

**Támogatás** | **Részletek**
--- | ---
**vCenter kiszolgálóhoz/ESXI** | VMware virtuális gépeket telepít át egy vagy több vCenter-kiszolgálók egy 5.5-ös, 6.0-s, 6.5-ös vagy 6.7 fut, vagy egy 5.5-ös, 6.0-s, 6.5-ös vagy 6.7 vSphere-verzió az ESXI-gazdagépen futó kell felügyelnie.

### <a name="agent-based-migration-vcenter-server-permissions"></a>Az ügynök-alapú áttelepítés-vCenter Server engedélyei

**Engedélyek** | **Részletek**
--- | --- 
Datastore.AllocateSpace | Engedélyezze a lemezterület-foglalás egy adattár a virtuális gép esetén pillanatkép, a Klónozás vagy a virtuális lemezt. 
Datastore.Browse | Lehetővé teszi a böngészés a virtuális gép naplófájlok hibaelhárítása a pillanatkép létrehozását és törlését.
Datastore.LowLevelFileOperations | Lehetővé teszi az olvasási, írási, törlése és átnevezése műveletek hibaelhárítása a pillanatkép létrehozása vagy törlése a adattárolója böngészőben.
Datastore.UpdateVirtualMachineFiles | Lehetővé teszik az elérési utak frissítése után az adattárhoz resignatured egy adattár a virtuális gép fájljait.
Network.AssignNetwork | Lehetővé teszi, hogy a hálózat hozzárendelése egy VM-erőforrás.
AssignVirtualMachineToResourcePool | A virtuális gépek erőforráskészlethez hozzárendelés engedélyezése.
Resource.MigratePoweredOffVirtualMachine | Lehetővé teszik egy kikapcsolt virtuális gép egy másik erőforráskészlet vagy a gazdagépen való áttelepítését.
Resource.MigratePoweredOnVirtualMachine | Áttelepítés egy bekapcsolt virtuális gép egy másik erőforráskészlet vagy a gazdagép a vMotion használatával teszi lehetővé.
Tasks.CreateTask | Felhasználó által definiált feladat létrehozása egy bővítmény engedélyezése.
Tasks.UpdateTask | Egy bővítmény frissíteni egy felhasználó által definiált feladat engedélyezése.
VirtualMachine.Configuration. | Lehetővé teszi a virtuális gép lehetőségek és eszközök konfigurálása.
Virtual Machine.Interaction.AnswerQuestion | Elháríthatja azokat a problémákat a virtuális gép állapotváltásra vagy a futásidejű hibák engedélyezése.
Virtual Machine.Interaction.DeviceConnection | Lehetővé teszi a csatlakoztatott virtuális gép kikapcsolt virtuális eszközök állapotának módosítása.
Virtual Machine.Interaction.ConfigureCDMedia | A virtuális DVD-ről vagy CD-ROM eszköz konfigurálását teszik lehetővé.
Virtual Machine.Interaction.ConfigureFloppyMedia | Virtuális hajlékonylemez eszköz konfigurálását teszik lehetővé.
Virtual Machine.Interaction.PowerOff | Lehetővé teszi, hogy a virtuális gép van kapcsolva az Azure-ba való migráláskor.
Virtual Machine.Interaction.PowerOn | Kikapcsolt virtuális gép bekapcsolása, és a egy felfüggesztett virtuális gép folytatása engedélyezése.
Virtual Machine.Interaction.VMwareToolsInstall | Csatlakoztatja, és a vendég operációs rendszer CD-ROM-ról, a VMware Tools CD telepítő shellről engedélyezése.
VirtualMachine.Inventory.CreateNew | Lehetővé teszi a virtuális gépek létrehozása és a szükséges erőforrások.
VirtualMachine.Inventory.Register | Egy meglévő virtuális gép vCenter-kiszolgáló vagy a gazdagép inventory felvételének engedélyezése.
VirtualMachine.Inventory.Unregister | Lehetővé teszi egy VMe a vCenter-kiszolgáló vagy a gazdagép készlet regisztrációjának törlése.
VirtualMachine.Provisioning.AllowVirtualMachineFilesUpload | Lehetővé teszi az írási műveletek egy virtuális Gépet, beleértve a vmx, lemezek, naplók és nvram társított fájlokat.
VirtualMachine.Provisioning.AllowVirtualMachineDownload | Lehetővé teszi az olvasási műveletek egy virtuális Gépet, a hibaelhárítási naplók letöltéséhez társított fájlokat.
VirtualMachine.SnapshotManagement.RemoveSnapshot | Egy pillanatképet a pillanatkép előzményekből eltávolításának engedélyezése.

## <a name="agent-based-migration-replication-appliance-requirements"></a>Az ügynök-alapú áttelepítés-replikáció készülék követelmények

A követelményei a [replikációs berendezés](migrate-replication-appliance.md) használatos az ügynök-alapú áttelepítés VMware virtuális gépek és fizikai kiszolgálók Azure Migrálása kiszolgáló áttelepítése a táblázat foglalja össze.

> [!NOTE]
> A replikációs készülék az Azure Migrate hub megadott OVA sablonnal beállításakor a a készülék Windows Server 2016 fut, és megfelel a támogatási követelményeinek. A replikációs készülék a fizikai kiszolgálón manuálisan állít be, majd győződjön meg arról, hogy megfelel a követelményeknek.



**Összetevő** | **Követelmény** 
--- | ---
 | **VMware-beállítások** (VMware VM-készülék)
**PowerCLI** | [A PowerCLI 6.0-s verzió](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) kell telepíteni, ha a replikáció berendezés VMware virtuális gép fut-e.
**Hálózati adapter típusa** | VMXNET3 (Ha a készülék VMware virtuális gép)
 | **Hardverbeállítások** 
Processzormagok | 8 
RAM | 16 GB
Lemezek száma | Három: Az operációs rendszer lemez folyamatkiszolgálói gyorsítótárlemez és adatmegőrzési meghajtó.
Szabad lemezterület (gyorsítótár) | 600 GB
Szabad területe (adatmegőrzési lemez) | 600 GB
**Szoftverfrissítési beállítások** | 
Operációs rendszer | A Windows Server 2016 vagy Windows Server 2012 R2 rendszerben
Operációs rendszer területi beállítása | Angol (en-us)
TLS | A TLS 1.2 engedélyezni kell.
.NET-keretrendszer | .NET-keretrendszer 4.6-os vagy újabb verzióját kell telepítenie kell a gép (az erős titkosítás engedélyezve van.
MySQL | A berendezés MySQL kell telepíteni.<br/> MySQL kell telepíteni. Manuálisan is telepítheti, vagy a készülék üzembe helyezése során a Site Recovery is telepítheti. 
Más alkalmazások | Más alkalmazás ne futtassa a replikáció berendezésen.
Windows Server-szerepkörök | Ezek a szerepkörök nem engedélyezi: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V 
Csoportházirendek | Ezek a szabályzatok csoport nem engedélyezi: <br> -Hozzáférés megakadályozása a parancssorba. <br> -A beállításjegyzék szerkesztőeszközeihez való hozzáférés letiltása. <br> -Megbízhatósági logika fájlmellékletekhez. <br> – Kapcsolja be a parancsfájl végrehajtása. <br> [További információ](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | – Nincs már meglévő alapértelmezett webhelye <br> – Nincs már létező webhely vagy alkalmazás 443-as porton <br>-Engedélyezése [a névtelen hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Engedélyezése [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) beállítás 
**Hálózati beállítások** | 
IP-cím típusa | Statikus 
Portok | 443 (vezérlőcsatorna-vezénylés)<br>9443 (Adatátvitel) 
Hálózati adapter típusa | VMXNET3 

### <a name="replication-appliance-url-access"></a>Replikációs készülék URL-hozzáférés

A replikációs berendezés az URL-címet hozzá kell férnie.

**URL-cím** | **Részletek**
--- | ---
\*.backup.windowsazure.com | A replikált adatok átvitel és összehangoláshoz
\*.store.core.windows.net | A replikált adatok átvitel és összehangoláshoz
\*.blob.core.windows.net | A replikált adatokat tároló tárfiók eléréséhez használt
\*.hypervrecoverymanager.windowsazure.com | Replikációkezelési műveletekhez és összehangoláshoz
https:\//management.azure.com | Replikációkezelési műveletekhez és összehangoláshoz 
*.services.visualstudio.com | Telemetria célra (nem kötelező)
time.nist.gov | A rendszer és a globális idő közötti időszinkronizálás ellenőrzéséhez.
time.windows.com | A rendszer és a globális idő közötti időszinkronizálás ellenőrzéséhez.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | OVF-telepítő a következő URL-címekhez hozzá kell férnie. Hozzáférés-vezérlés és az identitás használják az Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | MySQL letöltéshez


#### <a name="mysql-installation-options"></a>MySQL-telepítési beállítások

MySQL, a replikáció berendezés, ezek a módszerek egyikének használatával is telepíthető.

**Telepítés** | **Részletek**
--- | ---
Manuális letöltés és telepítés | Töltse le a MySQL-alkalmazás & helyezze azokat a C:\Temp\ASRSetup mappájába, majd manuálisan telepíti.<br/> Ha beállította a készülék MySQL jelennek meg, már telepítve van. 
Online nem letöltése | Helyezze el a MySQL-telepítő alkalmazás C:\Temp\ASRSetup mappában. A berendezés telepítésekor, és kattintson a MySQL letöltéséhez és telepítéséhez, a telepítő a hozzáadott telepítő használja. 
Az Azure-ból letölthető áttelepítése | A berendezés telepítésekor, és MySQL-hez készült megerősítését, válassza ki a **töltse le és telepítse**.



## <a name="agent-based-migration-vmware-vm-requirements"></a>Migrálás – VMware-alapú ügynök Virtuálisgép-követelmények

**Támogatás** | **Részletek**
--- | ---
**Gépeken futó számítási feladatokhoz** | Az Azure Migrate támogatja az áttelepítést, bármely számítási feladat (például: az Active Directory, az SQL server stb.) egy támogatott gépen futó.
**Operációs rendszerek** | A legfrissebb információkért tekintse át a [operációs rendszer támogatásának](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) Site Recovery. Az Azure Migrate biztosít azonos virtuális gép operációs rendszerének támogatása.
**Linux rendszer/Vendég fájltárolás** | A legfrissebb információkért tekintse át a [fájlrendszer Linux-támogatás](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) Site Recovery. Az Azure Migrate rendelkezik azonos Linux fájlrendszer-támogatás.
**Hálózat és tárolás** | A legfrissebb információkért tekintse át a [hálózati](../site-recovery/vmware-physical-azure-support-matrix.md#network) és [tárolási](../site-recovery/vmware-physical-azure-support-matrix.md#storage) a Site Recovery előfeltételei. Az Azure Migrate azonos hálózati vagy tárolási követelményeit ismerteti.
**Azure-követelmények** | A legfrissebb információkért tekintse át a [Azure-beli hálózati](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [tárolási](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage), és [számítási](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) a Site Recovery követelményeinek. Az Azure Migrate azonos követelményei a VMware-migrálás.
**Mobilitási szolgáltatás** | A mobilitási szolgáltatás ügynökének telepítenie kell az egyes virtuális Gépeken, amelyeket szeretné áttelepíteni.
**Céllemez** | Virtuális gépek csak a managed Disks szolgáltatásba (standard HHD, prémium szintű SSD) az Azure-ban telepíthető át.

   

## <a name="agent-based-migration-url-access-requirements"></a>Az ügynök-alapú áttelepítés URL-címet a hozzáférési követelmények

A mobilitási szolgáltatás VMware virtuális gépeken futó Internet internetkapcsolattal kell rendelkeznie.

- Amikor telepíti a mobilitási szolgáltatást, így a kapcsolat ellenőrzése az URL-címeket az alábbi táblázatban összefoglalt nem.
- Egy URL-alapú firewall.proxy használja, ha engedélyezi a hozzáférést ezen URL-EK, megakadályozhatja, hogy a proxy oldja fel az URL-címek keresése során kapott minden olyan CNAME-rekordokat.

**URL-cím** | **Részletek**  
--- | --- 
*.portal.azure.com | Keresse meg az Azure Migrate az Azure Portalon.
*.windows.net | Jelentkezzen be az Azure-előfizetésébe.
*.microsoftonline.com | Active Directory-alkalmazások az Azure Migrate szolgáltatással való kommunikációra a berendezés létrehozásához. 
management.azure.com | Active Directory-alkalmazások az Azure Migrate szolgáltatással való kommunikációra a berendezés létrehozásához.
dc.services.visualstudio.com | Belső a figyeléshez használt alkalmazás-naplók feltöltése.
*.vault.azure.net | Az Azure Key Vault titkos kulcsainak kezelése.
*.servicebus.windows.net | A berendezés és az Azure Migrate szolgáltatás közötti kommunikáció során.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Csatlakozás az Azure Migrate szolgáltatás URL-címeit.
*.blob.core.windows.net | Adatok feltöltése a storage-fiókok.

## <a name="agent-based-migration-port-requirements"></a>Az ügynök-alapú áttelepítés-port követelményei

**Device** | **kapcsolat**
--- | --- 
Virtuális gépek | A virtuális gépeken futó mobilitási szolgáltatás kommunikál a helyszíni konfigurációs kiszolgálót HTTPS a 443-as porton bejövő, a replikáció kezelését.<br/><br/> Virtuális gépek a replikációs adatokat a folyamatkiszolgálónak (a konfigurációs kiszolgáló gépen futó) HTTPS 9443-as porton bejövő küldése. Ez a port módosítható.
Replikációs készülék | A replikációs berendezés koordinálja a replikálás az Azure-ral HTTPS 443-as kimenő porton keresztül.
Folyamatkiszolgáló | A folyamatkiszolgáló fogadja a replikált adatokat, optimalizálja a és titkosítja azokat, és elküldi azt az Azure storage 443-as porton keresztüli kimenő.<br/> Alapértelmezés szerint a folyamatkiszolgáló fut, a replikáció berendezésen.

## <a name="azure-vm-requirements"></a>Azure Virtuálisgép-követelmények

Az összes helyszíni virtuális gépek Azure-bA replikált meg kell felelnie az Azure virtuális gépekre vonatkozó, az alábbi táblázatban foglaltak. Előfeltételek ellenőrzi a replikációs futtatásakor a Site Recovery az ellenőrzés sikertelen lesz, ha az egyes követelmények nem teljesülnek.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Vendég operációs rendszer | Ellenőrizze a támogatott operációs rendszerek [VMware virtuális gépek a replikációs ügynök nélküli kivételfigyelés](#agentless-migration-vmware-vm-requirements), és a [VMware virtuális gépek a replikációs ügynök-alapú](#agent-based-migration-vmware-vm-requirements).<br/> Áttelepítheti a támogatott operációs rendszeren futó bármilyen számítási feladat. | Az ellenőrzés sikertelen, ha nem támogatott.
Vendég operációs rendszer architektúrája | 64 bites. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációsrendszer-lemez mérete | Legfeljebb 2048 GB-ig. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációsrendszer-lemezek száma | 1 | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemezek száma | 64 vagy kevesebb. | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemez mérete | Legfeljebb 4095 GB-ig | Az ellenőrzés sikertelen, ha nem támogatott.
Hálózati adapterek | Több adapter támogatott. | 
Megosztott VHD | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
FC-lemez | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
BitLocker | Nem támogatott. | A BitLocker az adott gép replikálását engedélyezése előtt le kell tiltani. 
a virtuális gép neve | 1 és 63 karakternél.<br/> Csak betűket, számokat és kötőjelet tartalmazhat.<br/><br/> A gép nevét kell kezdődnie, és betűvel vagy számmal végződhet. |  Frissítse az értéket a Site Recovery virtuálisgép-tulajdonságokat.
Kapcsolódás után az áttelepítési – Windows | Csatlakozás az Azure virtuális gépeken futó Windows áttelepítés után:<br/> -Áttelepítés előtt engedélyezze az RDP a helyszíni virtuális gépen. Ellenőrizze, hogy a **Nyilvános** profilnál felvette-e a listára a TCP- és UDP-szabályokat, valamint hogy a **Windows-tűzfal** > **Engedélyezett alkalmazások** területén az összes profil számára engedélyezve van-e az RDP.<br/> A site-to-site VPN eléréséhez, engedélyezze az RDP és az RDP engedélyezése **Windows tűzfal** -> **engedélyezett alkalmazások és szolgáltatások** a **tartomány és privát** hálózatok. Emellett ellenőrizze, hogy az operációs rendszer TÁROLÓHÁLÓZATI szabályzatát állítsa **OnlineAll**. [További információk](https://support.microsoft.com/kb/3031135). | 
Kapcsolódás után az áttelepítési – Linux | Csatlakozás az Azure virtuális gépek SSH-val az áttelepítés után:<br/> A helyszíni gépen az áttelepítés előtt ellenőrizze, hogy a Secure Shell szolgáltatás kezdési értéke, és, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.<br/> A feladatátvételt követően az Azure virtuális gépen, az SSH-port, a hálózati biztonsági csoport szabályai a feladatait átadó virtuális gép és az Azure-alhálózaton, amelyhez csatlakoztatva van a bejövő kapcsolatok engedélyezése. Emellett adja hozzá a virtuális gép nyilvános IP-címet. |  


## <a name="next-steps"></a>További lépések

[A VMware előkészítése](tutorial-prepare-vmware.md) elemzésekhez és migráláshoz.








