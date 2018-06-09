---
title: Az Azure PIM erőforrás RBAC áttekintése |} Microsoft Docs
description: Az RBAC funkció áttekintést kaphat a PIM, beleértve a fontos Szakszavai és értesítések
services: active-directory
documentationcenter: ''
author: barclayn
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: 304a06dee04b405c7e7acb2c73b31af426591d1d
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233288"
---
# <a name="pim-for-azure-resources"></a>Az Azure-erőforrások PIM

Az Azure Active Directory Privileged Identity Management (PIM), kezelheti, felügyeletére és figyelésére elérhető Azure erőforrások a szervezeten belül. Ez magában foglalja, előfizetések, erőforrás-csoportok és még a virtuális gépek. Az Azure portálon, amely kihasználja az Azure szerepköralapú hozzáférés vezérlés (RBAC) bármilyen olyan erőforrás kihasználhatja az összes nagy biztonsági és életciklus-kezelési képességei Azure AD PIM nyújtotta előnyöket, és néhány nagyszerű új szolgáltatásokat tervezzük kerüljön Az Azure AD szerepkörök hamarosan. 

## <a name="pim-for-azure-resources-helps-resource-administrators"></a>Az Azure-erőforrások PIM erőforrás rendszergazdáit segíti a

- Tekintse meg, mely felhasználók és csoportok vannak hozzárendelve a felügyelt Azure-erőforrások szerepkörei
- Igény szerinti, mint az előfizetések, erőforráscsoportok, erőforrások kezeléséhez "csak az időben" hozzáférés engedélyezése
- Jár le automatikusan az új időhöz kötött hozzárendelés beállítások hozzárendelt felhasználók/csoportok erőforrások elérése
- Gyors tevékenységek és a készenléti ütemezések ideiglenes erőforrás-hozzáférés hozzárendelése
- Többtényezős hitelesítés kikényszerítéséhez az erőforrások eléréséhez a beépített és egyéni szerepkör 
- Erőforrás-hozzáférési korrelált erőforrás tevékenységre vonatkozó jelentések lekérése során a felhasználó aktív munkamenetét
- Riasztásokat kaphat, ha új felhasználók vagy csoportok vannak hozzárendelve az erőforrás-hozzáférés, illetve ha az általuk aktivált jogosult hozzárendelések

Az Azure AD PIM a beépített Azure erőforrás-szerepköröket, valamint a egyéni (RBAC) szerepkörök, például (de nem kizárólagosan) képes kezelni:

- Tulajdonos
- Felhasználói hozzáférés rendszergazdája
- Közreműködő
- Biztonsági rendszergazda
- Biztonsági és egyéb

>[!NOTE]
Felhasználók vagy a tulajdonos vagy a felhasználói hozzáférés adminisztrátora szerepkört, és, amelyek lehetővé teszik az előfizetés-kezelés az Azure AD globális rendszergazdák csoport tagjai az erőforrás-rendszergazdák. A rendszergazdák előfordulhat, hogy szerepköröket, szerepkör beállításainak konfigurálása, és tekintse át a PIM használó Azure-erőforrások hozzáférés. A lista megtekintése [beépített szerepkörök az Azure-erőforrások](../../role-based-access-control/built-in-roles.md).

## <a name="tasks"></a>Feladatok

A PIM szerepkörök aktiválása, függőben lévő aktiválások/kérelmek, függőben lévő jóváhagyások megtekintése kényelmes hozzáférést biztosít (az [az Azure Active directory szerepkörök](azure-ad-pim-approval-workflow.md)), és ellenőrzi, hogy a válasz a bal oldali navigációs menü feladatok részéből függőben.

Elérésekor a feladatok menü elemek bármelyikét a áttekintése belépési pontról, az eredményül kapott nézet tartalmazza az Azure Active directory szerepkörök, mind az Azure erőforrás-szerepkörök eredménye. 

![](media/azure-pim-resource-rbac/role-settings-details.png)

A szerepkörök az Azure Active directory szerepkörök és az Azure erőforrás-szerepkörök aktív és a megfelelő szerepkör-hozzárendelések listáját tartalmazzák.

## <a name="activate-roles"></a>Szerepkörök aktiválása

Szerepkörök aktiválása az Azure-erőforrások bevezet egy új felület, amely lehetővé teszi az aktiválási a jövőbeni dátum/idő ütemezése, és válasszon egy adott aktiválási időtartamot a legnagyobb (a rendszergazdák által konfigurált) belül jogosult szerepkör tagjai. További tudnivalók [itt az Azure AD-szerepkörök aktiválása](../active-directory-privileged-identity-management-how-to-activate-role.md).

![](media/azure-pim-resource-rbac/contributor.png)

Az aktiválások menüből adjon meg a kívánt kezdő dátum és idő, a szerepkör aktiválásához. Opcionálisan csökkentése az aktiválás időtartama (mennyi ideig a szerepköre aktív), és ha szükséges; indoklásának beírásához Kattintson az aktiválás gombra.

A kezdő dátum és idő nem módosul, ha a szerepkör aktiválódik másodpercen belül. Egy szerepkör aszinkron aktiválás szalagcím üzenet a saját szerepkörök lapon jelenik meg. Kattintson a frissítés gombra kattintva törölheti ezt az üzenetet.

![](media/azure-pim-resource-rbac/my-roles.png)

Ha az aktiválás dátuma jövőbeli időpontra van ütemezve, a függőben lévő kérelem függőben lévő kérelmek fülre, a bal oldali navigációs menü megjelenik. Abban az esetben, ha a szerepkör aktiválása már nincs szükség, a felhasználó megszakítja a kérést a lap jobb oldalán a Mégse gombra kattintva.

![](media/azure-pim-resource-rbac/pending-requests.png)

## <a name="discover-and-manage-azure-resources"></a>Felderítése és felügyelete az Azure-erőforrások

Található, és szerepkörök kezelése az Azure-erőforrás, válassza ki az Azure-erőforrások a kezelés lapon, a bal oldali navigációs menü. A szűrők vagy használatával keresősáv az oldal tetején található erőforrás.

![](media/azure-pim-resource-rbac/azure-resources.png)

## <a name="resource-dashboards"></a>Erőforrás-irányítópultok

A felügyelet nézet irányítópult négy elsődleges összetevőből áll. Grafikus ábrázolása erőforrás szerepkör aktiválások az elmúlt hét napban. Az adatok hatóköre a kiválasztott erőforrás a, és megjeleníti a leggyakrabban használt szerepkörök (tulajdonos, közreműködő, felhasználói hozzáférés adminisztrátora) és az összes szerepkör kombinált aktiválást.

Az a diagram jobb oldalán aktiválások van két diagramot, amely a szerepkör-hozzárendelések terjesztési hozzárendelés-típus, a felhasználókat és csoportokat egyaránt megjeleníthető. Válassza a diagram szelet módosítja százalékos (vagy fordítva) értékét.

![](media/azure-pim-resource-rbac/admin-view.png)

Alább a diagramok a felhasználókat és csoportokat az új szerepkör-hozzárendelések száma az elmúlt 30 napban (bal oldali) és a teljes hozzárendelések (csökkenő) szerint rendezve szerepkörök listáját láthatja.

![](media/azure-pim-resource-rbac/role-settings.png)

## <a name="manage-role-assignments"></a>Szerepkör-hozzárendelések kezelése

Rendszergazdák kezelhetik a szerepkör-hozzárendelések szerepkörök vagy a tagot a bal oldali navigációs való kiválasztással. Szerepkörök kiválasztásával lehetővé teszi, hogy a rendszergazdák számára, hogy a fájlkezelési feladatokat egy adott szerepkör hatóköre, amíg tagok megjeleníti az összes felhasználó- és szerepkör-hozzárendelések az erőforrás.

![](media/azure-pim-resource-rbac/roles.png)

![](media/azure-pim-resource-rbac/members.png)

>[!NOTE]
Ha van függőben lévő aktiválási szerepkör, értesítésszalagról jelenik meg a lap tetején tagsági megtekintésekor.

## <a name="assign-roles"></a>Szerepkörök hozzárendelése

Egy felhasználó vagy csoport hozzárendelése egy szerepkörhöz, válassza ki a szerepkört (Ha a szerepkörök megtekintése), vagy kattintson a Hozzáadás gombra az műveletsávon (Ha a tagok megtekintése).

![](media/azure-pim-resource-rbac/members2.png)

>[!NOTE]
Egy felhasználó vagy csoport hozzáadása a tagok lapon, ha szüksége egy felhasználó vagy csoport kiválasztása előtt válasszon egy szerepkör hozzáadása menüjéből.

![](media/azure-pim-resource-rbac/select-role.png)

Válasszon egy felhasználót vagy csoportot a könyvtárból.

![](media/azure-pim-resource-rbac/choose.png)

Válassza ki a megfelelő hozzárendelési típusát a legördülő menüből. 

**Csak az idő-hozzárendelési:** azt a felhasználó vagy csoport tagjai támogatható, de nem állandó hozzáférést biztosít a szerepkör egy megadott ideig idő vagy korlátlan ideig (Ha be van állítva, a szerepkör beállításai). 

**Közvetlen hozzárendelés:** nem követeli meg a felhasználó vagy csoport tagjai (más néven állandó hozzáférés) szerepkör-hozzárendelés aktiválásához. A Microsoft azt javasolja, hogy közvetlen hozzárendelés használatával elmozdul a telefonos vagy bizalmas tevékenységek, például a rövid távú használatra, ahol hozzáférés nem lesz szükség a feladat befejezésekor.

![](media/azure-pim-resource-rbac/membership-settings.png)

A hozzárendelés típusa legördülő lista alatt egy jelölőnégyzet teszi adja meg, ha a hozzárendelés állandó kell lennie (csak az idő hozzárendelés/véglegesen aktív aktiválásához közvetlen hozzárendelés véglegesen jogosult). Adjon meg egy adott hozzárendelés időtartama, törölje a jelölőnégyzet és módosítása, illetve befejező dátum-és.

>[!NOTE]
Lehet, hogy a jelölőnégyzet unmodifiable, ha egy másik rendszergazda adta meg a maximális hozzárendelés időtartama, az egyes hozzárendelés szerepkör beállításai.

![](media/azure-pim-resource-rbac/calendar.png)

## <a name="view-activation-and-azure-resource-activity"></a>Aktiválás és az Azure erőforrás-tevékenység megtekintése

Abban az esetben, ha egy adott felhasználó elvégez a különböző erőforrások mely műveletek van szüksége, áttekintheti a megadott aktiválási időszak (a jogosult felhasználók) társított Azure-erőforrás tevékenység. Indítsa el a tagok megtekintése vagy egy adott szerepkör tagjainak listája a felhasználó megadásával. Az eredmény a dátum a felhasználó Azure-erőforrások műveleteket, és a legutóbbi szerepkör aktiválások, hogy ugyanazon időszakra grafikus nézetet jelenít meg.

![](media/azure-pim-resource-rbac/user-details.png)

Egy adott szerepkör aktiválása kiválasztásával jelennek meg a szerepkör aktiválása részleteit és a megfelelő Azure-erőforrás tevékenység történt, hogy a felhasználó active.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="modify-existing-assignments"></a>Módosítsa a meglévő hozzárendelést

A felhasználó vagy csoport részletes nézete a meglévő hozzárendelést módosításához válassza a beállítások módosítása az műveletsávon az oldal tetején. Módosítsa a hozzárendelés-típus csak az idő-hozzárendelés vagy rendelését.

![](media/azure-pim-resource-rbac/change-settings.png)

## <a name="review-who-has-access-in-a-subscription"></a>Tekintse át, aki hozzáféréssel rendelkezik az előfizetés

Tekintse át a szerepkör-hozzárendelések az előfizetéshez, a tagok lapon válassza a bal oldali navigációs sávon, vagy jelölje ki a szerepköröket, és válasszon egy adott szerepkör tagjai áttekintése. 

Válassza ki az műveletsávon Hozzáadás hozzon létre egy új tekintse át a meglévő hozzáférési értékelést megnézni és tekintse át.

![](media/azure-pim-resource-rbac/owner.png)

[További tudnivalók a hozzáférés-ellenőrzés](../active-directory-privileged-identity-management-how-to-perform-security-review.md)

>[!NOTE]
Ellenőrzések csak támogatottak előfizetés erőforrástípusok most.

## <a name="configure-role-settings"></a>Felhasználóiszerep-beállítások konfigurálása

Szerepkör beállításainak konfigurálásakor adja meg az alapértelmezett beállításokat alkalmazza a PIM környezetben hozzárendeléseket. Adja meg, ezek az erőforrás, válassza a beállítások lapon a bal oldali navigációs, vagy a szerepkör-beállítások gombra az műveletsávon bármely szerepkör a jelenlegi beállítások megtekintéséhez.

Az műveletsávon a lap tetején kattintson a Szerkesztés lehetővé teszi az egyes beállítások módosítását.

![](media/azure-pim-resource-rbac/owner.png)

![](media/azure-pim-resource-rbac/owner02.png)

Beállítások módosításait is be vannak jelentkezve a szerepkör-beállítások lapon, beleértve az utolsó frissítés dátuma idő és a rendszergazdának, hogy a beállításai módosultak.

![](media/azure-pim-resource-rbac/role-settings-02.png)

## <a name="resource-audit"></a>Erőforrás naplózása

Erőforrás naplózási lehetővé teszi az összes szerepkör tevékenység az erőforrás nézetét. Az előre definiált dátum vagy egyéni tartomány információkat szűrheti is.
![](media/azure-pim-resource-rbac/last-day.png) Erőforrás naplózási is megtekintheti a felhasználói tevékenység részletei gyors hozzáférést biztosít. A nézetben minden "Szerepkör aktiválásához" műveletek az adott kérelmezőnek erőforrástevékenység mutató hivatkozásokat tartalmaz.
![](media/azure-pim-resource-rbac/resource-audit.png)

## <a name="just-enough-administration"></a>Éppen elég felügyelettel

A éppen elegendő (JEA) felügyeleti gyakorlati tanácsok a erőforrás szerepkör-hozzárendelések érték használata egyszerű, Azure-erőforrások PIM. Felhasználók és az Azure-előfizetésekhez vagy erőforrás-csoportok a csoport tagjai aktiválhatják a meglévő szerepkör-hozzárendelés csökkentett hatókörben. 

A lapon, a kezeléséhez szükséges alárendelt erőforrás található.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Jelöljön ki a bal oldali navigációs menü a szerepkört, és válassza ki a megfelelő szerepkört aktiválja. Figyelje meg a hozzárendelés-típus örökölt, mert a szerepkör a lent látható módon az erőforráscsoportot, hanem az előfizetés kapott.

![](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>További lépések

- [Beépített szerepkörök az Azure-erőforrások](../../role-based-access-control/built-in-roles.md)
- További tudnivalók [itt az Azure AD-szerepkörök aktiválása](../active-directory-privileged-identity-management-how-to-activate-role.md)
- [A PIM jóváhagyási munkafolyamatok](azure-ad-pim-approval-workflow.md)
