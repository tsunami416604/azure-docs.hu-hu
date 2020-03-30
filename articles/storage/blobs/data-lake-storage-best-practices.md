---
title: Gyakorlati tanácsok az Azure Data Lake Storage Gen2 használatával kapcsolatban | Microsoft dokumentumok
description: Ismerje meg az azure Data Lake Storage Gen2 (korábbi nevén Azure Data Lake Store) használatával kapcsolatos adatbetöltéssel, dátumbiztonsággal és teljesítménnyel kapcsolatos gyakorlati tanácsokat.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: ac4e126c7ecbd1fc781db74e5b19635b273bbb34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299673"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Gyakorlati tanácsok az Azure Data Lake Storage Gen2 használatával kapcsolatban

Ebben a cikkben az Azure Data Lake Storage Gen2 használatával kapcsolatos gyakorlati tanácsokat és szempontokat ismerteti. Ez a cikk a Data Lake Storage Gen2 biztonságával, teljesítményével, rugalmasságával és figyelésével kapcsolatos információkat tartalmaz. A Data Lake Storage Gen2 előtt a szolgáltatások, például az Azure HDInsight valóban nagy adathalmazaival való együttműködés összetett volt. Több Blob storage-fiókon keresztül kellett adatokat maszatosan leadni, hogy a petabájtos tárolás és az optimális teljesítmény ezen a skálán elérhető legyen. A Data Lake Storage Gen2 támogatja az 5 TB-os egyedi fájlméreteket, és a teljesítményre vonatkozó szigorú korlátok többségét eltávolították. Azonban még mindig vannak olyan szempontok, amelyek ezt a cikket ismerteti, így a legjobb teljesítményt a Data Lake Storage Gen2.

## <a name="security-considerations"></a>Biztonsági szempontok

Az Azure Data Lake Storage Gen2 POSIX-hozzáférés-vezérlést kínál az Azure Active Directory (Azure AD) felhasználóinak, csoportjainak és egyszerű szolgáltatásainak. Ezek a hozzáférés-vezérlők beállíthatók a meglévő fájlokra és könyvtárakra. A hozzáférés-vezérlés alapértelmezett engedélyek létrehozására is használható, amelyek automatikusan alkalmazhatók az új fájlokra vagy könyvtárakra. A Data Lake Storage Gen2 ACL-jairól további részleteket az [Azure Data Lake Storage Gen2 hozzáférés-vezérlési szolgáltatása talál.](storage-data-lake-storage-access-control.md)

### <a name="use-security-groups-versus-individual-users"></a>Biztonsági csoportok használata az egyes felhasználókkal szemben

A Data Lake Storage Gen2 big data-adatokkal való munka során valószínű, hogy egy egyszerű szolgáltatás használatával engedélyezző szolgáltatások, például az Azure HDInsight az adatokkal való együttműködéshez. Előfordulhatnak azonban olyan esetek, amikor az egyes felhasználóknak is hozzá kell férniük az adatokhoz. Minden esetben fontolja meg az Azure Active Directory [biztonsági csoportok](../common/storage-auth-aad.md) használatát ahelyett, hogy az egyes felhasználókat könyvtárakhoz és fájlokhoz rendelne.

Miután egy biztonsági csoport hozhozzá engedélyeket, felhasználók hozzáadása vagy eltávolítása a csoportból nem igényel frissítéseket a Data Lake Storage Gen2. Ez azt is biztosítja, hogy ne lépje túl a hozzáférés-vezérlési bejegyzések maximális számát hozzáférés-vezérlési listánként (ACL). Jelenleg ez a szám 32, (beleértve a négy POSIX-stílusú ACL-t, amelyek mindig minden fájlhoz és könyvtárhoz kapcsolódnak): a tulajdonában lévő felhasználó, a tulajdonában lévő csoport, a maszk és egyéb. Minden könyvtárnak kétféle Hozzáférés-vezérlési szabálya lehet, a hozzáférési szabályszolgáltatása és az alapértelmezett Hozzáférés-vezérlési szabályszabály, összesen 64 hozzáférés-vezérlési bejegyzéshez. Ezekről az ACL-okról további információt az [Azure Data Lake Storage Gen2 hozzáférés-vezérlése című témakörben](data-lake-storage-access-control.md)talál.

### <a name="security-for-groups"></a>Csoportok biztonsága

Ha Önnek vagy a felhasználóknak hozzáférésre van szükségük egy olyan tárfiókban lévő adatokhoz, amelyen engedélyezve van a hierarchikus névtér, a legjobb, ha az Azure Active Directory biztonsági csoportjait használja. Néhány ajánlott csoport lehet **ReadOnlyUsers**, **WriteAccessUsers**, és **FullAccessUsers** a tároló gyökerét, és még külön is a legfontosabb alkönyvtárak. Ha vannak más várható felhasználói csoportok, amelyek később hozzáadhatók, de még nem lettek azonosítva, érdemes lehet olyan hamis biztonsági csoportokat létrehozni, amelyek hozzáférnek bizonyos mappákhoz. A biztonsági csoport használata biztosítja, hogy elkerülhesse a hosszú feldolgozási időt, amikor új engedélyeket rendel több ezer fájlhoz.

### <a name="security-for-service-principals"></a>Szolgáltatásnév-biztonsági tagok biztonsága

Az Azure Active Directory egyszerű szolgáltatásait általában olyan szolgáltatások használják, mint az Azure Databricks a Data Lake Storage Gen2 adatainak eléréséhez. Sok ügyfél számára egyetlen Azure Active Directory egyszerű szolgáltatás megfelelő lehet, és teljes engedélyekkel rendelkezhet a Data Lake Storage Gen2 tároló gyökerében. Más ügyfelek több fürtre is szükség lehet különböző szolgáltatásnévi tagok, ahol az egyik fürt teljes hozzáféréssel rendelkezik az adatokhoz, és egy másik fürt, amely csak olvasási hozzáféréssel rendelkezik. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>A Data Lake Storage Gen2 tűzfal engedélyezése az Azure szolgáltatáshoz való hozzáféréssel

A Data Lake Storage Gen2 támogatja a tűzfal bekapcsolásának lehetőségét, és csak az Azure-szolgáltatásokhoz való hozzáférést korlátozza, amely ajánlott a külső támadások vektorának korlátozása. Tűzfal engedélyezhető egy tárfiókot az Azure Portalon keresztül a **tűzfal** > **engedélyezése tűzfal (ON)** > **Hozzáférés engedélyezése az Azure-szolgáltatások** beállításait.

A tárfiók azure Databricks eléréséhez telepítse az Azure Databricks a virtuális hálózatra, majd adja hozzá, hogy a virtuális hálózat a tűzfalhoz. Lásd: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása.](https://docs.microsoft.com/azure/storage/common/storage-network-security)

## <a name="resiliency-considerations"></a>Rugalmassággal kapcsolatos szempontok

Amikor egy rendszert a Data Lake Storage Gen2 vagy bármely felhőalapú szolgáltatás, figyelembe kell vennie a rendelkezésre állási követelmények és hogyan reagálhat a szolgáltatás esetleges megszakítások. Egy probléma lehet honosítható, hogy az adott példány, vagy akár régió-szerte, így miután egy tervet mindkét fontos. Attól függően, hogy a helyreállítási idő célkitűzés és a helyreállítási pont cél SLE-k a számítási feladatokhoz, előfordulhat, hogy válasszon egy többé-kevésbé agresszív stratégia magas rendelkezésre állás és a vész-helyreállítási.

### <a name="high-availability-and-disaster-recovery"></a>Magas rendelkezésre állás és vészhelyreállítás

A magas rendelkezésre állás (HA) és a vész-helyreállítási (DR) néha kombinálható, bár mindegyik egy kicsit eltérő stratégiát, különösen, amikor az adatok. A Data Lake Storage Gen2 már 3x replikációt kezel a motorháztető alatt, hogy megvédje a honosított hardverhibákat. Ezenkívül más replikációs lehetőségek, például a ZRS vagy a GZRS (előzetes verzió) javítják a HA-t, míg a GRS-& RA-GRS javítja a DR-t. Ha ha terv létrehozásakor a szolgáltatás megszakítása esetén a munkaterhelésnek a lehető leggyorsabban hozzá kell férnie a legfrissebb adatokhoz, és át kell váltania egy külön replikált példányra helyileg vagy egy új régióban.

A VÉSZ-stratégiában a régió katasztrofális meghibásodásának valószínűtlen eseményére való felkészülés érdekében fontos, hogy az adatok at egy másik régióba replikálják a GRS vagy RA-GRS replikáció használatával. Azt is figyelembe kell vennie a peremhálózati esetek, például az adatsérülés, ahol érdemes lehet létrehozni rendszeres pillanatképek, hogy esik vissza. Az adatok fontosságától és méretétől függően fontolja meg az 1, 6 és 24 órás időszakok gördülési különbözeti pillanatképeit, kockázati tűréshatárok szerint.

A Data Lake Storage Gen2 adatrugalmassága érdekében ajánlott az adatok at GRS vagy RA-GRS-en keresztül imitálni, amely megfelel a HA/DR követelményeinek. Emellett fontolja meg, hogyan az alkalmazás segítségével A Data Lake Storage Gen2 automatikusan feladatáta a másodlagos régióban a figyelési eseményindítók vagy a sikertelen kísérletek hossza, vagy legalább küldjön értesítést a rendszergazdák nak a manuális beavatkozás. Ne feledje, hogy van kompromisszum a felborulás, szemben várja a szolgáltatást, hogy jöjjön vissza online.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Két hely közötti adatmozgatás hoz a Distcp használata

Az elosztott példány rövidítése, a DistCp egy Linux parancssori eszköz, amely a Hadoop-hoz tartozik, és két hely között biztosítja az elosztott adatmozgást. A két hely lehet Data Lake Storage Gen2, HDFS vagy S3. Ez az eszköz mapreduce feladatokat használ egy Hadoop-fürtön (például a HDInsight) az összes csomópont on horizontális felskálázásához. A Distcp a big data mozgatásának leggyorsabb módja speciális hálózati tömörítő készülékek nélkül. A Distcp azt is lehetővé teszi, hogy csak két hely közötti különbözeteket frissítsenek, kezeli az automatikus újrapróbálkozásokat, valamint a számítási dinamikus méretezést. Ez a megközelítés hihetetlenül hatékony, amikor a dolgok replikálása, például a Hive/Spark táblák, amelyek számos nagy fájlokat egy könyvtárban, és csak azt szeretné, hogy másolja át a módosított adatokat. Ezen okok miatt a Distcp a leginkább ajánlott eszköz az adatok nagy adattárolók közötti másolásához.

A másolási feladatokat az Apache Oozie munkafolyamatok aktiválhatják a gyakorisági vagy adateseményindítók, valamint a Linux cron feladatok használatával. Az intenzív replikációs feladatok esetén ajánlott egy külön HDInsight-hadoop-fürtöt felpörgetni, amely kifejezetten a másolási feladatokhoz igazgatható és méretezhető. Ez biztosítja, hogy a másolási feladatok ne zavarják a kritikus feladatokat. Ha a replikációt elég széles frekvencián futtatja, a fürt akár az egyes feladatok között is levehető. Ha a másodlagos régióba való átkerülési idő, győződjön meg arról, hogy egy másik fürt is megpördült a másodlagos régióban az új adatok replikálása az elsődleges Data Lake Storage Gen2 fiók, amint biztonsági másolatot kap. A Distcp használatával például a [Distcp használata adatok másolása az Azure Storage Blobs és a Data Lake Storage Gen2 között.](../blobs/data-lake-storage-use-distcp.md)

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Másolási feladatok ütemezése az Azure Data Factory használatával

Az Azure Data Factory is használható másolási feladatok ütemezésére egy másolási tevékenység használatával, és akár a másolási varázsló segítségével is beállítható gyakorisággal. Ne feledje, hogy az Azure Data Factory rendelkezik a felhőalapú adatok mozgatási egységek (DMU-k) korlátjával, és végül a nagy adatszámítási feladatok átviteli/számítási sebességének korlátozásával rendelkezik. Emellett az Azure Data Factory jelenleg nem kínál különbözeti frissítéseket a Data Lake Storage Gen2 fiókok között, így a könyvtárak, például a Hive-táblák, teljes másolatot igényelne a replikáláshoz. A Data Factory segítségével történő másolásról további információt az [adatgyárban](../../data-factory/load-azure-data-lake-storage-gen2.md) történő másolásról talál.

## <a name="monitoring-considerations"></a>Ellenőrzési szempontok

A Data Lake Storage Gen2 metrikákat biztosít az Azure Portalon a Data Lake Storage Gen2 fiók és az Azure Monitor. A Data Lake Storage Gen2 elérhetősége megjelenik az Azure Portalon. A Data Lake Storage Gen2-fiók legfrissebb rendelkezésre állásának lehetővé téséhez saját szintetikus teszteket kell futtatnia a rendelkezésre állás ellenőrzéséhez. Más metrikák, például a teljes tárolási kihasználtság, olvasási/írási kérelmek és a beérkező/kimenő forgalom érhetők el az alkalmazások figyelése, és is riasztást, ha küszöbértékek (például átlagos késés vagy # hibák percenként) túllépik.

## <a name="directory-layout-considerations"></a>A címtárelrendezésre vonatkozó szempontok

Amikor adatokat ad le egy adattótóba, fontos, hogy előre tervezze meg az adatok szerkezetét, hogy a biztonság, a particionálás és a feldolgozás hatékonyan használható legyen. Az alábbi javaslatok közül sok az összes big data-számítási feladatra vonatkozik. Minden számítási feladat különböző követelményekkel rendelkezik az adatok felhasználásának módjára vonatkozóan, de az alábbiakban néhány gyakori elrendezést kell figyelembe venni az IoT és a kötegforgatókönyvek kezelése során.

### <a name="iot-structure"></a>IoT-struktúra

Az IoT-számítási feladatokban számos termékre, eszközre, szervezetre és ügyfélre kiterjedő adattárban lehet nek iktatása. Fontos, hogy előre tervezze meg a címtárelrendezést a szervezet, a biztonság és az adatok hatékony feldolgozása érdekében a beágyazott felhasználók számára. A figyelembe veendő általános sablon a következő elrendezés lehet:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Például egy repülőgép motorjának leszállási telemetriája az Egyesült Királyságban a következő struktúrának tűnhet:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Fontos oka van annak, hogy a dátumot a könyvtárstruktúra végére helyezze. Ha bizonyos régiókat vagy témákat szeretne lezárni a felhasználók/csoportok számára, akkor ezt a POSIX engedélyekkel könnyedén megteheti. Ellenkező esetben, ha szükség lenne egy bizonyos biztonsági csoport megtekintésére csak az Egyesült Királyság adatait, vagy bizonyos síkok, a dátum struktúra előtt külön engedélyt lenne szükség számos könyvtárak alatt óránként könyvtárat. Továbbá, miután a dátum szerkezete előtt exponenciálisan növeli a könyvtárak száma az idő múlásával.

### <a name="batch-jobs-structure"></a>Kötegelt feladatok szerkezete

Egy magas szintű, a kötegelt feldolgozás általánosan használt módszer az adatok lerakása egy "in" könyvtárban. Ezután az adatok feldolgozása után helyezze az új adatokat egy "out" könyvtárba az alsóbb rétegbeli folyamatok felhasználásához. Ez a könyvtárstruktúra néha olyan feladatok esetében látható, amelyek feldolgozást igényelnek az egyes fájlokon, és nem igényelnek tömegesen párhuzamos feldolgozást nagy adatkészleteken keresztül. A fentiekben ajánlott IoT-struktúrához hasonlóan egy jó könyvtárstruktúra is rendelkezik szülőszintű könyvtárakkal például a régióés a tárgyi kérdések (például szervezet, termék/gyártó) számára. Ez a struktúra segít az adatok védelmében a szervezetben, és jobban kezeli az adatokat a számítási feladatokban. Továbbá vegye figyelembe a dátumot és az időt a struktúrában, hogy jobb szervezést, szűrt kereséseket, biztonságot és automatizálást lehetővé tegye a feldolgozásban. A dátumszerkezet részletességi szintjét az adatok feltöltésének vagy feldolgozásának időköze határozza meg, például óránként, naponta vagy akár havonta.

Előfordulhat, hogy a fájlfeldolgozás adatsérülés vagy váratlan formátumok miatt sikertelen. Ilyen esetekben a könyvtárszerkezet számára előnyös lehet a **/bad** mappa, amely a fájloktovábbi vizsgálatra való áthelyezését eredményezi. A kötegelt feldolgozás is kezelni a jelentési vagy értesítési ezeket a *hibás* fájlokat a kézi beavatkozás. Vegye figyelembe a következő sablonszerkezetet:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Egy marketingcég például napi adatkivonatokat kap az ügyfelek frissítéseiről észak-amerikai ügyfeleitől. A feldolgozás előtt és után a következő kódrészletnek tűnhet:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

Abban a gyakori esetben, ha a kötegadatok feldolgozása közvetlenül adatbázisokba, például Hive vagy hagyományos SQL-adatbázisok, nincs szükség egy **/in** vagy **/out** mappát, mivel a kimenet már megy egy külön mappába a Hive tábla vagy külső adatbázis. Például az ügyfelek napi kivonatai a megfelelő mappákba kerülnek, és az Azure Data Factory, az Apache Oozie vagy az Apache Airflow vezénylése napi Hive- vagy Spark-feladatot indít el az adatok feldolgozásához és egy Hive-táblába való írásához.
