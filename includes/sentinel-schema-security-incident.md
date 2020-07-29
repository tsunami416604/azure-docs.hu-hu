---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-sentinel
author: yelevin
ms.service: azure-sentinel
ms.topic: include
ms.date: 06/28/2020
ms.author: yelevin
ms.custom: include file
ms.openlocfilehash: 76020b3c1f28e5b5f6363aef181b76bc93a9613e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294242"
---
### <a name="the-data-model-of-the-schema"></a>A séma adatmodellje

| Mező | Adattípus | Leírás |
| ---- | ---- | ---- |
| **AdditionalData** | dynamic | Riasztások száma, könyvjelzők száma, Hozzászólások száma, riasztási termékek neve és taktikája |
| **AlertIds** | dynamic | Riasztások, amelyekből az incidens létrejött |
| **BookmarkIds** | dynamic | Könyvjelzővel ellátott entitások |
| **Besorolás** | sztring | Incidens záró besorolása |
| **ClassificationComment** | sztring | Incidens záró besorolási megjegyzése |
| **ClassificationReason** | sztring | Incidens záró besorolásának oka |
| **ClosureTime** | dátum/idő | Időbélyeg (UTC) az incidens utolsó bezárásakor |
| **Megjegyzések** | dynamic | Incidensek megjegyzései |
| **CreatedTime** | dátum/idő | Az incidens létrehozásának időbélyegzője (UTC) |
| **Leírás** | sztring | Esemény leírása |
| **FirstActivityTime** | dátum/idő | Első esemény időpontja |
| **FirstModifiedTime** | dátum/idő | Az incidens első módosításának időbélyegzője (UTC) |
| **Incidensnév** | sztring | Belső GUID |
| **IncidentNumber** | int |  |
| **IncidentUrl** | sztring | Eseményre mutató hivatkozás |
| **Címkék** | dynamic | Címkék |
| **LastActivityTime** | dátum/idő | Legutóbbi esemény időpontja |
| **LastModifiedTime** | dátum/idő | Az incidens utolsó módosításának időbélyegzője (UTC) <br>(az aktuális rekord által leírt módosítás) |
| **ModifiedBy** | sztring | Az incidenst módosító felhasználó vagy rendszer |
| **Tulajdonos** | dynamic |  |
| **RelatedAnalyticRuleIds** | dynamic | Az incidens riasztásait kiváltó szabályok |
| **Súlyosság** | sztring | Az incidens súlyossága (magas/közepes/alacsony/tájékoztató) |
| **SourceSystem** | sztring | Állandó ("Azure") |
| **Állapot** | sztring |  |
| **TenantId** | sztring |  |
| **TimeGenerated** | dátum/idő | Az aktuális rekord létrehozásának időbélyegzője (UTC) <br>(az incidens módosítása után) |
| **Cím** | sztring | 
| **Típus** | sztring | Állandó ("biztonsági incidens") |
|
