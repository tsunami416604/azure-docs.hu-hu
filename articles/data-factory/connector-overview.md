---
title: Azure Data Factory-összekötő áttekintése
description: Ismerje meg Data Factory támogatott összekötőit.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: a729d470cccd4121523c767ada9077a51361c061
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181976"
---
# <a name="azure-data-factory-connector-overview"></a>Azure Data Factory-összekötő áttekintése

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Azure Data Factory a következő adattárakat és formátumokat támogatja a másolás, az adatfolyam, a keresés, a metaadatok lekérése és a törlés tevékenységek használatával. Az egyes adattárokra kattintva megtudhatja a támogatott funkciókat és a megfelelő konfigurációkat a részletek között.

## <a name="supported-data-stores"></a>Támogatott adattárak

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="integrate-with-more-data-stores"></a>Integráció több adattárral

Azure Data Factory az adattárak szélesebb körét érheti el, mint a fent említett lista. Ha olyan adattárból/adattárba kell áthelyeznie az adattárat, amely nem szerepel a Azure Data Factory beépített összekötő listáján, az alábbiakban néhány bővíthető lehetőség közül választhat:
- Az adatbázis és az adatraktár esetében általában talál egy megfelelő ODBC-illesztőt, amellyel az [általános ODBC-összekötőt](connector-odbc.md)használhatja.
- SaaS-alkalmazások esetén:
    - Ha REST API-kat biztosít, használhatja az [általános Rest-összekötőt](connector-rest.md).
    - Ha rendelkezik OData-adatcsatornával, használhatja az [általános OData-összekötőt](connector-odata.md).
    - Ha SOAP API-kat biztosít, használhatja az [általános http-összekötőt](connector-http.md).
    - Ha ODBC-illesztővel rendelkezik, használhatja az [általános ODBC-összekötőt](connector-odbc.md).
- Mások számára ellenőrizze, hogy tud-e adatokkal betölteni vagy közzétenni minden olyan adatforrást, mint az ADF által támogatott adattárak, például az Azure Blob/file/FTP/SFTP/etc, majd hagyja, hogy az ADF legyen elérhető. Az egyéni betöltési mechanizmust az [Azure Function](control-flow-azure-function-activity.md), az [egyéni tevékenység](transform-data-using-dotnet-custom-activity.md), a [Databricks](transform-data-databricks-notebook.md) / [HDInsight](transform-data-using-hadoop-hive.md), a [webes tevékenységek](control-flow-web-activity.md)stb. használatával hívhatja meg.

## <a name="supported-file-formats"></a>Támogatott fájlformátumok

A Azure Data Factory a következő fájlformátumokat támogatja. A formátum-alapú beállításokról a cikkekben talál további információt.

- [Avro formátum](format-avro.md)
- [Bináris formátum](format-binary.md)
- [Common Data Model formátum](format-common-data-model.md)
- [Tagolt szövegformátum](format-delimited-text.md)
- [Delta formátum](format-delta.md)
- [Excel formátum](format-excel.md)
- [JSON formátum](format-json.md)
- [ORC formátum](format-orc.md)
- [Parquet formátum](format-parquet.md)
- [XML formátum](format-xml.md)

## <a name="next-steps"></a>További lépések

- [Másolási tevékenység](copy-activity-overview.md)
- [Leképezési adatfolyam](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)
