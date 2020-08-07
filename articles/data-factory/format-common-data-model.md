---
title: Common Data Model formátum
description: Adatok átalakítása a Common adatmodell metaadat-rendszerén keresztül
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: daperlov
ms.openlocfilehash: 483e26cf4044b909c8d7923cfd74bd6fcf871e2a
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905289"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Közös adatmodell-formátum a Azure Data Factoryban
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Common adatmodell (CDM) metaadatrendszer lehetővé teszi, hogy az adatok és az azt jelenti, hogy könnyen meg lehessen osztani az alkalmazások és az üzleti folyamatok között. További információ: [Common adatmodell](https://docs.microsoft.com/common-data-model/) – áttekintés.

A Azure Data Factory-ben a felhasználók a [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2)-ban tárolt model.jsés jegyzékfájlok adatait is át tudják alakítani a leképezési adatfolyamatok használatával. Az adatok CDM-formátumban is elhelyezhetők a CDM-entitások olyan hivatkozásaival, amelyek CSV-vagy parketta-formátumban szeretnék kiadni az adatait a particionált mappákban. 

> [!NOTE]
> A Common adatmodell (CDM) formátum-összekötő az ADF-adatforgalomhoz jelenleg nyilvános előzetes verzióként érhető el.

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A Common adatmodell a forrásként és a fogadóként is elérhető [beágyazott adatkészletként](data-flow-source.md#inline-datasets) érhető el a leképezési adatforgalomban.

> [!NOTE]
> A CDM-entitások írásakor olyan meglévő CDM-entitás-definíciót (metaadat-sémát) kell használni, amelyet már definiáltak hivatkozásként való használatra. Az ADF-adatfolyam fogadója beolvassa a CDM-entitás fájlját, és importálja a sémát a fogadóba a mezők leképezéséhez.

### <a name="source-properties"></a>Forrás tulajdonságai

Az alábbi táblázatban a CDM-forrás által támogatott tulajdonságok szerepelnek. Ezeket a tulajdonságokat a **forrás beállításai** lapon módosíthatja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formátum | A formátumot kötelező megadni`cdm` | igen | `cdm` | formátumban |
| Metaadatok formátuma | Ahol az entitás az adatelemre hivatkozik. Ha a CDM 1,0-es verzióját használja, válassza a jegyzékfájlt. Ha 1,0 előtti CDM-verziót használ, válassza a model.jslehetőséget. | Igen | `'manifest'` vagy `'model'` | manifestType |
| Gyökér helye: tároló | A CDM-mappa tárolójának neve | igen | Sztring | Fájlrendszer |
| Gyökér helye: mappa elérési útja | A CDM-mappa gyökérmappa helye | igen | Sztring | folderPath |
| Manifest-fájl: entitás elérési útja | Az entitás mappájának elérési útja a gyökérkönyvtáron belül | nem | Sztring | entityPath |
| Manifest-fájl: jegyzékfájl neve | A jegyzékfájl neve. Alapértelmezett érték: "default"  | Nem | Sztring | manifestName |
| Szűrés utoljára módosítva | Válassza ki a fájlok szűrését az utolsó módosításuk alapján | nem | Timestamp | modifiedAfter <br> modifiedBefore | 
| Séma-társított szolgáltatás | A társított szolgáltatás, ahol a corpus található | igen, ha a jegyzékfájlt használja | `'adlsgen2'` vagy `'github'` | corpusStore | 
| Entitás-hivatkozási tároló | A Container Corpus a | igen, ha a manifest és a corpus használata ADLS Gen2 | Sztring | adlsgen2_fileSystem |
| Entitás-hivatkozási adattár | GitHub-adattár neve | igen, ha a GitHubon és a corpuson használja a GitHubot | Sztring | github_repository |
| Entitás hivatkozási ága | GitHub-tárház ág | igen, ha a GitHubon és a corpuson használja a GitHubot | Sztring |  github_branch |
| Corpus-mappa | a corpus gyökerének helye | igen, ha a jegyzékfájlt használja | Sztring | corpusPath |
| Corpus-entitás | Entitás hivatkozásának elérési útja | igen | Sztring | entitás |
| Nem található fájlok engedélyezése | Ha az értéke igaz, a rendszer nem dobja el a hibát, ha nem található fájl | nem | `true` vagy `false` | ignoreNoFilesFound |

### <a name="sink-settings"></a>Fogadó beállításai

* Mutasson a CDM-entitás hivatkozási fájljára, amely tartalmazza az írni kívánt entitás definícióját.

![entitás beállításai](media/data-flow/common-data-model-111.png "Entitások bemutatása")

* Adja meg a kimeneti fájlok partíciójának elérési útját és formátumát, amelyet az ADF az entitások írásához használni kíván.

![entitás formátuma](media/data-flow/common-data-model-222.png "Entitás formátuma")

* Állítsa be a kimeneti fájl helyét, valamint a jegyzékfájl helyét és nevét.

![CDM helye](media/data-flow/common-data-model-333.png "CDM helye")


#### <a name="import-schema"></a>Séma importálása

A CDM csak beágyazott adatkészletként érhető el, és alapértelmezés szerint nem rendelkezik társított sémával. Az oszlop metaadatainak beszerzéséhez kattintson a **vetítés** lapon található **séma importálása** gombra. Ez lehetővé teszi a corpus által megadott oszlopnevek és adattípusok hivatkozását. A séma importálásához egy [adatfolyam-hibakeresési munkamenetnek](concepts-data-flow-debug-mode.md) aktívnak kell lennie, és rendelkeznie kell egy meglévő CDM-entitás-definíciós fájllal, amely a következőre mutat:.

Amikor az adatáramlási oszlopokat az entitás tulajdonságaira rendeli a fogadó átalakításban, kattintson a "leképezés" fülre, és válassza a "séma importálása" lehetőséget. Az ADF beolvassa az entitásra mutató hivatkozást, amelyet a fogadó beállításaiban mutatott, így a cél CDM-sémához képezhető le.

![CDM-fogadó beállításai](media/data-flow/common-data-model-444.png "CDM-leképezés")

> [!NOTE]
>  Ha olyan model.jshasznál, amely Power BI vagy a Power platform adatfolyamok származik, akkor a forrás-átalakítás során a "corpus Path null vagy üres" hibaüzenet jelenhet meg. Ez valószínűleg a partíció helyének elérési útjának formázásával kapcsolatos, a fájl model.js. A probléma megoldásához kövesse az alábbi lépéseket: 

1. A model.jsfájl megnyitása egy szövegszerkesztőben
2. Keresse meg a partíciókat. Location tulajdonság 
3. A "blob.core.windows.net" módosítása a "dfs.core.windows.net" értékre
4. Javítsa ki a (z) "% 2F" kódolást az URL-ben a "/" értékre.
 

### <a name="cdm-source-data-flow-script-example"></a>A CDM-forrás adatáramlási parancsfájljának példája

```
source(output(
        ProductSizeId as integer,
        ProductColor as integer,
        CustomerId as string,
        Note as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ProductManifest',
    entityPath: 'Product',
    corpusPath: 'Products',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ProductData',
    fileSystem: 'data') ~> CDMSource
```

### <a name="sink-properties"></a>Fogadó tulajdonságai

Az alábbi táblázatban a CDM-fogadó által támogatott tulajdonságok szerepelnek. Ezeket a tulajdonságokat a **Beállítások** lapon módosíthatja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formátum | A formátumot kötelező megadni`cdm` | igen | `cdm` | formátumban |
| Gyökér helye: tároló | A CDM-mappa tárolójának neve | igen | Sztring | Fájlrendszer |
| Gyökér helye: mappa elérési útja | A CDM-mappa gyökérmappa helye | igen | Sztring | folderPath |
| Manifest-fájl: entitás elérési útja | Az entitás mappájának elérési útja a gyökérkönyvtáron belül | nem | Sztring | entityPath |
| Manifest-fájl: jegyzékfájl neve | A jegyzékfájl neve. Alapértelmezett érték: "default" | Nem | Sztring | manifestName |
| Séma-társított szolgáltatás | A társított szolgáltatás, ahol a corpus található | igen | `'adlsgen2'` vagy `'github'` | corpusStore | 
| Entitás-hivatkozási tároló | A Container Corpus a | igen, ha a corpus ADLS Gen2 | Sztring | adlsgen2_fileSystem |
| Entitás-hivatkozási adattár | GitHub-adattár neve | igen, ha Corpus a GitHubban | Sztring | github_repository |
| Entitás hivatkozási ága | GitHub-tárház ág | igen, ha Corpus a GitHubban | Sztring |  github_branch |
| Corpus-mappa | a corpus gyökerének helye | igen | Sztring | corpusPath |
| Corpus-entitás | Entitás hivatkozásának elérési útja | igen | Sztring | entitás |
| Partíció elérési útja | A partíció írásának helye | nem | Sztring | partitionPath |
| Mappa törlése | Ha a célmappa az írás előtt törlődik | nem | `true` vagy `false` | truncate |
| Formátum típusa | Válassza a parketta formátumának megadása lehetőséget. | nem | `parquet`Ha meg van adva | alformátum |
| Oszlop elválasztója | Ha DelimitedText ír, az oszlopok lehatároló módja | igen, ha írás a DelimitedText | Sztring | columnDelimiter |
| Első sor fejlécként | DelimitedText használata esetén, hogy az oszlopnevek fejlécként vannak-e hozzáadva | nem | `true` vagy `false` | columnNamesAsHeader |

### <a name="cdm-sink-data-flow-script-example"></a>A CDM-gyűjtő adatfolyam-parancsfájljának példája

A társított adatfolyam-parancsfájl:

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    entityPath: 'ProductSize',
    manifestName: 'ProductSizeManifest',
    corpusPath: 'Products',
    partitionPath: 'adf',
    folderPath: 'ProductSizeData',
    fileSystem: 'cdm',
    subformat: 'parquet',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CDMSink

```

## <a name="next-steps"></a>Következő lépések

Forrás- [átalakítás](data-flow-source.md) létrehozása a leképezési adatfolyamban.
