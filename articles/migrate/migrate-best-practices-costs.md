---
title: Ajánlott eljárások költségszámítás és méretezése a számítási feladatok migrálása az Azure-bA |} A Microsoft Docs
description: Ajánlott eljárások a költségszámítás és a méretezési számítási feladatok migrálása az Azure-bA.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: raynew
ms.openlocfilehash: 974ea5803b6e31ad8f940265071f41440d5355da
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700638"
---
# <a name="best-practices-for-costing-and-sizing-workloads-migrated-to-azure"></a>Ajánlott eljárások a költségszámítás és méretezési számítási feladatok migrálása az Azure-bA

A tervezés és kialakítási az áttelepítéshez, mint az Azure-migrálás, hosszú távú sikert költségek összpontosító biztosítja. A migrálási projekt alatt, kritikus fontosságú, hogy az összes csapatok (pénzügyi, kezeléséhez, alkalmazásokért felelős csapatok stb) kapcsolódó költségeit.
- Az áttelepítés előtt becslése a migrálás kiadásokat, az alapkonfigurációt a havi, negyedéves, és az éves költségvetés célok, kritikus fontosságú a sikeres.
- Az áttelepítés után kell optimalizálhatja a költségeket, folyamatosan figyelheti a számítási feladatokat, és tervezze meg jövőbeli használati mintákat. Az áttelepített erőforrások előfordulhat, hogy kezd, mint egy adott típusú számítási feladatok, de egy másik típusúvá időbeli alakulásával, használat, a költségek és időeltolású üzleti követelmények alapján.

Ez a cikk ismerteti az ajánlott eljárások költségszámítás és a méretezési előtt, és az áttelepítés után.  

> [!IMPORTANT]
> Az ajánlott eljárásokat és vélemények ebben a cikkben leírt Azure-platformon alapul, és szolgáltatás írásának időpontjában elérhető szolgáltatások. Funkciók és képességek idővel változni. Nem minden javaslat előfordulhat, hogy az üzembe helyezés a alkalmazni kell, ezért kattintson a megoldás.


## <a name="before-migration"></a>Migrálás előtt 

Előtt a munkaterheléseit a felhőbe helyezi át, becsült havi költségének őket az Azure-ban. Proaktív módon a felhőbeli költségek kezelése segít a működési költségeket költségvetés formátumhoz. Ha költségvetés korlátozott, vegye figyelembe ennek áttelepítés előtt.  Érdemes a számítási feladatok átalakítása az Azure kiszolgáló nélküli technológiákat, adott esetben a költségek csökkentése érdekében.

Ebben a szakaszban a gyakorlati tanácsok segítséget költségbecslést, hajtsa végre a megfelelő virtuális gépek és a storage, kihasználhatja az Azure Hybrid benefits, használjon fenntartott virtuális gépeket és megbecsülheti a felhő költségeit, előfizetések között.



## <a name="best-practice-estimate-monthly-workload-costs"></a>Ajánlott eljárás: Havi számítási Költségbecslés
 
Az előrejelzéshez az áttelepített alkalmazások és szolgáltatások a havi számla, nincsenek számos olyan eszközt is használhat.

- **Azure díjkalkulátor**: Kiválaszthatja a termékeket, például virtuális gépek és a tároló becsült szeretné. A díjkalkulátor hozhat létre a becsült költségek bemeneteként.

 ![Azure díjkalkulátor](./media/migrate-best-practices-costs/pricing.png) *Azure díjkalkulátor*

- **Az Azure Migrate**: Becsült költség, szüksége, tekintse át és az Azure-ban a számítási feladatok futtatásához szükséges összes erőforrást. Beszerezni ezeket az adatokat, az eszközök, beleértve a kiszolgálók, virtuális gépek, adatbázisok és tárolás készlet hoz létre. Az Azure Migrate használatával ezt az információt.

 - Az Azure Migrate deríti fel, és felméri a helyszíni környezetet egy leltárat biztosít.
 - Az Azure Migrate is csatlakoztathatók, és, virtuális gépek közötti függőségek megjelenítése, hogy teljes képet.
 - Az Azure Migrate értékelés becsült költség tartalmazza.
    - A számítási költségek: Ajánlott az értékelés létrehozása az Azure Virtuálisgép-méretet használja, az Azure Migrate használja a számlázási API becsült havi VM-költségek kiszámítása. A becslés úgy ítéli meg, az operációs rendszer, frissítési garanciával rendelkező, a fenntartott példányok, virtuális gép Üzemidő, helye és pénznem beállításai. A költségek összesíti az értékelésben minden virtuális gépen, és kiszámítja a teljes havi számítási költségeket.
    - Adattárolási költség: Az Azure Migrate teljes havi tárolási költségek a tárolási költségeket az összes virtuális gép tartalmaz egy értékelés összesítésével számítja ki. Kiszámíthatja a havi tárolási költségek egy adott gép csatlakoztatott összes lemez havi költségének összesítésével. 

    ![Az Azure Migrate](./media/migrate-best-practices-costs/assess.png) *Azure Migrate az értékelés*

**tudj meg többet:**
- [Használat](https://azure.microsoft.com/pricing/calculator/) az Azure díjkalkulátorát.
- [Áttekintés](https://docs.microsoft.com/azure/migrate/migrate-overview) az Azure Migrate.
- [További információ](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation) Azure Migrate-értékelések.
- [További](https://docs.microsoft.com/azure/dms/dms-overview) a Database Migration Service (DMS) kapcsolatos.

## <a name="best-practice-right-size-vms"></a>Ajánlott eljárás: Jobb méretű virtuális gépek

Számos lehetőség közül választhat, a munkaterhelés Azure virtuális gépek telepítésekor. Minden virtuális gép rendelkezik a funkciók és a Processzor, memória és a lemezek eltérő kombinációja. Virtuális gépek a következőképpen vannak csoportosítva.

**Típus** | **Részletek** | **Használat**
--- | --- | ---
**Általános célú** | Kiegyensúlyozott processzor-memória arány. | Megfelelő választás a tesztelési-fejlesztési feladatokhoz, kis és közepes méretű adatbázisokhoz, kis és közepes adatforgalmú webkiszolgálókhoz.
**Számításra optimalizált** | Magas processzor-memória arány a processzor javára. | Megfelelő választás a közepes forgalmú webkiszolgáló, hálózati berendezések, kötegfolyamatok, alkalmazások kiszolgálói.
**Memóriaoptimalizált** | Magas memória – Processzor. | Megfelelő választás a relációs adatbázisok, a közepes és nagy méretű gyorsítótár, a memóriabeli elemzésekhez.
**Tárolásra optimalizált** | Magas lemez-adatátviteli és I/O-műveleti jellemzők. | Jó big Data-, SQL és NoSQL-adatbázisok.
**GPU-optimalizált** | Specializált virtuális gépek. Egy vagy több gpu-kkal. | Nehéz a képek és videószerkesztésre.
**Nagy teljesítmény** | Leggyorsabb és leghatékonyabb Processzor. Választható nagy átviteli sebességű hálózati adapterekkel (RDMA) rendelkező virtuális gépek | Kritikus fontosságú, nagy teljesítményű alkalmazásokat.

- Fontos tudni, hogy a ezeket a virtuális gépeket, és a hosszú távú költségvetés hatások közötti árképzési eltéréseket.
- Minden megvan benne lévő Virtuálisgép-sorozatok számos.
- Ezenkívül amikor kiválaszt egy virtuális Gépet egy sorozat belül, csak méretezheti a virtuális gép felfelé és lefelé a sorozat belül. Ha például egy DSv2\_2 is vertikális felskálázás DSv2\_4, de nem lehet módosítani egy másik adatsorozathoz Fsv2 például\_2.

**tudj meg többet:**
- [További](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) Virtuálisgép-típusok és a méretezés és a Leképezési méret és típus.
- [Csomag](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs) Virtuálisgép-méretezési.
- [Felülvizsgálat](https://docs.microsoft.com/azure/migrate/contoso-migration-assessment) a fiktív Contoso vállalat egy minta értékelés.

## <a name="best-practice-select-the-right-storage"></a>Ajánlott eljárás: Válassza ki a megfelelő tárolási

Finomhangolásához és a helyszíni tárolók (TÁROLÓHÁLÓZATI és NAS) és a hálózatok támogatja azokat, költséges és időigényes lehet. A fájladatok (tárolás) gyakran áttelepítése a felhőbeli operatív kiküszöbölése érdekében és az ezzel járó adatkezelési nehézségeket. Adatok áthelyezése az Azure számos lehetőséget kínál a Microsoft, és ezek a lehetőségek döntéseket kell. Adatok a megfelelő tárolótípus kiadási mentheti a szervezet több ezer dollárt minden hónapban. Néhány megfontolandó szempont:

- Adatok, amely nem érhető el nagy részét, és nem üzleti szempontból kritikus fontosságú, nem kell elhelyezni a legfontosabb költséges tárhelyre.
- Ezzel szemben fontos üzleti szempontból kritikus fontosságú adatokat kell elhelyezkednie, magasabb szintű tárolási lehetőségek.
- Az áttelepítés tervezése során leltárt az adatok, és annak érdekében, hogy a leginkább megfelelő tárolási leképezése, fontosság szerinti besorolása. Fontolja meg a költségvetés és a költségeket, valamint a teljesítmény. Költség nem feltétlenül lehet a fő döntési tényező. A legkisebb költségű lehetőséget válassza háttérszínnek hozzáférhetővé teheti a számítási feladat teljesítményét és rendelkezésre állási kockázatokat. 

### <a name="storage-data-types"></a>Storage-adattípusok

Az Azure storage-adatok különböző típusú biztosít.

**Adattípus** | **Részletek** | **Használat** 
--- | --- |  ---
**Blobok** | Optimalizáltuk, hogy nagy mennyiségű strukturálatlan objektumok, például szöveg vagy bináris adatok tárolására<br/><br/> | Adatok elérése bárhonnan HTTP/HTTPS-kapcsolaton keresztül. | Streamelés és véletlenszerű adathozzáférési forgatókönyvek esetében használható. Ha például képek és a dokumentumokat közvetlenül a böngészőben szolgáltatására, video- és hangtartalom streamelése, és biztonsági mentési és vész-helyreállítási adatok tárolása.
**Fájlok** | Felügyelt fájlmegosztások SMB 3.0 protokollon keresztül érhető el | Akkor használja, ha a migrálás a helyszíni fájlmegosztásokat, és több/kapcsolatok fájlokban tárolt adatokhoz.
**Lemezek** | A lapblobok alapján.<br/><br/> Lemez típusa (sebesség): Standard szintű (HDD vagy SSD) vagy a prémium szintű (SSD).<br/><br/>Lemezkezelés: Nem felügyelt (felügyelt lemez beállításait és tárolás) vagy felügyelt (választja, a lemez típusát és az Azure felügyeli a lemezt Ön helyett). | Prémium szintű lemezek használata virtuális gépek számára. Felügyelt lemezek használata egyszerű kezelés és a méretezést.
**üzenetsorok** | Store és a nagy számú (HTTP vagy HTTPS) hitelesített hívásokon keresztül elért üzenetek beolvasása | Alkalmazás-összetevők találkozzon aszinkron Üzenetsor-kezelés.
**Táblák** | Táblák Store. | Most már része az Azure Cosmos DB Table API.



### <a name="access-tiers"></a>Hozzáférési szintek
Az Azure storage block blob adatainak elérése különböző lehetőségeket kínál. A megfelelő hozzáférési szint kiválasztásával segít a leginkább költséghatékony módon tárolja a blokkblobok adataival.

**Típus** | **Részletek** | **Használat**
--- | --- | ---
**gyakori elérésű** | Magasabb tárolási költségek, mint a ritkán használt adatok. Alacsonyabb, mint a ritkán használt adatok hozzáférési díjak.<br/><br/>Ez az alapértelmezett szint. | Aktív gyakran használt adatok használata.
**Ritka elérésű** | Alacsonyabb tárolási költségek, mint a gyakran használt adatok. Magasabb hozzáférési költségek, mint a gyakran használt adatok.<br/><br/> Store a minimum 30 napig. | Rövid távú Store adata érhető el, de a használt ritkán.
**Archívum** | Egyes blokkblobokhoz használható.<br/><br/> Leginkább költséghatékony lehetőséget kínál. Adatok elérése drágább, mint a gyakran és ritkán használt. | Az adatokat, amelyeket server órás késése tűri legalább 180 napig maradnak szinten használható. 

### <a name="storage-account-types"></a>Storage-fióktípusok

Az Azure storage-fiókok és a teljesítményszintek különböző típusú biztosít.

**Fiók típusa** | **Részletek** | **Használat**
--- | --- | ---
**Általános célú v2 Standard** | Támogatja a blobok (letiltása, lapon, Hozzáfűzés), fájlok, lemezek, üzenetsorokat és táblákat.<br/><br/> Gyors Elérésűre, ritka elérésű és az archív elérési szint támogatja. A ZRS használata támogatott. | A legtöbb forgatókönyvek és a legtöbb típusú adatokat használva. A standard szintű tárfiókok HDD vagy SSD-alapú lehet.
**Általános célú v2 prémium** | Támogatja a Blob storage-adatokból (lapblobok). Gyors Elérésűre, ritka elérésű és az archív elérési szint támogatja. A ZRS használata támogatott.<br/><br/> SSD tárolja. | A Microsoft javasolja, hogy az összes virtuális gép használatával.
**Általános célú v1** | Hozzáférés rétegezést nem támogatott. A ZRS nem támogatja. | Ha a klasszikus Azure üzemi modellt kell használja.
**Blob** | Strukturálatlan objektumok tárolására szolgáló specializált tárfiók. Biztosítja a blokkblobok és hozzáfűző blobok csak (nincs fájl, várólista, tábla vagy a lemez tárolási-szolgáltatások). Azonos tartósságot, rendelkezésre állását, méretezhetőségét és teljesítményét, általános célú v2 biztosít. | az ilyen fiókokban nem lehet lapblobokat tárolni, és ezért nem tárolhat VHD-fájlokat. A gyakori vagy ritka elérésű hozzáférési szint állíthatja be.

### <a name="storage-redundancy-options"></a>A Storage redundanciabeállításai

Storage-fiókok használhatja a különböző típusú redundancia a rugalmasság és a magas rendelkezésre állás.

**Típus** | **Részletek** | **Használat**
--- | --- | ---
**Helyileg redundáns tárolás (LRS)** | Egy egyetlen tárolási egység egy külön tartalék és frissítési tartományban lévő replikálásával védi a helyi üzemkimaradások esetére. Megőrzi az adatok több példányban egyazon adatközpontban. Itt legalább 99,999999999 %-os (11 9\'s) tárolt objektumok számára egy adott évben. | Fontolja meg, ha az alkalmazás tárolja-e az adatokat, amelyek egyszerűen rekonstruálható.
**Zónaredundáns tárolás (ZRS)** | Védi újra egy adatközpont-meghibásodás után három tárolási fürtöket egy régió közötti replikálásával. Minden egyes tárolási fürt fizikailag elkülönített és a saját rendelkezésre állási zónában található. Itt legalább 99,9999999999 %-os (12 9\'s) tárolt objektumok tartja, hogy az adatok több példányát több adatközpontban vagy régióban egy adott évben. | Figyelembe vennie, ha a konzisztencia, tartósság és magas rendelkezésre állás. Előfordulhat, hogy nem ellen regionális katasztrófa során több zónában véglegesen érintett.
**Georedundáns tárolás (GRS)** | Egy másodlagos régióban több száz mérföld erről az elsődleges adatok replikálásával védi egy teljes régióra üzemkimaradások esetére. Itt 99,99999999999999 %-ában (16 9\'s) tárolt objektumok számára egy adott évben. | Replika nem érhető el, kivéve, ha a Microsoft kezdeményezi egy feladatátvételt a másodlagos régióba. Ha feladatátvételt hajt végre, olvasási és írási hozzáférés érhető el.
**Írásvédett Georedundáns tárolás (RA-GRS)** | GRS hasonló. Itt 99,99999999999999 %-ában (16 9\'s) tárolt objektumok számára egy adott évben | Itt és 99,99 %-os olvasási rendelkezésre állás azáltal, hogy olvasási hozzáférés Georedundáns tároláshoz használt második régióban.

**tudj meg többet:**
- [Felülvizsgálat](https://azure.microsoft.com/pricing/details/storage/) Azure Storage szolgáltatás díjszabása.
- [Ismerje meg](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) Azure Import/Export a migrálás nagy mennyiségű adatokat az Azure-blobok és fájlok. 
- [Hasonlítsa össze](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) blobok, fájlok és a lemez tárolási adattípusok.
- [További](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) hozzáférés szintekről.
- [Felülvizsgálat](https://docs.microsoft.com/azure/storage/common/storage-account-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) különböző típusú tárfiókok.
- Ismerje meg [tárhely-redundancia](https://docs.microsoft.com/azure/storage/common/storage-redundancy), [LRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), [ZRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), [GRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), és [írásvédett GRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#read-access-geo-redundant-storage).
- [További](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) Azure Files szolgáltatással kapcsolatos.



## <a name="best-practice-leverage-azure-hybrid-benefits"></a>Ajánlott eljárás: Használja ki az Azure Hybrid benefits

Év rendszerek – például a Windows Server és SQL Server szoftver befektetés, mert a Microsoft egyedi helyzetben, hogy kínálja fel a felhőben, jelentős kedvezményeket, amely nem feltétlenül adja meg az egyéb felhőszolgáltatók, érték szerepel. 

Az integrált Microsoft helyszíni/Azure-termék portfóliójának állít elő, versenyképes és költség előnyeit. Ha jelenleg rendelkezik az operációs rendszer vagy más szoftverlicencelés garancia (SA), a felhőben az Azure Hybrid Benefittel ezeket a licenceket eltarthat Önnel.

**tudj meg többet:**

- [Vessen egy pillantást](https://azure.microsoft.com/pricing/hybrid-benefit/) a Hybrid Benefit – megtakarítási Kalkulátor.
- [További](https://azure.microsoft.com/pricing/hybrid-benefit/) Hybrid Benefit Windows Server kapcsolatban.
- [Felülvizsgálat](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) az SQL Server Azure virtuális gépek díjszabási útmutatóját.


## <a name="best-practice-use-reserved-vm-instances"></a>Ajánlott eljárás: Fenntartott VM-példányok használata

A legtöbb felhőalapú platform, használatalapú fizetéses beállítása. A modell megadja hátrányait, mivel a dinamikus számítási feladatok nem feltétlenül tudja lesz. Ha megad egy munkaterhelés számára egyértelmű céljaira, hozzájárul az infrastruktúra.

Azure Reserved VM instances használatával, akkor fizessen elő az egy vagy három éves időszakra, Virtuálisgép-példányhoz. 

- Előre fizetett keretösszeg erőforrásokért kell a kedvezményeket is nyújt.
- A használatalapú fizetéssel akár 72 %-kal VM, SQL database számítási, az Azure Cosmos DB vagy egyéb erőforrás-használati díjak jelentősen csökkentheti. 
- Foglalások számlázási engedményt adja meg, és az erőforrások futási állapotát nem befolyásolják.
- Fenntartott példányokat lemondhatja a szolgáltatásokat.

![Fenntartott példányok](./media/migrate-best-practices-costs/reserve.png)
*fenntartott Azure-beli virtuális gépek*

**tudj meg többet:**
- [Ismerje meg](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) Azure foglalásokat.
- [Olvasási](https://azure.microsoft.com/pricing/reserved-vm-instances/#faq) a fenntartott példányok – gyakori kérdések.
- [Útmutató díjszabási](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) SQL Server Azure virtuális gépekhez.


## <a name="best-practice-aggregate-cloud-spend-across-subscriptions"></a>Ajánlott eljárás: Összesített felhő költsége előfizetések között

Fontos elkerülhetetlen, hogy végül fog egynél több Azure-előfizetéssel rendelkezik. Például szükség lehet további-előfizetéseket külön fejlesztési és éles környezetek határokat, vagy előfordulhat, hogy egy platform, amely minden ügyfél számára különálló előfizetés szükséges. Összesített adatok egységes platformban az összes előfizetés jelentéskészítés lehetővé teszi, akkor olyan valuable funkció.

Ehhez használhatja az Azure Cost Management API-kat. Ezt követően után az adatok összevonása egy egyetlen forrásból, például az Azure SQL-be, használhatja eszközökkel, mint például a Power BI az összesített adatokat illesztésének. Összesített előfizetés, és részletes jelentések hozhat létre. Például a felhasználók számára, akiknek szükség van az előjelzéses betekintést Költségkezelés, hozhat létre a költségek, részleg, erőforráscsoport alapján meghatározott nézetek stb. Ön nem kell megadnia őket teljes hozzáféréssel az Azure számlázási adatok.

**tudj meg többet:**

- [Áttekintés](https://docs.microsoft.com/azure/billing/billing-consumption-api-overview) az Azure Consumption API.
- [Ismerje meg](https://docs.microsoft.com/power-bi/desktop-connect-azure-consumption-insights) Azure Consumption Insightshoz a Power BI Desktop csatlakozik.
- [Ismerje meg, hogyan](https://docs.microsoft.com/azure/billing/billing-manage-access) számlázási adatokat az Azure szerepköralapú hozzáférés-vezérlés (RBAC) használatával való hozzáférés kezelése.
 
## <a name="after-migration"></a>Áttelepítés után

A sikeres áttelepítéshez a számítási feladatok, és néhány hét használati adatokat gyűjt után egy egyértelmű kiindulópont annak megértéséhez, erőforrások költségeit.
- Adatok elemzése, mivel az Azure-erőforráscsoportot és az erőforrások költségvetés alapterv létrehozása megkezdése.
- Ezt követően értelmezését, ahol a felhő költségvetést töltött, továbbá csökkentse a költségeket, hogyan elemezheti.

Ebben a szakaszban szereplő eljárások közé tartozik az Azure Cost Managementtel a költségek a költségvetés és -elemzés, az erőforrások figyelése és erőforrás-csoport költségvetése végrehajtási és optimalizálása a figyelés, tárolási és virtuális gépek.

## <a name="best-practice-use-azure-cost-management"></a>Ajánlott eljárás: Az Azure Cost Management használata

A Microsoft biztosít az Azure Cost Management segítségével nyomon követheti a Költekezési, a következő:

- Segít figyelése és szabályozása az Azure-költségeit, és erőforrások felhasználásának optimalizálása.
- Áttekinti a teljes előfizetés és az összes erőforrását, és javaslatokat tesz.
- Itt egy teljes körű API-val, külső eszközökkel és jelentéskészítési pénzügyi rendszerek integrálásához.
- Erőforrás-használat nyomon követi, és a felhőköltségek kontrollja egyetlen, egységesített nézetben.
- Itt részletes működési és költségvetési információk annak érdekében, hogy megalapozott döntéseket hozhasson.

Cost Management, a következőket teheti:


- **Hozzon létre egy költségvetés**: Hozzon létre egy pénzügyi elszámoltathatóság költségvetés.
    - Azt is figyelembe a szolgáltatások felhasználásához, vagy iratkozzon fel egy adott időszakra (havonta, negyedévente, évente) és a egy hatókör (előfizetések vagy erőforráscsoportok). Például egy havi, negyedéves és éves időszakra egy Azure-előfizetés költségvetés hozhat létre.
    - Miután létrehozta a költségvetést, a költségek elemzése jelenik meg. Megtekintés ellen aktuális költségeket a költségvetés az egyik első lépéseként van szükség, amikor a költségek elemzése és a költségek.
    - E-mail-értesítések küldhetők költségvetési küszöbértékek elérésekor.
    - Az Azure storage, elemzés céljából exportálhatja a költségek felügyeleti adatokat.

    ![A Cost Management költségvetés](./media/migrate-best-practices-costs/budget.png) *Azure Cost Management költségvetés*

- **Hajtsa végre a költségelemzés**: Get-költségelemzés vizsgálata és elemzése a szervezeti költségeket, a segítségével megismerheti, hogyan miatt felmerülő költségekkel, és a költési trendek azonosítására.
    - Költségelemzés a nagyvállalati szerződéssel rendelkező felhasználók számára érhető el.
    - Megtekintheti a cost analysis-adatok különböző hatókörök, például úgy, hogy részleg, fiók, előfizetésekhez vagy erőforráscsoportokhoz.
    - A költségek elemzése, amely megjeleníti az aktuális hónap a teljes költségét, és összesített napi költségek kérheti le. 

    ![Felügyeleti elemzés](./media/migrate-best-practices-costs/analysis.png) *Azure Cost Management elemzése*
- **Javaslatok**: Javaslatok az Advisor, amelyek bemutatják, hogyan lehet optimalizálni, és növelheti a hatékonyságot.


**tudj meg többet:**

- [Áttekintés](https://docs.microsoft.com/azure/cost-management/overview) az Azure Cost Management.
- [Ismerje meg, hogyan](https://docs.microsoft.com/azure/cost-management/cost-mgt-best-practices) optimalizálása a felhőbe való befektetéséből, az Azure Cost Managementbe.
- [Ismerje meg, hogyan](https://docs.microsoft.com/azure/cost-management/use-reports) Azure Cost Management jelentéseinek használata.
- [Első oktatóanyag](https://docs.microsoft.com/azure/cost-management/tutorial-acm-opt-recommendations?toc=/azure/billing/TOC.json) a javaslatok a költségek optimalizálásához.
- [Felülvizsgálat](https://docs.microsoft.com/rest/api/consumption/budgets) az Azure-használati API-t.

## <a name="best-practice-monitor-resource-utilization"></a>Ajánlott eljárás: Erőforrás-használat figyelése

Az Azure-ban fizet, amit ténylegesen használ erőforrásokat vesznek igénybe, és hogy nem kell fizetnie, ha nem. A virtuális gépek esetében a számlázás következik be egy virtuális gép le van foglalva, és nem kell fizetnie után a virtuális gép fel van szabadítva. Ezt szem kell használatban lévő virtuális gépek figyelése, és ellenőrizze a virtuális gép méretezése.

- Folyamatosan értékelni alaptervek meghatározni a virtuális gépek számítási feladataihoz.
- Például ha a számítási feladatok használt erősen hétfőtől péntekig 6 között 8 -kor, de ezen az időszakon kívül alig használja, akkor sikerült Visszaléptetés a virtuális gépek kívüli időszakokban. Ez arra utalhat, Virtuálisgép-méretek módosítása, vagy a virtuális gép méretezési csoportokat használ az automatikus skálázási virtuális gépekhez felfelé vagy lefelé.
- Néhány vállalat "késleltetés", a virtuális gépek azáltal, a naptár, amikor azok elérhetőnek kell lennie, és ha szükség van rájuk nem. 
- Virtuális gép figyelése, mellett célszerű figyelemmel kísérni más hálózati erőforrások, például az ExpressRoute és a virtuális hálózati átjárók esetében, illetve a használatát.
- Virtuálisgép-használat számos Microsoft-eszközöket, beleértve az Azure Cost Managementbe az Azure Monitor és az Azure Advisor használatával követheti nyomon. Harmadik felektől származó eszközök is elérhetők.  

**tudj meg többet:**
- Áttekintheti a [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) és [az Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview).
- [Első](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations) Advisor ajánlásait költsége.
- [Megtudhatja, hogyan [optimalizálhatja a költségeket a javaslatok](https://docs.microsoft.com/azure/cost-management/tutorial-acm-opt-recommendations?toc=/azure/billing/TOC.json), és [váratlan költségek elkerülése](https://docs.microsoft.com/azure/billing/billing-getting-started).
- [Ismerje meg](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit/) az Azure-erőforrás-optimalizálási (ARO) eszközkészlet

## <a name="best-practice-implement-resource-group-budgets"></a>Ajánlott eljárás: Erőforrás-csoport költségvetése megvalósítása

Erőforráscsoportok gyakran költség határok képviselő használhatók. Együtt ez a használati minta az Azure-csapat továbbra is nyomon követheti és elemezheti a költségkeret-beállítási különböző szinteken, beleértve a költségvetés az erőforráscsoportot és az erőforrások létrehozására erőforrás új és továbbfejlesztett módon fejleszthet.  

- Egy erőforrás-csoport költségvetés segítségével nyomon követheti egy erőforráscsoportot a költségeket.
- Riasztások aktiválása céljából, és futtassa a forgatókönyvek széles választéka, amíg a költségvetés elérte vagy túllépte a. 

**tudj meg többet:**

- [Ismerje meg, hogyan](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario) költségvetése Azure-költségek kezelésére.
- [Kövesse az oktatóanyag](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?toc=/azure/billing/TOC.json) létrehozása és kezelése az Azure-költségvetési.


## <a name="best-practice-optimize-azure-monitor-retention"></a>Ajánlott eljárás: Az Azure Monitor megőrzési optimalizálása

Erőforrások áthelyezése az Azure-ba, és azokat a diagnosztikai naplózás engedélyezése, a naplóadatok sok hozza létre. Általában a naplózási adatokat küld egy tárfiókot, amelyet a Log Analytics-munkaterület van leképezve.

- Minél hosszabb a napló Adatmegőrzés időtartama, minél több adat lesz.
- Nem minden naplóadatok nem egyenlő, és bizonyos erőforrásokhoz, mint a többi további naplóadatokat hoz létre.
- Miatt, szabályozásoknak és a megfelelőség valószínű, hogy néhány hosszabb, mint a többi erőforrás adatainak megőrzése kell.
- A naplófájl tárolási költségek optimalizálása és a naplózási adatok kell között egy gondos sort kell vezeti.
- Javasoljuk, hogy a lehetőség mérlegelése és, hogy az nvdimm-n nem fontos a naplók megőrzése spending pénzt közvetlenül a migrálás befejezése után a naplózás beállítása.

**tudj meg többet:**

- [Ismerje meg](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs) felhasználás és becsült költségek figyelése.
 
## <a name="best-practice-optimize-storage"></a>Ajánlott eljárás: A tárolási költségek optimalizálására

Ha követte a lépéseket ajánlott eljárások az áttelepítés előtt storage kiválasztása, valószínűleg az előnyöket összekötőkkel vannak. Vannak azonban valószínűleg további tárolási költségek, amelyek továbbra is optimalizálhatja. Idővel blobok és fájlok elavulttá váltak. Adatok előfordulhat, hogy nem használható többé, de jogszabályi követelmények arra utalhat, hogy szeretne-e egy bizonyos ideig legyen. Mint ilyen előfordulhat, hogy nem kell tárolni, a nagy teljesítményű tárolást, az eredeti áttelepítés során használt.

Azonosításával és elavult adatok áthelyezése a olcsóbb tárolására szolgáló területek óriási hatással vannak a havi tárolási költségvetést, és költségmegtakarítást. Az Azure számos módon segítenek azonosítani és majd tárolja ezeket az elavult adatokat biztosít.

- A hozzáférési réteget használó általános célú v2, kevésbé fontos adatok áthelyezése a gyors elérésűről lassú elérésű és az archivált előnyeit.
- A StorSimple használatával segítheti a testre szabott szabályzatok alapján elavult adatok áthelyezéséhez. 

**tudj meg többet:**
- [További](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) hozzáférés szintekről.
- [Áttekintés](https://docs.microsoft.com/azure/azure-monitor/overview) storsimple, és [StorSimple díjszabás](https://azure.microsoft.com/pricing/details/storsimple/).

## <a name="best-practice-automate-vm-optimization"></a>Ajánlott eljárás: Automatizálhatja a virtuális gép optimalizálása

A végleges célja az, hogy egy virtuális Gépet a felhőben futó, hogy a CPU, memória és az lemezt használ. Ha a nem optimalizált virtuális gépek felderítése, vagy ha a virtuális gépek nem használható fel a gyakori egymást, logikus leállíthatja őket, vagy linearity azokat a Virtuálisgép-méretezési csoportok használatával.

Egy virtuális Gépet az Azure Automation, a virtuális gép méretezési csoportok, a automatikus leállítási és a parancsfájlalapú vagy 3. külső megoldások is optimalizálhatja. 

**tudj meg többet:**

- [Ismerje meg, hogyan](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision) vertikális automatikus méretezés.
- [Ütemezés](https://azure.microsoft.com/updates/azure-devtest-labs-schedule-vm-auto-start/) egy virtuális gép automatikus indítása.
- [Ismerje meg, hogyan](https://docs.microsoft.com/azure/automation/automation-solution-vm-management) indítása és leállítása a virtuális gépek munkaidőn az Azure Automationben.
- [További információ] [az Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview), és a [Azure erőforrás-optimalizálás (ARO) eszközkészlet](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit/).

## <a name="best-practices-use-logic-apps-and-runbooks-with-budgets-api"></a>Ajánlott eljárások: A Logic Apps és a runbook használata költségvetése API-val

Az Azure biztosít egy REST API-t, amely hozzáfér a bérlő számlázási adatokat.

- Használhatja a költségvetés API külső rendszerekkel és az API-adatokból készített metrikák által aktivált munkafolyamatok integrálásához.
- Használati és erőforrás olvashatók be az előnyben részesített adatelemző eszközökkel.
- Az Azure erőforrás-használati és RateCard API-k segítségével pontosan előrejelezheti és felügyelheti a költségeket.
- Az API-k egy erőforrás-szolgáltató vannak megvalósítva, és az API-k az Azure Resource Manager által elérhetővé tett szerepelnek.
- A költségvetéshez API integrálhatók az Azure Logic Apps és a Runbookok.

**tudj meg többet:**

- [További](https://docs.microsoft.com/rest/api/consumption/budgets) a költségvetés API-val kapcsolatos.
- [Elemzések lekérése](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) be Azure-használat a számlázási API-val.


## <a name="best-practice-implement-serverless-technologies"></a>Ajánlott eljárás: Kiszolgáló nélküli technológiák megvalósítása

Virtuális gépek számítási feladataihoz gyakran települnek át, "adott állapotában" üzemszünet elkerülése érdekében. Virtuális gépek gyakran akkor is tartalmazhat, feladatok, amelyek időszakos futtatását, egy rövid ideig tart, vagy másik lehetőségként több órával. Például például a Windows ütemezett feladatokat futtató virtuális gépek feladat scheduler vagy a PowerShell-parancsfájlokat. Ezek a feladatok nem futnak, amikor meg azonban üzemeltetést a virtuális gép és a tárolási költségeket lemez.

Az áttelepítés után ilyen típusú feladatok alapos áttekintése után érdemes lehet a kiszolgáló nélküli technológiák, például az Azure Functions vagy az Azure Batch-feladat áttelepítsék azokat. Ezzel a megoldással nem kell többé kezeli és tartja karban a virtuális gépeket, hogyan lehet megfelelővé tenni egy további költségek mentése. 

**tudj meg többet:**
- [Ismerje meg](https://azure.microsoft.com/services/functions/) az Azure Functions
- [Tudjon meg többet](https://azure.microsoft.com/en-us/services/batch/) Azure Batch
  
## <a name="next-steps"></a>További lépések 

Tekintse át a többi ajánlott eljárásokat:

- [Ajánlott eljárások](migrate-best-practices-security-management.md) biztonsági és felügyeleti migrálás után.
- [Ajánlott eljárások](migrate-best-practices-networking.md) hálózati migrálás után.

