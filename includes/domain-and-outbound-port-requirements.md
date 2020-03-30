---
title: fájl belefoglalása
description: fájl belefoglalása
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 6af6795fefb41f1d8f2b56e0aa1fb367fc18cee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74559304"
---
| Tartománynevek                  | Kimenő portok | Leírás                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Az Azure Data Factory adatmozgatási szolgáltatásaihoz való csatlakozáshoz az önkiszolgáló integrációs futásidő szükséges. |
| `*.frontend.clouddatahub.net` | 443            | A Data Factory szolgáltatáshoz való csatlakozáshoz a saját üzemeltetésű integrációs futásidő szükséges. |
| `download.microsoft.com`    | 443            | A frissítések letöltéséhez a saját üzemeltetésű integrációs futásidő szükséges. Ha letiltotta az automatikus frissítést, kihagyhatja a tartomány konfigurálását. |
| `*.core.windows.net`          | 443            | A saját üzemeltetésű integrációs futásidejű az Azure storage-fiókhoz való csatlakozáshoz a [szakaszos másolási](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) funkció használatakor használható. |
| `*.database.windows.net`      | 1433           | Csak akkor szükséges, ha az Azure SQL Database vagy az Azure SQL Data Warehouse másolni, és nem kötelező egyébként. A szakaszos másolás funkcióval adatokat másolhat az SQL Database vagy az SQL Data Warehouse rendszerbe az 1433-as port megnyitása nélkül. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Csak akkor szükséges, ha az Azure Data Lake Store-ból vagy az Azure Data Lake Store-ba másol, és más külön ben nem kötelező. |
