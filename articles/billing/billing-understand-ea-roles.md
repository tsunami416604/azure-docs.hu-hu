---
title: A vállalati rendszergazdai szerepkörök megismerése az Azure-ban | Microsoft Docs
description: Ismerje meg az Azure vállalati rendszergazdai szerepköreit.
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
ms.openlocfilehash: 39526defb8f41ddacb0a26d7ad852f820ca6ea77
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034528"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Az Azure Nagyvállalati Szerződés felügyeleti szerepköreinek megismerése az Azure-ban

A szervezet használatának és a költségeknek a kezeléséhez az Azure-ügyfelek egy Nagyvállalati Szerződés (EA) segítségével öt különböző felügyeleti szerepkört rendelhetnek:

- Vállalati rendszergazda
- Vállalati rendszergazda (csak olvasható)
- Részleg adminisztrátora
- Részleg rendszergazdája (csak olvasható)
- Fióktulajdonos
 
Ezek a szerepkörök az Azure nagyvállalati szerződések kezelésére vonatkoznak, és a beépített szerepkörökön kívül az Azure-nak is meg kell határoznia az erőforrásokhoz való hozzáférést. További információ: [beépített szerepkörök az Azure-erőforrásokhoz](../role-based-access-control/built-in-roles.md).

A következő szakaszok ismertetik az egyes szerepkörök korlátozásait és képességeit.

## <a name="user-limit-for-admin-roles"></a>Felhasználói korlát rendszergazdai szerepkörökhöz

|Role| Felhasználói korlát|
|---|---|
|Vállalati rendszergazda|Korlátlan|
|Vállalati rendszergazda (csak olvasható)|Korlátlan|
|Részleg adminisztrátora|Korlátlan|
|Részleg rendszergazdája (csak olvasható)|Korlátlan|
|Fióktulajdonos|1 fiók 1<sup></sup>|

<sup>1</sup> minden fiókhoz egyedi Microsoft-fiók vagy munkahelyi vagy iskolai fiók szükséges.

## <a name="organization-structure-and-permissions-by-role"></a>Szervezeti felépítés és engedélyek szerepkör szerint

|Feladatok| Vállalati rendszergazda|Vállalati rendszergazda (csak olvasható)|Részleg adminisztrátora|Részleg rendszergazdája (csak olvasható)|Fióktulajdonos|
|---|---|---|---|---|---|
|Vállalati rendszergazdák megtekintése|✔|✔|✘|✘|✘|
|Vállalati rendszergazdák hozzáadása vagy eltávolítása|✔|✘|✘|✘|✘|
|Értesítési névjegyek<sup>2</sup> megtekintése |✔|✔|✘|✘|✘|
|Értesítési névjegyek hozzáadása vagy eltávolítása<sup>2</sup> |✔|✘|✘|✘|✘|
|Részlegek létrehozása és kezelése |✔|✘|✘|✘|✘|
|Részleg rendszergazdáinak megtekintése|✔|✔|✔|✔|✘|
|Részleg rendszergazdáinak hozzáadása vagy eltávolítása|✔|✘|✔|✘|✘|
|Fiókok megtekintése a beléptetés során |✔|✔|✔<sup>3</sup>|✔<sup>3</sup>|✘|
|Fiókok hozzáadása a regisztrációhoz és a fiók tulajdonosának módosítása|✔|✘|✔<sup>3</sup>|✘|✘|
|Előfizetések és előfizetési engedélyek létrehozása és kezelése|✘|✘|✘|✘|✔|

- <sup>2</sup> az értesítési kapcsolattartók e-mailes kommunikációt küldenek az Azure nagyvállalati Szerződésról.
- <sup>3</sup> a feladat a részleg fiókjaira korlátozódik.


## <a name="usage-and-costs-access-by-role"></a>A használat és a költségek hozzáférése szerepkör szerint

|Feladatok| Vállalati rendszergazda|Vállalati rendszergazda (csak olvasható)|Részleg adminisztrátora|Részleg rendszergazdája (csak olvasható) |Fióktulajdonos|
|---|---|---|---|---|---|
|Egyenleg megtekintése, beleértve a pénzügyi kötelezettségvállalást|✔|✔|✘|✘|✘|
|Részleg költségkeretének megtekintése|✔|✔|✘|✘|✘|
|Részleg költségkeretének beállítása|✔|✘|✘|✘|✘|
|Szervezet EA árlista megtekintése|✔|✔|✘|✘|✘|
|Használat és a Cost részleteinek megtekintése|✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✔<sup>5</sup>|
|Erőforrások kezelése a Azure Portalban|✘|✘|✘|✘|✔|

- a <sup>4</sup> megköveteli, hogy a vállalati rendszergazda engedélyezze a **da View díjakra** vonatkozó szabályzatot az Enterprise Portalon. A részleg rendszergazdája Ekkor megtekintheti a részleg költségeit.
- <sup>5</sup> megköveteli, hogy a vállalati rendszergazda engedélyezze az **Ao View díjakra** vonatkozó szabályzatot az Enterprise Portalon. A fiók tulajdonosa Ekkor megtekintheti a fiókhoz tartozó költségadatok részleteit.


## <a name="pricing-in-azure-portal"></a>Díjszabás a Azure Portal

A felügyeleti szerepkörtől függően különböző díjszabást láthat a Azure Portal, valamint azt, hogy a vállalati rendszergazda hogyan adja meg a megtekintési szabályzatokat. Az Enterprise Portal két olyan szabályzata, amely hatással van a Azure Portalban látható díjszabásra:

- DA megtekintési díjak
- AO megtekintési díjak

A szabályzatok beállításával kapcsolatos további információkért lásd: a [Számlázási adatokhoz való hozzáférés kezelése az Azure](billing-manage-access.md)-ban.

Az alábbi táblázat a Nagyvállalati Szerződés rendszergazdai szerepkörök, a View charges Policy, a szerepköralapú hozzáférés-vezérlés (RBAC) szerepkört mutatja be a Azure Portal, valamint a Azure Portalban látható díjszabást. A vállalati rendszergazda mindig a szervezet nagyvállalati szerződésének díjszabása alapján látja el a használati adatokat. A részleg rendszergazdája és a fiók tulajdonosa azonban a díjszabási szabályzat és a RBAC szerepkör alapján eltérő árképzési nézeteket lát. Az alábbi táblázatban felsorolt részleg-rendszergazdai szerepkör a részleg és a Department admin (csak olvasható) szerepkörökre vonatkozik.

|Rendszergazdai szerepkör Nagyvállalati Szerződés|Szerepkörre vonatkozó díjak szabályzatának megtekintése|RBAC szerepkör|Díjszabási nézet|
|---|---|---|---|
|Fiók tulajdonosának vagy részlegének rendszergazdája|✔ Engedélyezve|Tulajdonos|A szervezet EA díjszabása|
|Fiók tulajdonosának vagy részlegének rendszergazdája|✘ Letiltva|Tulajdonos|Kiskereskedelmi díjszabás|
|Fiók tulajdonosának vagy részlegének rendszergazdája|✔ Engedélyezve |nincs|Nincs díjszabás|
|Fiók tulajdonosának vagy részlegének rendszergazdája|✘ Letiltva |nincs|Nincs díjszabás|
|Nincsenek|Nem alkalmazható |Tulajdonos|Kiskereskedelmi díjszabás|

Megadhatja a vállalati rendszergazda szerepkört, és megtekintheti a díjak szabályzatait az Enterprise Portalon. A RBAC szerepkört a Azure Portal lehet frissíteni. További információ: [a hozzáférés kezelése a RBAC és a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>További lépések

- [Számlázási adatokat az Azure-hoz való hozzáférés kezelése](billing-manage-access.md)
- [Hozzáférés kezelése az RBAC és az Azure Portal használatával](../role-based-access-control/role-assignments-portal.md)
- [Beépített szerepkörök Azure-erőforrásokhoz](../role-based-access-control/built-in-roles.md)
