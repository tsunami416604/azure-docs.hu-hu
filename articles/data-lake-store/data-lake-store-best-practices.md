---
title: Gyakorlati tanácsok az Azure Data Lake Storage Gen1 használatával kapcsolatban | Microsoft dokumentumok
description: Ismerje meg az azure Data Lake Storage Gen1 (korábbi nevén Azure Data Lake Store) használatával kapcsolatos adatbetöltéssel, dátumbiztonsággal és teljesítménnyel kapcsolatos gyakorlati tanácsokat.
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: a8ca67d1ff3100aee02ed473c9cc2180de3973b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638935"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Az Azure Data Lake Storage Gen1 használatának gyakorlati tanácsok

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Ebben a cikkben az Azure Data Lake Storage Gen1 használatával kapcsolatos gyakorlati tanácsokat és szempontokat ismerteti. Ez a cikk a Data Lake Storage Gen1 biztonságával, teljesítményével, rugalmasságával és figyelésével kapcsolatos információkat tartalmaz. A Data Lake Storage Gen1 előtt a szolgáltatások, például az Azure HDInsight valóban nagy adathalmazaival való együttműködés összetett volt. Több Blob storage-fiókon keresztül kellett adatokat maszatosan leadni, hogy a petabájtos tárolás és az optimális teljesítmény ezen a skálán elérhető legyen. A Data Lake Storage Gen1 segítségével a méret és a teljesítmény szigorú korlátainak nagy része eltávolításra kerül. Azonban még mindig vannak olyan szempontok, amelyek ezt a cikket ismerteti, így a legjobb teljesítményt a Data Lake Storage Gen1.

## <a name="security-considerations"></a>Biztonsági szempontok

Az Azure Data Lake Storage Gen1 POSIX hozzáférés-vezérlést és részletes naplózást kínál az Azure Active Directory (Azure AD) felhasználóinak, csoportjainak és egyszerű szolgáltatásainak. Ezek a hozzáférés-vezérlők meglévő fájlokra és mappákra állíthatók be. A hozzáférés-vezérlés sel új fájlokra vagy mappákra alkalmazható alapértelmezett értékek létrehozására is használható. Ha az engedélyek meglévő mappákra és gyermekobjektumokra vannak beállítva, az engedélyeket minden objektumon rekurzív módon kell propagálni. Ha nagy számú fájl van, az engedélyek propagálása hosszú időt vehet igénybe. A szükséges idő másodpercenként 30-50 feldolgozott objektum között mozoghat. Ezért megfelelően tervezze meg a mappaszerkezetet és a felhasználói csoportokat. Ellenkező esetben nem várt késéseket és problémákat okozhat, amikor az adatokkal dolgozik.

Tegyük fel, hogy van egy mappája 100 000 gyermekobjektummal. Ha a másodpercenként feldolgozott 30 objektum alsó határát veszi igénybe, az egész mappa engedélyének frissítése egy órát is igénybe vehet. A Data Lake Storage Gen1 Hozzáférés-vezérlésről további részleteket az [Azure Data Lake Storage Gen1 hozzáférés-vezérlése talál.](data-lake-store-access-control.md) Az ACL-ek rekurzív hozzárendelésének jobb teljesítményéhez használhatja az Azure Data Lake parancssori eszközt. Az eszköz több szálat és rekurzív navigációs logikát hoz létre, hogy gyorsan alkalmazzon ACL-okat több millió fájlra. Az eszköz elérhető Linux és Windows, és a [dokumentáció](https://github.com/Azure/data-lake-adlstool) és [letöltések](https://aka.ms/adlstool-download) ehhez az eszközhöz megtalálható a GitHub. Ugyanezek a teljesítménybeli fejlesztések a Data Lake Storage Gen1 [.NET](data-lake-store-data-operations-net-sdk.md) és [Java](data-lake-store-get-started-java-sdk.md) SDK-k segítségével írt saját eszközeivel is engedélyezhetők.

### <a name="use-security-groups-versus-individual-users"></a>Biztonsági csoportok használata az egyes felhasználókkal szemben

Amikor a Data Lake Storage Gen1 big data használatával dolgozik, valószínűleg egy egyszerű szolgáltatás használatával teszi lehetővé az olyan szolgáltatások, mint például az Azure HDInsight az adatokkal való együttműködéshez. Előfordulhatnak azonban olyan esetek, amikor az egyes felhasználóknak is hozzá kell férniük az adatokhoz. Ilyen esetekben az Azure Active Directory [biztonsági csoportjait](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) kell használnia ahelyett, hogy egyes felhasználókat mappákhoz és fájlokhoz rendelne.

Miután egy biztonsági csoport hozhozzá engedélyeket, felhasználók hozzáadása vagy eltávolítása a csoportból nem igényel frissítéseket a Data Lake Storage Gen1. Ez azt is biztosítja, hogy ne lépje túl a [32 hozzáférési és alapértelmezett Hozzáférés-hozzáférés korlátot](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits) (ez magában foglalja a négy POSIX-stílusú Hozzáférés-hozzáférést, amelyek mindig minden fájlhoz és mappához kapcsolódnak: [a tulajdonában lévő felhasználó,](data-lake-store-access-control.md#the-owning-user) [a tulajdonában lévő csoport](data-lake-store-access-control.md#the-owning-group), a [maszk](data-lake-store-access-control.md#the-mask)és egyéb).

### <a name="security-for-groups"></a>Csoportok biztonsága

Ahogy azt már tárgyaltuk, ha a felhasználóknak hozzáférésre van szükségük a Data Lake Storage Gen1 szolgáltatáshoz, a legjobb az Azure Active Directory biztonsági csoportjait használni. Néhány ajánlott csoport lehet **ReadOnlyUsers**, **WriteAccessUsers**, és **FullAccessUsers** a fiók gyökerét, és még külön is a kulcs almappákat. Ha vannak más várható felhasználói csoportok, amelyek később hozzáadhatók, de még nem lettek azonosítva, érdemes lehet olyan hamis biztonsági csoportokat létrehozni, amelyek hozzáférnek bizonyos mappákhoz. A biztonsági csoport használata biztosítja, hogy később ne legyen szükség hosszú feldolgozási időre ahhoz, hogy új engedélyeket rendeljen több ezer fájlhoz.

### <a name="security-for-service-principals"></a>Szolgáltatásnév-biztonsági tagok biztonsága

Az Azure Active Directory egyszerű szolgáltatásait általában olyan szolgáltatások használják, mint az Azure HDInsight a Data Lake Storage Gen1 adatainak eléréséhez. Attól függően, hogy a hozzáférési követelmények több számítási feladatok között, előfordulhat, hogy bizonyos szempontok at a szervezeten belüli és kívüli biztonság biztosítása érdekében. Sok ügyfél számára egyetlen Azure Active Directory egyszerű szolgáltatás megfelelő lehet, és teljes engedélyekkel rendelkezhet a Data Lake Storage Gen1 fiók gyökerében. Más ügyfelek több fürtre is szükség lehet különböző szolgáltatásnévi tagok, ahol az egyik fürt teljes hozzáféréssel rendelkezik az adatokhoz, és egy másik fürt, amely csak olvasási hozzáféréssel rendelkezik. A biztonsági csoportokhoz is érdemes lehet egy egyszerű szolgáltatás létrehozása minden egyes várható forgatókönyv (olvasás, írás, teljes) a Data Lake Storage Gen1 fiók létrehozása után.

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>A Data Lake Storage Gen1 tűzfal engedélyezése az Azure szolgáltatáshoz való hozzáféréssel

A Data Lake Storage Gen1 támogatja a tűzfal bekapcsolásának lehetőségét, és csak az Azure-szolgáltatásokhoz való hozzáférést korlátozza, amely a külső behatolásokból származó kisebb támadási vektor számára ajánlott. Tűzfal engedélyezhető a Data Lake Storage Gen1 fiók az Azure Portalon a **tűzfal** > **engedélyezése tűzfal (ON)** > **Hozzáférés engedélyezése az Azure-szolgáltatások beállításaihoz.**

![Tűzfalbeállítások a Data Lake Storage Gen1 szolgáltatásban](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Tűzfalbeállítások a Data Lake Storage Gen1 szolgáltatásban")

Ha a tűzfal engedélyezve van, csak az Azure-szolgáltatások, például a HDInsight, a Data Factory, az SQL Data Warehouse stb. Az Azure által használt belső hálózati címfordítás miatt a Data Lake Storage Gen1 tűzfal nem támogatja az adott szolgáltatások IP-cím szerinti korlátozását, és csak az Azure-on kívüli végpontok korlátozására szolgál, például a helyszíni használatra.

## <a name="performance-and-scale-considerations"></a>Teljesítmény- és méretezési szempontok

A Data Lake Storage Gen1 egyik leghatékonyabb szolgáltatása, hogy eltávolítja az adatátviteli rendszer szigorú korlátait. A korlátok eltávolítása lehetővé teszi az ügyfelek számára, hogy növeljék az adatok méretét és a teljesítménykövetelmények együtt, anélkül, hogy az adatok szilánk. A Data Lake Storage Gen1 teljesítményének optimalizálása az egyik legfontosabb szempont, hogy a legjobban teljesít, ha adott párhuzamosság.

### <a name="improve-throughput-with-parallelism"></a>Az átviteli teljesítmény javítása párhuzamossakkal

Fontolja meg, hogy magonként 8–12 szálat adjon meg a legoptimálisabb olvasási/írási átviteli teljesítmény érdekében. Ez annak köszönhető, hogy egyetlen szálon blokkolja az olvasási/írási műveleteket, és több szál nagyobb egyidejűséget engedélyezhet a virtuális gépen. Annak érdekében, hogy a szintek kifogástalanok és a párhuzamosság növelhető, győződjön meg róla, hogy a virtuális gép CPU-kihasználtsága figyelése.

### <a name="avoid-small-file-sizes"></a>Kerülje a kis fájlméreteket

A POSIX-engedélyek és a Data Lake Storage Gen1 naplózása olyan többletterheléssel jár, amely számos kis fájl lal végzett munka során válik nyilvánvalóvá. Ajánlott eljárásként az adatokat nagyobb fájlokba kell kötegelnie, szemben több ezer vagy több millió kisfájl adatírásával a Data Lake Storage Gen1 szolgáltatásba. A kis fájlméretek elkerülése számos előnnyel járhat, például:

* A hitelesítési ellenőrzések csökkentése több fájlban
* Csökkentett nyitott fájlkapcsolatok
* Gyorsabb másolás/replikáció
* Kevesebb feldolgozandó fájl a Data Lake Storage Gen1 POSIX engedélyek frissítésekor

Attól függően, hogy milyen szolgáltatások és számítási feladatok használják az adatokat, a fájlok esetében a mérete 256 MB vagy annál nagyobb. Ha a fájlméretek nem kötegelhetők a Data Lake Storage Gen1 szolgáltatásban való landoláskor, külön tömörítési feladatkal rendelkezhet, amely ezeket a fájlokat nagyobbakra egyesíti. A fájlméretekkel és az adatok nak a Data Lake Storage Gen1 szolgáltatásban történő rendszerezésével kapcsolatos további információkért és javaslatokért olvassa el [az Adatkészlet strukturálása című témakört.](data-lake-store-performance-tuning-guidance.md#structure-your-data-set)

### <a name="large-file-sizes-and-potential-performance-impact"></a>Nagy fájlméretek és a teljesítményre gyakorolt potenciális hatás

Bár a Data Lake Storage Gen1 támogatja a nagy méretű nagy fájlokat, az optimális teljesítmény érdekében és az adatok olvasásának folyamatától függően, előfordulhat, hogy nem ideális átlagosan 2 GB fölé menni. Ha például a **Distcp** használatával adatokat másol helyek vagy különböző tárfiókok között, a fájlok a legpontosabb részletességi szintatérképes feladatok meghatározásához. Tehát, ha 10 fájlt másol, amelyek egyenként 1 TB-osak, legbőlegelnek legelőklegelnek. Továbbá, ha sok fájlt mappers rendelt, kezdetben a leképező párhuzamosan mozog a nagy fájlokat. Azonban, mivel a feladat kezd a szél le csak néhány leképező továbbra is kiosztott, és akkor lehet megragadt egy leképező rendelt egy nagy fájlt. A Microsoft a Hadoop későbbi verzióiban nyújtotta be a Distcp fejlesztéseket a probléma megoldására.

Egy másik példa, hogy fontolja meg, amikor az Azure Data Lake Analytics a Data Lake Storage Gen1 használata. A kicsomagoló által végzett feldolgozástól függően egyes nem felosztható fájlok (például XML, JSON) teljesítménye 2 GB-nál nagyobb lehet. Azokban az esetekben, ahol a fájlokat egy kibontó (például CSV) feloszthatja, a nagy fájlok at előnyben részesítik.

### <a name="capacity-plan-for-your-workload"></a>Kapacitásterv a munkaterheléshez

Az Azure Data Lake Storage Gen1 eltávolítja a blob storage-fiókokra helyezett szigorú I/O-szabályozási korlátokat. Vannak azonban még puha korlátok, amelyeket figyelembe kell venni. Az alapértelmezett be-/kimenő forgalom szabályozási korlátok a legtöbb forgatókönyv igényeinek megfelelően. Ha a számítási feladatoknak növelniük kell a korlátokat, működjön együtt a Microsoft támogatási szolgálatával. Tekintse meg a korlátokat a proof-of-concept szakaszban, hogy az IO-szabályozás korlátait ne sújtsa a termelés során. Ebben az esetben előfordulhat, hogy meg kell várni a Microsoft mérnöki csapatának manuális növelését. Ha az I/O-szabályozás történik, az Azure Data Lake Storage Gen1 429-es hibakódot ad vissza, és ideális esetben újra kell próbálni a megfelelő exponenciális visszamaradási szabályzattal.

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>Az "írások" optimalizálása a Data Lake Storage Gen1 illesztőprogram-pufferrel

A teljesítmény optimalizálása és az IOPS csökkentése érdekében a Hadoop Data Lake Storage Gen1 szolgáltatásába íráskor a Data Lake Storage Gen1 illesztőprogram-puffermérethez a lehető legközelebb lévő írási műveleteket hajtsa végre. Lehetőleg ne lépje túl a puffer méretét kiürítés előtt, például az Apache Storm vagy a Spark streamelési számítási feladatokkal történő streameléskor. Amikor a Data Lake Storage Gen1-re ír a HDInsight/Hadoop-ból, fontos tudni, hogy a Data Lake Storage Gen1 4 MB-os pufferrel rendelkező illesztőprogrammal rendelkezik. Mint sok más fájlrendszer-illesztőprogram, ez a puffer is manuálisan kiüríthető a 4 MB-os méret elérése előtt. Ha nem, akkor azonnal kiüríti a tárolóba, ha a következő írás meghaladja a puffer maximális méretét. Ahol lehetséges, el kell kerülnie a puffer túlterhelését vagy jelentős alulterhelését a házirend szám vagy időablak szerinti szinkronizálása/kiürítése során.

## <a name="resiliency-considerations"></a>Rugalmassággal kapcsolatos szempontok

Amikor egy rendszert a Data Lake Storage Gen1 vagy bármely felhőalapú szolgáltatás, figyelembe kell vennie a rendelkezésre állási követelmények és hogyan kell reagálni a szolgáltatás esetleges megszakítások. Egy probléma lehet honosítható, hogy az adott példány, vagy akár régió-szerte, így miután egy tervet mindkét fontos. Attól függően, hogy a **helyreállítási idő célkitűzés** és a helyreállítási pont **cél** SLE-k a számítási feladatokhoz, előfordulhat, hogy válasszon egy többé-kevésbé agresszív stratégia magas rendelkezésre állás és a vész-helyreállítási.

### <a name="high-availability-and-disaster-recovery"></a>Magas rendelkezésre állás és vészhelyreállítás

A magas rendelkezésre állás (HA) és a vész-helyreállítási (DR) néha kombinálható, bár mindegyik egy kicsit eltérő stratégiát, különösen, amikor az adatok. A Data Lake Storage Gen1 már 3x replikációt kezel a motorháztető alatt, hogy megvédje a honosított hardverhibákat. Mivel azonban a régiók közötti replikáció nincs beépítve, ezt saját kezűleg kell kezelnie. Ha ha terv létrehozásakor a szolgáltatás megszakítása esetén a munkaterhelésnek a lehető leggyorsabban hozzá kell férnie a legfrissebb adatokhoz, és át kell váltania egy külön replikált példányra helyileg vagy egy új régióban.

A VÉSZ-stratégiában a régió katasztrofális meghibásodásának valószínűtlen eseményére való felkészülés érdekében az is fontos, hogy az adatok egy másik régióba replikálódjanak. Ezek az adatok kezdetben megegyezhetnek a replikált HA-adatokkal. Azonban azt is figyelembe kell vennie a peremhálózati esetek, például az adatsérülés, ahol érdemes lehet létrehozni rendszeres pillanatképek, hogy esik vissza. Az adatok fontosságától és méretétől függően fontolja meg az 1, 6 és 24 órás időszakok gördülési különbözeti pillanatképeit a helyi és/vagy másodlagos tárolóban, a kockázati tűréshatároknak megfelelően.

Az adatok rugalmasságát a Data Lake Storage Gen1, ajánlott az adatok georeplikálása egy külön régióba a gyakorisággal, amely megfelel a HA/DR követelményeknek, ideális esetben óránként. Ez a replikációs gyakoriság minimálisra csökkenti a tömeges adatmozgásokat, amelyek versengő átviteli igényeket okozhatnak a fő rendszerrel és egy jobb helyreállítási pont célkitűzéssel (RPO). Emellett fontolja meg, hogyan az alkalmazás segítségével A Data Lake Storage Gen1 automatikusan feladatáta a másodlagos fiók figyelési eseményindítók vagy a sikertelen kísérletek hossza, vagy legalább küldjön értesítést a rendszergazdák nak a manuális beavatkozás. Ne feledje, hogy van kompromisszum a felborulás, szemben várja a szolgáltatást, hogy jöjjön vissza online. Ha az adatok replikálása még nem fejeződött be, a feladatátvétel adatvesztést, inkonzisztenciát vagy az adatok összetett egyesítését okozhatja.

Az alábbiakban a Data Lake Storage Gen1 fiókok közötti replikáció vezényléséhez ajánlott három legfontosabb lehetőség, valamint az egyes fiókok közötti főbb különbségek találhatók.

|  |Distcp között  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Méretezési határértékek**     | Munkavégző csomópontok határolja        | Max Cloud Data Movement egységek korlátozzák        | Analytics-egységek kötik        |
|**Támogatja a delták másolását**     |   Igen      | Nem         | Nem         |
|**Beépített vezénylés**     |  Nem (Oozie Airflow vagy cron feladatok használata)       | Igen        | Nem (Azure Automation vagy Windows Feladatütemező használata)         |
|**Támogatott fájlrendszerek**     | ADL, HDFS, WASB, S3, GS, CFS        |Számos, lásd: [Csatlakozók](../data-factory/connector-azure-blob-storage.md).         | ADL-től ADL-ig, WASB-tól ADL-ig (csak ugyanabban a régióban)        |
|**Operációs rendszer támogatása**     |Bármely Hadoop-ot futtató operációs rendszer         | N/A          | Windows 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Két hely közötti adatmozgatás hoz a Distcp használata

Az elosztott példány rövidítése, a Distcp egy Linux parancssori eszköz, amely a Hadoop-hoz tartozik, és két hely között biztosítja az elosztott adatmozgást. A két hely lehet Data Lake Storage Gen1, HDFS, WASB vagy S3. Ez az eszköz mapreduce feladatokat használ egy Hadoop-fürtön (például a HDInsight) az összes csomópont on horizontális felskálázásához. A Distcp a big data mozgatásának leggyorsabb módja speciális hálózati tömörítő készülékek nélkül. A Distcp azt is lehetővé teszi, hogy csak két hely közötti különbözeteket frissítsenek, kezeli az automatikus újrapróbálkozásokat, valamint a számítási dinamikus méretezést. Ez a megközelítés hihetetlenül hatékony, amikor a dolgok replikálása, például a Hive/Spark táblák, amelyek számos nagy fájlokat egy könyvtárban, és csak azt szeretné, hogy másolja át a módosított adatokat. Ezen okok miatt a Distcp a leginkább ajánlott eszköz az adatok nagy adattárolók közötti másolásához.

A másolási feladatokat az Apache Oozie munkafolyamatok aktiválhatják a gyakorisági vagy adateseményindítók, valamint a Linux cron feladatok használatával. Az intenzív replikációs feladatok esetén ajánlott egy külön HDInsight-hadoop-fürtöt felpörgetni, amely kifejezetten a másolási feladatokhoz igazgatható és méretezhető. Ez biztosítja, hogy a másolási feladatok ne zavarják a kritikus feladatokat. Ha a replikációt elég széles frekvencián futtatja, a fürt akár az egyes feladatok között is levehető. Ha a másodlagos régióba való átkerülési idő, győződjön meg arról, hogy egy másik fürt is megvan fazonálva a másodlagos régióban az új adatok replikálása az elsődleges Data Lake Storage Gen1 fiók, amint biztonsági másolatot kap. A Distcp használatával például a [Distcp használata adatok másolása az Azure Storage Blobs és a Data Lake Storage Gen1 között.](data-lake-store-copy-data-wasb-distcp.md)

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Másolási feladatok ütemezése az Azure Data Factory használatával

Az Azure Data Factory másolási feladatok ütemezésére is használható **másolási tevékenység**használatával, és akár egy gyakorisággal is beállítható a **Másolás varázsló segítségével.** Ne feledje, hogy az Azure Data Factory rendelkezik a felhőalapú adatok mozgatási egységek (DMU-k) korlátjával, és végül a nagy adatszámítási feladatok átviteli/számítási sebességének korlátozásával rendelkezik. Emellett az Azure Data Factory jelenleg nem kínál különbözeti frissítéseket a Data Lake Storage Gen1 fiókok között, így a mappák, például a Hive-táblák replikálásához teljes másolatszükséges. A Data Factory segítségével történő másolásról további információt a [Tevékenység másolása hangolási útmutatóban](../data-factory/copy-activity-performance.md) talál.

### <a name="adlcopy"></a>AdlCopy

Az AdlCopy egy Windows parancssori eszköz, amely lehetővé teszi, hogy csak ugyanabban a régióban másolja az adatokat két Data Lake Storage Gen1 fiók között. Az AdlCopy eszköz egy önálló lehetőséget biztosít, vagy egy Azure Data Lake Analytics-fiók használatát a másolási feladat futtatásához. Bár eredetileg igény szerinti példányok, szemben a robusztus replikáció, ez egy másik lehetőség, hogy nem elosztott másolás a Data Lake Storage Gen1 fiókok ugyanazon a régión belül. A megbízhatóság érdekében ajánlott a prémium Data Lake Analytics opció használata bármilyen éles számítási feladatokhoz. Az önálló verzió foglalt válaszokat adhat vissza, és korlátozott skálával és figyeléssel rendelkezik.

A Distcp-hez hasonlóan az AdlCopy-t is olyan nak kell hangszerelte, mint az Azure Automation vagy a Windows Feladatütemező. A Data Factory-hoz, az AdlCopy nem támogatja a csak frissített fájlok másolását, hanem a meglévő fájlok újramásolását és felülírását. Az AdlCopy használatával kapcsolatos további információkért és példákért az [Azure Storage Blobs adatainak másolása a Data Lake Storage Gen1 szolgáltatásba című](data-lake-store-copy-data-azure-storage-blob.md)témakörben talál.

## <a name="monitoring-considerations"></a>Ellenőrzési szempontok

A Data Lake Storage Gen1 részletes diagnosztikai naplókat és naplózást biztosít. A Data Lake Storage Gen1 néhány alapvető metrikát biztosít az Azure Portalon a Data Lake Storage Gen1 fiók és az Azure Monitor. A Data Lake Storage Gen1 elérhetősége megjelenik az Azure Portalon. Azonban ez a metrika hétpercenként frissül, és nem lehet lekérdezni egy nyilvánosan elérhető API-n keresztül. A Data Lake Storage Gen1-fiók legfrissebb rendelkezésre állásának lehetővé téséhez saját szintetikus teszteket kell futtatnia a rendelkezésre állás ellenőrzéséhez. Más metrikák, például a teljes tárolási kihasználtság, olvasási/írási kérelmek és a be- és kilépési kérelmek frissítése akár 24 órát is igénybe vehet. Így a további naprakész mutatókat manuálisan kell kiszámítani a Hadoop parancssori eszközeivel vagy a naplóadatok összesítésével. A legutóbbi tárolási kihasználtság beszerzésének leggyorsabb módja a HDFS-parancs futtatása egy Hadoop fürtcsomópontról (például a főcsomópont):

    hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Adattótároló gen1 diagnosztikájának exportálása

A Data Lake Storage Gen1 kereshető naplóihoz való hozzáférés egyik leggyorsabb módja, hogy lehetővé tegye a log-szállítást a **Log Analytics** szolgáltatásba a Data Lake Storage Gen1 fiók **diagnosztika** panelje alatt. Ez azonnali hozzáférést biztosít a bejövő naplókhoz idő- és tartalomszűrőkkel, valamint 15 perces időközönként aktivált riasztási beállításokkal (email/webhook). További információt az [Azure Data Lake Storage Gen1 diagnosztikai naplóinak elérése című témakörben talál.](data-lake-store-diagnostic-logs.md)

A valós idejű riasztásért és a naplók leállításának további szabályozásáért fontolja meg a naplók exportálását az Azure EventHubra, ahol a tartalom egyenként vagy egy időablakban elemezhető, hogy valós idejű értesítéseket küldjön egy várólistába. Egy külön alkalmazás, például egy [logikai alkalmazás](../connectors/connectors-create-api-azure-event-hubs.md) ezután felhasználhatja és kommunikálhatja a riasztásokat a megfelelő csatornára, valamint metrikákat küldhet a figyelési eszközök, például a NewRelic, a Datadog vagy az AppDynamics. Másik lehetőségként, ha egy harmadik féltől származó eszközt, például ElasticSearch, exportálhatja a naplókat blob storage és az [Azure Logstash plugin](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) az adatok felhasználására az Elasticsearch, Kibana és Logstash (ELK) verem.

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Hibakeresési szintű naplózás bekapcsolása a HDInsightban

Ha a Data Lake Storage Gen1 naplószállításnincs bekapcsolva, az Azure HDInsight is lehetővé teszi az ügyféloldali naplózás bekapcsolását [a Data Lake Storage Gen1 számára](data-lake-store-performance-tuning-mapreduce.md) log4j-en keresztül. Az **Ambari** > **YARN** > **Config** > Advanced**yarn-log4j konfigurációkban**a következő tulajdonságot kell beállítania:

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG

Miután a tulajdonság be van állítva, és a csomópontok újraindulnak, a Data Lake Storage Gen1 diagnosztika a csomópontok (/tmp/\<user\>/yarn.log) YARN naplóiba kerül, és fontos részleteket, például hibákat vagy szabályozást (HTTP 429 hibakód) figyelhet. Ugyanezek az adatok az Azure Monitor-naplókban vagy bárhol is figyelhetők a Data Lake Storage Gen1 fiók [diagnosztika](data-lake-store-diagnostic-logs.md) paneljén. Javasoljuk, hogy legalább az ügyféloldali naplózás be van kapcsolva, vagy használja a naplószállítási lehetőséget a Data Lake Storage Gen1 szolgáltatással a működési láthatóság és a könnyebb hibakeresés érdekében.

### <a name="run-synthetic-transactions"></a>Szintetikus tranzakciók futtatása

Jelenleg a szolgáltatás rendelkezésre állási metrikája data lake storage gen1 az Azure Portalon rendelkezik 7 perces frissítési ablak. Emellett nem lehet lekérdezni egy nyilvánosan elérhető API használatával. Ezért ajánlott létrehozni egy alapszintű alkalmazást, amely szintetikus tranzakciókat a Data Lake Storage Gen1, amely biztosítja akár a rendelkezésre állás i. Egy példa lehet egy WebJob, Logic App vagy az Azure Function App egy olvasási, létrehozási és frissítési adatok a Data Lake Storage Gen1, és elküldi az eredményeket a figyelési megoldás. A műveleteket el lehet végezni egy ideiglenes mappában, majd törölni a teszt után, amely a követelményektől függően 30-60 másodpercenként futtatható.

## <a name="directory-layout-considerations"></a>A címtárelrendezésre vonatkozó szempontok

Amikor adatokat ad le egy adattótóba, fontos, hogy előre tervezze meg az adatok szerkezetét, hogy a biztonság, a particionálás és a feldolgozás hatékonyan használható legyen. Az alábbi javaslatok közül sok használható, függetlenül attól, hogy az Azure Data Lake Storage Gen1, blob storage vagy HDFS. Minden számítási feladat különböző követelményekkel rendelkezik az adatok felhasználásának módjára vonatkozóan, de az alábbiakban néhány gyakori elrendezést kell figyelembe venni az IoT és a kötegforgatókönyvek kezelése során.

### <a name="iot-structure"></a>IoT-struktúra

Az IoT-számítási feladatokban számos termékre, eszközre, szervezetre és ügyfélre kiterjedő adattárban lehet nek iktatása. Fontos, hogy előre tervezze meg a címtárelrendezést a szervezet, a biztonság és az adatok hatékony feldolgozása érdekében a beágyazott felhasználók számára. A figyelembe veendő általános sablon a következő elrendezés lehet:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Például egy repülőgép motorjának leszállási telemetriája az Egyesült Királyságban a következő struktúrának tűnhet:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Fontos oka van annak, hogy a dátumot a mappastruktúra végére helyezze. Ha bizonyos régiókat vagy témákat szeretne lezárni a felhasználók/csoportok számára, akkor ezt a POSIX engedélyekkel könnyedén megteheti. Ellenkező esetben, ha szükség van arra, hogy egy bizonyos biztonsági csoportot csak az Egyesült Királyság adatainak vagy bizonyos síkjainak megtekintésére korlátozzák, a dátumstruktúra előtt külön engedély re van szükség számos mappához minden óra mappában. Továbbá, miután a dátum struktúra előtt lenne exponenciálisan növeli a mappák száma az idő múlásával.

### <a name="batch-jobs-structure"></a>Kötegelt feladatok szerkezete

A kötegelt feldolgozás általánosan használt módszere a kötegelt feldolgozás általánosan használt módszere az adatok "in" mappában való lerakása. Ezután az adatok feldolgozása után helyezze az új adatokat egy "out" mappába az alsóbb rétegbeli folyamatok felhasználásához. Ez a könyvtárstruktúra néha olyan feladatok esetében látható, amelyek feldolgozást igényelnek az egyes fájlokon, és nem igényelnek tömegesen párhuzamos feldolgozást nagy adatkészleteken keresztül. A fentiekben ajánlott IoT-struktúrához hasonlóan egy jó könyvtárstruktúra is rendelkezik szülőszintű mappákkal például a régióés a tárgy (például szervezet, termék/gyártó) számára. Ez a struktúra segít az adatok védelmében a szervezetben, és jobban kezeli az adatokat a számítási feladatokban. Továbbá vegye figyelembe a dátumot és az időt a struktúrában, hogy jobb szervezést, szűrt kereséseket, biztonságot és automatizálást lehetővé tegye a feldolgozásban. A dátumszerkezet részletességi szintjét az adatok feltöltésének vagy feldolgozásának időköze határozza meg, például óránként, naponta vagy akár havonta.

Előfordulhat, hogy a fájlfeldolgozás adatsérülés vagy váratlan formátumok miatt sikertelen. Ilyen esetekben a könyvtárszerkezet számára előnyös lehet a **/bad** mappa, amely a fájloktovábbi vizsgálatra való áthelyezését eredményezi. A kötegelt feldolgozás is kezelni a jelentési vagy értesítési ezeket a *hibás* fájlokat a kézi beavatkozás. Vegye figyelembe a következő sablonszerkezetet:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Egy marketingcég például napi adatkivonatokat kap az ügyfelek frissítéseiről észak-amerikai ügyfeleitől. A feldolgozás előtt és után a következő kódrészletnek tűnhet:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

Abban a gyakori esetben, ha a kötegadatok feldolgozása közvetlenül adatbázisokba, például Hive vagy hagyományos SQL-adatbázisok, nincs szükség egy **/in** vagy **/out** mappát, mivel a kimenet már megy egy külön mappába a Hive tábla vagy külső adatbázis. Például az ügyfelek napi kivonatai a megfelelő mappákba kerülnek, és az Azure Data Factory, az Apache Oozie vagy az Apache Airflow vezénylése napi Hive- vagy Spark-feladatot indít el az adatok feldolgozásához és egy Hive-táblába való írásához.

## <a name="next-steps"></a>További lépések

* [Az Azure Data Lake storage gen1 áttekintése](data-lake-store-overview.md)
* [Hozzáférés-vezérlés az Azure Data Lake Storage gen1 szolgáltatásában](data-lake-store-access-control.md)
* [Biztonság az Azure Data Lake Storage gen1-ben](data-lake-store-security-overview.md)
* [Az Azure Data Lake Storage Gen1 hangolása a teljesítmény érdekében](data-lake-store-performance-tuning-guidance.md)
* [Teljesítményhangolási útmutató a HDInsight Spark és az Azure Data Lake Storage Gen1 használatával kapcsolatban](data-lake-store-performance-tuning-spark.md)
* [Teljesítményhangolási útmutató a HDInsight-struktúra azure Data Lake Storage Gen1 használatával](data-lake-store-performance-tuning-hive.md)
* [HDInsight-fürtök létrehozása a Data Lake Storage Gen1 segítségével](data-lake-store-hdinsight-hadoop-use-portal.md)
