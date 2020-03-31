---
title: Fejlesztői fiókok engedélyezése az Azure Active Directory használatával
titleSuffix: Azure API Management
description: Megtudhatja, hogyan engedélyezheti a felhasználókat az Azure Active Directory használatával az API Management ben.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 6102b1e1d6ddbac01033b9cecfeba96a7eb33777
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473540"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Fejlesztői fiókok engedélyezése az Azure Active Directory használatával az Azure API Managementben

Ez a cikk bemutatja, hogyan engedélyezheti a hozzáférést a fejlesztői portálhoz az Azure Active Directory (Azure AD) felhasználói számára. Ez az útmutató azt is bemutatja, hogyan kezelheti az Azure AD-felhasználók csoportjait a felhasználókat tartalmazó külső csoportok hozzáadásával.

## <a name="prerequisites"></a>Előfeltételek

- Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
- Azure API Management-példány importálása és közzététele. További információt az Importálás és közzététel című [témakörben talál.](import-and-publish.md)

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Fejlesztői fiókok engedélyezése az Azure AD használatával

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 
2. Válassza ezt: ![nyíl](./media/api-management-howto-aad/arrow.png).
3. Írja be az **API** kifejezést a keresőmezőbe.
4. Válassza az **API Management services lehetőséget.**
5. Válassza ki az API Management-szolgáltatáspéldányát.
6. A **Biztonság csoportban**válassza **az Identitások lehetőséget.**
7. Felülről válassza a **+Hozzáadás** lehetőséget.

    Az **Identitásszolgáltató hozzáadása** ablaktábla a jobb oldalon jelenik meg.
8. A **Szolgáltató típusa csoportban**válassza az **Azure Active Directory**lehetőséget.

    Az ablaktáblán megjelennek azok a vezérlők, amelyek lehetővé teszik más szükséges adatok bevitelét. A vezérlők közé tartozik **az ügyfélazonosító** és **az ügyféltitok**. (Ezekről a vezérlőkről a cikk későbbi részében olvashat.)
9. Jegyezze fel az **átirányítási URL tartalmát.**
    
   ![Identitásszolgáltató azure portalon való hozzáadásának lépései](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. Nyisson meg egy másik lapot a böngészőben. 
11. Keresse meg az [Azure Portal – Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) alkalmazást regisztrálva egy alkalmazást az Active Directoryban.
12. A **Kezelés csoportban**válassza **az Alkalmazásregisztrációk lehetőséget.**
13. Válassza **az Új regisztráció lehetőséget.** A **Jelentkezés regisztrálása** lapon állítsa be az értékeket a következőképpen:
    
    * A **Név** beállítása értelmes névre. pl. *fejlesztő-portál*
    * Állítsa **a támogatott fióktípusokat** **csak ebben a szervezeti címtárban lévő fiókok elemre.** 
    * Állítsa be az **URI átirányítását** a 9. 
    * Válassza a **Regisztráció**lehetőséget. 

14.  Az alkalmazás regisztrálása után másolja az **alkalmazás (ügyfél) azonosítóját** az **Áttekintés** lapról. 
15. Lépjen vissza az API Management-példányhoz. Az **Identitásszolgáltató hozzáadása** ablakban illessze be az **alkalmazás (ügyfél) azonosító** értékét az **Ügyfélazonosító** mezőbe.
16. Váltson vissza az Azure AD-konfigurációra, válassza ki **a tanúsítványokat & titkos kulcsokat** **a Kezelés csoportban.** Válassza az **Új ügyféltitkos** kulcsot gombot. Írjon be egy értéket a **Leírás**mezőbe, válassza a **Lejárás** beállítását, és válassza a **Hozzáadás lehetőséget.** A lap elhagyása előtt másolja az ügyféltitkos értéket. A következő lépésben szüksége lesz ezekre. 
17. A **Kezelés csoportban**válassza a **Hitelesítés** lehetőséget, majd válassza **az Azonosítótokenek** lehetőséget az Implicit támogatás csoportban. **Implicit Grant**
18. Lépjen vissza az API Management-példányhoz, illessze be a titkos kulcsot az **ügyfél titkos** mezőjébe.

    > [!IMPORTANT]
    > A kulcs lejárta előtt frissítse az **ügyféltitkot.** 
    >  
    >

19. Az **Identitásszolgáltató hozzáadása** ablak tartalmazza az Engedélyezett bérlők szövegmezőt is. **Allowed Tenants** Itt adja meg az Azure AD-példányok tartományait, amelyekhez hozzáférést szeretne adni az API Management szolgáltatáspéldány API-khoz. Több tartományt is elválaszthat új vonalakkal, szóközökkel vagy vesszőkkel.

    > [!NOTE]
    > Több tartományt is megadhat az **Engedélyezett bérlők** szakaszban. Ahhoz, hogy bármely felhasználó bejelentkezzen egy másik tartományból, mint az eredeti tartomány, ahol az alkalmazás regisztrálva volt, a különböző tartomány globális rendszergazdájának engedélyt kell adnia az alkalmazás számára a címtáradatok elérésére. Az engedély megadásához a globális rendszergazdának: a. Ugrás `https://<URL of your developer portal>/aadadminconsent` (például https://contoso.portal.azure-api.net/aadadminconsent).
    > b. Írja be az Azure AD-bérlő tartománynevét, amelyhez hozzáférést kíván adni.
    > c. Válassza a **Küldés** lehetőséget. 

20.  Miután megadta a kívánt konfigurációt, válassza a **Hozzáadás**lehetőséget.

A módosítások mentése után a megadott Azure AD-példány felhasználói bejelentkezhetnek a fejlesztői portálra az [Azure AD-fiók használatával a Bejelentkezés a fejlesztői portálra](#log_in_to_dev_portal)című lépéseit követve.

## <a name="add-an-external-azure-ad-group"></a>Külső Azure AD-csoport hozzáadása

Miután engedélyezte a hozzáférést a felhasználók számára egy Azure AD-bérlőben, hozzáadhatja az Azure AD-csoportokat az API Managementhez. Ennek eredményeképpen az Azure AD-csoportok használatával szabályozhatja a termékek láthatóságát.

Egy külső Azure AD-csoport hozzáadása az APIM-be, először ki kell töltenie az előző szakaszt. Ezenkívül a regisztrált alkalmazásnak az alábbi lépésekkel `Directory.Read.All` engedéllyel hozzáférést kell biztosítania a Microsoft Graph API-hoz: 

1. Lépjen vissza az előző szakaszban létrehozott alkalmazásregisztrációhoz.
2. Válassza az **API-engedélyek**lehetőséget, majd kattintson **az engedély hozzáadása**gombra. 
3. Az **API-engedélyek kérése** ablaktáblán jelölje ki a **Microsoft API-k** lapot, majd a **Microsoft Graph** csempét. Válassza **az Alkalmazásengedélyek**lehetőséget , keresse meg a **Címtárat,** majd válassza a **Directory.Read.All** engedélyt. 
4. Kattintson az **ablaktábla** alján az Engedélyek hozzáadása elemre, majd **a(z) {tenantname} rendszergazdai hozzájárulásának megadása** elemre, hogy hozzáférést biztosítson a címtár összes felhasználója számára. 

Most antól hozzáadhat külső Azure AD-csoportokat az API Management-példány **Csoportok** lapjáról.

1. Válassza ki a **Csoportok** lapot.
2. Válassza az **AAD-csoport hozzáadása** gombot.
    !["AAD-csoport hozzáadása" gomb](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Jelölje ki a hozzáadni kívánt csoportot.
4. Nyomja meg a **Kijelölés** gombot.

Egy külső Azure AD-csoport hozzáadása után áttekintheti és konfigurálhatja a tulajdonságait. A **Csoportok** lapon jelölje ki a csoport nevét. Itt szerkesztheti a csoport **nevét** és **leírását.**
 
A konfigurált Azure AD-példány felhasználói most már bejelentkezhetnek a fejlesztői portálra. Megtekinthetik és feliratkozhatnak azokkal a csoportokkal, amelyek számára láthatóak.

## <a name="developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"/>Fejlesztői portál – Azure AD-fiók hitelesítésének hozzáadása

A fejlesztői portálon az AAD-vel való bejelentkezés a **Bejelentkezés gombbal lehetséges: OAuth** widget. A widget már szerepel az alapértelmezett fejlesztői portál tartalom bejelentkezési oldalán.

Bár egy új fiók automatikusan létrejön, amikor egy új felhasználó bejelentkezik az AAD-vel, érdemes lehet ugyanazt a widgetet hozzáadni a regisztrációs oldalhoz.

A **Regisztrációs űrlap: Az OAuth** widget az OAuth-ra való regisztrációhoz használt űrlapot jelöli.

> [!IMPORTANT]
> Az AAD-módosítások érvénybe léptetéséhez újra közzé kell tennie [a portált.](api-management-howto-developer-portal-customize.md#publish)

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Örökölt fejlesztői portál – bejelentkezés az Azure AD-vel

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Bejelentkezés a fejlesztői portálra az előző szakaszokban konfigurált Azure AD-fiók használatával:

1. Nyisson meg egy új böngészőablakot az Active Directory alkalmazáskonfigurációjából származó bejelentkezési URL-cím használatával, és válassza az **Azure Active Directory**lehetőséget.

   ![Bejelentkezési lap][api-management-dev-portal-signin]

1. Adja meg az egyik felhasználó hitelesítő adatait az Azure AD-ben, és válassza **a Bejelentkezés**lehetőséget.

   ![Bejelentkezés felhasználónévvel és jelszóval][api-management-aad-signin]

1. Ha további információra van szükség, a rendszer kérheti a regisztrációs űrlapot. Töltse ki a regisztrációs űrlapot, és válassza **a Regisztráció**lehetőséget.

   ![A regisztrációs űrlapon a "Regisztráció" gomb][api-management-complete-registration]

A felhasználó most már be van jelentkezve az API Management szolgáltatáspéldány fejlesztői portáljára.

![Fejlesztői portál a regisztráció befejezése után][api-management-registration-complete]

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

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
