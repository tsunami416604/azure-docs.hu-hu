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
ms.openlocfilehash: 422c2a8a61b1df36b452c153aa6cd78ba7e2dcef
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64723642"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Az Azure Active Directoryval az Azure API Management fejlesztői fiókok engedélyezése

Ez a cikk bemutatja, hogyan hozzáférés engedélyezése a felhasználók számára a fejlesztői portálra az Azure Active Directory (Azure AD). Ez az útmutató emellett bemutatja, hogyan kezelheti az Azure AD felhasználói csoportokat hozzáadja a felhasználókat tartalmazó külső csoportok.

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre a következő rövid útmutatót: [Az Azure API Management szolgáltatáspéldány létrehozása](get-started-create-service-instance.md).
- Importálása és közzététele az Azure API Management-példány. További információkért lásd: [importálása és közzététele](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Fejlesztői fiókok engedélyezése az Azure AD-vel

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Válassza ezt: ![nyíl](./media/api-management-howto-aad/arrow.png).
3. Típus **api** kifejezést a keresőmezőbe.
4. Válassza ki **API Management-szolgáltatások**.
5. Válassza ki az API Management-szolgáltatáspéldányát.
6. A **biztonsági**válassza **identitások**.
7. Válassza ki **+ Hozzáadás** a lista elejéről.

    A **Hozzáadás identitásszolgáltató** ablaktáblán a jobb oldalon jelenik meg.
8. A **szolgáltatótípus**válassza **Azure Active Directory**.

    Vezérlők, amelyek lehetővé teszik, hogy adja meg az egyéb szükséges adatokat a panelen jelennek meg. A vezérlők **ügyfél-azonosító** és **titkos Ügyfélkód**. (A lekérése a vezérlőelemek információt a cikk későbbi részében.)
9. Jegyezze fel a tartalmát **átirányítási URL-cím**.
    
   ![Az Azure Portalon egy identitásszolgáltató hozzáadásának lépései](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. A böngészőben nyisson meg egy másik lapon. 
11. Keresse meg a [az Azure portal - alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) alkalmazás regisztrálása az Active Directoryban.
12. A **kezelés**válassza **alkalmazásregisztrációk**.
13. Válassza ki **új regisztrációs**. Az a **alkalmazás regisztrálása** lapon, a következőképpen adja a következő értékeket:
    
* Állítsa be **neve** egy kifejező nevet. például *fejlesztői – portál*
* Állítsa be **támogatott fióktípusok** való **fiókok csak a szervezeti könyvtárban található**. 
* Állítsa be **átirányítási URI-t** a 9. lépésében kapott érték. 
* Válasszon **regisztrálása**. 

14.  Miután az alkalmazás regisztrálva van, másolja a **Alkalmazásazonosítót (ügyfél)** a a **áttekintése** lap. 
15. Lépjen vissza az API Management-példány. Az a **Hozzáadás identitásszolgáltató** ablakban illessze be a **Alkalmazásazonosítót (ügyfél)** be értéket a **ügyfél-azonosító** mezőbe.
16. Váltson vissza az Azure AD-konfigurációjának, válassza ki a **tanúsítványok és titkos kulcsok** alatt **kezelés**. Válassza ki a **új titkos ügyfélkulcsot** gombra. Adjon meg egy értéket a **leírás**, az egyik lehetőséget sem **lejárat** válassza **Hozzáadás**. Másolja ki az ügyfél titkos érték, mielőtt elhagyja a lapot. A következő lépésben szüksége lesz ezekre. 
17. Lépjen vissza az API Management-példány, a titkos kulcsát a illessze be a **titkos Ügyfélkód** mezőbe.

    > [!IMPORTANT]
    > Ellenőrizze, hogy frissítette a **titkos Ügyfélkód** a kulcs érvényességének lejárta előtt. 
    >  
    >

18. A **Hozzáadás identitásszolgáltató** ablakban is tartalmaz a **engedélyezett bérlők** szövegmezőben. Itt adja meg a tartományban, amelyhez hozzá szeretné hozzáférést biztosítani az API-k az API Management szolgáltatáspéldányt, az Azure AD-példányban. Több tartomány elválasztás sortörésekből álló szóközöket tartalmazza, szóközök és vesszővel válassza el egymástól.

> [!NOTE]
> A több tartományt is megadhat a **engedélyezett bérlők** szakaszban. Minden felhasználó egy másik tartományban található, mint az eredeti tartomány, ahol az alkalmazás regisztrálva lett a bejelentkezéshez, a másik tartományban globális rendszergazdájának engedélyt kell az alkalmazás hozzáférési címtáradatok. Engedélyt adni, a globális rendszergazdának kell: egy. Lépjen a `https://<URL of your developer portal>/aadadminconsent` (például https://contoso.portal.azure-api.net/aadadminconsent).
> b. Írja be az Azure AD-bérlővel, amelyeket be szeretne hozzáférést biztosít a tartomány nevét.
> c. Válassza ki **elküldése**. 

19.  Miután megadta a szükséges konfiguráció, válassza ki a **Hozzáadás**.

Miután menti a módosításokat, felhasználók a megadott Azure ad-példány bejelentkezhet a fejlesztői portál által leírt lépések végrehajtásával [egy olyan Azure AD felhasználói fiókkal jelentkezzen be a fejlesztői portál](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Adjon hozzá egy külső Azure AD-csoport

Miután engedélyezte a hozzáférést a felhasználók számára az Azure AD-példányban, az API Management az Azure AD-csoportokat is hozzáadhat. Ezt követően könnyebben kezelheti a társítást a csoportban a fejlesztők a kívánt termékeket.

 > [!IMPORTANT]
 > Egy külső hozzáadása az Azure AD-csoporthoz, először konfigurálnia kell az Azure AD-példányt a **identitások** lapon az alábbi eljárást az előző szakaszban. Ezenkívül az alkalmazás rendelkeznie kell a hozzáférést az Azure AD Graph API `Directory.Read.All` engedéllyel. 

Hozzáadhat külső Azure AD csoportkezelési származó a **csoportok** lap az API Management-példány.

1. Válassza ki a **Csoportok** lapot.
2. Válassza ki a **AAD hozzáadása csoporthoz** gombra.
   !["Az AAD-csoport hozzáadása" gomb](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Válassza ki a hozzáadni kívánt csoportot.
4. Nyomja le az **kiválasztása** gombra.

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

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
