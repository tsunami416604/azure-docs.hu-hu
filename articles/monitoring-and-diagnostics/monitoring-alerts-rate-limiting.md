---
title: Értékelje, korlátozza az SMS, e-mailek, az Azure-alkalmazások leküldéses értesítései és webhookok
description: Ismerje meg, hogyan Azure lehetséges SMS, az e-mailek, az Azure App leküldéses vagy webhook értesítéseket a műveletcsoport számát korlátozza.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/12/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 7ac5890769809e3f6f8afe320922facc04195042
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53166013"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Értékelje a bejegyzések a hangalapú, SMS, e-mailek, az Azure-alkalmazások leküldéses értesítései és webhook korlátozása
Az értesítések, amelyek akkor fordul elő, ha túl sok küld egy adott telefonszám, e-mail címét vagy eszköz felfüggesztés sebességkorlátozással. Sebességkorlátozással biztosítja, hogy riasztások kezelhető, és a gyakorlatban is használható.

A sebesség korlát küszöbértékek a következők:

 - **SMS**: Az SMS 5 percenként legfeljebb 1.
 - **Beszédfelismerési**: Legfeljebb 1 hanghívás 5 percenként.
 - **e-mailek**: Legfeljebb 100 e-mailek egy órán belül.
 
 Egyéb műveletek nem korlátozott sebességű el.

## <a name="rate-limit-rules"></a>Forgalmi korlát szabályok
- Egy adott telefonszámot vagy e-mail sebessége korlátozott, ha az a küszöbérték engedélyezettnél több üzenetet kap.
- Telefonos vagy e-mailek számos előfizetésekben Műveletcsoportok része lehet. Az összes előfizetés arány korlátozása vonatkozik. Érvényes, amint az küszöbérték elérése esetén akkor is, ha több előfizetés üzeneteket küldi.
- Ha egy e-mail-cím sebessége korlátozott, egy további értesítést küld a sebességkorlátozás kommunikációhoz. Az e-mailek államok, ha lejár a sebességkorlátozás.

## <a name="next-steps"></a>További lépések ##
* Tudjon meg többet [SMS-riasztás viselkedése](monitoring-sms-alert-behavior.md).
* Get- [tevékenységnapló-riasztások áttekintése](monitoring-overview-alerts.md), és a riasztások fogadása.  
* Ismerje meg, hogyan [riasztások konfigurálása, ha a szolgáltatás állapotával kapcsolatos értesítés közzétételekor](../azure-monitor/platform/alerts-activity-log-service-notifications.md).
