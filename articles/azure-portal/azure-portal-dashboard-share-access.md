---
title: Azure Portal irányítópultok megosztása a RBAC használatával | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan oszthat meg egy irányítópultot a Azure Portal szerepköralapú Access Control használatával.
services: azure-portal
documentationcenter: ''
author: mblythe
manager: mtillman
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2016
ms.author: mblythe
ms.openlocfilehash: da983a6a3c86be87f1a24b67252a40adac2fa59d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75641384"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Azure-irányítópultok megosztása a szerepköralapú hozzáférés-vezérlés használatával

Az irányítópult konfigurálását követően közzéteheti és megoszthatja azt a szervezet más felhasználóival. Lehetővé teszi mások számára az irányítópult megtekintését az Azure [szerepköralapú Access Control](../role-based-access-control/role-assignments-portal.md)használatával. Felhasználót vagy felhasználói csoportot rendelhet egy szerepkörhöz, és ez a szerepkör határozza meg, hogy a felhasználók megtekinthetik vagy módosíthatják a közzétett irányítópultot. 

Az összes közzétett irányítópult Azure-erőforrásként van megvalósítva, ami azt jelenti, hogy az előfizetésben kezelhető elemekként vannak tárolva, és egy erőforráscsoport tartalmazza őket.  Hozzáférés-vezérlési perspektívából az irányítópultok nem különböznek más erőforrásokkal, például egy virtuális géppel vagy egy Storage-fiókkal.

> [!TIP]
> Az irányítópulton lévő csempék a saját hozzáférés-vezérlési követelményeiket a megjelenített erőforrások alapján kényszerítik ki.  Ezért megtervezheti a széles körben megosztott irányítópultokat, miközben továbbra is védi az adatok védelmét az egyes csempék esetében.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Az irányítópultok hozzáférés-vezérlésének ismertetése
Szerepköralapú Access Control (RBAC) esetén a felhasználók a hatókör három különböző szintjén oszthatók ki szerepkörökhöz:

* előfizetést
* erőforráscsoport
* erőforrás

A hozzárendelt engedélyek öröklik az előfizetésből az erőforrásra. A közzétett irányítópult egy erőforrás. Ezért előfordulhat, hogy az előfizetéshez tartozó, a közzétett irányítópulton is működő szerepkörökhöz hozzárendelt felhasználók vannak. 

Például:  Tegyük fel, hogy rendelkezik Azure-előfizetéssel, és a csapat különböző tagjai az előfizetéshez tartozó **tulajdonos**, **közreműködő**vagy **olvasó** szerepkört kaptak. Azok a felhasználók, akik tulajdonos vagy közreműködők, irányítópultokat listáznak, tekinthetnek meg, hozhatnak létre, módosíthatnak vagy törölhetnek az előfizetésen belül.  Azok a felhasználók, akik az olvasók számára képesek listázni és megtekinteni az irányítópultokat, de nem módosíthatják és nem törölhetik azokat.  Az olvasói hozzáféréssel rendelkező felhasználók helyi módosításokat végezhetnek egy közzétett irányítópulton (például egy probléma hibaelhárításakor), de a módosításokat nem tehetik vissza a kiszolgálóra.  Lehetőségük van arra, hogy saját maguk is elvégezzék az irányítópult privát másolatát

Ugyanakkor engedélyeket is hozzárendelhet az erőforráscsoporthoz, amely több irányítópultot vagy egy egyéni irányítópultot tartalmaz. Dönthet például úgy, hogy egy adott felhasználói csoportnak korlátozott engedélyekkel kell rendelkeznie az előfizetésben, de nagyobb hozzáférésre van szüksége egy adott irányítópulthoz. Ezeket a felhasználókat hozzárendelheti egy szerepkörhöz az irányítópulton. 

## <a name="publish-dashboard"></a>Irányítópult közzététele
Tegyük fel, hogy befejezte egy olyan irányítópult konfigurálását, amelyet szeretne megosztani az előfizetésében lévő felhasználói csoporttal. Az alábbi lépések a Storage Managers nevű testreszabott csoportot ábrázolják, de a csoport nevét tetszőlegesen megadhatja. Active Directory csoport létrehozásával és a felhasználók adott csoporthoz való hozzáadásával kapcsolatos információkért lásd: [csoportok kezelése a Azure Active Directoryban](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

1. Az irányítópulton válassza a **megosztás**lehetőséget.
   
     ![megosztás kiválasztása](./media/azure-portal-dashboard-share-access/select-share.png)
2. A hozzáférés kiosztása előtt közzé kell tennie az irányítópultot. Alapértelmezés szerint az irányítópultot az **irányítópultok**nevű erőforráscsoport teszi közzé. Kattintson a **Publish** (Közzététel) elemre.
   
     ![publish](./media/azure-portal-dashboard-share-access/publish.png)

Az irányítópult már közzé van téve. Ha az előfizetésből örökölt engedélyek megfelelőek, semmit nem kell tennie. A szervezet más felhasználói hozzáférhetnek az irányítópulthoz, és módosíthatják azt az előfizetési szint szerepkörük alapján. Ebben az oktatóanyagban azonban egy felhasználói csoportot rendelünk hozzá egy szerepkörhöz az irányítópulton.

## <a name="assign-access-to-a-dashboard"></a>Irányítópulthoz való hozzáférés kiosztása
1. Az irányítópult közzététele után válassza a **felhasználók kezelése**lehetőséget.
   
     ![felhasználók kezelése](./media/azure-portal-dashboard-share-access/manage-users.png)
2. Megjelenik azoknak a meglévő felhasználóknak a listája, akik már hozzá vannak rendelve egy szerepkörhöz ehhez az irányítópulthoz. A meglévő felhasználók listája eltér az alábbi képen. Legvalószínűbb, hogy a hozzárendelések öröklik az előfizetést. Új felhasználó vagy csoport hozzáadásához válassza a **Hozzáadás**lehetőséget.
   
     ![felhasználó hozzáadása](./media/azure-portal-dashboard-share-access/existing-users.png)
3. Válassza ki azt a szerepkört, amely a megadni kívánt engedélyeket képviseli. Ebben a példában válassza a **közreműködő**lehetőséget.
   
     ![szerepkör kiválasztása](./media/azure-portal-dashboard-share-access/select-role.png)
4. Válassza ki azt a felhasználót vagy csoportot, amelyet hozzá szeretne rendelni a szerepkörhöz. Ha nem látja a keresett felhasználót vagy csoportot a listában, használja a keresőmezőt. Az elérhető csoportok listája a Active Directoryban létrehozott csoportokra fog támaszkodni.
   
     ![felhasználó kiválasztása](./media/azure-portal-dashboard-share-access/select-user.png) 
5. Ha befejezte a felhasználók vagy csoportok hozzáadását, kattintson **az OK gombra**. 
6. A rendszer hozzáadja az új hozzárendelést a felhasználók listájához. Figyelje meg, hogy a **hozzáférése** az **örökölt**helyett **hozzárendelve** van felsorolva.
   
     ![hozzárendelt szerepkörök](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>Következő lépések
* A szerepkörök listáját lásd [: RBAC: beépített szerepkörök](../role-based-access-control/built-in-roles.md).
* Az erőforrások kezelésével kapcsolatos további információkért lásd: [Azure-erőforrások kezelése a portálon keresztül](resource-group-portal.md).

