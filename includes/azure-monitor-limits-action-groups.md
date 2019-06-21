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
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305289"
---
| Resource | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Az Azure app-leküldés | 10 azure app-műveletek száma műveletcsoport. | Az ügyfélszolgálat. |
| E-mail | Műveletcsoport 1000 e-mail műveletek.<br>Legfeljebb 100 e-mailek egy órán belül.<br>További tájékoztatás a [sebessége korlátozza az információk](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Az ügyfélszolgálat. |
| ITSM | 10 ITSM-műveletek műveletcsoport. | Az ügyfélszolgálat. | 
| Logikai alkalmazás | 10 logic app-műveletek a műveletcsoport. | Az ügyfélszolgálat. |
| Forgatókönyv | Műveletcsoport 10 runbook műveletek. | Az ügyfélszolgálat. |
| SMS | Műveletcsoport 10 SMS műveletek.<br>1-nél több SMS-üzenet 5 percenként.<br>További tájékoztatás a [sebessége korlátozza az információk](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Az ügyfélszolgálat. |
| Hang | Műveletcsoport 10 hangalapú műveletek.<br>Legfeljebb 1 hanghívás 5 percenként.<br>További tájékoztatás a [sebessége korlátozza az információk](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Az ügyfélszolgálat. |
| Webhook | 10 webhook-műveletek műveletcsoport.  Webhook-hívások maximális száma érték az 1500 előfizetésenként percenként. Egyéb korlátok esetén érhető el [művelet jellemző](../articles/azure-monitor/platform/action-groups.md#action-specific-information).  | Az ügyfélszolgálat. |
