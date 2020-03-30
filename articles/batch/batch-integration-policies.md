---
title: Integráció az Azure-szabályzatokkal - Azure Batch | Microsoft dokumentumok
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77618372"
---
# <a name="integration-with-azure-policy"></a>Integráció az Azure-szabályzattal

Az Azure Policy egy olyan azure-szolgáltatás, amelyet olyan szabályzatok létrehozására, hozzárendelésére és kezelésére használhat, amelyek szabályokat kényszerítenek ki az erőforrásokra, hogy ezek az erőforrások továbbra is megfeleljenek a vállalati szabványoknak és a szolgáltatásiszint-szerződéseknek. Az Azure Policy kiértékeli az erőforrásokat, hogy nem felelnek-e meg a hozzárendelt szabályzatok. 

Az Azure Batch két beépített bővítményt is rendelkezik, amelyek segítenek a szabályzatok megfelelőségének kezelésében. 

|**Név**...|   **Leírás**|**Hatás(ok)**|  **Verzió**|    **Forrás**
|----------------|----------|----------|----------------|---------------|
|A Batch-fiókok diagnosztikai naplóit engedélyezni kell|   Diagnosztikai naplók engedélyezése. Ez lehetővé teszi a vizsgálati célokra használt tevékenységnyomvonalak újbóli létrehozását; biztonsági incidens esetén, vagy ha a hálózat biztonsága sérül|AuditIfNotExists, Letiltva|  2.0.0|  GitHub|
|Metrikariasztási szabályokat kell konfigurálni a Batch-fiókokban| Metrikariasztási szabályok naplózási konfigurációjának naplózása a Batch-fiókban a szükséges metrika engedélyezéséhez|   AuditIfNotExists, Letiltva| 1.0.0|  GitHub|

A házirend-definíciók leírják azokat a feltételeket, amelyeknek teljesülniük kell. Egy feltétel összehasonlítja az erőforrás tulajdonságot egy szükséges értékkel. Az erőforrástulajdonság-mezők előre definiált aliasokkal érhetők el. Tulajdonságaliasok használatával férhet hozzá egy erőforrástípus bizonyos tulajdonságaihoz. Az aliasok lehetővé teszik, hogy korlátozza, milyen értékek vagy feltételek engedélyezettek egy erőforrás egy tulajdonságához. Minden alias leképezi az adott erőforrástípus különböző API-verziókelérési útjait. A házirend-kiértékelés során a házirend-motor lekéri az API-verzió tulajdonságelérési útját.

A Batch által igényelt erőforrások a következők: fiók, számítási csomópont, készlet, feladat és feladat. Így a tulajdonságaliasok használatával hozzáférhet az erőforrások adott tulajdonságaihoz. További információ az [aliasokról](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

Győződjön meg arról, hogy ismeri az aktuális aliasokat, és tekintse át az erőforrásokat és szabályzatokat, használja az Azure-szabályzat bővítményt a Visual Studio-kódhoz. Minden olyan platformra telepíthető, amelyet a Visual Studio Kód támogat. Ez a támogatás magában foglalja a Windows, Linux és macOS operációs rendszert. Lásd a [telepítési útmutatót.](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode)



