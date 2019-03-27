---
title: Korlátok és kvóták – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: További információ a korlátok és kvóták a Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 85abc4a50710629d3485d05115698e59a93fc96e
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472440"
---
# <a name="limits-and-quotas"></a>Korlátok és kvóták

Nincsenek kulcsok a Custom Vision service a két szinten. Iratkozzon fel a F0 (ingyenes) vagy S0 (standard) előfizetés az Azure Portalon keresztül. Tekintse meg a megfelelő [Cognitive Services díjszabási oldalát](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) részletek a díjszabásról és a tranzakciók.

Betanító kép projektenként, és projektenként címkék száma várhatóan S0 projektek idővel megnövekszik.

||**F0**|**S0**|
|-----|-----|-----|
|Projektek|2|100|
|Betanító kép projektenként |5000|100 000|
|Előrejelzés / hónap|10,000 |Korlátlan|
|Címkék / project|50|500|
|Az ismétlések |10|10|
|Min megcímkézett képeket egy címke, a besorolás (több mint 50 ajánlott) |5|5|
|Min megcímkézett képeket egy címke, Objektumdetektálás (több mint 50 ajánlott)|15|15|
|Mennyi ideig előrejelzési rendszerképek tárolása|30 nap|30 nap|
|[Előrejelzési](https://go.microsoft.com/fwlink/?linkid=865445) műveletek a storage (tranzakció / másodperc)|2|10|
|[Előrejelzési](https://go.microsoft.com/fwlink/?linkid=865445) nélküli storage (tranzakció / másodperc)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (API-hívások / másodperc)|2|10|
|[Más API-hívások](https://go.microsoft.com/fwlink/?linkid=865446) (tranzakció / másodperc)|10|10|
|Maximális képméret (képzési képfeltöltéshez) |6 MB|6 MB|
|Maximális képméret (előrejelzés)|4 MB|4 MB|
