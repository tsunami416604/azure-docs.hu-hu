---
title: Integráció az Azure-szabályzatokkal
description: Azure Policy egy Azure-beli szolgáltatás, amellyel olyan házirendeket hozhat létre, rendelhet hozzá és kezelhet, amelyek az erőforrásokra vonatkozó szabályokat kényszerítenek.
ms.topic: article
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: a160de1277afea026a16f470c8f76cdc2ec1733f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184265"
---
# <a name="integration-with-azure-policy"></a>Integráció a Azure Policy

Azure Policy egy Azure-beli szolgáltatás, amellyel olyan házirendeket hozhat létre, rendelhet hozzá és kezelhet, amelyek az erőforrásokra vonatkozó szabályokat kényszerítenek, így biztosítva, hogy az erőforrások megfeleljenek a vállalati szabványoknak és a szolgáltatói szerződéseknek. Azure Policy kiértékeli az erőforrásokat, és nem felel meg a hozzárendelt szabályzatoknak. 

A Azure Batch két beépített bővítménnyel segíti a szabályzatok megfelelőségének kezelését. 

|**Név**...|   **Leírás**|**Hatás (ok)**|  **Verzió**|    **Forrás**
|----------------|----------|----------|----------------|---------------|
|A Batch-fiókokban lévő diagnosztikai naplókat engedélyezni kell|   Diagnosztikai naplók engedélyezésének naplózása. Ez lehetővé teszi, hogy újból létrehozza a vizsgálati célokra használandó tevékenység-nyomvonalat; biztonsági incidens bekövetkezésekor vagy a hálózat biztonsága esetén|AuditIfNotExists, letiltva|  2.0.0|  GitHub|
|A metrika riasztási szabályait a Batch-fiókokon kell konfigurálni| A metrika riasztási szabályainak konfigurálása a Batch-fiókban a szükséges metrika engedélyezéséhez|   AuditIfNotExists, letiltva| 1.0.0|  GitHub|

A szabályzat-definíciók azokat a feltételeket írják le, amelyeknek teljesülniük kell. A feltétel összehasonlítja az erőforrás tulajdonságot egy szükséges értékkel. Az erőforrás-tulajdonságok mezői előre definiált aliasokkal érhetők el. Tulajdonság-Aliasok használatával férhet hozzá az erőforrástípus adott tulajdonságaihoz. Az aliasok lehetővé teszik annak korlátozását, hogy az adott erőforrás egy tulajdonsága milyen értékeket vagy feltételeket engedélyezzen. Az egyes aliasok egy adott erőforrástípus különböző API-verzióinak elérési útjaira mutatnak. A házirend kiértékelése során a házirend-kezelő beolvassa az adott API-verzióhoz tartozó tulajdonság elérési útját.

A Batch számára szükséges erőforrások közé tartoznak a következők: fiók, számítási csomópont, készlet, feladat és feladat. Ezért a tulajdonságok aliasai használatával férhet hozzá az adott erőforrásokhoz. További információ az [aliasokról](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

A Visual Studio Code-hoz készült Azure Policy bővítménnyel ellenőrizheti, hogy ismeri-e az aktuális aliasokat, és tekintse át az erőforrásokat és a szabályzatokat. Ez a Visual Studio Code által támogatott összes platformon telepíthető. Ez a támogatás magában foglalja a Windows, a Linux és a macOS rendszer használatát. Lásd: [telepítési irányelvek](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode).



