---
title: Fejlesztői fiókok engedélyezése az Azure Active Directoryval – az Azure API Management |} A Microsoft Docs
description: Ismerje meg, hogyan engedélyezheti a felhasználók az API Management az Azure Active Directory használatával.
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
ms.openlocfilehash: 6c288e4492ac56436d40d1e3db98af8eb7b173c8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436318"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Az Azure Active Directoryval az Azure API Management fejlesztői fiókok engedélyezése

Ez a cikk bemutatja, hogyan hozzáférés engedélyezése a felhasználók számára a fejlesztői portálra az Azure Active Directory (Azure AD). Ez az útmutató emellett bemutatja, hogyan kezelheti az Azure AD felhasználói csoportokat hozzáadja a felhasználókat tartalmazó külső csoportok.

> [!NOTE]
> Az Azure AD-integráció érhető el a [fejlesztői, Standard és prémium szintű](https://azure.microsoft.com/pricing/details/api-management/) csak szint esetében.

## <a name="prerequisites"></a>Előfeltételek

- Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
- Importálása és közzététele az Azure API Management-példány. További információkért lásd: [importálása és közzététele](import-and-publish.md).

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Fejlesztői fiókok engedélyezése az Azure AD-vel

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Válassza ezt: ![nyíl](./media/api-management-howto-aad/arrow.png).
1. Típus **api** kifejezést a keresőmezőbe.
1. Válassza ki **API Management-szolgáltatások**.
1. Válassza ki az API Management-szolgáltatáspéldányát.
1. A **biztonsági**válassza **identitások**.

1. Válassza ki **+ Hozzáadás** a lista elejéről.

    A **Hozzáadás identitásszolgáltató** ablaktáblán a jobb oldalon jelenik meg.
1. A **szolgáltatótípus**válassza **Azure Active Directory**.

    Vezérlők, amelyek lehetővé teszik, hogy adja meg az egyéb szükséges adatokat a panelen jelennek meg. A vezérlők **ügyfél-azonosító** és **titkos Ügyfélkód**. (A lekérése a vezérlőelemek információt a cikk későbbi részében.)
1. Jegyezze fel a tartalmát a **átirányítási URL-cím**.
    
   ![Az Azure Portalon egy identitásszolgáltató hozzáadásának lépései](./media/api-management-howto-aad/api-management-with-aad001.png)  
1. A böngészőben nyisson meg egy másik lapon. 
1. Nyissa meg az [Azure Portal](https://portal.azure.com).
1. Válassza ezt: ![nyíl](./media/api-management-howto-aad/arrow.png).
1. Típus **aktív**. A **Azure Active Directory** ablaktáblán jelenik meg.
1. Válassza az **Azure Active Directory** elemet.
1. A **kezelés**válassza **alkalmazásregisztrációk**.
1. Válassza az **Új alkalmazás regisztrálása** elemet.

    ![Egy új alkalmazásregisztráció létrehozására vonatkozó beállításokat](./media/api-management-howto-aad/api-management-with-aad002.png)

    A **létrehozás** ablaktáblán a jobb oldalon jelenik meg. Ez hol adja meg az Azure AD-alkalmazás vonatkozó információk.
1. Adjon meg egy nevet az alkalmazásnak.
1. Az alkalmazás típusának válassza **Web app és az API**.
1. A bejelentkezési URL-címet adja meg a bejelentkezési URL-címét a fejlesztői portál. Ebben a példában a bejelentkezési URL-je https://apimwithaad.portal.azure-api.net/signin.
1. Válassza ki **létrehozás** hozhat létre az alkalmazást.
1. Az alkalmazás megkereséséhez válassza ki a **alkalmazásregisztrációk** és Keresés név alapján.

    ![Box, ahol keresése alkalmazás](./media/api-management-howto-aad/find-your-app.png)
1. Miután az alkalmazás regisztrálva van, lépjen be **válasz URL-cím** , és győződjön meg arról, **átirányítási URL-cím** a portáltól kapott 9. lépés értékre van állítva. 
1. Ha szeretne konfigurálni az alkalmazás (például módosítani **azonosító URL-címe**), jelölje be **tulajdonságok**.

    ![A "Tulajdonságok" ablak megnyitása](./media/api-management-howto-aad/api-management-with-aad004.png)

    Ha több Azure AD-példányban használható ehhez az alkalmazáshoz, jelölje be **Igen** a **több-bérlős**. Az alapértelmezett érték **nem**.
1. Állítsa be az Alkalmazásengedélyek kiválasztásával **szükséges engedélyek**.
1. Válassza ki az alkalmazását, és válassza a **címtáradatok olvasása** és **jelentkezzen be a felhasználói profil olvasása és** jelölőnégyzeteket.

    ![A jelölőnégyzetek az engedélyek](./media/api-management-howto-aad/api-management-with-aad005.png)

    További információ az alkalmazás és delegált engedélyek: [eléréséhez a Graph API][Accessing the Graph API].
1. A bal oldali ablaktáblán, másolja a **Alkalmazásazonosító** értéket.

    !["Az alkalmazás Azonosítójának" érték](./media/api-management-howto-aad/application-id.png)
1. Váltson vissza az API Management alkalmazás. 

    Az a **Hozzáadás identitásszolgáltató** ablakban illessze be a **Alkalmazásazonosító** értékét a **ügyfél-azonosító** mezőbe.
1. Váltson vissza az Azure AD-konfigurációjának, és válassza ki **kulcsok**.
1. Hozzon létre egy új kulcsot adjon meg egy nevet és egy időtartam. 
1. Kattintson a **Mentés** gombra. A kulcs akkor jön létre.

    Másolja a vágólapra a kulcsot.

    ![A kulcs létrehozására vonatkozó beállításokat](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > Jegyezze fel ezt a kulcsot. Miután bezárta az Azure AD-konfiguráció panelen, a kulcs nem jeleníthető meg újra.
    > 
    > 
1. Váltson vissza az API Management alkalmazás. 

    A a **Hozzáadás identitásszolgáltató** ablakban illessze be a kulcsot a **titkos Ügyfélkód** szövegmező.

    > [!IMPORTANT]
    > Ellenőrizze, hogy frissítette a **titkos Ügyfélkód** a kulcs érvényességének lejárta előtt. 
    >  
    >
1. A **Hozzáadás identitásszolgáltató** ablakban is tartalmaz a **engedélyezett bérlők** szövegmezőben. Itt adja meg a tartományban, amelyhez hozzá szeretné hozzáférést biztosítani az API-k az API Management szolgáltatáspéldányt, az Azure AD-példányban. Több tartomány elválasztás sortörésekből álló szóközöket tartalmazza, szóközök és vesszővel válassza el egymástól.

    A több tartományt is megadhat a **engedélyezett bérlők** szakaszban. Minden felhasználó egy másik tartományban található, mint az eredeti tartomány, ahol az alkalmazás regisztrálva lett a bejelentkezéshez, a másik tartományban globális rendszergazdájának engedélyt kell az alkalmazás hozzáférési címtáradatok. Engedélyt adni, a globális rendszergazdának kell elvégeznie:
    
    a. Lépjen a `https://<URL of your developer portal>/aadadminconsent` (például https://contoso.portal.azure-api.net/aadadminconsent).
    
    b. Írja be az Azure AD-bérlővel, amelyeket be szeretne hozzáférést biztosít a tartomány nevét.
    
    c. Válassza ki **elküldése**. 
    
    A következő példában a globális rendszergazdájának miaoaad.onmicrosoft.com próbál engedélyt az adott fejlesztői portálra. 

1. Miután megadta a szükséges konfiguráció, válassza ki a **Hozzáadás**.

    !["Felvétele" gombra a "Hozzáadás identitásszolgáltató" ablaktáblán](./media/api-management-howto-aad/api-management-with-aad007.png)

Miután menti a módosításokat, felhasználók a megadott Azure ad-példány bejelentkezhet a fejlesztői portál által leírt lépések végrehajtásával [egy olyan Azure AD felhasználói fiókkal jelentkezzen be a fejlesztői portál](#log_in_to_dev_portal).

![Az Azure AD-bérlő nevének megadása](./media/api-management-howto-aad/api-management-aad-consent.png)

A következő képernyőn a rendszer kéri a globális rendszergazda jogosultságot ad a megerősítése. 

![Engedélyek hozzárendelése a megerősítése](./media/api-management-howto-aad/api-management-permissions-form.png)

Ha a nem globális rendszergazda megpróbál bejelentkezni, mielőtt engedélyt ad egy globális rendszergazdai, a bejelentkezési kísérlet sikertelen lesz, és egy hiba képernyő jelenik meg.

## <a name="add-an-external-azure-ad-group"></a>Adjon hozzá egy külső Azure AD-csoport

Miután engedélyezte a hozzáférést a felhasználók számára az Azure AD-példányban, az API Management az Azure AD-csoportokat is hozzáadhat. Ezt követően könnyebben kezelheti a társítást a csoportban a fejlesztők a kívánt termékeket.

Egy külső konfigurálása az Azure AD-csoporthoz, először konfigurálnia kell az Azure AD-példányt a **identitások** lapon az alábbi eljárást az előző szakaszban. 

Hozzáadhat külső Azure AD csoportkezelési származó a **csoportok** lap az API Management-példány.

1. Válassza ki a **Csoportok** lapot.
1. Válassza ki a **AAD hozzáadása csoporthoz** gombra.
   !["Az AAD-csoport hozzáadása" gomb](./media/api-management-howto-aad/api-management-with-aad008.png)
1. Válassza ki a hozzáadni kívánt csoportot.
1. Nyomja le az **kiválasztása** gombra.

Miután hozzáadott egy külső Azure ad-ben csoport tekintheti át és konfigurálja a tulajdonságait. Válassza ki a csoport nevét a **csoportok** fülre. Itt szerkesztheti **neve** és **leírás** csoport adatait.
 
A konfigurált felhasználók Azure AD-példányt most már bejelentkezhet a fejlesztői portálra. Megtekintheti, és iratkozzon fel azokat a csoportokat, láthatóság rendelkeznek.

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Egy olyan Azure AD felhasználói fiókkal jelentkezzen be a fejlesztői portál

A jelentkezzen be a fejlesztői portál egy Azure AD-fiókot, amely az előző szakaszokban konfigurált használatával:

1. Nyisson meg egy új böngészőablakot a bejelentkezési URL-cím az Active Directory-alkalmazás konfigurációjából, és válassza ki **Azure Active Directory**.

   ![Bejelentkezési oldal][api-management-dev-portal-signin]

1. Egyik felhasználó hitelesítő adatainak megadása az Azure ad-ben, valamint **jelentkezzen be a**.

   ![Bejelentkezés felhasználónévvel és jelszóval][api-management-aad-signin]

1. Kérheti egy regisztrációs űrlapot, ha minden olyan további információra szükség. Töltse ki a regisztrációs űrlapot, és válassza ki **regisztráció**.

   !["Regisztráció" gombra a regisztrációs űrlapot][api-management-complete-registration]

A felhasználó már bejelentkezett a fejlesztői portálra, az API Management szolgáltatáspéldányhoz tartozó.

![Regisztráció befejezése után a fejlesztői portál][api-management-registration-complete]

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
