---
title: Azure Data Factory-összekötő áttekintése
description: Ismerje meg Data Factory támogatott összekötőit.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/08/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: f22b4bec17abe95be2a17d3e0e3c41fab3483fe0
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86224815"
---
# <a name="azure-data-factory-connector-overview"></a>Azure Data Factory-összekötő áttekintése

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Azure Data Factory a következő adattárakat és formátumokat támogatja a másolás, az adatfolyam, a keresés, a metaadatok lekérése és a törlés tevékenységek használatával. Az egyes adattárokra kattintva megtudhatja a támogatott funkciókat és a megfelelő konfigurációkat a részletek között.

## <a name="supported-data-stores"></a>Támogatott adattárak

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="supported-file-formats"></a>Támogatott fájlformátumok

A Azure Data Factory a következő fájlformátumokat támogatja. A formátum-alapú beállításokról a cikkekben talál további információt.

- [Avro formátum](format-avro.md)
- [Bináris formátum](format-binary.md)
- [Common Data Model formátum](format-common-data-model.md)
- [Tagolt szövegformátum](format-delimited-text.md)
- [Különbözeti formátum](format-delta.md)
- [Excel formátum](format-excel.md)
- [JSON formátum](format-json.md)
- [ORC formátum](format-orc.md)
- [Parquet formátum](format-parquet.md)

## <a name="next-steps"></a>További lépések

- [Másolási tevékenység](copy-activity-overview.md)
- [Leképezési adatfolyam](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)
