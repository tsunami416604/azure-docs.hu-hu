---
title: A vállalati rendszergazdai szerepkörök az Azure-ban
description: Megismerheti a vállalati rendszergazdai szerepköröket az Azure-ban.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: banders
ms.openlocfilehash: b8c523acabd02dc30e9b13f7f83a4a44554cbd4d
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690928"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Az Azure Nagyvállalati Szerződés rendszergazdai szerepkörei az Azure-ban

A szervezet használati adatainak és költségeinek kezeléséhez a Nagyvállalati Szerződéssel (EA) rendelkező Azure-ügyfelek öt különféle rendszergazdai szerepkört rendelhetnek felhasználóikhoz:

- Vállalati rendszergazda
- Vállalati rendszergazda (csak olvasási)<sup>1</sup>
- Részlegszintű rendszergazda
- Részlegszintű rendszergazda (csak olvasási)
- Fióktulajdonos<sup>2</sup>

<sup>1</sup>A nagyvállalati szerződés számlázási kapcsolattartója lesz ehhez a szerepkörhöz rendelve.

<sup>2</sup>A számlázási kapcsolattartót nem lehet hozzáadni vagy módosítani az Azure EA portálon, és az EA-regisztrációhoz a szerződés szintjén hozzáadott számlázási kapcsolattartóként megadott felhasználó lesz hozzáadva. A számlázási kapcsolattartó módosításához regionális műveleti központ (ROC) partner-/szoftvertanácsadójához kell kérelmet benyújtani.

Ezek a szerepkörök kifejezetten az Azure Nagyvállalati Szerződések kezelésére szolgálnak, és kiegészítik az Azure beépített szerepköreit, amelyek az erőforrásokhoz való hozzáférést szabályozzák. További információkért lásd az [Azure-erőforrások beépített szerepköreit](../../role-based-access-control/built-in-roles.md).

A következő szakaszok az egyes szerepkörök korlátait és képességeit mutatják be.

## <a name="user-limit-for-admin-roles"></a>A rendszergazdai szerepkörök felhasználói korlátai

|Szerepkör| Felhasználói korlát|
|---|---|
|Vállalati rendszergazda|Korlátlan|
|Vállalati rendszergazda (csak olvasási)|Korlátlan|
|Részlegszintű rendszergazda|Korlátlan|
|Részlegszintű rendszergazda (csak olvasási)|Korlátlan|
|Fióktulajdonos|Fiókonként 1<sup>3</sup>|

<sup>3</sup>Mindegyik fiókhoz szükség van egyedi Microsoft-fiókra, illetve munkahelyi vagy iskolai fiókra.

## <a name="organization-structure-and-permissions-by-role"></a>Szervezeti felépítés és engedélyek szerepkörönként

|Feladatok| Vállalati rendszergazda|Vállalati rendszergazda (csak olvasási)|Részlegszintű rendszergazda|Részlegszintű rendszergazda (csak olvasási)|Fióktulajdonos| Partner|
|---|---|---|---|---|---|---|
|Vállalati rendszergazdák megtekintése|✔|✔|✘|✘|✘|✔|
|Vállalati rendszergazdák hozzáadása vagy eltávolítása|✔|✘|✘|✘|✘|✘|
|Értesítési kapcsolattartók megtekintése<sup>4</sup> |✔|✔|✘|✘|✘|✔|
|Értesítési kapcsolattartók hozzáadása vagy eltávolítása<sup>4</sup> |✔|✘|✘|✘|✘|✘|
|Részlegek létrehozására és kezelésére |✔|✘|✘|✘|✘|✘|
|Részlegszintű rendszergazdák megtekintése|✔|✔|✔|✔|✘|✔|
|Részlegszintű rendszergazdák hozzáadása vagy eltávolítása|✔|✘|✔|✘|✘|✘|
|A regisztrációhoz tartozó fiókok megtekintése |✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✘|✔|
|Fiókok hozzáadása a regisztrációhoz és a fióktulajdonos módosítása|✔|✘|✔<sup>5</sup>|✘|✘|✘|
|Előfizetések és előfizetési engedélyek létrehozása és kezelése|✘|✘|✘|✘|✔|✘|

- <sup>4</sup>Az értesítési kapcsolattartók kapják meg az Azure Nagyvállalati Szerződésre vonatkozó tájékoztató e-maileket.
- <sup>5</sup>A feladat a részlegen belüli fiókokra korlátozódik.


## <a name="usage-and-costs-access-by-role"></a>Szerepkörök szerint hozzáférés a használati adatokhoz és a költségekhez

|Feladatok| Vállalati rendszergazda|Vállalati rendszergazda (csak olvasási)|Részlegszintű rendszergazda|Részlegszintű rendszergazda (csak olvasási) |Fióktulajdonos| Partner|
|---|---|---|---|---|---|---|
|Kreditegyenleg megtekintése, beleértve a pénzügyi keretet|✔|✔|✘|✘|✘|✔|
|Részleg költségkvótáinak megtekintése|✔|✔|✘|✘|✘|✔|
|Részleg költségkvótáinak beállítása|✔|✘|✘|✘|✘|✘|
|Szervezet EA-árlistájának megtekintése|✔|✔|✘|✘|✘|✔|
|Használati adatok és költségek megtekintése|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|Erőforrások kezelése az Azure Portalon|✘|✘|✘|✘|✔|✘|

- <sup>6</sup>A vállalati rendszergazdának engedélyeznie kell a **díjtételek megtekintését a részlegszintű rendszergazda számára** az Enterprise Portalon. A részlegszintű rendszergazda ezután megtekintheti a részleg költségeire vonatkozó adatokat.
- <sup>7</sup>A vállalati rendszergazdának engedélyeznie kell a **díjtételek megtekintését a fióktulajdonos számára** az Enterprise Portalon. A fióktulajdonos ezután megtekintheti a fiók költségeire vonatkozó adatokat.


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

## <a name="next-steps"></a>További lépések

- [Az Azure-beli számlázási információkhoz való hozzáférés kezelése](manage-billing-access.md)
- [Hozzáférés kezelése az RBAC és az Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md)
- [Beépített szerepkörök Azure-erőforrásokhoz](../../role-based-access-control/built-in-roles.md)
