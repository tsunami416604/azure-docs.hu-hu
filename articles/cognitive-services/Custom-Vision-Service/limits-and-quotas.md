---
title: Korlátok és kvóták - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Ez a cikk ismerteti a különböző típusú licenckulcsok és a korlátok és kvóták a Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 5f481ebf219eea8ae31e9802144bbf771a24e8b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79081777"
---
# <a name="limits-and-quotas"></a>Korlátok és kvóták

A Custom Vision szolgáltatáshoz két kulcsszint áll. Az Azure Portalon keresztül regisztrálhat egy F0 (ingyenes) vagy S0 (standard) előfizetésre. Tekintse meg a megfelelő [Cognitive Services díjszabási oldalon](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) a tarifa és a tranzakciók részleteit.

Az S0-projektek esetében várhatóan növekedni fog a projektenkénti betanítási képek száma projektenként és címkék száma.

||**F0**|**S0**|
|-----|-----|-----|
|Projektek|2|100|
|Képzési képek projektenként |5000|100 000|
|Előrejelzések / hónap|10,000 |Korlátlan|
|Címkék / projekt|50|500|
|Ismétléseket |10|10|
|Min címkézett képek tag, besorolás szerint (50+ ajánlott) |5|5|
|Min címkézett képek címke, objektumészlelése (50+ ajánlott)|15|15|
|Mennyi ideig tárolt előrejelzési képek|30 nap|30 nap|
|[Előrejelzési](https://go.microsoft.com/fwlink/?linkid=865445) műveletek tárolással (tranzakciók másodpercenként)|2|10|
|[Előrejelzési](https://go.microsoft.com/fwlink/?linkid=865445) műveletek tárolás nélkül (tranzakciók másodpercenként)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (MÁSODPERCENKÉNTI API-hívások)|2|10|
|[Egyéb API-hívások](https://go.microsoft.com/fwlink/?linkid=865446) (tranzakciók másodpercenként)|10|10|
|Elfogadott képtípusok|jpg, png, bmp, gif|jpg, png, bmp, gif|
|M0 kép magassága/szélessége képpontban|256 (lásd a megjegyzést)|256 (lásd a megjegyzést)|
|Kép magasságának/szélességének maximális mérete képpontban|korlátlan|korlátlan|
|Maximális képméret (betanítási kép feltöltése) |6 MB|6 MB|
|Maximális képméret (előrejelzés)|4 MB|4 MB|
|Maximális területek objektumészlelési betanítási képenként|300|300|
|Maximális címkék besorolási képenként|100|100|

> [!NOTE]
> A 256 képpontnál kisebb képek elfogadottak lesznek, de felskálázhatók.
