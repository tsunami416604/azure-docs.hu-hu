---
title: "fájl belefoglalása"
description: "fájl belefoglalása"
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c1e490aacdc9f712c6e186e56fe35fd5872fb575
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
[Az Azure házirendek](/azure/azure-policy/) biztosíthatja összes erőforrás előfizetés súgó felel meg a vállalati szabványoknak. A házirendekkel korlátozza, hogy a központi telepítési beállítások csak erőforrástípusok és jóváhagyott termékváltozatok a költségek csökkentése érdekében. Szabályok és a műveletek megadhatja az erőforrásokhoz, és ezeket a szabályokat automatikus központi telepítés során lépnek érvénybe. Például a telepített erőforrások esetén típusú szabályozhatja. Vagy az engedélyezett helyek erőforrások korlátozhatja. Egyes szabályzatokat művelet megtagadása, és néhány házirend beállítása művelet naplózását.

A házirend olyan kiegészítő a szerepköralapú hozzáférés-vezérlést (RBAC). Szerepalapú felhasználói hozzáférés összpontosít, és van egy alapértelmezett megtagadási explicit és a rendszer. Házirend során, és a telepítés utáni erőforrás-tulajdonságok összpontosít. Az alapértelmezett engedélyezése és explicit megtagadja a rendszer.

Nincsenek két fogalom megértéséhez a házirendek – házirend-definíciók és házirend-hozzárendelést. Házirend-definíció a feltételek kikényszerítéséhez írja le. Egy házirend-hozzárendelést a művelet egy adott hatókör házirend-definíció helyezi.

![Házirendek rendelhetők](./media/resource-manager-governance-policy/policy-concepts.png)

Azure biztosít több beépített házirend-definíciók módosítás nélkül is használhatja. Adja meg, amelyeknél engedélyezve van a hatókörén értékek megadásához paraméterértékeket. Ha a beépített házirend-definíció nem megfelel a követelményeknek, akkor [egyéni házirend-meghatározások létrehozása](../articles/azure-policy/create-manage-policy.md).

### <a name="who-can-create-and-assign-policies"></a>Aki hozhat létre és rendelhet hozzá házirendeket

A szabályzatok használatához az RBAC-n keresztül hitelesítenie kell magát. A fióknak kifejezetten rendelkeznie kell a következőkkel:

* `Microsoft.Authorization/policydefinitions/write` engedély a szabályzatok definiálásához.
* `Microsoft.Authorization/policyassignments/write` engedély a szabályzatok hozzárendeléséhez.

Ezek az engedélyek nem szerepelnek a **közreműködő** szerepkör.
