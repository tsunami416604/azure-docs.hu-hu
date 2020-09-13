---
title: fájlbefoglalás
description: fájlbefoglalás
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 0b2831321c9f4d855a19605c1ce5ace9fa11c88b
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596114"
---
| Tartománynevek                  | Kimenő portok | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | A saját üzemeltetésű integrációs modul számára szükséges a Azure Data Factory adatátviteli szolgáltatásaihoz való csatlakozáshoz. |
| `{datafactory}.{region}.datafactory.azure.net`<br> vagy `*.frontend.clouddatahub.net` | 443            | A saját üzemeltetésű integrációs modul számára szükséges a Data Factory szolgáltatáshoz való csatlakozáshoz. <br>Új létrehozott Data Factory esetén keresse meg a teljes tartománynevet a saját üzemeltetésű Integration Runtime-kulcsból, amely a következő formátumban van: {DataFactory}. {Region}. DataFactory. Azure. net. Ha a régi adatelőállító nem látja a teljes tartománynevet a saját üzemeltetésű integrációs kulcsban, használja a *. frontend.clouddatahub.net helyet. |
| `download.microsoft.com`    | 443            | A frissítések letöltéséhez a saját üzemeltetésű Integration Runtime szükséges. Ha letiltotta az automatikus frissítést, akkor kihagyhatja a tartomány konfigurálását. |
| `*.core.windows.net`          | 443            | A saját üzemeltetésű integrációs modul használja az Azure Storage-fiókhoz való csatlakozásra az [előkészített másolási](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) szolgáltatás használatakor. |
| `*.database.windows.net`      | 1433           | Csak akkor szükséges, ha Azure SQL Database vagy az Azure szinapszis Analytics szolgáltatásból másol, vagy nem kötelező. Az előkészített másolási szolgáltatással az 1433-as port megnyitása nélkül másolhatja az adatSQL Database vagy a szinapszis Analytics szolgáltatásba. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Csak akkor szükséges, ha a vagy a rendszerből másol Azure Data Lake Store és opcionálisan. |
