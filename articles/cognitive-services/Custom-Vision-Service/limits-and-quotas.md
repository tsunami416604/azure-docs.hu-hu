---
title: Korlátok és kvóták – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: További információ a korlátok és kvóták a Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: anroth
ms.openlocfilehash: 53884cfc2683832d9df2b215c92b6b683ba29d2c
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363463"
---
# <a name="limits-and-quotas"></a>Korlátok és kvóták

Nincsenek kulcsok a Custom Vision Service mindhárom szintet. Az Azure Portalon keresztül kapjuk f0 és S0-erőforrásokat. Díjszabás és a tranzakciók definíciók részletek találhatók a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/).  S0 projektek F0 projektek frissíthetők.

Korlátozott próbaverzió Projekt erőforrások vannak csatolva a Custom Vision bejelentkezés (, egy AAD-fiók vagy MSA-fiókot.) A szolgáltatás rövid kísérletek használandó szolgálnak.  Korai ingyenes előzetes verzióban az Azure előzetes verzióira (2018. március 1.) bevezetése előtt létrehozott fiókok megőrzi a korábbi kvóták korlátozott kísérletekhez. 

||**Korlátozott próbaverzió**|**F0 (Azure)**|**S0 (Azure)**|
|-----|-----|-----|-----|
|Projektek|2|2|100|
|Betanító kép projektenként|5000|5000|50,000|
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
|Maximális képméret (képzési képfeltöltéshez) |6MB|6MB|6MB|
|Maximális képméret (előrejelzés)|4MB|4MB|4MB|

A korlátozások *# betanító kép projektenként* és *# címkék és a projekt* növelhető S0 projektek idővel várhatóan. 
