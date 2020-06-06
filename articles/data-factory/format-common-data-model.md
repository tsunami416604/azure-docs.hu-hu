---
title: Common Data Model formátum
description: Adatok átalakítása a Common adatmodell metaadat-rendszerén keresztül
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: daperlov
ms.openlocfilehash: 0dce717461754ac1259bc666adf4eb9f7ef9d6c2
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465269"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Közös adatmodell-formátum a Azure Data Factoryban
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Common adatmodell (CDM) metaadatrendszer lehetővé teszi, hogy az adatok és az azt jelenti, hogy könnyen meg lehessen osztani az alkalmazások és az üzleti folyamatok között. További információ: [Common adatmodell](https://docs.microsoft.com/common-data-model/) – áttekintés.

Azure Data Factory a felhasználók a [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2) tárolt CDM-entitásokból és azokból is átalakíthatók a leképezési adatfolyamatok használatával.

> [!NOTE]
> A Common adatmodell (CDM) formátum-összekötő az ADF-adatforgalomhoz jelenleg nyilvános előzetes verzióként érhető el.

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A Common adatmodell a forrásként és a fogadóként is elérhető [beágyazott adatkészletként](data-flow-source.md#inline-datasets) érhető el a leképezési adatforgalomban.

### <a name="source-properties"></a>Forrás tulajdonságai

Az alábbi táblázatban a CDM-forrás által támogatott tulajdonságok szerepelnek.

| Name | Description | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formátum | A formátumot kötelező megadni`cdm` | igen | `cdm` | formátumban |
| Metaadatok formátuma | Ahol az entitás az adatelemre hivatkozik. Ha a CDM 1,0-es verzióját használja, válassza a jegyzékfájlt. Ha 1,0 előtti CDM-verziót használ, válassza a Model. JSON elemet. | Yes | `'manifest'` vagy `'model'` | manifestType |
| Gyökér helye: tároló | A CDM-mappa tárolójának neve | igen | Sztring | Fájlrendszer |
| Gyökér helye: mappa elérési útja | A CDM-mappa gyökérmappa helye | igen | Sztring | folderPath |
| Manifest-fájl: entitás elérési útja | Az entitás mappájának elérési útja a gyökérkönyvtáron belül | nem | Sztring | entityPath |
| Manifest-fájl: jegyzékfájl neve | A jegyzékfájl neve. Alapértelmezett érték: "default"  | No | Sztring | manifestName |
| Szűrés utoljára módosítva | Válassza ki a fájlok szűrését az utolsó módosításuk alapján | nem | Időbélyeg | modifiedAfter <br> modifiedBefore | 
| Séma-társított szolgáltatás | A társított szolgáltatás, ahol a corpus található | igen, ha a jegyzékfájlt használja | `'adlsgen2'` vagy `'github'` | corpusStore | 
| Entitás-hivatkozási tároló | A Container Corpus a | igen, ha a manifest és a corpus használata ADLS Gen2 | Sztring | adlsgen2_fileSystem |
| Entitás-hivatkozási adattár | GitHub-adattár neve | igen, ha a GitHubon és a corpuson használja a GitHubot | Sztring | github_repository |
| Entitás hivatkozási ága | GitHub-tárház ág | igen, ha a GitHubon és a corpuson használja a GitHubot | Sztring |  github_branch |
| Corpus-mappa | a corpus gyökerének helye | igen, ha a jegyzékfájlt használja | Sztring | corpusPath |
| Corpus-entitás | Entitás hivatkozásának elérési útja | igen | Sztring | entitás |
| Nem található fájlok engedélyezése | Ha az értéke igaz, a rendszer nem dobja el a hibát, ha nem található fájl | nem | `true` vagy `false` | ignoreNoFilesFound |

#### <a name="cdm-source-example"></a>A CDM forrásának példája

Az alábbi képen egy példa látható egy CDM-forrás konfigurációra az adatfolyamatok leképezése során.

![CDM-forrás](media/format-common-data-model/data-flow-source.png)

A társított adatfolyam-parancsfájl:

```
source(output(
        ServingSizeId as integer,
        ServingSize as integer,
        ServingSizeUomId as string,
        ServingSizeNote as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'ServingSize.cdm.json/ServingSize',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ServingSizeManifest',
    entityPath: 'ServingSize',
    corpusPath: 'ProductAhold_Updated',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ServingSizeData',
    fileSystem: 'data') ~> CDMSource
```


### <a name="sink-properties"></a>Fogadó tulajdonságai

Az alábbi táblázatban a CDM-fogadó által támogatott tulajdonságok szerepelnek.

| Name | Description | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formátum | A formátumot kötelező megadni`cdm` | igen | `cdm` | formátumban |
| Gyökér helye: tároló | A CDM-mappa tárolójának neve | igen | Sztring | Fájlrendszer |
| Gyökér helye: mappa elérési útja | A CDM-mappa gyökérmappa helye | igen | Sztring | folderPath |
| Manifest-fájl: entitás elérési útja | Az entitás mappájának elérési útja a gyökérkönyvtáron belül | nem | Sztring | entityPath |
| Manifest-fájl: jegyzékfájl neve | A jegyzékfájl neve. Alapértelmezett érték: "default" | No | Sztring | manifestName |
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

#### <a name="cdm-sink-example"></a>CDM-gyűjtő – példa

Az alábbi ábrán egy példa látható egy CDM-fogadó konfigurációra az adatfolyamatok leképezése során.

![CDM-forrás](media/format-common-data-model/data-flow-sink.png)

A társított adatfolyam-parancsfájl:

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'ServingSize.cdm.json/ServingSize',
    format: 'cdm',
    entityPath: 'ServingSize',
    manifestName: 'ServingSizeManifest',
    corpusPath: 'ProductAhold_Updated',
    partitionPath: 'adf',
    folderPath: 'ServingSizeData',
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
