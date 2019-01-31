---
title: Azure Active Directory használatával hitelesíti a Batch Management solutions |} A Microsoft Docs
description: Alkalmazások létrehozása az Azure resource Managerben, és a Batch erőforrás-szolgáltató az Azure AD-hitelesítést.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/27/2017
ms.author: lahugh
ms.openlocfilehash: 67aced11d885be949e7e7dc14ba7aa59e903716a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467849"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Hitelesítés az Active Directory Batch Management solutions

Az alkalmazásokat, amelyek az Azure Batch Management szolgáltatás hívása a hitelesítéshez [Azure Active Directory] [ aad_about] (Azure AD). Az Azure AD egy, a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatása. Azure magát az Azure AD, az ügyfelek, a szolgáltatás-rendszergazdák és a szervezeti felhasználók hitelesítésére.

A Batch Management .NET könyvtár típusok használata Batch-fiókok, fiókkulcsok, alkalmazások és az alkalmazáscsomagok számára tesz elérhetővé. A Batch Management .NET könyvtár egy Azure-erőforrás-szolgáltató ügyfél, és együtt használatos [Azure Resource Manager] [ resman_overview] programozott módon kezelheti ezeket az erőforrásokat. Az Azure AD bármely Azure-erőforrás szolgáltató ügyfél, például a Batch Management .NET könyvtár és révén-kérelmek hitelesítéséhez szükséges [Azure Resource Manager][resman_overview].

Ebben a cikkben tárgyaljuk, az Azure AD-vel történő hitelesítés a Batch Management .NET-es libraryt használó alkalmazások. Bemutatjuk, hogyan az Azure AD használatával hitelesíti a előfizetés rendszergazdai vagy társadminisztrátori, integrált hitelesítés használatával. Használjuk a [AccountManagement] [ acct_mgmt_sample] mintaprojektet, elérhető a Githubon, az Azure AD-vel a Batch Management .NET könyvtár az bemutatására.

A Batch Management .NET könyvtár és a AccountManagement minta használatával kapcsolatos további tudnivalókért lásd: [kezelése a Batch-fiókok és kvóták a Batch Management .NET-hez készült ügyféloldali kódtára a](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Az alkalmazás regisztrálása az Azure ad-vel

Az Azure [Active Directory Authentication Library] [ aad_adal] (ADAL) programozható felületet biztosít az Azure AD az alkalmazásaiban. Adal-t hívja az alkalmazásból, regisztrálnia kell az alkalmazását az Azure AD-bérlő. Ha regisztrálja az alkalmazást, adja meg az Azure AD-információk az alkalmazásról, beleértve a nevét, az Azure AD bérlőn belül. Ezután az Azure AD-alkalmazás azonosítója, amellyel az alkalmazás társítása az Azure ad-vel futásidőben biztosít. Az Alkalmazásazonosítót kapcsolatos további információkért lásd: [alkalmazás és egyszerű szolgáltatási objektumok Azure Active Directoryban](../active-directory/develop/app-objects-and-service-principals.md).

A AccountManagement mintaalkalmazás regisztrálásához kövesse a [egy alkalmazás hozzáadása](../active-directory/develop/quickstart-v1-add-azure-ad-app.md) szakasz [alkalmazások integrálása az Azure Active Directory] [ aad_integrate]. Adja meg **natív ügyfélalkalmazás** az alkalmazás számára. Az iparági szabványos OAuth 2.0-s URI-azonosítóját a **átirányítási URI-t** van `urn:ietf:wg:oauth:2.0:oob`. Azonban megadhat bármilyen érvényes URI-t (például `http://myaccountmanagementsample`) esetében a **átirányítási URI-t**, mert nem kell valódi végpontnak lennie:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

Miután elvégezte a regisztrációs folyamat, látni fogja az alkalmazás azonosítója és az alkalmazás felsorolt Objektumazonosító (egyszerű szolgáltatásnevének).  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Az Azure Resource Manager API-hoz hozzáférést az alkalmazáshoz

Ezután szüksége lesz az alkalmazás az Azure Resource Manager API-hoz való hozzáférés delegálására. A Resource Manager API-hoz az Azure ad-ben az azonosító **Windows Azure szolgáltatásfelügyeleti API**.

Kövesse az alábbi lépéseket az Azure Portalon:

1. Válassza ki az Azure Portal bal oldali navigációs ablaktáblán, **minden szolgáltatás**, kattintson a **Alkalmazásregisztrációk**, és kattintson a **hozzáadása**.
2. Keresse meg az alkalmazásregisztrációk a listában az alkalmazás neve:

    ![Keresse meg az alkalmazás neve](./media/batch-aad-auth-management/search-app-registration.png)

3. Megjelenítés a **beállítások** panelen. Az a **API-hozzáférés** szakaszban jelölje be **szükséges engedélyek**.
4. Kattintson a **Hozzáadás** hozzáadása egy új szükséges engedélyt. 
5. Adja meg az 1. lépésben, **Windows Azure szolgáltatásfelügyeleti API**, válassza ki az API az eredmények listájából, majd kattintson a **kiválasztása** gombra.
6. A 2. lépésben, jelölje be a **más szervezet felhasználói hozzáférés az Azure klasszikus üzemi modellben**, és kattintson a **válassza** gombra.
7. Kattintson a **kész** gombra.

A **szükséges engedélyek** panelen most már látható, hogy az alkalmazásnak engedélyek az adal-t és a Resource Manager API-k. Engedélyek az adal-t alapértelmezés szerint amikor az alkalmazás regisztrálása az Azure ad-ben.

![Az Azure Resource Manager API-engedélyek delegálása](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Azure AD-végpontok

A Batch-kezelési megoldások az Azure AD-hitelesítést, jól ismert végpontokat kell.

- A **gyakori Azure AD-végpont** összegyűjtéséhez felület egy adott bérlő nem áll rendelkezésre, ha integrált hitelesítés esetén általános hitelesítő adatokat biztosít:

    `https://login.microsoftonline.com/common`

- A **Azure Resource Manager-végpontot** szolgál a Batch management szolgáltatás irányuló kérelmek hitelesítéséhez szükséges jogkivonat beszerzése:

    `https://management.core.windows.net/`

A AccountManagement mintaalkalmazás állandókat ezeket a végpontokat határoz meg. Ezt a állandót változatlanul hagyja:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Hivatkozás az alkalmazás azonosítója 

Az ügyfélalkalmazás az Alkalmazásazonosítót (más néven az ügyfél-azonosító) használ futtatáskor az Azure AD eléréséhez. Miután regisztrálta az alkalmazást az Azure Portalon, frissítse a kódot, és használja az Alkalmazásazonosítót, regisztrált egy alkalmazást az Azure AD által támogatott. A AccountManagement mintaalkalmazásban másolja az alkalmazás azonosítója az Azure Portalon a megfelelő konstans:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
Az átirányítási URI-t, amely a regisztrációs folyamat során megadott példányát is. Az átirányítási URI-t a kódban megadott meg kell egyeznie az átirányítási URI-t az alkalmazás regisztrációja során megadott.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Az Azure AD hitelesítési jogkivonat beszerzése

Miután a AccountManagement minta regisztrálása az Azure AD-bérlő és a forrás mintakód frissítse az értékeket, a minta készen áll a hitelesítés az Azure AD használatával. A minta futtatásakor megkísérli az ADAL-hitelesítési jogkivonat beszerzése. Ebben a lépésben, a Microsoft hitelesítő adatokat kér: 

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

Miután megadta a hitelesítő adatait, a mintaalkalmazás a Batch management szolgáltatás hitelesített küldött kérések kiadására is folytatható. 

## <a name="next-steps"></a>További lépések

További információ a futó a [AccountManagement mintaalkalmazás][acct_mgmt_sample], lásd: [kezelése a Batch-fiókok és kvóták a Batch Management .NET-hez készült ügyféloldali kódtára a](batch-management-dotnet.md) .

Az Azure AD kapcsolatos további információkért tekintse meg a [Azure Active Directory dokumentációs](https://docs.microsoft.com/azure/active-directory/). Részletes példákat adal-t használó érhetők el a [Azure-Kódminták](https://azure.microsoft.com/resources/samples/?service=active-directory) könyvtár.

Az Azure AD-vel Batch-szolgáltatás alkalmazások hitelesítéséhez, lásd: [az Active Directory hitelesítést Batch-szolgáltatási megoldások](batch-aad-auth.md). 


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Mi az Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Hitelesítési forgatókönyvek az Azure ad-ben"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Alkalmazások integrálása az Azure Active Directoryval"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: http://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md
