---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 963e49ea0e5536be0fca6d565b439aef4a08793d
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2021
ms.locfileid: "98605252"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Azure-alkalmazás leküldése | műveleti csoportonként 10 Azure-alkalmazás műveletei. | Ugyanaz, mint az alapértelmezett |
| E-mail | 1 000 e-mail művelet egy műveleti csoportban.<br>Legfeljebb 100 e-mail egy órán belül.<br>Tekintse meg a [díjszabási információkat](../articles/azure-monitor/platform/alerts-rate-limiting.md)is. | Ugyanaz, mint az alapértelmezett |
| ITSM | 10 ITSM művelet egy műveleti csoportban. | Ugyanaz, mint az alapértelmezett | 
| Logikai alkalmazás | 10 logikai alkalmazás műveletei egy műveleti csoportban. | Ugyanaz, mint az alapértelmezett |
| Forgatókönyv | 10 runbook művelet egy műveleti csoportban. | Ugyanaz, mint az alapértelmezett |
| SMS | 10 SMS művelet egy műveleti csoportban.<br>5 percenként legfeljebb 1 SMS-üzenet jelenik meg.<br>Tekintse meg a [díjszabási információkat](../articles/azure-monitor/platform/alerts-rate-limiting.md)is. | Ugyanaz, mint az alapértelmezett |
| Hang | 10 hangművelet egy műveleti csoportban.<br>5 percenként legfeljebb 1 hanghívás.<br>Tekintse meg a [díjszabási információkat](../articles/azure-monitor/platform/alerts-rate-limiting.md)is. | Ugyanaz, mint az alapértelmezett |
| Webhook | 10 webhook-művelet egy műveleti csoportban.  A webhook-hívások maximális száma 1500 percenként, előfizetésben. A további korlátozások a [Művelet-specifikus információkon](../articles/azure-monitor/platform/action-groups.md#action-specific-information)érhetők el.  | Ugyanaz, mint az alapértelmezett |
