---
title: Integráció az Azure-szabályzatokkal – Azure Batch | Microsoft Docs
description: ''
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/24/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 9a306457f838fc79d12be3217d96cc8fb25c9c1b
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77618372"
---
# <a name="integration-with-azure-policy"></a>Integráció a Azure Policy

Azure Policy egy Azure-beli szolgáltatás, amellyel olyan házirendeket hozhat létre, rendelhet hozzá és kezelhet, amelyek az erőforrásokra vonatkozó szabályokat kényszerítenek, így biztosítva, hogy az erőforrások megfeleljenek a vállalati szabványoknak és a szolgáltatói szerződéseknek. Azure Policy kiértékeli az erőforrásokat, és nem felel meg a hozzárendelt szabályzatoknak. 

A Azure Batch két beépített bővítménnyel segíti a szabályzatok megfelelőségének kezelését. 

|**Név**...|   **Leírás**|**Hatás (ok)**|  **Verzió**|    **Forrás**
|----------------|----------|----------|----------------|---------------|
|A Batch-fiókokban lévő diagnosztikai naplókat engedélyezni kell|   Diagnosztikai naplók engedélyezésének naplózása. Ez lehetővé teszi, hogy újból létrehozza a vizsgálati célokra használandó tevékenység-nyomvonalat; biztonsági incidens bekövetkezésekor vagy a hálózat biztonsága esetén|AuditIfNotExists, letiltva|  2.0.0|  GitHub|
|A metrika riasztási szabályait a Batch-fiókokon kell konfigurálni| A metrika riasztási szabályainak konfigurálása a Batch-fiókban a szükséges metrika engedélyezéséhez|   AuditIfNotExists, letiltva| 1.0.0|  GitHub|

A szabályzat-definíciók azokat a feltételeket írják le, amelyeknek teljesülniük kell. A feltétel összehasonlítja az erőforrás tulajdonságot egy szükséges értékkel. Az erőforrás-tulajdonságok mezői előre definiált aliasokkal érhetők el. Tulajdonság-Aliasok használatával férhet hozzá az erőforrástípus adott tulajdonságaihoz. Az aliasok lehetővé teszi korlátozása, milyen értékeket, vagy a feltételek az erőforrás-tulajdonságok megengedettek. Minden egyes alias képez le egy adott erőforrás típusát különböző API-verzióit szereplő elérési utakat. Szabályzat-kiértékelés során a házirendmotor lekérdezi, hogy API-verzió tulajdonság elérési útját.

A Batch számára szükséges erőforrások közé tartoznak a következők: fiók, számítási csomópont, készlet, feladat és feladat. Ezért a tulajdonságok aliasai használatával férhet hozzá az adott erőforrásokhoz. További információ az [aliasokról](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

A Visual Studio Code-hoz készült Azure Policy bővítménnyel ellenőrizheti, hogy ismeri-e az aktuális aliasokat, és tekintse át az erőforrásokat és a szabályzatokat. Ez a Visual Studio Code által támogatott összes platformon telepíthető. Ez a támogatás magában foglalja a Windows, a Linux és a macOS rendszer használatát. Lásd: [telepítési irányelvek](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode).



