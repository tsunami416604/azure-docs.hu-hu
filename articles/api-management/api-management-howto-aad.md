---
title: Azure Active Directory - Azure API Management segítségével hitelesíthetők a fejlesztői fiókok |} Microsoft Docs
description: Tudnivalók a felhasználók hitelesítése az API Management az Azure Active Directory használatával.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: 9f3669d205ab4bd24ccba53ffb532fe1d88131ac
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Az Azure API Management Azure Active Directory segítségével hitelesíthetők a fejlesztői fiókok

Ez a cikk bemutatja, hogyan engedélyezze a hozzáférést a fejlesztői portálhoz, a felhasználók számára az Azure Active Directory (Azure AD). Ez az útmutató is bemutatja, hogyan kezelheti az Azure AD felhasználói csoportokat külső csoportnak, a felhasználók hozzáadásával.

> [!NOTE]
> Az Azure AD-integrációs érhető el a [fejlesztői, Standard és Premium](https://azure.microsoft.com/pricing/details/api-management/) csak tiers.

## <a name="prerequisites"></a>Előfeltételek

- Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
- Importálja, és tegye közzé az Azure API Management-példány. További információkért lásd: [importálása és közzététele](import-and-publish.md).

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Engedélyezi a fejlesztői fiókok Azure AD használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Válassza ezt: ![nyíl](./media/api-management-howto-aad/arrow.png).
3. Típus **api** be a keresőmezőbe.
4. Válassza ki **API Management-szolgáltatások**.
5. Válassza ki az API Management szolgáltatáspéldány.
6. A **biztonsági**, jelölje be **identitások**.

7. Válassza ki **+ Hozzáadás** a lista elejéről.

    A **Hozzáadás identitásszolgáltató** ablaktáblán a jobb oldalon jelenik meg.
8. A **szolgáltatótípust**, jelölje be **Azure Active Directory**.

    Vezérlők, amelyek lehetővé teszik, hogy más szükséges információt a ablaktáblán jelennek meg. A vezérlők használhatók **ügyfél-azonosító** és **ügyfélkulcs**. (A get vezérlőkkel kapcsolatos információk a cikk későbbi részében.)
9. Jegyezze fel a tartalmát a **átirányítási URL-cím**.
    
   ![Az identitásszolgáltató hozzáadása az Azure portálon lépései](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. A böngészőben nyissa meg egy másik lapján. 
11. Nyissa meg az [Azure Portal](https://portal.azure.com).
12. Válassza ezt: ![nyíl](./media/api-management-howto-aad/arrow.png).
13. Típus **aktív**. A **Azure Active Directory** ablaktáblán jelenik meg.
14. Válassza az **Azure Active Directory** elemet.
15. A **kezelése**, jelölje be **App regisztrációk**.
16. Válassza az **Új alkalmazás regisztrálása** elemet.

    ![Új alkalmazás regisztrációjának létrehozására vonatkozó beállításokat](./media/api-management-howto-aad/api-management-with-aad002.png)

    A **létrehozása** ablaktáblán a jobb oldalon jelenik meg. Ez hol írja be a Azure AD alkalmazás-vonatkozó információkat.
17. Adjon meg egy nevet az alkalmazásnak.
18. Az alkalmazástípus kiválasztása **Web app/API**.
19. A bejelentkezési URL-címet adja meg a bejelentkezési URL-CÍMÉT a fejlesztői portálján. Ebben a példában a bejelentkezési URL-je https://apimwithaad.portal.azure-api.net/signin.
20. Válassza ki **létrehozása** az alkalmazás létrehozására.
21. Megkeresheti az alkalmazást, jelölje be **App regisztrációk** és keresés alapján.

    ![Ahol keres egy alkalmazás bezárásához](./media/api-management-howto-aad/find-your-app.png)
22. Az alkalmazás regisztrálása után Ugrás **válasz URL-CÍMEN** , és győződjön meg arról, hogy **átirányítási URL-cím** portáltól kapott 9. lépés a értékre van beállítva. 
23. Ha azt szeretné, hogy az alkalmazás konfigurálása (például **azonosító URL-címet**) elemre, jelölje be **tulajdonságok**.

    ![A "Tulajdonságok" ablak megnyitása](./media/api-management-howto-aad/api-management-with-aad004.png)

    Ha több Azure AD-példányban ehhez az alkalmazáshoz fog használni, jelölje be **Igen** a **Multi-központjaként**. Az alapértelmezett érték **nem**.
24. Alkalmazásengedélyek beállítása kiválasztásával **szükséges engedélyek**.
25. Válassza ki az alkalmazást, majd válassza ki a **címtáradatok olvasása** és **jelentkezzen be a felhasználói profil olvasása és** jelölőnégyzeteket.

    ![Jelölőnégyzetek az engedélyek](./media/api-management-howto-aad/api-management-with-aad005.png)

    További információ az alkalmazás és a delegált engedélyek: [fér hozzá a Graph API][Accessing the Graph API].
26. A bal oldali ablaktáblán, másolja a **Alkalmazásazonosító** érték.

    !["Alkalmazásazonosító" érték](./media/api-management-howto-aad/application-id.png)
27. Lépjen vissza az API Management alkalmazás. 

    Az a **Hozzáadás identitásszolgáltató** ablakban illessze be a **Alkalmazásazonosító** értéket a **ügyfél-azonosító** mezőbe.
28. Váltson vissza az az Azure Active Directory beállítása, és válassza ki **kulcsok**.
29. Hozza létre az új kulcs nevét és időtartama megadásával. 
30. Kattintson a **Mentés** gombra. A kulcs jön létre.

    Másolja a vágólapra a kulcsot.

    ![A kulcs létrehozására vonatkozó beállításokat](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > Jegyezze fel ezt a kulcsot. Az Azure AD konfigurációs ablak bezárását követően a kulcs nem jeleníthető meg újra.
    > 
    > 
31. Lépjen vissza az API Management alkalmazás. 

    Az a **Hozzáadás identitásszolgáltató** ablakban illessze be a kulcsot a a **ügyfélkulcs** szövegmezőben.
32. A **Hozzáadás identitásszolgáltató** ablakban találhatók a **engedélyezett bérlők** szövegmezőben. Itt adja meg a tartomány, amelyhez hozzáférést biztosítson az API-k, az API Management szolgáltatáspéldány szeretné az Azure AD-példánya. Elkülönítheti a több tartomány ágyazódjanak, szóközzel vagy vesszővel válassza el egymástól.

    A több tartományt is megadhat a **engedélyezett bérlők** szakasz. Minden olyan felhasználó, mint az eredeti tartomány, ahol az alkalmazás regisztrálva lett más tartományokból bejelentkezés, előtt ugyanaz a tartományi globális rendszergazdájának engedélyeznie kell az alkalmazás hozzáférési címtáradatok. Engedélyt szeretne megadni, a globális rendszergazdának a következőket:
    
    a. Ugrás a `https://<URL of your developer portal>/aadadminconsent` (például https://contoso.portal.azure-api.net/aadadminconsent).
    
    b. Adja meg, amelyeket be szeretne hozzáférést az Azure AD-bérlő tartománynevét.
    
    c. Válassza ki **nyújt**. 
    
    A következő példában a globális rendszergazdájának miaoaad.onmicrosoft.com próbál engedélyt az adott fejlesztői portálra. 

33. A kívánt konfigurációs megadása után válassza ki a **Hozzáadás**.

    !["Hozzáadás" gombra a "Hozzáadás identitásszolgáltató" ablaktáblán](./media/api-management-howto-aad/api-management-with-aad007.png)

Miután menti a módosításokat, felhasználók az Azure ad-ben megadott példányhoz való bejelentkezés a fejlesztői portálján a lépések [az Azure AD-fiókkal jelentkezzen be a fejlesztői portálra](#log_in_to_dev_portal).

![Megadja annak az Azure AD-bérlő nevét](./media/api-management-howto-aad/api-management-aad-consent.png)

A következő képernyőn a globális rendszergazdai jogosultságot ad az engedély megerősítéséhez kéri. 

![Engedélyek hozzárendelése megerősítése](./media/api-management-howto-aad/api-management-permissions-form.png)

A nem globális rendszergazda kísérli meg jelentkezzen be, mielőtt engedélyt ad egy globális rendszergazda, ha a bejelentkezés sikertelen lesz, és egy hiba történt a képernyő jelenik meg.

## <a name="add-an-external-azure-ad-group"></a>Egy külső hozzáadása az Azure AD-csoport

Miután engedélyezte a hozzáférést a felhasználók számára az Azure AD-példányban, az API Management az Azure AD-csoportok is hozzáadhat. Ezt követően könnyebben kezelheti a kívánt termékek a fejlesztők a csoport társítását.

Egy külső konfigurálása az Azure AD-csoport, először konfigurálnia kell az Azure AD-példányhoz a **identitások** lapon az alábbi eljárást az előző szakaszban. 

Hozzáadhat külső Azure AD a csoportok a **csoportok** fülre az API Management-példány.

1. Válassza ki a **Csoportok** lapot.
2. Válassza ki a **AAD hozzáadása csoporthoz** gombra.
   !["Az AAD-csoport hozzáadása" gombra](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Válassza ki azt a csoportot, amelyhez hozzá szeretné adni.
4. Nyomja meg a **válasszon** gombra.

Miután hozzáadta az Azure AD külső csoport, tekintse át, és konfigurálja a tulajdonságait. Válassza ki a csoport nevét a **csoportok** fülre. Itt szerkesztheti **neve** és **leírás** csoport adatait.
 
A konfigurált felhasználók Azure AD-példányban most már bejelentkezhet a fejlesztői portálján. Tekinthet meg és minden olyan csoportot, amelyeknél az láthatósági előfizetni.

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Az Azure AD-fiókkal jelentkezzen be a fejlesztői portálra

A bejelentkezéshez a fejlesztői portálhoz az Azure AD-fiókot, amely a korábbi szakaszokban konfigurált használatával:

1. A bejelentkezési URL-CÍMÉT az Active Directory-alkalmazás konfigurációja nyisson meg egy új böngészőablakot, és válassza ki **Azure Active Directory**.

   ![Bejelentkezési oldal][api-management-dev-portal-signin]

2. Egyik felhasználó hitelesítő adatainak megadása az Azure ad-ben, valamint **bejelentkezés**.

   ![Bejelentkezés a felhasználónév és jelszó][api-management-aad-signin]

3. Előfordulhat, hogy kérni fogja a regisztrációs űrlap Ha bármilyen további információkra szükség. Végezze el a regisztrációs képernyő, és válassza ki **regisztráljon**.

   ![Regisztrációs űrlap "Előfizetés" gombja][api-management-complete-registration]

A felhasználó a fejlesztői portálhoz, az API Management szolgáltatáspéldány most jelentkezett be.

![Fejlesztői portálján regisztráció befejezése után][api-management-registration-complete]

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

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
