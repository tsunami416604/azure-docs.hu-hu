---
title: "Engedélyezi a fejlesztői fiókok Azure Active Directory - Azure API Management használata |} Microsoft Docs"
description: "Útmutató az Azure Active Directoryval az API Management felhasználók hitelesítéséhez."
services: api-management
documentationcenter: API Management
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: c9d99d6f7c2777c8e68f5db50b402280377d88e9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Hogyan szeretné engedélyekkel felruházni fejlesztői fiókok Azure Active Directory használatával az Azure API Management

Ez az útmutató bemutatja, hogyan engedélyezze a hozzáférést a fejlesztői portálhoz, a felhasználók számára az Azure Active Directoryból. Ez az útmutató is bemutatja, hogyan Azure Active Directory-felhasználók kezelése külső csoportok, amelyek tartalmazzák az Azure Active Directory a felhasználók hozzáadásával.

> [!WARNING]
> Az Azure Active Directory-integráció érhető el a [fejlesztői, Standard és Premium](https://azure.microsoft.com/pricing/details/api-management/) csak tiers.

## <a name="prerequisites"></a>Előfeltételek

- Fejezze be a következő gyorsindítási: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).
- Importálja, és tegye közzé az API Management-példány. További információkért lásd: [importálása és közzététele](import-and-publish.md).

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Hogyan szeretné engedélyekkel felruházni fejlesztői fiókok Azure Active Directory használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Válassza ezt: ![nyíl](./media/api-management-howto-aad/arrow.png).
3. A keresőmezőbe írja be a "api".
4. Kattintson a **API Management-szolgáltatások**.
5. Válassza ki a APIM szolgáltatáspéldány.
6. A **biztonsági**, jelölje be **identitások**.

    ![Külső identitások](./media/api-management-howto-aad/api-management-with-aad001.png)
7. Kattintson a **+ Hozzáadás** a lista elejéről.

    A **Hozzáadás identitásszolgáltató** ablak jobb oldalt megjelenik.
8. A **szolgáltatótípust**, jelölje be **Azure Active Directory**.

    Vezérlők, amelyek lehetővé teszik, hogy más szükséges információt a ablakban jelennek meg. A vezérlők használhatók: **ügyfél-azonosító**, **ügyfélkulcs** (kap információkat az oktatóanyag későbbi részében).
9. Jegyezze fel a **átirányítási URL-cím**.  
10. A böngészőben nyissa meg egy másik lapján. 
11. Nyissa meg az [Azure Portalt](https://portal.azure.com).
12. Válassza ezt: ![nyíl](./media/api-management-howto-aad/arrow.png).
13. Típus: "active", a **Azure Active Directory** jelenik meg.
14. Válassza ki **az Azure Active Directory**.
15. A **kezelése**, jelölje be **App regisztrációs**.

    ![Appok regisztrálása](./media/api-management-howto-aad/api-management-with-aad002.png)
16. Kattintson az **Új alkalmazásregisztráció** elemre.

    A **létrehozása** ablak jobb oldalt megjelenik. Ez az alkalmazás aad-ben relavent információkat rögzítheti.
17. Eneter az alkalmazás nevét.
18. Az alkalmazástípus kiválasztása **Web app/API**.
19. Bejelentkezési URL-címet adja meg a bejelentkezési URL-CÍMÉT a fejlesztői portálján. Ebben a példában a bejelentkezési URL-je: https://apimwithaad.portal.azure-api.net/signin.
20. Kattintson a **létrehozása** az alkalmazás létrehozására.
21. Megkeresheti az alkalmazást, jelölje be **App regisztrációk** és keresés alapján.

    ![Appok regisztrálása](./media/api-management-howto-aad/find-your-app.png)
22. Az alkalmazás regisztrálása után Ugrás **válasz URL-CÍMEN** , és győződjön meg arról, hogy a "átirányítási URL-cím" értéke a portáltól kapott 9. lépés marad. 
23. Ha azt szeretné, hogy az alkalmazás konfigurálása (például **azonosító URL-címet**) kiválasztása **tulajdonságok**.

    ![Appok regisztrálása](./media/api-management-howto-aad/api-management-with-aad004.png)

    Ha több aktív Azure-könyvtárak nem használható ehhez az alkalmazáshoz, kattintson a **Igen** a **alkalmazás több-bérlős**. Az alapértelmezett érték **nem**.
24. Alkalmazásengedélyek beállítása kiválasztásával **szükséges engedélyek**.
25. Válassza ki az alkalmazáshoz, és jelölje **címtáradatok olvasása** és **jelentkezzen be a felhasználói profil olvasása és**.

    ![Appok regisztrálása](./media/api-management-howto-aad/api-management-with-aad005.png)

    További információ az alkalmazás és a delegált jogosultságokkal sikeresen telepítették: [fér hozzá a Graph API][Accessing the Graph API].
26. A bal oldali, másolja a **Alkalmazásazonosító** érték.

    ![Appok regisztrálása](./media/api-management-howto-aad/application-id.png)
27. Lépjen vissza az API Management alkalmazás. A **Hozzáadás identitásszolgáltató** ablak üzenetnek kell megjelennie. <br/>Beillesztés a **Alkalmazásazonosító** értéket a **ügyfél-azonosító** mezőbe.
28. Váltson vissza az az Azure Active Directory konfigurációját, és kattintson a **kulcsok**.
29. Új kulcs létrehozásához speicifying nevét és időtartama. 
30. Kattintson a **Save** (Mentés) gombra. A kulcs létrehozása lekérdezi.

    Másolja a vágólapra a kulcsot.

    ![Appok regisztrálása](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > Jegyezze fel ezt a kulcsot. Az Azure Active Directory konfigurációs ablak bezárása után a kulcs nem jeleníthető meg újra.
    > 
    > 
31. Lépjen vissza az API Management alkalmazás. <br/>A a **Hozzáadás identitásszolgáltató** ablakban illessze be a kulcs a **ügyfélkulcs** szövegmezőben.
32. A **Hozzáadás identitásszolgáltató** ablakban, a **engedélyezett bérlők** a szövegmezőben adja meg, mely könyvtárak van az API Management service-példány az API-k eléréséhez. <br/>Adja meg a tartomány, amelyhez hozzáférést szeretne az Azure Active Directory-példányok. Elkülönítheti a több tartomány ágyazódjanak, szóközzel vagy vesszővel válassza el egymástól.

    Több tartomány is megadhatók a **engedélyezett bérlők** szakasz. Előtt minden olyan felhasználó, mint az eredeti tartomány, ahol az alkalmazás regisztrálva lett más tartományokból is bejelentkezhetnek, ugyanaz a tartományi globális rendszergazdájának engedélyeznie kell az alkalmazás hozzáférési címtáradatok. Adja meg az engedélyt, a globális rendszergazdának kell lépjen `https://<URL of your developer portal>/aadadminconsent` (például https://contoso.portal.azure-api.net/aadadminconsent), írja be a tartomány nevét annak az Active Directory-bérlő szeretnék a hozzáférést, és kattintson a Küldés gombra. A következő példában a globális rendszergazdájának `miaoaad.onmicrosoft.com` próbál engedélyt az adott fejlesztői portálra. 

33. Ha a szükséges konfiguráció van megadva, kattintson **Hozzáadás**.

    ![Appok regisztrálása](./media/api-management-howto-aad/api-management-with-aad007.png)

Ha menti a módosításokat, a felhasználók a megadott Azure Active Directoryban bármikor beléphet a fejlesztői portálhoz lépéseit követve [jelentkezzen be egy Azure Active Directory-fiókot a fejlesztői portálra](#log_in_to_dev_portal).

![Engedélyek](./media/api-management-howto-aad/api-management-aad-consent.png)

A következő képernyőn a globális rendszergazdai jogosultságot ad az engedély megerősítéséhez kéri. 

![Engedélyek](./media/api-management-howto-aad/api-management-permissions-form.png)

Ha a nem globális rendszergazda megpróbál bejelentkezni, mielőtt a globális rendszergazda által megadott engedélyekkel, a bejelentkezési kísérlet sikertelen lesz, és egy hiba történt a képernyő jelenik meg.

## <a name="how-to-add-an-external-azure-active-directory-group"></a>Egy külső Azure Active Directory-csoport hozzáadása

Miután engedélyezte a hozzáférést a felhasználók számára egy Azure Active Directory, Azure Active Directory-csoportok is hozzáadhat, az API Management könnyebben kezelhetik a kívánt termékek a fejlesztők a csoport társítását.

Egy külső Azure Active Directory-csoport konfigurálásához, az Azure Active Directory először konfigurálni kell az identitások lapon az alábbi eljárást az előző szakaszban. 

Külső Active Directory-csoportok hozzáadott a **csoportok** fülre az API Management-példány.

![Csoportok](./media/api-management-howto-aad/api-management-with-aad008.png)

1. Válassza ki a **Csoportok** lapot.
2. Kattintson a **AAD hozzáadása csoporthoz** gombra.
3. Válassza ki a hozzáadni kívánt csoportot.
4. Nyomja le az **válasszon** gombra.

Egy Azure Active Directory-csoport létrehozása után áttekintheti és konfigurálni a tulajdonságait külső olyan csoportot, ha azok hozzáadott, kattintson a csoport nevét a **csoportok** fülre.

Itt szerkesztheti a **neve** és a **leírás** a csoport.
 
A konfigurált Azure Active Directory felhasználók jelentkezzen be a fejlesztői portálján, tekintse meg, és minden olyan csoportot, amelyeknél látható a következő szakaszban található utasításokat követve előfizetni.

## <a name="a-idlogintodevportalhow-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a><a id="log_in_to_dev_portal"/>A fejlesztői portálra egy Azure Active Directory-fiókot bejelentkezés

A fejlesztői portálra a korábbi szakaszokban konfigurált Azure Active Directory-fiókkal bejelentkezni, nyisson meg egy új böngészőt ablak használatával a **bejelentkezési URL-cím** az Active Directory-alkalmazás konfigurációból kattintson**Az azure Active Directory**.

![Fejlesztői portálján][api-management-dev-portal-signin]

Adja meg az egyik felhasználó hitelesítő adatait az Azure Active Directoryban, és kattintson a **bejelentkezés**.

![Bejelentkezés][api-management-aad-signin]

Kérheti a regisztrációs űrlap Ha bármilyen további információkra szükség. Végezze el a regisztrációs képernyő, és kattintson a **regisztráljon**.

![Regisztráció][api-management-complete-registration]

A felhasználó most jelentkezett be a fejlesztői portálra, az API Management szolgáltatáspéldány.

![A regisztráció kész.][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
