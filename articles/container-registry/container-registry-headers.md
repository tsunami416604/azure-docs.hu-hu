---
title: "Azure-tárolót beállításjegyzék adattárak |} Microsoft Docs"
description: "Azure-tároló beállításjegyzék adattárak Docker lemezképek használata"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: cristyg
ms.openlocfilehash: 2090d4c951e2261529bf1b7b361510d5822060a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-container-registry-repositories"></a>Azure-tárolót beállításjegyzék adattárak

Az Azure tároló nyilvántartó szolgáltatások és orchestrators számos kompatibilisek. Könnyebb nyomon követheti a forrás-szolgáltatások és az ügynökök, amelyből ACR használatos, azt elindította a Docker fejlécmező az Docker.config fájl használatával.



## <a name="viewing-repositories-in-the-portal"></a>A portálon megtekintik adattárak

A ACR fejlécek a formátumot követi:
```
X-Meta-Source-Client: <cloud>/<service>/<optionalservicename>
```

* Felhő: Azure, Azure verem, vagy más kormányzati vagy ország-specifikus Azure felhők. Azure verem és a kormányzati jelenleg nem támogatott, bár ez a paraméter lehetővé teszi a jövőbeli támogatási.
* Szolgáltatás: a szolgáltatás nevét.
* Optionalservicename: szolgáltatások subservices, vagy adjon meg egy SKU nem kötelező paraméter (pl.: webalkalmazások megfeleljen az Azure-beli/app-szolgáltatás vagy-webalkalmazások).

Partneri szolgáltatások és orchestrators javasolt, hogy adott térközkaraktert használja a telemetriai adatok számára. Felhasználók is módosíthatja a értéket kapott a fejlécre, ha ezt kívánják.

Az értékek szeretnénk ACR partnerek számára, hogy az "X-Meta-forrás-ügyfél" mező tölti ki az alábbi:

| Szolgáltatás neve              | Fejléc                                |
| ------------------------- | ------------------------------------- |
| Azure Container Service   | Azure/compute/azure-tároló-szolgáltatás |
| Az App Service - webalkalmazásokban    | Azure-beli/app-szolgáltatás vagy-webalkalmazások            |
| Az App Service - Logic Apps alkalmazások  | Azure/app-service/logikai-alkalmazások          |
| Batch                     | Azure/compute/kötegelt                   |
| Felhő konzol             | Azure/felhő-konzol                   |
| Functions                 | Azure/compute/funkciók               |
| Az eszközök internetes hálózata - központ  | Azure-iot-központ                         |
| HDInsight                 | Azure/data/hdinsight                  |
| Jenkins                   | Azure/jenkins                         |
| Machine Learning          | Azure/data/machile-tanulás           |
| Service Fabric            | Azure/compute/service-háló          |
| VSTS                      | Azure/vsts                            |


## <a name="next-steps"></a>Következő lépések
[További tudnivalók nyilvántartó és a támogatott szolgáltatások és orchestrators](container-registry-intro.md)
