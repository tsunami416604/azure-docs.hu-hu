---
title: Az Azure Portal irányítópultjainak megosztása szerepköralapú hozzáférés-vezérlés sel
description: Ez a cikk bemutatja, hogyan oszthat meg egy irányítópultot az Azure Portalon a szerepköralapú hozzáférés-vezérlés használatával.
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 4eef5a9e3f010e19871471d007ff2a0cc24d3834
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461378"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Azure-irányítópultok megosztása a szerepköralapú hozzáférés-vezérlés használatával

Az irányítópult konfigurálása után közzéteheti azt, és megoszthatja a szervezet többi felhasználójával. Engedélyezheti, hogy mások is megtekinthessék az irányítópultot az Azure [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md) (RBAC) használatával. Felhasználó vagy felhasználói csoport hozzárendelése szerepkörhöz. Ez a szerepkör határozza meg, hogy ezek a felhasználók megtekinthetik vagy módosíthatják-e a közzétett irányítópultot.

Minden közzétett irányítópult Azure-erőforrásként van megvalósítva. Kezelhető elemekként léteznek az előfizetésen belül, és egy erőforráscsoportban találhatók. Hozzáférés-vezérlés szempontjából az irányítópultok nem különböznek más erőforrásoktól, például egy virtuális géptől vagy egy tárfióktól.

> [!TIP]
> Az irányítópult egyes csempéi az általuk megjelenített erőforrások alapján érvényesítik saját hozzáférés-vezérlési követelményeiket. Az irányítópultot széles körben megoszthatja, miközben védi az egyes csempék adatait.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Az irányítópultok hozzáférés-vezérlésének ismertetése

A szerepköralapú hozzáférés-vezérlés (RBAC) segítségével a felhasználókat három különböző hatókörszintű szerepkörhöz rendelheti:

* előfizetést
* erőforráscsoport
* Erőforrás

Az előfizetéstől az erőforráshoz rendelt engedélyek. A közzétett irányítópult egy erőforrás. Előfordulhat, hogy már rendelkezik felhasználókkal a közzétett irányítópultra vonatkozó előfizetés szerepköreihez.

Tegyük fel, hogy rendelkezik egy Azure-előfizetéssel, és a csapat különböző tagjai a *tulajdonos,* *közreműködő*vagy *olvasó* szerepköreit kapták az előfizetéshez. A tulajdonosok vagy közreműködők irányítópultokat sorolhatnak fel, tekinthetnek meg, hozhatnak létre, módosíthatnak vagy törölhetnek irányítópultokat az előfizetésen belül. Az olvasók at olvasó felhasználók felsorolhatják és megtekinthetik az irányítópultokat, de nem módosíthatják vagy törölhetik őket. Az olvasóhozzáféréssel rendelkező felhasználók helyi módosításokat hajthatnak végre egy közzétett irányítópulton, például hibaelhárításkor, de a módosításokat nem tehetik közzé a kiszolgálón. Ők is, hogy egy privát másolatot a műszerfal a maguk számára.

A több irányítópultot tartalmazó erőforráscsoporthoz vagy egy adott irányítópulthoz is hozzárendelhet idik. Dönthet például úgy, hogy a felhasználók egy csoportjának korlátozott engedélyekkel kell rendelkeznie az előfizetésben, de nagyobb hozzáféréssel kell rendelkeznie egy adott irányítópulthoz. Rendelje hozzá ezeket a felhasználókat az irányítópult szerepköréhez.

## <a name="publish-dashboard"></a>Irányítópult közzététele

Tegyük fel, hogy konfigurál egy irányítópultot, amelyet meg szeretne osztani az előfizetés felhasználóinak egy csoportjával. Az alábbi lépések bemutatják, hogyan oszthat meg egy irányítópultot a Storage Managers nevű csoporttal. Nevezd meg a csoportodat, amit csak akarsz. További információt a Csoportok kezelése az Azure Active Directoryban című [témakörben talál.](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

A hozzáférés hozzárendelése előtt közzé kell tennie az irányítópultot.

1. Az irányítópulton válassza a **Megosztás**lehetőséget.

    ![megosztás kiválasztása az irányítópulthoz](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. A **Megosztás + hozzáférés-vezérlés csoportban**válassza **a Közzététel**lehetőséget.

    ![irányítópult közzététele](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Alapértelmezés szerint a megosztás közzéteszi az irányítópultot egy irányítópultok nevű **erőforráscsoportban.** Másik erőforráscsoport kijelöléséhez törölje a jelet a jelölőnégyzetből.

Az irányítópult közzététele most már meg van téve. Ha az előfizetéstől örökölt engedélyek megfelelőek, nem kell többet tennie. A szervezet más felhasználói is elérhetik és módosíthatják az irányítópultot az előfizetési szintű szerepkörük alapján.

## <a name="assign-access-to-a-dashboard"></a>Hozzáférés hozzárendelése irányítópulthoz

Az irányítópult szerepköréhez felhasználók egy csoportját rendelheti hozzá.

1. Az irányítópult közzététele után válassza a **Megosztás** megosztása vagy **a Megosztás megszüntetése** lehetőséget a Megosztás + **hozzáférés-vezérlés**eléréséhez.

1. A **Megosztás + hozzáférés-vezérlés csoportban**válassza **a Felhasználók kezelése**lehetőséget.

    ![irányítópult felhasználóinak kezelése](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

1. Válassza a **szerepkör-hozzárendelések** lehetőséget az irányítópulthoz már hozzárendelt meglévő felhasználók megtekintéséhez.

1. Új felhasználó vagy csoport hozzáadásához válassza a **Hozzáadás,** majd **a Szerepkör-hozzárendelés hozzáadása**lehetőséget.

    ![felhasználó hozzáadása az irányítópulthoz való hozzáféréshez](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. Válassza ki azt a szerepkört, amely az engedélyezendő engedélyeket jelöli. Ebben a példában válassza a **Közreműködő**lehetőséget.

1. Válassza ki a szerepkörhöz rendelni kívánt felhasználót vagy csoportot. Ha nem látja a keresett felhasználót vagy csoportot a listában, használja a keresőmezőt. Az elérhető csoportok listája az Active Directoryban létrehozott csoportoktól függ.

1. Ha befejezte a felhasználók vagy csoportok hozzáadását, válassza a **Mentés lehetőséget.**

## <a name="next-steps"></a>További lépések

* A szerepkörök listáját az [Azure-erőforrások beépített szerepkörei című témakörben található.](../role-based-access-control/built-in-roles.md)
* Az erőforrások kezeléséről az [Azure-erőforrások kezelése az Azure Portal használatával című témakörben](resource-group-portal.md)olvashat.

