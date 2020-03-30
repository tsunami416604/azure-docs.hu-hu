---
title: Az Azure Batch-szolgáltatások hitelesítése az Azure Active Directoryval
description: Batch támogatja az Azure AD hitelesítéshez a Batch szolgáltatás. Ismerje meg, hogyan lehet hitelesíteni két módon.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 01/28/2020
ms.author: labrenne
ms.openlocfilehash: f56c05f64086ac2e98e69d6b21fae7a0a63b5006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019519"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Batch szolgáltatásmegoldások hitelesítése az Active Directoryval

Az Azure Batch támogatja a hitelesítést [az Azure Active Directory][aad_about] (Azure AD) használatával. Az Azure AD a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatása. Maga az Azure az Azure AD-t használja ügyfelei, a szolgáltatás-rendszergazdák és a szervezeti felhasználók hitelesítésére.

Ha az Azure AD-hitelesítést az Azure Batch használatával használja, kétféleképpen hitelesítheti magát:

- Integrált **hitelesítés** használatával hitelesíti az alkalmazással interakcióba lépő felhasználót. Az integrált hitelesítést használó alkalmazások összegyűjtik a felhasználó hitelesítő adatait, és ezeket a hitelesítő adatokat használják a Batch-erőforrásokhoz való hozzáférés hitelesítéséhez.
- Egy **szolgáltatásnév** használatával hitelesítheti a felügyelet nélküli alkalmazásokat. A szolgáltatásnév határozza meg a szabályzatot és az engedélyeket egy alkalmazás annak érdekében, hogy képviselje az alkalmazást, amikor erőforrások elérése futásidőben.

Az Azure AD-ről az [Azure Active Directory dokumentációjában](https://docs.microsoft.com/azure/active-directory/)olvashat bővebben.

## <a name="endpoints-for-authentication"></a>Végpontok a hitelesítéshez

A Batch-alkalmazások azure AD-vel történő hitelesítéséhez néhány jól ismert végpontot kell megadnia a kódban.

### <a name="azure-ad-endpoint"></a>Azure AD-végpont

Az Azure AD-jogosultságok alapvégpontja:

`https://login.microsoftonline.com/`

Az Azure AD-vel való hitelesítéshez ezt a végpontot használja a bérlőazonosítóval (címtárazonosítóval). A bérlői azonosító azonosítja a hitelesítéshez használandó Azure AD-bérlőt. A bérlői azonosító lekéréséhez kövesse az [Azure Active Directory bérlői azonosítójának beszerzése](#get-the-tenant-id-for-your-active-directory)című részben ismertetett lépéseket:

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> A bérlő-specifikus végpont szükséges, ha egy egyszerű szolgáltatás használatával hitelesíti. 
> 
> A bérlő-specifikus végpont nem kötelező, ha integrált hitelesítéssel hitelesíti, de ajánlott. Azonban az Azure AD közös végpont is használhatja. A közös végpont egy általános hitelesítő adatok összegyűjtésére, ha egy adott bérlő nincs megadva. A közös végpont `https://login.microsoftonline.com/common`a .
>
>

Az Azure AD-végpontokról az [Azure AD hitelesítési forgatókönyvei][aad_auth_scenarios]című témakörben talál további információt.

### <a name="batch-resource-endpoint"></a>Kötegelt erőforrás-végpont

Az **Azure Batch erőforrás-végpont** használatával jogkivonatot szerezhet a Batch szolgáltatásnak érkező kérelmek hitelesítéséhez:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Az alkalmazás regisztrálása egy bérlővel

Az első lépés az Azure AD hitelesítése az alkalmazás regisztrálása egy Azure AD-bérlőben. Az alkalmazás regisztrálása lehetővé teszi, hogy hívja meg az Azure [Active Directory hitelesítési könyvtár][aad_adal] (ADAL) a kódból. Az ADAL api-t biztosít az Azure AD-vel való hitelesítéshez az alkalmazásból. Az alkalmazás regisztrálása szükséges, függetlenül attól, hogy integrált hitelesítést vagy egyszerű szolgáltatás használatát tervezi.Registering your application is required whether you plan to use integrated authentication or a service principal.

Amikor regisztrálja az alkalmazást, adja meg az alkalmazás adatait az Azure AD-nek. Az Azure AD ezután egy alkalmazásazonosítót (más néven *ügyfélazonosítót)* biztosít, amelyet az alkalmazás azure AD-vel való társítására használ futásidőben. Az alkalmazásazonosítóról az [Alkalmazás- és egyszerű szolgáltatásobjektumok az Azure Active Directoryban](../active-directory/develop/app-objects-and-service-principals.md)című témakörben olvashat bővebben.

A Batch-alkalmazás regisztrálásához kövesse az [Alkalmazások integrálása][aad_integrate]az Azure Active Directoryval című [alkalmazás hozzáadása](../active-directory/develop/quickstart-register-app.md) szakasz lépéseit. Ha natív alkalmazásként regisztrálja az alkalmazást, bármilyen érvényes URI-t megadhat az **átirányítási**URI-hoz. Nem kell, hogy valódi végpont legyen.

Miután regisztrálta az jelentkezését, megjelenik az alkalmazásazonosítója:

![Batch-alkalmazás regisztrálása az Azure AD-vel](./media/batch-aad-auth/app-registration-data-plane.png)

Az alkalmazások Azure AD-vel való regisztrálásáról az [Azure AD hitelesítési forgatókönyvei](../active-directory/develop/authentication-scenarios.md)című témakörben talál további információt.

## <a name="get-the-tenant-id-for-your-active-directory"></a>Az Active Directory bérlői azonosítójának beszereznie

A bérlői azonosító azonosítja az Azure AD-bérlőt, amely hitelesítési szolgáltatásokat nyújt az alkalmazásnak. A bérlőazonosító lekért érdekében kövesse az alábbi lépéseket:

1. Az Azure Portalon válassza ki az Active Directoryt.
1. Válassza **a Tulajdonságok lehetőséget.**
1. Másolja a **címtárazonosítóhoz**megadott GUID értéket. Ezt az értéket bérlői azonosítónak is nevezik.

![A címtárazonosító másolása](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>Integrált hitelesítés használata

Az integrált hitelesítéssel történő hitelesítéshez meg kell adnia az alkalmazás engedélyeket a Batch szolgáltatás API-hoz való csatlakozáshoz. Ez a lépés lehetővé teszi, hogy az alkalmazás hitelesítse a Batch service API-ra irányuló hívásokat az Azure AD-vel.

Miután regisztrálta az alkalmazást, kövesse az alábbi lépéseket az Azure Portalon, hogy hozzáférést biztosítson számára a Batch szolgáltatáshoz:

1. Az Azure Portal bal oldali navigációs ablaktábláján válassza a **Minden szolgáltatás**lehetőséget. Válassza **az Alkalmazásregisztrációk lehetőséget.**
1. Keresse meg az alkalmazás nevét az alkalmazásregisztrációk listájában:

    ![Az alkalmazás nevének keresése](./media/batch-aad-auth/search-app-registration.png)

1. Jelölje ki az alkalmazást, és válassza az **API-engedélyeket.**
1. Az **API-engedélyek** csoportban válassza az **Engedély hozzáadása**lehetőséget.
1. Az **API kiválasztása**csoportban keresse meg a Batch API-t. Keressen rá az egyes sztringekre, addig amíg meg nem találja az API-t:
    1. **Microsoft Azure Batch**
    1. A **ddbf3205-c6bd-46ae-8127-60eb93363864** a Batch API azonosítója.
1. Miután megtalálta a Batch API-t, jelölje ki, és válassza **a Kijelölés lehetőséget.**
1. A **Select permissions (Engedélyek kiválasztása)** párbeszédpanelen jelölje be az **Access Azure Batch Service** melletti jelölőnégyzetet, majd az **Engedélyek hozzáadása**lehetőséget.

Az **API-engedélyek** szakasz most azt mutatja, hogy az Azure AD-alkalmazás hozzáfér a Microsoft Graph és a Batch szolgáltatás API-t. Az engedélyek automatikusan megkapnak a Microsoft Graph számára, amikor először regisztrálja az alkalmazást az Azure AD-vel.

![API-engedélyek megadása](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Egyszerű szolgáltatás használata

Felügyelet nélkül futó alkalmazás hitelesítéséhez használjon egyszerű szolgáltatást. Miután regisztrálta az alkalmazást, kövesse az alábbi lépéseket az Azure Portalon egy egyszerű szolgáltatás konfigurálásához:

1. Kérjen titkos kérelmet a jelentkezéséhez.
1. Szerepköralapú hozzáférés-vezérlés (RBAC) hozzárendelése az alkalmazáshoz.

### <a name="request-a-secret-for-your-application"></a>Titkos kérelem kérése az alkalmazáshoz

Amikor az alkalmazás hitelesíti magát egy egyszerű szolgáltatás, elküldi az alkalmazás azonosítóját és egy titkos kulcsot az Azure AD-nek. Létre kell hoznia és másolnia kell a titkos kulcsot a kódból való használathoz.

Hajtsa végre a következő lépéseket az Azure Portalon:

1. Az Azure Portal bal oldali navigációs ablaktábláján válassza a **Minden szolgáltatás**lehetőséget. Válassza **az Alkalmazásregisztrációk lehetőséget.**
1. Válassza ki az alkalmazást az alkalmazásregisztrációk listájából.
1. Jelölje ki az alkalmazást, majd válassza **a Tanúsítványok & titkos kulcsok**lehetőséget. Az **Ügyféltitok csoportban** válassza az **Új ügyféltitok**lehetőséget.
1. Titkos titok létrehozásához adja meg a titkos kulcsot. Ezután válasszon ki egy lejárati lejárati egy év, két év vagy nem lejárati..
1. A **Titk** létrehozásához és megjelenítéséhez válassza a Hozzáadás lehetőséget. Másolja a titkos értéket egy biztonságos helyre, mivel az oldal elhagyása után nem fogja tudni újra elérni.

    ![Titkos kulcs létrehozása](./media/batch-aad-auth/secret-key.png)

### <a name="assign-rbac-to-your-application"></a>RBAC hozzárendelése az alkalmazáshoz

A szolgáltatásnév hitelesítéséhez rBAC-ot kell hozzárendelnie az alkalmazáshoz. Kövesse az alábbi lépéseket:

1. Az Azure Portalon keresse meg az alkalmazás által használt Batch-fiókot.
1. A Batch fiók **Beállítások** szakaszában válassza a **Hozzáférés-vezérlés (IAM) lehetőséget.**
1. Válassza a **Szerepkör-hozzárendelések** lapot.
1. Válassza a **Szerepkör-hozzárendelés hozzáadása** lehetőséget.
1. A **Szerepkör** legördülő menüben válassza az alkalmazás *közreműködői* vagy *olvasói* szerepkörét. Ezekről a szerepkörökről további információt az [Azure Portalon a szerepköralapú hozzáférés-vezérlés első lépései című témakörben talál.](../role-based-access-control/overview.md)  
1. A **Kijelölés** mezőbe írja be az alkalmazás nevét. Válassza ki az alkalmazást a listából, majd válassza a **Mentés gombot.**

Az alkalmazás nak most meg kell jelennie a hozzáférés-vezérlési beállítások egy RBAC szerepkörhöz rendelt.

![RBAC szerepkör hozzárendelése az alkalmazáshoz](./media/batch-aad-auth/app-rbac-role.png)

### <a name="assign-a-custom-role"></a>Egyéni szerepkör hozzárendelése

Az egyéni szerepkör részletes engedélyt ad a felhasználónak feladatok, feladatok és egyebek elküldésére. Ez lehetővé teszi, hogy a felhasználók ne hajtsanak végre olyan műveleteket, amelyek befolyásolják a költségeket, például készleteket vagy csomópontokat módosítanak.

Egyéni szerepkör használatával engedélyeket adhat egy Azure AD-felhasználónak, csoportnak vagy egyszerű szolgáltatásnak a következő RBAC-műveletekhez:

- Microsoft.Batch/batchAccounts/pools/write
- Microsoft.Batch/batchAccounts/pools/delete
- Microsoft.Batch/batchAccounts/pools/read
- Microsoft.Batch/batchAccounts/jobSchedules/write
- Microsoft.Batch/batchAccounts/jobSchedules/delete
- Microsoft.Batch/batchAccounts/jobSchedules/read
- Microsoft.Batch/batchAccounts/jobs/write
- Microsoft.Batch/batchAccounts/jobs/delete
- Microsoft.Batch/batchAccounts/jobs/read
- Microsoft.Batch/batchAccounts/certificates/write
- Microsoft.Batch/batchAccounts/certificates/delete
- Microsoft.Batch/batchAccounts/certificates/read
- Microsoft.Batch/batchAccounts/read (bármely olvasási művelethez)
- Microsoft.Batch/batchAccounts/listKeys/action (bármilyen művelethez)

Az egyéni szerepkörök az Azure AD által hitelesített felhasználók, nem a Batch-fiók hitelesítő adatai (megosztott kulcs) számára. Vegye figyelembe, hogy a Batch-fiók hitelesítő adatai teljes körű engedélyt adnak a Batch-fióknak. Azt is vegye figyelembe, hogy az automatikus készletkészlet-alapú feladatok készletszintű engedélyeket igényelnek.

Íme egy példa egy egyéni szerepkör-definícióra:

```json
{
 "properties":{
    "roleName":"Azure Batch Custom Job Submitter",
    "type":"CustomRole",
    "description":"Allows a user to submit jobs to Azure Batch but not manage pools",
    "assignableScopes":[
      "/subscriptions/88888888-8888-8888-8888-888888888888"
    ],
    "permissions":[
      {
        "actions":[
          "Microsoft.Batch/*/read",
          "Microsoft.Authorization/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*",
          "Microsoft.Insights/alertRules/*"
        ],
        "notActions":[

        ],
        "dataActions":[
          "Microsoft.Batch/batchAccounts/jobs/*",
          "Microsoft.Batch/batchAccounts/jobSchedules/*"
        ],
        "notDataActions":[

        ]
      }
    ]
  }
}
```

Az egyéni szerepkör létrehozásáról további általános tudnivalókat az [Egyéni szerepkörök az Azure-erőforrásokhoz](../role-based-access-control/custom-roles.md)című témakörben talál.

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Az Azure Active Directory bérlői azonosítójának beszereznie

A bérlői azonosító azonosítja az Azure AD-bérlőt, amely hitelesítési szolgáltatásokat nyújt az alkalmazásnak. A bérlőazonosító lekért érdekében kövesse az alábbi lépéseket:

1. Az Azure Portalon válassza ki az Active Directoryt.
1. Válassza **a Tulajdonságok lehetőséget.**
1. Másolja a **címtárazonosítóhoz**megadott GUID értéket. Ezt az értéket bérlői azonosítónak is nevezik.

![A címtárazonosító másolása](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>Kódpéldák

Ebben a szakaszban a kódpéldák bemutatják, hogyan hitelesíthető az Azure AD integrált hitelesítés sel és egy egyszerű szolgáltatás használatával. A legtöbb kódpélda a .NET-et használja, de a fogalmak más nyelvekesetében is hasonlóak.

> [!NOTE]
> Egy Azure AD-hitelesítési jogkivonat egy óra elteltével lejár. Hosszú élettartamú **BatchClient** objektum használata esetén azt javasoljuk, hogy minden kérésnél kérjen le egy jogkivonatot az ADAL-ból, hogy mindig érvényes jogkivonatot biztosítson. 
>
>
> Ennek elérése érdekében a .NET, írjon egy metódust, amely lekéri a jogkivonatot az Azure AD-ből, és adja át a metódust egy **BatchTokenCredentials** objektum meghatalmazottként. A delegálási metódus tág anamandós kérésesetén a Batch szolgáltatás hívása történik annak érdekében, hogy érvényes jogkivonat ot biztosítson. Alapértelmezés szerint az ADAL gyorsítótárazza a jogkivonatokat, így egy új jogkivonat csak szükség esetén kerül lekérésre az Azure AD-ből. Az Azure AD-ben lévő jogkivonatokról további információt az [Azure AD hitelesítési forgatókönyvei][aad_auth_scenarios]című témakörben talál.
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Példa kód: Az Azure AD integrált hitelesítésének használata a Batch .NET-tel

A Batch .NET integrált hitelesítésével történő hitelesítéshez hivatkozzon az [Azure Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) csomagra és az [ADAL-csomagra.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

A következő `using` állítások felvétele a kódba:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Hivatkozzon az Azure AD-végponta a kódban, beleértve a bérlőazonosítót. A bérlői azonosító lekéréséhez kövesse az [Azure Active Directory bérlői azonosítójának beszerzése](#get-the-tenant-id-for-your-active-directory)című részben ismertetett lépéseket:

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Hivatkozzon a Batch szolgáltatás erőforrás-végpontjára:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Hivatkozzon a Batch-fiókra:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Adja meg az alkalmazásazonosítóját (ügyfélazonosítóját). Az alkalmazásazonosító az azure-portálon érhető el az alkalmazás regisztrációjából:

```csharp
private const string ClientId = "<application-id>";
```

Másolja a megadott átirányítási URI-t is, ha natív alkalmazásként regisztrálta az alkalmazást. A kódban megadott átirányítási URI-nak meg kell egyeznie az alkalmazás regisztrálásakor megadott átirányítási URI-val:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Írjon egy visszahívási módszert a hitelesítési jogkivonat beszerzéséhez az Azure AD-ből. Az itt látható **GetAuthenticationTokenAsync** visszahívási módszer meghívja az ADAL-t az alkalmazással interakcióba lépő felhasználó hitelesítéséhez. Az ADAL által biztosított **AcquireTokenAsync** metódus kéri a felhasználótól a hitelesítő adataikat, és az alkalmazás akkor folytatja, amikor a felhasználó megadja azokat (kivéve, ha már gyorsítótárazott hitelesítő adatokkal rendelkezik):

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

**BatchTokenCredentials** objektum ot hozhat létre, amely a delegáltot paraméterként veszi fel. Ezekkel a hitelesítő adatokkal nyissa meg a **BatchClient** objektumot. Ezt a **BatchClient** objektumot használhatja a BatchClient szolgáltatás sal végzett további műveletekhez:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Példa kód: Egy Azure AD szolgáltatás egyszerű használata a Batch .NET-tel

A batch .NET szolgáltatásnévvel történő hitelesítéshez hivatkozzon az [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) csomagra és az [ADAL-csomagra.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

A következő `using` állítások felvétele a kódba:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Hivatkozzon az Azure AD-végponta a kódban, beleértve a bérlőazonosítót. Egyszerű szolgáltatás használata esetén meg kell adnia egy bérlő-specifikus végpontot. A bérlői azonosító lekéréséhez kövesse az [Azure Active Directory bérlői azonosítójának beszerzése](#get-the-tenant-id-for-your-active-directory)című részben ismertetett lépéseket:

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Hivatkozzon a Batch szolgáltatás erőforrás-végpontjára:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Hivatkozzon a Batch-fiókra:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Adja meg az alkalmazásazonosítóját (ügyfélazonosítóját). Az alkalmazásazonosító az azure-portálon érhető el az alkalmazás regisztrációjából:

```csharp
private const string ClientId = "<application-id>";
```

Adja meg az Azure Portalról másolt titkos kulcsot:

```csharp
private const string ClientKey = "<secret-key>";
```

Írjon egy visszahívási módszert a hitelesítési jogkivonat beszerzéséhez az Azure AD-ből. Az itt látható **GetAuthenticationTokenAsync** visszahívási metódus a felügyelet nélküli hitelesítéshez hívja meg az ADAL-t:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

**BatchTokenCredentials** objektum ot hozhat létre, amely a delegáltot paraméterként veszi fel. Ezekkel a hitelesítő adatokkal nyissa meg a **BatchClient** objektumot. Ezután használja ezt **a BatchClient** objektumot a BatchService szolgáltatás sal végzett további műveletekhez:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Példa kód: Egy Azure AD szolgáltatás névszerint a Batch Python

A Batch Python egyszerű szolgáltatáshitelesítésével hitelesíteni, telepítse és hivatkozzon az [azure-batch](https://pypi.org/project/azure-batch/) és [az azure-közös](https://pypi.org/project/azure-common/) modulok.

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Egyszerű szolgáltatás használata esetén meg kell adnia a bérlői azonosítót. A bérlői azonosító lekéréséhez kövesse az [Azure Active Directory bérlői azonosítójának beszerzése](#get-the-tenant-id-for-your-active-directory)című részben ismertetett lépéseket:

```python
TENANT_ID = "<tenant-id>"
```

Hivatkozzon a Batch szolgáltatás erőforrás-végpontjára:  

```python
RESOURCE = "https://batch.core.windows.net/"
```

Hivatkozzon a Batch-fiókra:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

Adja meg az alkalmazásazonosítóját (ügyfélazonosítóját). Az alkalmazásazonosító az azure-portálon érhető el az alkalmazás regisztrációjából:

```python
CLIENT_ID = "<application-id>"
```

Adja meg az Azure Portalról másolt titkos kulcsot:

```python
SECRET = "<secret-key>"
```

**ServicePrincipalCredentials** objektum létrehozása:

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

**BatchServiceClient** objektum megnyitásához használja a szolgáltatásnév hitelesítő adatait. Ezután használja ezt a BatchServiceClient objektumot a **BatchServiceService** szolgáltatás sal végzett további műveletekhez.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>További lépések

- Az Azure AD-ről az [Azure Active Directory dokumentációjában](https://docs.microsoft.com/azure/active-directory/)olvashat bővebben. Az ADAL használatát bemutató részletes példák az [Azure Code Samples](https://azure.microsoft.com/resources/samples/?service=active-directory) függvénytárban érhetők el.

- A szolgáltatásegyszerű szolgáltatásokról az [Alkalmazás- és egyszerű szolgáltatásobjektumok az Azure Active Directoryban.](../active-directory/develop/app-objects-and-service-principals.md) Ha az Azure Portal használatával szeretne egyszerű szolgáltatást létrehozni, olvassa el a [Portál használata az erőforrásokhoz hozzáférő Active Directory-alkalmazás és egyszerű szolgáltatás létrehozásácímű témakört.](../active-directory/develop/howto-create-service-principal-portal.md) A PowerShell vagy az Azure CLI szolgáltatásegyszerű szolgáltatást is létrehozhat.

- A Kötegkezelő alkalmazások Azure AD használatával történő hitelesítéséhez olvassa el a [Kötegkezelési megoldások hitelesítése az Active Directoryval](batch-aad-auth-management.md).

- Egy Python-példát, hogyan hozhat létre egy Azure AD-jogkivonattal hitelesített Batch-ügyfél, tekintse meg az [Azure Batch egyéni lemezkép telepítése python script mintával.](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md)

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Mi az Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Az Azure AD hitelesítési forgatókönyvei"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Alkalmazások integrálása az Azure Active Directoryval"
[azure_portal]: https://portal.azure.com
