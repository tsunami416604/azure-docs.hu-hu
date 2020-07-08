---
title: fájlbefoglalás
description: fájlbefoglalás
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 2922a1fb92f3df07429d3463b8bf639f336776af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67305289"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Azure-alkalmazás leküldése | műveleti csoportonként 10 Azure-alkalmazás műveletei. | Hívja a támogatási szolgálatot. |
| E-mail | 1 000 e-mail művelet egy műveleti csoportban.<br>Legfeljebb 100 e-mail egy órán belül.<br>Tekintse meg a [díjszabási információkat](../articles/azure-monitor/platform/alerts-rate-limiting.md)is. | Hívja a támogatási szolgálatot. |
| ITSM | 10 ITSM művelet egy műveleti csoportban. | Hívja a támogatási szolgálatot. | 
| Logikai alkalmazás | 10 logikai alkalmazás műveletei egy műveleti csoportban. | Hívja a támogatási szolgálatot. |
| Forgatókönyv | 10 runbook művelet egy műveleti csoportban. | Hívja a támogatási szolgálatot. |
| SMS | 10 SMS művelet egy műveleti csoportban.<br>5 percenként legfeljebb 1 SMS-üzenet jelenik meg.<br>Tekintse meg a [díjszabási információkat](../articles/azure-monitor/platform/alerts-rate-limiting.md)is. | Hívja a támogatási szolgálatot. |
| Hang | 10 hangművelet egy műveleti csoportban.<br>5 percenként legfeljebb 1 hanghívás.<br>Tekintse meg a [díjszabási információkat](../articles/azure-monitor/platform/alerts-rate-limiting.md)is. | Hívja a támogatási szolgálatot. |
| Webhook | 10 webhook-művelet egy műveleti csoportban.  A webhook-hívások maximális száma 1500 percenként, előfizetésben. A további korlátozások a [Művelet-specifikus információkon](../articles/azure-monitor/platform/action-groups.md#action-specific-information)érhetők el.  | Hívja a támogatási szolgálatot. |
