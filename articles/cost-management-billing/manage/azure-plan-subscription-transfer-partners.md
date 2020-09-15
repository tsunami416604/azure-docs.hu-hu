---
title: Azure-előfizetések átvitele az egyik partnertől a másikhoz (előzetes verzió)
description: Ez a cikk segít megismerni azokat az információkat, amelyeket tudni érdemes az Azure-előfizetés számlázási tulajdonjogának átadása előtt és után.
author: bandersmsft
ms.reviewer: mcville
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: banders
ms.openlocfilehash: 246128ec3d26e4bf3f2a5a8f0660aab7d9daca3d
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2020
ms.locfileid: "89554202"
---
# <a name="transfer-azure-subscriptions-from-one-partner-to-another-preview"></a>Azure-előfizetések átvitele az egyik partnertől a másikhoz (előzetes verzió)

Ez a cikk segít megismerni azokat az információkat, amelyeket tudni érdemes az Azure-előfizetés számlázási tulajdonjogának átadása előtt és után. Egy Azure-csomagban lévő Azure-előfizetés az egyik Microsoft-partnertől a másikhoz való átvitelének megkezdéséhez fel kell vennie a kapcsolatot a partnerével. A partnere el fogja küldeni a kezdéshez szükséges utasításokat. Az átviteli folyamat befejezését követően az előfizetés számlázási tulajdonjoga módosul.

## <a name="user-access"></a>Felhasználói hozzáférés

A meglévő felhasználók, csoportok vagy szolgáltatásnevek hozzáféréseire, amelyek az Azure szerepköralapú hozzáférés-vezérlés (RBAC) segítségével lettek hozzárendelve, a váltás nincs hatással. Az [Azure RBAC](../../role-based-access-control/overview.md) lehetővé teszi annak meghatározását, hogy kik férhetnek hozzá az Azure-erőforrásokhoz, mire használhatják őket, és mely területekhez kapnak hozzáférést. Az új partner nem kap RBAC-hozzáférést az erőforrásaihoz az előfizetés-átadás révén. A korábbi partner megtartja RBAC-hozzáférését.

Ezért fontos, hogy eltávolítsa a régi partner Azure RBAC-hozzáférését, és hozzáférést adjon az új partner számára. További információ az új partner hozzáférésének biztosításával kapcsolatban: [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../../role-based-access-control/overview.md) További információ a korábbi partner RBAC-hozzáférésének eltávolításával kapcsolatban: [Szerepkör-hozzárendelés eltávolítása](../../role-based-access-control/role-assignments-portal.md#remove-a-role-assignment).

Az új partner továbbá nem kap automatikusan [megbízott felügyeleti (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) hozzáférést az előfizetéseihez. A partnerének azért van szüksége AOBO-ra, hogy az Ön nevében kezelhesse az Azure-előfizetéseket. Az AOBO-hozzáférés az új partner számára történő megadásával kapcsolatban további információt [az Azure-előfizetési fiók számlázási tulajdonjoga másik fióknak való átadásával foglalkozó részben](billing-subscription-transfer.md) talál.

## <a name="stop-a-transfer"></a>Átvitel leállítása

Miután megkapta a megerősítést az átadási kérelem elküldéséről, az alábbi lehetőségek közül választhat, **ha nem szeretné folytatni az átvitelt**.

- Ha a jelenlegi partner még nem fogadta el a kérelmet, megkérheti az új partnert az elindított átadási kérelem törlésére (ha annak állapota Függőben).
- Kérje meg jelenlegi partnerét, hogy ne tegyen semmit, amikor megkapja az átadási kérelmet. A jelenlegi partner hozzájárulása nélkül az átvitel nem folytatódhat. A kérelem le fog járni.
- Az új partnerrel létesített _viszonteladói kapcsolatot eltávolíthatja_. Ez a művelet a továbbiakban nem teszi lehetővé az előfizetés áthelyezését. Ezzel hatékonyan törölheti a kérelmet.

Ezenkívül a [Microsoft jogi információit tartalmazó](https://www.microsoft.com/legal/) webhelyen elérhető lehetőségek használatával segítséget kérhet, illetve jelentheti a kötelességszegést és a gyanús tevékenységeket. Az észrevételek bejelentésére szolgáló lehetőség a Megfelelőség és etika területen található.

## <a name="next-steps"></a>Következő lépések

- RBAC-hozzáférés megadása az új partner számára: [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../../role-based-access-control/overview.md)
- Az AOBO-hozzáférés az új partner számára történő megadásával kapcsolatban [az Azure-előfizetési fiók számlázási tulajdonjoga másik fióknak való átadásával foglalkozó részben](billing-subscription-transfer.md) tájékozódhat.