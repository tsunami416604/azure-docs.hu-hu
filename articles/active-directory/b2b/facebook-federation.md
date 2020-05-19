---
title: Facebook hozzáadása identitás-szolgáltatóként – Azure AD
description: A összevonása a Facebook használatával lehetővé teheti a külső felhasználók (vendégek) számára, hogy saját Facebook-fiókjával jelentkezzenek be az Azure AD-alkalmazásokba.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a92fbd254f223e2c7eb70a4e86bb7e904294395e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597708"
---
# <a name="add-facebook-as-an-identity-provider-for-external-identities"></a>Facebook hozzáadása identitás-szolgáltatóként külső identitásokhoz

Hozzáadhat Facebook-t önkiszolgáló bejelentkezési felhasználói folyamatokhoz (előzetes verzió), így a felhasználók saját Facebook-fiókjaikat is bejelentkezhetnek az alkalmazásaiba. Ahhoz, hogy a felhasználók bejelentkezzenek a Facebook használatával, először engedélyeznie kell az [önkiszolgáló regisztrációt](self-service-sign-up-user-flow.md) a bérlő számára. Miután hozzáadta a Facebookot identitás-szolgáltatóként, állítson be egy felhasználói folyamatot az alkalmazáshoz, és válassza a Facebook lehetőséget a bejelentkezési lehetőségek közül.

## <a name="create-an-app-in-the-facebook-developers-console"></a>Alkalmazás létrehozása a Facebook Fejlesztői konzolon

Facebook-fiók [identitás-szolgáltatóként](identity-providers.md)való használatához létre kell hoznia egy alkalmazást a Facebook Fejlesztői konzolon. Ha még nem rendelkezik Facebook-fiókkal, regisztrálhat a következő címen: [https://www.facebook.com/](https://www.facebook.com) .

> [!NOTE]  
> Használja az alábbi URL-címeket az alábbi 9. és 16. lépésekben.
> - Adja meg a **webhely URL-címét** `https://login.microsoftonline.com` .
> - **Érvényes OAuth átirányítási URI**-k esetén adja meg a következőt: `https://login.microsoftonline.com/te/<tenant-id>/oauth2/authresp` . A Azure Active Directory áttekintés paneljén található `<tenant-ID>` .


1. A Facebook-fiók hitelesítő adataival jelentkezzen be a [facebookba a fejlesztők számára](https://developers.facebook.com/) .
2. Ha még nem tette meg, regisztrálnia kell Facebook-fejlesztőként. Ehhez válassza az első **lépések** lehetőséget az oldal jobb felső sarkában, fogadja el a Facebook szabályzatait, és végezze el a regisztráció lépéseit.
3. Válassza **a saját alkalmazások** lehetőséget, majd **hozza létre az alkalmazást**.
4. Adjon meg egy **megjelenítendő nevet** és egy érvényes **kapcsolattartási e-mailt**.
5. Válassza az **alkalmazás-azonosító létrehozása**lehetőséget. Előfordulhat, hogy el kell fogadnia a Facebook-platform szabályzatait, és el kell végeznie egy online biztonsági ellenőrzését.
6. Válassza a **Beállítások**  >  **alapszintű**lehetőséget.
7. Válasszon egy **kategóriát**, például az üzleti és a lapokat. Ez az érték a Facebook számára szükséges, de nem használható az Azure AD-hez.
8. A lap alján válassza a **platform hozzáadása**lehetőséget, majd válassza a **webhely**lehetőséget.
9. A **webhely URL-címe**mezőben adja meg a megfelelő URL-címet (lásd fent).
10. Az **adatvédelmi szabályzat URL-címe**mezőbe írja be annak az oldalnak az URL-címét, amelyen az alkalmazás adatvédelmi információit megőrzi, például: `http://www.contoso.com` .
11. Válassza a **módosítások mentése**lehetőséget.
12. Az oldal tetején másolja az **alkalmazás-azonosító**értékét.
13. Válassza az **alkalmazás titkos kulcsának** **megjelenítése** és másolása lehetőséget. Mindkettőt használja a Facebook identitás-szolgáltatóként való konfigurálásához a bérlőben. Az **alkalmazás titkos kulcsa** fontos biztonsági hitelesítő adat.
14. Válassza ki a **termékek**melletti plusz jelet, majd válassza a **beállítás** a **Facebook-Bejelentkezés**alatt lehetőséget.
15. A **Facebook-Bejelentkezés**területen válassza a **Beállítások**lehetőséget.
16. Az **érvényes OAuth átirányítási URI**-k mezőben adja meg a megfelelő URL-címet (lásd fent).
17. A lap alján kattintson a **módosítások mentése** gombra.
18. Annak érdekében, hogy a Facebook-alkalmazás elérhető legyen az Azure AD-ben, válassza ki a lap jobb felső sarkában található állapotjelzőt, és kapcsolja be **, hogy az** alkalmazás nyilvános legyen, majd válassza a **váltás mód**lehetőséget. Ezen a ponton az állapotnak a **fejlesztéstől** az **élő**értékre kell váltania.
    
## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Facebook-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) az Azure ad-bérlő globális rendszergazdájaként.
2. Az **Azure-szolgáltatások**területen válassza a **Azure Active Directory**lehetőséget.
3. A bal oldali menüben válassza a **külső identitások**lehetőséget.
4. Válassza **a minden identitás szolgáltató**lehetőséget, majd válassza a **Facebook**lehetőséget.
5. Az **ügyfél-azonosító**mezőben adja meg a korábban létrehozott Facebook-alkalmazás alkalmazás- **azonosítóját** .
6. Az **ügyfél titka**mezőben adja meg a rögzített **alkalmazás titkát** .

   ![A közösségi identitás-szolgáltató hozzáadása lapot ábrázoló képernyőfelvétel](media/facebook-federation/add-social-identity-provider-page.png)

7. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

- [Külső felhasználók meghívása együttműködésre](add-users-administrator.md)
- [Önkiszolgáló regisztráció hozzáadása egy alkalmazáshoz](self-service-sign-up-user-flow.md)
