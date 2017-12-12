---
title: "Ossza meg az Azure portál irányítópultok RBAC |} Microsoft Docs"
description: "Ez a cikk azt ismerteti, hogyan az Azure-portálon az irányítópultok megosztása a szerepköralapú hozzáférés-vezérlés használatával."
services: azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2016
ms.author: tomfitz
ms.openlocfilehash: 33ddfef1e23a6ff0c2cb8e359d408d2c42cbcf3a
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Azure irányítópultok megosztása a szerepköralapú hozzáférés-vezérlés használatával
Miután egy irányítópultot, tegye közzé, és megoszthatják más felhasználókkal a szervezetében. Engedélyezi a többi Azure használatával az irányítópult megtekintéséhez [szerepköralapú hozzáférés-vezérlés](../active-directory/role-based-access-control-configure.md). Egy felhasználó vagy csoport számára hozzárendelése egy szerepkörhöz, és ez a szerepkör határozza meg, hogy ezek a felhasználók megtekintheti és módosíthatja a közzétett irányítópulton. 

Az összes közzétett irányítópultok Azure-erőforrások, mint kerülnek végrehajtásra, ami azt jelenti, hogy az előfizetés kezelhető elemeiként létezik, és egy erőforráscsoport található.  Egy hozzáférés-vezérlő szempontjából irányítópultok ugyanazok, mint más erőforrások, például egy virtuális gép vagy egy tárfiókot.

> [!TIP]
> Egyes csempéit az irányítópultra kényszerítése a saját hozzáférés-vezérlési követelményeinek, a megjelenő erőforrások alapján.  Ezért egy irányítópultot, miközben továbbra biztosítva az egyes csempék lévő adatok védelme körben megosztott kialakítani.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Irányítópultok ismertetése hozzáférés-vezérlés
A szerepköralapú hozzáférés-vezérlés (RBAC), felhasználók hozzárendelheti a szerepkörökhöz hatókör három különböző szinteken:

* előfizetést
* erőforráscsoport
* erőforrás

A megadott engedélyeknek örökölt le az erőforrás-előfizetésből. A közzétett irányítópulton erőforrás. Emiatt előfordulhat, hogy már felhasználók rendelve az előfizetéshez tartozó szerepkörök, amelyek a közzétett irányítópult is működik. 

Íme egy példa.  Tegyük fel, Azure-előfizetéssel rendelkezik, és a csoport tagjai különféle hozzárendelt szerepkörök adott biztonsági **tulajdonos**, **közreműködő**, vagy **olvasó** az előfizetéshez. Felhasználók, akik a tulajdonos vagy közreműködő szerepkörrel rendelkező személyek képesek listában, megtekintése, létrehozása, módosítása vagy törlése irányítópultok előfizetésen belül.  Olvasók felhasználók lehet lista, és tekintse meg az irányítópultok, de nem módosítsa vagy törölje őket.  Olvasási joggal rendelkező felhasználók csak közzétett irányítópulton helyi módosításokat végezhetnek (például, amikor egy probléma), azonban nem tudja közzétenni az ezeket a módosításokat a kiszolgálóra.  Az irányítópult titkos másolatot készíteni maguknak is rendelkeznek

Azonban akkor is hozzárendelheti engedélyek több irányítópultokat tartalmazó erőforráscsoportot, vagy egy egyéni irányítópultot. Dönthet például úgy, hogy a felhasználók egy csoportjánál kell korlátozott engedélyekkel az előfizetés, de nagyobb mértékű hozzáférést egy adott irányítópult között. Ezek a felhasználók hozzárendelése az irányítópult a szerepkörhöz. 

## <a name="publish-dashboard"></a>Irányítópult közzététele
Tegyük fel, hogy az előfizetésben a felhasználók egy csoportjánál megosztani kívánt irányítópult beállítása után. Az alábbi lépéseket jelzik a tárolási kezelők nevű egyéni csoport, de nevet adhat a csoport függetlenül szeretné. Az Active Directory-csoport létrehozása és a csoporthoz tartozó felhasználók hozzáadásával kapcsolatos információkért lásd: [csoportkezelés az Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

1. Az irányítópulton, válassza ki a **megosztás**.
   
     ![Válasszon megosztást](./media/azure-portal-dashboard-share-access/select-share.png)
2. Hozzáférés engedélyezése előtt közzé kell tennie az irányítópulton. Alapértelmezés szerint az irányítópult közzéteendő nevű erőforráscsoport **irányítópultok**. Válassza ki **közzététele**.
   
     ![publish](./media/azure-portal-dashboard-share-access/publish.png)

Az irányítópult már közzé van téve. Ha az előfizetés az örökölt engedélyeket megfelelő, nem kell további teendője. A munkahely más felhasználóinak tudni érni és módosítani az irányítópult az előfizetés szintű szerepkör lesz. Azonban ebben az oktatóanyagban most hozzárendelése a felhasználók egy csoportjánál az irányítópult szerepkört.

## <a name="assign-access-to-a-dashboard"></a>Hozzáférést biztosít egy irányítópult
1. Miután közzétette az irányítópulton, válassza ki a **felhasználók kezelése**.
   
     ![felhasználók kezelése](./media/azure-portal-dashboard-share-access/manage-users.png)
2. Látni fogja, amely már hozzá vannak rendelve egy szerepkört ehhez az irányítópulthoz a meglévő felhasználók listáját. A meglévő felhasználók listáját az alábbi képen eltérő lesz. A hozzárendelések a legvalószínűbb ok az, az előfizetés öröklik. Új felhasználó vagy csoport hozzáadásához válassza **Hozzáadás**.
   
     ![felhasználó hozzáadása](./media/azure-portal-dashboard-share-access/existing-users.png)
3. Válassza ki a szerepkör, amely jelöli az engedélyeket szeretne megadni. Ehhez a példához válassza ki a **közreműködő**.
   
     ![szerepkör kiválasztása](./media/azure-portal-dashboard-share-access/select-role.png)
4. Válassza ki a felhasználó vagy csoport hozzárendelése a szerepkörhöz használni kívánt. Ha nem látja a felhasználó vagy csoport listájában keresi, használja a keresőmezőt. Az Active Directory létrehozott csoportok függ a rendelkezésre álló csoportok listájából.
   
     ![felhasználó kiválasztása](./media/azure-portal-dashboard-share-access/select-user.png) 
5. Ha befejezte a felhasználók vagy csoportok hozzáadása, válassza ki a **OK**. 
6. Az új hozzárendelés hozzáadódik a felhasználók listáját. Figyelje meg, hogy a **hozzáférés** jelenik meg, **hozzárendelt** helyett **örökölt**.
   
     ![rendelt szerepkörök](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>Következő lépések
* A szerepkörök listájáért lásd: [RBAC: beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md).
* Erőforrások kezelésével kapcsolatos információkért lásd: [kezelése Azure-erőforrások portálon keresztül](resource-group-portal.md).

