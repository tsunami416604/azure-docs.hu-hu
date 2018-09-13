---
title: Az RBAC használatával az Azure portal irányítópult megosztása |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Portal irányítópult megosztása szerepköralapú hozzáférés-vezérlés használatával.
services: azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2016
ms.author: tomfitz
ms.openlocfilehash: c07a9d92cac13d6325e66f44426f1a64e8ac53cb
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35923656"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Azure-irányítópultok megosztása szerepköralapú hozzáférés-vezérlés használatával
Miután egy irányítópultot, tegye közzé, és megoszthatja más felhasználók a szervezetben. Engedélyezi másoknak, hogy az irányítópult megtekintéséhez használja az Azure [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md). Egy felhasználó vagy felhasználók csoportja rendelhet egy szerepkört, és a szerepkör határozza meg, hogy ezek a felhasználók megtekintheti vagy módosíthatja a közzétett irányítópultot. 

Az összes közzétett irányítópultok implementálhatók Azure-erőforrások, ami azt jelenti, hogy jelen vannak az előfizetésen belüli a felügyelhető elemek és a egy erőforráscsoportban található.  Access control szempontból az irányítópultok ugyanazok, mint más erőforrások, például egy virtuális gép vagy a storage-fiók.

> [!TIP]
> Az irányítópulton lévő egyes csempék kényszerítése a saját hozzáférés-vezérlési követelményeinek megjelenő erőforrások alapján.  Ezért miközben továbbra is védi az adatokat az egyes csempékhez széles körben megosztott irányítópult is tervezhet.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Az irányítópultok hozzáférés-vezérlés ismertetése
A szerepköralapú hozzáférés vezérlése (RBAC), hozzárendelheti felhasználói szerepkörök hatókörének három különböző szinteken:

* előfizetést
* erőforráscsoport
* erőforrás

Az engedélyek hozzárendelése a erőforrás le előfizetés öröklődnek. A közzétett irányítópultot egy erőforráshoz. Emiatt előfordulhat, hogy már rendelkezik tartozó felhasználók számára a közzétett irányítópultot is működnek szerepkört az előfizetés. 

Íme egy példa.  Tegyük fel, az Azure-előfizetéssel rendelkezik, és a csapat tagjai különféle van hozzájuk rendelve a szerepköröket **tulajdonosa**, **közreműködői**, vagy **olvasó** az előfizetéshez. Tulajdonos vagy közreműködő felhasználók listában, megtekintése, létrehozása, módosítása vagy törlése az előfizetésen belüli irányítópultok képesek.  Felhasználók, akik olvasó lehet listázása és megtekintése az irányítópultok, de nem módosíthatja vagy törölheti őket.  Olvasó hozzáféréssel rendelkező felhasználók képesek-e helyi szerkesztenie közzétett irányítópultot (például, amikor egy probléma elhárításának), de nem tudja közzétenni az ezeket a módosításokat a kiszolgálóra.  A beállítás, az irányítópult egy privát másolatot maguk rendelkeznek

Azonban is hozzárendelhet engedélyeket egy adott irányítópult vagy a több irányítópultok tartalmazó erőforráscsoportot. Például dönthet úgy, hogy a felhasználók egy csoportjánál kell korlátozott engedélyekkel rendelkeznek az előfizetés, de nagyobb a hozzáférést egy adott irányítópult között. Felhasználók hozzárendelése az irányítópult a szerepkörhöz. 

## <a name="publish-dashboard"></a>Irányítópult közzététele
Tegyük fel, hogy meg szeretné osztani egy csoportot az előfizetésében irányítópult beállítása után. Az alábbi lépéseket jelzik a Storage-kezelők nevű egyéni csoport, de nevet adhat a csoport függetlenül szeretné. További információ az Active Directory csoport létrehozásával és felhasználók hozzáadása ehhez a csoporthoz: [csoportkezelés az Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

1. Az irányítópulton, válassza ki a **megosztás**.
   
     ![Válassza ki a megosztást](./media/azure-portal-dashboard-share-access/select-share.png)
2. Hozzáférés hozzárendelése előtt közzé kell tennie az irányítópulton. Alapértelmezés szerint az irányítópult lesz közzétéve nevű erőforráscsoport **irányítópultok**. Kattintson a **Publish** (Közzététel) elemre.
   
     ![publish](./media/azure-portal-dashboard-share-access/publish.png)

Az irányítópult most már közzé van téve. Ha az előfizetés az örökölt engedélyekkel megfelelő, nem kell több teendője. A szervezet más felhasználói elérheti és módosíthatja az irányítópulton előfizetési szintű szerepkörük alapján lesz. Azonban ebben az oktatóanyagban most rendelje hozzá a felhasználók egy csoportjánál egy szerepkörhöz az irányítópulton.

## <a name="assign-access-to-a-dashboard"></a>Hozzáférés hozzárendelése egy irányítópulton
1. Miután közzétette az irányítópulton, válassza ki a **felhasználók kezelése**.
   
     ![felhasználók kezelése](./media/azure-portal-dashboard-share-access/manage-users.png)
2. Látni fogja, hogy már hozzárendelt egy szerepkört ehhez az irányítópulthoz a meglévő felhasználók listáját. A meglévő felhasználók listáját az alábbi képen eltérő lesz. Nagy valószínűséggel az előfizetéshez a hozzárendeléseket öröklődnek. Új felhasználó vagy csoport hozzáadásához válassza **Hozzáadás**.
   
     ![Felhasználó hozzáadása](./media/azure-portal-dashboard-share-access/existing-users.png)
3. Válassza ki a szerepkört, amely jelöli az engedélyeket, amelyet szeretne megadni. Ebben a példában válassza **közreműködői**.
   
     ![szerepkör kiválasztása](./media/azure-portal-dashboard-share-access/select-role.png)
4. Válassza ki a felhasználót vagy csoportot, amelyet hozzá kíván rendelni a szerepkörhöz. Ha nem látja a felhasználót vagy csoportot a listában keres, használja a keresőmezőt. A rendelkezésre álló csoportok listájából a az Active Directoryban létrehozott csoportok függ.
   
     ![felhasználó kiválasztása](./media/azure-portal-dashboard-share-access/select-user.png) 
5. Amikor befejezte a felhasználókkal vagy csoportokkal bővítheti, válassza ki **OK**. 
6. Az új hozzárendelésnek hozzáadódik a felhasználók listáját. Figyelje meg, hogy a **hozzáférés** állapottal **hozzárendelt** helyett **örökölt**.
   
     ![hozzárendelt szerepkörök](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>További lépések
* Szerepkörök listáját lásd: [RBAC: beépített szerepkörök](../role-based-access-control/built-in-roles.md).
* Erőforrások kezelésével kapcsolatos további információkért lásd: [Azure-erőforrások a portál](resource-group-portal.md).

