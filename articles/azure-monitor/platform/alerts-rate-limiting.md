---
title: Sms-ek, e-mailek, leküldéses értesítések sebességkorlátozása
description: Ismerje meg, hogy az Azure hogyan korlátozza a lehetséges SMS-ek, e-mailek, Azure App leküldéses vagy webhook-értesítések számát egy műveletcsoportból.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 3/12/2018
ms.subservice: alerts
ms.openlocfilehash: 61e6cc22171815b15b865dd6ed5670bd9c446ead
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114332"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Hang-, SMS-, e-mailek, Azure-alkalmazásleküldéses értesítések és webhook-bejegyzések sebességkorlátozása
A díjkorlátozás az értesítések felfüggesztése, amely akkor következik be, ha túl sok van elküldve egy adott telefonszámra, e-mail címre vagy eszközre. A sebességkorlátozás biztosítja, hogy a riasztások kezelhetők és végrehajthatók legyenek.

A díjkorlát küszöbértékei a következők:

- **SMS**: 5 percenként legfeljebb 1 SMS.
- **Hang**: 5 percenként legfeljebb 1 hanghívás.
- **E-mail**: Nem több, mint 100 e-mailegy óra alatt.
 
  Más intézkedések nem arány korlátozott.

## <a name="rate-limit-rules"></a>Díjkorlát szabályai
- Egy adott telefonszámot vagy e-mailt a sebesség korlátozott, ha több üzenetet kap, mint a küszöbérték lehetővé teszi.
- A telefonszám vagy e-mail számos előfizetésben része lehet a műveletcsoportoknak. A díjkorlátozás minden előfizetésre vonatkozik. A küszöbérték elérésekor érvényes, még akkor is, ha az üzenetek több előfizetésből érkeznek.
- Ha egy e-mail cím korlátozott, a rendszer további értesítést küld a sebességkorlátozás közlésére. Az e-mail kimondja, hogy mikor jár le a sebességkorlátozás.

## <a name="next-steps"></a>További lépések ##
* További információ az [SMS-riasztások viselkedéséről.](alerts-sms-behavior.md)
* Áttekintést [kaphat a tevékenységnapló-riasztásokról,](alerts-overview.md)és megtudhatja, hogyan fogadhat értesítéseket.  
* További információ arról, hogyan konfigurálhatja a [riasztásokat a szolgáltatás állapotáról szóló értesítés feladásakor.](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)

