---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 2922a1fb92f3df07429d3463b8bf639f336776af
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "67305289"
---
| Resource | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Leküldés Azure-alkalmazásból | műveleti csoportonként 10 Azure-alkalmazás műveletei. | Hívja a támogatási szolgálatot. |
| Email | 1 000 e-mail művelet egy műveleti csoportban.<br>Legfeljebb 100 e-mail egy órán belül.<br>Tekintse meg a [díjszabási információkat](../articles/azure-monitor/platform/alerts-rate-limiting.md)is. | Hívja a támogatási szolgálatot. |
| ITSM | 10 ITSM művelet egy műveleti csoportban. | Hívja a támogatási szolgálatot. | 
| Logikai alkalmazás | 10 logikai alkalmazás műveletei egy műveleti csoportban. | Hívja a támogatási szolgálatot. |
| Runbook | 10 runbook művelet egy műveleti csoportban. | Hívja a támogatási szolgálatot. |
| SMS | 10 SMS művelet egy műveleti csoportban.<br>5 percenként legfeljebb 1 SMS-üzenet jelenik meg.<br>Tekintse meg a [díjszabási információkat](../articles/azure-monitor/platform/alerts-rate-limiting.md)is. | Hívja a támogatási szolgálatot. |
| Hang | 10 hangművelet egy műveleti csoportban.<br>5 percenként legfeljebb 1 hanghívás.<br>Tekintse meg a [díjszabási információkat](../articles/azure-monitor/platform/alerts-rate-limiting.md)is. | Hívja a támogatási szolgálatot. |
| Webhook | 10 webhook-művelet egy műveleti csoportban.  A webhook-hívások maximális száma 1500 percenként, előfizetésben. A további korlátozások a [Művelet-specifikus információkon](../articles/azure-monitor/platform/action-groups.md#action-specific-information)érhetők el.  | Hívja a támogatási szolgálatot. |
