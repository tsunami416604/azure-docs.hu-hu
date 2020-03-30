---
title: A tevékenység teljesítményének és méretezhetőségének másolása útmutató
description: Ismerje meg azokat a kulcsfontosságú tényezőket, amelyek befolyásolják az adatok mozgását az Azure Data Factoryban a másolási tevékenység használatakor.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: 231b0d77dc441e70dc0ec8de313291bb6b4f9292
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261397"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>A tevékenység teljesítményének és méretezhetőségének másolása útmutató

> [!div class="op_single_selector" title1="Válassza ki az Azure Data Factory által használt verziót:"]
> * [1-es verzió](v1/data-factory-copy-activity-performance.md)
> * [Aktuális verzió](copy-activity-performance.md)

Akár a Data Lake-ből vagy a nagyvállalati adattárházból (EDW) az Azure-ba szeretne nagyméretű adatáttelepítést végrehajtani, akár különböző forrásokból nagy méretű adatokat szeretne beadni az Azure-ba a big data-elemzéshez, az optimális teljesítmény elérése és Skálázhatóság.  Az Azure Data Factory egy teljesítményalapú, rugalmas és költséghatékony mechanizmust biztosít az adatok nagy méretekben történő betöltéséhez, így kiválóan alkalmas az adatmérnökök számára, akik nagy teljesítményt és skálázható adatbetöltési folyamatokat szeretnének építeni.

A cikk elolvasása után a következő kérdésekre válaszolhat:

- Milyen szintű teljesítményt és méretezhetőséget érhetek el az ADF másolási tevékenység használatával az adatáttelepítési és adatbetöltési forgatókönyvekhez?

- Milyen lépéseket kell megtetnem az ADF másolási tevékenység teljesítményének behangolása érdekében?
- Milyen ADF perf optimalizálási gombokat használhatok a teljesítmény optimalizálásához egyetlen másolási tevékenység futtatásához?
- Milyen egyéb tényezőket kell figyelembe venni az ADF-en kívül a másolási teljesítmény optimalizálásakor?

> [!NOTE]
> Ha általában nem ismeri a másolási tevékenységet, olvassa el a [másolási tevékenység áttekintését](copy-activity-overview.md) a cikk elolvasása előtt.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Az ADF használatával elérhető teljesítmény és méretezhetőség másolása

Az ADF kiszolgáló nélküli architektúrát kínál, amely lehetővé teszi a párhuzamosságokat különböző szinteken, ami lehetővé teszi a fejlesztők számára, hogy csővezetékeket építsenek a hálózati sávszélesség, valamint a tárolási IOPS és sávszélesség teljes kihasználásához a környezet ben lévő adatátviteli sebesség maximalizálása érdekében.  Ez azt jelenti, hogy az elérhető átviteli teljesítmény megbecsülhető a forrásadattár, a céladattár és a forrás és a cél közötti hálózati sávszélesség minimális átviteli értékének mérésével.  Az alábbi táblázat az adatok mérete és a környezet sávszélesség-korlátja alapján számítja ki a másolás időtartamát. 

| Adatméret / <br/> Sávszélesség | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 min    | 1,4 min   | 0,3 min   | 0,1 óra  | 0,03 min | 0,01 min | 0,0 min   |
| **10 GB**                   | 27,3 min   | 13,7 min  | 2,7 min   | 1,3 min  | 0,3 min  | 0,1 óra  | 0,03 min  |
| **100 GB**                  | 4,6 óra    | 2,3 óra   | 0,5 óra   | 0,2 óra  | 0.05 óra | 0.02 óra | 0,0 óra   |
| **1 TB**                    | 46,6 óra   | 23.3 óra  | 4,7 óra   | 2,3 óra  | 0,5 óra  | 0,2 óra  | 0.05 óra  |
| **10 TB**                   | 19,4 nap  | 9,7 nap  | 1,9 nap  | 0,9 nap | 0,2 nap | 0,1 nap | 0,02 nap |
| **100 TB**                  | 194,2 nap | 97,1 nap | 19,4 nap | 9,7 nap | 1,9 nap | 1 nap    | 0,2 nap  |
| **1 PB**                    | 64,7 hó    | 32,4 hó   | 6,5 hó    | 3,2 mo   | 0,6 mo   | 0,3 mo   | 0,06 mo   |
| **10 PB**                   | 647,3 mo   | 323,6 hó  | 64,7 hó   | 31,6 hó  | 6,5 hó   | 3,2 mo   | 0,6 mo    |

Az ADF-példány különböző szinteken méretezhető:

![hogyan méretezhető az ADF másolása](media/copy-activity-performance/adf-copy-scalability.png)

- Az ADF vezérlőfolyamat több másolási tevékenységet is indíthat párhuzamosan, például a [Minden ciklushoz parancsot](control-flow-for-each-activity.md)használva.
- Egyetlen másolási tevékenység kihasználhatja a méretezhető számítási erőforrások előnyeit: az Azure-integrációs futásidő használatakor kiszolgáló nélküli módon [legfeljebb 256 DNS-t](#data-integration-units) adhat meg minden egyes másolási tevékenységhez; saját üzemeltetésű integrációs futásidő használataesetén manuálisan skálázhatja a gépet, vagy horizontális felskálázhatja több gépre[(legfeljebb 4 csomópontra),](create-self-hosted-integration-runtime.md#high-availability-and-scalability)és egyetlen másolási tevékenység particionálja a fájlkészletét az összes csomóponton.
- Egyetlen másolási tevékenység olvas be az adattárból, és több szálat használ [párhuzamosan.](#parallel-copy)

## <a name="performance-tuning-steps"></a>Teljesítményhangolási lépések

Az alábbi lépésekkel hangolhatja be az Azure Data Factory szolgáltatás teljesítményét a másolási tevékenységgel.

1. **Vegyen fel egy tesztadatkészletet, és hozzon létre egy alapvonalat.** A fejlesztési fázisban tesztelje a folyamatot a másolási tevékenység egy reprezentatív adatminta segítségével. A kiválasztott adatkészletnek a tipikus adatmintákat (mappastruktúra, fájlminta, adatséma és így tovább) kell képviselnie, és elég nagy ahhoz, hogy kiértékelje a másolási teljesítményt, például 10 percet vagy annál tovább tart a másolási tevékenység befejezéséhez. A másolási tevékenység [figyelését](copy-activity-monitoring.md)követően gyűjtse össze a végrehajtási adatokat és a teljesítményjellemzőket.

2. **Egyetlen másolási tevékenység teljesítményének maximalizálása:**

   Először is azt javasoljuk, hogy először maximalizálja a teljesítményt egyetlen másolási tevékenység használatával.

   - **Ha a másolási tevékenység végrehajtása egy Azure-integrációs runtime:** indítsa el az alapértelmezett [értékekadat-integrációs egységek (DIU)](#data-integration-units) és [a párhuzamos másolási](#parallel-copy) beállításokat. 

   - **Ha a másolási tevékenység végrehajtása saját üzemeltetésű integrációs futásidejű:** azt javasoljuk, hogy az adattárolót üzemeltető kiszolgálótól elkülönített dedikált gépet használjon az integrációs futásidő üzemeltetéséhez. Kezdje a [párhuzamos másolási](#parallel-copy) beállítás alapértelmezett értékeivel, és használjon egyetlen csomópontot a saját üzemeltetésű infravörös kapcsolatához.  

   Végezzen teljesítményteszt-futtatást, és jegyezze fel az elért teljesítményt, valamint a DIUs-hoz és a párhuzamos másolatokat hasonló tényleges értékeket. Tekintse meg a [keresési figyelés másolása,](copy-activity-monitoring.md) hogyan gyűjtheti a futtatási eredmények és a használt teljesítménybeállítások, és hogyan [hárítsa el a másolási tevékenység teljesítményét](copy-activity-performance-troubleshooting.md) a szűk keresztmetszet azonosítása és megoldása. 

   A hibaelhárítási és hangolási útmutatót követően további teljesítményteszt-futtatásokat hajtson végre. Ha az egypéldányos tevékenység futtatása nem érhető el jobb átviteli képesség, fontolja meg az összesített átviteli képesség maximalizálása több példány egyidejű futtatásával a 3.


3. **Az összesített átviteli arány maximalizálása több példány egyidejű futtatásával:**

   Most, hogy maximalizálta egyetlen másolási tevékenység teljesítményét, ha még nem érte el a környezet átviteli felső határait – a hálózatot, a forrásadat-tárolót és a céladat-tárolót –, több másolási tevékenységet is futtathat párhuzamosan az ADF vezérlőfolyamat-konstrukciók, például az [Egyes ciklusok használatával.](control-flow-for-each-activity.md) Példaként a [Fájlok másolása több tárolóból,](solution-template-copy-files-multiple-containers.md) [Adatok áttelepítése az Amazon S3-ról az ADLS Gen2-be](solution-template-migration-s3-azure.md), vagy [például a Hármas példány vezérlőtábla-megoldássablonokkal](solution-template-bulk-copy-with-control-table.md) című dokumentum.

5. **Bontsa ki a konfigurációt a teljes adatkészletre.** Ha elégedett a végrehajtási eredményekkel és a teljesítménnyel, kibonthatja a definíciót és a folyamatot a teljes adatkészlet lefedéséhez.

## <a name="troubleshoot-copy-activity-performance"></a>Másolási tevékenység teljesítményének – problémamegoldás

Kövesse a [Teljesítményhangolás lépéseit](#performance-tuning-steps) a forgatókönyv teljesítménytesztjének megtervezéséhez és elvégzéséhez. És megtudhatja, hogyan háríthatja el az egyes másolási tevékenységek futtatásának teljesítményével kapcsolatos problémát az Azure Data Factoryban [a másolási tevékenység teljesítményének hibaelhárítása című témakörből.](copy-activity-performance-troubleshooting.md)

## <a name="copy-performance-optimization-features"></a>Teljesítményoptimalizálási funkciók másolása

Az Azure Data Factory a következő teljesítményoptimalizálási funkciókat biztosítja:

- [Adatintegrációs egységek](#data-integration-units)
- [Saját üzemeltetésű integrációs futásidejű méretezhetőség](#self-hosted-integration-runtime-scalability)
- [Párhuzamos másolat](#parallel-copy)
- [Szakaszos másolás](#staged-copy)

### <a name="data-integration-units"></a>Adatintegrációs egységek

Az adatintegrációs egység olyan mérték, amely egyetlen egység energiaellátását (a PROCESSZOR, a memória és a hálózati erőforrás-elosztás kombinációját) jelöli az Azure Data Factoryban. Az adatintegrációs egység csak az [Azure-integrációs futásidőre](concepts-integration-runtime.md#azure-integration-runtime)vonatkozik , de [az önkiszolgáló integrációs futásidőre](concepts-integration-runtime.md#self-hosted-integration-runtime)nem. [További információ](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Saját üzemeltetésű integrációs futásidejű méretezhetőség

Egyidejű munkaterhelés növeléséhez vagy nagyobb teljesítmény eléréséhez felskálázhatja vagy kiskálázhatja a saját üzemeltetésű integrációs futásidőt. [További információ](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability).

### <a name="parallel-copy"></a>Párhuzamos másolat

A párhuzamos másolást beállíthatja a másolási tevékenység által használandó párhuzamosság jelzésére. Ezt a tulajdonságot a forrásból beolvasott vagy a fogadó adattárolóiba párhuzamosan írt másolási tevékenységen belüli szálak maximális számaként is felhasználhatja. [További információ](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Szakaszos másolás

Amikor adatokat másol egy forrásadattárból egy fogadó adattárba, dönthet úgy, hogy a Blob storage-ot ideiglenes átmeneti tárolóként használja. [További információ](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>További lépések
Lásd a többi másolási tevékenységcikkeket:

- [Tevékenység másolása – áttekintés](copy-activity-overview.md)
- [Másolási tevékenység teljesítményének – problémamegoldás](copy-activity-performance-troubleshooting.md)
- [Tevékenységteljesítmény-optimalizálási funkciók másolása](copy-activity-performance-features.md)
- [Az Azure Data Factory segítségével áttelepítheti az adatokat az adattóból vagy az adattárházból az Azure-ba](data-migration-guidance-overview.md)
- [Adatok migrálása az Amazon S3-ból az Azure Storage-ba](data-migration-guidance-s3-azure-storage.md)
