---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c04514218c7ed8dfd72b94345d2deb88e663fda1
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2018
ms.locfileid: "29529977"
---
[Az Azure házirendek](/azure/azure-policy/) biztosíthatja összes erőforrás előfizetés súgó felel meg a vállalati szabványoknak. A házirendekkel korlátozza, hogy a központi telepítési beállítások csak erőforrástípusok és jóváhagyott termékváltozatok a költségek csökkentése érdekében. Szabályok és a műveletek megadhatja az erőforrásokhoz, és ezeket a szabályokat automatikus központi telepítés során lépnek érvénybe. Például a telepített erőforrások esetén típusú szabályozhatja. Vagy az engedélyezett helyek erőforrások korlátozhatja. Egyes szabályzatokat művelet megtagadása, és néhány házirend beállítása művelet naplózását.

A házirend olyan kiegészítő a szerepköralapú hozzáférés-vezérlést (RBAC). Szerepalapú felhasználói hozzáférés összpontosít, és van egy alapértelmezett megtagadási explicit és a rendszer. Házirend során, és a telepítés utáni erőforrás-tulajdonságok összpontosít. Az alapértelmezett engedélyezése és explicit megtagadja a rendszer.

Nincsenek a két fogalom megértéséhez házirendekkel - *házirend-definíciók* és *házirend-hozzárendelések*. Házirend-definíció a feltételek kikényszerítéséhez írja le. Egy házirend-hozzárendelést a művelet egy adott hatókör házirend-definíció helyezi.

![Házirendek rendelhetők](./media/resource-manager-governance-policy/policy-concepts.png)

Azure biztosít több beépített házirend-definíciók módosítás nélkül is használhatja. Adja meg, amelyeknél engedélyezve van a hatókörén értékek megadásához paraméterértékeket. Ha a beépített házirend-definíció nem megfelel a követelményeknek, akkor [egyéni házirend-meghatározások létrehozása](../articles/azure-policy/create-manage-policy.md).
