---
title: Vész-helyreállítási VMware Azure-bA az Azure Site Recovery Deployment Planner jelentés elemzése |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Site Recovery Deployment Planner a VMware-vészhelyreállításhoz az Azure-bA a létrehozott jelentés elemzése.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/14/2019
ms.author: mayg
ms.openlocfilehash: cd486fa504ac819684d8c547e7a0f740b3eed4e4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58109627"
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report-for-vmware-disaster-recovery-to-azure"></a>A VMware-vészhelyreállításhoz az Azure-bA az Azure Site Recovery Deployment Planner jelentés elemzése

Az elkészített Microsoft Excel-jelentés a következő táblázatokat tartalmazza:
## <a name="on-premises-summary"></a>Helyszíni összefoglalás
A Helyszíni összefoglalás munkalap áttekintést nyújt arról a VMware-környezetről, amelyről profilt készített.

![A VMware-környezet helyszíni összefoglalása](media/site-recovery-vmware-deployment-planner-analyze-report/on-premises-summary-v2a.png)

**Kezdő dátum** és **befejező dátum**: A jelentés létrehozásához tekinthető profilkészítési adatok kezdő és záró dátuma. Alapértelmezés szerint a kezdő dátum az a dátum, amikor a profilkészítés megkezdődik, a záró dátum pedig az a dátum, amikor a profilkészítés leáll. Ezek lehetnek a StartDate és EndDate értékek, ha a jelentést ezekkel a paraméterekkel hozza létre.

**Profilkészítési napok teljes száma**: A jelentéskészítés kezdő és záró dátuma között eltelt profilkészítési napok teljes száma.

**Kompatibilis virtuális gépek száma**: A szükséges hálózati sávszélességet, a storage-fiókok, Microsoft Azure magok, konfigurációs kiszolgálók és további folyamatkiszolgálók szükséges számú számított, amelynek kompatibilis virtuális gépek teljes száma.

**Az összes kompatibilis virtuális gépre kiterjedően lemezek teljes számának**: A szám, amellyel a bemeneti adatok egyik konfigurációs kiszolgálók és az üzemelő példányban használandó további folyamatkiszolgálók számát.

**Átlagos száma kompatibilis virtuális gépenként lemezek**: Minden kompatibilis virtuális gépen alapján számított átlagos száma.

**Átlagos lemezméret (GB)**: Az átlagos alapján számított átlagos lemezméret az összes kompatibilis virtuális gépek.

**Desired RPO (perc)**: Vagy az alapértelmezett helyreállításipont-célkitűzés a értéke vagy a "DesiredRPO" paraméterhez szükséges sávszélesség becslése érdekében a jelentés elkészítésekor.

**Kívánt sávszélesség (Mbps)**: A megadott érték rendelkezik a "Bandwidth" paraméterhez a jelentéskészítéskor az elérhető RPO becslése érdekében idején.

**Megfigyelt átlagos adatváltozás naponta (GB)**: Az összes profilkészítési napon megfigyelt átlagos adatváltozás. Ez a szám az egyik olyan bemenet, amely alapján a rendszer meghatározza az üzemelő példányban használandó konfigurációs kiszolgálók és további folyamatkiszolgálók számát.

## <a name="recommendations"></a>Javaslatok

>[!Note]
>A felügyelt lemezek esetében, amelyek közvetlenül, ha figyelmen kívül a javaslat a tárfiókok száma.

A VMware – Azure jelentés javaslati táblázatában a következő részletek találhatók (a kiválasztott RPO szerint):

![VMware – Azure jelentéshez kapcsolódó javaslatok](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

### <a name="profiled-data"></a>Profilkészítés során létrehozott adatok
![A profilkészítés során létrehozott adatok nézete a Deployment Planner eszközben](media/site-recovery-vmware-deployment-planner-analyze-report/profiled-data-v2a.png)

**Ekkor a profiladatok időszak**: Az időtartam, ameddig a profilkészítés futtatásának. Az eszköz alapértelmezés szerint a profilkészítés során létrehozott minden adatot számításba vesz, kivéve, ha egy adott időszakról készít jelentést, és megadja a StartDate és EndDate beállításokat a jelentéskészítés során.

**Kiszolgálónév**: A név vagy IP-címét a VMware vCenter vagy ESXi-gazdagép, amelyek virtuális gépeiről jelentés készül.

**Desired RPO**: A helyreállítási időkorlát az üzembe helyezéshez. Alapértelmezés szerint a szükséges hálózati sávszélesség kiszámítása 15, 30 és 60 perces RPO értékkel történik. A kiválasztástól függően az eszköz frissíti az érintett értékeket a munkalapon. Ha használta a *DesiredRPOinMin* paramétert a jelentés elkészítése közben, akkor az az érték jelenik meg a Kívánt helyreállítási időkorlát eredményei között.

### <a name="profiling-overview"></a>Profilkészítés áttekintése

![A profilkészítés eredményei a Deployment Planner eszközben](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-v2a.png)

**Összesen a profilkészítés során létrehozott virtuális gépek**: Virtuális gépek, amelyek profilkészítés során létrehozott adatok érhetők el teljes száma. Ha a VMListFile olyan virtuális gépek nevét is tartalmazza, amelyekről nem készült profil, a rendszer ezeket figyelmen kívül hagyja a jelentéskészítés során, és nem számítja bele őket a profilkészítésben részt vevő virtuális gépek teljes számába.

**Kompatibilis virtuális gépek**: Az Azure Site Recovery használatával védhető virtuális gépek számát. Ez azon kompatibilis virtuális gépek teljes számát jelöli, amelyekhez a rendszer kiszámolta a szükséges hálózati sávszélességet, a tárfiókok, az Azure-magok, valamint a konfigurációs és további folyamatkiszolgálók számát. Az egyes kompatibilis virtuális gépek részletei elérhetők a „Compatible VMs” (Kompatibilis virtuális gépek) szakaszban.

**Nem kompatibilis virtuális gépek**: A profilkészítés során létrehozott virtuális gépek, amelyek nem védhetők Site recoveryvel száma. Az inkompatibilitás okait a „Nem kompatibilis virtuális gépek” című szakaszban olvashatja. Ha a VMListFile olyan virtuális gépek nevét is tartalmazza, amelyekről nem készült profil, a rendszer nem számítja bele őket a nem kompatibilis virtuális gépek számába. Az ilyen virtuális gépek az „Incompatible VMs” (Nem kompatibilis virtuális gépek) szakasz végén, „Data not found” (Nem található adat) megjelöléssel láthatók.

**Desired RPO**: A kívánt helyreállítási pont célkitűzés, percek alatt. A jelentés három helyreállítási Időkorlát értékről jön létre: 15 (alapértelmezett), 30 és 60 perc. A jelentésben szereplő ajánlott sávszélesség attól függően változik, hogy melyik lehetőséget választja a munkalap jobb felső sarkában található Desired RPO (Kívánt RPO) legördülő listából. Ha egyéni értékű *-DesiredRPO* paraméterrel készítette el a jelentést, ez az egyéni érték jelenik meg alapértelmezett értékként a Kívánt helyreállítási időkorlát legördülő listában.

### <a name="required-network-bandwidth-mbps"></a>Szükséges hálózati sávszélesség (Mbps)

![Szükséges hálózati sávszélesség a Deployment Planner eszközben](media/site-recovery-vmware-deployment-planner-analyze-report/required-network-bandwidth-v2a.png)

**Az RPO az idő 100 százalékában betarthassa:** Az ajánlott sávszélesség (Mbps), amelyet ki a kívánt RPO az idő 100 százalékában betarthassa. Ekkora sávszélességet kell kijelölnie az összes kompatibilis virtuális gép stabil állapotú változásreplikációjához, ha biztosan ki kívánja zárni a helyreállítási időkorlát megszegését.

**A replikációhoz Időkorlát az idő 90 százalékában**: Szélessávú díjszabása miatt vagy egyéb okból nem lehet beállítani a sávszélességet a kívánt RPO az idő 100 százalékában is megadhatja egy alacsonyabb sávszélesség-beállítást, amely megfelel a kívánt RPO az idő 90 százalékában betarthatja. Az alacsonyabb sávszélesség-beállítás következményeinek áttekintéséhez a jelentés lehetőségelemzést biztosít elemzést a helyreállítási időkorlát várható megszegéseinek számáról és időtartamáról.

**Az elért átviteli sebesség:** Az átviteli sebesség a kiszolgálón, amelyen a GetThroughput parancs futtatásához, a Microsoft Azure-régió a storage-fiók. Ez az átvitelisebesség-érték azt a becsült szintet jelöli, amely akkor érhető el, ha a kompatibilis virtuális gépeket a Site Recoveryvel védi, feltéve, hogy a konfigurációs kiszolgáló vagy folyamatkiszolgáló tárolója és hálózati jellemzői megegyeznek azon kiszolgálóéval, amelyen az eszközt futtatta.

A replikációhoz a helyreállítási időkorlát 100 százalékos eléréséhez javasolt sávszélességet kell beállítania. Ha a sávszélesség beállítása után nem nő az eszköz által jelentett elért átviteli sebesség, tegye a következőket:

1. Ellenőrizze, hogy van-e olyan hálózati szolgáltatásminőség (QoS), amely korlátozná a Site Recovery átviteli sebességét.

2. Ellenőrizze, hogy a hálózati késés minimalizálása érdekében a Site Recovery-tároló a legközelebbi fizikai támogatott Microsoft Azure-régióban található-e.

3. Ellenőrizze a helyi tároló jellemzőit, és állapítsa meg, hogy fejleszthető-e a hardver (pl. váltás HDD-ről SSD-re).

4. Módosítsa a Site Recovery beállításait a folyamatkiszolgálón [a replikációhoz használt hálózati sávszélesség növelésével](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Ha az eszközt olyan konfigurációs kiszolgálón vagy folyamatkiszolgálón futtatja, amely már rendelkezik védett virtuális gépekkel, futtassa többször azt eszközt. Az elért átviteli sebesség eltérő lesz az aktuális adatforgalomtól függően.

A Site Recovery minden vállalati üzemelő példánya esetében az [ExpressRoute](https://aka.ms/expressroute) használata javasolt.

### <a name="required-storage-accounts"></a>Szükséges tárfiókok
A következő diagram az összes kompatibilis virtuális gép védelméhez szükséges (standard és prémium szintű) tárfiókok teljes számát mutatja. A „VM-storage placement” (Virtuálisgép-tároló elhelyezése) szakaszból megtudhatja, hogy melyik tárfiókot melyik virtuális géphez használhatja.

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

**Összetevők szerinti költségek** a Vészhelyreállítás teljes költsége négy összetevőből áll: Számítási, tárolási, hálózati és az Azure Site Recovery licencköltségei. A költségeket a használat alapján számoljuk ki, amely költségek a replikáció során és a számítás, tárterület (prémium és standard), a helyszíni hely és az Azure között konfigurált ExpressRoute/VPN, valamint az Azure Site Recovery licence DR működési ideje során lépnek fel.

**Állapot szerinti költségek**: A vészhelyreállítás (DR) teljes költségeinek kategóriái két különböző állapoton alapulnak – Replikáció és DR működése.

**Replikáció költségei**:  A replikáció során felmerülő költségek. Ez fedezi a tárolás, a hálózat és az Azure Site Recovery-licenc költségeit.

**Dr költsége**: A tesztek során felmerülő költségek. Az Azure Site Recovery virtuális gépeket indít el a feladatátvételi tesztek során. A DR működési költségei a futó virtuális gépek számítási és tárolási költségeit fedezik.

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
>A felügyelt lemezek esetében, amelyek közvetlenül, ha nem kell aggódnia a tárfiókok száma. A storage szolgáltatás esetében használja csak a javaslat a tárolótípus (Standard vagy prémium). Az azonos típusú alkalmazható a felügyelt lemezek.

![Virtuálisgép-tároló elhelyezése](media/site-recovery-vmware-deployment-planner-analyze-report/vm-storage-placement-v2a.png)

**Lemezes tárolás típusa**: Vagy standard vagy prémium szintű storage-fiók, amelyet említett összes megfelelő virtuális gépek replikálása az **VMs to Place** oszlop.

**A javasolt előtag**: A javasolt háromkarakteres előtag használható a storage-fiók elnevezéséhez. Saját előtagot is használhat, de az eszköz által javasolt nevek követik a [tárfiókok partíció-elnevezési szabályait](https://aka.ms/storage-performance-checklist).

**Javasolt fióknév**: A storage-fiók neve, a javasolt előtag használata után. Cserélje le a csúcsos zárójelben (< és >) lévő nevet a kívánt értékre.

**Log Storage-fiók**: Minden replikációs naplót egy standard szintű tárfiókot vannak tárolva. A prémium szintű tárfiókokba replikált virtuális gépek esetében be kell állítani egy további standard szintű tárfiókot a naplók tárolására. Egyetlen standard szintű naplótárolási fiókot több prémium szintű replikációs tárfiók is használhat. A standard szintű tárfiókokba replikált virtuális gépek ugyanazt a tárfiókot használják a naplókhoz is.

**Tárfiók neve a javasolt**: A naplókat tároló tárfiók neve, a javasolt előtag használata után. Cserélje le a csúcsos zárójelben (< és >) lévő nevet a kívánt értékre.

**Elhelyezés összegzése**: A teljes virtuális gépek összegzését betölteni a tárfiók a replikáció során, és a feladatátvételi teszt vagy tényleges. Tartalmazza a tárfiókhoz rendelt virtuális gépek teljes számát, az Azure Storage-fiókba elhelyezni kívánt összes virtuális gép összesített olvasási/írási IOPS-értékét, az összesített írási (replikációs) IOPS-értéket, az összes lemez teljes beállítási méretét, valamint a lemezek teljes számát.

**Virtuális gépek helyen**: Egy lista összes virtuális gép, amelyet az optimális teljesítmény és használat az adott tárfiókon ajánlott elhelyezni.

## <a name="compatible-vms"></a>Kompatibilis virtuális gépek
![A kompatibilis virtuális gépek Excel-táblázata](media/site-recovery-vmware-deployment-planner-analyze-report/compatible-vms-v2a.png)

**Virtuális gép neve**: A virtuális gép neve vagy a jelentés létrehozásakor a VMListFile-ban használt IP-címet. Ez az oszlop a virtuális gépekhez csatolt lemezek (VMDK-k) listáját is megjeleníti. Az ismétlődő nevű vagy IP-című vCenter-beli virtuális gépek megkülönböztetésére a nevek tartalmazzák az ESXi-gazdagépnevet is. A feltüntetett ESXi-gazdagép az a számítógép, ahol a virtuális gép megtalálható volt, amikor az eszköz először felderítette azt a profilkészítés során.

**VM Compatibility**: Értékek a következők **Igen** és **Igen**\*. **Igen** \* a példányokra, amelyben a virtuális gép megfelel egy [prémium szintű SSD-k](../virtual-machines/windows/disks-types.md). Itt a profilkészítés során megállapított magas adatváltozású vagy IOPS-értékű lemez megfelel a P20-as vagy P30-as kategóriának, de a lemez mérete miatt a rendszer P10-es vagy P20-as kategóriába sorolja be. A tárfiók a lemez mérete alapján dönti el, hogy melyik prémium szintű lemeztípushoz rendelje hozzá a lemezt. Példa:
* 128 GB alatt P10.
* 128 GB és 256 GB között P15
* 256 GB és 512 GB között P20.
* 512 GB és 1024 GB között P30.
* 1025 GB és 2048 GB között P40.
* 2049 GB és 4095 GB között P50.

Ha például a számítási feladatok jellemzői alapján egy lemez a P20-as vagy P30-as kategóriába tartozik, de a mérete alapján egy alacsonyabb prémium szintű tárolólemez-típusba kerül, az eszköz **Yes**\* (Igen) megjelöléssel látja el ezeket a virtuális gépeket. Az eszköz azt is javasolja, hogy módosítsa a forráslemez méretét, hogy a lemez megfeleljen az ajánlott prémium szintű tárolólemez-típusnak, vagy hogy módosítsa a céllemez típusát a feladatátvétel után.

**Tárolási típus**: Standard vagy prémium.

**A javasolt előtag**: A tárfiók három karakterből álló előtagja.

**Storage-fiók**: A javasolt tárfiókelőtagot használó név.

**R/W IOPS (növekedési tényezővel)-mel kiugró**: A maximális írási/olvasási iops-t a lemezen (alapértelmezés szerint a 95. percentilis), beleértve a későbbi növekedési faktort is (az alapértelmezett érték 30 százalék). Vegye figyelembe, hogy egy virtuális gép teljes írási/olvasási IOPS-értéke nem mindig egyezik meg az egyes virtuálisgép-lemezek írási/olvasási IOPS-értékeinek összegével. Ez azért van, mert a virtuális gép írási/olvasási IOPS-csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért írási/olvasási IOPS-értékek legmagasabb összege.

**Peak Data Churn (Mbps) (a növekedési tényezővel)**: A lemez adatváltozásának csúcsértéke (alapértelmezés szerint a 95. percentilis), beleértve a későbbi növekedési faktort is (az alapértelmezett érték 30 százalék). Vegye figyelembe, hogy a virtuális gép teljes adatváltozása nem mindig egyezik meg az egyes lemezek adatváltozásának összegével. Ez azért van, mert a virtuális gép adatváltozásának csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért adatváltozás-értékek legmagasabb összege.

**Az Azure Virtuálisgép-méret**: A hozzárendelt Azure Cloud Services virtuális-gép ideális mérete a helyszíni virtuális géphez. A hozzárendelés a helyszíni virtuális gép memóriáján, a lemezek/magok/hálózati adapterek számán és az olvasási/írási IOPS-értéken alapul. Mindig a legalacsonyabb, a helyszíni virtuális gép összes jellemzőjének megfelelő Azure-beli virtuálisgép-méretet ajánlott használni.

**Lemezek száma**: A virtuális gépen a virtuális gép lemezeinek (vmdk-inak) teljes száma.

**Lemezméret (GB)**: A virtuális gép összes lemezének teljes beállítási mérete. Az eszköz a virtuális gép egyes lemezeinek méretét is megjeleníti.

**Magok**: A virtuális gép processzormagjainak száma.

**Memória (MB)**: A virtuális gép RAM.

**Hálózati adapterek**: A virtuális gép hálózati adaptereinek száma.

**Rendszerindítás-típus**: A virtuális gép rendszerindítási típusa. Ez BIOS vagy EFI lehet.  Jelenleg az Azure Site Recovery támogatja a Windows Server EFI típusú virtuális gépeket (Windows Server 2012, 2012 R2 és 2016) abban az esetben, ha a rendszerindító lemez partícióinak száma kevesebb mint 4, a rendszerindító szektor mérete pedig 512 bájt. Az EFI típusú virtuális gépek védelme érdekében az Azure Site Recovery mobilitási szolgáltatás verziójának legalább 9.13-asnak kell lennie. Az EFI virtuális gépek esetében kizárólag a feladatátvétel támogatott. A feladat-visszavétel nem támogatott.  

**Operációs rendszer típusa**: A virtuális gép operációs rendszerének típusa. Ennek értéke Windows, Linux vagy egyéb lehet a virtuális gép létrehozása során a VMware vSphere használatával kiválasztott sablon alapján.  

## <a name="incompatible-vms"></a>Nem kompatibilis virtuális gépek

![A nem kompatibilis virtuális gépek Excel-táblázata
](media/site-recovery-vmware-deployment-planner-analyze-report/incompatible-vms-v2a.png)

**Virtuális gép neve**: A virtuális gép neve vagy a jelentés létrehozásakor a VMListFile-ban használt IP-címet. Ez az oszlop a virtuális gépekhez csatolt VMDK-k listáját is megjeleníti. Az ismétlődő nevű vagy IP-című vCenter-beli virtuális gépek megkülönböztetésére a nevek tartalmazzák az ESXi-gazdagépnevet is. A feltüntetett ESXi-gazdagép az a számítógép, ahol a virtuális gép megtalálható volt, amikor az eszköz először felderítette azt a profilkészítés során.

**VM Compatibility**: Azt jelzi, hogy miért az adott virtuális gép nem kompatibilis a Site Recoveryvel. Az indokok a virtuális gép minden nem kompatibilis lemezénél vannak megadva, és a közzétett [tárhelykorlátok](https://aka.ms/azure-storage-scalbility-performance) alapján a következők lehetnek:

* A lemez mérete nagyobb 4095 GB-nál. Az Azure Storage jelenleg nem támogatja a 4095 GB-nál nagyobb adatlemez-méretet.

* Az operációsrendszer-lemez mérete nagyobb 2048 GB-nál. Az Azure Storage jelenleg nem támogatja a 2048 GB-nál nagyobb méretű operációsrendszer-lemezeket.

* A virtuális gép teljes mérete (replikáció + TFO) meghaladja a támogatott tárfiók méretkorlátozását (35 TB). Ez a fajta inkompatibilitás általában akkor fordul elő, ha a virtuális gép egy lemezének egyik teljesítményjellemzője meghaladja a támogatott standard szintű tárolóra vonatkozó Azure- vagy Site Recovery-korlátozásokat. Egy ilyen példány a prémium szintű tárolózónába kényszeríti a virtuális gépet. A prémium szintű tárfiókok maximális támogatott mérete azonban 35 TB, és egyetlen védett virtuális gép nem védhető több tárfiókon keresztül. Ügyeljen arra is, hogy amikor a feladatátvételi tesztet védett virtuális gépen hajtja végre, akkor az ugyanazon a tárfiókon fut, ahol a replikáció folyik. Ezen a példányon a lemez méretének kétszeresét kell kiosztani, hogy a replikáció is folytatódhasson, és ezzel párhuzamosan a feladatátvételi teszt is sikeres legyen.

* A forrás IOPS-érték meghaladja a tároló lemezenkénti 7500-as IOPS-korlátját.

* A forrás IOPS-érték meghaladja a tároló virtuális gépenkénti 80 000-es IOPS-korlátját.

* Az átlagos adatváltozás meghaladja a Site Recovery lemezenkénti átlagos I/O-mérethez megadott 10 MB/s értékű adatváltozási korlátját.

* Az átlagos adatváltozás meghaladja a Site Recovery virtuális gépenkénti átlagos I/O-mérethez megadott 25 MB/s értékű adatváltozási korlátját (az összes lemez adatváltozása együtt).

* Az összes virtuális gépre vonatkozó adatváltozás csúcsértéke meghaladja a Site Recovery virtuális gépenkénti 54 Mb/s-os támogatott adatváltozási csúcsérték korlátját.

* Az átlagos tényleges írási IOPS-érték meghaladja a Site Recovery lemezenkénti 840-es támogatott IOPS-korlátját.

* A kiszámított pillanatkép-tároló mérete meghaladja a 10 TB-os támogatott méretet.

* A napi adatváltozás túllépi a folyamatkiszolgálók által támogatott napi 2 TB-os keretet.


**R/W IOPS (növekedési tényezővel)-mel kiugró**: A lemez IOPS csúcs munkaterhelés (alapértelmezés szerint a 95. percentilis), beleértve a későbbi növekedési faktort is (az alapértelmezett érték 30 százalék). Vegye figyelembe, hogy a virtuális gép teljes írási/olvasási IOPS-értéke nem mindig egyezik meg az egyes virtuálisgép-lemezek írási/olvasási IOPS-értékeinek összegével. Ez azért van, mert a virtuális gép írási/olvasási IOPS-csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért írási/olvasási IOPS-értékek legmagasabb összege.

**Peak Data Churn (Mbps) (a növekedési tényezővel)**: Adatváltozásának csúcsértéke (alapértelmezés szerint a 95. százalékérték) lemezen beleértve a későbbi növekedési tényezővel (alapértelmezés szerint 30 százalék). Vegye figyelembe, hogy a virtuális gép teljes adatváltozása nem mindig egyezik meg az egyes lemezek adatváltozásának összegével. Ez azért van, mert a virtuális gép adatváltozásának csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért adatváltozás-értékek legmagasabb összege.

**Lemezek száma**: A virtuális gép vmdk-inak teljes száma.

**Lemezméret (GB)**: A virtuális gép összes lemezének teljes beállítási mérete. Az eszköz a virtuális gép egyes lemezeinek méretét is megjeleníti.

**Magok**: A virtuális gép processzormagjainak száma.

**Memória (MB)**: A virtuális gép RAM mennyisége.

**Hálózati adapterek**: A virtuális gép hálózati adaptereinek száma.

**Rendszerindítás-típus**: A virtuális gép rendszerindítási típusa. Ez BIOS vagy EFI lehet.  Jelenleg az Azure Site Recovery támogatja a Windows Server EFI típusú virtuális gépeket (Windows Server 2012, 2012 R2 és 2016) abban az esetben, ha a rendszerindító lemez partícióinak száma kevesebb mint 4, a rendszerindító szektor mérete pedig 512 bájt. Az EFI típusú virtuális gépek védelme érdekében az Azure Site Recovery mobilitási szolgáltatás verziójának legalább 9.13-asnak kell lennie. Az EFI virtuális gépek esetében kizárólag a feladatátvétel támogatott. A feladat-visszavétel nem támogatott.

**Operációs rendszer típusa**:  A virtuális gép operációs rendszerének típusa. Ennek értéke Windows, Linux vagy egyéb lehet a virtuális gép létrehozása során a VMware vSphere használatával kiválasztott sablon alapján.

## <a name="azure-site-recovery-limits"></a>Az Azure Site Recovery korlátai
Az alábbi táblázat az Azure Site Recovery korlátait tartalmazza. Ezek a korlátok a saját tesztjeinken alapulnak, de nem fedhetik le az alkalmazások minden lehetséges I/O-kombinációját. A tényleges eredmények a saját alkalmazásának I/O-műveletei alapján változhatnak. A legjobb eredmények érdekében még az üzembe helyezés megtervezése után is ajánlott az alkalmazás alapos tesztelése feladatátvételi tesztek használatával, így valós képet kaphat az alkalmazás teljesítményéről.

**Replikáció tárolási célja** | **Forráslemez átlagos I/O-mérete** |**Forráslemez átlagos adatváltozása** | **Forráslemez teljes napi adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 8 KB  | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 16 KB | 4 MB/s |  Lemezenként 336 GB
Prémium szintű P10 vagy P15 lemez | 32 KB vagy több | 8 MB/s | Lemezenként 672 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 8 KB    | 5 MB/s | Lemezenként 421 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 16 KB vagy több |10 MB/s | Lemezenként 842 GB

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
