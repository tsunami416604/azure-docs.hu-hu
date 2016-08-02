<properties 
    pageTitle="Site Recovery: gyakori kérdések | Microsoft Azure" 
    description="Ebben a cikkben az Azure Site Recovery szolgáltatással kapcsolatos gyakori kérdésekre kaphat választ."
    services="site-recovery" 
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags 
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na" 
    ms.workload="storage-backup-recovery"
    ms.date="03/27/2016"
    ms.author="raynew"/>


# Azure Site Recovery: gyakori kérdések (GYIK)

Ebben a cikkben az Azure Site Recovery szolgáltatással kapcsolatos gyakori kérdésekre találhat választ.

Ha a cikk elolvasása után további kérdései vannak, beküldheti őket a cikk végén, vagy felteheti őket az [Azure Recovery Services fórumán](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).

## Általános kérdések

### Mire való a Site Recovery?

A Site Recovery segíti Önt abban, hogy kialakíthassa üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégiáját azzal, hogy a helyszíni virtuális gépek és fizikai kiszolgálók Azure-ba vagy egy másodlagos adatközpontba való replikálásának munkafolyamatát levezényli és automatizálja.  [További információk](site-recovery-overview.md).

### Mit tud replikálni a Site Recovery?

- **Hyper-V virtuális gépek**: a Site Recovery bármilyen számítási feladatot képes replikálni, amely egy támogatott Hyper-V virtuális gépen fut. 
- **Fizikai kiszolgálók**: a Site Recovery bármilyen számítási feladatot képes replikálni, amely egy támogatott Windows/Linux fizikai kiszolgálón fut.
- **VMware virtuális gépek**: a Site Recovery bármilyen számítási feladatot képes replikálni, amely egy támogatott VMware-alapú virtuális gépen fut.


### Mire van szükség Hyper-V esetén?

A szükséges Hyper-V gazdakiszolgálóra vonatkozó feltételek a telepítési forgatókönyvtől függenek. A Hyper-V-vel kapcsolatos előfeltételekről az alábbi cikkekben olvashat:

- [Hyper-V virtuális gépek replikálása Azure-ba (VMM nélkül)](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Hyper-V virtuális gépek replikálása Azure-ba (VMM-mel)](site-recovery-vmm-to-azure.md#before-you-start)
- [Hyper-V virtuális gépek replikálása másodlagos adatközpontba](site-recovery-vmm-to-vmm.md#before-you-start)

Hyper-V gazdakiszolgálón futó vendéggép esetén:

- Ha másodlagos helyre replikál, tájékozódjon a [Hyper-V virtuális gépek által támogatott vendég operációs rendszerekről](https://technet.microsoft.com/library/mt126277.aspx).
- Ha az Azure-ba replikál, a Site Recovery ugyanazokat a vendég operációs rendszereket támogatja, mint az [Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).


### Lehetséges a virtuális gépek védelme, ha a Hyper-V egy ügyfél típusú operációs rendszeren fut?

Nem. A virtuális gépeknek egy támogatott Windows kiszolgáló gépen futó Hyper-V gazdakiszolgálón kell lenniük. Ha ügyfélgépet szeretne védeni, az [Azure-ba](site-recovery-vmware-to-azure-classic.md), vagy egy [másodlagos adatközpontba](site-recovery-vmware-to-vmware.md) replikálhatja azt fizikai gépként.


### Milyen számítási feladatokat tud védeni a Site Recovery?

A Site Recovery bármilyen számítási feladatot képes replikálni, amely támogatott virtuális gépen vagy fizikai kiszolgálón fut. A Site Recovery támogatja az alkalmazástudatos replikációt, így az alkalmazások működőképes állapotban állíthatók helyre. Olyan Microsoft-alkalmazásokkal integrálható, mint a SharePoint, Exchange, Dynamics, SQL Server vagy Active Directory, és szorosan együttműködik olyan vezető szállítókkal, mint az Oracle, a SAP, az IBM vagy a Red Hat. [További információk](site-recovery-workload.md) a számítási feladatok védelméről.


### Szükséges, hogy a Hyper-V gazdagépek a System Center VMM-felhőkben legyenek ahhoz, hogy replikálhatók legyenek a Site Recoveryvel? 

Ha másodlagos adatközpontba szeretne replikálni, akkor a Hyper-V virtuális gépnek Hyper-V gazdakiszolgálón kell futnia egy VMM-felhőben. Ha az Azure-ba szeretne replikálni, akkor a Hyper-V gazdakiszolgálónak nem szükséges a VMM-felhőben lennie. [További információk](site-recovery-hyper-v-site-to-azure.md). 

### Üzembe helyezhetem VMM-mel a Site Recovery-t, ha csak egy VMM-kiszolgálóm van? 

Igen. Replikálhat virtuális gépeket a VMM-felhőben lévő Hyper-V kiszolgálókból az Azure-ba, vagy ugyanazon a kiszolgálón található VMM-felhők között. Helyszíniről helyszínire történő replikáció esetében ajánlatos, hogy rendelkezzen VMM-kiszolgálóval mind az elsődleges, mind a másodlagos helyen. [További információk](site-recovery-single-vmm.md)

### Milyen fizikai kiszolgálókat lehet védeni?

Az Azure-ba vagy másodlagos helyre replikált fizikai kiszolgálókat védhet, amelyek támogatott Windows vagy Linux operációs rendszereket futtatnak. [További információk](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) az operációs rendszerekkel kapcsolatos követelményekről az Azure-ba vagy másodlagos helyre irányuló replikáció esetén. Vegye figyelembe, hogy feladatátvétel esetén az Azure tárolási szolgáltatásba replikált fizikai kiszolgálók virtuális gépként fognak futni az Azure-ban. Az Azure-ból a helyi környezetbe történő feladatvisszavétel esetén fizikai kiszolgálóra való feladatvisszavétel jelenleg nem támogatott. A feladatvisszavétel csak VMware-en futó virtuális gépre lehetséges.

### Milyen VMware virtuális gépek védhetők?

VMware virtuális gépek védelméhez vSphere hipervizorra van szükség, a virtuális gépeknek pedig VMware-eszközökkel kell rendelkezniük. A hipervizorok kezeléséhez a VMware vCenter használatát javasoljuk. [További információk](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) a VMware-kiszolgálók és virtuális gépek Azure-ba vagy másodlagos helyre történő replikálásának részletes követelményeiről.

### Elérhető a vészhelyreállítás a fiókirodák számára a Site Recoveryvel?

Igen. Ha fiókirodákban végez replikációt és feladatátvételt, egy központi helyen kezelheti és ellenőrizheti az összes fiókiroda számítási feladatait a Site Recoveryvel. A központi telephelyről könnyen intézheti a feladatátvételt és a vészhelyreállítást az összes fiókiroda tekintetében anélkül, hogy el kellene látogatnia a fiókirodák telephelyeire. 

## Biztonság


### A replikációs adatok el lesznek küldve a Site Recovery szolgáltatáshoz?

Nem. A Site Recovery nem gyűjt replikációs adatokat, és nem rendelkezik semmilyen információval a virtuális gépeken vagy a fizikai kiszolgálókon futó tartalomról.

A replikációs adatcsere a helyszíni Hyper-V gazdagépek, a VMware hipervizorok vagy fizikai kiszolgálók és az Azure tárolási szolgáltatás között történik.  A Site Recovery nem képes ezekhez az adatokhoz hozzáférni. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg. 

A Site Recovery ISO 27001:2005 tanúsítvánnyal rendelkezik, és folyamatban van a HIPAA, DPA és FedRAMP JAB minősítés is.

### A megfelelőség érdekében saját helyszíni környezeti metaadatainknak is ugyanabban a földrajzi régióban kell maradniuk. Lehetséges ez a Site Recoveryvel?

Igen. Amikor egy adott régióban létrehoz egy Site Recovery-tárolót, gondoskodunk róla, hogy a replikáció lehetővé tételéhez és levezényléséhez szükséges minden metaadat ugyanabban a földrajzi régióban maradjon.

### A Site Recovery titkosítja a replikációt?
Helyszíni virtuális gépek és fizikai kiszolgálók közötti replikáció esetén támogatott az átvitel közbeni titkosítás. Virtuális gépek és fizikai kiszolgálók Azure-ba történő replikálása esetén mind az átvitel közbeni titkosítás, mind az (Azure-beli) inaktív adatok titkosítása támogatott. 



## Replikáció

### Van valamilyen előfeltétele a virtuális gépek Azure-ba való replikációjának?

Az Azure-ba replikálandó virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](site-recovery-best-practices.md#azure-virtual-machine-requirements).

### Replikálható Hyper-V 2. generációs virtuális gép az Azure-ba?

Igen. A Site Recovery a 2. generációs virtuális gépeket 1. generációssá alakítja a feladatátvétel során. Feladatvisszavételkor visszaalakítja a virtuális gépeket 2. generációssá. [További információ](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### Ha Azure-ba replikálok, hogyan kell fizetni az Azure virtuális gépekért? 

A szokásos replikáció esetén az adatokat georedundáns Azure-tárterületre replikáljuk, és nem kell díjat fizetnie Azure infrastruktúra-szolgáltatás virtuális gépeiért.  Amikor feladatátvételt indít az Azure-ba, a Site Recovery automatikusan létrehoz virtuális gépeket az Azure infrastruktúra-szolgáltatásban, ezt követően pedig az Azure-ban felhasznált számítási erőforrások díját fogjuk számlázni.

### Létezik SDK a Site Recovery munkafolyamatainak automatizálásához?

Igen. A Site Recovery munkafolyamatainak automatizálásához a Rest API-t, a PowerShellt vagy az Azure SDK-t használhatja. További információk a [Site Recovery üzembe helyezéséről a PowerShell és az Azure Resource Manager használatával](site-recovery-deploy-with-powershell-resource-manager.md).

### Milyen típusú tárfiókra van szükségem ahhoz, hogy az Azure-ba replikálhassak?

Ehhez [standard georedundáns tárfiókot](../storage/storage-introduction.md#replication-for-durability-and-high-availability) kell használnia. Prémium szintű Storage jelenleg nem használható.

### Milyen gyakran replikálhatom az adatokat?

- **Hyper-V:** a Windows Server 2012 R2 kiszolgálón futó Hyper-V virtuális gépeket 30 másodpercenként, 5 percenként vagy 15 percenként lehet replikálni. Ha SAN-replikációt állított be, a replikáció szinkronizált lesz.
- **VMware és fizikai kiszolgálók:** a replikáció gyakoriságának ezeknél nincs jelentősége. A replikáció folyamatos lesz. 

### Ki lehet terjeszteni a replikációt egy már létező helyreállítási helyről egy másik helyreállítási helyre?

A kiterjesztett vagy láncolt replikáció nem támogatott. Ezzel a funkcióval kapcsolatban [visszajelzést küldhet](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication/).


### Végezhetek offline replikációt, amikor első alkalommal replikálok Azure-ba? 

Ez a funkció nem támogatott. Ezzel a funkcióval kapcsolatban [visszajelzést küldhet](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from/).

### Kizárhatok a replikációból bizonyos lemezeket?

Ez a funkció nem támogatott. Ezzel a funkcióval kapcsolatban [visszajelzést küldhet](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication/).

### Replikálhatok dinamikus lemezeken futó virtuális gépeket?

A dinamikus lemezek Hyper-V virtuális gépek replikálása esetén támogatottak. Támogatottak továbbá akkor is, ha VMware-alapú virtuális gépeket vagy fizikai gépeket replikál, amennyiben a [továbbfejlesztett üzembe helyezési modellt](site-recovery-vmware-to-azure-classic.md) használja. Vegye figyelembe, hogy az operációs rendszer lemezének alaplemeznek kell lennie.

### Szabályozhatom a Hyper-V replikációs forgalom sávszélességét?
- Ha két helyszíni hely között zajlik a Hyper-V virtuális gépek replikációja, használhatja a Windows QoS-t. Ehhez itt talál egy minta parancsfájlt: 

        New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
        gpupdate.exe /force

- Ha Hyper-V virtuális gépeket az Azure-ba replikál, a forgalomszabályozás beállításához használhatja az alábbi PowerShell parancsmagmintát:

        Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

- A Hyper-V forgalomszabályozásáról további információt [ebben a cikkben](https://support.microsoft.com/en-us/kb/3056159) talál.
- Az Azure-ba történő VMware-replikáció forgalomszabályozásáról [itt tudhat meg többet](site-recovery-vmware-to-azure-classic.md#capacity-planning).


## Feladatátvétel

### Hogyan érhetem el az Azure virtuális gépeket, miután megtörtént a feladatátvétel az Azure-ba? 

Az Azure virtuális gépeket biztonságos internetkapcsolaton keresztül, helyek közötti VPN-en keresztül, vagy Azure ExpressRoute segítségével érheti el. A VPN-kapcsolaton keresztüli kommunikáció belső portokra irányul azon az Azure-hálózaton belül, amelyen a virtuális gép is van. Az interneten keresztüli kommunikáció a virtuális gépek publikus végpontjaira van leképezve az Azure felhőszolgáltatáson belül.

### Az Azure-ba irányuló feladatátvétel esetén hogyan gondoskodik az Azure az adatok hibatűréséről?

Az Azure-t hibatűrőnek terveztük. A Site Recovery már úgy van felépítve, hogy szükség esetén lehetővé váljon a feladatátvétel egy másodlagos Azure adatközpontba, az Azure SLA-ban (szolgáltatásszint-szerződésben) foglaltak szerint. Ha erre sor kerül, biztosítjuk, hogy az Ön metaadatai és tárolói ugyanabban a földrajzi régióban maradjanak, amelyet Ön a tároló számára kiválasztott. 

### Ha két hely közötti replikációt végzek, mi történik az elsődleges hely esetleges váratlan leállásakor?

Elindíthat egy nem tervezett feladatátvételt a másodlagos helyről. A Site Recovery nem követeli meg a feladatátvétel végrehajtásához, hogy az elsődleges helyről kapcsolódjon.

### Automatikus a feladatátvétel?

A feladatátvétel nem automatikus. A feladatátvételt egyetlen kattintással elindíthatja a portálon, vagy használhatja a [Site Recovery PowerShell parancsmagokat](https://msdn.microsoft.com/library/dn850420.aspx) is az elindításához. A feladatvisszavételt egy műveletsorozattal lehet elvégezni a Site Recovery-portálon.

A feladatátvétel automatizálásához használhatja a helyszíni Orchestratort vagy az Operations Managert a virtuális gép hibájának észlelésére, majd elindíthatja a feladatátvételt az SDK használatával.


## 
### Szolgáltató vagyok. Működik a Site Recovery dedikált és megosztott infrastruktúra-modellekkel?
Igen, a Site Recovery támogatja mind a dedikált, mind a megosztott infrastruktúra-modelleket.

### Szolgáltatók esetén a Site Recovery szolgáltatás hozzáfér a bérlők identitásadataihoz?
Nem. A bérlőnek nincs szüksége a Site Recovery-portál elérésére. Csak a szolgáltatást nyújtó rendszergazdája kommunikál a portállal.


### A bérlő alkalmazásainak adatai eljutnak az Azure-hoz?

Ha a replikáció a szolgáltató által birtokolt helyek között történik, az alkalmazásadatok soha nem kerülnek az Azure-ba. Az adatok átvitel közben titkosítva vannak, a replikáció pedig közvetlenül a szolgáltatást nyújtó helyei között történik.

Ha az Azure-ba replikál, az alkalmazásadatok Azure-tárterületre kerülnek, a Site Recovery szolgáltatáshoz azonban nem jutnak el. Az adatok átvitel közben titkosítva vannak, és az Azure-ban is titkosítva maradnak. 

### Kapnak a bérlőim számlát valamilyen Azure-szolgáltatásról?

Nem. Az Azure közvetlenül a szolgáltatóval áll számlázási kapcsolatban. A bérlők felé történő számlázásért a szolgáltató felel.

### Ha az Azure-ba replikálok, szükséges a virtuális gépeket folyamatosan az Azure-ban futtatni?

Nem. Az adatok a replikáció során az Ön előfizetéséhez tartozó georedundáns Azure-tárfiókba kerülnek. Ha feladatátvételi tesztet (vészhelyreállítási gyakorlatot) vagy tényleges feladatátvételt végez, a Site Recovery automatikusan létrehozza a virtuális gépeket az előfizetéséhez.

### Elérhető bérlő szintű elkülönítés az Azure-ba való replikációnál?

Igen.

### Jelenleg milyen platformok támogatottak?

Támogatott az Azure Pack, a Cloud Platform System és a System Center (2012 vagy újabb). Az Azure Pack-integrációról további információkat a [Configure protection for virtual machines](https://technet.microsoft.com/library/dn850370.aspx) (Védelem konfigurálása virtuális gépekhez) című cikkben talál.

### Támogatott az egyetlen Azure Pack-re és az egyetlen VMM-kiszolgálóra alapuló üzembe helyezési modell?

Igen. Replikálhat Hyper-V virtuális gépeket Azure-ba, és replikálhat szolgáltatói helyek között is.  Vegye figyelembe, hogy ha szolgáltatói helyek között történik a replikáció, az Azure-forgatókönyvek integrációja nem lehetséges.


## Következő lépések

- Olvassa el a [Site Recovery áttekintését](site-recovery-overview.md)
- Információk a [Site Recovery architektúrájáról](site-recovery-components.md)  



<!--HONumber=Jun16_HO2-->


