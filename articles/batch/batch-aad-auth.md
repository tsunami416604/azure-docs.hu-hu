---
title: Azure Active Directory használatával hitelesíti az Azure Batch szolgáltatás megoldások |} A Microsoft Docs
description: A Batch támogatja az Azure AD a Batch szolgáltatásban történő hitelesítéshez.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/18/2018
ms.author: danlep
ms.openlocfilehash: 964ef3bd988d71bfb90dbaa473b4b259769064b3
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722055"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>A Batch szolgáltatási megoldások és az Active Directory hitelesítést

Az Azure Batch támogatja a hitelesítés a [Azure Active Directory] [ aad_about] (Azure AD). Az Azure AD egy, a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatása. Azure magát az Azure AD, az ügyfelek, a szolgáltatás-rendszergazdák és a szervezeti felhasználók hitelesítéséhez.

Az Azure AD-hitelesítés az Azure Batch használatakor hitelesítheti a két módszer egyikével:

- Használatával **integrált hitelesítés** , amely az alkalmazás használata a felhasználó hitelesítéséhez. Integrált hitelesítést használó alkalmazások a felhasználói hitelesítő adatokat gyűjt, és ezeket a hitelesítő adatokat használ a Batch-erőforrásokhoz való hozzáférés hitelesítéséhez.
- Használatával egy **szolgáltatásnév** felügyelet nélküli alkalmazások hitelesítéséhez. Egyszerű szolgáltatás futásidőben erőforrások elérésekor az alkalmazás ábrázolhassa határozza meg a házirend és a egy alkalmazás engedélyeit.

Az Azure AD kapcsolatos további információkért tekintse meg a [Azure Active Directory dokumentációs](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Hitelesítési végpontjai

Batch-alkalmazások az Azure AD-hitelesítést, a kódban néhány jól ismert végpontokat is kell.

### <a name="azure-ad-endpoint"></a>Az Azure AD-végpont

Az alapszintű Azure AD-szolgáltató végpont:

`https://login.microsoftonline.com/`

Az Azure AD-hitelesítést, ezt a végpontot a bérlő Azonosítóját (címtár-azonosító) együtt használható. A bérlő Azonosítóját az Azure AD-bérlő, a hitelesítéshez használandó azonosítja. A Bérlőazonosító lekéréséhez kövesse az ismertetett lépéseket [a Bérlőazonosító beszerzése az Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> A bérlő-specifikus végpont megadása kötelező Önt a hitelesítéshez egy egyszerű szolgáltatás használatával. 
> 
> A bérlő-specifikus végpont Önt a hitelesítéshez integrált hitelesítés használata kötelező, de ajánlott. Azonban is használhatja az Azure AD közös végpontot. A közös végpontot felület összegyűjtése, ha nem áll rendelkezésre egy adott bérlő általános hitelesítő adatokat biztosít. A közös végpont `https://login.microsoftonline.com/common`.
>
>

Az Azure AD-végpontok kapcsolatos további információkért lásd: [hitelesítési forgatókönyvek az Azure ad-ben][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Batch-erőforrás végpontjának

Használja a **Azure Batch-erőforrás végpontjának** , a Batch szolgáltatás irányuló kérelmek hitelesítéséhez szükséges jogkivonat beszerzése:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Az alkalmazás regisztrálása a bérlőhöz

Az első lépés az Azure AD-vel történő hitelesítéséhez az Azure AD-bérlő regisztrálja az alkalmazást. Az alkalmazás regisztrálása lehetővé teszi, hogy, hogy az Azure meghívható [Active Directory Authentication Library] [ aad_adal] (ADAL) a kódból. Az ADAL API-alkalmazását az Azure ad-ben való hitelesítéshez használt biztosít. Az alkalmazás regisztrálása kötelező e tervezi használni az integrált hitelesítés és a egy egyszerű szolgáltatást.

Ha regisztrálja az alkalmazást, adja meg információkat az alkalmazásról, az Azure ad-hez. Az Azure AD majd biztosít egy Alkalmazásazonosítót (más néven egy *ügyfél-azonosító*), hogy használhatja-e az alkalmazás társítása az Azure ad-vel futásidőben. Az Alkalmazásazonosítót kapcsolatos további információkért lásd: [alkalmazás és egyszerű szolgáltatási objektumok Azure Active Directoryban](../active-directory/develop/app-objects-and-service-principals.md).

A Batch-alkalmazás regisztrálásához kövesse a [egy alkalmazás hozzáadása](../active-directory/develop/quickstart-v1-add-azure-ad-app.md) szakasz [alkalmazások integrálása az Azure Active Directory][aad_integrate]. Natív alkalmazás regisztrálhatja alkalmazását, ha bármely érvényes URI-azonosítóját is megadhat a **átirányítási URI-t**. Nem kell valódi végpontnak lennie.

Miután regisztrálta az alkalmazást, látni fogja az alkalmazás azonosítója:

![A Batch-alkalmazás regisztrálása az Azure ad-vel](./media/batch-aad-auth/app-registration-data-plane.png)

Egy alkalmazás regisztrálása az Azure ad-vel kapcsolatos további információkért lásd: [hitelesítési forgatókönyvek az Azure ad-ben](../active-directory/develop/authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Az Active Directory a Bérlőazonosító beszerzése

A Bérlőazonosító azonosítja az Azure AD-bérlővel, amely az alkalmazás hitelesítési szolgáltatásokat biztosít. A Bérlőazonosító lekéréséhez kövesse az alábbi lépéseket:

1. Az Azure Portalon válassza ki az Active Directoryban.
2. Kattintson a **Tulajdonságok** elemre.
3. Másolja a megadott GUID értéket a **címtár-azonosító**. Ennek az értéknek is nevezik a bérlő azonosítója.

![Másolja ki a címtár-azonosító](./media/batch-aad-auth/aad-directory-id.png)


## <a name="use-integrated-authentication"></a>Integrált hitelesítés használata

Beépített hitelesítéssel hitelesíteni, kell biztosítania az alkalmazás engedélyeit a Batch szolgáltatás API-hoz csatlakozhat. Ez a lépés lehetővé teszi, hogy az alkalmazás az Azure ad-vel a Batch szolgáltatás API-hívás hitelesítéséhez.

Miután [az alkalmazás regisztrálása](#register-your-application-with-an-azure-ad-tenant), kövesse az alábbi lépéseket a Batch szolgáltatáshoz való hozzáférést biztosítania az Azure Portalon:

1. Válassza ki az Azure Portal bal oldali navigációs ablaktáblán, **minden szolgáltatás**. Kattintson a **Alkalmazásregisztrációk**.
2. Keresse meg az alkalmazásregisztrációk a listában az alkalmazás neve:

    ![Keresse meg az alkalmazás neve](./media/batch-aad-auth/search-app-registration.png)

3. Kattintson az alkalmazást, majd **beállítások**. Az a **API-hozzáférés** szakaszban jelölje be **szükséges engedélyek**.
4. Az a **szükséges engedélyek** panelen kattintson a **Hozzáadás** gombra.
5. A **API kiválasztása**, keressen rá a Batch API. Keressen rá az egyes sztringekre, addig amíg meg nem találja az API-t:
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. Újabb Azure AD-bérlők ezt a nevet használhatják.
    3. A **ddbf3205-c6bd-46ae-8127-60eb93363864** a Batch API azonosítója. 
6. Miután megtalálta a Batch API-t, jelölje ki, és kattintson a **kiválasztása**.
7. A **engedélyek kiválasztása**, jelölje be a **hozzáférés az Azure Batch szolgáltatás** kattintson **kiválasztása**.
8. Kattintson a **Done** (Kész) gombra.

A **szükséges engedélyek** windows most mutat be, hogy az Azure AD-alkalmazás hozzáfér az adal-t mind a Batch szolgáltatás API-t. Engedélyek az adal-t automatikusan, ha az alkalmazás regisztrálása az Azure ad-ben.

![Engedélyezési API-engedélyek](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Egy egyszerű szolgáltatás használatával 

Futó felügyelet nélküli alkalmazások hitelesítéséhez, használhat egy egyszerű szolgáltatást. Miután regisztrálta az alkalmazást, kövesse az alábbi lépéseket egy egyszerű szolgáltatás konfigurálása az Azure Portalon:

1. Az alkalmazás titkos kulcsot kérni.
2. Az RBAC szerepkör hozzárendelése az alkalmazáshoz.

### <a name="request-a-secret-key-for-your-application"></a>Az alkalmazás titkos kulcs kérése

Amikor az alkalmazás egy egyszerű szolgáltatás végzi a hitelesítést, küld az Alkalmazásazonosítót és a egy titkos kulcsot az Azure AD. Kell létrehozni, és másolja a titkos kulcsot a kódból használni.

Kövesse az alábbi lépéseket az Azure Portalon:

1. Válassza ki az Azure Portal bal oldali navigációs ablaktáblán, **minden szolgáltatás**. Kattintson a **Alkalmazásregisztrációk**.
2. Keresse meg az alkalmazásregisztrációk a listában az alkalmazás nevére.
3. Kattintson az alkalmazást, majd **beállítások**. Az a **API-hozzáférés** szakaszban jelölje be **kulcsok**.
4. Hozzon létre egy kulcsot, adja meg egy leírást a kulcshoz. Ezután válassza ki egy időtartamot a kulcshoz egy vagy két év. 
5. Kattintson a **mentése** gombra kattintva hozzon létre, és a kulcs megjelenítéséhez. Másolja egy biztonságos helyre, a kulcs értékét, akkor nem lesz újból elérhető ez a panel elhagyása után. 

    ![Titkos kulcs létrehozása](./media/batch-aad-auth/secret-key.png)

### <a name="assign-an-rbac-role-to-your-application"></a>Az RBAC szerepkör hozzárendelése az alkalmazáshoz

Egyszerű szolgáltatás hitelesítése, kell egy RBAC szerepkör hozzárendelése az alkalmazáshoz. Kövesse az alábbi lépéseket:

1. Az Azure Portalon lépjen a Batch-fiókot, amelyet az alkalmazás.
2. Az a **beállítások** a Batch-fiókhoz, válassza a panel **hozzáférés-vezérlés (IAM)**.
3. Kattintson a **szerepköreinek hozzárendeléseit** fülre.
4. Kattintson a **szerepkör-hozzárendelés hozzáadása** gombra. 
5. Az a **szerepkör** legördülő menüben válassza a _közreműködői_ vagy _olvasó_ az alkalmazás-szerepkör. Ezen szerepkörökkel kapcsolatos további információkért lásd: [szerepköralapú hozzáférés-vezérlés az Azure Portalon – első lépések](../role-based-access-control/overview.md).  
6. Az a **kiválasztása** mezőben adja meg az alkalmazás nevére. Válassza ki az alkalmazást a listából, majd kattintson **mentése**.

Az alkalmazás ekkor meg kell jelennie a hozzáférés-vezérlési beállításokkal rendelkező egy hozzárendelt RBAC szerepkör. 

![Az RBAC szerepkör hozzárendelése az alkalmazáshoz](./media/batch-aad-auth/app-rbac-role.png)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>A Bérlőazonosító beszerzése az Azure Active Directory

A Bérlőazonosító azonosítja az Azure AD-bérlővel, amely az alkalmazás hitelesítési szolgáltatásokat biztosít. A Bérlőazonosító lekéréséhez kövesse az alábbi lépéseket:

1. Az Azure Portalon válassza ki az Active Directoryban.
2. Kattintson a **Tulajdonságok** elemre.
3. Másolja a megadott GUID értéket a **címtár-azonosító**. Ennek az értéknek is nevezik a bérlő azonosítója.

![Másolja ki a címtár-azonosító](./media/batch-aad-auth/aad-directory-id.png)


## <a name="code-examples"></a>Hitelesítésikód-példák

Ebben a szakaszban szereplő példák bemutatják, hogyan integrált hitelesítést használó Azure AD-vel és az egyszerű szolgáltatás hitelesítése. Ezek többsége a .NET használata, de a fogalmak hasonló más nyelven.

> [!NOTE]
> Azure AD-hitelesítési token egy óra múlva lejár. A hosszú élettartamú használatakor **BatchClient** objektumot, azt javasoljuk, hogy kérheti le jogkivonatot az ADAL halasztása minden kérelemnél, ellenőrizze, hogy mindig érvényes jogkivonatot. 
>
>
> A .NET-ben ennek érdekében olyan metódus írása, amely lekéri a jogkivonatot az Azure ad-ből, és adja át, a módszer egy **BatchTokenCredentials** meghatalmazottként objektum. A delegált metódus lehívásra kerül halasztása minden kérelemnél, győződjön meg arról, hogy egy érvényes tokent biztosít a Batch szolgáltatásnak. Alapértelmezés szerint a ADAL jogkivonatokat gyorsítótárazza, így új tokent az Azure ad-ből beolvasott csak szükség esetén. Az Azure ad-ben jogkivonatok kapcsolatos további információkért lásd: [hitelesítési forgatókönyvek az Azure ad-ben][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Példa: Azure AD-vel integrált hitelesítés Batch .NET-tel

Hivatkozás integrált hitelesítés a Batch .NET-hitelesítésre, az [Azure Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) csomag és a [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) csomagot.

A következők `using` utasítások a kódban:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Referencia az Azure AD-végpont a kódban, beleértve a bérlő azonosítója. A Bérlőazonosító lekéréséhez kövesse az ismertetett lépéseket [a Bérlőazonosító beszerzése az Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

A Batch szolgáltatás-erőforrás végpontjának hivatkozni:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

A Batch-fiók hivatkozni:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Adja meg az Alkalmazásazonosítót (ügyfél-azonosító) az alkalmazáshoz. Az Alkalmazásazonosító az alkalmazás regisztrációját az Azure Portalon érhető el:

```csharp
private const string ClientId = "<application-id>";
```

Az átirányítási URI-t a megadott, is másolja, ha az alkalmazás natív alkalmazás regisztrálása után. Az átirányítási URI-t a kódban megadott meg kell egyeznie az átirányítási URI-t az alkalmazás regisztrációja során megadott:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Az Azure ad-ből a hitelesítési jogkivonat beszerzése egy visszahívási metódus írása. A **GetAuthenticationTokenAsync** itt látható a hitelesítéshez a felhasználó, aki a tesztverzióval az alkalmazás adal-t hívások visszahívási metódus. A **AcquireTokenAsync** ADAL által biztosított metódus bekéri a felhasználótól a hitelesítő adataikat, és az alkalmazás telepítése folytatódik, miután a felhasználó megadja azokat a (kivéve, ha már gyorsítótárazott hitelesítő adatok):

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

Hozza létre a **BatchTokenCredentials** objektum, amely a küldött paraméterként. Nyissa meg ezeket a hitelesítő adatokat használja a **BatchClient** objektum. Használhatja azt **BatchClient** objektumot a Batch szolgáltatás további műveletek:

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

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Példa: egy Azure AD egyszerű szolgáltatás használatával a Batch .NET-tel

Egy egyszerű szolgáltatást a Batch .NET-hitelesítésre, hivatkozhat a [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) csomag és a [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) csomagot.

A következők `using` utasítások a kódban:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Referencia az Azure AD-végpont a kódban, beleértve a bérlő azonosítója. Egyszerű szolgáltatás használata esetén meg kell adnia egy bérlő-specifikus végpontot. A Bérlőazonosító lekéréséhez kövesse az ismertetett lépéseket [a Bérlőazonosító beszerzése az Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

A Batch szolgáltatás-erőforrás végpontjának hivatkozni:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

A Batch-fiók hivatkozni:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Adja meg az Alkalmazásazonosítót (ügyfél-azonosító) az alkalmazáshoz. Az Alkalmazásazonosító az alkalmazás regisztrációját az Azure Portalon érhető el:

```csharp
private const string ClientId = "<application-id>";
```

Adja meg a titkos kulcsot az Azure Portalról másolt:

```csharp
private const string ClientKey = "<secret-key>";
```

Az Azure ad-ből a hitelesítési jogkivonat beszerzése egy visszahívási metódus írása. A **GetAuthenticationTokenAsync** visszahívási metódus a felügyelet nélküli hitelesítés ADAL-hívást itt látható:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Hozza létre a **BatchTokenCredentials** objektum, amely a küldött paraméterként. Nyissa meg ezeket a hitelesítő adatokat használja a **BatchClient** objektum. Akkor használja azt **BatchClient** objektumot a Batch szolgáltatás további műveletek:

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
### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Példa: egy Azure AD egyszerű szolgáltatás használata a Batch Python

Egy egyszerű szolgáltatást a Batch Python-hitelesítésre, telepítse, és hivatkozni a [azure batch](https://pypi.org/project/azure-batch/) és [azure-közös](https://pypi.org/project/azure-common/) modulok.


```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Egyszerű szolgáltatás használata esetén meg kell adnia a bérlő azonosítója. A Bérlőazonosító lekéréséhez kövesse az ismertetett lépéseket [a Bérlőazonosító beszerzése az Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```python
TENANT_ID = "<tenant-id>";
```

A Batch szolgáltatás-erőforrás végpontjának hivatkozni:  

```python
RESOURCE = "https://batch.core.windows.net/";
```

A Batch-fiók hivatkozni:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com";
```

Adja meg az Alkalmazásazonosítót (ügyfél-azonosító) az alkalmazáshoz. Az Alkalmazásazonosító az alkalmazás regisztrációját az Azure Portalon érhető el:

```python
CLIENT_ID = "<application-id>";
```

Adja meg a titkos kulcsot az Azure Portalról másolt:

```python
SECRET = "<secret-key>";
```

Hozzon létre egy **ServicePrincipalCredentials** objektum:

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Egyszerű szolgáltatás hitelesítő adatai segítségével nyissa meg a **BatchServiceClient** objektum. Akkor használja azt **BatchServiceClient** objektum a későbbi műveletek során a Batch-szolgáltatás.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>További lépések

* Az Azure AD kapcsolatos további információkért tekintse meg a [Azure Active Directory dokumentációs](https://docs.microsoft.com/azure/active-directory/). Részletes példákat adal-t használó érhetők el a [Azure-Kódminták](https://azure.microsoft.com/resources/samples/?service=active-directory) könyvtár.

* Szolgáltatásnevekkel kapcsolatos további tudnivalókért lásd: [alkalmazás és egyszerű szolgáltatási objektumok Azure Active Directoryban](../active-directory/develop/app-objects-and-service-principals.md). Hozzon létre egy egyszerű szolgáltatást az Azure portal használatával, lásd: [Active Directory-alkalmazás és -erőforrások eléréséhez szolgáltatásnév létrehozása a portálon](../active-directory/develop/howto-create-service-principal-portal.md). PowerShell-lel vagy Azure CLI-vel is létrehozhat egy egyszerű szolgáltatást.

* Az Azure AD-vel Batch Management alkalmazások hitelesítéséhez, lásd: [az Active Directory hitelesítést Batch Management solutions](batch-aad-auth-management.md).

* Egy Python példa bemutatja, hogyan hozhat létre egy Batch-ügyfél hitelesítése az Azure AD-token használatával, lásd: a [üzembe helyezése az Azure Batch egyéni rendszerképet egy Python-szkriptet](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) minta.

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Mi az Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Hitelesítési forgatókönyvek az Azure ad-ben"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Alkalmazások integrálása az Azure Active Directoryval"
[azure_portal]: http://portal.azure.com
