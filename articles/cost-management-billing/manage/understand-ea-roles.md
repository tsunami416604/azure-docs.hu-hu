---
title: A vállalati rendszergazdai szerepkörök az Azure-ban | Microsoft Docs
description: Megismerheti a vállalati rendszergazdai szerepköröket az Azure-ban.
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/02/2020
ms.author: banders
ms.openlocfilehash: ae33d320213cc526710845e78c23a83143a73771
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989903"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Az Azure Nagyvállalati Szerződés rendszergazdai szerepkörei az Azure-ban

A szervezet használati adatainak és költségeinek kezeléséhez a Nagyvállalati Szerződéssel (EA) rendelkező Azure-ügyfelek öt különféle rendszergazdai szerepkört rendelhetnek felhasználóikhoz:

- Vállalati rendszergazda
- Vállalati rendszergazda (csak olvasási)
- Részlegszintű rendszergazda
- Részlegszintű rendszergazda (csak olvasási)
- Fióktulajdonos
 
Ezek a szerepkörök kifejezetten az Azure Nagyvállalati Szerződések kezelésére szolgálnak, és kiegészítik az Azure beépített szerepköreit, amelyek az erőforrásokhoz való hozzáférést szabályozzák. További információkért lásd az [Azure-erőforrások beépített szerepköreit](../../role-based-access-control/built-in-roles.md).

A következő szakaszok az egyes szerepkörök korlátait és képességeit mutatják be.

## <a name="user-limit-for-admin-roles"></a>A rendszergazdai szerepkörök felhasználói korlátai

|Szerepkör| Felhasználói korlát|
|---|---|
|Vállalati rendszergazda|Korlátlan|
|Vállalati rendszergazda (csak olvasási)|Korlátlan|
|Részlegszintű rendszergazda|Korlátlan|
|Részlegszintű rendszergazda (csak olvasási)|Korlátlan|
|Fióktulajdonos|Fiókonként 1<sup>1</sup>|

<sup>1</sup>Mindegyik fiókhoz szükség van egyedi Microsoft-fiókra, illetve munkahelyi vagy iskolai fiókra.

## <a name="organization-structure-and-permissions-by-role"></a>Szervezeti felépítés és engedélyek szerepkörönként

|Feladatok| Vállalati rendszergazda|Vállalati rendszergazda (csak olvasási)|Részlegszintű rendszergazda|Részlegszintű rendszergazda (csak olvasási)|Fióktulajdonos|
|---|---|---|---|---|---|
|Vállalati rendszergazdák megtekintése|✔|✔|✘|✘|✘|
|Vállalati rendszergazdák hozzáadása vagy eltávolítása|✔|✘|✘|✘|✘|
|Értesítési kapcsolattartók megtekintése<sup>2</sup> |✔|✔|✘|✘|✘|
|Értesítési kapcsolattartók hozzáadása vagy eltávolítása<sup>2</sup> |✔|✘|✘|✘|✘|
|Részlegek létrehozására és kezelésére |✔|✘|✘|✘|✘|
|Részlegszintű rendszergazdák megtekintése|✔|✔|✔|✔|✘|
|Részlegszintű rendszergazdák hozzáadása vagy eltávolítása|✔|✘|✔|✘|✘|
|A regisztrációhoz tartozó fiókok megtekintése |✔|✔|✔<sup>3</sup>|✔<sup>3</sup>|✘|
|Fiókok hozzáadása a regisztrációhoz és a fióktulajdonos módosítása|✔|✘|✔<sup>3</sup>|✘|✘|
|Előfizetések és előfizetési engedélyek létrehozása és kezelése|✘|✘|✘|✘|✔|

- <sup>2</sup>A kapcsolattartók kapják meg az Azure Nagyvállalati Szerződésre vonatkozó tájékoztató e-maileket.
- <sup>3</sup>A feladat a részlegen belüli fiókokra korlátozódik.


## <a name="usage-and-costs-access-by-role"></a>Szerepkörök szerint hozzáférés a használati adatokhoz és a költségekhez

|Feladatok| Vállalati rendszergazda|Vállalati rendszergazda (csak olvasási)|Részlegszintű rendszergazda|Részlegszintű rendszergazda (csak olvasási) |Fióktulajdonos|
|---|---|---|---|---|---|
|Kreditegyenleg megtekintése, beleértve a pénzügyi keretet|✔|✔|✘|✘|✘|
|Részleg költségkvótáinak megtekintése|✔|✔|✘|✘|✘|
|Részleg költségkvótáinak beállítása|✔|✘|✘|✘|✘|
|Szervezet EA-árlistájának megtekintése|✔|✔|✘|✘|✘|
|Használati adatok és költségek megtekintése|✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✔<sup>5</sup>|
|Erőforrások kezelése az Azure Portalon|✘|✘|✘|✘|✔|

- <sup>4</sup>A vállalati rendszergazdának engedélyeznie kell a **díjtételek megtekintését a részlegszintű rendszergazda számára** az Enterprise Portalon. A részlegszintű rendszergazda ezután megtekintheti a részleg költségeire vonatkozó adatokat.
- <sup>5</sup>A vállalati rendszergazdának engedélyeznie kell a **díjtételek megtekintését a fióktulajdonos számára** az Enterprise Portalon. A fióktulajdonos ezután megtekintheti a fiók költségeire vonatkozó adatokat.


## <a name="pricing-in-azure-portal"></a>Az Azure Portal díjszabása

Azt, hogy az Azure Portalon milyen díjszabás jelenik meg befolyásolja az Ön rendszergazdai szerepköre, valamint az, hogy a vállalati rendszergazda milyen díjtétel-megtekintési szabályzatokat állított be. Az Enterprise Portalon beállítható két szabályzat, amely befolyásolja az Azure Portalon megjelenő díjat:

- Díjtételek megtekintése a részlegszintű rendszergazda számára
- Díjtételek megtekintése a fióktulajdonos számára

A szabályzatok beállításával kapcsolatban tekintse meg [az Azure-beli számlázási információkhoz való hozzáférés kezelését](manage-billing-access.md) ismertető témakört.

A következő táblázat bemutatja a Nagyvállalati Szerződés rendszergazdai szerepkörei, a beállított díjtétel-megtekintési szabályzat, az Azure Portal szerepköralapú hozzáférés-vezérlési (RBAC-) szerepköre, valamint az Azure Portalon megjelenő díjak közötti összefüggéseket. A vállalati rendszergazda mindig a szervezet EA-díjszabása szerinti használati adatokat látja. A részlegszintű rendszergazdák és a fióktulajdonosok ugyanakkor eltérő díjakat láthatnak a díjtétel-megtekintési szabályzattól és a saját RBAC-szerepkörüktől függően. A következő táblázatban szereplő részlegszintű rendszergazda szerepkör magában foglalja a Részlegszintű rendszergazda és a Részlegszintű rendszergazda (csak olvasási) szerepkört is.

|Nagyvállalati Szerződés rendszergazdai szerepköre|A szerepkörhöz beállított díjtétel-megtekintési szabályzat|RBAC-szerepkör|Megjelenő díjszabás|
|---|---|---|---|
|Fióktulajdonos VAGY részlegszintű rendszergazda|✔ Engedélyezve|Tulajdonos|Szervezet EA-díjszabása|
|Fióktulajdonos VAGY részlegszintű rendszergazda|✘ Letiltva|Tulajdonos|Kiskereskedelmi díjszabás|
|Fióktulajdonos VAGY részlegszintű rendszergazda|✔ Engedélyezve |Nincs|Nincs díjszabás|
|Fióktulajdonos VAGY részlegszintű rendszergazda|✘ Letiltva |Nincs|Nincs díjszabás|
|None|Nem alkalmazható |Tulajdonos|Kiskereskedelmi díjszabás|

A vállalati rendszergazdai szerepkört és a díjtétel-megtekintési szabályzatokat az Enterprise Portalon lehet beállítani. Az RBAC-szerepkört az Azure Portalon lehet frissíteni. További információkért lásd [a hozzáférés az RBAC és az Azure Portal használatával történő kezelését](../../role-based-access-control/role-assignments-portal.md) ismertető cikket.

## <a name="next-steps"></a>Következő lépések

- [Az Azure-beli számlázási információkhoz való hozzáférés kezelése](manage-billing-access.md)
- [Hozzáférés kezelése az RBAC és az Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md)
- [Beépített szerepkörök Azure-erőforrásokhoz](../../role-based-access-control/built-in-roles.md)
