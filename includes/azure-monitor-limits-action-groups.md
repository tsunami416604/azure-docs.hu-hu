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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67305289"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Azure-alkalmazás leküldéses | 10 Azure-alkalmazásműveletek műveletcsoportonként. | Hívja a támogatást. |
| E-mail | 1000 e-mail művelet egy műveletcsoportban.<br>Nem több, mint 100 e-mail egy óra alatt.<br>Lásd még a [sebességkorlátozó információkat](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Hívja a támogatást. |
| ITSM | 10 ITSM-művelet egy műveletcsoportban. | Hívja a támogatást. | 
| Logikai alkalmazás | 10 logikai alkalmazásművelet egy műveletcsoportban. | Hívja a támogatást. |
| Forgatókönyv | 10 runbook-művelet egy műveletcsoportban. | Hívja a támogatást. |
| SMS | 10 SMS-műveletek egy műveletcsoportban.<br>5 percenként legfeljebb 1 SMS-üzenet.<br>Lásd még a [sebességkorlátozó információkat](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Hívja a támogatást. |
| Hang | 10 hangművelet egy műveletcsoportban.<br>5 percenként legfeljebb 1 hanghívás.<br>Lásd még a [sebességkorlátozó információkat](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Hívja a támogatást. |
| Webhook | 10 webhook-művelet egy műveletcsoportban.  A webhook-hívások maximális száma előfizetésenként percenként 1500. Egyéb korlátozások a [műveletspecifikus információknál](../articles/azure-monitor/platform/action-groups.md#action-specific-information)érhetők el .  | Hívja a támogatást. |
