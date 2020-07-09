---
title: fájlbefoglalás
description: fájlbefoglalás
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 6af6795fefb41f1d8f2b56e0aa1fb367fc18cee2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74559304"
---
| Tartománynevek                  | Kimenő portok | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | A saját üzemeltetésű integrációs modul számára szükséges a Azure Data Factory adatátviteli szolgáltatásaihoz való csatlakozáshoz. |
| `*.frontend.clouddatahub.net` | 443            | A saját üzemeltetésű integrációs modul számára szükséges a Data Factory szolgáltatáshoz való csatlakozáshoz. |
| `download.microsoft.com`    | 443            | A frissítések letöltéséhez a saját üzemeltetésű Integration Runtime szükséges. Ha letiltotta az automatikus frissítést, akkor kihagyhatja a tartomány konfigurálását. |
| `*.core.windows.net`          | 443            | A saját üzemeltetésű integrációs modul használja az Azure Storage-fiókhoz való csatlakozásra az [előkészített másolási](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) szolgáltatás használatakor. |
| `*.database.windows.net`      | 1433           | Csak akkor szükséges, ha a vagy a rendszerből másol Azure SQL Database vagy Azure SQL Data Warehouse, és nem kötelező. Az előkészített másolási szolgáltatással az 1433-as port megnyitása nélkül másolhatja az Adatmásolást SQL Databaseba vagy SQL Data Warehouseba. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Csak akkor szükséges, ha a vagy a rendszerből másol Azure Data Lake Store és opcionálisan. |
