---
title: fájlbefoglalás
description: fájlbefoglalás
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 610713286e3cb7a084b2e81260797d4cac0ddad7
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91641840"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Azure-alkalmazás leküldése | műveleti csoportonként 10 Azure-alkalmazás műveletei. | Hívja a támogatási szolgálatot. |
| E-mail | 1 000 e-mail művelet egy műveleti csoportban.<br>Legfeljebb 100 e-mail egy órán belül.<br>Tekintse meg a [díjszabási információkat](../articles/azure-monitor/platform/alerts-rate-limiting.md)is. | Ugyanaz, mint az alapértelmezett |
| ITSM | 10 ITSM művelet egy műveleti csoportban. | Hívja a támogatási szolgálatot. | 
| Logikai alkalmazás | 10 logikai alkalmazás műveletei egy műveleti csoportban. | Hívja a támogatási szolgálatot. |
| Forgatókönyv | 10 runbook művelet egy műveleti csoportban. | Hívja a támogatási szolgálatot. |
| SMS | 10 SMS művelet egy műveleti csoportban.<br>5 percenként legfeljebb 1 SMS-üzenet jelenik meg.<br>Tekintse meg a [díjszabási információkat](../articles/azure-monitor/platform/alerts-rate-limiting.md)is. | Hívja a támogatási szolgálatot. |
| Hang | 10 hangművelet egy műveleti csoportban.<br>5 percenként legfeljebb 1 hanghívás.<br>Tekintse meg a [díjszabási információkat](../articles/azure-monitor/platform/alerts-rate-limiting.md)is. | Hívja a támogatási szolgálatot. |
| Webhook | 10 webhook-művelet egy műveleti csoportban.  A webhook-hívások maximális száma 1500 percenként, előfizetésben. A további korlátozások a [Művelet-specifikus információkon](../articles/azure-monitor/platform/action-groups.md#action-specific-information)érhetők el.  | Hívja a támogatási szolgálatot. |
