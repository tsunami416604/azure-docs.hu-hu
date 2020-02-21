---
title: Csatlakoztatott szervezet hozzáadása az Azure AD-jogosultságok kezelésében – Azure Active Directory
description: Megtudhatja, hogyan engedélyezheti a szervezeten kívüli felhasználók számára a hozzáférési csomagok bekérését, így dolgozhat a projekteken.
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
ms.date: 01/22/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ecf3a8819518c674a3d8bd7af55d1a3c6393c42
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77483856"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Csatlakoztatott szervezet hozzáadása az Azure AD-jogosultságok kezelésében

Az Azure AD-jogosultságok kezelése lehetővé teszi, hogy együttműködjön a szervezeten kívüli személyekkel. Ha gyakran dolgozik együtt a külső Azure AD-címtárban vagy-tartományban lévő felhasználókkal, csatlakoztatott szervezetként is hozzáadhatja őket. Ez a cikk azt ismerteti, hogyan adhat hozzá egy csatlakoztatott szervezetet, hogy lehetővé tegye a szervezeten kívüli felhasználók számára az erőforrások kérését a címtárban.

## <a name="what-is-a-connected-organization"></a>Mi az a csatlakoztatott szervezet?

A csatlakoztatott szervezet egy külső Azure AD-címtár vagy-tartomány, amelyhez kapcsolata van.

Tegyük fel például, hogy a Woodgrove bankban dolgozik, és két külső szervezettel kíván együttműködni. Ez a két szervezet különböző konfigurációkkal rendelkezik:

- A Graphic Design Institute az Azure AD-t használja, és a felhasználók egyszerű felhasználónevet használnak `graphicdesigninstitute.com`
- A contoso még nem használja az Azure AD-t. A contoso felhasználói egyszerű felhasználónevet `contoso.com`.

Ebben az esetben két csatlakoztatott szervezetet is beállíthat. Hozzon létre egy csatlakoztatott szervezetet a Graphic Design Institute számára, és egyet a contoso számára. Ha ezt követően hozzáadja a két csatlakoztatott szervezetet egy szabályzathoz, akkor az egyes szervezetek felhasználóinak a szabályzatnak megfelelő egyszerű felhasználónevet igényelhetnek hozzáférési csomagokat. A graphicdesigninstitute.com tartománnyal rendelkező felhasználói egyszerű névvel rendelkező felhasználók megfelelnek a Graphic Design Institute csatlakoztatott szervezetének, és engedélyezni kell a kérelmek küldését, míg a felhasználók egy egyszerű felhasználónévvel rendelkeznek, amely a contoso.com tartományával egyezik a contoso-beli csatlakoztatott szervezet és csomagok igénylésére is jogosult. Továbbá, mivel a grafikai tervező Intézet az Azure AD-t használja, minden olyan felhasználó, aki egy [ellenőrzött tartományhoz](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) tartozó egyszerű névvel van ellátva

![Példa csatlakoztatott szervezetre](./media/entitlement-management-organization/connected-organization-example.png)

Az Azure AD-címtár vagy-tartomány felhasználóinak hitelesítése a hitelesítés típusától függ. A csatlakoztatott szervezetek hitelesítési típusai a következők:

- Azure AD
- [Közvetlen összevonás](../b2b/direct-federation.md)
- [Egyszeri jelszó](../b2b/one-time-passcode.md) (tartomány)

A csatlakoztatott szervezetek hozzáadásával kapcsolatos bemutatóért tekintse meg a következő videót:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Csatlakoztatott szervezet hozzáadása

Kövesse az alábbi lépéseket egy külső Azure AD-címtár vagy-tartomány csatlakoztatott szervezetként való hozzáadásához.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda vagy vendég meghívója

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **csatlakoztatott szervezetek** elemre, majd kattintson a **csatlakoztatott szervezet hozzáadása**lehetőségre.

    ![Identitás szabályozása – csatlakoztatott szervezetek – csatlakoztatott szervezet hozzáadása](./media/entitlement-management-organization/connected-organization.png)

1. Az **alapvető beállítások** lapon adja meg a szervezet megjelenítendő nevét és leírását.

    ![Csatlakoztatott szervezet hozzáadása – alapismeretek lap](./media/entitlement-management-organization/organization-basics.png)

1. A **címtár + tartomány** lapon kattintson a **címtár + tartomány hozzáadása** lehetőségre a címtárak és tartományok kiválasztása ablaktábla megnyitásához.

1. Adja meg a tartománynevet az Azure AD-címtár vagy-tartomány kereséséhez. A teljes tartománynevet kell beírnia.

1. Ellenőrizze, hogy a megfelelő szervezet-e a megadott névvel és hitelesítési típussal. A felhasználók bejelentkezésének módja a hitelesítési típustól függ.

    ![Csatlakoztatott szervezet hozzáadása – könyvtárak és tartományok kijelölése](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Az Azure AD-címtár vagy-tartomány hozzáadásához kattintson a **Hozzáadás** gombra. Jelenleg csak egy Azure AD-címtár vagy-tartomány adható hozzá csatlakoztatott szervezeten belül.

    > [!NOTE]
    > Az Azure AD-címtár vagy-tartomány összes felhasználója ezt a hozzáférési csomagot fogja kérni. Ez magában foglalja az Azure AD-beli felhasználókat a címtárhoz társított összes altartományból, kivéve, ha ezeket a tartományokat az Azure B2B engedélyezési vagy megtagadási listája blokkolja. További információ: a [vállalatközi felhasználók meghívásának engedélyezése vagy letiltása adott szervezetekben](../b2b/allow-deny-list.md).

1. Az Azure AD-címtár vagy-tartomány hozzáadása után kattintson a **kiválasztás**elemre.

    A szervezet megjelenik a listában.

    ![Csatlakoztatott szervezet hozzáadása – címtárak lap](./media/entitlement-management-organization/organization-directory-domain.png)

1. A **szponzorok** lapon adja hozzá az opcionális szponzorokat ehhez a csatlakoztatott szervezethez.

    A szponzorok olyan belső vagy külső felhasználók, akik már szerepelnek a címtárban, amely az ezzel a csatlakoztatott szervezettel való kapcsolat kapcsolódási pontként szolgál. A belső szponzorok a címtárban tag felhasználók. A külső szponzorok a csatlakoztatott szervezet azon felhasználói, akik korábban meghívást kaptak, és már szerepelnek a címtárban. A szponzorokat jóváhagyóként lehet használni, ha a csatlakoztatott szervezet felhasználói hozzáférést igényelnek ehhez a hozzáférési csomaghoz. További információ a vendég felhasználó címtárban való meghívásáról: [Azure Active Directory B2B együttműködéssel rendelkező felhasználók hozzáadása a Azure Portal](../b2b/add-users-administrator.md).

    Ha a **Hozzáadás/Eltávolítás**gombra kattint, megjelenik egy ablaktábla, amely kijelöli a belső vagy külső szponzorokat. A panelen megjelenik a címtárban található felhasználók és csoportok szűretlen listája.

    ![Hozzáférési csomag – házirend – csatlakoztatott szervezet hozzáadása – szponzorok lap](./media/entitlement-management-organization/organization-sponsors.png)

1. A **felülvizsgálat + létrehozás** lapon tekintse át a szervezet beállításait, majd kattintson a **Létrehozás**gombra.

    ![Hozzáférési csomag – házirend – csatlakoztatott szervezet hozzáadása – áttekintés + Létrehozás lap](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Csatlakoztatott szervezet frissítése 

Ha a csatlakoztatott szervezet egy másik tartományra vált, ha új névvel rendelkezik a szervezet számára, vagy módosítani szeretné a szponzorokat, frissítheti a csatlakoztatott szervezetet.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda vagy vendég meghívója

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **csatlakoztatott szervezetek** elemre, majd a csatlakoztatott szervezet megnyitásához kattintson a elemre.

1. Az Áttekintés lapon kattintson a **Szerkesztés** elemre a szervezet nevének vagy leírásának módosításához.  

1. A címtár + tartomány lapon kattintson a **könyvtár és tartomány frissítése** lehetőségre egy másik könyvtárra vagy tartományra való váltáshoz.

1. A szponzorok lapon kattintson a **belső szponzorok hozzáadása** vagy a **külső szponzorok hozzáadása** lehetőségre a felhasználó szponzorként való hozzáadásához.  Egy szponzor eltávolításához kattintson a szponzorra, majd a jobb oldali menüben kattintson a **Törlés**parancsra.


## <a name="delete-a-connected-organization"></a>Csatlakoztatott szervezet törlése

Ha már nincs kapcsolata egy külső Azure AD-címtárral vagy-tartománnyal, akkor törölheti a csatlakoztatott szervezetet.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda vagy vendég meghívója

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **csatlakoztatott szervezetek** elemre, majd a csatlakoztatott szervezet megnyitásához kattintson a elemre.

1. Az Áttekintés lapon kattintson a **Törlés** gombra a csatlakoztatott szervezet törléséhez.

    Jelenleg csak akkor törölhet egy csatlakoztatott szervezetet, ha nincsenek csatlakoztatott felhasználók.

    ![Identitás szabályozása – csatlakoztatott szervezetek – csatlakoztatott szervezet törlése](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Következő lépések

- [Hozzáférés szabályozása külső felhasználók számára](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [A címtárban nem szereplő felhasználók számára](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
