---
title: "Korlátozza az SMS, e-mailek és webhookokkal értékelje |} Microsoft Docs"
description: "Ismerje meg, hogyan Azure korlátozza az egy művelet csoport lehetséges SMS, e-mailek vagy webhook értesítések száma."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: bde645624ab1860d19ba18470f55845855a7d1fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="rate-limiting-for-sms-messages-emails-and-webhook-posts"></a>Értékelje a bejegyzéseket az SMS-üzenetek, e-mailek és webhook korlátozása
Az értesítések, amelyek akkor fordul elő, ha túl sok értesítést küld egy adott telefonszámát vagy e-mail címét felfüggesztés sebességkorlátozást. Sebességkorlátozást biztosítja, hogy riasztások kezelhető és hajtható végre.

SMS és az e-mailek szabályait azonosak. A sebesség korlát küszöbértéke:

 - **SMS**: 10 üzenetek egy óra alatt.
 - **E-mailek**: 100 üzenetek egy óra alatt.

## <a name="rate-limit-rules"></a>Sebesség korlát szabályok
- Egy adott telefonszámát vagy e-mail sebessége korlátozott, ha a küszöb által engedélyezettnél több üzenetet kapott.
- Telefonos vagy e-mailek sok előfizetésekhez művelet csoportok része lehet. Minden előfizetésekhez sebességkorlátozást vonatkozik. Amint a küszöbérték elérésekor vonatkozik, akkor is, ha több előfizetéssel az üzenetküldés.  
- Ha telefonos vagy e-mailek sebessége korlátozott, egy további értesítést küld a sebesség korlátozása kommunikációhoz. A értesítési állapotokat, ha a lejár a sebesség korlátozása.

## <a name="rate-limit-of-webhooks"></a>A webhook sávszélesség-korlátjának ##
Nincs webhookok a helyen nincs sebességével.

## <a name="next-steps"></a>Következő lépések ##
* További információ [SMS riasztási viselkedés](monitoring-sms-alert-behavior.md).
* Első egy [tevékenység napló riasztások áttekintése](monitoring-overview-alerts.md), és megtudhatja, hogyan szeretné megkapni a riasztásokat.  
* Megtudhatja, hogyan [riasztások konfigurálása, ha az állapotfigyelő szolgáltatáshoz értesítést visszaküldi](monitoring-activity-log-alerts-on-service-notifications.md).
