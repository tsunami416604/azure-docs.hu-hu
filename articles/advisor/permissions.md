---
title: Engedélyek az Azure Advisorban
description: Tanácsadói engedélyek, valamint az, hogy hogyan akadályozhatják meg az előfizetések konfigurálását, illetve a javaslatok elhalasztását vagy elvetését.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: c850d757044066d5c4a793e076436906d715833c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422320"
---
# <a name="permissions-in-azure-advisor"></a>Engedélyek az Azure Advisorban

Az Azure Advisor az Azure-erőforrások és -előfizetések használatán és konfigurációján alapuló javaslatokat tartalmaz. Az Advisor a [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) által biztosított [beépített szerepköröket](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) használja a javaslatokhoz és az Advisor-funkciókhoz való hozzáférés kezeléséhez. 

## <a name="roles-and-their-access"></a>Szerepek és azok hozzáférése

Az alábbi táblázat az Advisoron belüli szerepköröket és hozzáféréseket határozza meg:

| **Szerepkör** | **Javaslatok megtekintése** | **Szabályok szerkesztése** | **Előfizetés-konfiguráció szerkesztése** | **Erőforráscsoport-konfiguráció szerkesztése**| **Ajánlások elvetése és elhalasztása**|
|---|:---:|:---:|:---:|:---:|:---:|
|Előfizetés tulajdonosa|**X**|**X**|**X**|**X**|**X**|
|Előfizetés közreműködője|**X**|**X**|**X**|**X**|**X**|
|Előfizetés-olvasó|**X**|--|--|--|--|
|Erőforráscsoport tulajdonosa|**X**|--|--|**X**|**X**|
|Erőforráscsoport közreműködője|**X**|--|--|**X**|**X**|
|Erőforráscsoport olvasója|**X**|--|--|--|--|
|Erőforrás tulajdonosa|**X**|--|--|--|**X**|
|Erőforrás-közreműködő|**X**|--|--|--|**X**|
|Erőforrás-olvasó|**X**|--|--|--|--|

> [!NOTE]
> A javaslatok megtekintéséhez való hozzáférés függ a hozzáférés a javaslat érintett erőforrás.

## <a name="permissions-and-unavailable-actions"></a>Engedélyek és nem elérhető műveletek

A megfelelő engedélyek hiánya letilthatja a műveletek elvégzését az Advisorban. Az alábbiakban néhány gyakori probléma.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Nem lehet konfigurálni az előfizetéseket vagy az erőforráscsoportokat

Amikor megpróbálja konfigurálni az előfizetéseket vagy az erőforráscsoportokat az Advisorban, láthatja, hogy a felvétel vagy kizárás lehetősége le van tiltva. Ez az állapot azt jelzi, hogy nem rendelkezik megfelelő szintű engedéllyel az adott erőforráscsoporthoz vagy előfizetéshez. A probléma megoldásához olvassa el, hogyan adhat felhasználói hozzáférést a [rendszerhez.](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Nem lehet elhalasztani vagy elutasítani egy ajánlást

Ha hibaüzenetet kap egy javaslat elhalasztása vagy elvetése során, előfordulhat, hogy nem rendelkezik a megfelelő engedélyekkel. Győződjön meg arról, hogy legalább közreműködői hozzáféréssel rendelkezik az elhaposta vagy elvetése érdekében az érintett erőforráshoz. A probléma megoldásához olvassa el, hogyan adhat felhasználói hozzáférést a [rendszerhez.](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

## <a name="next-steps"></a>További lépések

Ez a cikk áttekintést ad arról, hogy az Advisor hogyan használja az RBAC-ot a felhasználói engedélyek szabályozására, és hogyan oldhatja meg a gyakori problémákat. Ha többet szeretne megtudni az Advisorról, olvassa el az:

- [Mi az Azure Advisor?](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Ismerkedés az Azure Advisor szolgáltatással](https://docs.microsoft.com/azure/advisor/advisor-get-started)
