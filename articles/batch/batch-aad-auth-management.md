---
title: Kötegkezelési megoldások hitelesítése az Azure Active Directory használatával
description: Fedezze fel az Azure Active Directory használatával a kötegelt kezelés .NET függvénytárat használó alkalmazások hitelesítését.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/27/2017
ms.author: labrenne
ms.openlocfilehash: 5c217971bd213c97a2ee31a0a1f513b601d14df9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472979"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Kötegkezelési megoldások hitelesítése az Active Directoryval

Az Azure Batch Management szolgáltatást hívó alkalmazások az [Azure Active Directoryval][aad_about] (Azure AD) hitelesítik magukat. Az Azure AD a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatása. Maga az Azure használja az Azure AD-t az ügyfelek, a szolgáltatás-rendszergazdák és a szervezeti felhasználók hitelesítéséhez.

A Kötegkezelés .NET függvénytár a Batch-fiókok, a fiókkulcsok, az alkalmazások és az alkalmazáscsomagok kezeléséhez használható típusokat teszi elérhetővé. A Batch Management .NET függvénytár egy Azure-erőforrás-szolgáltató ügyfél, és az [Azure Resource Manager][resman_overview] segítségével kezeli ezeket az erőforrásokat programozott módon. Az Azure AD-nek hitelesítenie kell az Azure erőforrás-szolgáltató ügyfélen keresztül, beleértve a Batch Management .NET függvénytárat és az [Azure Resource Manageren][resman_overview]keresztül érkező kérelmeket.

Ebben a cikkben azt vizsgálja, hogy az Azure AD használatával hitelesítheti a Batch Management .NET függvénytárat használó alkalmazásokból. Bemutatjuk, hogyan használhatja az Azure AD-t egy előfizetés-rendszergazda vagy társrendszergazda hitelesítésére, integrált hitelesítés használatával. A GitHubon elérhető [AccountManagement][acct_mgmt_sample] mintaprojekt segítségével végigvezetjük az Azure AD-t a Batch Management .NET-kódtárral.

A Kötegkezelés . [Manage Batch accounts and quotas with the Batch Management client library for .NET](batch-management-dotnet.md)

## <a name="register-your-application-with-azure-ad"></a>Az alkalmazás regisztrálása az Azure AD-vel

Az Azure [Active Directory hitelesítési könyvtár][aad_adal] (ADAL) egy programozott felületet biztosít az Azure AD-hez az alkalmazásokon belüli használatra. Az ADAL-t az alkalmazásból való hívásához regisztrálnia kell az alkalmazást egy Azure AD-bérlőben. Amikor regisztrálja az alkalmazást, adja meg az Azure AD-nek az alkalmazással kapcsolatos információkat, beleértve az Azure AD-bérlőn belüli nevét is. Az Azure AD ezután egy alkalmazásazonosítót biztosít, amely segítségével társítja az alkalmazást az Azure AD-hez futásidőben. Az alkalmazásazonosítóról az [Alkalmazás- és egyszerű szolgáltatásobjektumok az Azure Active Directoryban](../active-directory/develop/app-objects-and-service-principals.md)című témakörben olvashat bővebben.

Az AccountManagement mintaalkalmazás regisztrálásához kövesse az Alkalmazások [integrálása][aad_integrate]az Azure Active Directoryval című [alkalmazás hozzáadása](../active-directory/develop/quickstart-register-app.md) szakaszlépéseit. Adja meg a **natív ügyfélalkalmazást** az alkalmazás típusához. Az **átirányítási URI-hoz** az OAuth 2.0 URI iparági szabványa a. `urn:ietf:wg:oauth:2.0:oob` Az **átirányítási URI-hoz**azonban `http://myaccountmanagementsample`bármilyen érvényes URI-t megadhat (például) , mivel nem kell valódi végpontnak lennie:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

A regisztrációs folyamat befejezése után megjelenik az alkalmazásazonosító és az alkalmazáshoz megadott objektum (egyszerű szolgáltatásazonosító).  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Az Azure Resource Manager API-hozzáférés ének megadása az alkalmazáshoz

Ezután az alkalmazáshoz való hozzáférést delegálhatja az Azure Resource Manager API-hoz. Az Erőforrás-kezelő API Azure AD-azonosítója a **Windows Azure Service Management API.**

Hajtsa végre a következő lépéseket az Azure Portalon:

1. Az Azure Portal bal oldali navigációs ablakában válassza a **Minden szolgáltatás**lehetőséget, kattintson az **Alkalmazásregisztrációk**elemre, majd a **Hozzáadás**gombra.
2. Keresse meg az alkalmazás nevét az alkalmazásregisztrációk listájában:

    ![Az alkalmazás nevének keresése](./media/batch-aad-auth-management/search-app-registration.png)

3. Jelenítse meg a **Beállítások** panelt. Az **API Access (API Access)** szakaszban válassza a **Szükséges engedélyek**lehetőséget.
4. Új szükséges engedély hozzáadásához kattintson a **Hozzáadás** gombra. 
5. Az első lépésben írja be a **Windows Azure Service Management API-t,** válassza ki az API-t az eredmények listájából, majd kattintson a **Kijelölés** gombra.
6. A 2. **Access Azure classic deployment model as organization users** **Select**
7. Kattintson a **Kész** gombra.

A Szükséges engedélyek panel most már **megmutatja,** hogy az alkalmazás engedélyei az ADAL és az Erőforrás-kezelő API-k számára is meg vannak adva. Az ADAL alapértelmezés szerint engedélyeket kap, amikor először regisztrálja az alkalmazást az Azure AD-vel.

![Engedélyek delegálása az Azure Resource Manager API-hoz](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Azure AD-végpontok

A kötegelt kezelési megoldások azure AD-vel történő hitelesítéséhez két jól ismert végpontra van szükség.

- Az **Azure AD közös végpont** általános hitelesítő adatok összegyűjtésére szolgál, ha egy adott bérlő nincs megadva, mint az integrált hitelesítés esetén:

    `https://login.microsoftonline.com/common`

- Az **Azure Resource Manager-végpont** a kötegkezelési szolgáltatás kéréseinek hitelesítésére szolgáló jogkivonat beszerzésére szolgál:

    `https://management.core.windows.net/`

Az AccountManagement mintaalkalmazás ezekhez a végpontokhoz állandókat határoz meg. Hagyja ezeket az állandókat változatlanul:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Hivatkozás az alkalmazásazonosítóra 

Az ügyfélalkalmazás az alkalmazásazonosítót (más néven az ügyfélazonosítót) használja az Azure AD futásidőben történő eléréséhez. Miután regisztrálta az alkalmazást az Azure Portalon, frissítse a kódot, hogy az Azure AD által a regisztrált alkalmazáshoz megadott alkalmazásazonosítót használhassa. Az AccountManagement mintaalkalmazásban másolja az alkalmazásazonosítót az Azure Portalról a megfelelő állandóra:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Graph API. For information
// about registering an application in Azure Active Directory, please see "Register an application with the Microsoft identity platform" here:
// https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-register-app
private const string ClientId = "<application-id>";
```
Másolja a regisztrációs folyamat során megadott átirányítási URI-t is. A kódban megadott átirányítási URI-nak meg kell egyeznie az alkalmazás regisztrálásakor megadott átirányítási URI-val.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD-hitelesítési jogkivonat beszerzése

Miután regisztrálta a AccountManagement-mintát az Azure AD-bérlőben, és frissítette a minta forráskódját az értékekkel, a minta készen áll az Azure AD használatával történő hitelesítésre. A minta futtatásakor az ADAL hitelesítési jogkivonatot próbál beszerezni. Ebben a lépésben a microsoftos hitelesítő adatok megadását kéri: 

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

Miután megadt a hitelesítő adatokat, a mintaalkalmazás folytathatja a hitelesített kérelmek kiadását a Batch-kezelési szolgáltatás számára. 

## <a name="next-steps"></a>További lépések

Az [AccountManagement mintaalkalmazás][acct_mgmt_sample]futtatásáról további információt a [Batch-fiókok és kvóták kezelése a Kötegkezelés ügyféltárral a .NET számára című témakörben talál.](batch-management-dotnet.md)

Az Azure AD-ről az [Azure Active Directory dokumentációjában](https://docs.microsoft.com/azure/active-directory/)olvashat bővebben. Az ADAL használatát bemutató részletes példák az [Azure Code Samples](https://azure.microsoft.com/resources/samples/?service=active-directory) függvénytárban érhetők el.

A Batch-szolgáltatásalkalmazások Azure AD használatával történő hitelesítéséhez olvassa el a [Batch szolgáltatásmegoldások hitelesítése az Active Directoryval](batch-aad-auth.md). 


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Mi az Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Az Azure AD hitelesítési forgatókönyvei"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Alkalmazások integrálása az Azure Active Directoryval"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: https://portal.azure.com
[resman_overview]: ../azure-resource-manager/management/overview.md
