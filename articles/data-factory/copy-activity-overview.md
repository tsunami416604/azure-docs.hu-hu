---
title: Tevékenység másolása az Azure Data Factoryban
description: Ismerje meg a másolási tevékenységet az Azure Data Factoryban. Segítségével adatokat másolhat egy támogatott forrásadattárból egy támogatott fogadó adattárba.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: jingwang
ms.openlocfilehash: edb80c604951a140d21e3775eec3f1dc6d55af73
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421414"
---
# <a name="copy-activity-in-azure-data-factory"></a>Tevékenység másolása az Azure Data Factoryban

> [!div class="op_single_selector" title1="Válassza ki a használt Adatgyár verzióját:"]
> * [1-es verzió](v1/data-factory-data-movement-activities.md)
> * [Aktuális verzió](copy-activity-overview.md)

Az Azure Data Factoryban a Másolási tevékenység segítségével adatokat másolhat a helyszínen és a felhőben található adattárak között. Az adatok másolása után más tevékenységekkel tovább alakíthatja és elemezheti azokat. A Másolás tevékenység segítségével közzéteheti az üzleti intelligencia (BI) és az alkalmazások felhasználásának átalakítási és elemzési eredményeit.

![A Másolás tevékenység szerepe](media/copy-activity-overview/copy-activity.png)

A Másolás tevékenység [integrációs futásidejűen](concepts-integration-runtime.md)hajtódik végre. Különböző típusú integrációs futtatási időszakokat használhat különböző adatmásolási forgatókönyvekhez:

* Ha adatokat másol két adattárak között, amelyek nyilvánosan elérhető az interneten keresztül bármely IP-címről, használhatja az Azure-integrációs futásidejű a másolási tevékenység. Ez az integrációs futásidő biztonságos, megbízható, méretezhető és [globálisan elérhető.](concepts-integration-runtime.md#integration-runtime-location)
* Amikor adatokat másol a helyszíni vagy hozzáférés-vezérléssel (például egy Azure virtuális hálózattal rendelkező) hálózatba és olyan adattárakból, amelyeken önkiszolgáló integrációs futásidejűt kell beállítania.

Az integrációs futásidejű kell társítható az egyes forrás-és fogadó adattár. Arról, hogy a Másolás tevékenység hogyan határozza meg, hogy melyik integrációs futásidejűt használja, olvassa el a Használni használandó infravörös kapcsolat meghatározása című [témakört.](concepts-integration-runtime.md#determining-which-ir-to-use)

Ha adatokat szeretne másolni egy forrásból egy fogadóba, a Másolás tevékenységet futtató szolgáltatás a következő lépéseket hajtja végre:

1. Adatokat olvas be egy forrásadattárból.
2. Szerializálást/deszerializálást, tömörítést/dekompressziót, oszlopleképezést és így tovább hajt végre. Ezeket a műveleteket a bemeneti adatkészlet, a kimeneti adatkészlet és a másolási tevékenység konfigurációja alapján hajtja végre.
3. Adatokat ír a fogadó/cél adattárba.

![Másolási tevékenység áttekintése](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Támogatott adattárak és formátumok

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Támogatott fájlformátumok

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A Másolás tevékenységgel fájlokat másolhat két fájlalapú adattár között, amely esetben az adatok másolása szerializálás vagy deszerializálás nélkül történik. Ezenkívül elemezheti vagy létrehozhatja a megadott formátumú fájlokat, például a következőket hajthatja végre:

* Adatok másolása egy helyszíni SQL Server-adatbázisból, és írjon az Azure Data Lake Storage Gen2-be Parketta formátumban.
* Fájlok másolása szöveges (CSV) formátumban egy helyszíni fájlrendszerből, és írjon az Azure Blob storage Avro formátumban.
* Tömörített fájlok másolása egy helyszíni fájlrendszerből, kibonthatja őket menet közben, és kibontott fájlokat írhat az Azure Data Lake Storage Gen2-be.
* Adatok másolása Gzip tömörített szöveg (CSV) formátumban az Azure Blob storage-ból, és írja meg az Azure SQL Database-be.
* Sokkal több olyan tevékenység, amely szerializálást/deszerializációt vagy tömörítést/dekompressziót igényel.

## <a name="supported-regions"></a>Támogatott régiók

A másolási tevékenységet lehetővé tévő szolgáltatás globálisan elérhető az [Azure integrációs futásidejű helyeken](concepts-integration-runtime.md#integration-runtime-location)felsorolt régiókban és földrajzi területeken. A globálisan elérhető topológia biztosítja a hatékony adatmozgást, amely általában elkerüli a régiók közötti ugrásokat. Tekintse meg a [Termékek régiónként,](https://azure.microsoft.com/regions/#services) hogy ellenőrizze a data factory rendelkezésre állását és az adatok mozgását egy adott régióban.

## <a name="configuration"></a>Konfiguráció

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Általában az Azure Data Factory másolási tevékenységének használatához a következőket kell használnia:

1. **Csatolt szolgáltatások létrehozása a forrás-adattárhoz és a fogadó adattárhoz.** A támogatott összekötők listáját a cikk [Támogatott adattárak és formátumok](#supported-data-stores-and-formats) című szakaszában találja. A konfigurációs információkat és a támogatott tulajdonságokat az összekötő cikk "Csatolt szolgáltatás tulajdonságai" című szakaszában talál. 
2. **Hozzon létre adatkészleteket a forráshoz és a fogadóhoz.** A konfigurációs információkat és a támogatott tulajdonságokat a forrás- és fogadóösszekötő-cikkek "Adatkészlet tulajdonságai" szakaszában talál.
3. **Hozzon létre egy folyamatot a Másolás tevékenységgel.** A következő szakasz egy példát tartalmaz.

### <a name="syntax"></a>Szintaxis

A Másolás tevékenység következő sablonja tartalmazza a támogatott tulajdonságok teljes listáját. Adja meg azokat, amelyek illeszkednek a forgatókönyvhöz.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>Szintaxis részletei

| Tulajdonság | Leírás | Kötelező? |
|:--- |:--- |:--- |
| type | Másolási tevékenység esetén állítsa be a`Copy` | Igen |
| Bemenetek | Adja meg a forrásadatokra mutató létrehozott adatkészletet. A Másolás tevékenység csak egyetlen bemenetet támogat. | Igen |
| Kimenetek | Adja meg azt az adatkészletet, amely a fogadó adatokra mutat. A Másolás tevékenység csak egyetlen kimenetet támogat. | Igen |
| typeProperties | Adja meg a másolási tevékenység konfigurálásához tulajdonságokat. | Igen |
| source | Adja meg a másolási forrás típusát és az adatok beolvasásához tartozó tulajdonságokat.<br/>További információt a [Támogatott adattárak és formátumok](#supported-data-stores-and-formats)című összekötőcikk "Tevékenységtulajdonságok másolása" című szakaszában talál. | Igen |
| Mosogató | Adja meg a másolási fogadó típusát és az adatok írásának megfelelő tulajdonságait.<br/>További információt a [Támogatott adattárak és formátumok](#supported-data-stores-and-formats)című összekötőcikk "Tevékenységtulajdonságok másolása" című szakaszában talál. | Igen |
| Fordító | Explicit oszlopleképezések megadása forrástól fogadóig. Ez a tulajdonság akkor érvényes, ha az alapértelmezett másolási viselkedés nem felel meg az igényeinek.<br/>További információ: [Sémaleképezés másolási tevékenységben.](copy-activity-schema-and-type-mapping.md) | Nem |
| dataIntegrationUnits | Adjon meg egy olyan mértéket, amely az [Azure-integrációs futásidejű](concepts-integration-runtime.md) által az adatmásoláshoz használt energiamennyiséget jelöli. Ezeket az egységeket korábban felhőalapú adatmozgási egységeknek (DMU) nevezték. <br/>További információ: [Data Integration Units](copy-activity-performance-features.md#data-integration-units). | Nem |
| párhuzamos másolatok | Adja meg azt a párhuzamosságt, amelyet a Másolás tevékenységnek használnia kell a forrásból történő adatok olvasásakor és az adatok fogadóba írásakor.<br/>További információ: [Párhuzamos másolat](copy-activity-performance-features.md#parallel-copy). | Nem |
| Megőrzése | Adja meg, hogy meg szeretné-e őrizni a metaadatokat/ACL-okat az adatmásolás során. <br/>További információt a [Metaadatok megőrzése](copy-activity-preserve-metadata.md)című témakörben talál. |Nem |
| engedélyezés<br/>átmeneti beállítások | Adja meg, hogy a köztes adatokat a Blob storage-ban adja-e meg ahelyett, hogy közvetlenül másolna adatokat a forrásból a fogadóba.<br/>A hasznos forgatókönyvekről és a konfigurációs részletekről a [Szakaszos másolás](copy-activity-performance-features.md#staged-copy)című témakörben talál további információt. | Nem |
| enableSkipIncompatibleRow<br/>redirectInkompatibilisRowSettings| Adja meg, hogyan kezelje az inkompatibilis sorokat, amikor adatokat másol a forrásból a fogadóba.<br/>További információ: [Fault tolerance](copy-activity-fault-tolerance.md). | Nem |

## <a name="monitoring"></a>Figyelés

Az Azure Data Factoryban vizuálisan és programozott módon futtatott másolási tevékenység figyelheti. További információt a [Másolási tevékenység figyelése (Figyelési tevékenység) témakörben talál.](copy-activity-monitoring.md)

## <a name="incremental-copy"></a>Növekményes másolat

A Data Factory lehetővé teszi a különbözeti adatok növekményes másolását a forrás-adattárból a fogadó adattárba. További [részletek: Oktatóanyag: Adatok fokozatos másolása](tutorial-incremental-copy-overview.md).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás

A [másolási tevékenység figyelési](copy-activity-monitoring.md) felülete megmutatja az egyes tevékenységek futtatásához a másolási teljesítménystatisztikát. A [másolási tevékenység teljesítmény és méretezhetőségi útmutató](copy-activity-performance.md) ismerteti a legfontosabb tényezők, amelyek befolyásolják az adatok mozgását az Azure Data Factory másolási tevékenység keresztül. Emellett felsorolja a tesztelés során megfigyelt teljesítményértékeket, és ismerteti, hogyan optimalizálhatja a Másolás tevékenység teljesítményét.

## <a name="resume-from-last-failed-run"></a>Folytatás az utolsó sikertelen futtatásból

Másolási tevékenység támogatja folytatódik az utolsó sikertelen futtatás, ha nagy méretű fájlokat, mint a bináris formátumú fájlalapú tárolók között, és úgy dönt, hogy megőrizze a mappa / fájl hierarchia forrásból fogadó, például az adatok áttelepítése az Amazon S3 az Azure Data Lake Storage Gen2. A következő fájlalapú összekötőkre vonatkozik: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md)és [SFTP](connector-sftp.md).

A másolási tevékenység folytatását a következő két módon használhatja ki:

- **Tevékenységszint újrapróbálkozása:** Beállíthatja az újrapróbálkozások számát a másolási tevékenységre. A folyamat végrehajtása során, ha ez a másolási tevékenység futtatása sikertelen, a következő automatikus újrapróbálkozás az utolsó próbaverzió hibapontjától indul.
- **Újrafuttatás sikertelen tevékenységből:** A folyamat végrehajtása befejezése után az ADF felhasználói felület figyelési nézetében vagy programozott módon is elindíthatja a sikertelen tevékenység ismétlését. Ha a sikertelen tevékenység egy másolási tevékenység, a folyamat nem csak újrafut ebből a tevékenységből, hanem folytatja az előző futtatás hibapontjáról.

    ![Önéletrajz másolása](media/copy-activity-overview/resume-copy.png)

Néhány megjegyzés:

- Az önéletrajz fájlszinten történik. Ha a másolási tevékenység sikertelen egy fájl másolásakor, a következő futtatáskor a program újra másolja ezt a fájlt.
- A folytatás megfelelő működéséhez ne módosítsa a másolási tevékenység beállításait az ismétlések között.
- Amikor adatokat másol az Amazon S3, az Azure Blob, az Azure Data Lake Storage Gen2 és a Google Cloud Storage, másolási tevékenység folytatódhat tetszőleges számú másolt fájlokat. Míg a többi fájl-alapú összekötők forrásként, jelenleg másolási tevékenység támogatja folytatódik egy korlátozott számú fájl, általában a tartományban több tízezer, és függ a fájl elérési utak; az ezen a számon túli fájlokat az ismétlések során újra átmásolja a rendszer.

A bináris fájlmásolástól eltérő esetekben a másolási tevékenység ismétlése az elejétől kezdődik.

## <a name="preserve-metadata-along-with-data"></a>Metaadatok megőrzése adatokkal együtt

Miközben adatokat másol a forrásból a fogadóba, például az adattó áttelepítése esetén is megőrizheti a metaadatokat és az ACL-okat a másolási tevékenység használatával. A részletekért lásd: [Metaadatok megőrzése.](copy-activity-preserve-metadata.md)

## <a name="schema-and-data-type-mapping"></a>Séma- és adattípus-hozzárendelés

A [Séma- és adattípus-hozzárendelés](copy-activity-schema-and-type-mapping.md) című témakörben talál információt arról, hogy a Másolás tevékenység hogyan rendeli le a forrásadatokat a fogadóhoz.

## <a name="add-additional-columns-during-copy"></a>További oszlopok hozzáadása másolás közben

A forrásadattárból a fogadóba történő másolásmellett beállíthatja azt is, hogy további adatoszlopokat adjon hozzá a fogadóba másolandó adatokhoz. Példa:

- Fájlalapú forrásból történő másoláskor tárolja a relatív fájl elérési útját további oszlopként, hogy nyomon követhesse, honnan származnak az adatok.
- Adjon hozzá egy oszlopot ADF-kifejezéssel, csatolja az ADF rendszerváltozókat, például a folyamat nevét/folyamatazonosítóját, vagy más dinamikus értéket tároljon az upstream tevékenység kimenetéből.
- Adjon hozzá egy statikus értéket tartalmazó oszlopot, hogy megfeleljen a későbbi felhasználási igénynek.

A másolási tevékenység forráslapján a következő konfigurációtalálható: 

![További oszlopok hozzáadása a másolási tevékenységhez](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>Ez a funkció a legújabb adatkészletmodellel működik. Ha ez a lehetőség nem jelenik meg a felhasználói felületen, próbáljon meg létrehozni egy új adatkészletet.

A programozott konfiguráláshoz `additionalColumns` adja hozzá a tulajdonságot a másolási tevékenység forrásához:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| további oszlopok | Adjon hozzá további adatoszlopokat a gyűjtőbe másolni.<br><br>A tömb `additionalColumns` alatt lévő minden objektum egy további oszlopot jelöl. A `name` meghatározza az oszlop nevét, és az `value` oszlop adatértékét jelzi.<br><br>Az engedélyezett adatértékek a következők:<br>- **`$$FILEPATH`**- egy fenntartott változó jelzi a forrásfájlok relatív elérési útját az adatkészletben megadott mappaelérési úthoz. Alkalmazás fájlalapú forrásra.<br>- **Kifejezés**<br>- **Statikus érték** | Nem |

**Példa:**

```json
"activities":[
    {
        "name": "CopyWithAdditionalColumns",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                "additionalColumns": [
                    {
                        "name": "filePath",
                        "value": "$$FILEPATH"
                    },
                    {
                        "name": "pipelineName",
                        "value": {
                            "value": "@pipeline().Pipeline",
                            "type": "Expression"
                        }
                    },
                    {
                        "name": "staticValue",
                        "value": "sampleValue"
                    }
                ],
                ...
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="fault-tolerance"></a>Hibatűrés

Alapértelmezés szerint a Másolás tevékenység leállítja az adatok másolását, és hibát ad vissza, ha a forrásadatsorok nem kompatibilisek a fogadó adatsoraival. A másolás sikeressé adásához beállíthatja, hogy a Másolás tevékenység kihagyja és naplózza az inkompatibilis sorokat, és csak a kompatibilis adatokat másolja. A részletekért [lásd: Tevékenységhiba-tűrés másolása.](copy-activity-fault-tolerance.md)

## <a name="next-steps"></a>További lépések
Tekintse meg az alábbi rövid útmutatókat, oktatóanyagokat és mintákat:

- [Adatok másolása egyik helyről egy másik helyre ugyanabban az Azure Blob tárfiókban](quickstart-create-data-factory-dot-net.md)
- [Adatok másolása az Azure Blob storage-ból az Azure SQL Database-be](tutorial-copy-data-dot-net.md)
- [Adatok másolása helyszíni SQL Server-adatbázisból az Azure-ba](tutorial-hybrid-copy-powershell.md)
