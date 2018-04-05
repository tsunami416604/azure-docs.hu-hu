---
title: Azure Active Directory segítségével hitelesítheti a kötegelt megoldások |} Microsoft Docs
description: Alkalmazások az Azure resource manager-val készült, és az Azure AD hitelesíti a kötegelt erőforrás-szolgáltató.
services: batch
documentationcenter: .net
author: dlepow
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
ms.author: danlep
ms.openlocfilehash: a16d60dd48efeced7735562884acd26640d36483
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Hitelesítés kötegelt megoldásokat az Active Directoryban

A hitelesítést az Azure Batch Management szolgáltatás alkalmazások [Azure Active Directory] [ aad_about] (az Azure AD). Az Azure AD a Microsoft több-bérlős felhőalapú címtár és az identity management szolgáltatás. Azure magát, a a felhasználók, a szolgáltatás-rendszergazdák és a szervezeti felhasználók az Azure AD használ.

A Batch Management .NET kódtár típusok kötegelt fiókok, kulcsait, alkalmazások és csomagok való munkához tesz elérhetővé. A Batch Management .NET könyvtár az Azure-erőforrás-szolgáltató ügyfél, és együtt használni [Azure Resource Manager] [ resman_overview] programozott módon kezelheti ezeket az erőforrásokat. Az Azure AD bármely Azure-erőforrás szolgáltató ügyfél, beleértve a Batch Management .NET kódtár és révén kérelmek hitelesítéséhez szükséges [Azure Resource Manager][resman_overview].

Ebben a cikkben azt megismerkedhet az Azure AD a Batch Management .NET kódtárral libraryt használó alkalmazások hitelesítéséhez. Megmutatjuk, hogyan egy előfizetés rendszergazdának vagy társadminisztrátornak, integrált hitelesítés hitelesítéséhez az Azure AD használja. Használjuk a [AccountManagment] [ acct_mgmt_sample] mintaprojektet, elérhető a Githubon, az Azure AD a Batch Management .NET kódtárral könyvtárhoz bízná.

A Batch Management .NET kódtárral és a AccountManagement minta használatával kapcsolatos további tudnivalókért lásd: [kezelése Batch fiókjainak és kvótáinak, a Batch Management ügyféloldali kódtára a .NET](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Az alkalmazás regisztrálása az Azure ad szolgáltatással

Az Azure [Active Directory Authentication Library] [ aad_adal] (ADAL) programozott felülete lehetőséget nyújt az Azure AD az alkalmazások belüli használatra. Az alkalmazás adal-t hívja, regisztrálnia kell az alkalmazást az Azure AD-bérlő. Ha a regisztrálnia az alkalmazást, adja meg az adatokat az Azure AD az alkalmazásról, többek között a nevét, az Azure AD-bérlő belül. Az Azure AD majd biztosít, amelyekkel az alkalmazás társítása az Azure AD futásidőben Azonosítóját. Az Alkalmazásazonosító kapcsolatos további információkért lásd: [alkalmazás és szolgáltatás egyszerű objektumok az Azure Active Directoryban](../active-directory/develop/active-directory-application-objects.md).

A AccountManagement mintaalkalmazás regisztrálásához kövesse a [egy alkalmazás hozzáadása](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) szakasz [alkalmazások integrálása az Azure Active Directory] [ aad_integrate]. Adja meg **natív ügyfélalkalmazás** az alkalmazás. Az iparági szabványos OAuth 2.0 URI-Azonosítóját a **átirányítási URI-** van `urn:ietf:wg:oauth:2.0:oob`. Azonban megadhat bármilyen érvényes URI-azonosító (például `http://myaccountmanagementsample`) az a **átirányítási URI-**, mert nem kell a tényleges végpontnak lennie:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

A regisztrációs folyamat befejezése után megjelenik az alkalmazás Azonosítóját és az alkalmazás felsorolt Objektumazonosító (egyszerű szolgáltatásnév).  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Az Azure Resource Manager API hozzáférést biztosíthat az alkalmazás

Ezt követően az alkalmazás az Azure Resource Manager API-hoz való hozzáférés delegálására lesz szüksége. A Resource Manager API-hoz az Azure AD-azonosító **Windows Azure szolgáltatásfelügyeleti API**.

Kövesse az alábbi lépéseket az Azure-portálon:

1. Az Azure portál bal oldali navigációs ablaktábláján válassza **minden szolgáltatás**, kattintson a **App regisztrációk**, és kattintson a **Hozzáadás**.
2. Keresse meg az alkalmazás regisztrációk a listában az alkalmazás nevét:

    ![Keresse meg az alkalmazás neve](./media/batch-aad-auth-management/search-app-registration.png)

3. Megjelenítés a **beállítások** panelen. Az a **API-hozzáférés** szakaszban jelölje be **szükséges engedélyek**.
4. Kattintson a **Hozzáadás** hozzáadása egy új szükséges engedéllyel. 
5. Adja meg az 1. lépésben **Windows Azure szolgáltatásfelügyeleti API**, jelölje ki, hogy az API az eredménylistában, majd kattintson a **válasszon** gombra.
6. A 2. lépésben, jelölje be a jelölőnégyzetet a **Access Azure klasszikus üzembe helyezési modellel, mint a szervezeti felhasználók**, és kattintson a **válasszon** gombra.
7. Kattintson a **végzett** gombra.

A **szükséges engedélyek** panelen látható, hogy az alkalmazás engedélyekkel az adal-t és a Resource Manager API-k. Engedélyekkel az adal-ra alapértelmezés szerint amikor az alkalmazás regisztrálására az Azure ad-val.

![Az Azure Resource Manager API engedélyének delegálása](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Azure AD-végpontok

A kötegelt megoldásokat az Azure AD hitelesíti, két jól ismert végpontok lesz szüksége.

- A **az Azure AD közös végpont** összegyűjtéséhez felület egy adott bérlő nem ad meg, amikor integrált hitelesítés esetében általános hitelesítő adatokat biztosít:

    `https://login.microsoftonline.com/common`

- A **Azure Resource Manager-végpont** segítségével szerezzen be egy tokent a Batch szolgáltatás kérelmek hitelesítéséhez:

    `https://management.core.windows.net/`

A AccountManagement mintaalkalmazás állandókat ezeket a végpontokat határoz meg. Ezek állandók változatlanul hagyja:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Az Alkalmazásazonosító hivatkozik 

Az ügyfélalkalmazást (más néven az ügyfél-azonosító) Alkalmazásazonosítót használ a futási időben az Azure AD eléréséhez. Az alkalmazás már regisztrált az Azure portálon, frissíti a kódot, és az Azure ad-val a regisztrált alkalmazás által biztosított Alkalmazásazonosítót használja. A AccountManagement mintaalkalmazást másolja az alkalmazás azonosítója az Azure-portálon a megfelelő állandó:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
Az átirányítási URI-t a regisztráció során megadott szeretne másolni. Az átirányítási URI a kódban megadott meg kell egyeznie az átirányítási URI-t az alkalmazás regisztrálásakor kapott.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Az Azure AD hitelesítési jogkivonat

Miután a AccountManagement minta regisztrálni kell az Azure AD bérlője, és frissítse az adatforrás példakód az értékek, a minta készen áll a hitelesítés az Azure AD használatával. A minta futtatásakor az ADAL megkísérli egy hitelesítési jogkivonatot. Ebben a lépésben megkérdezi a Microsoft hitelesítő adatait: 

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

Miután megadta a hitelesítő adatait, a mintaalkalmazást a hitelesített kérelmeket kiadni a Batch szolgáltatás lépne. 

## <a name="next-steps"></a>További lépések

További információ a futó a [AccountManagement mintaalkalmazás][acct_mgmt_sample], lásd: [kezelése Batch fiókjainak és kvótáinak, a Batch Management ügyféloldali kódtára a .NET](batch-management-dotnet.md) .

Az Azure AD kapcsolatos további tudnivalókért tekintse meg a [Azure Active Directory dokumentációjának](https://docs.microsoft.com/azure/active-directory/). Részletes példa bemutatja, hogyan adal-t használó érhetők el a [Azure mintakódok](https://azure.microsoft.com/resources/samples/?service=active-directory) könyvtár.

A Batch szolgáltatás alkalmazások az Azure AD hitelesítéshez lásd: [hitelesítéséhez kötegelt szolgáltatási megoldások és az Active Directory](batch-aad-auth.md). 


[aad_about]: ../active-directory/active-directory-whatis.md "Mi az Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Az Azure Active Directory hitelesítési forgatókönyvei"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Alkalmazások integrálása az Azure Active Directoryval"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: http://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md
