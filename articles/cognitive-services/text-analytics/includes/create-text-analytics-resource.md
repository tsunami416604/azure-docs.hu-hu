---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre egy cognitive services text analytics erőforrás.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 022ffcd806d4d4f89f8a8cf256a541518ea12602
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455096"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Cognitive Services Text Analytics erőforrás létrehozása

1. Jelentkezzen be a [Azure Portalon](https://portal.azure.com)
1. Válassza ki **+ erőforrás létrehozása**, navigáljon a **mesterséges Intelligencia és Machine Learning > Text Analytics**, vagy kattintson a [létrehozás **Szövegelemzés**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Érték|
    |--|--|
    |Name (Név)|Kívánt nevét (2-64 karakter)|
    |Előfizetés|Válassza ki a megfelelő előfizetést|
    |Location|Válasszon ki egy közeli helyet|
    |Tarifacsomag|`S` – a standard díjcsomag|
    |Erőforráscsoport|Válasszon ki egy rendelkezésre álló|

1. Kattintson a **létrehozás** és várja meg a létrehozandó erőforrás. Miután elkészült, a böngésző automatikusan átirányítja az újonnan létrehozott erőforrás-oldalon
1. Adatokat gyűjthet konfigurált `endpoint` és API-kulcs:

    |Portál erőforrás lapján|Beállítás|Érték|
    |--|--|--|
    |**Áttekintés**|Végpont|Másolja a végpont. Hasonló `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |**kulcsok**|API-kulcs|1\. a két kulcs másolása. Egy 32 alfanumerikus karakterből álló karakterlánc szóközöket vagy kötőjeleket, nélkül `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|