---
title: A vállalati rendszergazdai szerepkörök az Azure-ban
description: Megismerheti a vállalati rendszergazdai szerepköröket az Azure-ban. Öt különböző adminisztratív szerepkört rendelhet hozzá.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: banders
ms.openlocfilehash: 13b344d3f13993dc7b6acf7bfe9a0ccdea0c866b
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371354"
---
# <a name="managing-azure-enterprise-roles"></a>Az Azure Enterprise szerepköreinek kezelése

A szervezet használati adatainak és költségeinek kezeléséhez a Nagyvállalati Szerződéssel (EA) rendelkező Azure-ügyfelek öt különféle rendszergazdai szerepkört rendelhetnek felhasználóikhoz:

- Vállalati rendszergazda
- Vállalati rendszergazda (csak olvasási)<sup>1</sup>
- Részlegszintű rendszergazda
- Részlegszintű rendszergazda (csak olvasási)
- Fióktulajdonos<sup>2</sup>

<sup>1</sup>A nagyvállalati szerződés számlázási kapcsolattartója lesz ehhez a szerepkörhöz rendelve.

<sup>2</sup>A számlázási kapcsolattartót nem lehet hozzáadni vagy módosítani az Azure EA portálon, és az EA-regisztrációhoz a szerződés szintjén hozzáadott számlázási kapcsolattartóként megadott felhasználó lesz hozzáadva. A számlázási kapcsolattartó módosításához regionális műveleti központ (ROC) partner-/szoftvertanácsadójához kell kérelmet benyújtani.

A regisztráció kiépítése során megadott első regisztrációs rendszergazda határozza meg a számlázási kapcsolattartó fiókja hitelesítésének típusát. Ha csak olvasási jogosultsággal rendelkező rendszergazdaként adják hozzá a számlázási kapcsolattartót az EA Portalhoz, akkor a kapcsolattartó hitelesítésének típusa Microsoft-fiókkal történő hitelesítés lesz. 

Ha például a kezdeti hitelesítési típus vegyesre van állítva, akkor az EA Microsoft-fiókként lesz hozzáadva, és a számlázási kapcsolattartó csak olvasási EA-rendszergazdai jogosultságokkal fog rendelkezni. Ha az EA-rendszergazda nem hagyja jóvá egy meglévő számlázási kapcsolattartó Microsoft-fiókkal történő hitelesítését, az EA-rendszergazda törölheti a szóban forgó felhasználót, és megkérheti az ügyfelet, hogy újból adja hozzá a felhasználót csak olvasási jogosultsággal rendelkező rendszergazdaként, munkahelyi vagy iskolai fiókkal, amely csak a regisztráció szintjén van megadva az EA Portalon.

Ezek a szerepkörök kifejezetten az Azure Nagyvállalati Szerződések kezelésére szolgálnak, és kiegészítik az Azure beépített szerepköreit, amelyek az erőforrásokhoz való hozzáférést szabályozzák. További információ: [Beépített Azure-szerepkörök](../../role-based-access-control/built-in-roles.md).

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

## <a name="add-a-new-enterprise-administrator"></a>Új vállalati rendszergazda hozzáadása

Az Azure EA-regisztrációk kezelésénél a vállalati rendszergazdák rendelkeznek a legtöbb jogosultsággal. Az első Azure EA-rendszergazdát a nagyvállalati szerződés megkötésekor hozták létre. Viszont bármikor hozzá lehet adni új rendszergazdákat, vagy el is lehet távolítani őket. Az új rendszergazdákat csak a meglévő rendszergazdák adhatják hozzá. A vállalati rendszergazdák hozzáadásával kapcsolatos további információkért lásd az [új vállalati rendszergazda létrehozását](ea-portal-get-started.md#create-another-enterprise-administrator) ismertető részt. A számlázási profil szerepköreivel és feladataival kapcsolatos további információkért lásd [a számlázási profil szerepköreinek és azok feladatainak ismertetését](understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-account-owner-state-from-pending-to-active"></a>A fióktulajdonosi állapot frissítése Függőben értékről Aktív értékre

Amikor először hozzáadják az új fióktulajdonosokat (AO) egy Azure EA-regisztrációhoz, a _függőben_ állapot jelenik meg náluk. Amikor az új fióktulajdonosok megkapják az aktiváló e-mailt, be tudnak jelentkezni a fiókjuk aktiválásához. A fiókjuk aktiválásakor a fiók állapota _függőben_ értékről _aktív_ értékre módosul. A fióktulajdonosnak el kell olvasnia a figyelmeztető üzenetet, majd a **Folytatás** lehetőséget kell választania. A rendszer kérheti az új felhasználók vezeték- és utónevét egy Kereskedelmi fiók létrehozásához. Ha kéri, akkor meg kell adniuk a szükséges adatokat a folytatáshoz, majd megtörténik a fiók aktiválása.

## <a name="add-a-department-admin"></a>Részlegszintű rendszergazda hozzáadása

Ha az egyik Azure EA-rendszergazda létrehozott egy részleget, az Azure vállalati rendszergazda részlegszintű rendszergazdákat adhat hozzá, és hozzárendelheti őket egy-egy részleghez. A részlegszintű rendszergazdák új fiókokat hozhatnak létre. Az Azure EA-előfizetések létrehozásához új fiókokra van szükség.

A részlegszintű rendszergazdák hozzáadásával kapcsolatos további információkért tekintse meg az [Azure EA-részlegszintű rendszergazdák létrehozását](ea-portal-get-started.md#add-a-department-administrator) ismertető témakört.

## <a name="usage-and-costs-access-by-role"></a>Szerepkörök szerint hozzáférés a használati adatokhoz és a költségekhez

|Feladatok| Vállalati rendszergazda|Vállalati rendszergazda (csak olvasási)|Részlegszintű rendszergazda|Részlegszintű rendszergazda (csak olvasási) |Fióktulajdonos| Partner|
|---|---|---|---|---|---|---|
|Kreditegyenleg megtekintése, beleértve az Azure-előrefizetést|✔|✔|✘|✘|✘|✔|
|Részleg költségkvótáinak megtekintése|✔|✔|✘|✘|✘|✔|
|Részleg költségkvótáinak beállítása|✔|✘|✘|✘|✘|✘|
|Szervezet EA-árlistájának megtekintése|✔|✔|✘|✘|✘|✔|
|Használati adatok és költségek megtekintése|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|Erőforrások kezelése az Azure Portalon|✘|✘|✘|✘|✔|✘|

- <sup>6</sup>A vállalati rendszergazdának engedélyeznie kell a **díjtételek megtekintését a részlegszintű rendszergazda számára** az Enterprise Portalon. A részlegszintű rendszergazda ezután megtekintheti a részleg költségeire vonatkozó adatokat.
- <sup>7</sup>A vállalati rendszergazdának engedélyeznie kell a **díjtételek megtekintését a fióktulajdonos számára** az Enterprise Portalon. A fióktulajdonos ezután megtekintheti a fiók költségeire vonatkozó adatokat.

## <a name="see-pricing-for-different-user-roles"></a>A különböző felhasználói szerepkörök díjszabásának megtekintése

Azt, hogy az Azure Portalon milyen díjszabás jelenik meg befolyásolja az Ön rendszergazdai szerepköre, valamint az, hogy a vállalati rendszergazda milyen díjtétel-megtekintési szabályzatokat állított be. Az Enterprise Portalon beállítható két szabályzat, amely befolyásolja az Azure Portalon megjelenő díjat:

- Díjtételek megtekintése a részlegszintű rendszergazda számára
- Díjtételek megtekintése a fióktulajdonos számára

A szabályzatok beállításával kapcsolatban tekintse meg [az Azure-beli számlázási információkhoz való hozzáférés kezelését](manage-billing-access.md) ismertető témakört.

A következő táblázat bemutatja a Nagyvállalati Szerződés rendszergazdai szerepkörei, a beállított díjtétel-megtekintési szabályzat, az Azure Portal Azure-szerepköre, valamint az Azure Portalon megjelenő díjak közötti összefüggéseket. A vállalati rendszergazda mindig a szervezet EA-díjszabása szerinti használati adatokat látja. A részlegszintű rendszergazdák és a fióktulajdonosok ugyanakkor eltérő díjakat láthatnak a díjtétel-megtekintési szabályzattól és a saját Azure-szerepkörüktől függően. A következő táblázatban szereplő részlegszintű rendszergazda szerepkör magában foglalja a Részlegszintű rendszergazda és a Részlegszintű rendszergazda (csak olvasási) szerepkört is.

|Nagyvállalati Szerződés rendszergazdai szerepköre|A szerepkörhöz beállított díjtétel-megtekintési szabályzat|Azure-szerepkör|Megjelenő díjszabás|
|---|---|---|---|
|Fióktulajdonos VAGY részlegszintű rendszergazda|✔ Engedélyezve|Tulajdonos|Szervezet EA-díjszabása|
|Fióktulajdonos VAGY részlegszintű rendszergazda|✘ Letiltva|Tulajdonos|Kiskereskedelmi díjszabás|
|Fióktulajdonos VAGY részlegszintű rendszergazda|✔ Engedélyezve |Nincs|Nincs díjszabás|
|Fióktulajdonos VAGY részlegszintű rendszergazda|✘ Letiltva |Nincs|Nincs díjszabás|
|None|Nem alkalmazható |Tulajdonos|Kiskereskedelmi díjszabás|

A vállalati rendszergazdai szerepkört és a díjtétel-megtekintési szabályzatokat az Enterprise Portalon lehet beállítani. Az Azure-szerepkört az Azure Portalon lehet frissíteni. További információ: [Azure-beli szerepkör-hozzárendelés hozzáadása vagy eltávolítása az Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).



## <a name="next-steps"></a>További lépések

- [Az Azure-beli számlázási információkhoz való hozzáférés kezelése](manage-billing-access.md)
- [Azure-beli szerepkör-hozzárendelés hozzáadása vagy eltávolítása az Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md)
- [Beépített Azure-szerepkörök](../../role-based-access-control/built-in-roles.md)
