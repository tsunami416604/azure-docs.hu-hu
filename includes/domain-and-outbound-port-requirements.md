---
title: fájl belefoglalása
description: fájl belefoglalása
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: f634ebb4d8c3f443b5d843b0093c955b384a9f5f
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285614"
---
| Tartománynevek                  | Kimenő portok | Leírás                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | A saját üzemeltetésű integrációs modul számára szükséges a Data Factory adatátviteli szolgáltatásaihoz való csatlakozáshoz. |
| `*.frontend.clouddatahub.net` | 443            | A saját üzemeltetésű integrációs modul számára szükséges a Data Factory szolgáltatáshoz való csatlakozáshoz. |
| `download.microsoft.com`    | 443            | A frissítések letöltéséhez a saját üzemeltetésű Integration Runtime szükséges. Ha letiltotta az automatikus frissítést, kihagyhatja ezt. |
| `*.core.windows.net`          | 443            | A saját üzemeltetésű integrációs modul használja az Azure Storage-fiókhoz való csatlakozásra az [előkészített másolási](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) szolgáltatás használatakor. |
| `*.database.windows.net`      | 1433           | Választható Azure SQL Database vagy Azure SQL Data Warehouse másolásakor szükséges. Az előkészített másolási szolgáltatással az 1433-as port megnyitása nélkül másolhatja az Adatmásolást Azure SQL Databaseba vagy Azure SQL Data Warehouseba. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Választható A vagy a rendszerből Azure Data Lake Store történő másoláskor szükséges. |
