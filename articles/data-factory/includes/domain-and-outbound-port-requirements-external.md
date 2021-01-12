---
title: fájl belefoglalása
description: fájl belefoglalása
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 4ef7bc7ed1ab255bffdfec10c07bc040019d9f1e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121621"
---
| Tartománynevek                  | Kimenő portok | Leírás                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | A saját üzemeltetésű integrációs modul használja az Azure Storage-fiókhoz való csatlakozásra az előkészített másolási szolgáltatás használatakor. |
| `*.database.windows.net`      | 1433           | Csak akkor szükséges, ha Azure SQL Database vagy az Azure szinapszis Analytics szolgáltatásból másol, vagy nem kötelező. A szakaszos másolási szolgáltatással az 1433-as port megnyitása nélkül másolhat az adatSQL Database vagy az Azure szinapszis Analytics szolgáltatásba. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Csak akkor szükséges, ha a vagy a rendszerből másol Azure Data Lake Store és opcionálisan. |
