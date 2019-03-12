---
title: Díjszabás és korlátok – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: További információ a korlátok és kvóták a Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: anroth
ms.openlocfilehash: 58109e17ed33e6af8dedf3ed8c1cc9ddf546a05e
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588669"
---
# <a name="pricing-and-limits"></a>Díjszabás és korlátok

Nincsenek kulcsok a Custom Vision Service mindhárom szintet. Korlátozott próbaverzió Projekt erőforrások csatolja a Custom Vision bejelentkezéséhez (azt jelenti, egy Azure Active Directory-fiókot vagy MSA-fiók). A szolgáltatás rövid kísérletek használandó szolgálnak. Iratkozzon fel a F0 (ingyenes) vagy S0 (standard) előfizetés az Azure Portalon keresztül. Tekintse meg a megfelelő [Cognitive Services díjszabási oldalát](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) részletek a díjszabásról és a tranzakciók.

Korai ingyenes előzetes verzióban az Azure előzetes verzióira (2018. március 1) bevezetése előtt létrehozott fiókok megőrzi a korábbi kvóták korlátozott kísérletekhez.

Betanító kép projektenként, és projektenként címkék száma várhatóan S0 projektek idővel megnövekszik.

||**Korlátozott próbaverzió**|**F0**|**S0**|
|-----|-----|-----|-----|
|Projektek|2|2|100|
|Betanító kép projektenként, besorolási|5000|5000|50,000|
|Betanító kép projektenként, Objektumdetektálás|5000|5000|10,000|
|Előrejelzés / hónap|10,000 |10,000|Korlátlan|
|Címkék / project|50|50|250|
|Az ismétlések |10|10|10|
|Minimális megcímkézett képeket egy címke, a besorolás (több mint 50 ajánlott) |5|5|5|
|Minimális megcímkézett képeket egy címke, Objektumdetektálás (több mint 50 ajánlott)|15|15|15|
|Mennyi ideig előrejelzési rendszerképek tárolása|30 nap|30 nap|30 nap|
|[Előrejelzési](https://go.microsoft.com/fwlink/?linkid=865445) műveletek a storage (tranzakció / másodperc)|2|2|10|
|[Előrejelzési](https://go.microsoft.com/fwlink/?linkid=865445) nélküli storage (tranzakció / másodperc)|2|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (API-hívások / másodperc)|2|2|10|
|[Más API-hívások](https://go.microsoft.com/fwlink/?linkid=865446) (tranzakció / másodperc)|10|10|10|
|Maximális képméret (képzési képfeltöltéshez) |6 MB|6 MB|6 MB|
|Maximális képméret (előrejelzés)|4 MB|4 MB|4 MB|


