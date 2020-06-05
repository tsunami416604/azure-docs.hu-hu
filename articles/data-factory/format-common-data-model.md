---
title: Közös adatmodell formátuma
description: Adatok átalakítása a Common adatmodell metaadat-rendszerén keresztül
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 2faa14ef7724bb9cccfe425569539f5ef0621a28
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84435393"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Közös adatmodell-formátum a Azure Data Factoryban
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Common adatmodell (CDM) metaadatrendszer lehetővé teszi, hogy az adatok és az azt jelenti, hogy könnyen meg lehessen osztani az alkalmazások és az üzleti folyamatok között. További információ: [Common adatmodell](https://docs.microsoft.com/common-data-model/) – áttekintés.

Azure Data Factory a felhasználók a [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2) tárolt CDM-entitásokból és azokból is átalakíthatók a leképezési adatfolyamatok használatával.

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A Common adatmodell a forrásként és a fogadóként is elérhető [beágyazott adatkészletként](data-flow-source.md#inline-datasets) érhető el a leképezési adatforgalomban.

### <a name="source-properties"></a>Forrás tulajdonságai

Az alábbi táblázatban a CDM-forrás által támogatott tulajdonságok szerepelnek.

| Name | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formátum | A mindig`cdm` | igen | `cdm` | formátumban |
| Metaadatok formátuma | Ahol az entitás az adatelemre hivatkozik. Ha a CDM 1,0-es verzióját használja, válassza a jegyzékfájlt. Ha 1,0 előtti CDM-verziót használ, válassza a Model. JSON elemet. | Igen | `'manifest'` vagy `'model'` | manifestType |
| Gyökér helye: tároló | A CDM-mappa tárolójának neve | igen | Sztring | Fájlrendszer |
| Gyökér helye: mappa elérési útja | A CDM-mappa gyökérmappa helye | igen | Sztring | folderPath |
| Manifest-fájl: entitás elérési útja | Az entitás mappájának elérési útja a gyökérkönyvtáron belül | nem | Sztring | entityPath |
| Manifest-fájl: jegyzékfájl neve | A jegyzékfájl neve  | igen, ha a jegyzékfájlt használja | Sztring | manifestName |
| Szűrés utoljára módosítva | Válassza ki a fájlok szűrését az utolsó módosításuk alapján | nem | Időbélyeg | modifiedAfter <br> modifiedBefore | 
| Séma-társított szolgáltatás | A társított szolgáltatás, ahol a corpus található | igen, ha a jegyzékfájlt használja | `'adlsgen2'` vagy `'github'` | corpusStore | 
| Entitás-hivatkozási tároló | A Container Corpus a | igen, ha a manifest és a corpus használata ADLS Gen2 | Sztring | adlsgen2_fileSystem |
| Entitás-hivatkozási adattár | GitHub-adattár neve | igen, ha a GitHubon és a corpuson használja a GitHubot | Sztring | github_repository |
| Entitás hivatkozási ága | GitHub-tárház ág | igen, ha a GitHubon és a corpuson használja a GitHubot | Sztring |  github_branch |
| Corpus-mappa | a corpus gyökerének helye | igen, ha a jegyzékfájlt használja | Sztring | corpusPath |
| Corpus-entitás | Entitás hivatkozásának elérési útja | igen | Sztring | entitás |
| Nem található fájlok engedélyezése | Ha az értéke igaz, a rendszer nem dobja el a hibát, ha nem található fájl | nem | `true` vagy `false` | ignoreNoFilesFound |

![CDM-forrás](media/format-common-data-model/data-flow-source.png)

### <a name="sink-properties"></a>Fogadó tulajdonságai

Az alábbi táblázatban a CDM-fogadó által támogatott tulajdonságok szerepelnek.

| Name | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formátum | A mindig`cdm` | igen | `cdm` | formátumban |
| Gyökér helye: tároló | A CDM-mappa tárolójának neve | igen | Sztring | Fájlrendszer |
| Gyökér helye: mappa elérési útja | A CDM-mappa gyökérmappa helye | igen | Sztring | folderPath |
| Manifest-fájl: entitás elérési útja | Az entitás mappájának elérési útja a gyökérkönyvtáron belül | nem | Sztring | entityPath |
| Manifest-fájl: jegyzékfájl neve | A jegyzékfájl neve  | igen | Sztring | manifestName |
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

![CDM-forrás](media/format-common-data-model/data-flow-sink.png)

## <a name="next-steps"></a>Következő lépések

Forrás- [átalakítás](data-flow-source.md) létrehozása a leképezési adatfolyamban.