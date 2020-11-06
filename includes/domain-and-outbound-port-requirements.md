---
title: fájl belefoglalása
description: fájl belefoglalása
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 79a8bc73f416c8d10d83e7ad94a727094f072b00
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331707"
---
| Tartománynevek                  | Kimenő portok | Leírás                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | A saját üzemeltetésű integrációs modul szükséges az interaktív létrehozáshoz. |
| `{datafactory}.{region}.datafactory.azure.net`<br> vagy `*.frontend.clouddatahub.net` | 443            | A saját üzemeltetésű integrációs modul számára szükséges a Data Factory szolgáltatáshoz való csatlakozáshoz. <br>Új létrehozott Data Factory esetén keresse meg a teljes tartománynevet a saját üzemeltetésű Integration Runtime-kulcsból, amely a következő formátumban van: {DataFactory}. {Region}. DataFactory. Azure. net. Ha a régi adatelőállító nem látja a teljes tartománynevet a saját üzemeltetésű integrációs kulcsban, használja a *. frontend.clouddatahub.net helyet. |
| `download.microsoft.com`    | 443            | A frissítések letöltéséhez a saját üzemeltetésű Integration Runtime szükséges. Ha letiltotta az automatikus frissítést, akkor kihagyhatja a tartomány konfigurálását. |
| `*.core.windows.net`          | 443            | A saját üzemeltetésű integrációs modul használja az Azure Storage-fiókhoz való csatlakozásra az [előkészített másolási](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) szolgáltatás használatakor. |
| `*.database.windows.net`      | 1433           | Csak akkor szükséges, ha Azure SQL Database vagy az Azure szinapszis Analytics szolgáltatásból másol, vagy nem kötelező. Az előkészített másolási szolgáltatással az 1433-as port megnyitása nélkül másolhatja az adatSQL Database vagy a szinapszis Analytics szolgáltatásba. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Csak akkor szükséges, ha a vagy a rendszerből másol Azure Data Lake Store és opcionálisan. |
