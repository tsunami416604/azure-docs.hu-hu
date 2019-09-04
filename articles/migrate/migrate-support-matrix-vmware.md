---
title: Azure Migrate támogatási mátrix a VMware értékeléséhez és áttelepítéséhez
description: Összefoglalja a VMware virtuális gépek Azure-ba történő értékelésére és áttelepítésére vonatkozó támogatási beállításokat és korlátozásokat az Azure Migrate szolgáltatással.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: raynew
ms.openlocfilehash: 7fe2c39871f1cd512da7f9a2c5146e79abbe74a6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279600"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>A VMware felmérésének és migrálásának támogatási mátrixa

A [Azure Migrate](migrate-overview.md) használatával a gépek felmérhetők és áttelepíthetők a Microsoft Azure felhőbe. Ez a cikk a helyszíni VMware virtuális gépek felmérésére és áttelepítésére vonatkozó támogatási beállításokat és korlátozásokat foglalja össze.


## <a name="vmware-scenarios"></a>VMware-forgatókönyvek

A táblázat összefoglalja a VMware virtuális gépek támogatott forgatókönyveit.

**Üzembe helyezés** | **Részletek**
--- | ---
**Helyszíni VMware virtuális gépek felmérése** | [Állítsa be](tutorial-prepare-vmware.md) az első értékelést.<br/><br/> Nagy léptékű értékelés [futtatása](scale-vmware-assessment.md) .
**VMware virtuális gépek migrálása** | Az áttelepítést ügynök nélküli áttelepítés használatával vagy ügynök-alapú áttelepítés használatával végezheti el. [További információ](server-migrate-overview.md)


## <a name="azure-migrate-projects"></a>Azure Migrate projektek

**Támogatás** | **Részletek**
--- | ---
**Azure-engedélyek** | Azure Migrate projekt létrehozásához közreműködői vagy tulajdonosi engedélyekkel kell rendelkeznie az előfizetésben.
**VMware-korlátozások**  | Egyetlen projektben akár 35 000 VMware virtuális gépet is megvizsgálhat. Egy Azure-előfizetésben több projektet is létrehozhat. A projektek tartalmazhatják a VMware virtuális gépeket és a Hyper-V virtuális gépeket is, az értékelési korlátokig.
**Régiócsoport** | Azure Migrate-projektet számos földrajzi régióban is létrehozhat. Habár ezekben a földrajzi területeken csak projekteket hozhat létre, a gépeket más célhelyekre is kielemezheti vagy áttelepítheti. A projekt földrajza csak a felderített metaadatok tárolására szolgál.

**Régiócsoport** | **Metaadatok tárolási helye**
--- | ---
Azure Government | USA-beli államigazgatás – Virginia
Ázsia és a Csendes-óceáni térség | Kelet-Ázsia vagy Délkelet-Ázsia
Ausztrália | Kelet-Ausztrália vagy Délkelet-Ausztrália
Kanada | Közép-Kanada vagy Kelet-Kanada
Európa | Észak-Európa vagy Nyugat-Európa
India | Közép-India vagy Dél-India
Japán |  Kelet-japán vagy Nyugat-Japán
Egyesült Királyság | Egyesült Királyság déli régiója vagy Egyesült Királyság nyugati régiója
Egyesült Államok | USA középső régiója vagy USA 2. nyugati régiója


 > [!NOTE]
 > A Azure Government támogatása jelenleg csak a Azure Migrate [régebbi verziójához](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) érhető el.


## <a name="assessment-vcenter-server-requirements"></a>Értékelés – vCenter Server követelmények

Ez a táblázat összefoglalja a VMware virtualizációs kiszolgálók értékelésének támogatását és korlátozásait.

**Támogatás** | **Részletek**
--- | ---
**vCenter server** | Az értékelni kívánt VMware virtuális gépeket egy vagy több, 5,5, 6,0, 6,5 vagy 6,7 rendszert futtató vCenter-kiszolgáló felügyeli.

## <a name="assessment-vcenter-server-permissions"></a>Értékelés – vCenter Server engedélyek

Az értékeléshez csak olvasható fiókra van szükség a vCenter Server.

## <a name="assessment-appliance-requirements"></a>Felmérés – készülékre vonatkozó követelmények

Azure Migrate egy könnyű berendezést futtat a VMware virtuális gépek felderítéséhez, valamint a virtuális gép metaadatainak és teljesítményadatait a Azure Migrateba való küldéséhez. A VMware rendszerhez készült készülék üzembe helyezése vCenter Serverba importált PETESEJT-sablonnal történik. A következő táblázat összefoglalja a készülékre vonatkozó követelményeket.

**Támogatás** | **Részletek**
--- | ---
**Berendezések üzembe helyezése** | A készüléket VMware virtuális gépként kell üzembe helyezni. Elegendő erőforrásra van szüksége a vCenter Server egy virtuális gép lefoglalásához 32 GB RAM-mal, 8 vCPU és egy külső virtuális kapcsolóval.<br/><br/> A berendezéshez közvetlenül vagy proxyn keresztül kell internet-hozzáférést igényelni.<br/> A berendezés virtuális gépnek a 5,5-es vagy újabb verzióját futtató ESXi-gazdagépre kell telepítenie. 
**Azure Migrate projekt** | Egy készülék egyetlen projekthez is társítható. <br/> Tetszőleges számú berendezés társítható egyetlen projekthez.<br/> Egy projektben akár 35 000 virtuális gépet is megvizsgálhat.
**Felfedezés** | A készülékek akár 10 000 VMware virtuális gépet is felfedezhetnek vCenter Serveron.<br/> Egy berendezés egyetlen vCenter Serverhoz tud csatlakozni.
**Értékelési csoport** | Egyetlen csoportban legfeljebb 35 000 gépet adhat hozzá.
**Assessment** | Egyetlen értékeléssel akár 35 000 virtuális gépet is megvizsgálhat.


## <a name="assessment-url-access-requirements"></a>Assessment-URL-hozzáférési követelmények

Az Azure Migrate berendezésnek internetkapcsolatra van szüksége.

- A berendezés telepítésekor a Azure Migrate az alábbi táblázatban összefoglalt URL-címek kapcsolatát.
- Ha URL-alapú proxyt használ az internethez való csatlakozáshoz, engedélyezze a hozzáférést ezekhez az URL-címekhez, és győződjön meg arról, hogy a proxy feloldja az URL-címek keresésekor fogadott CNAME-rekordokat.

**URL-cím** | **Részletek**  
--- | --- |
*.portal.azure.com  | Navigáljon a Azure Portal Azure Migrate.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com | Jelentkezzen be az Azure-előfizetésébe.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Hozzon létre Active Directory alkalmazásokat a készülékhez a Azure Migrate szolgáltatással való kommunikációhoz.
management.azure.com | Hozzon létre Active Directory alkalmazásokat a készülékhez a Azure Migrate szolgáltatással való kommunikációhoz.
dc.services.visualstudio.com | A belső figyeléshez használt alkalmazás-naplók feltöltése.
*.vault.azure.net | A Azure Key Vault titkainak kezelése.
*.servicebus.windows.net | A készülék és a Azure Migrate szolgáltatás közötti kommunikáció.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Kapcsolódjon Azure Migrate szolgáltatás URL-címeihez.
*.blob.core.windows.net | Adatok feltöltése a Storage-fiókba.
http://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | Azure Migrate berendezés frissítéseihez használatos.

## <a name="assessment-port-requirements"></a>Értékelés – portra vonatkozó követelmények

**Device** | **kapcsolat**
--- | ---
Berendezés | Bejövő kapcsolatok a 3389-as TCP-porton, hogy engedélyezze a távoli asztali kapcsolatokat a berendezéssel.<br/><br/> Bejövő kapcsolatok a 44368-as porton a berendezés-kezelő alkalmazás távoli eléréséhez az URL-cím használatával:```https://<appliance-ip-or-name>:44368``` <br/><br/>A 443-es, 5671-as és 5672-es porton kimenő kapcsolatok a felderítési és a teljesítménybeli metaadatok küldésére Azure Migrate.
vCenter Server | A 443-es TCP-porton bejövő kapcsolatok lehetővé teszik, hogy a berendezés konfigurációs és teljesítménybeli metaadatokat gyűjtsön az értékelésekhez. <br/><br/> A készülék alapértelmezés szerint az 443-as porton csatlakozik a vCenter-hez. Ha a vCenter-kiszolgáló egy másik portot figyel, akkor a felderítés beállításakor módosíthatja a portot.

## <a name="migration---limitations"></a>Áttelepítés – korlátozások
Egyszerre legfeljebb 10 virtuális gépet választhat a replikáláshoz. Ha több gépet szeretne áttelepíteni, a replikálást a 10 csoportba. A VMware ügynök nélküli Migrálás esetében akár 100 replikálást is futtathat egyszerre.

## <a name="agentless-migration-vmware-server-requirements"></a>Ügynök nélküli áttelepítés – VMware Server-követelmények

Ez a táblázat összefoglalja a VMware virtualizációs kiszolgálók értékelésének támogatását és korlátozásait.

**Támogatás** | **Részletek**
--- | ---
vCenter Server | 5,5, 6,0, 6,5 vagy 6,7.
VMware vSphere | 5,5, 6,0, 6,5 vagy 6,7 verzió,

## <a name="agentless-migration-vcenter-server-permissions"></a>Ügynök nélküli áttelepítés – vCenter Server engedélyek

**Engedélyek** | **Részletek**
--- | ---
Datastore.Browse | Lehetővé teszi a virtuális gépek naplófájljainak böngészését a pillanatképek létrehozásával és törlésével kapcsolatos hibák megoldásához.
Datastore.LowLevelFileOperations | Olvasási/írási/törlési/átnevezési műveletek engedélyezése az adattár böngészőben a pillanatképek létrehozásához és törléséhez.
VirtualMachine.Configuration.DiskChangeTracking | Engedélyezheti vagy letilthatja a virtuálisgép-lemezek módosításának nyomon követését, így a pillanatképek közötti megváltoztatott adatblokkok lehívhatók
VirtualMachine.Configuration.DiskLease | Engedélyezi a lemez címbérleti műveleteit a virtuális gépek számára, hogy beolvassa a lemezt a VMware vSphere Virtual Disk Development Kit (VDDK) használatával.
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | Engedélyezi a lemez megnyitását egy virtuális gépen a VDDK használatával.
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | Engedélyezi a virtuális géphez tartozó fájlok olvasási műveleteit, letölti a naplókat, és hiba esetén hibaelhárítást végez.
VirtualMachine.SnapshotManagement.* | Engedélyezi a virtuális gépek pillanatképének létrehozását és kezelését a replikáláshoz.
Virtuális gép. interakció. kikapcsolás | A virtuális gép kikapcsolásának engedélyezése az Azure-ba való áttelepítés során.


## <a name="agentless-migration-vmware-vm-requirements"></a>Ügynök nélküli áttelepítés – VMware virtuális gépekre vonatkozó követelmények

**Támogatás** | **Részletek**
--- | ---
**Támogatott operációs rendszerek** | Az Azure által támogatott Windows-és [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) - [alapú](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) operációs rendszerek az ügynök nélküli Migrálás használatával telepíthetők át.
**Az Azure szükséges módosításai** | Előfordulhat, hogy egyes virtuális gépek módosításokat igényelnek, hogy az Azure-ban is futtathatók legyenek. A Azure Migrate a következő operációs rendszerek esetében automatikusan végrehajtja ezeket a módosításokat:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> Más operációs rendszerek esetében manuálisan kell elvégezni a módosításokat az áttelepítés előtt. A kapcsolódó cikkek erre vonatkozó utasításokat tartalmaznak.
**Linux rendszerű rendszerindítás** | Ha a/boot dedikált partíción van, akkor az operációsrendszer-lemezen kell lennie, és nem szabad több lemezre osztania.<br/> Ha a/boot a gyökér (/) partíció része, akkor a "/" partíciónak az operációsrendszer-lemezen kell lennie, és nem szabad más lemezekre kiterjednie.
**UEFI-rendszerindítás** | Az UEFI-rendszerindítással rendelkező virtuális gépek migrálása nem támogatott.
**Lemez mérete** | 2 TB operációsrendszer-lemez; 4 TB adatlemezek esetében.
**Lemezterület-korlátok** |  Akár 60 lemez/virtuális gép.
**Titkosított lemezek/kötetek** | A titkosított lemezekkel/kötetekkel rendelkező virtuális gépek migrálása nem támogatott.
**Megosztott lemezes fürt** | Nem támogatott.
**Független lemezek** | Nem támogatott.
**RDM/továbbító lemezek** | Ha a virtuális gépek RDM vagy továbbító lemezzel rendelkeznek, ezek a lemezek nem replikálódnak az Azure-ba.
**NFS** | A virtuális gépeken kötetként csatlakoztatott NFS-kötetek nem lesznek replikálva.
**iSCSI-tárolók** | Az iSCSI-tárolókkal rendelkező virtuális gépek nem támogatottak az ügynök nélküli Migrálás esetén.
**Többutas IO** | Nem támogatott.
**Tárolási vMotion** | Nem támogatott. A replikáció nem fog működni, ha a virtuális gép tárolási vMotion használ.
**Összevont hálózati adapterek** | Nem támogatott.
**IPv6** | Nem támogatott.
**Céllemez** | A virtuális gépeket csak felügyelt lemezekre lehet áttelepíteni (standard HDD, prémium SSD) az Azure-ban.
**Egyidejű replikáció** | 100 virtuális gépek száma vCenter Server. Ha többre van szüksége, a 100-es kötegekben telepítse át őket.


## <a name="agentless-migration-appliance-requirements"></a>Ügynök nélküli áttelepítés – készülékre vonatkozó követelmények


**Támogatás** | **Részletek**
--- | ---
**ESXi** | A berendezés virtuális gépnek a 5,5-es vagy újabb verzióját futtató ESXi-gazdagépre kell telepítenie.
**Azure Migrate projekt** | Egy készülék egyetlen projekthez is társítható.
**vCenter Server** | A készülékek akár 10 000 VMware virtuális gépet is felfedezhetnek vCenter Serveron.<br/> Egy készülék csatlakozhat egy vCenter Serverhoz.
**VDDK** | Ha ügynök nélküli áttelepítést futtat Azure Migrate kiszolgáló áttelepítésével, az VMware vSphere VDDK telepítve kell lennie a készülék virtuális gépén.

## <a name="agentless-migration-url-access-requirements"></a>Ügynök nélküli áttelepítés – URL-hozzáférési követelmények

Az Azure Migrate berendezésnek internetkapcsolatra van szüksége az internethez.

- A berendezés telepítésekor a Azure Migrate az alábbi táblázatban összefoglalt URL-címek kapcsolatát.
- Ha URL-alapú proxyt használ, engedélyezze a hozzáférést ezekhez az URL-címekhez, és győződjön meg arról, hogy a proxy feloldja az URL-címek keresésekor fogadott CNAME-rekordokat.

**URL-cím** | **Részletek**  
--- | ---
*.portal.azure.com | Navigáljon a Azure Portal Azure Migrate.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Jelentkezzen be az Azure-előfizetésébe.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Hozzon létre Active Directory alkalmazásokat a készülékhez a Azure Migrate szolgáltatással való kommunikációhoz.
management.azure.com | Hozzon létre Active Directory alkalmazásokat a készülékhez a Azure Migrate szolgáltatással való kommunikációhoz.
dc.services.visualstudio.com | A belső figyeléshez használt alkalmazás-naplók feltöltése.
*.vault.azure.net | A Azure Key Vault titkainak kezelése.
*.servicebus.windows.net | A készülék és a Azure Migrate szolgáltatás közötti kommunikáció.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Kapcsolódjon Azure Migrate szolgáltatás URL-címeihez.
*.blob.core.windows.net | Adatok feltöltése a Storage-fiókba.
http://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | Azure Migrate berendezés frissítéseihez használatos.


## <a name="agentless-migration-port-requirements"></a>Ügynök nélküli áttelepítés – portra vonatkozó követelmények

**Device** | **kapcsolat**
--- | ---
Berendezés | Az 443-as porton kimenő kapcsolatok a replikált adatok az Azure-ba való feltöltéséhez, valamint a replikáció és az áttelepítés összehangolása Azure Migrate szolgáltatásokkal való kommunikációhoz.
vCenter Server | Bejövő kapcsolatok a 443-as porton, hogy a készülék koordinálja a replikációt – pillanatképek létrehozása, Adatmásolás, kiadási Pillanatképek
vSphere/ESXI-gazdagép | Bejövő a 902-es TCP-porton, hogy a készülék Pillanatképek adatait replikálja.


## <a name="agent-based-migration-vmware-server-requirements"></a>Ügynök-alapú áttelepítés – VMware Server-követelmények

Ez a táblázat összefoglalja a VMware virtualizációs kiszolgálók értékelésének támogatását és korlátozásait.

**Támogatás** | **Részletek**
--- | ---
vCenter Server | 5,5, 6,0, 6,5 vagy 6,7.
VMware vSphere | 5,5, 6,0, 6,5 vagy 6,7.

### <a name="agent-based-migration-vcenter-server-permissions"></a>Ügynök-alapú áttelepítés – vCenter Server engedélyek

Írásvédett fiók a vCenter Serverhoz.

## <a name="agent-based-migration-replication-appliance-requirements"></a>Ügynök-alapú áttelepítés – replikációs berendezésre vonatkozó követelmények

A VMware virtuális gépek és a Azure Migrate kiszolgáló áttelepítését biztosító fizikai kiszolgálók ügynök alapú áttelepítéséhez használt [replikációs berendezésre](migrate-replication-appliance.md) vonatkozó követelmények a táblázatban vannak összegezve.

> [!NOTE]
> Amikor beállítja a replikációs berendezést az Azure Migrate központban megadott petesejtek sablonnal, a készülék futtatja a Windows Server 2016-et, és megfelel a támogatási követelményeknek. Ha a replikációs készüléket manuálisan állítja be egy fizikai kiszolgálón, akkor ellenőrizze, hogy az megfelel-e a követelményeknek.



**Összetevő** | **Követelmény**
--- | ---
 | **VMware-beállítások** (VMware VM-készülék)
PowerCLI | A [PowerCLI 6,0-es verziójának](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) telepítve kell lennie, ha a replikációs berendezés VMWare virtuális gépen fut.
Hálózati adapter típusa | VMXNET3 (ha a készülék VMware virtuális gép)
 | **Hardverbeállítások**
Processzormagok | 8
RAM | 16 GB
Lemezek száma | Három Az operációsrendszer-lemez, a Process Server cache lemez és a megőrzési meghajtó.
Szabad lemezterület (gyorsítótár) | 600 GB
Szabad lemezterület (adatmegőrzési lemez) | 600 GB
**Szoftverbeállítások** |
Operációs rendszer | Windows Server 2016 vagy Windows Server 2012 R2
Operációs rendszer területi beállítása | Angol (en-us)
TLS | A TLS 1,2-et engedélyezni kell.
.NET-keretrendszer | A .NET-keretrendszer 4,6-es vagy újabb verziójának telepítve kell lennie a gépen (erős kriptográfiai támogatással.
MySQL | A MySQL-t telepíteni kell a készülékre.<br/> Telepíteni kell a MySQL-t. Manuálisan is telepítheti, vagy Site Recovery telepítheti a készülék telepítése során.
Egyéb alkalmazások | Ne futtasson más alkalmazásokat a replikációs berendezésen.
Windows Server-szerepkörök | Ne engedélyezze ezeket a szerepköröket: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V
Csoportházirendek | Ne engedélyezze ezeket a csoportházirendeket: <br> – A parancssor elérésének tiltása. <br> – A beállításjegyzék szerkesztési eszközeihez való hozzáférés megakadályozása. <br> – A fájlmellékletek megbízhatósági logikája. <br> – A parancsfájlok végrehajtásának bekapcsolása. <br> [További információ](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | – Nincs előre meglévő alapértelmezett webhely <br> – Nincs már meglévő webhely/alkalmazás a 443-es porton <br>– [Névtelen hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) engedélyezése <br> – [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) -beállítás engedélyezése
**Hálózati beállítások** |
IP-cím típusa | Statikus tartalom
Portok | 443 (vezérlőcsatorna-vezénylés)<br>9443 (Adatátvitel)
Hálózati adapter típusa | VMXNET3

### <a name="replication-appliance-url-access"></a>Replikációs berendezés URL-hozzáférése

A replikációs berendezésnek hozzá kell férnie ezekhez az URL-címekhez.

**URL-cím** | **Részletek**
--- | ---
\*.backup.windowsazure.com | A replikált adatátvitelhez és a koordinációhoz használatos
\*.store.core.windows.net | A replikált adatátvitelhez és a koordinációhoz használatos
\*.blob.core.windows.net | A replikált adattárban tárolt Storage-fiók elérésére szolgál
\*.hypervrecoverymanager.windowsazure.com | Replikációs felügyeleti műveletekhez és koordináláshoz használatos
https:\//management.azure.com | Replikációs felügyeleti műveletekhez és koordináláshoz használatos
*.services.visualstudio.com | Telemetria célra használatos (opcionális)
time.nist.gov | A rendszer és a globális idő közötti időszinkronizálás ellenőrzéséhez.
time.windows.com | A rendszer és a globális idő közötti időszinkronizálás ellenőrzéséhez.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.Live.com <br/> https:\//Graph.Windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | A OVF-telepítőnek hozzá kell férnie ezen URL-címekhez. A hozzáférés-vezérléshez és az identitások kezeléséhez használják Azure Active Directory
https:\//dev.mysql.com/get/downloads/MySQLInstaller/MySQL-Installer-Community-5.7.20.0.msi | A MySQL letöltésének befejezése


#### <a name="mysql-installation-options"></a>MySQL telepítési lehetőségek

A MySQL-t a következő módszerek egyikével lehet telepíteni a replikációs készülékre.

**Metódus** | **Részletek**
--- | ---
Manuális letöltés és telepítés | Töltse le a MySQL-alkalmazást & helyezze a mappába a C:\Temp\ASRSetup, majd telepítse manuálisan.<br/> A készülék a MySQL beállítása után már telepítve lesz.
Online letöltés nélkül | Helyezze a MySQL Installer alkalmazást a C:\Temp\ASRSetup. mappába. Ha telepíti a készüléket, és rákattint a MySQL letöltésére és telepítésére, a telepítő a hozzáadott telepítőt fogja használni.
Letöltés és telepítés Azure Migrate | Ha telepíti a készüléket, és a rendszer kéri a MySQL-t, válassza a **letöltés és telepítés**lehetőséget.



## <a name="agent-based-migration-vmware-vm-requirements"></a>Ügynök-alapú áttelepítés – VMware virtuális gépekre vonatkozó követelmények

**Támogatás** | **Részletek**
--- | ---
**Gépi munkaterhelés** | A Azure Migrate támogatja az olyan munkaterhelések áttelepítését (például Active Directory, SQL Server stb.), amelyek egy támogatott gépen futnak.
**Operációs rendszerek** | A legfrissebb információkért tekintse át Site Recovery [operációs rendszer támogatását](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) . Azure Migrate azonos virtuálisgép-operációsrendszer-támogatást biztosít.
**Linux fájlrendszer/vendég tárterület** | A legfrissebb információkért tekintse át a Site Recovery [Linux-fájlrendszer támogatását](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) . Azure Migrate azonos a Linux fájlrendszer támogatásával.
**Hálózat/tárterület** | A legfrissebb információkért tekintse át a Site Recovery [hálózati](../site-recovery/vmware-physical-azure-support-matrix.md#network) és [tárolási](../site-recovery/vmware-physical-azure-support-matrix.md#storage) előfeltételeit. Azure Migrate azonos hálózati/tárolási követelményeket biztosít.
**Azure-követelmények** | A legfrissebb információkért tekintse át az [Azure hálózati](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [tárolási](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)és [számítási](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) követelményeit site Recovery. A Azure Migrate a VMware-Migrálás követelményeivel azonos.
**Mobilitási szolgáltatás** | Az áttelepíteni kívánt virtuális gépeken telepíteni kell a mobilitási szolgáltatás ügynökét.
**UEFI-rendszerindítás** | Az Azure-ban áttelepített virtuális gép automatikusan BIOS rendszerindító virtuális gépre lesz konvertálva.<br/><br/> Az operációsrendszer-lemez legfeljebb négy partíciót tartalmazhat, és a köteteket NTFS fájlrendszerrel kell formázni.
**Céllemez** | A virtuális gépeket csak felügyelt lemezekre lehet áttelepíteni (standard HDD, prémium SSD) az Azure-ban.
**Lemez mérete** | 2 TB operációsrendszer-lemez; 8 TB adatlemezek esetében.
**Lemezterület-korlátok** |  Akár 63 lemez/virtuális gép.
**Titkosított lemezek/kötetek** | A titkosított lemezekkel/kötetekkel rendelkező virtuális gépek migrálása nem támogatott.
**Megosztott lemezes fürt** | Nem támogatott.
**Független lemezek** | Támogatott.
**Továbbító lemezek** | Támogatott.
**NFS** | A virtuális gépeken kötetként csatlakoztatott NFS-kötetek nem lesznek replikálva.
iSCSI-tárolók | Az iSCSI-tárolókkal rendelkező virtuális gépek nem támogatottak az ügynök nélküli Migrálás esetén.
**Többutas IO** | Nem támogatott.
**Tárolási vMotion** | Támogatott
**Összevont hálózati adapterek** | Nem támogatott.
**IPv6** | Nem támogatott.




## <a name="agent-based-migration-url-access-requirements"></a>Ügynök-alapú áttelepítés – URL-hozzáférési követelmények

A VMware virtuális gépeken futó mobilitási szolgáltatásnak internetkapcsolatra van szüksége.

A mobilitási szolgáltatás központi telepítésekor az alábbi táblázatban összefoglalt URL-címek kapcsolatát is megtekintheti.


**URL-cím** | **Részletek**  
--- | ---
*.portal.azure.com | Navigáljon a Azure Portal Azure Migrate.
*.windows.net | Jelentkezzen be az Azure-előfizetésébe.
*.microsoftonline.com | Hozzon létre Active Directory alkalmazásokat a készülékhez a Azure Migrate szolgáltatással való kommunikációhoz.
management.azure.com | Hozzon létre Active Directory alkalmazásokat a készülékhez a Azure Migrate szolgáltatással való kommunikációhoz.
dc.services.visualstudio.com | A belső figyeléshez használt alkalmazás-naplók feltöltése.
*.vault.azure.net | A Azure Key Vault titkainak kezelése.
*.servicebus.windows.net | A készülék és a Azure Migrate szolgáltatás közötti kommunikáció.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Kapcsolódjon Azure Migrate szolgáltatás URL-címeihez.
*.blob.core.windows.net | Adatok feltöltése a Storage-fiókba.

## <a name="agent-based-migration-port-requirements"></a>Ügynök-alapú áttelepítés – portra vonatkozó követelmények

**Device** | **kapcsolat**
--- | ---
VM | A virtuális gépeken futó mobilitási szolgáltatás a replikációs felügyelet érdekében a HTTPS 443 bejövő porton keresztül kommunikál a helyszíni replikációs berendezéssel (konfigurációs kiszolgálóval).<br/><br/> A virtuális gépek replikációs adatküldést küldenek a folyamat-kiszolgálónak (amely a konfigurációs kiszolgáló gépen fut) a HTTPS 9443 bejövő porton. Ez a port módosítható.
Replikációs berendezés | A replikációs berendezés az Azure-ba irányuló replikációt a HTTPS 443 kimenő porton keresztül hangolja össze.
Folyamatkiszolgáló | A Process Server replikációs adatokat fogad, optimalizálja és titkosítja, majd az Azure Storage-ba küldi az 443-as porton keresztül.<br/> Alapértelmezés szerint a Process Server fut a replikációs berendezésen.

## <a name="azure-vm-requirements"></a>Azure-beli virtuális gépekre vonatkozó követelmények

Az Azure-ba replikált összes helyszíni virtuális gépnek meg kell felelnie az ebben a táblázatban összefoglalt Azure-beli virtuálisgép-követelményeknek. Ha Site Recovery futtatja az előfeltétel-ellenőrzés replikálását, akkor az ellenőrzés sikertelen lesz, ha egyes követelmények nem teljesülnek.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Vendég operációs rendszer | Ellenőrizze az [ügynök nélküli replikálást használó VMWare virtuális gépek](#agentless-migration-vmware-vm-requirements)támogatott operációs rendszereit, valamint az [ügynökön alapuló replikációt használó VMWare virtuális gépeket](#agent-based-migration-vmware-vm-requirements).<br/> A támogatott operációs rendszereken futó munkaterhelések áttelepíthetők. | Az ellenőrzés sikertelen, ha nem támogatott.
Vendég operációs rendszer architektúrája | 64 bites. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációs rendszer lemezének mérete | Akár 2 048 GB-ig. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációsrendszer-lemezek száma | 1 | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemezek száma | 64 vagy kevesebb. | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemez mérete | Legfeljebb 4 095 GB | Az ellenőrzés sikertelen, ha nem támogatott.
Hálózati adapterek | Több adapter is támogatott. |
Megosztott virtuális merevlemez | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
FC-lemez | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
BitLocker | Nem támogatott. | A számítógép replikálásának engedélyezése előtt le kell tiltani a BitLockert.
a virtuális gép neve | 1 – 63 karakter.<br/> Csak betűket, számokat és kötőjelet tartalmazhat.<br/><br/> A gép nevének betűvel vagy számmal kell kezdődnie és végződnie. |  Frissítse az értéket a Site Recovery számítógép tulajdonságai között.
Kapcsolat az áttelepítés után – Windows | Kapcsolódás a Windows rendszerű Azure-beli virtuális gépekhez a Migrálás után:<br/> – Az áttelepítés előtt engedélyezi az RDP-t a helyszíni virtuális gépen. Ellenőrizze, hogy a **Nyilvános** profilnál felvette-e a listára a TCP- és UDP-szabályokat, valamint hogy a **Windows-tűzfal** > **Engedélyezett alkalmazások** területén az összes profil számára engedélyezve van-e az RDP.<br/> A helyek közötti VPN-hozzáféréshez engedélyezze az RDP-t, és engedélyezze az RDP használatát a **Windows tűzfal** -> **engedélyezett alkalmazásaiban és szolgáltatásaiban** a **tartomány és a magánhálózatok** számára. Továbbá győződjön meg arról, hogy az operációs rendszer SAN-szabályzata **OnlineAll**értékre van állítva. [További információk](https://support.microsoft.com/kb/3031135). |
Kapcsolat Migrálás után – Linux | Kapcsolódás az Azure-beli virtuális gépekhez az SSH használatával történő áttelepítés után:<br/> Az áttelepítés előtt a helyszíni gépen győződjön meg arról, hogy a Secure Shell szolgáltatás indításra van beállítva, és hogy a tűzfalszabályok engedélyezik az SSH-kapcsolatokat.<br/> A feladatátvételt követően az Azure-beli virtuális gépen engedélyezze az SSH-porthoz való bejövő kapcsolatokat a hálózati biztonsági csoportra vonatkozó szabályokra vonatkozóan a feladatátvételen átesett virtuális gépen, valamint azt az Azure-alhálózatot, amelyhez csatlakoztatva van. Továbbá adjon hozzá egy nyilvános IP-címet a virtuális géphez. |  


## <a name="next-steps"></a>További lépések

[Felkészülés a VMware](tutorial-prepare-vmware.md) értékelésére és áttelepítésére.
