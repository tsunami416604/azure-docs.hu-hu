---
title: Értékelje az SMS, e-mailek, Azure-alkalmazások leküldéses értesítései és webhookokkal korlátozása
description: Ismerje meg, hogyan Azure lehetséges SMS, az e-mailek, az Azure App leküldéses vagy webhook értesítéseket művelet csoportból számának korlátozása.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/12/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 1acea47638c75971bad62f28dcd7e96823d84c1d
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262956"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Értékelje a bejegyzéseket a hang, SMS, e-mailek, Azure-alkalmazások leküldéses értesítései és webhook korlátozása
Az értesítések, amelyek akkor fordul elő, ha túl sok küld egy adott telefonszám, az e-mail cím vagy az eszköz felfüggesztés sebességkorlátozást. Sebességkorlátozást biztosítja, hogy riasztások kezelhető és hajtható végre.

A sebesség korlátot küszöbértékeit a következők:

 - **SMS**: 1-nél több SMS 5 percenként.
 - **Hangalámondás**: 1-nél több hang hívás 5 percenként.
 - **E-mailek**: egy óra alatt legfeljebb 100 e-maileket.
 
 Egyéb műveletek csak korlátozott sebessége.

## <a name="rate-limit-rules"></a>Sebesség korlát szabályok
- Egy adott telefonszámát vagy e-mail sebessége korlátozott, ha a küszöb által engedélyezettnél több üzenetet kapott.
- Telefonos vagy e-mailek sok előfizetésekhez művelet csoportok része lehet. Minden előfizetésekhez sebességkorlátozást vonatkozik. Amint a küszöbérték elérésekor vonatkozik, akkor is, ha több előfizetéssel az üzenetküldés.
- Amikor egy e-mail címet sebessége korlátozott, egy további értesítést küld a sebesség korlátozása kommunikációhoz. A e-mailek állapotokat, ha a lejár a sebesség korlátozása.

## <a name="next-steps"></a>További lépések ##
* További információ [SMS riasztási viselkedés](monitoring-sms-alert-behavior.md).
* Első egy [tevékenység napló riasztások áttekintése](monitoring-overview-alerts.md), és megtudhatja, hogyan szeretné megkapni a riasztásokat.  
* Megtudhatja, hogyan [riasztások konfigurálása, ha az állapotfigyelő szolgáltatáshoz értesítést visszaküldi](monitoring-activity-log-alerts-on-service-notifications.md).
