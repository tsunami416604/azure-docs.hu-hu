---
title: Az Azure-beli vállalati rendszergazdai szerepköreinek ismertetése |} A Microsoft Docs
description: Ismerje meg a vállalati rendszergazdai szerepkörök az Azure-ban.
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2018
ms.author: banders
ms.openlocfilehash: 98ed28af8df246549fb521a81f1968e1f5c28cc4
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54901564"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Megismerheti az Azure nagyvállalati szerződéssel rendszergazdai szerepkörök az Azure-ban

Segíti a vállalata használati kezelését, és a költségek, Azure ügyfelek együtt egy nagyvállalati szerződés (EA) öt különböző rendszergazdai szerepköröket rendelhet hozzá:

- Vállalati rendszergazda
- A vállalati rendszergazdák (írásvédett)
- Részleg adminisztrátora
- Szervezeti egység rendszergazdája (írásvédett)
- Fióktulajdonos
 
Ezek a szerepkörök jellemző kezelése az Azure Enterprise-megállapodások és az erőforrásokhoz való hozzáférés szabályozása az Azure rendelkezik a beépített szerepkörök mellett. További információkért lásd: [beépített szerepkörök az Azure-erőforrások](../role-based-access-control/built-in-roles.md).

A következő szakaszok ismertetik a korlátozások és az egyes szerepkörök képességeit.

## <a name="user-limit-for-admin-roles"></a>Rendszergazdai szerepkörökhöz a felhasználói korlátot

|Szerepkör| A felhasználói korlátot|
|---|---|
|Vállalati rendszergazda|Korlátlan|
|A vállalati rendszergazdák (írásvédett)|Korlátlan|
|Részleg adminisztrátora|Korlátlan|
|Szervezeti egység rendszergazdája (írásvédett)|Korlátlan|
|Fióktulajdonos|fiókonként 1<sup>1</sup>|

<sup>1</sup> minden egyes fiók egyedi Microsoft-fiókot, vagy a munkahelyi vagy iskolai fiókot igényel.

## <a name="organization-structure-and-permissions-by-role"></a>Szervezeti felépítés és a szerepkör engedélyei

|Feladatok| Vállalati rendszergazda|A vállalati rendszergazdák (írásvédett)|Részleg adminisztrátora|Szervezeti egység rendszergazdája (írásvédett)|Fióktulajdonos|
|---|---|---|---|---|---|
|Vállalati rendszergazdák megtekintése|✔|✔|✘|✘|✘|
|Adja hozzá, vagy távolítsa el a vállalati rendszergazdák|✔|✘|✘|✘|✘|
|Értesítési ügyfelek tekintheti<sup>2</sup> |✔|✔|✘|✘|✘|
|Hozzáadása vagy eltávolítása értesítési névjegyek<sup>2</sup> |✔|✘|✘|✘|✘|
|Hozzon létre és szervezeti egységek kezelése |✔|✘|✘|✘|✘|
|Nézet részleg rendszergazdái|✔|✔|✔|✔|✘|
|Adja hozzá, vagy távolítsa el a részleg rendszergazdái|✔|✔|✔|✘|✘|
|A regisztrációs fiókok nézet |✔|✔|✔<sup>3</sup>|✔<sup>3</sup>|✘|
|A regisztrációs fiókok hozzáadása, és módosítsa a fiók tulajdonosa|✔|✘|✔<sup>3</sup>|✘|✘|
|Előfizetések és az előfizetés engedélyek létrehozása és kezelése|✘|✘|✘|✘|✔|

- <sup>2</sup> értesítési névjegyek küldött e-mail-üzeneteket az Azure nagyvállalati szerződéssel kapcsolatos.
- <sup>3</sup> feladat korlátozódik, a felhasználói fiókokat.


## <a name="usage-and-costs-access-by-role"></a>Használat és költségek hozzáférés szerepkör szerint

|Feladatok| Vállalati rendszergazda|A vállalati rendszergazdák (írásvédett)|Részleg adminisztrátora|Szervezeti egység rendszergazdája (írásvédett) |Fióktulajdonos|
|---|---|---|---|---|---|
|Nézet kreditegyenlegét többek között a pénzügyi kötelezettségvállalás|✔|✔|✘|✘|✘|
|Költségkeret-beállítási kvóták megtekintése részleg|✔|✔|✘|✘|✘|
|Részleg kiadások kvóták beállítása|✔|✘|✘|✘|✘|
|A szervezet nagyvállalati szerződéssel rendelkező árlistát megtekintése|✔|✔|✘|✘|✘|
|Használat és költségek részleteinek megtekintése|✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✔<sup>5</sup>|
|Erőforrások kezelése az Azure Portalon|✘|✘|✘|✘|✔|

- <sup>4</sup> engedélyeznie kell, hogy a vállalati rendszergazda **DA költségek megtekintése** szabályzat a vállalati portálon. A részleg rendszergazda megtekintheti a részleg költségek részleteit.
- <sup>5</sup> engedélyeznie kell, hogy a vállalati rendszergazda **AO költségek megtekintése** szabályzat a vállalati portálon. A fiók tulajdonosa megtekintheti költség a fiók adatait.


## <a name="pricing-in-azure-portal"></a>Az Azure Portalon díjszabása

Az Azure Portalon, a rendszergazdai szerepkör és a nézet díjak házirendek beállításának módját a vállalati rendszergazda függően különböző díjszabás jelenhet meg. A vállalati portálon a két házirend, amelyek befolyásolják a díjszabás jelenik meg az Azure Portalon a következők:

- DA költségek megtekintése
- AO költségek megtekintése

Ezek a szabályzatok beállításával kapcsolatban lásd: [számlázási adatokat az Azure-hoz való hozzáférés kezelése](billing-manage-access.md).

Az alábbi táblázat a nagyvállalati szerződés rendszergazdai szerepköröket, a költségek házirend megtekintése, a szerepköralapú hozzáférés-vezérlés (RBAC) szerepkör az Azure Portalon, és a díjszabás láthatja, amelyeket közötti kapcsolatot az Azure Portalon. A vállalati rendszergazda mindig fog látni a használat részleteiről a szervezet nagyvállalati szerződésre vonatkozó díjszabás alapján. A részleg rendszergazda és a fiók tulajdonosa lásd a díj házirend megtekintése és azok RBAC szerepkör alapján különböző díjszabási nézetet. A részleg rendszergazdai szerepkör az alábbi táblázatban felsorolt részleg Admin és (írásvédett) részleg rendszergazdai szerepkörök is hivatkozik.

|Nagyvállalati Szerződés rendszergazdai szerepkör|Szerepkör díjak házirend megtekintése|RBAC szerepkör|Díjszabás megtekintése|
|---|---|---|---|
|Fiók tulajdonosa vagy a részleg rendszergazdája|✔ Engedélyezve|Tulajdonos|A szervezet nagyvállalati szerződés díjszabása|
|Fiók tulajdonosa vagy a részleg rendszergazdája|✘ le van tiltva|Tulajdonos|Kiskereskedelmi árképzésével|
|Fiók tulajdonosa vagy a részleg rendszergazdája|✔ Engedélyezve |nincs|Nincs díjszabás|
|Fiók tulajdonosa vagy a részleg rendszergazdája|✘ le van tiltva |nincs|Nincs díjszabás|
|None|Nem alkalmazható |Tulajdonos|Kiskereskedelmi árképzésével|

A vállalati rendszergazda szerepkör meg, és nézet díjak szabályzatok a vállalati portálon. Az RBAC szerepkör frissíthető az Azure Portalon. További információkért lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>További lépések

- [Számlázási adatokat az Azure-hoz való hozzáférés kezelése](billing-manage-access.md)
- [Hozzáférés kezelése az RBAC és az Azure Portal használatával](../role-based-access-control/role-assignments-portal.md)
- [Beépített szerepkörök Azure-erőforrásokhoz](../role-based-access-control/built-in-roles.md)
