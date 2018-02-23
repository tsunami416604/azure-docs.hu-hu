---
title: "Értékelje az SMS, e-mailek, Azure-alkalmazások leküldéses értesítései és webhookokkal korlátozása |} Microsoft Docs"
description: "Ismerje meg, hogyan Azure lehetséges SMS, az e-mailek, az Azure App leküldéses vagy webhook értesítéseket művelet csoportból számának korlátozása."
author: dkamstra
manager: chrad
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/16/2018
ms.author: dukek
ms.openlocfilehash: a4f97cc79945d266edd0af577e37fc9da2aa97bf
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="rate-limiting-for-sms-messages-emails-azure-app-push-notifications-and-webhook-posts"></a>Értékelje a bejegyzéseket az SMS-üzenetek, e-mailek, Azure-alkalmazások leküldéses értesítései és webhook korlátozása
Az értesítések, amelyek akkor fordul elő, ha túl sok értesítést küld egy adott telefonszám, az e-mail cím vagy az eszköz felfüggesztés sebességkorlátozást. Sebességkorlátozást biztosítja, hogy riasztások kezelhető és hajtható végre.

A sebesség korlátot küszöbértékeit a következők:

 - **SMS**: 1-nél több SMS 5 percenként.
 - **E-mailek**: egy óra alatt legfeljebb 100 e-maileket.
 
 Egyéb műveletek csak korlátozott sebessége.

## <a name="rate-limit-rules"></a>Sebesség korlát szabályok
- Egy adott telefonszámát vagy e-mail sebessége korlátozott, ha a küszöb által engedélyezettnél több üzenetet kapott.
- Telefonos vagy e-mailek sok előfizetésekhez művelet csoportok része lehet. Minden előfizetésekhez sebességkorlátozást vonatkozik. Amint a küszöbérték elérésekor vonatkozik, akkor is, ha több előfizetéssel az üzenetküldés.
- Amikor egy e-mail címet sebessége korlátozott, egy további értesítést küld a sebesség korlátozása kommunikációhoz. A értesítési állapotokat, ha a lejár a sebesség korlátozása.

## <a name="next-steps"></a>További lépések ##
* További információ [SMS riasztási viselkedés](monitoring-sms-alert-behavior.md).
* Első egy [tevékenység napló riasztások áttekintése](monitoring-overview-alerts.md), és megtudhatja, hogyan szeretné megkapni a riasztásokat.  
* Megtudhatja, hogyan [riasztások konfigurálása, ha az állapotfigyelő szolgáltatáshoz értesítést visszaküldi](monitoring-activity-log-alerts-on-service-notifications.md).
