---
title: Másolási tevékenység teljesítményére és méretezhetőségére vonatkozó útmutató
description: Ismerje meg azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés teljesítményére Azure Data Factory a másolási tevékenység használatakor.
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
ms.openlocfilehash: aedb3df69821d1436b03b2eb1f12873b624d426e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414172"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Másolási tevékenység teljesítményére és méretezhetőségére vonatkozó útmutató

> [!div class="op_single_selector" title1="Válassza ki a használt Azure Data Factory verzióját:"]
> * [1-es verzió](v1/data-factory-copy-activity-performance.md)
> * [Aktuális verzió](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Akár nagy léptékű adatáttelepítést szeretne végezni a EDW-ből az Azure-ba, vagy a különböző forrásokból az Azure-ba történő adatgyűjtést a big data Analitika számára, kritikus fontosságú az optimális teljesítmény és méretezhetőség.  A Azure Data Factory egy nagy teljesítményű, rugalmas és költséghatékony mechanizmust biztosít az adatok méretekben történő betöltéséhez, így kiválóan alkalmas az olyan adatmérnökök számára, akik magas teljesítményű és skálázható adatfeldolgozási folyamatokat kívánnak kialakítani.

A cikk elolvasása után a következő kérdésekre tud válaszolni:

- Milyen szintű teljesítményt és méretezhetőséget érhet el az ADF másolási tevékenység az adatáttelepítési és adatfeldolgozási forgatókönyvek esetében?

- Milyen lépéseket kell tennie az ADF másolási tevékenység teljesítményének finomhangolásához?
- Milyen ADF-optimalizálási gombokat használhatok egy adott másolási tevékenység teljesítményének optimalizálására?
- Az ADF-en kívül más tényezők is figyelembe veszik a másolási teljesítmény optimalizálásakor?

> [!NOTE]
> Ha nem ismeri a másolási tevékenységet általánosságban, tekintse meg a [másolási tevékenység áttekintését](copy-activity-overview.md) a cikk elolvasása előtt.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>A teljesítmény és a méretezhetőség elérhetővé másolása ADF használatával

Az ADF olyan kiszolgáló nélküli architektúrát kínál, amely különböző szinteken teszi lehetővé a párhuzamosságot, ami lehetővé teszi a fejlesztők számára, hogy teljes mértékben kihasználják a hálózati sávszélességet, valamint a tárolási IOPS és sávszélességet, hogy maximalizálják az adatátviteli sebességet a környezet számára.  Ez azt jelenti, hogy az elérni kívánt átviteli sebesség a forrás és a cél közötti minimális átviteli sebesség mérésével, a célhely adattárával és a hálózati sávszélességgel mérhető.  Az alábbi táblázat kiszámítja a másolás időtartamát az adatméret és a környezet sávszélesség-korlátja alapján. 

| Adatméret/ <br/> sávszélesség | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 GB/s   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 perc    | 1,4 perc   | 0,3 perc   | 0,1 perc  | 0,03 perc | 0,01 perc | 0,0 perc   |
| **10 GB**                   | 27,3 perc   | 13,7 perc  | 2,7 perc   | 1,3 perc  | 0,3 perc  | 0,1 perc  | 0,03 perc  |
| **100 GB**                  | 4,6 óra    | 2,3 óra   | 0,5 óra   | 0,2 óra  | 0,05 óra | 0,02 óra | 0,0 óra   |
| **1 TB**                    | 46,6 óra   | 23,3 óra  | 4,7 óra   | 2,3 óra  | 0,5 óra  | 0,2 óra  | 0,05 óra  |
| **10 TB**                   | 19,4 nap  | 9,7 nap  | 1,9 nap  | 0,9 nap | 0,2 nap | 0,1 nap | 0,02 nap |
| **100 TB**                  | 194,2 nap | 97,1 nap | 19,4 nap | 9,7 nap | 1,9 nap | 1 nap    | 0,2 nap  |
| **1 PB**                    | 64,7 mo    | 32,4 Mo   | 6,5 Mo    | 3,2 Mo   | 0,6 Mo   | 0,3 Mo   | 0,06 Mo   |
| **10 PB**                   | 647,3 Mo   | 323,6 Mo  | 64,7 mo   | 31,6 Mo  | 6,5 Mo   | 3,2 Mo   | 0,6 Mo    |

Az ADF-másolás különböző szinteken méretezhető:

![Az ADF másolási méretezése](media/copy-activity-performance/adf-copy-scalability.png)

- Az ADF-vezérlési folyamat egyszerre több másolási tevékenységet is elindíthat, például az [egyes hurkok esetében](control-flow-for-each-activity.md).
- Egy másolási tevékenység kihasználhatja a skálázható számítási erőforrások előnyeit: Azure Integration Runtime használata esetén [akár 256 DIUs-t](#data-integration-units) is megadhat az egyes másolási tevékenységekhez kiszolgáló nélküli módon. saját üzemeltetésű Integration Runtime használatakor manuálisan is méretezheti a gépet, vagy akár több gépre is kibővíthető ([legfeljebb 4 csomópont](create-self-hosted-integration-runtime.md#high-availability-and-scalability)), és egyetlen másolási tevékenység fogja particionálni az összes csomóponton beállított fájlt.
- Egy másolási tevékenység [párhuzamosan](#parallel-copy)több szál használatával olvas be és ír az adattárba.

## <a name="performance-tuning-steps"></a>Teljesítmény-finomhangolási lépések

Hajtsa végre ezeket a lépéseket a Azure Data Factory szolgáltatás teljesítményének finomhangolásához a másolási tevékenységgel.

1. **Válasszon ki egy tesztelési adatkészletet, és hozzon létre egy alaptervet.** A fejlesztési fázisban tesztelje a folyamatot a másolási tevékenységgel egy reprezentatív adatminta alapján. A kiválasztott adatkészlet a jellemző adatmintázatokat (a mappa szerkezete, a fájl mintája, az adatsémát stb.) jelöli, és elég nagy a másolási teljesítmény kiértékeléséhez, például a másolási tevékenység befejezéséhez 10 percet vagy azt meghaladó időt vesz igénybe. A [másolási tevékenység figyelését](copy-activity-monitoring.md)követő végrehajtási adatok és teljesítmény-jellemzők gyűjtése.

2. **Egy másolási tevékenység teljesítményének maximalizálása**:

   Első lépésként azt javasoljuk, hogy először egyetlen másolási tevékenységgel maximalizálja a teljesítményt.

   - **Ha a másolási tevékenységet egy Azure Integration Runtime hajtja végre,** akkor az alapértelmezett értékeket kell megkezdenie az [adatintegrációs egységekhez (DIU)](#data-integration-units) és a [párhuzamos másolási](#parallel-copy) beállításokhoz. 

   - **Ha a másolási tevékenységet egy saját üzemeltetésű Integration Runtime hajtja végre:** javasoljuk, hogy az integrációs modul üzemeltetéséhez az adattárat üzemeltető kiszolgálótól külön dedikált gépet használjon. A [párhuzamos másolási](#parallel-copy) beállítás alapértelmezett értékeivel kezdődik, és egyetlen csomópontot használ a saját üzemeltetésű IR-hez.  

   Végezzen teljesítményteszt-futtatást, és jegyezze fel az elért teljesítményt, valamint a ténylegesen használt értékeket, például a DIUs és a párhuzamos másolást. Tekintse át a [másolási tevékenység figyelését](copy-activity-monitoring.md) a futtatási eredmények és a teljesítmény-beállítások gyűjtésével kapcsolatban, és Ismerje meg, hogyan oldhatja fel a [másolási tevékenység teljesítményét](copy-activity-performance-troubleshooting.md) a szűk keresztmetszetek azonosítására és megoldására. 

   A hibaelhárítási és finomhangolási útmutatást követve további teljesítménytesztek futtatását is elvégezheti. Ha az Egypéldányos másolási tevékenység nem tud jobb teljesítményt elérni, érdemes lehet maximalizálni az összesített átviteli sebességet úgy, hogy több, a 3. lépésre hivatkozó példányt futtat.


3. **Az összesített átviteli sebesség maximalizálása több példány párhuzamos futtatásával:**

   Most, hogy teljes mértékben maximalizálta egy másolási tevékenység teljesítményét, ha még nem érte el a környezet-hálózat, a forrás adattár és a célhely adattárat, több másolási tevékenységet is futtathat párhuzamosan, az ADF vezérlési folyamatának összeállításával, például [az egyes hurokokhoz](control-flow-for-each-activity.md). Tekintse át a [fájlok több tárolóból való másolását](solution-template-copy-files-multiple-containers.md), az [Amazon S3-ból történő adatáttelepítést ADLS Gen2](solution-template-migration-s3-azure.md), vagy egy általános példaként egy vezérlőelem-tábla megoldási sablonjaival történő [tömeges másolást](solution-template-bulk-copy-with-control-table.md) .

5. **Bontsa ki a konfigurációt a teljes adatkészletre.** Ha elégedett a végrehajtás eredményeivel és teljesítményével, kiterjesztheti a definíciót és a folyamatot a teljes adatkészlet lefedéséhez.

## <a name="troubleshoot-copy-activity-performance"></a>A másolási tevékenység teljesítményével kapcsolatos hibák

Kövesse a [teljesítmény-hangolás lépéseit](#performance-tuning-steps) a teljesítmény-ellenőrzés megtervezéséhez és a forgatókönyvhöz való végrehajtásához. És megtudhatja, hogyan lehet elhárítani az egyes másolási tevékenységek futtatásának teljesítményét a Azure Data Factory a [másolási tevékenység teljesítményének hibakeresésével](copy-activity-performance-troubleshooting.md).

## <a name="copy-performance-optimization-features"></a>Teljesítmény-optimalizálási funkciók másolása

Azure Data Factory a következő teljesítmény-optimalizálási funkciókat biztosítja:

- [Adatintegrációs egységek](#data-integration-units)
- [Saját üzemeltetésű Integration Runtime skálázhatósága](#self-hosted-integration-runtime-scalability)
- [Párhuzamos másolás](#parallel-copy)
- [Előkészített másolás](#staged-copy)

### <a name="data-integration-units"></a>Adatintegrációs egységek

Az adatintegrációs egység olyan mérték, amely a Azure Data Factory egyetlen egységének a CPU-, memória-és hálózati erőforrás-lefoglalási kombinációját jelöli. Az adatintegrációs egység csak az [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime)szolgáltatásra vonatkozik, a saját üzemeltetésű [integrációs](concepts-integration-runtime.md#self-hosted-integration-runtime)modul nem. [További információ](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Saját üzemeltetésű Integration Runtime skálázhatósága

A párhuzamos munkaterhelések és a nagyobb teljesítmény elérése érdekében a saját üzemeltetésű Integration Runtime vertikális felskálázásával vagy felskálázásával végezhető el. [További információ](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability).

### <a name="parallel-copy"></a>Párhuzamos másolás

A párhuzamos másolással beállíthatja azt a párhuzamosságot, amelyet a másolási tevékenység használni kíván. Ezt a tulajdonságot úgy tekintheti meg, mint a másolási tevékenységben a forrásból beolvasott szálak maximális száma, vagy párhuzamosan írható a fogadó adattárba. [További információ](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Előkészített másolás

Amikor Adatmásolást végez egy forrás adattárból egy fogadó adattárba, a blob Storage-t átmeneti átmeneti tárolóként használhatja. [További információ](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>További lépések
Lásd a másolási tevékenység egyéb cikkeit:

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [A másolási tevékenység teljesítményével kapcsolatos hibák](copy-activity-performance-troubleshooting.md)
- [Másolási tevékenység teljesítményének optimalizálási funkciói](copy-activity-performance-features.md)
- [Az adatok áttelepíthetők a Azure Data Factory használatával az Azure-ba vagy az adattárházból](data-migration-guidance-overview.md)
- [Adatok migrálása az Amazon S3-ból az Azure Storage-ba](data-migration-guidance-s3-azure-storage.md)
