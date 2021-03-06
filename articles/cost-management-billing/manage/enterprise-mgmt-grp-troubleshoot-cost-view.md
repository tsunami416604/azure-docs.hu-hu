---
title: Az Azure Enterprise költségnézeteinek hibaelhárítása
description: Megtudhatja, hogyan oldhatja meg az Azure Portalon található szervezeti költségnézetek esetleges problémáit.
author: bandersmsft
ms.reviewer: amberb
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: troubleshooting
ms.date: 08/20/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: bd0abadb624bced7f603fcbd112f7c422e88fc0c
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371847"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Az Enterprise költségnézeteinek hibaelhárítása

A vállalati regisztrációkban számos olyan beállítás létezik, amelyek miatt előfordulhat, hogy a regisztrációhoz tartozó felhasználók nem látják a költségeket.  Ezeket a beállításokat a regisztrációs adminisztrátor kezeli. Ha a regisztrációt nem közvetlenül a Microsofton keresztül vásárolja, a beállításokat a partner kezeli.  Ennek a cikknek a segítségével megismerheti a beállításokat és azok a regisztrációra gyakorolt hatását. Ezek a beállítások függetlenek az Azure szerepköreitől.

## <a name="enable-access-to-costs"></a>Hozzáférés engedélyezése a költségekhez

A „Jogosulatlan” vagy a *„A költségnézetek le vannak tiltva a regisztrációban”* üzenetet látja a költségekkel kapcsolatos adatok keresésekor?
![Képernyőkép az előfizetés Aktuális költség mezőjének „Jogosulatlan” hibaüzenetéről.](./media/enterprise-mgmt-grp-troubleshoot-cost-view/unauthorized.png)

Ez a hiba a következő okok valamelyike miatt jelentkezhet:

1. Egy vállalati partneren keresztül vásárolta meg az Azure-t, és a partner még nem adta ki a díjszabást. A díjszabási beállítás az [Enterprise Portalon](https://ea.azure.com) történő beállításának frissítése érdekében vegye fel a kapcsolatot a partnerrel.
2. Ha Ön EA Direct-ügyfél, több ok is szóba jöhet:
    * Ön fióktulajdonos, és a regisztrációs adminisztrátor letiltotta a **díjtételek megtekintésének engedélyezését a fióktulajdonos számára**.  
    * Ön részlegszintű tulajdonos, és a regisztrációs adminisztrátor letiltotta a **díjtételek megtekintésének engedélyezését a részlegszintű rendszergazda számára**.
    * Hozzáférésért vegye fel a kapcsolatot a regisztrációs adminisztrátorral. A regisztrációs adminisztrátor az [Enterprise Portalon](https://ea.azure.com/manage/enrollment) frissítheti a beállításokat.

      ![Képernyőkép a díjtételek megtekintésének beállításáról az Enterprise Portalon.](./media/enterprise-mgmt-grp-troubleshoot-cost-view/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>Az objektum nem érhető el

Ha olyan hibaüzenetet kap, amikor megpróbál hozzáférni egy előfizetéshez vagy egy felügyeleti csoporthoz, amely szerint **Az objektum nem érhető el**, akkor nem rendelkezik megfelelő szerepkörrel az elem megtekintéséhez.  

![Képernyőkép „Az objektum nem érhető el” hibaüzenetről.](./media/enterprise-mgmt-grp-troubleshoot-cost-view/asset-not-found.png)

Kérjen hozzáférést az Azure-előfizetés vagy a felügyeleti csoport rendszergazdájától. További információ: [Azure-beli szerepkör-hozzárendelés hozzáadása vagy eltávolítása az Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Következő lépések
- Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
