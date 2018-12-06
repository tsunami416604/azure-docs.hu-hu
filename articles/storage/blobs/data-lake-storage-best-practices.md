---
title: Ajánlott eljárások az Azure Data Lake Storage Gen2 |} A Microsoft Docs
description: Adatbetöltés, dátum biztonsági és az Azure Data Lake Storage Gen2 (korábbi nevén Azure Data Lake Store) használatával kapcsolatos teljesítményadatok kapcsolatos ajánlott eljárások megismerése
services: storage
author: sachinsbigdata
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: article
ms.date: 12/06/2018
ms.author: sachins
ms.openlocfilehash: b62abe668ec086982683a29706dcf6ca36d3f682
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52975185"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2 használatának ajánlott eljárásai

Ebben a cikkben megismerkedhet ajánlott eljárásokat és az Azure Data Lake Storage Gen2 használatának szempontjai. Ez a cikk információkat biztosít a biztonság, a teljesítmény, a rugalmasság és a Data Lake Storage Gen2 figyelést biztosít. Data Lake Storage Gen2 előtti szolgáltatások, például az Azure HDInsight valóban big Data típusú adatokkal végzett bonyolult volt. Kellett az adatok horizontális skálázását több Blob storage-fiókok között, hogy petabájtos tárolás és az optimális teljesítmény érdekében, hogy nagy mennyiségű érhető el. A Data Lake Storage Gen2 a szigorú korlátok mérete és a teljesítmény érdekében a legtöbb el lesznek távolítva. Vannak azonban még néhány szempontot, hogy ez a cikk ismerteti, hogy megjelenik a Data Lake Storage Gen2 a legjobb teljesítményt.

## <a name="security-considerations"></a>Biztonsági szempontok

Az Azure Data Lake Storage Gen2 POSIX hozzáférés-vezérlés az Azure Active Directory (Azure AD) felhasználók, csoportok és egyszerű szolgáltatásokat kínál. Ezek a hozzáférés-vezérlés állítható meglévő fájlokat és könyvtárakat. A hozzáférés-vezérlés is automatikusan alkalmazható új fájlok vagy könyvtárak alapértelmezett engedélyek létrehozására használható. További részleteket a Data Lake Storage Gen2 ACL-ek esetén érhető el [hozzáférés-vezérlés az Azure Data Lake Storage Gen2](storage-data-lake-storage-access-control.md).

### <a name="use-security-groups-versus-individual-users"></a>Egyéni felhasználók és biztonsági csoportok használata

Big Data típusú adatok az Data Lake Storage Gen2 WWhen, akkor valószínű, hogy az egyszerű szolgáltatás használja például az adatok Azure HDInsight-szolgáltatások engedélyezése. Azonban lehetnek olyan esetekben, ahol egyéni felhasználók számára, valamint az adatok hozzáférésre van szükségük. Minden esetben határozottan fontolja meg az Azure Active Directory használatával [biztonsági csoportok](../common/storage-auth-aad.md) helyett egyéni felhasználók számára a fájlok és könyvtárak.

Engedélyek hozzárendelése egy biztonsági csoportot, után felhasználók hozzáadása és eltávolítása a csoportból Data Lake Storage Gen2-re frissítéseket nem igényel. Ezzel is biztosíthatja nem haladhatja meg a hozzáférés-vezérlési lista (ACL) egy hozzáférés-vezérlési bejegyzések maximális száma. Ez a szám jelenleg 32, (beleértve a négy POSIX-stílusú ACL-eket mindig társított minden fájl és könyvtár): a tulajdonos, a tulajdonoscsoport, a maszk és más. Minden könyvtár rendelkezhet a kétféle ACL-t, a hozzáférési ACL-t és az alapértelmezett ACL-t, összesen 64 hozzáférés-vezérlési bejegyzéseit. Ezen ACL-lel kapcsolatos további információkért lásd: [hozzáférés-vezérlés az Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="security-for-groups"></a>Biztonsági csoportok

A felhasználók hozzáférhetnek a storage-fiókban lévő adatok hierarchikus névtér esetében engedélyezve van, esetén ajánlott használni az Azure Active Directory biztonsági csoportokat. A következőben néhány ajánlott csoportok a kezdéshez lehet **ReadOnlyUsers**, **WriteAccessUsers**, és **FullAccessUsers** a fájlrendszer, és még akkor is legfelső szintű tájékozódhat a különböző kulcs alkönyvtárak. Ha bármely más várható csoportjait, amelyek később hozzáadhatók, de nem azonosított felhasználók, mégis, akkor érdemes megfontolni, helyőrző biztonsági csoportok, amelyek hozzáféréssel rendelkeznek bizonyos mappák létrehozása. Biztonsági csoport használatával biztosítja, hogy mennyi ideig feldolgozási időt elkerülheti, ha új engedélyek hozzárendelése a fájlok ezer.

### <a name="security-for-service-principals"></a>Az egyszerű szolgáltatások biztonsága

Az Azure Active Directory-szolgáltatásnevek általában használják a szolgáltatások, például az Azure Databricks a Data Lake Storage Gen2 hozzáférés az adatokhoz. Sok felhasználónál egy egyetlen Azure Active Directory egyszerű szolgáltatás sebesség már elegendő lehet, és azt is teljes körű jogosultságokkal rendelkeznek a Data Lake Storage Gen2 Filesystem gyökérmappájában. Más ügyfelek különböző szolgáltatásnevekkel, ahol egy fürt az adatokat, és a egy másik fürtöt csak olvasási hozzáféréssel rendelkező teljes hozzáféréssel rendelkezik több fürt szükség lehet. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>Az Azure-szolgáltatásokhoz a Data Lake Storage Gen2 tűzfal engedélyezése

Data Lake Storage Gen2 ne tudják bekapcsolni a tűzfalat, és korlátozza a hozzáférést csak Azure-szolgáltatásokkal, javasoljuk, hogy korlátozza a vektor külső támadásokat, amely támogatja. Tűzfal a storage-fiók az Azure Portalon keresztül is engedélyezhető a **tűzfal** > **tűzfal engedélyezése (bekapcsolva)** > **Azure-szolgáltatásokhozvalóhozzáférésengedélyezése** beállítások.

Databricks az előzetes verziójú funkció használatát az Azure Databricks-fürtöket ad hozzá egy virtuális hálózatot, amely előfordulhat, hogy engedélyezi a hozzáférést a Storage-tűzfalon keresztül igényli. A funkció engedélyezéséhez, helyezze el egy támogatási kérést.

## <a name="resiliency-considerations"></a>Rugalmassággal kapcsolatos szempontok

Amikor a rendszer a Data Lake Storage Gen2 vagy bármilyen felhőalapú szolgáltatás kiépítése, meg kell fontolnia a rendelkezésre állási követelmények vonatkoznak, és hogyan reagáljon a lehetséges zavart a szolgáltatásban. Problémát sikerült honosított, a példány, vagy akár régióra kiterjedő, így kellene csomagot is fontos. Attól függően, a helyreállítási időre vonatkozó célkitűzés és a helyreállítási pont célkitűzés SLA-k a számítási feladatok számára egy magas rendelkezésre állás és vészhelyreállítás helyreállítási jobban vagy kevésbé agresszív stratégiát érdemes választania.

### <a name="high-availability-and-disaster-recovery"></a>Magas rendelkezésre állás és vészhelyreállítás

Magas rendelkezésre ÁLLÁS és vészhelyreállítás (DR) is néha egyesíthet, bár a különösen akkor, ha az adatok betölt egy némileg eltérő stratégia mindegyik rendelkezik. Data Lake Storage Gen2 már kezeli a helyi hardverhibák ellen védő összetevői 3 x replikáció. Ezenfelül más replikációs beállítások, például a ZRS során GRS magas rendelkezésre ÁLLÁS javítása, és RA-GRS javítása DR. A terv létrehozását, a magas rendelkezésre ÁLLÁSÚ, egy szolgáltatáskimaradás esetén a számítási feladatok hozzá kell férnie a legfrissebb adatok a lehető leggyorsabban helyben vagy egy új régióban külön-külön replikált példányra között.

A Vészhelyreállítási stratégia előkészítése, illetve a régió, végzetes hiba nem túl valószínű esetben fontos is egy másik régióba GRS vagy RA-GRS-replikáció használatával replikálja az adatokat. Figyelembe kell venni a szélsőséges eseteket, ahol lehetséges, hogy szeretne létrehozni, rendszeres időközönként pillanatképek tud visszatérni adatsérülés például követelményeinek. Fontosság és az adatok méretétől függően a működés közbeni változások pillanatképeket 1 – 6 – és 24 órás időszakokra, érdemes kockázati tolerancia megfelelően.

A Data Lake Storage Gen2 adatrugalmasság érdekében ajánlott a georeplikáció GRS vagy RA-GRS, amely eleget tesz a magas rendelkezésre ÁLLÁS/Vészhelyreállítás követelmények keresztül az adatok. Emellett érdemes lehet az alkalmazás automatikusan átadja a feladatokat a másodlagos régióba eseményindítók figyelési a Data Lake Storage Gen2 segítségével módszert vagy hossza sikertelen kísérlet, vagy legalább a rendszergazdák kézi beavatkozás értesítést küldeni. Ne feledje, hogy nincs-e kompromisszummal jár, és a egy szolgáltatás Várakozás-ba irányuló feladatátvétel ismét online elérhető lesz.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>A Distcp használata a két hely közötti adatáthelyezés

Rövid a elosztott, a DistCp-példány Linux parancssori eszköz, amely együttműködik a Hadoop és a két hely között elosztott adatáthelyezés biztosít. A két helyen lehet a Data Lake Storage Gen2, HDFS, vagy az S3. Ezzel az eszközzel (például a HDInsight) Hadoop-fürtön a MapReduce-feladatok használatával horizontális felskálázása az összes csomóponton. A Distcp nélkül speciális hálózati tömörítés berendezések big Data típusú adatok áthelyezése a leggyorsabb módot kínálni arra számít. A Distcp is lehetőséget biztosít, hogy csak a változásokat között két helyen, automatikus újrapróbálkozások kezeli, valamint számítási dinamikus méretezés frissítése. Ez a megközelítés akkor rendkívül hatékony, ha a replikálása, például a Hive és a Spark táblákat, amelyeken egyetlen címtárban sok nagy fájlt betölt, és, csak a módosított adatokat másolni kívánja. Ebből kifolyólag a Distcp a leginkább ajánlott eszköz big Data típusú adatok adattárak közötti másolást.

Másolási feladat indítható el gyakorisága vagy adatok eseményindítók, valamint azokat az Linux cron feladat használatával Apache Oozie-munkafolyamatokkal. Nagy számításigényű replikációs feladatok esetében ajánlott egy külön HDInsight Hadoop-fürtöt, amely hangolva, és kifejezetten a másolási feladatokat a horizontálisan üzembe helyezését. Ez biztosítja, hogy a másolási feladatokat nem befolyásolják a kritikus feladatok. Replikációs futtat egy elég széles ahhoz gyakoriság, ha a fürt akkor is le lehessen állítani minden egyes feladat között. Ha feladatátvétele másodlagos régióba, ügyeljen arra, hogy egy másik fürtre is működésbe új adatokat az elsődleges Data Lake Storage Gen2-fiók való újbóli replikáláshoz, visszatér a után a másodlagos régióban. A Distcp használata példákért lásd: [az adatok Azure Storage-Blobok és a Data Lake Storage Gen2 közötti másolásához használja a Distcp](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Az Azure Data Factory segítségével a másolási feladatok ütemezése

Az Azure Data Factory egy másolási tevékenységgel másolási feladatok ütemezéséhez is használható, és akkor is beállíthatja a gyakoriságát, a másolás varázsló használatával. Ne feledje, hogy az Azure Data Factory felhőbeli adatáthelyezési egységek (DMUs) legfeljebb, és végül caps az átviteli sebesség/számítási nagy mennyiségű adat számítási feladatokhoz. Emellett az Azure Data Factory jelenleg nem használt összegeket különbözeti frissítések Data Lake Storage Gen2 fiókokba, így címtárakat, például a Hive táblák teljes másolat replikálásához lenne szükség. Tekintse meg a [data factory cikk](../../data-factory/load-azure-data-lake-storage-gen2.md) további információ a Data Factory másolás.

## <a name="monitoring-considerations"></a>Felügyeleti szempontok

Data Lake Storage Gen2 a Data Lake Storage Gen2-fiókja alatt az Azure Portalon és az Azure monitorban metrikákat nyújt. Data Lake Storage Gen2 rendelkezésre állása az Azure-portálon jelenik meg. A Data Lake Storage Gen2-fiókok legfrissebb elérhetőségének beolvasása, futtatnia kell a saját szintetikus tesztet végrehajtva ellenőrizze a rendelkezésre állási. Más mérőszámokat, például a tárterület teljes kihasználtsága, olvasási/írási kérelmek és a bejövő/kimenő forgalom érhetők el alkalmazások figyelésével adatbáziscsoportok, és riasztásokat is kiválthatják a küszöbértékek (például átlagos várakozási ideje vagy percenkénti hibák száma) túllépése esetén.

## <a name="directory-layout-considerations"></a>Directory elrendezés kapcsolatos szempontok

Tárol adatokat a data lake-be, ha fontos, előre megterveznie az adatok struktúráját, így a biztonsági, particionálás, és a feldolgozási ténylegesen felhasznált. Az alábbi javaslatokat számos alkalmazható minden big data-számítási feladatokhoz. Minden számítási feladathoz tartozik az adatokat használja fel, de néhány gyakori elrendezések az IoT használatakor érdemes figyelembe venni az alábbiakban és batch-forgatókönyv eltérő követelmények vonatkoznak.

### <a name="iot-structure"></a>IoT-struktúra

Az IoT-terheléseket az adattár, amely számos olyan termékeket, eszközök, szervezetek és ügyfelek átfogóan alatt érkezett adatok nagy fokú is lehet. Fontos, előre megterveznie a szervezet, a biztonság és a hatékony feldolgozás érdekében le-stream fogyasztók számára az adatok directory elrendezést. Megfontolandó általános sablon lehet az alábbi elrendezés:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Például egy repülőgép-motor, az Egyesült Királyság belül telemetriai üzenetsorokra hasonló lehet az alábbi struktúrával:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Ezért fontos a dátumot a könyvtárstruktúra végén helyezi van. Ha meg szeretné zárolását, így bizonyos régiókban, vagy a felhasználók/csoportok tárgyára, majd egyszerűen megteheti a POSIX-engedélyekkel. Ellenkező esetben hiba történt egy bizonyos biztonsági csoport korlátozhatja a csak az Egyesült Királyság adatokat és bizonyos síkok nézetben kell, ha a dátum struktúrával elöl egy külön engedély lenne szükséges számos címtárak minden órában a könyvtárban. Ezenkívül elöl kellene a dátum struktúra lenne alapján exponenciálisan növeli a könyvtárak száma, ideje történt.

### <a name="batch-jobs-structure"></a>Batch-feladatok struktúra

Egy magas szintű egy általánosan használt megközelítés a kötegelt feldolgozás már adatok kerül egy "a" Directory. Ezt követően az adatok feldolgozása után elhelyezhető az új adatok egy "lejárt" könyvtár az alárendelt folyamatok felhasználásához. A könyvtárstruktúra néha látható feladatok, amelyek az egyes fájlokon feldolgozást, és nem feltétlenül igényelnek a nagymértékben párhuzamos feldolgozási a nagy méretű adatkészleteken. A fenti ajánlott IoT struktúra, például egy jó könyvtárstruktúrát rendelkezik a szülő-szintű könyvtárak többek között az régió és tárgyára (például a szervezet, a termék/gyártó). Ez a struktúra segít az adatok védelme a szervezet és a számítási feladatok az adatok jobb kezelése. Továbbá fontolja meg a dátumot és időpontot a struktúra, jobb, szűrt keresések, biztonsági és az automatizálást a feldolgozás való használatának engedélyezése. A dátum struktúra szintjét az időközt, amelyen az adatok feltöltése vagy feldolgozása, például óránként, naponta, vagy akár havi határozza meg.

Egyes esetekben a fájl feldolgozása nem sikerül miatt adatsérülés vagy váratlan formátumban. Ezekben az esetekben könyvtárstruktúrát előnye származhat egy **/hibás** áthelyezni a fájlokat, és a további ellenőrzési mappát. A batch-feladat lehet, hogy kezelik a jelentéskészítési vagy az értesítés ezek *hibás* fájlok manuális beavatkozásra. Vegye figyelembe a következő sablon struktúrát:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Például egy marketing vállalat napi adatok kivonatok ügyfél frissítések kap az ügyfelek Észak-Amerikában. Az alábbi kódrészlet utasítás, előtti és utáni feldolgozás alatt:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

Kötegelt adatok feldolgozása folyamatban közvetlenül a Hive-adatbázisok vagy a hagyományos SQL-adatbázisok a gyakori esetben nincs szükség egy **/in** vagy **/out** óta már kerül a kimeneti mappa egy Különítse el a Hive-tábla és a külső adatbázisok mappát. Például napi kivonatokat a felhasználók által az Azure Data Factory, az Apache Oozie, például a megfelelő mappákat, és a vezénylési lenne megnyitja vagy Apache légmozgás dolgozza fel, és adatokat egy Hive-táblába írni egy napi Hive- vagy Spark feladat lép működésbe.
