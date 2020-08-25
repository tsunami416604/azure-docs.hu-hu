---
title: fájlbefoglalás
description: fájlbefoglalás
services: azure-sentinel
author: yelevin
ms.service: azure-sentinel
ms.topic: include
ms.date: 06/28/2020
ms.author: yelevin
ms.custom: include file
ms.openlocfilehash: 63cb53dc60a718892d4bf86140e7fd51303bd61c
ms.sourcegitcommit: f1b18ade73082f12fa8f62f913255a7d3a7e42d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2020
ms.locfileid: "88761719"
---
### <a name="the-data-model-of-the-schema"></a>A séma adatmodellje

| Mező | Adattípus | Leírás |
| ---- | ---- | ---- |
| **AdditionalData** | dinamikus | Riasztások száma, könyvjelzők száma, Hozzászólások száma, riasztási termékek neve és taktikája |
| **AlertIds** | dinamikus | Riasztások, amelyekből az incidens létrejött |
| **BookmarkIds** | dinamikus | Könyvjelzővel ellátott entitások |
| **Osztályozás** | sztring | Incidens záró besorolása |
| **ClassificationComment** | sztring | Incidens záró besorolási megjegyzése |
| **ClassificationReason** | sztring | Incidens záró besorolásának oka |
| **ClosedTime** | dátum/idő | Időbélyeg (UTC) az incidens utolsó bezárásakor |
| **Megjegyzések** | dinamikus | Incidensek megjegyzései |
| **CreatedTime** | dátum/idő | Az incidens létrehozásának időbélyegzője (UTC) |
| **Leírás** | sztring | Esemény leírása |
| **FirstActivityTime** | dátum/idő | Első esemény időpontja |
| **FirstModifiedTime** | dátum/idő | Az incidens első módosításának időbélyegzője (UTC) |
| **Incidensnév** | sztring | Belső GUID |
| **IncidentNumber** | int |  |
| **IncidentUrl** | sztring | Eseményre mutató hivatkozás |
| **Címkék** | dinamikus | Címkék |
| **LastActivityTime** | dátum/idő | Legutóbbi esemény időpontja |
| **LastModifiedTime** | dátum/idő | Az incidens utolsó módosításának időbélyegzője (UTC) <br>(az aktuális rekord által leírt módosítás) |
| **ModifiedBy** | sztring | Az incidenst módosító felhasználó vagy rendszer |
| **Tulajdonos** | dinamikus |  |
| **RelatedAnalyticRuleIds** | dinamikus | Az incidens riasztásait kiváltó szabályok |
| **Súlyosság** | sztring | Az incidens súlyossága (magas/közepes/alacsony/tájékoztató) |
| **SourceSystem** | sztring | Állandó ("Azure") |
| **Állapot** | sztring |  |
| **TenantId** | sztring |  |
| **TimeGenerated** | dátum/idő | Az aktuális rekord létrehozásának időbélyegzője (UTC) <br>(az incidens módosítása után) |
| **Cím** | sztring | 
| **Típus** | sztring | Állandó ("biztonsági incidens") |
|
