---
title: Az Azure ad-ben tagjogosultság-kezelés (előzetes verzió) – az Azure Active Directory feladatok delegálása
description: Ismerje meg a szerepköröket rendelhet az Azure Active Directory tagjogosultság-kezelés feladatok.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/10/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4c4933847a39a56084894e5bbd40e166e6b73b6
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798632"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>Feladatok az Azure ad-ben tagjogosultság-kezelés (előzetes verzió)

> [!IMPORTANT]
> Az Azure Active Directory (Azure AD) tagjogosultság-kezelés jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Alapértelmezés szerint a globális rendszergazdák és felhasználói rendszergazdái is létrehozása és kezelése tagjogosultság-kezelés az Azure AD minden aspektusát. Ezeket a szerepköröket a felhasználók azonban nem lehetséges, hogy tudja minden olyan esetben, ahol a hozzáférési csomagok szükségesek. Általában célszerű részlegek belüli felhasználók tudják, ki kell működhet.

Helyett a nem rendszergazdák nem korlátozott engedélyek megadását, felhasználók a legalább végezhető el a feladat, és ne hozzon létre ütköző szükséges engedélyekkel, vagy nem megfelelő hozzáférési jogosultságokat adhat meg. Ez a cikk ismerteti a szerepkörök, amelyeket hozzárendelhet tagjogosultság-kezelés a különböző feladatok delegálására.

## <a name="delegate-example-for-departmental-adoption"></a>Példa delegált részlegszintű elfogadása

Megértéséhez, előfordulhat, hogy hogyan delegálhat tagjogosultság-kezelés a feladatokat, fontolja meg egy példa segít. Tegyük fel, hogy a szervezet rendelkezik a következő öt felhasználók:

| Felhasználó | Részleg | Megjegyzések |
| --- | --- | --- |
| Alice | IT | Globális rendszergazda |
| Bob | Kutatás | Bob egyben Research csoport tulajdonosa |
| Sárközi | Kutatás |  |
| Dave | Marketing |  |
| Elisa | Marketing | ELISA egyben egy Marketing-alkalmazás tulajdonosa |

A kutatási és marketingrészleg használni kívánt tagjogosultság-kezelés a felhasználók számára. Alice még nem áll készen a használatára tagjogosultság-kezelés más részlegeinek alkalmazásaival szemben. Itt látható, hogy Ágnes átadhatja a kutatás és marketingrészleg feladatok egyik módja.

1. Alice létrehoz egy új Azure AD biztonsági csoport a katalógus létrehozói számára, és Bob, Carol, Dave és Elisa hozzáadja az adott csoport tagjai.

1. Alice beállításai a jogosultságot tartalmazó felügyeleti csoport hozzáadása a katalógus létrehozói szerepkört.

1. Carol létrehoz egy **kutatási** katalógusban, és hozzáadja a Bob társtulajdonosként a katalógus. Bob hozzáadása a az ő a tulajdonosa a katalógust erőforrásként, research csoport, hogy a használat egy hozzáférési csomag kutatási csoportmunkához.

1. Dave létrehoz egy **Marketing** katalógusban, és hozzáadja a Elisa társtulajdonosként a katalógus. ELISA hozzáadja a az ő a tulajdonosa a katalógus erőforrásként, marketing alkalmazást, hogy a használat egy hozzáférési csomag for marketing-együttműködés.

Most már a kutatás és a marketing részlegek jainkkal tagjogosultság-kezelés. Bob, Carol, Dave és Elisa létrehozhat és azok megfelelő katalógusok access-csomagok kezelése.

![A jogosultság felügyeleti delegált példa](./media/entitlement-management-delegate/elm-delegate.png)

## <a name="entitlement-management-roles"></a>A jogosultság felügyeleti szerepkörök

Tagjogosultság-kezelés a következő szerepkörök tagjogosultság-kezelés jellemző rendelkezik.

| Role | Leírás |
| --- | --- |
| Katalógus létrehozója | Hozzon létre és katalógusok kezeléséhez. Általában egy informatikai rendszergazda aki nem globális rendszergazda, vagy olyan erőforrások gyűjteménye, az erőforrás tulajdonosa. A személy, amely automatikusan létrehozza a katalógus lesz a katalógus első katalógus tulajdonosa, és adhat hozzá további katalógus tulajdonosai. |
| Katalógus tulajdonosa | Szerkessze, és a meglévő katalógusok kezelése. Általában a rendszergazda vagy erőforrás-tulajdonosok, vagy a felhasználó, aki jelölte ki a katalógus tulajdonosa. |
| Hozzáférés a Csomagkezelő | Szerkessze, és kezelni átveheti a katalógusban az összes meglévő hozzáférési csomagok. |

Emellett egy kijelölt jóváhagyó és a egy hozzáférés-csomag a kérelmező is jogosult, de azok nem szerepkörök.
 
* Jóváhagyó: Jóváhagyja vagy elutasítja a csomagok hozzáférés kérése egy házirend által engedélyezett, azonban nem módosíthatják a hozzáférés csomagdefiníciók.
* Kérelmező: Egy házirend-hozzáférés csomag jogosult kérelmezni hozzáférés csomagot.

Az alábbi táblázat a feladatokat, ezek a szerepkörök hajthat végre.

| Tevékenység | Katalógus létrehozója | Katalógus tulajdonosa | Hozzáférés a Csomagkezelő | Jóváhagyó |
| --- | :---: | :---: | :---: | :---: |
| [Új katalógus létrehozása](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [Adjon hozzá egy erőforrást egy katalógus](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [Katalógus szerkesztése](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [Katalógus törlése](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [Egy katalógus tulajdonosa vagy egy hozzáférési Csomagkezelő hozzáadása egy katalógushoz](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [Hozzon létre egy új hozzáférési csomagot a katalógusban található](entitlement-management-access-package-create.md) |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Egy hozzáférés-csomagban lévő erőforrás-szerepkörök kezelése](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Adja meg, akik kérhet egy hozzáférés-csomag](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Felhasználó hozzárendelése közvetlenül egy hozzáférés-csomag](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Az access-csomag-hozzárendeléssel rendelkezik felhasználó megtekintése](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Egy hozzáférés-csomagot kérelmek megtekintése](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Egy kérelem kézbesítési hibák megtekintése](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Függőben lévő kérelem megszakítása](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Egy hozzáférés-csomag elrejtése](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Töröl egy hozzáférés-csomagot](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Hozzáférési kérelem jóváhagyása](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Erőforrások hozzáadása a katalógus szükséges szerepkörök

Globális rendszergazda is hozzáadása vagy eltávolítása bármely (felhő által létrehozott biztonsági csoportok vagy felhő által létrehozott Office 365-csoportokat) csoport, alkalmazás vagy a SharePoint Online-webhelyen a katalógusban található. Egy felhasználó rendszergazda hozzáadása vagy eltávolítása minden olyan csoportot vagy alkalmazást a katalógusban található.

Egy felhasználó, aki nem globális rendszergazda vagy felhasználói rendszergazda, csoportok, alkalmazások vagy SharePoint Online-webhelyek hozzáadása egy katalógus, rendelkeznie kell, hogy a felhasználó *mindkét* az Azure AD directory szerepkör és a katalógus tulajdonos jogosultság szükséges. felügyeleti szerepkör. A következő táblázat felsorolja a szerepkör-kombinációkat szükséges erőforrások hozzáadása egy katalógushoz. Az erőforrások eltávolításához a katalógusból, ugyanezeket a szerepköröket kell rendelkeznie.

| Az Azure AD-címtárbeli szerepkört | A jogosultság felügyeleti szerepkör | Biztonsági csoport is hozzáadhat. | Office 365-csoportot adhat hozzá. | Alkalmazás adhat hozzá. | A SharePoint Online-webhelyen is hozzáadhat. |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Globális rendszergazda](../users-groups-roles/directory-assign-admin-roles.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Felhasználói adminisztrátor](../users-groups-roles/directory-assign-admin-roles.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune-rendszergazda](../users-groups-roles/directory-assign-admin-roles.md) | Katalógus tulajdonosa | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange-rendszergazda](../users-groups-roles/directory-assign-admin-roles.md) | Katalógus tulajdonosa |  | :heavy_check_mark: |  |  |
| [Csapatok szolgáltatás-rendszergazda](../users-groups-roles/directory-assign-admin-roles.md) | Katalógus tulajdonosa |  | :heavy_check_mark: |  |  |
| [SharePoint-rendszergazda](../users-groups-roles/directory-assign-admin-roles.md) | Katalógus tulajdonosa |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Alkalmazás-rendszergazda](../users-groups-roles/directory-assign-admin-roles.md) | Katalógus tulajdonosa |  |  | :heavy_check_mark: |  |
| [Felhőalkalmazás-rendszergazda](../users-groups-roles/directory-assign-admin-roles.md) | Katalógus tulajdonosa |  |  | :heavy_check_mark: |  |
| Felhasználó | Katalógus tulajdonosa | Csak akkor, ha a csoport tulajdonosa | Csak akkor, ha a csoport tulajdonosa | Csak akkor, ha alkalmazás tulajdonosa |  |

## <a name="add-a-catalog-creator"></a>Adja hozzá a katalógus-készítő

Ha azt szeretné, és delegálja a katalógus létrehozása, felhasználók hozzáadása a katalógus létrehozó szerepkörhöz.  Hozzáadhat egyéni felhasználókat, vagy pedig az kényelmi adhat hozzá egy csoporthoz, amelynek tagjai majd tudni katalógust létrehozni. Kövesse az alábbi lépéseket egy felhasználó hozzárendelése a katalógus létrehozó szerepkörhöz.

**Előfeltétel szerepkör:** Globális rendszergazda vagy felhasználói rendszergazda

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben lévő a **Tagjogosultság-kezelés** területén kattintson **beállítások**.

1. Kattintson a **Szerkesztés** gombra.

1. Az a **jogosultság felügyelet delegálása** területén kattintson **adja hozzá a katalógus alkotói** , válassza ki a felhasználókat vagy csoportokat, akik a jogosultság felügyeleti szerepkör tagjainak.

1. Kattintson a **Kiválasztás** gombra.

1. Kattintson a **Save** (Mentés) gombra.

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>Adjon hozzá egy katalógus tulajdonosa vagy egy hozzáférési Csomagkezelő

Ha szeretne egy katalógusban vagy a hozzáférési csomagok a katalógusban felügyelet delegálása, felhasználók hozzáadása a katalógus tulajdonosának vagy a hozzáférés package manager-szerepköröket. Amikor valaki létrehoz egy katalógust lesz a katalógus első tulajdonosa. Kövesse az alábbi lépéseket egy felhasználó hozzárendelése a katalógus tulajdonos vagy a hozzáférés csomagot kezelő szerepkört.

**Előfeltétel szerepkör:** Felhasználói rendszergazda vagy a katalógus tulajdonos

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **katalógusok** , majd nyissa meg a katalógus-rendszergazdák számára, hogy a hozzáadni kívánt.

1. A bal oldali menüben kattintson a **szerepkörök és a rendszergazdák**.

1. Kattintson a **tulajdonosok hozzáadása** vagy **hozzáférés csomagkezelők hozzáadása** válassza ki a kívánt ezek a szerepkörök tagjait.

1. Kattintson a **kiválasztása** ezekről a tagokról hozzáadásához.

## <a name="next-steps"></a>További lépések

- [Jóváhagyó hozzáadása](entitlement-management-access-package-edit.md#policy-request)
- [Erőforrások hozzáadása egy katalógushoz](entitlement-management-catalog-create.md#add-resources-to-a-catalog)
