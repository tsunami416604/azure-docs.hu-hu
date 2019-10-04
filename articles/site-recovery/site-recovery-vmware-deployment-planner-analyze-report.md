---
title: A VMware-ből az Azure-ba irányuló vész-helyreállítási Azure Site Recovery Deployment Planner jelentés elemzése | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan elemezhető a Azure Site Recovery Deployment Planner által létrehozott jelentés a VMware vész-helyreállításra az Azure-ba.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 7/29/2019
ms.author: mayg
ms.openlocfilehash: f4b63cfc67e20158e434e1a401d47144c3e0f90c
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68618776"
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report-for-vmware-disaster-recovery-to-azure"></a>A VMware vész-helyreállítási Azure Site Recovery Deployment Planner jelentés elemzése az Azure-ba

Az elkészített Microsoft Excel-jelentés a következő táblázatokat tartalmazza:
## <a name="on-premises-summary"></a>Helyszíni összefoglalás
A Helyszíni összefoglalás munkalap áttekintést nyújt arról a VMware-környezetről, amelyről profilt készített.

![A VMware-környezet helyszíni összefoglalása](media/site-recovery-vmware-deployment-planner-analyze-report/on-premises-summary-v2a.png)

**Kezdési** és **befejezési dátum**: A jelentéskészítési adatok kezdő és záró dátuma a jelentés generálásához. Alapértelmezés szerint a kezdő dátum az a dátum, amikor a profilkészítés megkezdődik, a záró dátum pedig az a dátum, amikor a profilkészítés leáll. Ezek lehetnek a StartDate és EndDate értékek, ha a jelentést ezekkel a paraméterekkel hozza létre.

**Profilkészítési napok száma összesen**: A jelentés alapjául szolgáló kezdő és záró dátumok közötti profilkészítési napok teljes száma.

**Kompatibilis virtuális gépek száma**: A kompatibilis virtuális gépek teljes száma, amelyekhez a szükséges hálózati sávszélesség, a szükséges számú Storage-fiók, a Microsoft Azure magok, a konfigurációs kiszolgálók és a további folyamat-kiszolgálók számítanak.

**A lemezek teljes száma az összes kompatibilis virtuális gépen**: Az egyik bemenetként használt szám az üzemelő példányban használandó konfigurációs kiszolgálók és további folyamat-kiszolgálók számának meghatározása céljából.

**Lemezek átlagos száma kompatibilis virtuális gépenként**: Az összes kompatibilis virtuális gépen számított lemezek átlagos száma.

**Átlagos lemez mérete (GB)** : Az összes kompatibilis virtuális gépen számított átlagos lemez mérete.

**Kívánt RPO (perc)** : Vagy az alapértelmezett helyreállítási időkorlát, vagy a jelentés létrehozásakor a "DesiredRPO" paraméternek átadott érték a szükséges sávszélesség becslése érdekében.

**Kívánt sávszélesség (Mbps)** : A "sávszélesség" paraméter számára a jelentés létrehozásakor átadott érték a rendelkezésre álló RPO becslése érdekében.

**Az átlagos adatváltozás napi száma (GB)** : Az összes profilkészítési napon megfigyelt átlagos adatváltozás. Ez a szám az egyik olyan bemenet, amely alapján a rendszer meghatározza az üzemelő példányban használandó konfigurációs kiszolgálók és további folyamatkiszolgálók számát.

## <a name="recommendations"></a>Javaslatok

A VMware – Azure jelentés javaslati táblázatában a következő részletek találhatók (a kiválasztott RPO szerint):

![VMware – Azure jelentéshez kapcsolódó javaslatok](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

### <a name="profiled-data"></a>Profilkészítés során létrehozott adatok
![A profilkészítés során létrehozott adatok nézete a Deployment Planner eszközben](media/site-recovery-vmware-deployment-planner-analyze-report/profiled-data-v2a.png)

A dokumentációban szereplő adatidőszak: A profilkészítés futtatásának időtartama. Az eszköz alapértelmezés szerint a profilkészítés során létrehozott minden adatot számításba vesz, kivéve, ha egy adott időszakról készít jelentést, és megadja a StartDate és EndDate beállításokat a jelentéskészítés során.

**Kiszolgáló neve**: Azon VMware vCenter vagy ESXi-gazdagép neve vagy IP-címe, amelynek a virtuális gépei jelentését létrehozták.

**Kívánt RPO**: Az üzemelő példány helyreállítási pontjának célja. Alapértelmezés szerint a szükséges hálózati sávszélesség kiszámítása 15, 30 és 60 perces RPO értékkel történik. A kiválasztástól függően az eszköz frissíti az érintett értékeket a munkalapon. Ha használta a *DesiredRPOinMin* paramétert a jelentés elkészítése közben, akkor az az érték jelenik meg a Kívánt helyreállítási időkorlát eredményei között.

### <a name="profiling-overview"></a>Profilkészítés áttekintése

![A profilkészítés eredményei a Deployment Planner eszközben](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-v2a.png)

A **Virtual Machines összesített száma**: Azoknak a virtuális gépeknek a száma, amelyeken a rendelkezésre álló adatmennyiség elérhető. Ha a VMListFile olyan virtuális gépek nevét is tartalmazza, amelyekről nem készült profil, a rendszer ezeket figyelmen kívül hagyja a jelentéskészítés során, és nem számítja bele őket a profilkészítésben részt vevő virtuális gépek teljes számába.

**Kompatibilis Virtual Machines**: Az Azure-ban Site Recovery használatával védhető virtuális gépek száma. Ez azon kompatibilis virtuális gépek teljes számát jelöli, amelyekhez a rendszer kiszámolta a szükséges hálózati sávszélességet, a tárfiókok, az Azure-magok, valamint a konfigurációs és további folyamatkiszolgálók számát. Az egyes kompatibilis virtuális gépek részletei elérhetők a „Compatible VMs” (Kompatibilis virtuális gépek) szakaszban.

Nem **kompatibilis Virtual Machines**: Azoknak a virtuális gépeknek a száma, amelyek nem kompatibilisek a Site Recoveryval való védelemhez. Az inkompatibilitás okait a „Nem kompatibilis virtuális gépek” című szakaszban olvashatja. Ha a VMListFile olyan virtuális gépek nevét is tartalmazza, amelyekről nem készült profil, a rendszer nem számítja bele őket a nem kompatibilis virtuális gépek számába. Az ilyen virtuális gépek az „Incompatible VMs” (Nem kompatibilis virtuális gépek) szakasz végén, „Data not found” (Nem található adat) megjelöléssel láthatók.

**Kívánt RPO**: A kívánt helyreállítási pont célkitűzése percben. A jelentés három RPO értéket generál: 15 (alapértelmezett), 30 és 60 perc. A jelentésben szereplő ajánlott sávszélesség attól függően változik, hogy melyik lehetőséget választja a munkalap jobb felső sarkában található Desired RPO (Kívánt RPO) legördülő listából. Ha egyéni értékű *-DesiredRPO* paraméterrel készítette el a jelentést, ez az egyéni érték jelenik meg alapértelmezett értékként a Kívánt helyreállítási időkorlát legördülő listában.

### <a name="required-network-bandwidth-mbps"></a>Szükséges hálózati sávszélesség (Mbps)

![Szükséges hálózati sávszélesség a Deployment Planner eszközben](media/site-recovery-vmware-deployment-planner-analyze-report/required-network-bandwidth-v2a.png)

**Az idő RPO 100 százaléka:** Az ajánlott sávszélesség (MB/s), amely az idő RPO 100 százalékára van lefoglalva. Ekkora sávszélességet kell kijelölnie az összes kompatibilis virtuális gép stabil állapotú változásreplikációjához, ha biztosan ki kívánja zárni a helyreállítási időkorlát megszegését.

**Az idő RPO 90 százaléka**: A szélessávú díjszabás vagy bármilyen más ok miatt, ha nem tudja beállítani az idő RPO 100 százalékához szükséges sávszélességet, dönthet úgy, hogy alacsonyabb sávszélesség-beállítást használ, amely megfelel a kívánt RPO 90 százalékának. Az alacsonyabb sávszélesség-beállítás következményeinek áttekintéséhez a jelentés lehetőségelemzést biztosít elemzést a helyreállítási időkorlát várható megszegéseinek számáról és időtartamáról.

**Elért átviteli sebesség:** Az átviteli sebesség azon a kiszolgálón, amelyen a GetThroughput parancsot futtatta a Storage-fiókot tartalmazó Microsoft Azure régióban. Ez az átvitelisebesség-érték azt a becsült szintet jelöli, amely akkor érhető el, ha a kompatibilis virtuális gépeket a Site Recoveryvel védi, feltéve, hogy a konfigurációs kiszolgáló vagy folyamatkiszolgáló tárolója és hálózati jellemzői megegyeznek azon kiszolgálóéval, amelyen az eszközt futtatta.

A replikációhoz a helyreállítási időkorlát 100 százalékos eléréséhez javasolt sávszélességet kell beállítania. Ha a sávszélesség beállítása után nem nő az eszköz által jelentett elért átviteli sebesség, tegye a következőket:

1. Ellenőrizze, hogy van-e olyan hálózati szolgáltatásminőség (QoS), amely korlátozná a Site Recovery átviteli sebességét.

2. Ellenőrizze, hogy a hálózati késés minimalizálása érdekében a Site Recovery-tároló a legközelebbi fizikai támogatott Microsoft Azure-régióban található-e.

3. Ellenőrizze a helyi tároló jellemzőit, és állapítsa meg, hogy fejleszthető-e a hardver (pl. váltás HDD-ről SSD-re).

4. Módosítsa a Site Recovery beállításait a folyamatkiszolgálón [a replikációhoz használt hálózati sávszélesség növelésével](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Ha az eszközt olyan konfigurációs kiszolgálón vagy folyamatkiszolgálón futtatja, amely már rendelkezik védett virtuális gépekkel, futtassa többször azt eszközt. Az elért átviteli sebesség eltérő lesz az aktuális adatforgalomtól függően.

A Site Recovery minden vállalati üzemelő példánya esetében az [ExpressRoute](https://aka.ms/expressroute) használata javasolt.

### <a name="required-storage-accounts"></a>Szükséges tárfiókok
A következő diagram az összes kompatibilis virtuális gép védelméhez szükséges (standard és prémium szintű) tárfiókok teljes számát mutatja. A „VM-storage placement” (Virtuálisgép-tároló elhelyezése) szakaszból megtudhatja, hogy melyik tárfiókot melyik virtuális géphez használhatja. Ha a Deployment Planner v 2.5-ös verziójával rendelkezik, ez a javaslat csak a replikáláshoz szükséges standard gyorsítótárbeli Storage-fiókok számát jeleníti meg, mivel az adatai közvetlenül Managed Disksba kerülnek.

![Szükséges tárfiókok a Deployment Planner eszközben](media/site-recovery-vmware-deployment-planner-analyze-report/required-storage-accounts-v2a.png)

### <a name="required-number-of-azure-cores"></a>Szükséges Azure-magok száma
Ez az eredmény az összes kompatibilis virtuális gép feladatátvétele vagy feladatátvételi tesztje előtt beállítandó magok teljes száma. Ha az előfizetés túl kevés maggal rendelkezik, a Site Recovery nem tudja létrehozni a virtuális gépeket a feladatátvételi teszt vagy a tényleges feladatátvétel alatt.

![Szükséges Azure-magok száma a Deployment Planner eszközben](media/site-recovery-vmware-deployment-planner-analyze-report/required-cores-v2a.png)

### <a name="required-on-premises-infrastructure"></a>Szükséges helyszíni infrastruktúra
Ez az adat az összes virtuális gép védelméhez szükséges konfigurálandó konfigurációs kiszolgálók és további folyamatkiszolgálók teljes száma. A [konfigurációs kiszolgáló támogatott méretére vonatkozó javaslatoktól](https://aka.ms/asr-v2a-on-prem-components) függően az eszköz további kiszolgálókat javasolhat. A javaslat a napi adatforgalmon vagy a védett virtuális gépek maximális számán alapul (virtuális gépenként átlagosan három lemezt feltételezve), ha eléri valamelyik korlátot a konfigurációs kiszolgálón vagy a további folyamatkiszolgálón. A napi összes adatváltozással és a védett lemezek teljes számával kapcsolatos adatok a „Helyszíni összefoglalás” szakaszban találhatók.

![Szükséges helyszíni infrastruktúra a Deployment Planner eszközben](media/site-recovery-vmware-deployment-planner-analyze-report/required-on-premises-components-v2a.png)

### <a name="what-if-analysis"></a>Lehetőségelemzés
Ez az elemzés kiemeli, hogy hányszor szegné meg a helyreállítási időkorlátot a profilkészítés időtartama alatt, ha alacsonyabb sávszélességet állít be, amely az idő 90 százalékában biztosítaná a kívánt RPO betartását. A helyreállítási időkorlát túllépése bármelyik nap egy vagy több alkalommal is előfordulhat. Az ábra a napi RPO-csúcsértéket mutatja.
Az elemzés alapján eldöntheti, hogy az RPO-túllépések teljes száma az összes nap alatt, valamint a napi RPO-csúcsérték elfogadható-e a megadott alacsonyabb sávszélesség esetén. Ha elfogadható, akkor lefoglalhatja az alacsonyabb sávszélességet a replikációhoz. Ha nem, akkor foglalja le a javasolt magasabb sávszélességet ahhoz, hogy az idő 100 százalékában betarthassa az RPO-t.

![Lehetőségelemzés a Deployment Planner eszközben](media/site-recovery-vmware-deployment-planner-analyze-report/what-if-analysis-v2a.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>A virtuálisgép-köteg ajánlott mérete a kezdeti replikációhoz
A szakaszban arra teszünk javaslatot, hogy hány virtuális gép védhető meg egyszerre úgy, hogy a kezdeti replikáció 72 órán belül befejeződjön a javasolt sávszélesség és a kívánt RPO 100 százalékos betartása mellett a beállítás során. Ezen érték konfigurálható. Módosításához használja a *GoalToCompleteIR* paramétert a jelentéskészítés során.

A diagram egy sávszélesség-tartományt és azt a virtuálisgép-köteg méretet mutatja, amellyel a kezdeti replikáció 72 órán belüli befejezhető. Ez a szám az összes kompatibilis virtuális gép és a virtuális gépek átlagos észlelt méretén alapul.

A nyilvános előzetes verzióban a jelentés nem határozza meg, hogy melyik virtuális gépeket foglalja bele egy kötegbe. A „Compatible VMs” (Kompatibilis virtuális gépek) szakaszban látható lemezméretek alapján kikeresheti az egyes virtuális gépek méretét, és kiválaszthatja az egyes kötegekhez tartozó virtuális gépeket, vagy választhat a számítási feladatok ismert jellemzői alapján is. A kezdeti replikáció befejezési ideje a virtuális gépek lemezeinek tényleges méretével, a felhasznált lemezterülettel és az elérhető hálózati átviteli sebességgel arányosan változik.

![A virtuálisgép-köteg ajánlott mérete](media/site-recovery-vmware-deployment-planner-analyze-report/ir-batching-v2a.png)

### <a name="cost-estimation"></a>Költségbecslés
Az ábra a választott célrégió és a jelentéskészítéshez megadott pénznem vészhelyreállításának (DR) becsült, teljes költségeinek összesített nézetét mutatja be az Azure-hoz.

![Költségbecslés összefoglalása](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

Az összefoglalás segít megismerni a tárolás, számítás, hálózat és licenc költségeit, amelyet akkor kell fizetnie, ha az Azure-ban lévő összes kompatibilis virtuális gépet az Azure Site Recovery használatával védi. A költségek kiszámítása a kompatibilis virtuális gépek, nem pedig a profilkészítésben részt vevő virtuális gépek alapján történik.  

A költségeket havi vagy éves bontásban tekintheti meg. További információkat olvashat a [támogatott célrégiókról](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) és a [támogatott pénznemekről](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).

**Cost by Components** A DR teljes költsége négy összetevőre oszlik: Számítási, tárolási, hálózati és Azure Site Recovery licencek díja. A költségeket a használat alapján számoljuk ki, amely költségek a replikáció során és a számítás, tárterület (prémium és standard), a helyszíni hely és az Azure között konfigurált ExpressRoute/VPN, valamint az Azure Site Recovery licence DR működési ideje során lépnek fel.

**Állapot szerinti költségek**: A vészhelyreállítás (DR) teljes költségeinek kategóriái két különböző állapoton alapulnak – Replikáció és DR működése.

**Replikálási díj**:  A replikáció során felmerülő költségek. Ez fedezi a tárolás, a hálózat és az Azure Site Recovery-licenc költségeit.

**Dr**működésének díja: A feladatátvételi teszt során felmerülő költségek. Az Azure Site Recovery virtuális gépeket indít el a feladatátvételi tesztek során. A DR működési költségei a futó virtuális gépek számítási és tárolási költségeit fedezik.

**Az Azure Storage tárterület éves/havi költségei** A tárterület teljes költségeit mutatja, amelyek a replikációhoz és a DR működésekor használt prémium és standard tárterület esetén merülnek fel.
A virtuális gépenkénti részletes költségelemzést a [Költségbecslés](site-recovery-vmware-deployment-planner-cost-estimation.md) táblázatban tekintheti meg.

### <a name="growth-factor-and-percentile-values-used"></a>A használt növekedési tényező és százalékértékek
A munkalap alján található szakasz a profilkészítésben részt vevő virtuális gépek összes teljesítményszámlálójához használt százalékértéket (az alapértelmezett érték 95 százalék), valamint az összes számításban használt növekedési tényezőt (az alapértelmezett érték 30 százalék) mutatja.

![A használt növekedési tényező és százalékértékek](media/site-recovery-vmware-deployment-planner-analyze-report/growth-factor-v2a.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Javaslatok az elérhető sávszélesség bemenetként való megadásával

![Javaslatok az elérhető sávszélesség bemenetként való megadásával](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-bandwidth-input-v2a.png)

Előfordulhat olyan helyzet, hogy legfeljebb x Mbps sávszélességet tud beállítani a Site Recovery replikációjára. Az eszközzel megadhatja a rendelkezésre álló sávszélességet (a jelentéskészítés közben, a -Bandwidth paraméterrel), és megkapja az elérhető RPO-t percben megadva. Az elérhető RPO-értéknek ismeretében eldöntheti, hogy további sávszélességet kell beállítania, vagy elfogadja a vészhelyreállítási megoldást ezzel az RPO-val.

![Elérhető RPO 500 Mbps sávszélességhez](media/site-recovery-vmware-deployment-planner-analyze-report/achievable-rpo-v2a.png)

## <a name="vm-storage-placement"></a>Virtuálisgép-tároló elhelyezése

>[!Note]
>A Deployment Planner v 2.5-ös verzióban azt ajánljuk, hogy a tárolók elhelyezése olyan gépek esetében, amelyek közvetlenül a felügyelt lemezekre replikálódnak.

![Virtuálisgép-tároló elhelyezése](media/site-recovery-vmware-deployment-planner-analyze-report/vm-storage-placement-v2a.png)

**Replikációs tár típusa**: Standard vagy prémium szintű felügyelt lemez, amely a **virtuális gépekről** az oszlopba helyezendő összes megfelelő virtuális gép replikálására szolgál.

**Log Storage-fiók típusa**: Az összes replikációs naplót egy standard Storage-fiók tárolja.

**A Storage-fiók javasolt előtagja**: A javasolt három karakterből álló előtag, amely a cache Storage-fiók elnevezésére használható. Saját előtagot is használhat, de az eszköz által javasolt nevek követik a [tárfiókok partíció-elnevezési szabályait](https://aka.ms/storage-performance-checklist).

**Javasolt bejelentkezési fiók neve**: A tárolási fiók neve, miután belefoglalta a javasolt előtagot. Cserélje le a csúcsos zárójelben (< és >) lévő nevet a kívánt értékre.

**Elhelyezés összegzése**: A virtuális gépeknek a tárolási típus szerint történő védelmét igénylő lemezek összefoglalása. Magában foglalja a virtuális gépek teljes számát, a teljes kiosztott méretet az összes lemezen, valamint a lemezek teljes számát.

**Virtual Machines a következő helyre**: Az összes olyan virtuális gép listája, amelyet az optimális teljesítmény és használat érdekében a megadott Storage-fiókba kell helyezni.

## <a name="compatible-vms"></a>Kompatibilis virtuális gépek
![A kompatibilis virtuális gépek Excel-táblázata](media/site-recovery-vmware-deployment-planner-analyze-report/compatible-vms-v2a.png)

**Virtuális gép neve**: A VMListFile használt virtuális gép neve vagy IP-címe jelentés létrehozásakor. Ez az oszlop a virtuális gépekhez csatolt lemezek (VMDK-k) listáját is megjeleníti. Az ismétlődő nevű vagy IP-című vCenter-beli virtuális gépek megkülönböztetésére a nevek tartalmazzák az ESXi-gazdagépnevet is. A feltüntetett ESXi-gazdagép az a számítógép, ahol a virtuális gép megtalálható volt, amikor az eszköz először felderítette azt a profilkészítés során.

**Virtuális**gépek kompatibilitása: Az értékek **Igen** és **Igen**\*. Igen\* olyan példányok esetén, amelyekben a virtuális gép a [prémium SSD](../virtual-machines/windows/disks-types.md)-k számára alkalmas. Itt a profilkészítés során megállapított magas adatváltozású vagy IOPS-értékű lemez megfelel a P20-as vagy P30-as kategóriának, de a lemez mérete miatt a rendszer P10-es vagy P20-as kategóriába sorolja be. A tárfiók a lemez mérete alapján dönti el, hogy melyik prémium szintű lemeztípushoz rendelje hozzá a lemezt. Példa:
* 128 GB alatt P10.
* 128 GB és 256 GB között P15
* 256 GB és 512 GB között P20.
* 512 GB és 1024 GB között P30.
* 1025 GB és 2048 GB között P40.
* 2049 GB és 4095 GB között P50.

Ha például a számítási feladatok jellemzői alapján egy lemez a P20-as vagy P30-as kategóriába tartozik, de a mérete alapján egy alacsonyabb prémium szintű tárolólemez-típusba kerül, az eszköz **Yes**\* (Igen) megjelöléssel látja el ezeket a virtuális gépeket. Az eszköz azt is javasolja, hogy módosítsa a forráslemez méretét, hogy a lemez megfeleljen az ajánlott prémium szintű tárolólemez-típusnak, vagy hogy módosítsa a céllemez típusát a feladatátvétel után.

**Tárolási típus**: Standard vagy prémium.

**Replikációhoz létrehozott Asrseeddisk (felügyelt lemez)** : A replikáció engedélyezésekor létrehozott lemez neve. Az Azure-ban tárolja az adattárakat és annak pillanatképeit.

**Maximális R/W IOPS (növekedési faktor)** : A maximális számítási feladatok olvasási/írási IOPS a lemezen (az alapértelmezett érték a 95. percentilis), beleértve a jövőbeli növekedési tényezőt (az alapértelmezett érték 30 százalék). Vegye figyelembe, hogy egy virtuális gép teljes írási/olvasási IOPS-értéke nem mindig egyezik meg az egyes virtuálisgép-lemezek írási/olvasási IOPS-értékeinek összegével. Ez azért van, mert a virtuális gép írási/olvasási IOPS-csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért írási/olvasási IOPS-értékek legmagasabb összege.

**Adatforgalom maximális kihasználtsága (MB/s) (növekedési tényezővel)** : A lemezen lévő maximális adatváltozási arány (az alapértelmezett érték a 95. percentilis), beleértve a jövőbeli növekedési tényezőt (az alapértelmezett érték 30 százalék). Vegye figyelembe, hogy a virtuális gép teljes adatváltozása nem mindig egyezik meg az egyes lemezek adatváltozásának összegével. Ez azért van, mert a virtuális gép adatváltozásának csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért adatváltozás-értékek legmagasabb összege.

**Azure-beli virtuális gép mérete**: A helyszíni virtuális gép ideális feltérképezett Azure Cloud Services virtuálisgép-mérete. A hozzárendelés a helyszíni virtuális gép memóriáján, a lemezek/magok/hálózati adapterek számán és az olvasási/írási IOPS-értéken alapul. Mindig a legalacsonyabb, a helyszíni virtuális gép összes jellemzőjének megfelelő Azure-beli virtuálisgép-méretet ajánlott használni.

**Lemezek száma**: A virtuálisgép-lemezek (VMDK) teljes száma a virtuális gépen.

**Lemez mérete (GB)** : A virtuális gép összes lemezének teljes telepítési mérete. Az eszköz a virtuális gép egyes lemezeinek méretét is megjeleníti.

**Magok**: A virtuális gépen található CPU-magok száma.

**Memória (MB)** : A virtuális gép MEMÓRIÁja.

**Hálózati adapterek**: A virtuális gépen lévő hálózati adapterek száma.

**Rendszerindítás típusa**: A virtuális gép rendszerindítási típusa. Ez BIOS vagy EFI lehet.  Jelenleg az Azure Site Recovery támogatja a Windows Server EFI típusú virtuális gépeket (Windows Server 2012, 2012 R2 és 2016) abban az esetben, ha a rendszerindító lemez partícióinak száma kevesebb mint 4, a rendszerindító szektor mérete pedig 512 bájt. Az EFI típusú virtuális gépek védelme érdekében az Azure Site Recovery mobilitási szolgáltatás verziójának legalább 9.13-asnak kell lennie. Az EFI virtuális gépek esetében kizárólag a feladatátvétel támogatott. A feladat-visszavétel nem támogatott.  

**Operációs rendszer típusa**: Ez a virtuális gép operációs rendszerének típusa. Ennek értéke Windows, Linux vagy egyéb lehet a virtuális gép létrehozása során a VMware vSphere használatával kiválasztott sablon alapján.  

## <a name="incompatible-vms"></a>Nem kompatibilis virtuális gépek

![A nem kompatibilis virtuális gépek Excel-táblázata
](media/site-recovery-vmware-deployment-planner-analyze-report/incompatible-vms-v2a.png)

**Virtuális gép neve**: A VMListFile használt virtuális gép neve vagy IP-címe jelentés létrehozásakor. Ez az oszlop a virtuális gépekhez csatolt VMDK-k listáját is megjeleníti. Az ismétlődő nevű vagy IP-című vCenter-beli virtuális gépek megkülönböztetésére a nevek tartalmazzák az ESXi-gazdagépnevet is. A feltüntetett ESXi-gazdagép az a számítógép, ahol a virtuális gép megtalálható volt, amikor az eszköz először felderítette azt a profilkészítés során.

**Virtuális**gépek kompatibilitása: Azt jelzi, hogy a megadott virtuális gép miért nem kompatibilis a Site Recoveryval való használattal. Az indokok a virtuális gép minden nem kompatibilis lemezénél vannak megadva, és a közzétett [tárhelykorlátok](https://aka.ms/azure-storage-scalbility-performance) alapján a következők lehetnek:

* A lemez mérete nagyobb 4095 GB-nál. Az Azure Storage jelenleg nem támogatja a 4095 GB-nál nagyobb adatlemez-méretet.

* Az operációsrendszer-lemez mérete nagyobb 2048 GB-nál. Az Azure Storage jelenleg nem támogatja a 2048 GB-nál nagyobb méretű operációsrendszer-lemezeket.

* A virtuális gép teljes mérete (replikáció + TFO) meghaladja a támogatott tárfiók méretkorlátozását (35 TB). Ez a fajta inkompatibilitás általában akkor fordul elő, ha a virtuális gép egy lemezének egyik teljesítményjellemzője meghaladja a támogatott standard szintű tárolóra vonatkozó Azure- vagy Site Recovery-korlátozásokat. Egy ilyen példány a prémium szintű tárolózónába kényszeríti a virtuális gépet. A prémium szintű tárfiókok maximális támogatott mérete azonban 35 TB, és egyetlen védett virtuális gép nem védhető több tárfiókon keresztül. Ügyeljen arra is, hogy amikor a feladatátvételi tesztet védett virtuális gépen hajtja végre, akkor az ugyanazon a tárfiókon fut, ahol a replikáció folyik. Ezen a példányon a lemez méretének kétszeresét kell kiosztani, hogy a replikáció is folytatódhasson, és ezzel párhuzamosan a feladatátvételi teszt is sikeres legyen.

* A forrás IOPS-érték meghaladja a tároló lemezenkénti 7500-as IOPS-korlátját.

* A forrás IOPS-érték meghaladja a tároló virtuális gépenkénti 80 000-es IOPS-korlátját.

* Az átlagos adatváltozás meghaladja a támogatott Site Recovery adatváltozási korlátot 20 MB/s értékre a lemez átlagos I/O-méretéhez.

* Az átlagos adatváltozás meghaladja a Site Recovery virtuális gépenkénti átlagos I/O-mérethez megadott 25 MB/s értékű adatváltozási korlátját (az összes lemez adatváltozása együtt).

* Az összes virtuális gépre vonatkozó adatváltozás csúcsértéke meghaladja a Site Recovery virtuális gépenkénti 54 Mb/s-os támogatott adatváltozási csúcsérték korlátját.

* Az átlagos tényleges írási IOPS-érték meghaladja a Site Recovery lemezenkénti 840-es támogatott IOPS-korlátját.

* A kiszámított pillanatkép-tároló mérete meghaladja a 10 TB-os támogatott méretet.

* A napi adatváltozás túllépi a folyamatkiszolgálók által támogatott napi 2 TB-os keretet.


**Maximális R/W IOPS (növekedési faktor)** : A maximális munkaterhelés IOPS (az alapértelmezett érték a 95. percentilis), beleértve a jövőbeli növekedési tényezőt (az alapértelmezett érték 30 százalék). Vegye figyelembe, hogy a virtuális gép teljes írási/olvasási IOPS-értéke nem mindig egyezik meg az egyes virtuálisgép-lemezek írási/olvasási IOPS-értékeinek összegével. Ez azért van, mert a virtuális gép írási/olvasási IOPS-csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért írási/olvasási IOPS-értékek legmagasabb összege.

**Adatforgalom maximális kihasználtsága (MB/s) (növekedési tényezővel)** : A lemezen lévő maximális adatváltozási arány (az alapértelmezett 95. percentilis), beleértve a jövőbeli növekedési tényezőt (az alapértelmezett érték 30 százalék). Vegye figyelembe, hogy a virtuális gép teljes adatváltozása nem mindig egyezik meg az egyes lemezek adatváltozásának összegével. Ez azért van, mert a virtuális gép adatváltozásának csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért adatváltozás-értékek legmagasabb összege.

**Lemezek száma**: A virtuális gép VMDK teljes száma.

**Lemez mérete (GB)** : A virtuális gép összes lemezének teljes telepítési mérete. Az eszköz a virtuális gép egyes lemezeinek méretét is megjeleníti.

**Magok**: A virtuális gépen található CPU-magok száma.

**Memória (MB)** : A virtuális gépen található RAM mennyisége.

**Hálózati adapterek**: A virtuális gépen lévő hálózati adapterek száma.

**Rendszerindítás típusa**: A virtuális gép rendszerindítási típusa. Ez BIOS vagy EFI lehet.  Jelenleg az Azure Site Recovery támogatja a Windows Server EFI típusú virtuális gépeket (Windows Server 2012, 2012 R2 és 2016) abban az esetben, ha a rendszerindító lemez partícióinak száma kevesebb mint 4, a rendszerindító szektor mérete pedig 512 bájt. Az EFI típusú virtuális gépek védelme érdekében az Azure Site Recovery mobilitási szolgáltatás verziójának legalább 9.13-asnak kell lennie. Az EFI virtuális gépek esetében kizárólag a feladatátvétel támogatott. A feladat-visszavétel nem támogatott.

**Operációs rendszer típusa**:  Ez a virtuális gép operációs rendszerének típusa. Ennek értéke Windows, Linux vagy egyéb lehet a virtuális gép létrehozása során a VMware vSphere használatával kiválasztott sablon alapján.

## <a name="azure-site-recovery-limits"></a>Az Azure Site Recovery korlátai
Az alábbi táblázat az Azure Site Recovery korlátait tartalmazza. Ezek a korlátok a saját tesztjeinken alapulnak, de nem fedhetik le az alkalmazások minden lehetséges I/O-kombinációját. A tényleges eredmények a saját alkalmazásának I/O-műveletei alapján változhatnak. A legjobb eredmények érdekében még az üzembe helyezés megtervezése után is ajánlott az alkalmazás alapos tesztelése feladatátvételi tesztek használatával, így valós képet kaphat az alkalmazás teljesítményéről.

**Replikáció tárolási célja** | **Forráslemez átlagos I/O-mérete** |**Forráslemez átlagos adatváltozása** | **Forráslemez teljes napi adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 8 KB  | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 16 KB | 4 MB/s |  Lemezenként 336 GB
Prémium szintű P10 vagy P15 lemez | 32 KB vagy több | 8 MB/s | Lemezenként 672 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 8 KB    | 5 MB/s | Lemezenként 421 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 16 KB vagy több | 20 MB/s | 1684 GB/lemez

**Forrásadat-változás** | **Felső korlát**
---|---
Átlagos adatváltozás virtuális gépenként| 25 MB/s
Adatváltozás csúcsértéke az összes lemezen virtuális gépenként | 54 MB/s
Folyamatkiszolgáló által támogatott napi adatváltozás maximuma | 2 TB

Ezek átlagos értékek, amelyek 30 százalékos I/O-átfedést feltételeznek. A Site Recovery képes magasabb átviteli sebesség kezelésére az átfedési arány, a nagyobb írási méretek és a számítási feladatok tényleges I/O-viselkedése alapján. Az előbbi számok egy általános, körülbelül ötperces várólistát feltételeznek. Ez azt jelenti, hogy a feltöltést követő öt percben megtörténik az adat feldolgozása, és létrejön egy helyreállítási pont.


## <a name="cost-estimation"></a>Költségbecslés
További információk a [költségbecslésről](site-recovery-vmware-deployment-planner-cost-estimation.md).


## <a name="next-steps"></a>További lépések
További információk a [költségbecslésről](site-recovery-vmware-deployment-planner-cost-estimation.md).
