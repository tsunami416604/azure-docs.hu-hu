---
title: Csatlakoztatott szervezet hozzáadása az Azure AD-jogosultságkezelésben – Azure Active Directory
description: Megtudhatja, hogy miként engedélyezheti a szervezeten kívüli személyeknek, hogy hozzáférési csomagokat kérjenek, hogy ön együttműködhessen a projekteken.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee370bc9c381eb11ae7cae53b31d0c987c52733c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128610"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Csatlakoztatott szervezet hozzáadása az Azure AD-jogosultságok kezelésében

Az Azure Active Directory (Azure AD) jogosultságkezeléssel együttműködhet a szervezeten kívüli személyekkel. Ha gyakran együttműködik a felhasználókkal egy külső Azure AD-címtárban vagy tartományban, hozzáadhatja őket, mint egy csatlakoztatott szervezet. Ez a cikk azt ismerteti, hogyan vehet fel egy csatlakoztatott szervezetet, hogy a szervezeten kívüli felhasználók erőforrásokat kérhessenek a címtárban.

## <a name="what-is-a-connected-organization"></a>Mi az összekapcsolt szervezet?

A csatlakoztatott szervezet egy külső Azure AD-címtár vagy tartomány, amely kapcsolatban áll.

Tegyük fel például, hogy a Woodgrove Banknál dolgozik, és két külső szervezettel szeretne együttműködni. Ez a két szervezet különböző konfigurációkkal rendelkezik:

- A Graphic Design Institute az Azure AD-t használja, és a felhasználók egy egyszerű felhasználónévvel rendelkeznek, amely *graphicdesigninstitute.com*végződik.
- A Contoso még nem használja az Azure AD-t. A Contoso-felhasználók egyszerű felhasználónévvel rendelkeznek, amely *contoso.com*végződik.

Ebben az esetben két csatlakoztatott szervezetet konfigurálhat. Hozzon létre egy csatlakoztatott szervezet Grafikai Tervező Intézet és egy Contoso. Ha ezután hozzáadja a két csatlakoztatott szervezetet egy házirendhez, a házirendnek megfelelő egyszerű felhasználónévvel rendelkező szervezetek felhasználói hozzáférési csomagokat kérhetnek. A *graphicdesigninstitute.com* tartománysal rendelkező egyszerű felhasználónévvel rendelkező felhasználók megegyeznének a Grafikai Intézethez kapcsolódó szervezettel, és engedélyeznék számukra a kérelmek küldését. A *contoso.com* tartománysal rendelkező egyszerű felhasználónévvel rendelkező felhasználók megfelelnek a Contoso-hoz kapcsolódó szervezetnek, és csomagokat is kérhetnek. És mivel a Graphic Design Institute az Azure AD-t használja, minden olyan egyszerű névvel rendelkező felhasználó, amely megfelel a bérlőhöz hozzáadott [ellenőrzött tartománynak,](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) például *a graphicdesigninstitute.example,* ugyanazzal a házirenddel is kérhet hozzáférési csomagokat.

![Példa csatlakoztatott szervezeti](./media/entitlement-management-organization/connected-organization-example.png)

Az Azure AD-címtárból vagy tartományhitelesítésből származó felhasználók hitelesítésének módja a hitelesítés típusától függ. Az összekapcsolt szervezetek hitelesítési típusai a következők:

- Azure AD
- [Közvetlen összevonás](../b2b/direct-federation.md)
- [Egyszeri jelkód](../b2b/one-time-passcode.md) (tartomány)

A csatlakoztatott szervezet hozzáadásának bemutatásához tekintse meg az alábbi videót:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Csatlakoztatott szervezet hozzáadása

Külső Azure AD-címtár vagy tartomány csatlakoztatott szervezetként való hozzáadásához kövesse az ebben a szakaszban található utasításokat.

**Előfeltételi szerepkör**: *Globális rendszergazda*, *Felhasználói rendszergazda*vagy *Vendégmeghívó*

1. Az Azure Portalon válassza az **Azure Active Directory**lehetőséget, majd válassza az **Identitáscég-irányítási**lehetőséget.

1. A bal oldali ablaktáblában válassza a **Csatlakoztatott szervezetek**lehetőséget, majd a Csatlakoztatott **szervezet hozzáadása**lehetőséget.

    ![A "Csatlakoztatott szervezet hozzáadása" gomb](./media/entitlement-management-organization/connected-organization.png)

1. Válassza az **Alapok** lapot, majd adja meg a szervezet megjelenítendő nevét és leírását.

    ![A "Csatlakoztatott szervezet hozzáadása" alapjai ablaktábla](./media/entitlement-management-organization/organization-basics.png)

1. Válassza a **Címtár + tartomány** lapot, majd a **Címtár hozzáadása + tartomány**lehetőséget.

    Megnyílik **a Könyvtárak kiválasztása + tartományok** ablaktábla.

1. A keresőmezőbe írjon be egy tartománynevet az Azure AD-címtár vagy tartomány kereséséhez. Ügyeljen arra, hogy adja meg a teljes domain nevet.

1. Ellenőrizze, hogy a szervezet neve és a hitelesítés típusa helyes-e. A felhasználók bejelentkezésének módja a hitelesítés típusától függ.

    ![A "Könyvtárak kiválasztása + tartományok" ablaktábla](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Válassza **a Hozzáadás** lehetőséget az Azure AD-címtár vagy -tartomány hozzáadásához. Jelenleg csatlakoztatott szervezetenként csak egy Azure AD-címtárat vagy tartományt adhat hozzá.

    > [!NOTE]
    > Az Azure AD címtárból vagy tartományból származó összes felhasználó kérheti ezt a hozzáférési csomagot. Ez magában foglalja az Azure AD-ben a címtárhoz társított összes altartományfelhasználóit, kivéve, ha ezeket a tartományokat az Azure AD üzleti vállalkozás (B2B) engedélyezési vagy megtagadási lista blokkolja. További információt az [Adott szervezetek B2B-felhasználóinak szóló meghívók engedélyezése vagy letiltása című témakörben talál.](../b2b/allow-deny-list.md)

1. Miután hozzáadta az Azure AD címtárát vagy tartományát, válassza a **Kiválasztás lehetőséget.**

    A szervezet megjelenik a listában.

    ![A "Címtár + tartomány" ablaktábla](./media/entitlement-management-organization/organization-directory-domain.png)

1. Válassza a **Szponzorok** lapot, majd adja hozzá a nem kötelező szponzorokat ehhez a csatlakoztatott szervezethez.

    A szponzorok már a címtárban lévő belső vagy külső felhasználók, akik a kapcsolat felvételének szempontjából a kapcsolattartó pontot képezik. A belső szponzorok a címtár ban lévő tagfelhasználók. A külső szponzorok a csatlakoztatott szervezet olyan vendégfelhasználói, akiket korábban meghívtak, és már szerepelnek a címtárban. A szponzorok jóváhagyóként használhatók, ha a csatlakoztatott szervezet felhasználói hozzáférést kérnek ehhez a hozzáférési csomaghoz. A vendégfelhasználó könyvtárba való meghívásáról az [Azure Active Directory B2B együttműködési felhasználóinak hozzáadása az Azure Portalon](../b2b/add-users-administrator.md)című témakörben talál további információt.

    Ha a **Hozzáadás/eltávolítás lehetőséget választja,** megnyílik egy ablaktábla, amelyben belső vagy külső szponzorokat választhat. Az ablaktábla a címtárban lévő felhasználók és csoportok szűretlen listáját jeleníti meg.

    ![A Szponzorok ablaktábla](./media/entitlement-management-organization/organization-sponsors.png)

1. Válassza a **Véleményezés + Létrehozás** lapot, tekintse át a szervezeti beállításokat, majd válassza a **Létrehozás lehetőséget.**

    ![A "Véleményezés + létrehozás" ablaktábla](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Csatlakoztatott szervezet frissítése 

Ha a csatlakoztatott szervezet egy másik tartományra változik, a szervezet neve megváltozik, vagy módosítani szeretné a szponzorokat, az ebben a szakaszban található utasításokat követve frissítheti a csatlakoztatott szervezetet.

**Előfeltételi szerepkör**: *Globális rendszergazda*, *Felhasználói rendszergazda*vagy *Vendégmeghívó*

1. Az Azure Portalon válassza az **Azure Active Directory**lehetőséget, majd válassza az **Identitáscég-irányítási**lehetőséget.

1. A bal oldali ablaktáblában válassza a **Csatlakoztatott szervezetek**lehetőséget, majd a megnyitáshoz jelölje ki a csatlakoztatott szervezetet.

1. A csatlakoztatott szervezet áttekintő ablaktábláján válassza a **Szerkesztés** lehetőséget a szervezet nevének vagy leírásának módosításához.  

1. A **Címtár + tartomány** ablaktáblán válassza a **Címtár + tartomány frissítése** lehetőséget egy másik könyvtárra vagy tartományra való váltáshoz.

1. A **Szponzorok** ablaktáblán válassza a **Belső szponzorok hozzáadása** vagy Külső szponzorok **hozzáadása** lehetőséget, ha egy felhasználót szponzorként szeretne felvenni. Szponzor eltávolításához jelölje ki a szponzort, és a jobb oldali ablaktáblában válassza a **Törlés**lehetőséget.


## <a name="delete-a-connected-organization"></a>Csatlakoztatott szervezet törlése

Ha már nem rendelkezik kapcsolattal egy külső Azure AD-címtárvagy tartomány, törölheti a csatlakoztatott szervezet.

**Előfeltételi szerepkör**: *Globális rendszergazda*, *Felhasználói rendszergazda*vagy *Vendégmeghívó*

1. Az Azure Portalon válassza az **Azure Active Directory**lehetőséget, majd válassza az **Identitáscég-irányítási**lehetőséget.

1. A bal oldali ablaktáblában válassza a **Csatlakoztatott szervezetek**lehetőséget, majd a megnyitáshoz jelölje ki a csatlakoztatott szervezetet.

1. A csatlakoztatott szervezet áttekintő ablaktábláján válassza a **Törlés** lehetőséget a törléshez.

    Jelenleg csak akkor törölheti a csatlakoztatott szervezetet, ha nincsenek csatlakoztatott felhasználók.

    ![A csatlakoztatott szervezet Törlése gombja](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>További lépések

- [Külső felhasználók hozzáférésének szabályozása](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [A címtárban nem lévő felhasználók hozzáférése szabályozása](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
