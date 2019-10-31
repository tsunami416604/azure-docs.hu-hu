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
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a8e5dc43170af8f33ad266edfa02ac5b41ca67a
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174142"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Csatlakoztatott szervezet hozzáadása az Azure AD-jogosultságok kezelésében

Az Azure AD-jogosultságok kezelése lehetővé teszi, hogy együttműködjön a szervezeten kívüli személyekkel. Ha gyakran dolgozik együtt a külső Azure AD-címtárban vagy-tartományban lévő felhasználókkal, csatlakoztatott szervezetként is hozzáadhatja őket. Ez a cikk azt ismerteti, hogyan adhat hozzá egy csatlakoztatott szervezetet, hogy lehetővé tegye a szervezeten kívüli felhasználók számára az erőforrások kérését a címtárban.

## <a name="what-is-a-connected-organization"></a>Mi az a csatlakoztatott szervezet?

A csatlakoztatott szervezet egy külső Azure AD-címtár vagy-tartomány, amelyhez kapcsolata van.

Tegyük fel például, hogy a Woodgrove bankban dolgozik, és két külső szervezettel kíván együttműködni: a Graphic Design Institute és a contoso használatával. Azt mondta, hogy az Ön kapcsolattartója az Azure AD-t használja a grafikai tervezési Intézetben, és a grafikai tervezési Intézet felhasználói egyszerű felhasználónevet használnak `graphicdesigninstitute.com`. Azt is mondta, hogy a contoso-nal még nem használja az Azure AD-t, de a contoso felhasználói egyszerű felhasználónevet használnak `contoso.com`.

Két csatlakoztatott szervezetet is beállíthat – egyet a Graphic Design Institute számára a `graphicdesigninstitute.com`tartománnyal, a másikat pedig a contoso tartományhoz `contoso.com`. Ha ezt követően hozzáadja a két csatlakoztatott szervezetet egy szabályzathoz, akkor az egyes szervezetek felhasználóinak, akik a szabályzatnak megfelelő egyszerű felhasználónévvel rendelkeznek, hozzáférési csomagokat igényelhetnek. Továbbá, mivel a grafikai tervező Intézet az Azure AD-vel lett azonosítva, ha a grafikai tervező Intézet ezt követően altartományokkal rendelkezik (például `graphicdesigninstitute.example`), akkor az adott egyszerű felhasználónévvel rendelkező felhasználók is igényelhetnek hozzáférési csomagokat a Ugyanez a szabályzat.

![Példa csatlakoztatott szervezetre](./media/entitlement-management-organization/connected-organization-example.png)

Az Azure AD-címtár vagy-tartomány felhasználóinak hitelesítése a hitelesítés típusától függ. A csatlakoztatott szervezetek hitelesítési típusai a következők:

- Azure AD
- [Közvetlen összevonás](../b2b/direct-federation.md)
- [Egyszeri jelszó](../b2b/one-time-passcode.md) (tartomány)

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

## <a name="delete-a-connected-organization"></a>Csatlakoztatott szervezet törlése

Ha már nincs kapcsolata egy külső Azure AD-címtárral vagy-tartománnyal, akkor törölheti a csatlakoztatott szervezetet.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda vagy vendég meghívója

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **csatlakoztatott szervezetek** elemre, majd a csatlakoztatott szervezet megnyitásához kattintson a elemre.

1. Az Áttekintés lapon kattintson a **Törlés** gombra a csatlakoztatott szervezet törléséhez.

    Jelenleg csak akkor törölhet egy csatlakoztatott szervezetet, ha nincsenek csatlakoztatott felhasználók.

    ![Identitás szabályozása – csatlakoztatott szervezetek – csatlakoztatott szervezet törlése](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Következő lépések

- [Hozzáférés szabályozása külső felhasználók számára](entitlement-management-organization.md)
- [A címtárban nem szereplő felhasználók számára](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
