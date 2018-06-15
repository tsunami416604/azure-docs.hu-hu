---
title: Gyakori kérdések - VMware Azure replikáció az Azure Site Recovery szolgáltatással |} Microsoft Docs
description: Ez a cikk gyakori kérdéseket foglalja össze, amikor Ön helyszíni VMware virtuális gépek replikálása Azure-ban az Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/15/2018
ms.author: raynew
ms.openlocfilehash: 345b73db423c6e12b56bb3308f7700917a372dda
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30185220"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Gyakori kérdések – Azure replikációs VMware

Ez a cikk ismerteti a kérdésekre adott válaszok közös észlelünk, ha a helyszíni VMware virtuális gépek replikálása Azure-bA. Ha kérdése van a cikk elolvasása után, az fel őket a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Általános kérdések
### <a name="how-is-site-recovery-priced"></a>Hogyan áron a Site Recovery?
Felülvizsgálati [Azure Site Recovery díjszabásáról](https://azure.microsoft.com/en-in/pricing/details/site-recovery/) részleteit.

### <a name="how-do-i-pay-for-azure-vms"></a>Hogyan fizetnie Azure virtuális gépekhez?
Replikálás során adatait az Azure storage replikálja a rendszer, és nem a virtuális gép módosításokat kell fizetnie. Ha a feladatátvételt az Azure-ba, a Site Recovery automatikusan létrehozza a Azure infrastruktúra-szolgáltatási virtuális gépeket. Ezt követően az Azure-ban felhasznált számítási erőforrások díjon számlázzuk.

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>Milyen feladatokat lehet elvégezni a VMware Azure replikációs?
- **Vész-helyreállítási**: teljes vész-helyreállítási állíthat be. Ebben a forgatókönyvben a helyszíni VMware virtuális gépek az Azure storage replikálja. Ezt követően a helyszíni infrastruktúra nem érhető el, ha átveheti az Azure-bA. Történő feladatátadást követően, Azure virtuális gépek jönnek létre, a replikált adatok használatával. Alkalmazások és munkaterhelések az Azure virtuális gépeken, amíg a helyszíni adatközpontját újra elérhetővé érheti el. Ezt követően, sikertelen lehet az Azure-ból a helyszíni helyre.
- **Áttelepítési**: a Site Recovery segítségével helyszíni VMware virtuális gépek áttelepítése az Azure-bA. Ebben a forgatókönyvben a helyszíni VMware virtuális gépek az Azure storage replikálja. Majd hogy átadja a helyszíni Azure. A feladatátvétel után még alkalmazások és munkafolyamatok rendelkezésre állnak és futó Azure virtuális gépeken.



## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>Mit kell az Azure-ban?
Azure-előfizetéssel, Recovery Services-tároló, a tárfiók és egy virtuális hálózathoz van szüksége. A tároló, a tárfiók és a hálózati ugyanabban a régióban kell lennie.

### <a name="what-azure-storage-account-do-i-need"></a>Milyen az Azure storage-fiók szükséges?
Az LRS- vagy GRS-tárfiók van szüksége. Mi a GRS használatát javasoljuk, mivel ez akár regionális kimaradás során, illetve az elsődleges régió helyreállíthatatlansága esetében gondoskodik az adatok hibatűréséről. Prémium szintű storage használata támogatott.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Nem a saját Azure-fiók létrehozása a virtuális gépek engedélyekre van szükség?
Ha egy előfizetés adminsztrátori, a replikáció szükséges engedélyekkel rendelkezik. Ha nem, az erőforráscsoport és a virtuális hálózati hely Reocvery konfigurálásakor adjon meg egy Azure virtuális gép létrehozásához szükséges engedélyek, és szükséges a kiválasztott tárolási accout írási engedélyekkel. [További információk](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).



## <a name="on-premises"></a>Helyszíni követelmények 

### <a name="what-do-i-need-on-premises"></a>Mire van szükség a helyszínen?
A helyszíni van szüksége a Site Recovery, egy VMware virtuális telepített összetevőit. Legalább egy ESXi-állomáson, egy VMware-infrastruktúra is szükséges, és azt javasoljuk, hogy a vCenter-kiszolgálót. Továbbá szüksége van egy vagy több VMware virtuális gépek replikálásához. [További](vmware-azure-architecture.md) kapcsolatos VMware Azure architektúrát.

A helyszíni konfigurációs kiszolgáló telepíthető a következő két módszer egyikével

1. Egy Virtuálisgép-sablont, amely rendelkezik a konfigurációs kiszolgáló előtelepített használatával telepítse. [Itt olvasható](vmware-azure-tutorial.md#download-the-vm-template).
2. A telepítő a Windows Server 2016 gépen a kiválasztott használatával telepítse. [Itt olvasható](physical-azure-disaster-recovery.md#set-up-the-source-environment).

Az első lépések a konfigurációs kiszolgáló a saját Windows Server gépeken, a védelem engedélyezése a védelmi cél telepítésének észlelése válassza **az Azure-bA > nem virtualizált vagy egyéb**.

### <a name="where-do-on-premises-vms-replicate-to"></a>Ha hajtsa végre a helyszíni virtuális gépek replikálása?
Az Azure storage replikálja az adatokat. Feladatátvételt, a Site Recovery automatikusan létrehoz Azure virtuális gépek a tárfiók.

### <a name="what-apps-can-i-replicate"></a>Milyen alkalmazásokat replikálhatok?
Bármely alkalmazás vagy a VMware virtuális gép, amely megfelel az számítási feladatot képes replikálni [replikációs követelményektől](vmware-physical-azure-support-matrix.md##replicated-machines). A Site Recovery támogatja az alkalmazástudatos replikációt, hogy az alkalmazások átadja a feladatokat, és ismét működőképes állapotban nem sikerült. A Site Recovery integrálható a Microsoft-alkalmazások, például a SharePoint, Exchange, Dynamics, SQL Server és Active Directory, és szorosan együttműködik olyan vezető szállítókkal, beleértve az Oracle, SAP, IBM és Red Hat. [További információk](site-recovery-workload.md) a számítási feladatok védelméről.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Replikálhatok Azure-bA a telephelyek közötti VPN?
A Site Recovery replikálja az adatokat a helyszíni az Azure storage egy nyilvános végpontot, vagy ExpressRoute nyilvános társviszony-létesítés használatával. A pont-pont VPN hálózaton replikáció nem támogatott.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Replikálhatok Azure-bA ExpressRoute?
Igen, ExpressRoute segítségével virtuális gépek replikálása az Azure-bA. A Site Recovery replikálja az adatokat egy Azure Storage-fiók alatt egy nyilvános végpontot, és állítsa be kell [nyilvános társviszony](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) a Site Recovery-replikáció. Miután a virtuális gépek feladatátvételt egy Azure virtuális hálózatra, akkor is elérhetőek használatával [magánhálózati társviszony-létesítés](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).


### <a name="why-cant-i-replicate-over-vpn"></a>Miért nem replikálja VPN-kapcsolaton keresztül?

Az Azure-bA replikált replikációs forgalom eléri a nyilvános végpontok az Azure Storage-fiók, így csak replikálhatja az ExpressRoute (nyilvános társviszony) a nyilvános interneten keresztül, és VPN nem működik. 



## <a name="what-are-the-replicated-vm-requirements"></a>Mik a replikált virtuális gép követelményei?

A replikáció a VMware virtuális gépek futnia kell egy támogatott operációs rendszert. Emellett a virtuális gép kell megfelelnie az Azure virtuális gépekhez. [További](vmware-physical-azure-support-matrix.md##replicated-machines) támogatási mátrixban.

## <a name="how-often-can-i-replicate-to-azure"></a>Milyen gyakran replikálhatom az Azure-bA?
VMware virtuális gépek replikálása Azure-bA folyamatos replikálása.

## <a name="can-i-extend-replication"></a>Ki lehet terjeszteni a replikációt?
A kiterjesztett vagy láncolt replikáció nem támogatott. Ez a szolgáltatás kérése [visszajelzési fórumon](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

## <a name="can-i-do-an-offline-initial-replication"></a>Végezhetek offline kezdeti replikációt?
Ez a funkció nem támogatott. Ez a szolgáltatás kérése a [visszajelzési fórumon](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Kizárhatok a lemezek?
Igen, lemezek lehet kizárni a replikálásból. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Virtuális gépek replikálhatok dinamikus lemezeken?
A dinamikus lemezek replikálható. Az operációsrendszer-lemez alaplemeznek kell lennie.

### <a name="can-i-add-a-new-vm-to-an-existing-replication-group"></a>Hozzáadható egy új virtuális gép egy meglévő replikációs csoporthoz?
Igen.

## <a name="configuration-server"></a>Configuration server

### <a name="what-does-the-configuration-server-do"></a>Hogyan működik a konfigurációs kiszolgáló?
A konfigurációs kiszolgáló futtatja a helyszíni Site Recovery-összetevőket, például: 
- A konfigurációs kiszolgáló, amely a helyszíni és az Azure közötti kommunikáció koordinálja, és adatreplikáció kezeli.
- A folyamatkiszolgáló, amelyek egy replikációs átjáróként működik. Akkor kap replikációs adatokat; optimalizálja a gyorsítótárazás, tömörítés és titkosítás; és elküldi azt az Azure storage., a folyamatkiszolgáló is telepíti a mobilitási szolgáltatás kíván készíteni, és a helyszíni VMware virtuális gépek automatikus felderítést hajt végre virtuális gépeken.
- A fő célkiszolgálón, amely az Azure-ból a feladat-visszavétel során kezeli a replikációs adatokat.

### <a name="where-do-i-set-up-the-configuration-server"></a>Ha a konfigurációs kiszolgáló beállítása?
Egy magas rendelkezésre állású helyszíni VMware virtuális gép van szüksége a konfigurációs kiszolgáló.

### <a name="what-are-the-requirements-for-the-configuration-server"></a>Mik a követelmények, a konfigurációs kiszolgáló?

Tekintse át a [Előfeltételek](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Is kézzel állítható be a konfigurációs kiszolgáló, egy sablon használata helyett?
Azt javasoljuk, hogy a legújabb verzióját az OVF-sablont használja [a konfigurációs kiszolgáló virtuális gép létrehozása](vmware-azure-deploy-configuration-server.md). Ha valamilyen okból nem lehet, például nincs a VMware-kiszolgáló eléréséhez, akkor [töltse le az egységes telepítő fájlt](physical-azure-set-up-source.md) a portálról, és futtassa a virtuális gép. 

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>A konfigurációs kiszolgáló egynél több régióba képes replikálni?
Nem. Ehhez szüksége minden régióban egy konfigurációs kiszolgálót.

### <a name="can-i-host-a-configuration-server-in-azure"></a>A konfigurációs kiszolgáló Azure-ban is futtatni?
Bár lehetséges a konfigurációs kiszolgálón futó Azure virtuális gép kell a helyszíni VMware-infrastruktúra és a virtuális gépek folytatott kommunikációhoz. A terhelés valószínűleg nem működőképes.


### <a name="where-can-i-get-the-latest-version-of-the-configuration-server-template"></a>Honnan szerezhetők be a konfigurációs kiszolgáló sablon legújabb verzióját?
Töltse le a legújabb verziót a [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

### <a name="how-do-i-update-the-configuration-server"></a>Hogyan frissíthetők a konfigurációs kiszolgáló?
Ezután telepítse a frissítéseket telepíti. A legújabb frissítésekkel kapcsolatos információk a megtalálhatja a [wiki frissítések lap](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

## <a name="mobility-service"></a>Mobilitási szolgáltatás

### <a name="where-can-i-find-the-mobility-service-installers"></a>Hol találnak a mobilitási szolgáltatás telepítők?
A telepítőcsomagokat tartják a **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** mappa a konfigurációs kiszolgálón.

## <a name="how-do-i-install-the-mobility-service"></a>Hogyan kell telepíteni a mobilitási szolgáltatást?
Telepítése minden egyes virtuális Gépre szeretne replikálni, használja a [leküldéses telepítése](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery), vagy a manuális telepítés [a felhasználói felület](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui), vagy [PowerShell-lel](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt). Másik lehetőségként telepítheti, mint egy központi telepítési eszközzel [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md), vagy [Azure Automation és DSC](vmware-azure-mobility-deploy-automation-dsc.md).



## <a name="security"></a>Biztonság

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Milyen hozzáférési kell a Site Recovery a VMware Server?
A Site Recoverynek hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az alábbiak érdekében:

- VMware virtuális gép fut, a konfigurációs kiszolgáló és a többi helyszíni Site Recovery összetevő beállítása. [További információ](vmware-azure-deploy-configuration-server.md)
- Virtuális gépek automatikus észleléséhez, a replikáció. További tudnivalók az automatikus felderítési fiók előkészítése. [További információ](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Milyen hozzáférési kell a Site Recovery a VMware virtuális gépek?

- Hogy a replikáció, egy VMware virtuális gép telepítve és fut a Site Recovery mobilitási szolgáltatás kell rendelkeznie. Manuálisan telepítse az eszközt, vagy adja meg, hogy a virtuális gépek replikációjának engedélyezése esetén a Site Recovery kell tennie egy leküldéssel telepíteni a szolgáltatást. Az ügyfélleküldéses telepítéshez, a Site Recovery kell egy fiókot, amely a szolgáltatás telepítésekor használhat. [További információk](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation). A folyamat (szolgáltatást futtató kiszolgáló alapértelmezés szerint a konfigurációs kiszolgálón) a telepítést hajt végre. [További](vmware-azure-install-mobility-service.md) mobilitási szolgáltatás a telepítéssel kapcsolatban.
- Replikálás során virtuális gépek kommunikálni helyreállítás az alábbiak szerint:
    - Virtuális gépek kommunikálni a konfigurációs kiszolgáló, a replikáció kezelését HTTPS 443-as port.
    - Virtuális gépek replikációs adatokat küldeni a folyamatkiszolgáló HTTPS 9443-porton (módosítható).
    - Ha engedélyezi a virtuális Gépre kiterjedő konzisztencia, virtuális gépek kommunikálhatnak egymással 20004 porton keresztül.


### <a name="is-replication-data-sent-to-site-recovery"></a>Replikációs adatokat küldi el a Site Recovery?
Nem, a Site Recovery nem gyűjt replikációs adatokat, és nem rendelkezik semmilyen információval, hogy a virtuális gépeken futó tartalomról. A replikációs adatcsere VMware hipervizorok és az Azure storage között. A Site Recovery nem képes ezekhez az adatokhoz hozzáférni. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.  

A Site Recovery ISO 27001:2013, 27018, HIPAA, DPA hitelesített, de SOC2 és FedRAMP JAB minősítés folyamatban van.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>A Microsoft megtarthatja a helyi metaadatok a földrajzi régiók belül?
Igen. Régióban létrehozásakor egy tárolót, gondoskodunk róla, hogy minden adott földrajzi régióban belül a Site Recovery továbbra is használja.

### <a name="does-site-recovery-encrypt-replication"></a>A Site Recovery titkosítja a replikációt?
Igen, mindkét titkosítási az átvitel és [az Azure-ban titkosítási](https://docs.microsoft.com/azure/storage/storage-service-encryption) támogatottak.


## <a name="failover-and-failback"></a>Feladatátvétel és feladat-visszavétel
### <a name="how-far-back-can-i-recover"></a>Milyen távolságban vissza állíthatja helyre?
A legrégebbi helyreállítási pont használható az Azure-bA VMware esetén 72 óra.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hogyan érhetem el az Azure virtuális gépek a feladatátvételt követően
A feladatátvétel után Azure virtuális gépeket biztonságos internetkapcsolaton keresztül, pont-pont VPN- vagy Azure ExpressRoute keresztül érheti el. Készítse elő a több csatlakozás érdekében minden lesz szüksége. [További információ](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Nem sikerült az adatok hibatűréséről?
Az Azure-t hibatűrőnek terveztük. A Site Recovery van a feladatátvétel egy másodlagos Azure adatközpontba, az Azure garantált szolgáltatási szintje fejthetők vissza. Ha feladatátvétel történik, biztosítjuk, hogy a metaadatok, és a tároló számára kiválasztott azonos földrajzi régióban maradjanak tárolók.

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?
[Feladatátvételi](site-recovery-failover.md) nem automatikus. Feladatátvételt egyetlen kattintással a portálon, vagy használhat [ PowerShell](/powershell/module/azurerm.siterecovery) elindítása a feladatátvételt.

### <a name="can-i-fail-back-to-a-different-location"></a>Vagyok meghiúsulhat vissza egy másik helyre?
Igen, ha az Azure-bA feladatátvételt, sikertelen lehet vissza egy másik helyre a egy-egy nem érhető el. [További információk](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>Miért kell egy VPN- vagy ExpressRoute történő feladat-visszavételt?

Ha nem sikerül az Azure-ból, az Azure-ból adatokat vissza a helyszíni virtuális Gépre másolja, míg privát hozzáférést kell biztosítania. 



## <a name="automation-and-scripting"></a>Automatizálás és parancsfájl-kezelési

### <a name="can-i-set-up-replication-with-scripting"></a>Adható meg a parancsfájl-replikáció?
Igen. A Rest API-t, PowerShell vagy az Azure SDK Site Recovery munkafolyamatainak automatizálható. [További](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Teljesítmény és kapacitás
### <a name="can-i-throttle-replication-bandwidth"></a>Szabályozhatja a replikáció sávszélesség?
Igen. [További információk](site-recovery-plan-capacity-vmware.md).


## <a name="next-steps"></a>További lépések
* [Felülvizsgálati](vmware-physical-azure-support-matrix.md) igényeinek támogatására.
* [Állítson be](vmware-azure-tutorial.md) VMware Azure replikációjára.
