---
title: Az Azure PIM erőforrás RBAC – áttekintés |} A Microsoft Docs
description: Az RBAC-funkció áttekintést kaphat a PIM, beleértve a terminológia és értesítések
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: 7cf628495a79fe775528080ae6ec31df8e9a0f37
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37447581"
---
# <a name="pim-for-azure-resources"></a>A PIM használata Azure-erőforrások

Az Azure Active Directory Privileged Identity Management (PIM), kezelheti, szabályozhatja és figyelheti a Azure-erőforrások a szervezeten belül. Ez magában foglalja az előfizetések, erőforráscsoportok és még a virtuális gépeket. Az Azure Portalon, amely kihasználja az Azure szerepkör alapú hozzáférés-vezérlés (RBAC) bármely erőforrás kihasználhatja összes nagyszerű biztonsági és életciklus-kezelési képességei Azure AD PIM által biztosított lehetőségeket, és ahhoz, hogy tervezzük nagyszerű új funkciókkal Az Azure AD-szerepkörök hamarosan. 

## <a name="pim-for-azure-resources-helps-resource-administrators"></a>Az Azure-erőforrások a PIM erőforrás rendszergazdáit segíti a

- Tekintse meg, milyen felhasználókkal és csoportokkal hozzárendelt szerepkörök felügyelt Azure-erőforrások
- Igény szerinti, például az előfizetések, erőforráscsoportok és egyéb erőforrások kezelése "csak az időben" hozzáférés engedélyezése
- Hozzárendelt felhasználók/csoportok erőforrás-hozzáférés automatikusan az új időhöz kötött hozzárendelési beállítások elévülése
- Gyors feladatok vagy a készenléti ütemezések ideiglenes erőforrás-hozzáférés hozzárendelése
- Az erőforrások eléréséhez a beépített vagy egyéni szerepkörhöz a többtényezős hitelesítés kényszerítése 
- Aktív felhasználói munkamenet során resource access korrelált erőforrás tevékenységre vonatkozó jelentések lekérése
- Értesítéseket kaphat az új felhasználók vagy csoportok hozzárendelése esetén erőforrásokhoz való hozzáférést, és ha azok a jogosult hozzárendelések aktiválása

Az Azure AD PIM-ben a beépített Azure-erőforrásszerepkörök, valamint a egyéni (RBAC) szerepkört, beleértve többek között a (,) képes kezelni:

- Tulajdonos
- Felhasználói hozzáférés rendszergazdája
- Közreműködő
- Biztonsági rendszergazda
- Biztonságkezelő és egyéb

>[!NOTE]
Felhasználók vagy a tulajdonos vagy a felhasználói hozzáférés rendszergazdája szerepkör, és előfizetés-kezelés engedélyezése az Azure AD globális rendszergazdák csoport tagjainak olyan erőforrás-rendszergazdák. Ezek a rendszergazdák előfordulhat, hogy szerepköröket, szerepkör-beállítások konfigurálása és a PIM használata az Azure-erőforrások hozzáférésének. A lista megtekintése [beépített szerepkörök az Azure-erőforrások](../../role-based-access-control/built-in-roles.md).

## <a name="tasks"></a>Feladatok

A PIM szerepkörök aktiválása, függőben lévő aktiválások/kérelmek, függőben lévő jóváhagyások megtekintése kényelmes hozzáférést biztosít (az [az Azure AD-címtárbeli szerepkörök](azure-ad-pim-approval-workflow.md)), és áttekinti a választ a tevékenységek szakaszban a bal oldali navigációs menü függőben van.

Fér hozzá a áttekintése belépési pont a feladatok menü elemeinek egyikét sem, ha az eredményül kapott nézetben tartalmazza az Azure AD-címtárbeli szerepkörök és az Azure-erőforrásszerepkörök eredményeit. 

![](media/azure-pim-resource-rbac/role-settings-details.png)

Saját szerepkörök az Azure AD-címtárbeli szerepkörök és az Azure-erőforrásszerepkörök aktív és a jogosult szerepkör-hozzárendelések listáját tartalmazzák.

## <a name="activate-roles"></a>Szerepkörök aktiválása

Szerepkörök aktiválása az Azure-erőforrások mutatja be egy új környezetet biztosít, amely lehetővé teszi, hogy a jogosult szerepkör tagjai az aktiválás a jövőbeli dátum/idő ütemezése, és válassza ki a maximális (a rendszergazdák által konfigurált) belül egy adott aktiválási időtartamát. Ismerje meg [itt az Azure AD-szerepkörök aktiválása](../active-directory-privileged-identity-management-how-to-activate-role.md).

![](media/azure-pim-resource-rbac/contributor.png)

A aktiválások menüben adjon meg a kívánt kezdő dátum és idő, a szerepkör aktiválását. Szükség esetén csökkentse az aktiválási időtartamát (mennyi ideig a szerepkör aktív) és a egy szükség; indoklás megadása Kattintson az aktiválás gombra.

Ha a kezdő dátum és idő nem módosítanak, a szerepkör másodpercen belül aktív lesz. Várólistára helyezett szerepkör aktiválási címsorában látható a saját szerepkörök lapon jelenik meg. A frissítés gombra kattintva törölje ezt az üzenetet.

![](media/azure-pim-resource-rbac/my-roles.png)

Az aktiválás egy jövőbeli dátumot időpontra van ütemezve, akkor a függőben lévő kérelem megjelenik a bal oldali navigációs menüben a függőben lévő kérelmek lapján. Abban az esetben, ha a szerepkör aktiválása már nem szükséges, a felhasználó lemondhatja, ha a kérés kattintson a Mégse gombra a lap jobb oldalán.

![](media/azure-pim-resource-rbac/pending-requests.png)

## <a name="discover-and-manage-azure-resources"></a>Fedezze fel, és az Azure-erőforrások kezelése

Keresse meg és kezelheti a szerepköröket egy Azure-erőforrás, válassza ki az Azure-erőforrások a kezelés lapon, a bal oldali navigációs menüben. Használatával a szűrőket vagy a keresősávba, a lap tetején található egy erőforrás.

![](media/azure-pim-resource-rbac/azure-resources.png)

## <a name="resource-dashboards"></a>Erőforrás-irányítópultok

Az adminisztrátori nézet irányítópult négy elsődleges összetevőket tartalmazza. Grafikus ábrázolását erőforrás szerepkör-aktiválások az elmúlt hét napban. Az adatok hatókörét a kiválasztott erőforrás, és megjeleníti a leggyakoribb szerepkörök (tulajdonos, közreműködő, felhasználói hozzáférés rendszergazdája), és minden szerepkör kombinált aktiválások.

Jobb oldalán a aktiválások grafikon a két diagram, amelyek megjelenítik a szerepkör-hozzárendelések terjesztési által a hozzárendelési típust, felhasználókat és csoportokat egyaránt. A diagram szelet kiválasztása módosítja a százalék (vagy fordítva) értékét.

![](media/azure-pim-resource-rbac/admin-view.png)

Alább a diagramok és a felhasználóknak és csoportoknak az új szerepkör-hozzárendelések száma az elmúlt 30 napban (balra), valamint a szerepkörök (csökkenő) hozzárendelések száma szerint rendezett listáját láthatja.

![](media/azure-pim-resource-rbac/role-settings.png)

## <a name="manage-role-assignments"></a>Szerepkör-hozzárendelések kezelése

Rendszergazdák kezelhetik a szerepkör-hozzárendelések válassza ki a bal oldali navigációs szerepkörök vagy a tagok. Szerepkör kiválasztása lehetővé teszi, hogy a rendszergazdák a felügyeleti feladatok egy adott szerepkör hatóköre közben tagok megjeleníti az összes felhasználó- és szerepkör-hozzárendelések az erőforrás.

![](media/azure-pim-resource-rbac/roles.png)

![](media/azure-pim-resource-rbac/members.png)

>[!NOTE]
Ha egy függőben lévő aktiválási szerepkör, értesítésszalagról megjelenik az oldal tetején lévő tagság megtekintésekor.

## <a name="assign-roles"></a>Szerepkörök hozzárendelése

Egy felhasználót vagy csoportot egy szerepkörhöz rendeléséhez jelölje ki a szerepkört (Ha a szerepkörök megtekintése), vagy kattintson a Hozzáadás gombra a művelet sávon (Ha a tagok nézetben).

![](media/azure-pim-resource-rbac/members2.png)

>[!NOTE]
Ha egy felhasználó vagy csoport hozzáadása a tagok lapon, a Hozzáadás menüben válasszon egy szerepkört, egy felhasználó vagy csoport kiválasztása előtt szüksége.

![](media/azure-pim-resource-rbac/select-role.png)

Válassza ki egy felhasználót vagy csoportot a címtárból.

![](media/azure-pim-resource-rbac/choose.png)

Válassza ki a megfelelő hozzárendelési típust a legördülő menüből. 

**Csak az idő-hozzárendelési:** a felhasználó vagy csoport tagjai a szerepkör jogosult, de nem állandó hozzáféréssel rendelkező idő vagy határozatlan időre meghatározott ideig biztosít (Ha be van állítva, a szerepkör-beállítások). 

**Közvetlen hozzárendelés:** nem követeli meg a felhasználó vagy csoport tagjai (más néven az állandó hozzáférés) szerepkör-hozzárendelés aktiválását. A Microsoft javasolja, hogy közvetlen hozzárendelés használatával például a készenléti műszakok vagy idő-és nagybetűket tevékenységeket, a rövid távú használatra, ahol hozzáférés nem lesz szükség a feladat befejezésekor.

![](media/azure-pim-resource-rbac/membership-settings.png)

A hozzárendelés típusa legördülő lista alatt egy jelölőnégyzet lehetővé teszi megadhatja, ha a hozzárendelés állandó (véglegesen aktiválására jogosulttá csak az idő hozzárendelés/véglegesen aktív közvetlen hozzárendelés). Egy adott hozzárendelés-időtartam megadása, törölje a jelölőnégyzet és módosíthatja a kezdő és/vagy záró dátum és időpont mezőket.

>[!NOTE]
A jelölőnégyzet unmodifiable, ha egy másik rendszergazda adta meg a hozzárendelés maximális időtartam minden hozzárendelés-típus a szerepkör-beállítások az is lehet.

![](media/azure-pim-resource-rbac/calendar.png)

## <a name="view-activation-and-azure-resource-activity"></a>Aktiválás és az Azure Resource-tevékenységek megtekintése

Abban az esetben, ha meg, milyen műveletet egy adott felhasználó hajtott végre a különböző erőforrások van szüksége, áttekintheti az Azure Resource tevékenység társított egy adott aktiválási időszak (a jogosult felhasználók). Első lépésként válassza ki a felhasználó, a tagok megtekintése vagy egy adott szerepkör tagjainak listája. Az eredmény az Azure-erőforrások dátum szerint a felhasználói műveleteket, és ugyanazt az időszakra, hogy a legutóbbi szerepkör-aktiválások grafikus nézetet jelenít meg.

![](media/azure-pim-resource-rbac/user-details.png)

Egy adott szerepkör-aktiválás kiválasztása jelennek meg a szerepkör aktiválásának részletei, és a megfelelő Azure-erőforrás tevékenység, amely történt, miközben a felhasználó volt aktív.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="modify-existing-assignments"></a>Meglévő hozzárendelések módosítása

A felhasználó/csoport részletes nézete a meglévő hozzárendelések módosításához válassza az oldal tetején a művelet sávon a beállítások módosítása. A hozzárendelés típusának módosítása csak az idő-hozzárendelés vagy közvetlen hozzárendelés.

![](media/azure-pim-resource-rbac/change-settings.png)

## <a name="review-who-has-access-in-a-subscription"></a>Tekintse át, aki hozzáféréssel rendelkezik az előfizetéshez

Tekintse át a szerepkör-hozzárendeléseket az előfizetésében, a tagok lapon válassza a bal oldali navigációs vagy szerepkörök kiválasztása, és válasszon egy adott szerepkör tagjai áttekintéséhez. 

Válassza ki a tekintse át a meglévő hozzáférési felülvizsgálatok megtekintéséhez, és válassza a Hozzáadás létrehoz egy új, a művelet sávon.

![](media/azure-pim-resource-rbac/owner.png)

[További információ a hozzáférési felülvizsgálatok](../active-directory-privileged-identity-management-how-to-perform-security-review.md)

>[!NOTE]
Értékelések jelenleg csak támogatottak előfizetés erőforrástípusok.

## <a name="configure-role-settings"></a>Szerepkör-beállítások konfigurálása

Szerepkör-beállítások konfigurálásával az alapértelmezett beállításokat alkalmazza a PIM környezetben társításának határozza meg. Ezek az erőforrás megadásához válassza ki a szerepkör-beállítások lapon a bal oldali navigációs menüben, vagy a szerepkör-beállítások gomb bármely szerepkör a jelenlegi beállítások megtekintéséhez a művelet sávon.

A művelet sávon a lap tetején kattintson a Szerkesztés lehetővé teszi, hogy minden beállítás módosításához.

![](media/azure-pim-resource-rbac/owner.png)

![](media/azure-pim-resource-rbac/owner02.png)

Beállítások módosításait a szerepkör-beállítások lapon, a legutóbbi frissítés dátuma és a rendszergazda módosította a beállításokat, beleértve a rendszer naplózza.

![](media/azure-pim-resource-rbac/role-settings-02.png)

## <a name="resource-audit"></a>Erőforrás naplózása

Erőforrás naplózása megtekintheti az összes szerepkör-tevékenység az erőforrás. Szűrheti az adatokat egy előre definiált dátum- vagy egyéni tartomány használatával.
![](media/azure-pim-resource-rbac/last-day.png) Erőforrás naplózása is megtekintheti a felhasználói tevékenység részletei gyors hozzáférést biztosít. A nézetben minden "A szerepkör aktiválása" műveletek az adott kérelmező erőforrástevékenység mutató hivatkozásokat.
![](media/azure-pim-resource-rbac/resource-audit.png)

## <a name="just-enough-administration"></a>Éppen elég felügyelettel

A PIM Használatát az Azure-erőforrások egyszerű éppen elegendő administration (JEA) ajánlott eljárások használata az erőforrás szerepkör-hozzárendelések. Felhasználók és az Azure-előfizetések vagy erőforráscsoportok hozzárendelésekkel csoporttagok aktiválhatja a meglévő szerepkör-hozzárendelés egy csökkentett hatókörben. 

A keresés lapon keresse meg az alárendelt erőforrás kezeléséhez szükséges.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Válassza ki a saját szerepkörök a bal oldali navigációs menüben, és válassza ki a megfelelő szerepkört aktiválja. Figyelje meg a hozzárendelés típusa örökölt, mivel a szerepkör hozzá volt rendelve az előfizetés, nem pedig az erőforráscsoport, a lent látható módon.

![](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>További lépések

- [Az Azure-erőforrások beépített szerepkörök](../../role-based-access-control/built-in-roles.md)
- Ismerje meg [itt az Azure AD-szerepkörök aktiválása](../active-directory-privileged-identity-management-how-to-activate-role.md)
- [A PIM jóváhagyási munkafolyamatokat](azure-ad-pim-approval-workflow.md)
