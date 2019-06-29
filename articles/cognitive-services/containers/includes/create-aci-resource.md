---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozzon létre egy azure container instance (ACI) erőforrást.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: b24a78873c3220b969fc548d2553e465e80773cd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455075"
---
## <a name="create-an-azure-container-instance-aci-resource"></a>Hozzon létre egy Azure Container Instance (ACI) erőforrást

1. Nyissa meg a [létrehozás](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) Container Instances lapot.

2. Az a **alapjai** lapra, adja meg a következő adatokat:

    |Beállítás|Érték|
    |--|--|
    |Előfizetés|Válassza ki előfizetését.|
    |Erőforráscsoport|Válassza ki az elérhető erőforráscsoportot vagy hozzon létre például egy új `cognitive-services`.|
    |Tárolónév|Írjon be egy nevet, például `cognitive-container-instance`. Ez a név alacsonyabb nagybetűs kell lennie.|
    |Location|Válassza ki a régiót, üzembe helyezéshez.|
    |Lemezkép típusa|`Public`|
    |Kép neve|Adja meg a Cognitive Services-tároló helyét. Ez lehet ugyanazon a helyen használt a `docker pull` parancs _például_: <br>`mcr.microsoft.com/azure-cognitive-services/sentiment`|
    |Operációs rendszer típusa|`Linux`|
    |Size|A javasolt javaslatokat az adott a Cognitive Services-tároló méretének módosítása.:<br>2 mag<br>4 GB

3. Az a **hálózatkezelés** lapra, adja meg a következő adatokat:

    |Beállítás|Érték|
    |--|--|
    |Portok|A TCP-port beállítása `5000`. Tesz elérhetővé a tárolót 5000-es porton.|

4. Az a **speciális** lapra, adja meg a következő adatokat a tároló áthaladását [kötelező számlázási beállítások](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers#billing-arguments) az ACI-erőforráshoz:

    |Speciális lap kulcs|Speciális lap érték|
    |--|--|
    |`apikey`|Átmásolja a **kulcsok** a Text Analytics erőforrás lapján. Egy 32 alfanumerikus karakterből álló karakterlánc szóközöket vagy kötőjeleket, nélkül `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Átmásolja a **áttekintése** a Text Analytics erőforrás lapján. Például: `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |`eula`|`accept`|

1. Kattintson a **ellenőrzés és létrehozás**
1. Kattintson az ellenőrzés elvégzése után **létrehozás** a létrehozási folyamat befejezéséhez.
1. Az erőforrás sikeres telepítést követően készen áll a használatra