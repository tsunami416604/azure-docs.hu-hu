---
title: Ajánlott eljárások az Azure Data Lake Store használatára vonatkozó |} Microsoft Docs
description: Ismerje meg az ajánlott eljárásokat adatfeldolgozást, dátum biztonsági és az Azure Data Lake Store használatához kapcsolódó teljesítmény
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: sachins
ms.openlocfilehash: 9fd6b72a7d09f85f7a6e60e5af4035ffc3862d2c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625338"
---
# <a name="best-practices-for-using-azure-data-lake-store"></a>Azure Data Lake Store használatának ajánlott eljárásai
Ebből a cikkből megismerheti kapcsolatos ajánlott eljárásokról és az Azure Data Lake Store használata szempontjai. A cikkben információkat biztosít a biztonsági, a teljesítmény, a rugalmasság és a Data Lake Store figyelését. Data Lake Store, mielőtt Azure HDInsight hasonló szolgáltatások valóban nagy adatokkal végzett bonyolult volt. Kellett részekre bonthatók az adatok több Blob storage-fiókok között, hogy petabájtnyi tárolási és optimális teljesítményt, hogy biztosít. A Data Lake Store a szigorú korlátok mérete és a teljesítményt a legtöbb törlődnek. Van azonban továbbra is számításba kell, hogy ez a cikk ismerteti, hogy a legjobb teljesítmény érdekében a Data Lake Store kaphat. 

## <a name="security-considerations"></a>Biztonsági szempontok

Azure Data Lake Store POSIX hozzáférés szabályozza, és részletes naplózást az Azure Active Directory (Azure AD) felhasználók, csoportok és szolgáltatásnevekről kínál. A hozzáférés-vezérlést állíthat be a meglévő fájlokhoz és mappákhoz. A hozzáférés-vezérlést is segítségével hozzon létre az alapértelmezett beállításokat, amelyek új fájlokat vagy mappákat is alkalmazhatók. Engedélyek meglévő mappákat és gyermekobjektum van beállítva, amikor a minden objektumon propagált rekurzív módon kell a jogosultságokat. Ha sok fájlt, az engedélyek propagálása nincs hosszú időt vehet igénybe. Igénybe vett idő között a másodpercenként feldolgozott 30 – 50-objektumok között lehet. A mappa struktúra és felhasználói csoportok, ezért megfelelően tervezése. Ellenkező esetben okozhat váratlan késést és a problémák az adatok használata. 

Tegyük fel, 100 000 gyermekobjektum egy mappa. 30 másodpercenként feldolgozott objektumok alsó határa igénybe vehet, ha az engedély az egész mappa frissítése eltarthat egy óra. További részletek a Data Lake Store hozzáférés-vezérlési listák [hozzáférés-vezérlés az Azure Data Lake Store](data-lake-store-access-control.md). A hozzáférés-vezérlési listák rekurzív módon hozzárendelésével jobb teljesítmény érdekében az Azure Data Lake parancssori eszközt használhatja. Az eszköz létrehozza a szálak és a rekurzív navigációs logika gyorsan ACL-ek alkalmazása több millió fájl. Az eszköz megtalálható a Linux és a Windows, és a [dokumentáció](https://github.com/Azure/data-lake-adlstool) és [letölti](http://aka.ms/adlstool-download) a ezt az eszközt a Githubon találhatók. Ezek azonos teljesítménynövekedést lehet engedélyezni a saját eszközök készült a Data Lake Store [.NET](data-lake-store-data-operations-net-sdk.md) és [Java](data-lake-store-get-started-java-sdk.md) SDK-k.

### <a name="use-security-groups-versus-individual-users"></a>Egyes felhasználók és biztonsági csoportok használata 

Amikor adatokkal dolgozik, és a big Data Lake Store-ban, valószínűleg egy egyszerű szolgáltatásnév segítségével például az adatok Azure HDInsight-szolgáltatások engedélyezése. Előfordulhatnak azonban olyan esetben, amikor az egyes felhasználók kell férhetnek hozzá az adatokhoz, valamint. Ilyen esetekben kell használnia Azure Active Directory [biztonsági csoportok](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) helyett egyéni felhasználók hozzárendelése a fájlokat és mappákat. 

Amennyiben egy biztonsági csoportot a engedélyeket, a felhasználók hozzáadása és eltávolítása a csoportból nem igényel Data Lake Store-frissítéseket. Ez hozzájárul annak biztosításához is nem lépik túl a korlátot [32 hozzáférés és a hozzáférés-vezérlési listákat alapértelmezett](../azure-subscription-service-limits.md#data-lake-store-limits) (Ez magában foglalja a négy POSIX-stílusú hozzáférés-vezérlési listákat, amelyek mindig társított minden fájl és mappa: [a tulajdonos felhasználó](data-lake-store-access-control.md#the-owning-user), [a tulajdonos csoport](data-lake-store-access-control.md#the-owning-group), [a maszk](data-lake-store-access-control.md#the-mask-and-effective-permissions), és az egyéb).

### <a name="security-for-groups"></a>Biztonsági csoportok 

Bemutatott, amikor a felhasználóknak kell a Data Lake Store elérését a legcélszerűbb Azure Active Directory biztonsági csoportok használata. Néhány ajánlott csoportok kezdődnie lehet **ReadOnlyUsers**, **WriteAccessUsers**, és **FullAccessUsers** annak a fióknak, és még akkor is legfelső szintű kulcs kiépítettektől eltérő külön almappák. Ha nincsenek más várható csoportját, előfordulhat, hogy később fel, de nem azonosított felhasználók még, akkor előfordulhat, hogy célszerű dummy biztonsági csoportokat, amelyek bizonyos mappákban hozzáférhetnek. Biztonsági csoport használata biztosítja, hogy később nem szükséges egy hosszú új engedélyek hozzárendelése a ezer feldolgozási ideje. 

### <a name="security-for-service-principals"></a>Biztonsági szolgáltatás rendszerbiztonsági tagoknak 

Az Azure Active Directory szolgáltatás rendszerbiztonsági tagok szolgáltatásokat, mint a Azure HDInsight a Data Lake Store a hozzáférési adatok által általában használt. Attól függően, hogy a hozzáférési követelmények keresztül több munkaterhelés előfordulhat, annak biztosítása érdekében a szervezeten kívül és belül biztonsági szempontokat. Sok felhasználónál elképzelhető, hogy egy egyetlen Azure Active Directory szolgáltatás egyszerű megfelelő, és teljes körű engedélyekkel a Data Lake Store gyökerében veheti fel. Más ügyfelek szükség lehet több fürt különböző szolgáltatásnevekről, ahol egy fürt az adatokat, és csak olvasási hozzáférést egy másik fürthöz teljes hozzáféréssel rendelkezik. Csakúgy, mint a biztonsági csoportok, érdemes, így egy egyszerű szolgáltatást, az egyes várható forgatókönyv (olvasási, írási, teljes), egy Data Lake Store-fiók létrehozása után. 

### <a name="enable-the-data-lake-store-firewall-with-azure-service-access"></a>A Data Lake Store tűzfalon a hozzáférést az Azure-szolgáltatás engedélyezése 

Data Lake Store támogatja a tűzfal bekapcsolását, és korlátozza a hozzáférést csak Azure-szolgáltatásokkal, ami egy kisebb támadási felület, a külső behatolások ajánlott. Tűzfal a Data Lake Store-fiókot az Azure portálon keresztül engedélyezhető a **tűzfal** > **engedélyezése tűzfal (bekapcsolva)** > **Azure-szolgáltatásokhoz való hozzáférés engedélyezése**  beállítások.  

![A Data Lake Store tűzfalbeállítások](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "tűzfalbeállítások Data Lake Store-ban")

Ha tűzfal engedélyezve van, csak az Azure szolgáltatások, például a HDInsight, adat-előállítót, az SQL Data Warehouse, stb. hozzáférhetnek Data Lake Store. Azure által használt belső hálózati címfordítást, mert a Data Lake Store tűzfal nem támogatja az IP által meghatározott szolgáltatások korlátozása, és csak a vonatkozó korlátozásokat, például a helyszínen Azure,-on kívüli végpontok szolgál. 

## <a name="performance-and-scale-considerations"></a>Teljesítmény és méretezhetőség kapcsolatos szempontok

Data Lake store a leghatékonyabb szolgáltatások egyike, hogy eltávolítja-e a szigorú korlátozásokat az adatátvitelt. A korlátozások eltávolítása lehetővé teszi az ügyfelek nő az adatok mérete, és teljesítményre vonatkozó követelmények mellett az anélkül, hogy a shard az adatokat. A legfontosabb szempont a Data Lake Store teljesítmény optimalizálása egyike, hogy a legjobb megadott párhuzamossági segítségével végzi.

### <a name="improve-throughput-with-parallelism"></a>Teljesítmény javítása a párhuzamos végrehajtás 

Gondoljon 8 – 12 szálak száma core a legoptimálisabb olvasási/írási átviteli sebesség eléréséhez. Ennek az az oka blokkoló olvasása/írása egyetlen szálon, és több szál magasabb egyidejűségi engedélyezze a virtuális Gépen. Annak érdekében, szintek kifogástalan, és növelheti a párhuzamosságot, ügyeljen arra, hogy a virtuális gép Processzor kihasználtsága.   

### <a name="avoid-small-file-sizes"></a>Kis méretűek elkerülése érdekében

POSIX engedélyek és a naplózás a Data Lake Store tartalmaz egy terhelést, nyilvánvaló szükségszerűséggé vált az számos kis fájlok használatakor. Ajánlott eljárásként és több ezer vagy több millió kis fájl írása a Data Lake Store-nagyobb fájlok kell kötegelt adatait. Kis méretűek elkerülése rendelkezhet több előnyeit, például:

* A hitelesítési ellenőrzések több teljesítményoptimalizáláshoz csökkentése
* Fájl megnyitása kapcsolatok csökkent
* Gyorsabb másolását vagy replikációs
* Kevesebb fájlt dolgozza fel, ha a Data Lake Store POSIX engedélyek frissítése 

Attól függően, hogy milyen szolgáltatásokat és a munkaterhelések használ az adatok, a helyes figyelembe kell venni a fájlok mérete 256 MB vagy kisebb. Ha a fájl mérete nem lehet kötegelni, ha a Data Lake Store üzenetsorokra, akkor egy külön tömörítés feladatot, amely nagyobb állók közül. ezeket a fájlokat egyesíti. További információk és a fájlméret és az adatok a Data Lake Store javaslat: [az adatkészlet szerkezeti](data-lake-store-performance-tuning-guidance.md#structure-your-data-set).

### <a name="large-file-sizes-and-potential-performance-impact"></a>Nagy fájlok mérete és a teljesítményre gyakorolt lehetséges hatásának

Bár a Data Lake Store támogatja nagy fájlok petabájt méretű, az optimális teljesítmény és attól függően, hogy az adatok olvasása a folyamat nem lehet ideális átlagosan 2 GB feletti ugorhat. Használata esetén például **ból a Distcp** helyek vagy eltérő tárfiókokból közötti másolásához fájljai a részletezettséggel granularitási térkép feladatok határozza meg. Igen 1 TB 10 fájlok másolása, legfeljebb 10 mappers foglal le. Is nagy mennyiségű fájlban a hozzárendelt mappers, először a mappers működnek, nagy fájlok áthelyezése párhuzamosan. A feladat indulásakor lezárását csak néhány mappers maradnak, és lehet elakadt a nagy fájlok rendelt egy egyetlen leképező. Microsoft elküldte a probléma megoldásához Hadoop későbbi verzióiban ból a Distcp fejlesztései.  

Egy másik példa kell figyelembe venni, amikor az Azure Data Lake Analytics használatával a Data Lake Store. Attól függően, hogy a feldolgozás, a készülék végzi, az egyes fájlok nem oszthatók (például, XML, JSON) csökkenhet az teljesítményt biztosít, 2 GB-nál nagyobb a sikerült. Azokban az esetekben, ahol a fájlokat egy kivonatoló (például CSV) által oszthatók nagy fájlok használata javasolt.

### <a name="capacity-plan-for-your-workload"></a>A munkaterhelés kapacitásának tervezése 

Azure Data Lake Store eltávolítja a szigorú IO korlátozása, hogy a Blob storage-fiókok korlátozza. Vannak azonban továbbra is ideiglenes korlátoknak, figyelembe kell venni. Az alapértelmezett be-és kilépési szabályozási korlátokat legtöbb esetben igényeinek. Ha a terhelést növelni korlátokat van szüksége, dolgozni a Microsoft támogatási szolgálatához. Emellett tekintse meg a koncepció igazolása szakaszában korlátok, hogy nem kattintson a IO szabályozási korlátok az éles üzem közben. Ebben az esetben, ha azt a Várakozás a Microsoft mérnöki csapat manuális növekedést lehet szükség. IO-szabályozás akkor fordul elő, ha az Azure Data Lake Store 429 hiba kódot ad vissza, és ideális esetben meg kell ismételni olyan megfelelő exponenciális leállítási házirend. 

### <a name="optimize-writes-with-the-data-lake-store-driver-buffer"></a>A Data Lake Store illesztőprogram pufferrel a "írás" optimalizálása 

A teljesítmény optimalizálása és IOPS Data Lake store a Hadoop írásakor, a Data Lake Store illesztőprogram puffer mérete a lehető legközelebb írási műveletek végrehajtására. Próbálja meg a puffer mérete legfeljebb előtt kiürítési, például amikor Apache Storm használatának streaming vagy Spark streamelési munkaterhelések. Írásakor Data Lake store a HDInsight/Hadoop, fontos tudni, hogy a Data Lake Store van-e a 4 MB-os puffer illesztőprogram. Sok fájlrendszer illesztőprogramja, mint a puffer manuális kiürítése 4 MB-os elérése előtt. Ha nem, akkor azonnal kiürítése Storage Ha a következő írás meghaladja a puffer maximális méretét. Ahol lehetséges, meg kell elkerülése túllépés vagy a puffer jelentős aláfutásgátló számát vagy a idő ablak Házirend szinkronizálása/kiürítése.

## <a name="resiliency-considerations"></a>Rugalmassággal kapcsolatos szempontok 

Amikor a rendszer a Data Lake Store vagy bármely felhőalapú szolgáltatás újratervezni, át kell gondolnia, a rendelkezésre állási követelményeinek, és hogyan reagáljon a lehetséges megszakítás mellett folytathatja a szolgáltatás. Problémát sikerült honosítandó példányának, vagy még régió kiterjedő, ezért a terv mindkét, akkor az fontos. Attól függően a **helyreállítási idő célkitűzése** és a **helyreállításipont-célkitűzés** SLA-k a munkaterhelés számára, akkor célszerű használni a magas rendelkezésre állási és vészhelyreállítási helyreállításhoz több vagy kevesebb agresszív stratégiát.

### <a name="high-availability-and-disaster-recovery"></a>Magas rendelkezésre állás és vészhelyreállítás 

Magas rendelkezésre ÁLLÁS és vészhelyreállítás (DR) is néha kombinálható, bár mindegyiknek némileg eltérő stratégiát, különösen akkor, ha az adatok ismét. Data Lake Store már kezeli 3 x replikáció alatt a technikai részletek, amelyek védelmet biztosítanak a honosított hardver meghibásodása. Azonban a régiók közötti replikáció nem épül fel, mert kell kezelni a saját maga. Egy tervet a magas rendelkezésre ÁLLÁSÚ kiépítéséhez, ha egy szolgáltatáskiesést esetén a munkaterhelés hozzá kell férnie a legfrissebb adatok minél gyorsabban átvált a egy külön replikált példány helyben vagy egy új területet.  

A vész-Helyreállítási stratégiát az a régió végzetes hiba esetére előkészítése fontos is egy másik régióban replikált adatokat. Ezek az adatok kezdetben lehet ugyanaz, mint a replikált adatok magas rendelkezésre ÁLLÁSÚ. Azonban is figyelembe kell vennie a peremhálózati esetben, ahol érdemes lehet rendszeres pillanatképeket térjenek vissza a program sérült adatokat például követelményeinek. Attól függően, hogy a fontos és az adatok mérete érdemes a működés közbeni 1 - 6- és 24 órás időszak különbözeti pillanatfelvételeket a helyi és/vagy másodlagos áruházból kockázat tolerancia megfelelően. 

Az a Data Lake Store adatrugalmasság érdekében javasoljuk, hogy földrajzi-replikálja az adatokat, hogy egy külön területet, amely megfelel az elérhető HA/DR, ideális esetben minden órában gyakorisággal. A replikáció gyakoriságát, minimálisra csökkenti a nagy adatok típusú áthelyezések, amelyeken a fő és jobb helyreállításipont-célkitűzés (RPO) kell versengő átviteli sebességet. Emellett érdemes a Data Lake Store automatikusan át a figyelési eseményindítók másodlagos fiókot használó alkalmazás módon vagy hosszát sikertelen kísérletek, vagy legalább a rendszergazdák kézi beavatkozás értesítést küldeni. Ne feledje, hogy van-e kompromisszumot feladatátadásra és Várakozás a szolgáltatás ismét online állapotú lesz. Az adatok replikálása még nem fejeződött be, ha egy feladatátvétel támadó esetleges adatvesztés, inkonzisztenciát vagy az adatok egyesítése összetett. 

Az alábbiakban a Data Lake Store-fiók, valamint az azok közötti fontosabb különbségeket koordinálása replikációs felső három ajánlott kapcsolók.


|  |Ból a Distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Méretkorlátai**     | Munkavégző csomópontokhoz időpontjaihoz        | Attól függ, hogy a maximális felhő adatmozgás egység        | Elemzési egység fennállnia        |
|**Támogatja az eltérések másolása**     |   Igen      | Nem         | Nem         |
|**Beépített vezénylési**     |  Nem (Oozie légmozgás vagy cron feladatok használata)       | Igen        | Nem (használja az Azure Automation szolgáltatásbeli vagy a Windows Feladatütemező)         |
|**Támogatott fájlrendszerek**     | ADL, HDFS, WASB, S3, GS, CFS        |Számos, lásd: [összekötők](../data-factory/connector-azure-blob-storage.md).         | Az ADL, az ADL (csak az azonos régióban) WASB ADL        |
|**Az operációs rendszer támogatása**     |A Hadoop futó operációs         | –          | Windows 10         |
   

### <a name="use-distcp-for-data-movement-between-two-locations"></a>A két hely közötti adatátvitel ból a Distcp használata 

Rövid a elosztott, ból a Distcp példány Linux parancssori eszköz, amely a Hadoop és biztosít elosztott adatátvitelt jelölik a két hely között. A két hely lehet Data Lake Store, HDFS, WASB vagy S3. Ez az eszköz a Hadoop-fürtök (például a HDInsight) MapReduce-feladatok a csomópontokon horizontális használja. A leggyorsabban helyezze át a big Data típusú adatok nélkül speciális hálózati tömörítési készülékek ból a Distcp számít. Ból a Distcp csak frissítése között két helyen, automatikus újrapróbálkozások kezeli, valamint a számítási dinamikus méretezés eltérések lehetőséget is biztosít. Ez a megközelítés esetén nagyon hatékony történő replikálása többek között a Hive/Spark-táblázatot, amely lehet egy egyetlen könyvtárban sok nagy fájlt, és szeretné másolja át a módosított adatok. Ezen okok miatt ból a Distcp a leginkább ajánlott eszköz közötti big Data típusú adatok tárolja az adatok másolásának. 

Apache Oozie munkafolyamatok gyakorisága vagy adatok eseményindítók, valamint Linux cron feladatok segítségével is elindítható a másolási feladatokat. Az intenzív replikációs feladatok ajánlott lépett fel egy külön HDInsight Hadoop-fürt, amely beállított és méretezhető kimondottan a másolási feladatokat. Ez biztosítja, hogy a másolási feladatokat nem befolyásolják a kritikus feladatok. Ha a replikációs programot az elég széles gyakoriságát, a fürt akkor is le lehessen állítani egyes feladatok közé. Feladatátadás a másodlagos régióba, győződjön meg arról, hogy egy másik fürthöz van is hoz létre replikálni új adatok vissza az elsődleges Data Lake Store-fiók, ismét elérhető biztonsági másodlagos régióban. Ból a Distcp használatával tekintse meg a [adatok Azure Storage Blobs és a Data Lake Store között történő másolásához használja ból a Distcp](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Azure Data Factory használatával másolási feladatok ütemezése 

Az Azure Data Factory is használható másolási feladat ütemezése egy **másolási tevékenység**, még akkor is beállítható a keresztül gyakoriságot és a **másolása varázsló**. Ne feledje, hogy az Azure Data Factory felhő adatok adatátviteli egység (DMUs) legfeljebb, és végül caps nagy data számítási feladatokat az átviteli sebesség/számítási. Emellett Azure Data Factory jelenleg nem ajánlja fel a különbözeti frissítések Data Lake Store fiókokba, így például a Hive táblák mappák igényelnének replikálásához teljes másolata. Tekintse meg a [másolási tevékenység teljesítményhangolási útmutató](../data-factory/v1/data-factory-copy-activity-performance.md) további információt a Data Factory másolásán alapulnak. 

### <a name="adlcopy"></a>AdlCopy

AdlCopy egy Windows parancssori eszköz, amely lehetővé teszi, hogy csak az ugyanabban a régióban belül két Data Lake Store-fiókok között adatokat másolhat. A AdlCopy eszközt biztosít egy önálló vagy egy Azure Data Lake Analytics-fiókot használni a másolási feladat futtatásához a beállításokat. Bár az eredetileg egy robusztus replikációs figyelésekor igény szerinti példány lett létrehozva, ehhez a Data Lake Store-fiók ugyanabban a régióban belül között elosztott másolása egy másik lehetőséget biztosít. A megbízhatóság javasoljuk a prémium szintű Data Lake Analytics beállítás használatához minden üzemi terhelés. Különálló foglalt válaszok lépjen vissza, és csak korlátozott méretezés és a figyelés. 

Ból a Distcp, például a AdlCopy kell például az Azure Automation vagy a Windows Feladatütemező valaminek kell összehangolva. Csakúgy, mint a Data Factory AdlCopy nem támogatja a csak a frissített fájlokat, de újramásolása, és felülírja a fájlokat. További információk és példák AdlCopy [adatok másolása az Azure Storage Blobs Data Lake store](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Figyelési kapcsolatos szempontok 

Data Lake Store biztosít részletes diagnosztikai naplók és a naplózás. Data Lake Store a Data Lake Store-fiókot az Azure portál és az Azure-figyelő néhány alapvető metrikákat biztosít. Data Lake Store rendelkezésre állását az Azure-portálon jelenik meg. Azonban ez a metrika nem frissítik hét percenként, és nem kérdezhető le a nyilvánosan elérhető API-n keresztül. Ahhoz, hogy a Data Lake Store-fiók legfrissebb rendelkezésre állását, futtatnia kell a saját szintetikus tesztet végrehajtva ellenőrizze a rendelkezésre állási. Más metrikákkal például teljes tárhely kihasználtságát, az olvasási/írási kérések és a be-és kilépési frissítéséhez akár 24 óráig is eltarthat. Igen több naprakész metrikák ki kell számítani manuálisan Hadoop parancssori eszközöket vagy összesítése naplóadatok keresztül. A legutóbbi tárhely kihasználtságát a leggyorsabb módja a HDFS parancs fut egy Hadoop-fürt csomópontja (például átjárócsomópont):   

    hdfs dfs -du -s -h adl://<adls_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-store-diagnostics"></a>Exportálás Data Lake Store diagnosztika 

Data Lake Store kereshető naplók férhetnek a leggyorsabb módon ahhoz, hogy naplóküldés **Naplóelemzési** alatt a **diagnosztika** a Data Lake Store-fiók paneljén. Ez bejövő naplók közvetlen hozzáférést biztosít az idő és a tartalom szűrőket, és 15 percenként belül indított beállítások (e-mail/webhook) lehetőséget. Útmutatásért lásd: [diagnosztikai naplók az Azure Data Lake Store elérése](data-lake-store-diagnostic-logs.md). 

További valós idejű riasztásokhoz, és további vezérlési helyéről megnyílik a naplókat fontolja meg, ahol tartalom elemzése önállóan vagy egy olyan időkeretet ahhoz, hogy annak a várólistára valós idejű értesítések küldése az Azure EventHub naplók exportálása. Egy különálló alkalmazás, többek között a [logikai alkalmazás](../connectors/connectors-create-api-azure-event-hubs.md) is majd felhasználását és a riasztásokat a megfelelő csatorna kommunikációt, valamint küldje el a mérni kívánt figyelési eszközök NewRelic, Datadog vagy AppDynamics. Azt is megteheti, ha egy külső eszköz, például ElasticSearch használ, akkor exportálhatja a naplókat a Blob Storage és a [Azure Logstash beépülő modul](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) az adatok felhasználását a Elasticsearch Kibana és Logstash (ELK) verembe.

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>A hibakeresési szintű HDInsight-naplózás bekapcsolása 

Ha Data Lake Store naplóküldésben nincs bekapcsolva, Azure HDInsight is lehetőséget kínál bekapcsolása [ügyféloldali naplózás a Data Lake Store](data-lake-store-performance-tuning-mapreduce.md) log4j keresztül. A következő tulajdonságot meg kell adni **Ambari** > **YARN** > **Config** > **yarn-log4j speciális konfigurációk**: 

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG 

Miután a tulajdonság értéke, és újraindítja a csomópontot, Data Lake Store diagnosztika beíródik YARN naplóit a csomópontokon (/tmp/<user>/yarn.log), és fontos részleteket, például hibák, vagy a sávszélesség-szabályozás (HTTP 429 hibakód) felügyelni. Ezt az információt is figyelhetők a Naplóelemzési vagy mindig a naplók rendszerrel szállított a [diagnosztika](data-lake-store-diagnostic-logs.md) a Data Lake Store-fiók panelen. Javasoljuk, hogy legalább az ügyféloldali naplózás engedélyezve van, vagy a naplóküldés működési látható és egyszerűbb hibakeresés a Data Lake Store beállítást használják.

### <a name="run-synthetic-transactions"></a>Szintetikus tranzakciók futtatása 

Jelenleg a szolgáltatás rendelkezésre állási metrikára, a Data Lake Store az Azure portálon rendelkezik 7 perces frissítési időszakkal. Is hogy nem kérdezhető le egy nyilvánosan elérhetővé API használatával. Ezért javasoljuk, hogy összeállíthat egy alapszintű alkalmazást, amelyet szintetikus tranzakciók Data Lake Store, amely legfeljebb percben rendelkezésre állását képes biztosítani. Például előfordulhat, hogy létrehozása, egy webjobs-feladat, logikai alkalmazás vagy egy Azure-függvény App hajtsa végre az olvasási hozzon létre, és a Data Lake Store elleni frissítése, és az eredményt elküldik a felügyeleti megoldás. A műveletek egy ideiglenes mappába végezheti el, és majd törlése után a tesztet, amely 30 – 60 másodpercenként, követelményeitől függően előfordulhat, hogy futtatható.

## <a name="directory-layout-considerations"></a>Directory elrendezés kapcsolatos szempontok

Adatok érkezési data lake-be, ha fontos, hogy az adatok szerkezete előre tervezze meg, hogy a biztonsági, particionálás és feldolgozási lehet hatékony kezeléséhez. Az alábbi javaslatokat számos is használható, hogy Azure Data Lake Store, a Blob Storage vagy a HDFS van-e. Minden munkaterhelés az adatok már használatban van, de néhány gyakori elrendezések IoT használata során figyelembe kell venni az alábbiakban és a batch-forgatókönyvek különböző követelményekkel rendelkezik.

### <a name="iot-structure"></a>Az IoT-struktúra 

A, IoT-munkaterhelések nagyfokú az adattároló, amely számos termékek, eszközök, a szervezetek és az ügyfelek átívelő éppen ki adatokat lehet megadni. Fontos előre megterveznie, szervezet, biztonsági és lefelé-adatfolyam fogyasztók számára az adatok hatékony feldolgozása directory elrendezését. Figyelembe kell venni egy általános sablon lehet a következő elrendezést: 

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/ 

Például az Egyesült Királyságban belül repülőgép motor telemetriai adat üzenetsorokra nézhet ki például az alábbi szerkezettel: 

    UK/Planes/BA1293/Engine1/2017/08/11/12/ 

Ezért fontos, amelyre az a dátum a gyökérmappa-szerkezetében végén van. Ha azt szeretné, bizonyos régiókban, vagy a felhasználók/csoportok tárgyára zárolását, majd, egyszerűen megteheti a POSIX engedélyekkel. Ellenkező esetben hiba történt az egyes biztonsági csoport korlátozása csak az Egyesült Királyságban adatokat vagy bizonyos síkok megtekintéséhez szükséges, ha a dátum struktúrával elöl külön engedély lenne szükséges számos mappák minden órában mappában. Emellett a dátum struktúra elöl rendelkező volna exponenciálisan növeli a számát mappák idő váltott.

### <a name="batch-jobs-structure"></a>Kötegelt feladatok struktúra 

Egy magas szintű egy általánosan használt megközelítés a köteg feldolgozása a rendszer adatokat megnyílik egy "in" mappában található. Ezt követően az adatok feldolgozása után helyezze el az új adatok egy "out" mappájába, alsóbb rétegbeli folyamatok felhasználását. A könyvtárstruktúra, amely az egyes fájlok feldolgozást, és nem lehet szükség a nagy adatkészletek keresztül nagymértékben párhuzamos feldolgozási feladatok néha látható. Az IoT-struktúra ajánlott, például egy jó könyvtárstruktúrát régió és tárgyára (például a szervezet, a termék/készítő) szülő-szintű mappáit rendelkezik. Ez a struktúra elősegíti az adatok védelme a szervezet és a jobb kezelése az adatok a munkaterhelések között. Továbbá fontolja meg a dátumot és időpontot a szerkezetében lévő, jobb szervezet, szűrt keresések, biztonsági és automatizálás engedélyezése feldolgozásakor. Az időköz, amelyen az adatok feltöltése vagy feldolgozását követően óránként, például napi vagy akár havi dátum struktúra részletesség szintjét határozza meg. 

Egyes esetekben fájl feldolgozása nem sikerül miatt a program sérült adatokat vagy váratlan formátumban. Ilyen esetekben könyvtárstruktúrát előnye származhat egy **/rossz** helyezze át a fájlokat a további ellenőrzési mappát. A kötegelt is előfordulhat, hogy kezelni, a jelentésekben vagy ezek értesítési *rossz* fájlok manuális beavatkozásra. Vegye figyelembe az alábbi sablon szerkezettel: 

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/ 
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/ 
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/ 

Marketing vállalkozás például napi adatok kivonatokat a felhasználói frissítések kap Észak-Amerikában ügyfeleiknek. Az alábbi kódrészletben nézhet, előtti és utáni feldolgozás alatt: 

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv 
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv 
 
A kötegelt adatok, például a Hive adatbázisok vagy a hagyományos SQL-adatbázisok feldolgozott általános esetben nincs szükség egy **/in** vagy **/out** óta már kerülnek a kimeneti mappa egy a Hive tábla vagy a külső adatbázis mappa külön. Például ügyfelek napi kivonata be a megfelelő mappákat és az orchestration volna ártó által Azure Data Factory, Apache Oozie hasonlót vagy Apache légmozgás szeretné feldolgozni, és az adatokat írjon be egy Hive tábla napi Hive vagy Spark feladatot indít.

## <a name="next-steps"></a>További lépések     

* [Az Azure Data Lake Store áttekintése](data-lake-store-overview.md) 
* [Az Azure Data Lake Store hozzáférés-vezérlés](data-lake-store-access-control.md) 
* [Az Azure Data Lake Store biztonsági](data-lake-store-security-overview.md)
* [Azure Data Lake Store teljesítményének hangolása](data-lake-store-performance-tuning-guidance.md)
* [Teljesítményhangolás HDInsight Spark az Azure Data Lake Store használatára vonatkozó útmutatás](data-lake-store-performance-tuning-spark.md)
* [Útmutató a HDInsight Hive használata az Azure Data Lake Store teljesítményhangolása](data-lake-store-performance-tuning-hive.md)
* [Adatok előkészítése az Azure Data Lake Store-hoz készült Azure Data Factory használatával](https://mix.office.com/watch/1oa7le7t2u4ka)
* [A HDInsight-fürtök létrehozása a Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md) 
