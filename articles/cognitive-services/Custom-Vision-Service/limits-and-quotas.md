---
title: Korlátozásai és a kvóták egyéni stratégiai szolgáltatás - Azure kognitív szolgáltatások |} Microsoft Docs
description: Tudnivalók a korlátozások és a kvóta az Azure Cognitives szolgáltatások egyéni stratégiai szolgáltatás.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 03/16/2018
ms.author: anroth
ms.openlocfilehash: 44666d5d7f2a51e4017c704205d21b1f6d06908c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349319"
---
# <a name="limits-and-quotas"></a>Korlátok és kvóták

Nincsenek egyéni stratégiai szolgáltatás kulcsok három réteg. F0 és S0 erőforrások akkor kapja meg az Azure-portálon. Tarifa- és tranzakciók definíciók részletek találhatók a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/).  F0 projektek S0 projektek frissíthető.

Korlátozott próbaverzió Projekt erőforrások csatolt egyéni stratégiai bejelentkezési adatait (Ez azt jelenti, hogy egy AAD-fiókba vagy msa-t a fiókot.) A szolgáltatás rövid kísérletek használandó szolgálnak.  Korai szabad előzetes (2018. március 1.) az Azure előzetesekre bevezetése előtt létrehozott fiókot megőrzi a korábbi kvóták korlátozott kísérletekhez. 

||**Korlátozott próbaverzió**|**F0 (Azure)**|**S0 (Azure)**|
|-----|-----|-----|-----|
|Projektek|2|2|100|
|Képzési kép / projekt|5000|5000|50,000|
|Előrejelzés / hónap|10,000 |10,000|Korlátlan|
|Címkék / project|50|50|250|
|Az ismétlés |10|10|10|
|Minimális feliratú kép / címke, a besorolás (50 + ajánlott) |5|5|5|
|Minimális feliratú kép / címkében objektum észlelési (50 + ajánlott)|15|15|15|
|Mennyi ideig előrejelzés lemezképek tárolása|30 nap|30 nap|30 nap|
|[Előrejelzés](https://go.microsoft.com/fwlink/?linkid=865445) műveletek tároló (tranzakció / másodperc)|2|2|10|
|[Előrejelzés](https://go.microsoft.com/fwlink/?linkid=865445) műveletek (tranzakció / másodperc) tároló nélkül|2|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (API hívásokat / másodperc)|2|2|10|
|[Más API-hívások](https://go.microsoft.com/fwlink/?linkid=865446) (tranzakció / másodperc)|10|10|10|
|Maximális képméret (képzési lemezkép feltöltése) |6MB|6MB|6MB|
|Maximális képméret (előrejelzés)|4MB|4MB|4MB|

Korlátozások a *# képzési kép / project* és *# címkék és a projekt* várhatóan S0 projektek időbeli növelni. 
