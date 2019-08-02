---
title: Feladatok delegálása az Azure AD-jogosultságok kezelésében (előzetes verzió) – Azure Active Directory
description: Ismerje meg azokat a szerepköröket, amelyeket hozzárendelhet a feladatok delegálásához Azure Active Directory jogosultságok kezelésében.
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
ms.openlocfilehash: e4e21fdef5be09148f001ab6f66f87dd270ccf54
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68618268"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>Feladatok delegálása az Azure AD-jogosultságok kezelésében (előzetes verzió)

> [!IMPORTANT]
> A Azure Active Directory (Azure AD) jogosultság-kezelési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Alapértelmezés szerint a globális rendszergazdák és a felhasználói rendszergazdák az Azure AD-jogosultságok felügyeletének minden aspektusát létrehozzák és kezelhetik. Előfordulhat azonban, hogy az ezekben a szerepkörökben lévő felhasználók nem ismerik az összes olyan forgatókönyvet, amelyben hozzáférési csomagok szükségesek. Jellemzően olyan részlegek felhasználói, akik tudják, hogy kivel kell együttműködni.

A nem rendszergazdák számára nem korlátozott engedélyek megadása helyett a lehető legkevesebb engedélyt adhat a felhasználóknak a feladatok elvégzéséhez, és el kell kerülniük az ütköző vagy nem megfelelő hozzáférési jogosultságok létrehozását. Ez a cikk azokat a szerepköröket ismerteti, amelyeket hozzárendelhet a jogosultság-kezelés különböző feladatai delegálásához.

## <a name="delegate-example-for-departmental-adoption"></a>Példa delegálásra a tanszékek bevezetéséhez

Ha meg szeretné tudni, hogyan delegálhatja a feladatokat a jogosultságok kezelésében, ez segít megfontolni egy példát. Tegyük fel, hogy a szervezete a következő öt felhasználóval rendelkezik:

| Felhasználó | Részleg | Megjegyzések |
| --- | --- | --- |
| Alice | IT | Globális rendszergazda |
| Bob | Kutatás | Bob egy kutatási csoport tulajdonosa is |
| Carole | Kutatás |  |
| Dave | Marketing |  |
| ELISA | Marketing | Az ELISA egy marketing-alkalmazás tulajdonosa is |

Mind a kutatási, mind a marketing részleg a jogosultságok felügyeletét szeretné használni a felhasználók számára. Alice még nem áll készen a jogosultsági felügyelet használatára más részlegek számára. Itt az egyik módszer, hogy Alice delegálhatja a feladatokat a kutatási és marketing részlegeknek.

1. Alice létrehoz egy új Azure AD biztonsági csoportot a katalógus-létrehozók számára, és hozzáadja a Bob, Carol, Dave és ELISA címkét a csoport tagjaihoz.

1. Alice a jogosultság-kezelési beállítások használatával hozzáadja ezt a csoportot a katalógus-létrehozói szerepkörhöz.

1. Carol létrehoz egy **kutatási** katalógust, és felveszi a Bob-t a katalógus társtulajdonosa. Bob hozzáadja azt a kutatási csoportot, amelyet a katalógus erőforrásként birtokol, így a kutatási együttműködéshez használható egy hozzáférési csomagban.

1. Dave létrehoz egy **marketing** -katalógust, és hozzáadja az ELISA-t a katalógus társtulajdonosa. Az ELISA hozzáadja azt a marketing-alkalmazást, amelynek tulajdonosa a katalógus erőforrásként, így használható egy hozzáférési csomagban a marketing-együttműködéshez.

Most a kutatási és marketing részlegek is használhatják a jogosultságok kezelését. Bob, Carol, Dave és ELISA a megfelelő katalógusokban hozhat létre és kezelhet hozzáférési csomagokat.

![Jogosultsági felügyeleti delegált példa](./media/entitlement-management-delegate/elm-delegate.png)

## <a name="entitlement-management-roles"></a>Jogosultsági felügyeleti szerepkörök

A jogosultságok kezeléséhez a következő szerepkörök tartoznak, amelyek a jogosultságok felügyeletére vonatkoznak.

| Role | Leírás |
| --- | --- |
| Katalógus létrehozója | Katalógusok létrehozása és kezelése. Általában az a rendszergazda, aki nem globális rendszergazda, vagy erőforrás-tulajdonos az erőforrások gyűjteménye számára. A katalógust létrehozó személy automatikusan a katalógus első katalógusának tulajdonosa lesz, és további katalógus-tulajdonosokat is hozzáadhat. |
| Katalógus tulajdonosa | Meglévő katalógusok szerkesztése és kezelése. Általában rendszergazda vagy erőforrás-tulajdonos, vagy a katalógus tulajdonosának kijelölt felhasználója. |
| A Package Manager elérése | A katalógusban lévő összes meglévő hozzáférési csomag szerkesztése és kezelése. |

Emellett a kijelölt jóváhagyó és egy hozzáférési csomag kérelmezője is rendelkezik jogosultságokkal, bár ezek nem szerepkörök.
 
* Jóváhagyó A szabályzat engedélyezi a csomagok elérésére irányuló kérések jóváhagyását vagy elutasítását, bár a hozzáférési csomag definíciói nem módosíthatók.
* Kérelmező Egy hozzáférési csomag szabályzata által jogosult a hozzáférési csomag igénylésére.

A következő táblázat felsorolja a szerepkörök által végrehajtható feladatokat.

| Tevékenység | Katalógus létrehozója | Katalógus tulajdonosa | A Package Manager elérése | Jóváhagyó |
| --- | :---: | :---: | :---: | :---: |
| [Új katalógus létrehozása](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [Erőforrás hozzáadása katalógushoz](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [Katalógus szerkesztése](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [Katalógus törlése](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [Katalógushoz tartozó tulajdonos vagy Access Package Manager hozzáadása](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [Új hozzáférési csomag létrehozása katalógusban](entitlement-management-access-package-create.md) |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Erőforrás-szerepkörök kezelése egy hozzáférési csomagban](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Annak meghatározása, hogy ki igényelhet hozzáférési csomagot](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Felhasználó közvetlen kiosztása egy hozzáférési csomaghoz](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Hozzáférési csomaghoz hozzárendelt hozzárendelés megtekintése](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Hozzáférési csomag kéréseinek megtekintése](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Kérelem kézbesítési hibáinak megtekintése](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Függőben lévő kérelem megszakítása](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Hozzáférési csomag elrejtése](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Hozzáférési csomag törlése](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Hozzáférési kérelem jóváhagyása](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Erőforrások katalógusba való felvételéhez szükséges szerepkörök

A globális rendszergazdák hozzáadhatnak vagy eltávolíthatnak bármely csoportot (felhőalapú biztonsági csoportokat vagy felhőben létrehozott Office 365-csoportokat), alkalmazást vagy SharePoint Online-webhelyeket katalógusban. A felhasználó rendszergazdája hozzáadhat vagy eltávolíthat bármely csoportot vagy alkalmazást egy katalógusban.

Olyan felhasználók esetében, akik nem globális rendszergazda vagy felhasználói rendszergazda, csoportok, alkalmazások vagy SharePoint Online-helyek katalógusba való felvételéhez a felhasználónak rendelkeznie *kell a szükséges* Azure ad-címtárbeli szerepkörrel és a katalógus tulajdonosi jogosultsági kezelési szerepkörével. A következő táblázat felsorolja azokat a szerepkör-kombinációkat, amelyek szükségesek az erőforrások katalógushoz való hozzáadásához. Az erőforrások katalógusból való eltávolításához ugyanazokat a szerepköröket kell megadnia.

| Azure AD-címtárbeli szerepkör | Jogosultsági felügyeleti szerepkör | Hozzáadhat biztonsági csoportot | Office 365-csoportot adhat hozzá | Hozzáadhat alkalmazást | Hozzáadhat SharePoint Online-webhelyet |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Globális rendszergazda](../users-groups-roles/directory-assign-admin-roles.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Felhasználói rendszergazda](../users-groups-roles/directory-assign-admin-roles.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune-rendszergazda](../users-groups-roles/directory-assign-admin-roles.md) | Katalógus tulajdonosa | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange-rendszergazda](../users-groups-roles/directory-assign-admin-roles.md) | Katalógus tulajdonosa |  | :heavy_check_mark: |  |  |
| [Teams szolgáltatás rendszergazdája](../users-groups-roles/directory-assign-admin-roles.md) | Katalógus tulajdonosa |  | :heavy_check_mark: |  |  |
| [SharePoint-rendszergazda](../users-groups-roles/directory-assign-admin-roles.md) | Katalógus tulajdonosa |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Alkalmazás-rendszergazda](../users-groups-roles/directory-assign-admin-roles.md) | Katalógus tulajdonosa |  |  | :heavy_check_mark: |  |
| [Cloud Application Administrator](../users-groups-roles/directory-assign-admin-roles.md) | Katalógus tulajdonosa |  |  | :heavy_check_mark: |  |
| Felhasználó | Katalógus tulajdonosa | Csak akkor, ha a csoport tulajdonosa | Csak akkor, ha a csoport tulajdonosa | Csak akkor, ha az alkalmazás tulajdonosa |  |

## <a name="add-a-catalog-creator"></a>Katalógus létrehozójának hozzáadása

Ha a katalógus létrehozását szeretné delegálni, vegye fel a felhasználókat a katalógus létrehozói szerepkörbe.  Hozzáadhat egyéni felhasználókat, vagy megadhat egy csoportot, amelynek tagjai létrehozhatnak katalógusokat. Kövesse az alábbi lépéseket egy felhasználó hozzárendeléséhez a katalógus-létrehozó szerepkörhöz.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda vagy felhasználói rendszergazda

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben, a **jogosultságok kezelése** szakaszban kattintson a **Beállítások**elemre.

1. Kattintson a **Szerkesztés** gombra.

1. A **jogosultság-kezelés delegálása** szakaszban kattintson a **katalógus-létrehozók hozzáadása** elemre azon felhasználók vagy csoportok kiválasztásához, akik a jogosultsági felügyeleti szerepkör tagjai lesznek.

1. Kattintson a **Kiválasztás** gombra.

1. Kattintson a **Save** (Mentés) gombra.

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>Katalógus tulajdonosának vagy egy Access Package Managernek a hozzáadása

Ha a katalógusban lévő katalógus vagy hozzáférési csomagok felügyeletét szeretné delegálni, vegye fel a felhasználókat a katalógus tulajdonosához, vagy a hozzáférés csomagkezelő szerepkörhöz. Aki létrehoz egy katalógust, az első katalógus tulajdonosa lesz. Az alábbi lépéseket követve rendelhet hozzá egy felhasználót a katalógus tulajdonosához vagy a Access Package Manager szerepkörhöz.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda vagy katalógus tulajdonosa

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **katalógusok** lehetőségre, majd nyissa meg azt a katalógust, amelyhez hozzá szeretné adni a rendszergazdákat.

1. A bal oldali menüben kattintson a **szerepkörök és rendszergazdák**elemre.

1. Kattintson a **tulajdonosok hozzáadása** vagy a **hozzáférési csomag** kezelőjének hozzáadása elemre a szerepkörök tagjainak kiválasztásához.

1. A tagok hozzáadásához kattintson a **kiválasztás** gombra.

## <a name="next-steps"></a>További lépések

- [Jóváhagyók hozzáadása](entitlement-management-access-package-edit.md#policy-request)
- [Erőforrások hozzáadása egy katalógushoz](entitlement-management-catalog-create.md#add-resources-to-a-catalog)
