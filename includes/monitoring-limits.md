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
ms.openlocfilehash: 29550581bbc395126d820d93b608e14ea318e268
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56653639"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Automatikus méretezési beállítások |100 régiónként és előfizetésenként | ugyanaz, mint az alapértelmezett |
| Metrikákhoz kapcsolódó riasztások (klasszikus) |100 aktív riasztási szabályok száma előfizetésenként | Telefonos támogatás |
| Metrikákhoz kapcsolódó riasztások |100 aktív riasztási szabályok száma előfizetésenként | Telefonos támogatás |
| Műveletcsoportok |előfizetésenként 2000 Műveletcsoportok | Telefonos támogatás |

**A művelet adott Csoportkorlát**
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Az Azure app-leküldés | 10 azure app-műveletek száma műveletcsoportot | Telefonos támogatás |
| E-mail | a műveletcsoport 1000 e-mail-műveletek. További tájékoztatás a [sebessége korlátozza az információk](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Telefonos támogatás |
| ITSM | Műveletcsoport 10 ITSM-műveletek | Telefonos támogatás | 
| Logikai alkalmazás | A műveletcsoport 10 logic App-műveletek | Telefonos támogatás |
| Forgatókönyv | A műveletcsoport 10 Runbook-műveletek | Telefonos támogatás |
| SMS | Műveletcsoport 10 SMS műveletek. További tájékoztatás a [sebessége korlátozza az információk](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Telefonos támogatás |
| Hang | Műveletcsoport 10 hangalapú műveletek. További tájékoztatás a [sebessége korlátozza az információk](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Telefonos támogatás |
| Webhook | 10 Webhook-műveletek műveletcsoport. Elérhető egyéb korlátok [művelet információkat](../articles/azure-monitor/platform/action-groups.md#action-specific-information).  | Telefonos támogatás |
