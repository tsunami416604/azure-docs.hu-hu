---
title: Ajánlott eljárások az Azure Data Lake Storage Gen1 |} A Microsoft Docs
description: Adatbetöltés, dátum biztonsági és az Azure Data Lake Storage Gen1 (korábbi nevén Azure Data Lake Store) használatával kapcsolatos teljesítményadatok kapcsolatos ajánlott eljárások megismerése
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: 50d0ed644b5afa744e8bce478199079fd4fb7432
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684157"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Az Azure Data Lake Storage Gen1 használatának ajánlott eljárásai

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Ebben a cikkben megismerkedhet ajánlott eljárásokat és az Azure Data Lake Storage Gen1 használatának szempontjai. Ez a cikk információkat biztosít a biztonság, a teljesítmény, a rugalmasság és a Data Lake Storage Gen1 figyelést biztosít. Data Lake Storage Gen1 előtti szolgáltatások, például az Azure HDInsight valóban big Data típusú adatokkal végzett bonyolult volt. Kellett az adatok horizontális skálázását több Blob storage-fiókok között, hogy petabájtos tárolás és az optimális teljesítmény érdekében, hogy nagy mennyiségű érhető el. A Data Lake Storage Gen1 mérete és a teljesítményt a szigorú korlátok többsége el lesznek távolítva. Vannak azonban még néhány szempontot, hogy ez a cikk ismerteti, hogy megjelenik a Data Lake Storage Gen1 a legjobb teljesítményt.

## <a name="security-considerations"></a>Biztonsági szempontok

Az Azure Data Lake Storage Gen1 ajánlatok POSIX hozzáférés-vezérlés, valamint a részletes naplózás az Azure Active Directory (Azure AD) felhasználók, csoportok és az egyszerű szolgáltatások. Ezek a hozzáférés-vezérlés állítható a már létező fájlokhoz és mappákhoz. A hozzáférés-vezérlés is használható hozhat létre az alapértelmezett érték, amely új fájlokat vagy mappákat is alkalmazható. Amikor meglévő mappákat és az gyermekobjektum értékre vannak beállítva, az engedélyeket kell terjesztését rekurzív módon minden egyes objektum. Nagy számú fájl esetén az engedélyek propagálása egy hosszú időbe telhet. Igénybe vett idő terjedhet a másodpercenként feldolgozott 30 – 50 objektumok között. Ezért tervezni a mappa szerkezete és felhasználói csoportok. Ellenkező esetben okozhat a nem várt késedelmeket és a problémák az adatok való munka során.

Tegyük fel, van egy mappája az gyermekobjektum 100 000. Ha az alsó határ értékének 30 másodpercenként feldolgozott-objektumok, az egész mappa engedélyeinek frissítése eltarthat egy óra. További részleteket a Data Lake Storage Gen1 ACL-ek esetén érhető el [hozzáférés-vezérlés az Azure Data Lake Storage Gen1](data-lake-store-access-control.md). Az ACL-ek rekurzív módon hozzárendelése jobb teljesítmény érdekében használhatja az Azure Data Lake parancssori eszközzel. Az eszköz létrehozza a szálak és a rekurzív navigációs logika gyorsan ACL-ek alkalmazása több millió fájl. Az eszköz érhető el a Linux és Windows, és a [dokumentáció](https://github.com/Azure/data-lake-adlstool) és [letölti](https://aka.ms/adlstool-download) ezt az eszközt a Githubon találja. Ezek azonos teljesítménnyel kapcsolatos fejlesztések a saját eszközök, a Data Lake Storage Gen1-készült engedélyezhető [.NET](data-lake-store-data-operations-net-sdk.md) és [Java](data-lake-store-get-started-java-sdk.md) SDK-k.

### <a name="use-security-groups-versus-individual-users"></a>Egyéni felhasználók és biztonsági csoportok használata

A Data Lake Storage Gen1 big Data típusú adatok használata, amikor nagy valószínűséggel egy egyszerű szolgáltatás segítségével szolgáltatások, például az Azure HDInsight az adatokkal való munka engedélyezése. Azonban lehetnek olyan esetekben, ahol egyéni felhasználók számára, valamint az adatok hozzáférésre van szükségük. Ezekben az esetekben kell használnia az Azure Active Directory [biztonsági csoportok](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) helyett egyéni felhasználók számára a fájlok és mappák.

Engedélyek hozzárendelése egy biztonsági csoportot, után a felhasználók hozzáadása és eltávolítása a csoportból nem igényel Data Lake Storage Gen1 frissítéseit. Ez hozzájárul annak biztosításához is nem haladhatja meg a korlátot [32 hozzáférési és alapértelmezett ACL-ek](../azure-subscription-service-limits.md#data-lake-store-limits) (Ez magában foglalja a négy POSIX-stílusú ACL-eket mindig társított minden fájl és mappa: [a tulajdonos felhasználó](data-lake-store-access-control.md#the-owning-user), [a tulajdonoscsoport](data-lake-store-access-control.md#the-owning-group), [a maszk](data-lake-store-access-control.md#the-mask), és más).

### <a name="security-for-groups"></a>Biztonsági csoportok

Írtaknak, amikor a felhasználóknak kell elérését a Data Lake Storage Gen1, célszerű használni az Azure Active Directory biztonsági csoportokat. A következőben néhány ajánlott csoportok a kezdéshez lehet **ReadOnlyUsers**, **WriteAccessUsers**, és **FullAccessUsers** a fiókot, és még akkor is legfelső szintű kulcs kiépítettektől külön almappák. Ha bármely más várható csoportjait, amelyek később hozzáadhatók, de nem azonosított felhasználók, mégis, akkor érdemes megfontolni, helyőrző biztonsági csoportok, amelyek hozzáféréssel rendelkeznek bizonyos mappák létrehozása. Biztonsági csoport használatával biztosítható, hogy később nem kell sokáig feldolgozási ideje az új engedélyek hozzárendelése a fájlok ezer.

### <a name="security-for-service-principals"></a>Az egyszerű szolgáltatások biztonsága

Az Azure Active Directory-szolgáltatásnevek általában használják a szolgáltatások, például Azure HDInsight a Data Lake Storage Gen1 hozzáférés az adatokhoz. Függően a hozzáférési követelmények több számítási feladatokhoz van néhány szempontot belüli és kívüli a szervezet biztonságának megteremtéséhez. Sok felhasználónál egy egyetlen Azure Active Directory egyszerű szolgáltatás sebesség már elegendő lehet, és azt is teljes körű jogosultságokkal rendelkeznek a Data Lake Storage Gen1 fiók gyökérmappájában. Más ügyfelek különböző szolgáltatásnevekkel, ahol egy fürt az adatokat, és a egy másik fürtöt csak olvasási hozzáféréssel rendelkező teljes hozzáféréssel rendelkezik több fürt szükség lehet. Csakúgy, mint a biztonsági csoportokat, akkor érdemes, így egy egyszerű szolgáltatást, az egyes várható forgatókönyvet (olvasási, írási, teljes), egy Data Lake Storage Gen1 fiók létrehozása után.

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>Az Azure-szolgáltatásokhoz a Data Lake Storage Gen1 tűzfal engedélyezése

Data Lake Storage Gen1 ne tudják bekapcsolni a tűzfalat, és korlátozza a hozzáférést csak az Azure-szolgáltatás, amely ajánlott a külső behatolások elleni egy kisebb támadási felület támogatja. Tűzfal a Data Lake Storage Gen1 fiók az Azure Portalon keresztül is engedélyezhető a **tűzfal** > **tűzfal engedélyezése (bekapcsolva)** > **engedélyezi a hozzáférést az Azure-bA szolgáltatások** beállítások.

![A Data Lake Storage Gen1 tűzfalbeállítások](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "tűzfalbeállítások a Data Lake Storage Gen1")

Ha tűzfal engedélyezve van, csak az Azure szolgáltatások, például a HDInsight, Data Factory, az SQL Data Warehouse és az egyéb rendelkezik hozzáféréssel a Data Lake Storage Gen1. Azure által használt belső hálózati címfordítást, mert a Data Lake Storage Gen1 tűzfal nem támogatja az IP-cím alapján adott szolgáltatások korlátozása, és csak a szolgál a vonatkozó korlátozásokat, például a helyszínen Azure,-on kívüli végpontok.

## <a name="performance-and-scale-considerations"></a>Teljesítmény- és méretezési szempontok

A leghatékonyabb szolgáltatása a Data Lake Storage Gen1 egyik célja, hogy eltávolítja a szigorú korlátok a fájlmegosztásra. A korlátozások eltávolítása lehetővé teszi ügyfeleink számára az adatok mérete nő, és teljesítményre vonatkozó követelmények tartozik anélkül, hogy a szegmensben az adatokat. A legfontosabb szempont a Data Lake Storage Gen1 teljesítményének optimalizálása egyik célja, hogy a lehető legjobb párhuzamossági megadott végrehajt.

### <a name="improve-throughput-with-parallelism"></a>A párhuzamosság átviteli sebesség növelése

Vegye figyelembe, hogy az olvasási/írási optimális átviteli magonként 8 – 12 szálat biztosít. Blokkoló olvasási/írási művelet egyetlen szálból okozza, és több szál magasabb szintű egyidejűség érdekében lehetővé teszi a virtuális gépen. Annak érdekében, hogy szintek kifogástalan állapotú, és növelhető a párhuzamosságot, ügyeljen arra, figyelheti a virtuális gép CPU-kihasználtság.

### <a name="avoid-small-file-sizes"></a>Kis méretűek elkerülése

A POSIX-engedélyek és a naplózás a Data Lake Storage Gen1 tartalmaz egy járó többletterhelés nyilvánvalóvá válik, ha nagyon sok kis méretű fájl dolgozik. Ajánlott eljárásként nagyobb méretű fájlok és több ezer vagy millió kisméretű fájlok írása a Data Lake Storage Gen1 be kell az adatok kötegelt. Kerülje a kis méretűek lehetnek több előnnyel jár, például:

* Azoknak a hitelesítési ellenőrzések között több fájl
* Fájl megnyitása kapcsolatok csökkent
* Gyorsabb másolása és replikáció
* Kevesebb fájlok feldolgozásához, amikor a Data Lake Storage Gen1 POSIX engedélyeinek frissítése

Attól függően, hogy milyen szolgáltatásokra és számítási feladatokra használ az adatok, egy jó kell figyelembe venni a fájlok mérete 256 MB vagy annál nagyobb. Ha a fájl mérete nem lehet kötegelni, ha a Data Lake Storage Gen1 üzenetsorokra, használhat egy külön tömörítés feladat, amely ezeket a fájlokat egyesíti a nagyobb azokat. További információk és a fájlméret és az adatok a Data Lake Storage Gen1 javaslat: [struktúra az adatkészlet](data-lake-store-performance-tuning-guidance.md#structure-your-data-set).

### <a name="large-file-sizes-and-potential-performance-impact"></a>Nagy fájlok mérete és a teljesítményre gyakorolt lehetséges hatás

Bár a Data Lake Storage Gen1 támogatja az akár több petabájtnyi nagy méretű fájlok mérete, az optimális teljesítmény és függően a folyamat az adatok olvasása nem lehet ideális megoldást kínál a go átlagosan 2 GB felett. Használata esetén például **Distcp** helyeket vagy különböző storage-fiókok közötti adatokat másolja, a fájlok a térkép feladatok határozza meg a legkiválóbb szintjét. Ezért ha 1 TB-os 10 fájlokat másol, legfeljebb 10 leképező vannak lefoglalva. Emellett a hozzárendelt leképező fájlok számos lehetősége van, ha kezdetben a leképező dolgozhat párhuzamosan nagy méretű fájlok áthelyezése. A feladat indulásakor befejezéséhez csak néhány leképező lefoglalva maradnak, és is elakadt a nagy fájlok rendelve egy egyetlen eseményleképező. A Microsoft elküldte a jövőbeli Hadoop-verziók a probléma megoldása érdekében a Distcp fejlesztései.

Egy másik példa kell figyelembe venni, amikor az Azure Data Lake Analytics használata a Data Lake Storage Gen1. A feldolgozás, a kivonatoló végezhető el, attól függően néhány fájlt, amelyet nem lehet megosztani (például XML, JSON) sikerült kárát 2 GB-nál nagyobb teljesítményre. Azokban az esetekben, ahol fájlok oszthatók fel egy mappába (például CSV) szerint nagy méretű fájlok használata javasolt.

### <a name="capacity-plan-for-your-workload"></a>A számítási kapacitás tervezése

Az Azure Data Lake Storage Gen1 eltávolítja az szigorú IO szabályozási korlátait, amely a Blob storage-fiókok kerülnek. Vannak azonban továbbra is ideiglenes korlátok, amelyek figyelembe kell venni. Az alapértelmezett bejövő/kimenő forgalom szabályozási korlátait igényeinek a legtöbb forgatókönyvhöz. Ha a számítási feladatok növelni a korlátozások van szüksége, működik a Microsoft ügyfélszolgálatához. Emellett tekintse meg a korlátok a koncepció igazolása szakasz során, hogy az i/o-szabályozási korlátok nem éles közben nyomja le. Ha ez történik, a Microsoft mérnöki csapat manuális növeléséhez várakozási igényelhet. I/o-szabályozás akkor fordul elő, ha az Azure Data Lake Storage Gen1 429-es hiba kódot ad vissza, és ideális esetben meg kell ismételni megfelelő exponenciális visszatartással szabályzat.

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>A Data Lake Storage Gen1 illesztőprogram puffer "írás" optimalizálása

A teljesítmény optimalizálása és csökkentheti a IOPS, a Hadoop Data Lake Storage Gen1 való írás esetén hajtsa végre az írási műveletek a Data Lake Storage Gen1 illesztőprogram pufferméret minél közelebb. Lehetőleg ne a puffer mérete meghaladja a előtt írta, mint például amikor streamelés az Apache Storm vagy Spark streamelési feladatok. Írás a Data Lake Storage Gen1 HDInsight/hadoopból, esetén fontos tudni, hogy a Data Lake Storage Gen1 van-e a 4 MB-os puffer illesztőprogram. Például sok fájl rendszer-illesztőprogramok ezt a puffert is manuális kiürítése 4 MB-os elérése előtt. Ha nem, akkor azonnal kiürül, Storage, ha a következő írási meghaladja a puffer maximális méretét. Ahol lehetséges, meg kell elkerülése érdekében túllépés vagy a puffer egy jelentős aláfutásgátló házirend szerint száma vagy idő szinkronizálása/írta.

## <a name="resiliency-considerations"></a>Rugalmassággal kapcsolatos szempontok

Amikor a rendszer a Data Lake Storage Gen1 vagy bármilyen felhőalapú szolgáltatás kiépítése, meg kell fontolnia a rendelkezésre állási követelmények vonatkoznak, és hogyan reagáljon a lehetséges zavart a szolgáltatásban. Problémát sikerült honosított, a példány, vagy akár régióra kiterjedő, így kellene csomagot is fontos. Attól függően, a **helyreállítási időre vonatkozó célkitűzés** és a **helyreállításipont-célkitűzés** SLA-k a számítási feladatok számára, akkor érdemes választani, magas rendelkezésre állás és vészhelyreállítás helyreállítási jobban vagy kevésbé agresszív stratégiáját.

### <a name="high-availability-and-disaster-recovery"></a>Magas rendelkezésre állás és vészhelyreállítás

Magas rendelkezésre ÁLLÁS és vészhelyreállítás (DR) is néha egyesíthet, bár a különösen akkor, ha az adatok betölt egy némileg eltérő stratégia mindegyik rendelkezik. Data Lake Storage Gen1 már kezeli a helyi hardverhibák ellen védő összetevői 3 x replikáció. Azonban mivel régiók közötti replikáció nem épített, kell kezelni a saját maga. A terv létrehozását, a magas rendelkezésre ÁLLÁSÚ, egy szolgáltatáskimaradás esetén a számítási feladatok hozzá kell férnie a legfrissebb adatok a lehető leggyorsabban helyben vagy egy új régióban külön-külön replikált példányra között.

A Vészhelyreállítási stratégia előkészítése, illetve a régió, végzetes hiba nem túl valószínű esetben fontos is egy másik régióba replikálja az adatokat. Ezek az adatok kezdetben lehet ugyanaz, mint a replikált adatok magas rendelkezésre ÁLLÁS. Emellett azonban meg kell fontolnia a szélsőséges eseteket, ahol lehetséges, hogy szeretne létrehozni, rendszeres időközönként pillanatképek tud visszatérni adatsérülés például követelményeinek. Fontosság és az adatok méretétől függően érdemes a működés közbeni 1 – 6 – és 24 órás időszakokra különbözeti pillanatképeket a helyi és/vagy másodlagos áruházban kockázati tolerancia megfelelően.

A Data Lake Storage Gen1 adatrugalmasság érdekében ajánlott georeplikáció, az adatok egy külön régióban, amely eleget tesz a magas rendelkezésre ÁLLÁS/Vészhelyreállítás követelmények ideális esetben minden órában gyakorisággal. Ez a replikációs gyakoriság minimálisra csökkenti a nagy adatmozgást, amelyeken a fő és jobb helyreállítási időkorlát (RPO) kell egymással versengő átviteli sebesség. Emellett érdemes lehet az alkalmazás automatikusan átadja a feladatokat a másodlagos eseményindítók figyelési fiókot a Data Lake Storage Gen1 segítségével módszert vagy hossza sikertelen kísérlet, vagy legalább a rendszergazdák kézi beavatkozás értesítést küldeni. Ne feledje, hogy nincs-e kompromisszummal jár, és a egy szolgáltatás Várakozás-ba irányuló feladatátvétel ismét online elérhető lesz. Az adatok replikálása nem fejeződött be, ha feladatátvétel esetleges adatvesztés, inkonzisztenciát vagy az adatok egyesítése összetett okozhatnak.

Az alábbiakban a Data Lake Storage Gen1 fiókok és az azok közötti fő különbségeket között vezénylésével replikációs felső három javasolt lehetőség.

|  |A Distcp  |Azure Data Factory  |Az AdlCopy  |
|---------|---------|---------|---------|
|**Kibővített skálázási korlátai**     | Amelyet a munkavégző csomópontok        | Maximális Felhőbeli adatáthelyezési egységek által korlátozott        | Kötelező érvénnyel elemzési egységek        |
|**Támogatja az eltérések másolása**     |   Igen      | Nem         | Nem         |
|**Beépített vezénylési**     |  Nem (használni az Oozie légmozgás vagy cron feladat)       | Igen        | Nem (használja az Azure Automation vagy a Windows Feladatütemezőt)         |
|**Támogatott fájlrendszerek**     | ADL, HDFS, WASB, S3, GS, CFS        |Számos, lásd: [összekötők](../data-factory/connector-azure-blob-storage.md).         | Az ADL az ADL, WASB az ADL (csak az azonos régióban)        |
|**Operációsrendszer-támogatást**     |Bármely operációs rendszeren futó Hadoop         | –          | Windows 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>A Distcp használata a két hely közötti adatáthelyezés

Rövid a elosztott, a Distcp-példány Linux parancssori eszköz, amely együttműködik a Hadoop és a két hely között elosztott adatáthelyezés biztosít. A két helyen lehet a Data Lake Storage Gen1, HDFS, WASB vagy az S3. Ezzel az eszközzel (például a HDInsight) Hadoop-fürtön a MapReduce-feladatok használatával horizontális felskálázása az összes csomóponton. A Distcp nélkül speciális hálózati tömörítés berendezések big Data típusú adatok áthelyezése a leggyorsabb módot kínálni arra számít. A Distcp is lehetőséget biztosít, hogy csak a változásokat között két helyen, automatikus újrapróbálkozások kezeli, valamint számítási dinamikus méretezés frissítése. Ez a megközelítés akkor rendkívül hatékony, ha a replikálása, például a Hive és a Spark táblákat, amelyeken egyetlen címtárban sok nagy fájlt betölt, és, csak a módosított adatokat másolni kívánja. Ebből kifolyólag a Distcp a leginkább ajánlott eszköz big Data típusú adatok adattárak közötti másolást.

Másolási feladat indítható el gyakorisága vagy adatok eseményindítók, valamint azokat az Linux cron feladat használatával Apache Oozie-munkafolyamatokkal. Nagy számításigényű replikációs feladatok esetében ajánlott egy külön HDInsight Hadoop-fürtöt, amely hangolva, és kifejezetten a másolási feladatokat a horizontálisan üzembe helyezését. Ez biztosítja, hogy a másolási feladatokat nem befolyásolják a kritikus feladatok. Replikációs futtat egy elég széles ahhoz gyakoriság, ha a fürt akkor is le lehessen állítani minden egyes feladat között. Ha feladatátvétele másodlagos régióba, ügyeljen arra, hogy egy másik fürtre is működésbe új adatokat az elsődleges Data Lake Storage Gen1 fiók való újbóli replikáláshoz, követően betölt vissza a másodlagos régióban. A Distcp használata példákért lásd: [az adatok Azure Storage-Blobok és a Data Lake Storage Gen1 közötti másolásához használja a Distcp](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Az Azure Data Factory segítségével a másolási feladatok ütemezése

Az Azure Data Factory használatával másolási feladatok ütemezéséhez is használható egy **másolási tevékenység**, még akkor is beállíthatja a gyakoriságot keresztül, és a **másolása varázsló**. Ne feledje, hogy az Azure Data Factory felhőbeli adatáthelyezési egységek (DMUs) legfeljebb, és végül caps az átviteli sebesség/számítási nagy mennyiségű adat számítási feladatokhoz. Emellett az Azure Data Factory jelenleg nem használt összegeket különbözeti frissítések Data Lake Storage Gen1 fiókokba, így mappák – például a Hive-táblák teljes másolat replikálásához lenne szükség. Tekintse meg a [finomhangolási útmutató a másolási tevékenység](../data-factory/copy-activity-performance.md) további információ a Data Factory másolás.

### <a name="adlcopy"></a>Az AdlCopy

Az AdlCopy egy Windows parancssori eszköz, amely lehetővé teszi, hogy csak az azonos régión belüli két Data Lake Storage Gen1 fiókok közötti adatokat másolja. Az AdlCopy eszközt biztosít egy önálló vagy a beállítás az másolási feladat futtatása az Azure Data Lake Analytics-fiók használatával. Igény szerinti példány helyett egy robusztus replikációs eredetileg lett létrehozva, azonban ehhez a Data Lake Storage Gen1 fiók ugyanazon a régión belül elosztott másolása egy másik lehetőséget biztosít. A megbízhatóság azt javasoljuk, hogy a prémium szintű a Data Lake Analytics beállítást használja minden olyan éles környezetbeli számítási feladatokra. Az önálló verziója foglalt válaszokat adhat vissza, és a méretezési csoport és a figyelés csak korlátozott.

A Distcp, például az AdlCopy kell lehet például az Azure Automation vagy a Windows Feladatütemező vezénylésüket valamit. A Data Factory AdlCopy csak a frissített fájlok másolása nem támogatja, de újraterjesztését, és felülírja a fájlokat. További információ és példák az AdlCopy használata [adatok másolása az Azure Storage-Blobokból a Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Felügyeleti szempontok

Data Lake Storage Gen1 biztosít részletes diagnosztikai naplók és a naplózás. Data Lake Storage Gen1 a Data Lake Storage Gen1 fiók alatt az Azure Portalon és az Azure monitorban néhány alapvető metrikákat biztosít. Data Lake Storage Gen1 rendelkezésre állása az Azure-portálon jelenik meg. Azonban ez a metrika adatai hét percenként frissülnek, és nem lehet lekérdezni a nyilvánosan elérhető API-n keresztül. Legfrissebb rendelkezésre állását egy Data Lake Storage Gen1 fiókot kapni, futtatnia kell a saját szintetikus tesztet végrehajtva ellenőrizze a rendelkezésre állási. Például a tárterület teljes kihasználtsága, az olvasási/írási kérések és a bejövő/kimenő forgalom más metrikákkal frissítése akár 24 órát is igénybe vehet. Így naprakész metrika kell számítani a manuális Hadoop parancssori eszközöket vagy összesítése adatait. A leggyorsabb módja a legutóbbi tárterület kihasználtsága lekérése a HDFS parancs fut egy Hadoop-fürt csomópontja (például átjárócsomópont):

    hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Data Lake Storage Gen1 diagnosztika exportálása

A Data Lake Storage Gen1 kereshető naplók eléréséhez leggyorsabb módon ahhoz, hogy a naplóküldés **Log Analytics** alatt a **diagnosztikai** a Data Lake Storage Gen1 fiók panelen. Ez a bejövő naplók azonnali hozzáférést biztosít az idő és a tartalom szűrőket, valamint riasztási beállítások (e-mailt és webhookot) belül 15 percenként aktiválódik. Útmutatásért lásd: [diagnosztikai naplóinak elérése az Azure Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

Több valós idejű riasztások és átfogóbb vezérlésre megnyitja a naplók hová fontolja meg a naplók exportálása az Azure Event hubhoz, ahol tartalom elemezhetők egyenként, vagy adott időtartamra annak érdekében, hogy egy üzenetsorba valós idejű értesítések elküldése. Egy különálló alkalmazás, például egy [logikai alkalmazás](../connectors/connectors-create-api-azure-event-hubs.md) is majd felhasználását és a riasztásokat a megfelelő csatorna való kommunikációhoz, valamint monitorozási eszközökkel, mint például a NewRelic, Datadoggal vagy AppDynamics metrikákat nyújt. Azt is megteheti, ha egy külső eszköz, például az ElasticSearch használ, exportálhatja a naplók a Blob Storage, és használja a [Azure Logstash beépülő modul](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) az adatok felhasználásához az Elasticsearch, a Kibana és a Logstash (elk-val) verembe.

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Hibakeresési szintűre a HDInsight-naplózás bekapcsolása

Ha a Data Lake Storage Gen1 naplóküldés nincs bekapcsolva, az Azure HDInsight azt is lehetővé teszi bekapcsolása [ügyféloldali naplózás a Data Lake Storage Gen1](data-lake-store-performance-tuning-mapreduce.md) log4j keresztül. Be kell a következő tulajdonság a **Ambari** > **YARN** > **Config** > **yarn-log4j speciális konfigurációk**:

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG

A tulajdonság értéke, és a csomópont újra lesz indítva, Data Lake Storage Gen1 diagnosztikai íródik a YARN-naplókat a csomópontok (/tmp/\<felhasználói\>/yarn.log), és fontos részleteket, például hibákat, vagy (HTTP 429-es hibakód) a szabályozás akkor lehet a figyelt. Ugyanezeket az adatokat az Azure Monitor naplóira vagy bárhol is legyenek a naplók rendszerrel szállított is megfigyelhetők a [diagnosztikai](data-lake-store-diagnostic-logs.md) a Data Lake Storage Gen1 fiók panelen. Ajánlott legalább ügyféloldali naplózás engedélyezve van, vagy a naplóküldés átláthatók és könnyebb hibakeresés a Data Lake Storage Gen1 beállítást használják.

### <a name="run-synthetic-transactions"></a>Szintetikus tranzakciók futtatása

Jelenleg a szolgáltatás rendelkezésre állási metrika, a Data Lake Storage Gen1 az Azure Portalon a 7 perces frissítési időszak van. Ezenkívül azt nem lehet lekérdezni egy nyilvánosan elérhető API-val. Ezért ajánlott létrehozni egy alapszintű alkalmazást, amelyet a Data Lake Storage Gen1 legfeljebb a percenkénti rendelkezésre állási szintetikus tranzakciók. Egy példa arra, hogy létrehoz, a webjobs-feladat, a logikai alkalmazás vagy Azure-Függvényalkalmazás hajtsa végre az olvasási, hozhatók létre, és a Data Lake Storage Gen1 elleni frissítése és az eredményeket elküldik a figyelési megoldást. A műveletek is végezhető el egy ideiglenes mappába, és majd törlése után a tesztet, amely minden 30 – 60 másodpercre, követelményeitől függően előfordulhat, hogy futtatható.

## <a name="directory-layout-considerations"></a>Directory elrendezés kapcsolatos szempontok

Tárol adatokat a data lake-be, ha fontos, előre megterveznie az adatok struktúráját, így a biztonsági, particionálás, és a feldolgozási ténylegesen felhasznált. Számos, az alábbi javaslatokat is használható, azt az Azure Data Lake Storage Gen1, a Blob Storage vagy a HDFS-e. Minden számítási feladathoz tartozik az adatokat használja fel, de néhány gyakori elrendezések az IoT használatakor érdemes figyelembe venni az alábbiakban és batch-forgatókönyv eltérő követelmények vonatkoznak.

### <a name="iot-structure"></a>IoT-struktúra

Az IoT-terheléseket az adattár, amely számos olyan termékeket, eszközök, szervezetek és ügyfelek átfogóan alatt érkezett adatok nagy fokú is lehet. Fontos, előre megterveznie a szervezet, a biztonság és a hatékony feldolgozás érdekében le-stream fogyasztók számára az adatok directory elrendezést. Megfontolandó általános sablon lehet az alábbi elrendezés:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Például egy repülőgép-motor, az Egyesült Királyság belül telemetriai üzenetsorokra hasonló lehet az alábbi struktúrával:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Ezért fontos a dátumot a gyökérmappa-szerkezetében végén helyezi van. Ha meg szeretné zárolását, így bizonyos régiókban, vagy a felhasználók/csoportok tárgyára, majd egyszerűen megteheti a POSIX-engedélyekkel. Ellenkező esetben hiba történt egy bizonyos biztonsági csoport korlátozhatja a csak az Egyesült Királyság adatokat és bizonyos síkok nézetben kell, ha a dátum struktúrával elöl egy külön engedély lenne szükséges számos mappák minden órában mappában. Ezenkívül elöl kellene a dátum struktúra lenne alapján exponenciálisan növeli a mappák számát, ideje történt.

### <a name="batch-jobs-structure"></a>Batch-feladatok struktúra

Egy magas szintű egy általánosan használt megközelítés a kötegelt feldolgozás már adatok kerül egy "a" mappában. Ezt követően az adatok feldolgozása után helyezze az új adatok mappába egy "lejárt" alsóbb rétegbeli folyamatok felhasználásához. A könyvtárstruktúra néha látható feladatok, amelyek az egyes fájlokon feldolgozást, és nem feltétlenül igényelnek a nagymértékben párhuzamos feldolgozási a nagy méretű adatkészleteken. A fenti ajánlott IoT struktúra, például egy jó könyvtárstruktúrát rendelkezik a szülő-szintű mappák számára többek között az régió és tárgyára (például a szervezet, a termék/gyártó). Ez a struktúra segít az adatok védelme a szervezet és a számítási feladatok az adatok jobb kezelése. Továbbá fontolja meg a dátumot és időpontot a struktúra, jobb, szűrt keresések, biztonsági és az automatizálást a feldolgozás való használatának engedélyezése. A dátum struktúra szintjét az időközt, amelyen az adatok feltöltése vagy feldolgozása, például óránként, naponta, vagy akár havi határozza meg.

Egyes esetekben a fájl feldolgozása nem sikerül miatt adatsérülés vagy váratlan formátumban. Ezekben az esetekben könyvtárstruktúrát előnye származhat egy **/hibás** áthelyezni a fájlokat, és a további ellenőrzési mappát. A batch-feladat lehet, hogy kezelik a jelentéskészítési vagy az értesítés ezek *hibás* fájlok manuális beavatkozásra. Vegye figyelembe a következő sablon struktúrát:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Például egy marketing vállalat napi adatok kivonatok ügyfél frissítések kap az ügyfelek Észak-Amerikában. Az alábbi kódrészlet utasítás, előtti és utáni feldolgozás alatt:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

Kötegelt adatok feldolgozása folyamatban közvetlenül a Hive-adatbázisok vagy a hagyományos SQL-adatbázisok a gyakori esetben nincs szükség egy **/in** vagy **/out** óta már kerül a kimeneti mappa egy Különítse el a Hive-tábla és a külső adatbázisok mappát. Például napi kivonatokat a felhasználók által az Azure Data Factory, az Apache Oozie, például a megfelelő mappákat, és a vezénylési lenne megnyitja vagy Apache légmozgás dolgozza fel, és adatokat egy Hive-táblába írni egy napi Hive- vagy Spark feladat lép működésbe.

## <a name="next-steps"></a>További lépések

* [Az Azure Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md)
* [Hozzáférés-vezérlés az Azure Data Lake Storage Gen1](data-lake-store-access-control.md)
* [Biztonság az Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)
* [Az Azure Data Lake Storage Gen1 teljesítmény hangolása](data-lake-store-performance-tuning-guidance.md)
* [Teljesítmény-finomhangolási útmutató a HDInsight Spark használata Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-spark.md)
* [Teljesítmény-finomhangolási útmutató a HDInsight Hive-val az Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-hive.md)
* [HDInsight-fürtök létrehozása a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
