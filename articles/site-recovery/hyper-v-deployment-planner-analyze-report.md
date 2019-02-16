---
title: A Hyper-V virtuális gépek vészhelyreállítása az Azure-bA az Azure Site Recovery Deployment Planner jelentés elemzése |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Site Recovery Deployment Planner a Hyper-V virtuális gépek vészhelyreállítása az Azure-bA a létrehozott jelentés elemzése.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 0ee3ff8bb1ebfc276550cba3570fdf4360aabf72
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327867"
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report"></a>Az Azure Site Recovery Deployment Planner jelentés elemzése
Ez a cikk az Azure Site Recovery Deployment Planner Hyper-V – Azure forgatókönyvére vonatkozó Excel-jelentés táblázatait ismerteti.

## <a name="on-premises-summary"></a>Helyszíni összefoglalás
A Helyszíni összefoglalás munkalap áttekintést nyújt a vizsgált Hyper-V-környezetről.

![Helyszíni összefoglalás](media/hyper-v-deployment-planner-analyze-report/on-premises-summary-h2a.png)

**Kezdő dátum** és **befejező dátum**: A jelentés létrehozásához tekinthető profilkészítési adatok kezdő és záró dátuma. Alapértelmezés szerint a kezdő dátum az a dátum, amikor a profilkészítés megkezdődik, a záró dátum pedig az a dátum, amikor a profilkészítés leáll. Ezek az adatok lehetnek a „StartDate” és „EndDate” értékek, ha a jelentést ezekkel a paraméterekkel hozza létre.

**Profilkészítési napok teljes száma**: A jelentéskészítés kezdő és záró dátuma között eltelt profilkészítési napok teljes száma.

**Kompatibilis virtuális gépek száma**: A kompatibilis virtuális gépek, amelyek a szükséges hálózati sávszélességet, a szükséges tárfiókok és Azure-magok számát a rendszer kiszámolta teljes száma.

**Az összes kompatibilis virtuális gépre kiterjedően lemezek teljes számának**: Minden kompatibilis virtuális gépen lemezek teljes száma.

**Átlagos száma kompatibilis virtuális gépenként lemezek**: Minden kompatibilis virtuális gépen alapján számított átlagos száma.

**Átlagos lemezméret (GB)**: Az átlagos alapján számított átlagos lemezméret az összes kompatibilis virtuális gépek.

**Desired RPO (perc)**: Vagy az alapértelmezett helyreállításipont-célkitűzés a értéke vagy a "DesiredRPO" paraméterhez szükséges sávszélesség becslése érdekében a jelentés elkészítésekor.

**Kívánt sávszélesség (Mbps)**: A megadott érték a "Bandwidth" paraméterhez a jelentéskészítéskor az elérhető helyreállítási időkorlát (RPO) becslése érdekében idején.

**Megfigyelt átlagos adatváltozás naponta (GB)**: Az összes profilkészítési napon megfigyelt átlagos adatváltozás.

## <a name="recommendations"></a>Javaslatok 
A Hyper-V – Azure jelentés javaslati táblázatában a következő részletek találhatók (a kiválasztott RPO szerint):

![Hyper-V – Azure jelentéshez kapcsolódó javaslatok](media/hyper-v-deployment-planner-analyze-report/Recommendations-h2a.png)

### <a name="profile-data"></a>Profiladatok
![Profiladatok](media/hyper-v-deployment-planner-analyze-report/profile-data-h2a.png)

**Ekkor a profiladatok időszak**: Az időtartam, ameddig a profilkészítés futtatásának. Az eszköz alapértelmezés szerint a profilkészítés során létrehozott minden adatot számításba vesz. Ha használta a StartDate és az EndDate lehetőséget a jelentés létrehozásakor, a rendszer a meghatározott időszakra vonatkozóan hozza azt létre. 

**Hyper-V servers profiled száma**: Akkor jön létre, amelyek virtuális gépeiről jelentés Hyper-V kiszolgálók száma. Jelölje ki a számot a Hyper-V-kiszolgálók nevének megjelenítéséhez. Megnyílik a helyszíni tárolóra vonatkozó követelmények táblázata, amelyen minden kiszolgáló fel van sorolva a hozzájuk kapcsolódó tárolási követelményekkel. 

**Desired RPO**: A helyreállítási időkorlát az üzembe helyezéshez. Alapértelmezés szerint a szükséges hálózati sávszélesség kiszámítása 15, 30 és 60 perces RPO értékkel történik. A kiválasztástól függően az eszköz frissíti az érintett értékeket a munkalapon. Ha használta a DesiredRPOinMin paramétert a jelentés elkészítése közben, akkor az az érték jelenik meg a kívánt helyreállítási időkorlát eredményei között.

### <a name="profiling-overview"></a>Profilkészítés áttekintése
![Profilkészítés áttekintése](media/hyper-v-deployment-planner-analyze-report/profiling-overview-h2a.png)

**Összesen a profilkészítés során létrehozott virtuális gépek**: Virtuális gépek, amelyek profilkészítés során létrehozott adatok érhetők el teljes száma. Ha a VMListFile olyan virtuális gépek nevét is tartalmazza, amelyekről nem készült profil, a rendszer ezeket figyelmen kívül hagyja a jelentéskészítés során, és nem számítja bele őket a profilkészítésben részt vevő virtuális gépek teljes számába.

**Kompatibilis virtuális gépek**: Az Azure-bA az Azure Site Recovery használatával védhető virtuális gépek számát. Az olyan kompatibilis virtuális gépek teljes száma, amelyekhez a rendszer kiszámolta szükséges hálózati sávszélességet, valamint a szükséges tárfiókok és Azure-magok számát. Az egyes kompatibilis virtuális gépek részletei elérhetők a „Compatible VMs” (Kompatibilis virtuális gépek) szakaszban.

**Nem kompatibilis virtuális gépek**: A profilkészítés során létrehozott virtuális gépek, amelyek nem védhetők Site recoveryvel száma. Az inkompatibilitás okait a „Nem kompatibilis virtuális gépek” című szakaszban olvashatja. Ha a VMListFile olyan virtuális gépek nevét is tartalmazza, amelyekről nem készült profil, a rendszer nem számítja bele őket a nem kompatibilis virtuális gépek számába. Az ilyen virtuális gépek az „Incompatible VMs” (Nem kompatibilis virtuális gépek) szakasz végén, „Data not found” (Nem található adat) megjelöléssel láthatók.

**Desired RPO**: A kívánt helyreállítási pont célkitűzés, percek alatt. A jelentés három helyreállítási Időkorlát értékről jön létre: 15 (alapértelmezett), 30 és 60 perc. A jelentésben szereplő ajánlott sávszélesség attól függően változik, hogy melyik lehetőséget választja a munkalap jobb felső sarkában található **Desired RPO** (Kívánt RPO) legördülő listából. Ha egyéni értékű -DesiredRPO paraméterrel készítette el a jelentést, ez az egyéni érték jelenik meg alapértelmezett értékként a **Desired RPO** (Kívánt RPO) legördülő listában.

### <a name="required-network-bandwidth-mbps"></a>Szükséges hálózati sávszélesség (Mbps)
![Szükséges hálózati sávszélesség](media/hyper-v-deployment-planner-analyze-report/required-network-bandwidth-h2a.png)

**Helyreállítási Időkorlát 100 %-os teljesítéséhez**: Az ajánlott sávszélesség (Mbps), amelyet ki a kívánt RPO az idő 100 százalékában betarthassa. Ekkora sávszélességet kell kijelölnie az összes kompatibilis virtuális gép stabil állapotú változásreplikációjához, ha biztosan ki kívánja zárni a helyreállítási időkorlát megszegését.

**RPO 90 %-os teljesítéséhez**: Talán szélessávú díjszabás vagy más ok miatt nem állítható a sávszélességet a kívánt RPO az idő 100 százalékában. Ilyen esetben választhat egy alacsonyabb sávszélesség-beállítást, amellyel az idő 90 százalékában betarthatja a kívánt RPO-t. Az alacsonyabb sávszélesség-beállítás következményeinek áttekintéséhez a jelentés lehetőségelemzést biztosít elemzést a helyreállítási időkorlát várható megszegéseinek számáról és időtartamáról.

**Elért átviteli sebesség**: Az átviteli sebesség a kiszolgálón, amelyen futtatja a GetThroughput parancs, az Azure-régió a storage-fiókot. Ez az átvitelisebesség-érték azt a becsült szintet jelöli, amely akkor érhető el, ha a kompatibilis virtuális gépeket a Site Recoveryvel védi. A Hyper-V-kiszolgáló tárolási és hálózati jellemzőinek meg kell egyezniük azon kiszolgálóéval, amelyen az eszközt futtatja.

A Site Recovery minden vállalati üzemelő példánya esetében az [ExpressRoute](https://aka.ms/expressroute) használata javasolt.

### <a name="required-storage-accounts"></a>Szükséges tárfiókok
A következő diagram az összes kompatibilis virtuális gép védelméhez szükséges (standard és prémium szintű) tárfiókok teljes számát mutatja. A „VM-storage placement” (Virtuálisgép-tároló elhelyezése) szakaszból megtudhatja, hogy melyik tárfiókot melyik virtuális géphez használhatja.

![Szükséges Azure Storage-fiókok](media/hyper-v-deployment-planner-analyze-report/required-storage-accounts-h2a.png)

### <a name="required-number-of-azure-cores"></a>Szükséges Azure-magok száma
Ez az eredmény az összes kompatibilis virtuális gép feladatátvétele vagy feladatátvételi tesztje előtt beállítandó magok teljes száma. Ha az előfizetés túl kevés maggal rendelkezik, a Site Recovery nem tudja létrehozni a virtuális gépeket a feladatátvételi teszt vagy a tényleges feladatátvétel alatt.

![Szükséges Azure-magok száma](media/hyper-v-deployment-planner-analyze-report/required-number-of-azure-cores-h2a.png)


### <a name="additional-on-premises-storage-requirement"></a>A helyszíni tárolásra vonatkozó további követelmények

A Hyper-V-kiszolgálók sikeres replikációjához és a változásreplikációhoz szükséges teljes szabad tárterület, amelynek megléte esetén a virtuális gépek replikációja semmiképpen nem okozza a termelési alkalmazások nem kívánt leállását. Az egyes kötetkövetelményekkel kapcsolatban [A helyszíni tárolásra vonatkozó követelmények](#on-premises-storage-requirement) című szakaszban talál további információt. 

[A helyszíni tárolásra vonatkozó követelmények](#why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication) című szakaszban talál további információt arról, hogy a replikációhoz miért van szükség szabad helyre.

![A helyszíni tárolásra vonatkozó követelmények és másolási gyakoriság](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-and-copy-frequency-h2a.png)

### <a name="maximum-copy-frequency"></a>Maximális másolási gyakoriság
Az ajánlott maximális másolási gyakoriságot be kell állítani ahhoz, hogy a replikáció elérje a kívánt RPO-t. Az alapértelmezett érték öt perc. A jobb RPO eléréséhez a másolási gyakoriságot 30 másodpercre is beállíthatja.

### <a name="what-if-analysis"></a>Lehetőségelemzés
![Lehetőségelemzés](media/hyper-v-deployment-planner-analyze-report/what-if-analysis-h2a.png) Ez az elemzés kiemeli, hogy hányszor szegné meg a helyreállítási időkorlátot a profilkészítés időtartama alatt, ha alacsonyabb sávszélességet állít be, amely az idő 90 százalékában biztosítaná a kívánt RPO betartását. A helyreállítási időkorlát túllépése bármelyik nap egy vagy több alkalommal is előfordulhat. Az ábra a napi RPO-csúcsértéket mutatja. Az elemzés alapján eldöntheti, hogy az RPO-túllépések teljes száma az összes nap alatt, valamint a napi RPO-csúcsérték elfogadható-e a megadott alacsonyabb sávszélesség esetén. Ha elfogadható, akkor lefoglalhatja az alacsonyabb sávszélességet a replikációhoz. Ha nem, akkor foglalja le a javasolt magasabb sávszélességet ahhoz, hogy az idő 100 százalékában betarthassa az RPO-t. 

### <a name="recommendation-for-successful-initial-replication"></a>Javaslatok a sikeres kezdeti replikációhoz
Ebben a szakaszban a virtuális gépeket védő kötegek mennyiségéről, valamint a kezdeti replikáció (IR) sikeres befejezéséhez szükséges minimális sávszélességről olvashat. 

![IR-kötegelés](media/hyper-v-deployment-planner-analyze-report/ir-batching-h2a.png)

A virtuális gépeket a megadott kötegelési sorrendben kell levédeni. Minden egyes köteg a virtuális gépek külön listáját tartalmazza. Az 1. kötegbe tartozó virtuális gépeket a 2. kötegbe tartozók előtt kell levédeni. A 2. kötegbe tartozó virtuális gépeket a 3. kötegbe tartozók előtt kell levédeni, és így tovább. Ha az 1. kötegbe tartozó virtuális gépek kezdeti replikációja befejeződött, engedélyezheti a 2. kötegbe tartozó virtuális gépekét. Hasonlóképpen, ha a 2. kötegbe tartozó virtuális gépek kezdeti replikációja befejeződött, ugyanígy engedélyezheti a 3. kötegbe tartozó virtuális gépekét, és így tovább. 

Ha nem követi a kötegelési sorrendet, előfordulhat, hogy nem fog rendelkezésre állni elegendő sávszélesség a később levédendő virtuális gépek kezdeti replikációjához. Ennek eredményeképpen a virtuális gépek nem tudják majd befejezni a kezdeti replikációt, vagy előfordulhat, hogy több védett virtuális gép újraszinkronizálási üzemmódba lép. A kiválasztott RPO-táblázatra vonatkozó IR-kötegelés részletes információkat tartalmaz arról, hogy mely virtuális gépeket kell belevenni a kötegekbe.

Az itt látható diagramon a kezdeti és változásreplikáció sávszélesség-elosztása látható kötegekre lebontva, a megadott kötegelési sorrend szerint. Az első kötegbe tartozó virtuális gépek levédésekor a kezdeti replikáció során a teljes sávszélesség rendelkezésre áll. Ha az első köteg kezdeti replikációja befejeződött, a sávszélesség egy részére szükség lesz a változásreplikációhoz. A fennmaradó sávszélesség fog rendelkezésre állni a második köteg virtuális gépeinek kezdeti replikációjához. 

A 2. köteg oszlopában az 1. köteg virtuális gépeinek változásreplikációjához szükséges sávszélesség, valamint a 2. köteg virtuális gépeinek kezdeti replikációjához elérhető sávszélesség látható. Ehhez hasonló módon a 3. köteg oszlopa a korábbi kötegek (1. és 2.) virtuális gépeinek változásreplikációjához szükséges sávszélességet, valamint a 3. köteg virtuális gépeinek kezdeti replikációjához rendelkezésre álló sávszélességet tartalmazza, és így tovább. Ha az összes köteg kezdeti replikációja befejeződött, az utolsó oszlopban az összes virtuális gép változásreplikációjához szükséges sávszélesség látható.

**Miért van szükség a kezdeti replikáció kötegelésére?**
A kezdeti replikáció befejezési ideje a virtuális gépek lemezeinek méretétől, a felhasznált lemezterülettől és az elérhető hálózati átviteli sebességtől függ. A részletek a kiválasztott RPO-táblázat IR-kötegelésében érhetők el.

### <a name="cost-estimation"></a>Költségbecslés
Az ábra az Azure-ba irányuló vészhelyreállítás (DR) becsült teljes költségének összefoglaló nézetét jeleníti meg a jelentéskészítéshez megadott pénznemben és célrégióra vonatkozóan.

![Költségbecslés összefoglalása](media/hyper-v-deployment-planner-analyze-report/cost-estimation-summary-h2a.png)

Az összefoglalás segít megismerni a tárolás, számítás, hálózat és licencelés költségeit, amelyet akkor kell fizetnie, ha az Azure-ban lévő összes kompatibilis virtuális gépet a Site Recovery használatával védi. A költségek kiszámítása a kompatibilis virtuális gépek, nem pedig a profilkészítésben részt vevő virtuális gépek alapján történik. 
 
A költségeket havi vagy éves bontásban tekintheti meg. További információkat olvashat a [támogatott célrégiókról](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) és a [támogatott pénznemekről](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies).

**Összetevők szerinti költségek**: A Vészhelyreállítás teljes költsége négy összetevőből áll: számítási, tárolási, hálózati és a Site Recovery licencköltségei. A költségeket a használat alapján számoljuk ki, amely költségek a replikáció során és a DR működési ideje során jelentkeznek. A számítás a számítási igény, a tárterület (prémium és standard), a helyszíni hely és az Azure között konfigurált ExpressRoute/VPN, valamint a Site Recovery-licenc figyelembe vételével történik.

**Állapot szerinti költségek**: A vészhelyreállítás teljes költségeinek kategóriái két különböző állapoton alapulnak: replikáció és Dr. 

**Replikáció költségei**: A replikáció során felmerülő költségek. Ez fedezi a tárolás, a hálózat és a Site Recovery-licenc költségeit. 

**Dr költsége**: A tesztek során felmerülő költségek. A Site Recovery virtuális gépeket indít el a feladatátvételi tesztek során. A DR működési költségei a futó virtuális gépek számítási és tárolási költségeit fedezik. 

**Az Azure Storage költsége / hónap/év**: A sávdiagram használt prémium és standard tárterület esetén a replikáció és Dr felmerülő tárterület teljes költségeit mutatja. A virtuális gépenkénti részletes költségelemzést a [Költségbecslés](hyper-v-deployment-planner-cost-estimation.md) táblázatban tekintheti meg.

### <a name="growth-factor-and-percentile-values-used"></a>A használt növekedési tényező és százalékértékek
A munkalap alján található szakasz a profilkészítésben részt vevő virtuális gépek összes teljesítményszámlálójához használt százalékértéket (az alapértelmezett érték a 95. percentilis), valamint az összes számításban használt növekedési tényezőt (az alapértelmezett érték 30 százalék) mutatja.

![A használt növekedési tényező és százalékértékek](media/hyper-v-deployment-planner-run/growth-factor-max-percentile-value.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Javaslatok az elérhető sávszélesség bemenetként való megadásával
![Profilkészítés áttekintése sávszélesség-bevitellel](media/hyper-v-deployment-planner-analyze-report/profiling-overview-bandwidth-input-h2a.png)

Előfordulhat olyan helyzet, hogy legfeljebb x Mbps sávszélességet tud beállítani a Site Recovery replikációjára. Az eszközzel megadhatja a rendelkezésre álló sávszélességet (jelentéskészítés közben a -Bandwidth paraméterrel), és megkapja az elérhető RPO-t percben megadva. Az elérhető RPO-érték ismeretében eldöntheti, hogy szükséges-e további sávszélességet kiépítenie, vagy elfogadja a vészhelyreállítási megoldást ezzel az RPO-val.

![Elérhető RPO](media/hyper-v-deployment-planner-analyze-report/achivable-rpo-h2a.PNG)

## <a name="vm-storage-placement-recommendation"></a>Javaslat a virtuálisgép-tároló elhelyezésére 
![Virtuálisgép-tároló elhelyezése](media/hyper-v-deployment-planner-analyze-report/vm-storage-placement-h2a.png)

**Lemezes tárolás típusa**: Vagy standard vagy prémium szintű storage-fiók, amelyet említett összes megfelelő virtuális gépek replikálása az **VMs to Place** oszlop.

**A javasolt előtag**: A javasolt háromkarakteres előtag használható a storage-fiók elnevezéséhez. Saját előtagot is használhat, de az eszköz által javasolt nevek követik a [tárfiókok partíció-elnevezési szabályait](https://aka.ms/storage-performance-checklist).

**Javasolt fióknév**: A storage-fiók neve, a javasolt előtag használata után. Cserélje le a csúcsos zárójelben (< és >) lévő nevet a kívánt értékre.

**Log Storage-fiók**: Minden replikációs naplót egy standard szintű tárfiókot vannak tárolva. A prémium szintű tárfiókokba replikált virtuális gépek esetében be kell állítani egy további standard szintű tárfiókot a naplók tárolására. Egyetlen standard szintű naplótárolási fiókot több prémium szintű replikációs tárfiók is használhat. A standard szintű tárfiókokba replikált virtuális gépek ugyanazt a tárfiókot használják a naplókhoz is.

**Tárfiók neve a javasolt**: A naplókat tároló tárfiók neve, a javasolt előtag használata után. Cserélje le a csúcsos zárójelben (< és >) lévő nevet a kívánt értékre.

**Elhelyezés összegzése**: A teljes virtuális gépek összegzését betölteni a tárfiók a replikáció során, és a feladatátvételi teszt vagy tényleges. Az összegzés a következőket tartalmazza:

* A tárfiókhoz rendelt virtuális gépek teljes számát. 
* A tárfiókban elhelyezni kívánt összes virtuális gép összesített olvasási/írási IOPS-értékét.
* Az összesített írási (replikációs) IOPS-értéket.
* Az összes lemez teljes beállítási méretét.
* A lemezek teljes számát.

**VMs to Place**: Egy lista összes virtuális gép, amelyet az optimális teljesítmény és használat az adott tárfiókon ajánlott elhelyezni.

## <a name="compatible-vms"></a>Kompatibilis virtuális gépek
A Site Recovery Deployment Planner által létrehozott Excel-jelentésben minden kompatibilis virtuális gépre vonatkozó részletet megtalál a „Compatible VMs” (Kompatibilis virtuális gépek) táblázatban.

![Kompatibilis virtuális gépek](media/hyper-v-deployment-planner-analyze-report/compatible-vms-h2a.png)

**Virtuális gép neve**: A jelentés létrehozásakor a VMListFile-ban használt virtuális gép nevét. Ez az oszlop a virtuális gépekhez csatolt lemezek (VHD-k) listáját is megjeleníti. A nevek tartalmazzák a Hyper-V kiszolgálóneveket, amelyeken az eszköz felfedezte a virtuális gépeket a profilkészítési időszakban.

**VM Compatibility**: Értékek a következők **Igen** és **Igen**\*. **Igen** \* a példányokra, amelyben a virtuális gép megfelel egy [prémium szintű SSD-k](../virtual-machines/windows/disks-types.md). A profilkészítés során megállapított magas adatváltozású vagy IOPS-értékű lemez megfelel a lemezhez leképezett méretet meghaladó prémium lemezméretnek. A tárfiók a lemez mérete alapján dönti el, hogy melyik prémium szintű lemeztípushoz rendelje hozzá a lemezt: 
* 128 GB alatt P10.
* 128 GB és 256 GB között P15.
* 256 GB és 512 GB között P20.
* 512 GB és 1024 GB között P30.
* 1025 GB és 2048 GB között P40.
* 2049 GB és 4095 GB között P50.

Ha például a számítási feladatok jellemzői alapján egy lemez a P20-as vagy P30-as kategóriába tartozik, de a mérete alapján egy alacsonyabb prémium szintű tárolólemez-típusba kerül, az eszköz **Yes**\* (Igen) megjelöléssel látja el ezeket a virtuális gépeket. Az eszköz azt is javasolja, hogy módosítsa a forráslemez méretét, hogy a lemez megfeleljen az ajánlott prémium szintű tárolólemez-típusnak, vagy hogy módosítsa a céllemez típusát a feladatátvétel után.

**Tárolási típus**: Standard vagy prémium.

**A javasolt előtag**: A tárfiók három karakterből álló előtagja.

**Storage-fiók**: A javasolt tárfiókelőtagot használó név.

**R/W IOPS (növekedési tényezővel)-mel kiugró**: A maximális írási/olvasási iops-t a lemezen (az alapértelmezett érték 95. percentilis) a későbbi növekedési tényezővel (az alapértelmezett érték 30 százalék). Egy virtuális gép teljes írási/olvasási IOPS-értéke nem mindig egyezik meg az egyes virtuálisgép-lemezek írási/olvasási IOPS-értékeinek összegével. A virtuális gép írási/olvasási IOPS-csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért írási/olvasási IOPS-értékek legmagasabb összege.

**Peak Data Churn MB/s (a növekedési tényezővel)**: A lemez adatváltozásának csúcsértéke (az alapértelmezett érték 95. percentilis) a későbbi növekedési tényezővel (az alapértelmezett érték 30 százalék). A virtuális gép teljes adatváltozása nem mindig egyezik meg az egyes virtuálisgép-lemezek adatváltozásának összegével. A virtuális gép maximális adatváltozása az egyes lemezeken a profilkészítési időszak alatt percenként mért adatváltozások legmagasabb összege.

**Az Azure Virtuálisgép-méret**: A helyszíni virtuális géphez hozzárendelt Azure Cloud Services Virtuálisgép-méretet a ideális. A hozzárendelés a helyszíni virtuális gép memóriáján, a lemezek/magok/hálózati adapterek számán és az olvasási/írási IOPS-értéken alapul. Mindig a legalacsonyabb, a helyszíni virtuális gép összes jellemzőjének megfelelő Azure-beli virtuálisgép-méretet ajánlott használni.

**Lemezek száma**: A virtuális gép lemezeinek (VHD) a virtuális gép teljes száma.

**Lemezméret (GB)**: A virtuális gép összes lemezének teljes mérete. Az eszköz a virtuális gép egyes lemezeinek méretét is megjeleníti.

**Magok**: A virtuális gép processzormagjainak száma.

**Memória (MB)**: A virtuális gép RAM.

**Hálózati adapterek**: A virtuális gép hálózati adaptereinek száma.

**Rendszerindítás-típus**: A virtuális gép rendszerindítási típusa. Ez BIOS vagy EFI lehet.

## <a name="incompatible-vms"></a>Nem kompatibilis virtuális gépek
A Site Recovery Deployment Planner által létrehozott Excel-jelentésben az összes, nem kompatibilis virtuális gépre vonatkozó részletet megtalálja az „Incompatible VMs” (Nem kompatibilis virtuális gépek) táblázatban.

![Nem kompatibilis virtuális gépek](media/hyper-v-deployment-planner-analyze-report/incompatible-vms-h2a.png)

**Virtuális gép neve**: A jelentés létrehozásakor a VMListFile-ban használt virtuális gép nevét. Ez az oszlop a virtuális gépekhez csatolt lemezek (VHD-k) listáját is megjeleníti. A nevek tartalmazzák a Hyper-V kiszolgálóneveket, amelyeken az eszköz felfedezte a virtuális gépeket a profilkészítési időszakban.

**VM Compatibility**: Azt jelzi, hogy miért az adott virtuális gép nem kompatibilis a Site Recoveryvel. Az indokok a virtuális gép minden nem kompatibilis lemezénél vannak megadva, és a közzétett [tárhelykorlátok](https://aka.ms/azure-storage-scalbility-performance) alapján a következők lehetnek:

* A lemez mérete nagyobb 4095 GB-nál. Az Azure Storage jelenleg nem támogatja a 4095 GB-nál nagyobb adatlemez-méretet.

* Az 1. generációs virtuális gép (BIOS rendszerindítás-típus) operációsrendszer-lemezének mérete nagyobb 2047 GB-nál. A Site Recovery jelenleg nem támogatja a 2047 GB-nál nagyobb méretű operációsrendszer-lemezeket 1. generációs virtuális gépek esetében.

* A 2. generációs virtuális gép (EFI rendszerindítás-típus) operációsrendszer-lemezének mérete nagyobb 300 GB-nál. A Site Recovery jelenleg nem támogatja a 300 GB-nál nagyobb méretű operációsrendszer-lemezeket 2. generációs virtuális gépek esetében.

* A virtuális gép neve nem tartalmazhatja a következő, nem támogatott karaktereket: “” [] `. Az eszköz nem tudja lekérni azon virtuális gépek profilkészítési adatait, amelyek tartalmazzák nevükben a fenti karaktereket. 

* A VHD-n kettő vagy több virtuális gép osztozik. Az Azure nem támogatja a közös VHD-vel rendelkező virtuális gépeket.

* A Virtuális Fiber Channel vezérlőt használó virtuális gépek nem támogatottak. A Site Recovery nem támogatja a Virtuális Fiber Channel vezérlőt használó virtuális gépeket.

* Egy Hyper-V-fürt nem tartalmaz replikációs közvetítőt. A Site Recovery nem támogatja egy Hyper-V-fürt virtuális gépét, ha a Hyper-V-replikaközvetítő nem lett konfigurálva a fürt számára.

* A virtuális gép nem magas rendelkezésre állású. A Site Recovery nem támogatja olyan Hyper-V-fürtcsomópont virtuális gépét, amelynek VHD lemezeit a helyi lemez tárolja, nem a fürtlemez. 

* A virtuális gépek teljes mérete (replikáció + feladatátvételi teszt) meghaladja a támogatott prémium tárfiók méretkorlátozását (35 TB). Ez a fajta inkompatibilitás általában akkor fordul elő, ha a virtuális gép egy lemezének egyik teljesítményjellemzője meghaladja a támogatott standard szintű tárolóra vonatkozó Azure- vagy Site Recovery-korlátozásokat. Egy ilyen példány a prémium szintű tárolózónába kényszeríti a virtuális gépet. A prémium szintű tárfiókok maximális támogatott mérete azonban 35 TB, és egyetlen védett virtuális gép nem védhető több tárfiókon keresztül. 

    Amikor a feladatátvételi teszt védett virtuális gépen megy végbe, és ha egy felügyelet nélküli lemez konfigurálva lett feladatátviteli tesztre, akkor a teszt ugyanazon a tárfiókon fut, ahol a replikáció folyik. Ebben az esetben további, ugyanakkora mennyiségű tárterületre van szükség, mint a replikáció esetében. Ez biztosítja a replikáció lefolyását, valamint ezzel párhuzamosan a feladatátvételi teszt sikerét. Ha egy felügyelt lemez konfigurálva lett feladatátviteli tesztre, nincs szükség további terület kijelölésére azon virtuális gép esetén, amelyen a feladatátviteli teszt zajlik.

* A forrás IOPS-érték meghaladja a tároló lemezenkénti 7500-as IOPS-korlátját.

* A forrás IOPS-érték meghaladja a tároló virtuális gépenkénti 80 000-es IOPS-korlátját.

* A forrás virtuális gép átlagos adatváltozása meghaladja a Site Recovery átlagos I/O-mérethez megadott 10 MB/s értékű adatváltozási korlátját.

* A forrás virtuális gép átlagos tényleges írási IOPS-értéke meghaladja a Site Recovery lemezenkénti 840-es támogatott IOPS-korlátját.

* A kiszámított pillanatkép-tároló mérete meghaladja a 10 TB-os támogatott méretet.

**R/W IOPS (növekedési tényezővel)-mel kiugró**: A lemez IOPS csúcs munkaterhelés (az alapértelmezett érték 95. percentilis) a későbbi növekedési tényezővel (az alapértelmezett érték 30 százalék). A virtuális gép teljes írási/olvasási IOPS-értéke nem mindig egyezik meg az egyes virtuálisgép-lemezek írási/olvasási IOPS-értékeinek összegével. A virtuális gép írási/olvasási IOPS-csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért írási/olvasási IOPS-értékek legmagasabb összege.

**Peak Data Churn (MB/s) (a növekedési tényezővel)**: A lemez adatváltozásának csúcsértéke (az alapértelmezett érték 95. percentilis) a későbbi növekedési tényezővel (az alapértelmezett érték 30 százalék). Vegye figyelembe, hogy a virtuális gép teljes adatváltozása nem mindig egyezik meg az egyes virtuálisgép-lemezek adatváltozásának összegével. A virtuális gép maximális adatváltozása az egyes lemezeken a profilkészítési időszak alatt percenként mért adatváltozások legmagasabb összege.

**Lemezek száma**: A virtuális gép VHD-inek teljes száma.

**Lemezméret (GB)**: A virtuális gép összes lemezének teljes beállítási mérete. Az eszköz a virtuális gép egyes lemezeinek méretét is megjeleníti.

**Magok**: A virtuális gép processzormagjainak száma.

**Memória (MB)**: A virtuális gép RAM mennyisége.

**Hálózati adapterek**: A virtuális gép hálózati adaptereinek száma.

**Rendszerindítás-típus**: A virtuális gép rendszerindítási típusa. Ez BIOS vagy EFI lehet.

## <a name="azure-site-recovery-limits"></a>Az Azure Site Recovery korlátai
Az alábbi táblázat a Site Recovery korlátait tartalmazza. Ezek a korlátok teszteken alapulnak, azonban nem fedhetik le az alkalmazások minden lehetséges I/O-kombinációját. A tényleges eredmények a saját alkalmazásának I/O-műveletei alapján változhatnak. A legjobb eredmények érdekében, még az üzembe helyezés megtervezése után is alaposan tesztelje az alkalmazást feladatátvételi tesztek használatával, így valós képet kaphat az alkalmazás teljesítményéről.
 
**Replikáció tárolási célja** | **Forrás virtuális gép átlagos I/O-mérete** |**Forrás virtuális gép átlagos adatváltozása** | **Forrás virtuális gép teljes napi adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB | Virtuális gépenként 2 MB/s | Virtuális gépenként 168 GB
Prémium szintű Storage | 8 KB  | Virtuális gépenként 5 MB/s | Virtuális gépenként 421 GB
Prémium szintű Storage | 16 KB vagy több| Virtuális gépenként 10 MB/s | Virtuális gépenként 842 GB

Ezek átlagos határértékek, amelyek 30 százalékos I/O-átfedést feltételeznek. A Site Recovery képes magasabb átviteli sebesség kezelésére az átfedési arány, a nagyobb írási méretek és a számítási feladatok tényleges I/O-viselkedése alapján. Az előbbi számok egy általános, körülbelül ötperces várólistát feltételeznek. Ez azt jelenti, hogy a feltöltést követő öt percben megtörténik az adat feldolgozása, és létrejön egy helyreállítási pont.

## <a name="on-premises-storage-requirement"></a>A helyszíni tárolásra vonatkozó követelmények

A munkalapon szerepelnek a Hyper-V kiszolgálók (amelyeken a VHD-k megtalálhatók) köteteinek a sikeres kezdeti és változásreplikációhoz szükséges teljes szabad tárterületre vonatkozó követelményei. A replikáció engedélyezése előtt adja hozzá a szükséges tárterületet a kötetekhez, így biztosíthatja, hogy a replikáció során nem következik be nem kívánt leállás a termelési alkalmazásokban. 

  A Site Recovery Deployment Planner azonosítja az optimális tárterület-követelményeket a VHD mérete és a replikációhoz felhasznált hálózati sávszélesség alapján.

![A helyszíni tárolásra vonatkozó követelmények](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-requirement-h2a.png)

### <a name="why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication"></a>Miért van szükség szabad területre a Hyper-V kiszolgálón a replikációhoz?
* Amikor engedélyezi egy virtuális gép replikációját, a Site Recovery pillanatképet készít a virtuális gép egyes VHD-iről a kezdeti replikációhoz. A kezdeti replikáció során az alkalmazás új módosításokat ír a lemezekre. A Site Recovery a naplófájlokban követi nyomon ezeket a változásokat, ehhez pedig további tárterületre van szükség. A kezdeti replikáció befejezéséig a naplófájlokat a rendszer helyben tárolja. 

    Ha nincs elegendő hely a naplófájlok és a pillanatkép (AVHDX) számára, a replikáció átlép újraszinkronizálási üzemmódba, és soha nem fejeződik be. A legrosszabb esetben a VHD-méret 100%-ának megfelelő további szabad tárterületre van szükség a kezdeti replikációhoz.
* Ha a kezdeti replikáció befejeződik, elkezdődik a változásreplikáció. A Site Recovery a naplófájlokban követi nyomon ezeket a változásokat. Ezek ugyanazon a köteten találhatók, amelyen a virtuális gép VHD-i vannak. A naplófájlok replikálása az Azure-ba a konfigurált másolási gyakoriságnak megfelelően történik. Az elérhető hálózati sávszélességtől függően a naplófájlok Azure-ba történő replikációja némi időt vesz igénybe. 

    Ha nincs elegendő szabad terület a naplófájlok tárolásához, a replikáció szünetel, a virtuális gép replikációja pedig „újraszinkronizálás szükséges” állapotúra vált.
* Ha a hálózati sávszélesség nem elég a naplófájlok Azure-ba történő áthelyezéséhez, akkor azok a köteten halmozódnak fel. A legrosszabb esetben – amikor a naplófájlok mérete eléri a VHD méretének 50%-át – a virtuális gép replikációja „újraszinkronizálás szükséges” állapotúra változik. A legrosszabb esetben a VHD mérete 50%-ának megfelelő további szabad tárterületre van szükség a változásreplikációhoz.

**A Hyper-V-gazdagép**: Profilkészítés Hyper-V kiszolgálók listája. Ha egy kiszolgáló egy Hyper-V fürt részét képezi, az összes fürtcsomópont csoportosítva lesz.

**Volume (VHD útvonala)**: Minden olyan kötete, egy Hyper-V-gazdagép, ahol a VHD/vhdx-fájlokat találhatók. 

**Szabad hely (GB)**: A szabad terület a köteten.

**Teljes tárterület a köteten (GB) szükséges**: A teljes szabad tárterület a köteten a sikeres kezdeti és változásreplikációhoz szükséges. 

**Ki kell építeni a köteten a sikeres replikálás (GB) további tárterület teljes**: Azt javasolja, hogy a teljes további tárterületre, hogy ki kell építeni a köteten a sikeres kezdeti és változásreplikációhoz.

## <a name="initial-replication-batching"></a>Kezdeti replikáció kötegelése 

### <a name="why-do-i-need-initial-replication-batching"></a>Miért van szükség a kezdeti replikáció kötegelésére?
Ha összes virtuális gép védelme egyszerre zajlik, a szabad tárterületre vonatkozó követelmény sokkal magasabb. Ha nem áll rendelkezésre elég tárterület, a virtuális gépek replikációja újraszinkronizálási üzemmódra vált. Emellett az összes virtuális gép együttes kezdeti replikációjának befejezéséhez szükséges hálózati sávszélesség is sokkal magasabb. 

### <a name="initial-replication-batching-for-a-selected-rpo"></a>Kezdeti replikáció kötegelése a kiválasztott RPO esetében
Ezen a munkalapon az egyes kötegek részletes nézete található a kezdeti replikációhoz. A rendszer minden egyes RPO esetében külön IR-kötegelési táblázatot hoz létre. 

Ha minden kötet esetében megfelelt a javasolt helyszíni tárterület-követelményeknek, a replikálandó fő információ a párhuzamosan védhető virtuális gépek listája. Ezek a virtuális gépek egy kötegbe vannak csoportosítva, és több köteg is lehet. A virtuális gépeket a megadott kötegelési sorrendben kell levédeni. Először a 1. kötegbe tartozó virtuális gépeket védje le, majd ha befejeződött a kezdeti replikáció, védje le a 2. kötegbe tartozókat, és így tovább. A kötegek és a hozzájuk tartozó virtuális gépek listáját ebben a táblázatban találja. 

![Az IR-kötegelés részletei](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo-h2a.png)

![Az IR-kötegelés további részletei](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo2-h2a.png)

### <a name="each-batch-provides-the-following-information"></a>Minden kötegben az alábbi információk találhatók meg 
**A Hyper-V-gazdagép**: A Hyper-V gazdagép, a védeni kívánt virtuális gép.

**Virtuális gép**: A védeni kívánt virtuális Gépet. 

**Megjegyzések**: Ha bármilyen műveletre szükség egy virtuális gép egy meghatározott kötete esetében, az itt elérhető a megjegyzést. Ha például nem érhető el elegendő szabad terület egy köteten, akkor megjelenik az „Add additional storage to protect this VM” (Adjon hozzá további tárterületet a virtuális gép levédéséhez) megjegyzés.

**Volume (VHD útvonala)**: A kötet neve, a virtuális gép VHD-ket. 

**Szabad hely a köteten (GB)**: A virtuális Géphez tartozó köteten lévő szabad terület. A köteteken lévő elérhető szabad terület kiszámításakor a rendszer figyelembe veszi az előző kötegek virtuális gépei – amelyeknek a VHD-i egyazon köteten találhatók – változásreplikációja által felhasznált lemezterületet is. 

Például tegyük fel, hogy a VM1, VM2 és VM3 virtuális gép egy köteten található (E:\VHDpath). A replikáció előtt a köteten 500 GB szabad terület van. A VM1 gép az 1. köteg része, a VM2 a 2. kötegé, a VM3 pedig a 3. kötegé. A VM1 által 500 GB szabad terület érhető el. A VM2 esetében a szabad terület 500 GB – ennyire van szükség a VM1 változásreplikációjához. Ha a VM1-nek 300 GB területre van szüksége a változásreplikációhoz, akkor a VM2 számára 500 GB - 300 GB = 200 GB szabad terület lesz elérhető. A VM2-nek hasonló módon 300 GB területre van szüksége a változásreplikációhoz. A VM3 számára 200 GB - 300 GB = -100 GB szabad terület lesz elérhető.

**A köteten (GB). a kezdeti replikálás a szükséges tárhelyet**: Szabad tárterület a köteten a virtuális gép kezdeti replikációjához szükséges.

**A köteten (GB) változásreplikációjához szükséges tárhelyet**: Szabad tárterület a köteten a virtuális gép változásreplikációjához szükséges.

**További tárterület szükséges deficit elkerülésére, hogy sikertelen replikáció (GB) alapuló**: További lemezterület a köteten a virtuális gép szükséges. Ez a maximális kezdeti replikációra és változásreplikációra vonatkozó tárterületi követelmények maximuma, levonva belőle a köteten elérhető szabad területet.

**A kezdeti replikációhoz (Mbps) szükséges minimális sávszélesség**: A virtuális gép kezdeti replikációjához szükséges minimális sávszélesség.

**A minimális sávszélesség (Mbps) változásreplikációjához szükséges**: A virtuális gép változásreplikációjához szükséges minimális sávszélesség.

### <a name="network-utilization-details-for-each-batch"></a>Az egyes kötegek hálózatfelhasználásának részletei 
Minden kötegtáblázathoz tartozik egy összefoglalás az adott köteg hálózatfelhasználásáról.

**Batch számára elérhető sávszélesség**: A batch az előző köteg változásreplikációjához szükséges sávszélesség figyelembe vételével rendelkezésre álló sávszélességet.

**Hozzávetőleges köteg kezdeti replikációjához elérhető sávszélesség**: A köteg virtuális gépeinek kezdeti replikációjához elérhető sávszélesség. 

**Köteg változásreplikációjához felhasznált hozzávetőleges sávszélesség**: A köteg virtuális gépeinek változásreplikációjához szükséges sávszélesség. 

**Kezdeti replikálás time for batch (ÓÓ: PP) becsült**: A kezdeti replikáció becsült ideje óra: perc.



## <a name="next-steps"></a>További lépések
További információk a [költségbecslésről](hyper-v-deployment-planner-cost-estimation.md).
