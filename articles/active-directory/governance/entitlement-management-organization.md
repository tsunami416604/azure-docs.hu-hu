---
title: Csatlakoztatott szervezet hozzáadása az Azure AD-jogosultságok kezelésében – Azure Active Directory
description: Megtudhatja, hogyan engedélyezheti a szervezeten kívüli felhasználók számára a hozzáférési csomagok bekérését, így dolgozhat a projekteken.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50c5c02327aa9f48a605607de901258827b14896
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783943"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Csatlakoztatott szervezet hozzáadása az Azure AD-jogosultságok kezelésében

A Azure Active Directory (Azure AD) jogosultsági felügyelettel a szervezeten kívüli személyekkel is dolgozhat. Ha gyakran dolgozik együtt a külső Azure AD-címtárban vagy-tartományban lévő felhasználókkal, csatlakoztatott szervezetként is hozzáadhatja őket. Ez a cikk azt ismerteti, hogyan adhat hozzá egy csatlakoztatott szervezetet, hogy lehetővé tegye a szervezeten kívüli felhasználók számára az erőforrások kérését a címtárban.

## <a name="what-is-a-connected-organization"></a>Mi az a csatlakoztatott szervezet?

A csatlakoztatott szervezet egy külső Azure AD-címtár vagy-tartomány, amelyhez kapcsolata van.

Tegyük fel például, hogy a Woodgrove bankban dolgozik, és két külső szervezettel kíván együttműködni. Ez a két szervezet különböző konfigurációkkal rendelkezik:

- A Graphic Design Institute az Azure AD-t használja, és a felhasználók egyszerű felhasználónevet használnak, amely a *graphicdesigninstitute.com*-mel végződik.
- A contoso még nem használja az Azure AD-t. A contoso felhasználói egyszerű felhasználónevet biztosítanak, amely a *contoso.com*végződik.

Ebben az esetben két csatlakoztatott szervezetet is beállíthat. Létre kell hoznia egy csatlakoztatott szervezetet a Graphic Design Institute számára, és egyet a contoso számára. Ha ezt követően hozzáadja a két csatlakoztatott szervezetet egy szabályzathoz, akkor az egyes szervezetek felhasználóinak a szabályzatnak megfelelő egyszerű felhasználónevet igényelhetnek hozzáférési csomagokat. A *graphicdesigninstitute.com* tartománnyal rendelkező felhasználói egyszerű névvel rendelkező felhasználók megegyeznek a grafikai tervezési intézményhez csatlakoztatott szervezettel, és a kérések elküldésére jogosultak. A *contoso.com* tartománnyal rendelkező felhasználói egyszerű névvel rendelkező felhasználók megegyeznek a contoso által csatlakoztatott szervezettel, és csomagok igénylésére is jogosultak. És mivel a grafikai tervező Intézet az Azure AD-t használja, minden olyan felhasználó, aki a bérlőhöz hozzáadott [ellenőrzött tartományhoz](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) tartozó egyszerű névvel rendelkezik, például a *graphicdesigninstitute. example*, a hozzáférési csomagokat ugyanezen házirend használatával is kérheti.

![Példa csatlakoztatott szervezetre](./media/entitlement-management-organization/connected-organization-example.png)

Az Azure AD-címtár vagy-tartomány hitelesítésének felhasználói a hitelesítési típustól függenek. A csatlakoztatott szervezetek hitelesítési típusai a következők:

- Azure AD
- [Közvetlen összevonás](../external-identities/direct-federation.md)
- [Egyszeri jelszó](../external-identities/one-time-passcode.md) (tartomány)

A csatlakoztatott szervezetek hozzáadásával kapcsolatos bemutatóért tekintse meg a következő videót:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Csatlakoztatott szervezet hozzáadása

Külső Azure AD-címtár vagy-tartomány csatlakoztatott szervezetként való hozzáadásához kövesse az ebben a szakaszban található utasításokat.

**Előfeltételként szükséges szerepkör**: *globális rendszergazda* vagy *felhasználói rendszergazda*

1. A Azure Portal válassza a **Azure Active Directory**lehetőséget, majd válassza az **identitás irányítása**elemet.

1. A bal oldali ablaktáblán válassza a **csatlakoztatott szervezetek**lehetőséget, majd válassza a **csatlakoztatott szervezet hozzáadása**elemet.

    ![A "csatlakoztatott szervezet hozzáadása" gomb](./media/entitlement-management-organization/connected-organization.png)

1. Válassza az **alapismeretek** fület, majd adja meg a szervezet megjelenítendő nevét és leírását.

    ![A "csatlakoztatott szervezet hozzáadása" alapismeretek panel](./media/entitlement-management-organization/organization-basics.png)

1. Válassza a **címtár + tartomány** lapot, majd válassza a **könyvtár és tartomány hozzáadása**lehetőséget.

    Megnyílik a **könyvtárak és tartományok kiválasztása** panel.

1. A keresőmezőbe írja be a tartománynevet az Azure AD-címtár vagy-tartomány kereséséhez. Ügyeljen arra, hogy a teljes tartománynevet adja meg.

1. Ellenőrizze, hogy helyesek-e a szervezet neve és a hitelesítés típusa. A felhasználók bejelentkezésének módja a hitelesítési típustól függ.

    ![A "könyvtárak és tartományok kiválasztása" panel](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Az Azure AD-címtár vagy-tartomány hozzáadásához válassza a **Hozzáadás** lehetőséget. Jelenleg csak egy Azure AD-címtár vagy-tartomány adható hozzá csatlakoztatott szervezeten belül.

    > [!NOTE]
    > Az Azure AD-címtár vagy-tartomány összes felhasználója ezt a hozzáférési csomagot fogja kérni. Ez magában foglalja az Azure AD-beli felhasználókat a címtárhoz társított összes altartományból, kivéve, ha ezeket a tartományokat az Azure AD Business to Business (B2B) engedélyezési vagy letiltási listája letiltja. További információ: a [vállalatközi felhasználók meghívásának engedélyezése vagy letiltása adott szervezetekben](../external-identities/allow-deny-list.md).

1. Az Azure AD-címtár vagy-tartomány hozzáadása után válassza a **kiválasztás**lehetőséget.

    A szervezet megjelenik a listában.

    ![A "címtár + tartomány" panel](./media/entitlement-management-organization/organization-directory-domain.png)

1. Válassza a **szponzorok** fület, majd adja hozzá az opcionális szponzorokat ehhez a csatlakoztatott szervezethez.

    A szponzorok olyan belső vagy külső felhasználók, akik már szerepelnek a címtárban, amely az ezzel a csatlakoztatott szervezettel való kapcsolat kapcsolódási pontként szolgál. A belső szponzorok a címtárban tag felhasználók. A külső szponzorok a csatlakoztatott szervezet azon felhasználói, akik korábban meghívást kaptak, és már szerepelnek a címtárban. A szponzorokat jóváhagyóként lehet használni, ha a csatlakoztatott szervezet felhasználói hozzáférést igényelnek ehhez a hozzáférési csomaghoz. További információ a vendég felhasználó címtárban való meghívásáról: [Azure Active Directory B2B együttműködéssel rendelkező felhasználók hozzáadása a Azure Portal](../external-identities/add-users-administrator.md).

    Ha a **Hozzáadás/Eltávolítás**lehetőséget választja, megnyílik egy ablaktábla, ahol belső vagy külső támogatókat is választhat. A panelen megjelenik a címtárban található felhasználók és csoportok szűretlen listája.

    ![A szponzorok panel](./media/entitlement-management-organization/organization-sponsors.png)

1. Válassza a **felülvizsgálat + létrehozás** lapot, tekintse át a szervezet beállításait, majd válassza a **Létrehozás**lehetőséget.

    ![A "felülvizsgálat + létrehozás" panel](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Csatlakoztatott szervezet frissítése 

Ha a csatlakoztatott szervezet egy másik tartományra vált, a szervezet neve megváltozik, vagy módosítani szeretné a szponzorokat, a jelen szakasz utasításait követve frissítheti a csatlakoztatott szervezetet.

**Előfeltételként szükséges szerepkör**: *globális rendszergazda* vagy *felhasználói rendszergazda*

1. A Azure Portal válassza a **Azure Active Directory**lehetőséget, majd válassza az **identitás irányítása**elemet.

1. A bal oldali ablaktáblán válassza a **csatlakoztatott szervezetek**lehetőséget, majd válassza ki a csatlakoztatott szervezetet a megnyitásához.

1. A szervezet nevének vagy leírásának módosításához a csatlakoztatott szervezet áttekintés paneljén válassza a **Szerkesztés** lehetőséget.  

1. A **címtár + tartomány** ablaktáblán válassza a **könyvtár** és tartomány frissítése lehetőséget egy másik könyvtárra vagy tartományra való váltáshoz.

1. A **szponzorok** ablaktáblán válassza a **belső szponzorok hozzáadása** vagy a **külső szponzorok hozzáadása** lehetőséget egy felhasználó szponzorként való hozzáadásához. Egy szponzor eltávolításához válassza ki a szponzort, és a jobb oldali ablaktáblában válassza a **Törlés**lehetőséget.


## <a name="delete-a-connected-organization"></a>Csatlakoztatott szervezet törlése

Ha már nincs kapcsolata egy külső Azure AD-címtárral vagy-tartománnyal, akkor törölheti a csatlakoztatott szervezetet.

**Előfeltételként szükséges szerepkör**: *globális rendszergazda* vagy *felhasználói rendszergazda*

1. A Azure Portal válassza a **Azure Active Directory**lehetőséget, majd válassza az **identitás irányítása**elemet.

1. A bal oldali ablaktáblán válassza a **csatlakoztatott szervezetek**lehetőséget, majd válassza ki a csatlakoztatott szervezetet a megnyitásához.

1. A csatlakoztatott szervezet áttekintő ablaktábláján válassza a **Törlés** lehetőséget a törléshez.

    Jelenleg csak akkor törölhet egy csatlakoztatott szervezetet, ha nincsenek csatlakoztatott felhasználók.

    ![A csatlakoztatott szervezet törlése gomb](./media/entitlement-management-organization/organization-delete.png)

## <a name="managing-a-connected-organization-programmatically"></a>Csatlakoztatott szervezet programozott kezelése

A csatlakoztatott szervezeteket Microsoft Graph használatával is létrehozhatja, listázhatja, frissítheti és törölheti. Egy megfelelő szerepkörrel rendelkező felhasználó, aki delegált engedéllyel rendelkezik, `EntitlementManagement.ReadWrite.All` meghívhatja az API-t a [connectedOrganization](/graph/api/resources/connectedorganization?view=graph-rest-beta) -objektumok kezelésére, és azokhoz is beállíthatja a szponzorokat.

## <a name="next-steps"></a>Következő lépések

- [Külső felhasználók hozzáférésének szabályozása](./entitlement-management-external-users.md)
- [A címtárban nem szereplő felhasználók hozzáférésének szabályozása](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)