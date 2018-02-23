---
title: "Az Azure Active Directoryt hitelesítéséhez az Azure Batch szolgáltatás megoldások |} Microsoft Docs"
description: "Kötegelt az Azure AD a Batch szolgáltatás hitelesítést támogatja."
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 09/28/2017
ms.author: danlep
ms.openlocfilehash: 8ad9c3a779ca52140a78ae905e4825f3144976aa
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Kötegelt szolgáltatási megoldások és az Active Directory hitelesítéséhez

Az Azure Batch támogatja a hitelesítési [Azure Active Directory] [ aad_about] (az Azure AD). Az Azure AD a Microsoft több-bérlős felhőalapú címtár és az identity management szolgáltatás. Azure magát a felhasználók, a szolgáltatás-rendszergazdák és a szervezeti felhasználók hitelesítéséhez használja az Azure AD.

Az Azure AD-alapú hitelesítés használata az Azure Batch, az alábbi két módszer egyikével hitelesítheti:

- A **integrált hitelesítés** , amely az alkalmazás dolgozik a felhasználó hitelesítéséhez. Integrált hitelesítést használó alkalmazások a felhasználói hitelesítő adatokat gyűjt, és ezeket a hitelesítő adatokat használ kötegelt erőforrásokhoz való hozzáférés hitelesítéséhez.
- Használatával egy **egyszerű** egy felügyelet nélküli alkalmazás hitelesítéséhez. Egy egyszerű szolgáltatást határozza meg a házirend és megadásával az alkalmazás ahhoz, hogy az alkalmazás futásidőben erőforrásokhoz való hozzáféréskor.

Az Azure AD kapcsolatos további tudnivalókért tekintse meg a [Azure Active Directory dokumentációjának](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>A hitelesítési végpontot

Batch-alkalmazások az Azure AD hitelesíti, akkor is kell néhány jól ismert végpontok a kódban.

### <a name="azure-ad-endpoint"></a>Azure AD-végpont

A következő szolgáltató az Azure AD-végpont esetében:

`https://login.microsoftonline.com/`

Hitelesítés az Azure ad-vel, ehhez a végponthoz, a bérlő azonosítója (directory ID) együtt használja. A bérlő azonosítója az Azure AD-bérlő a hitelesítéshez használandó azonosítja. Bérlői azonosító lekéréséhez kövesse a témakörben ismertetett lépéseket [bérlői azonosító lekérése az Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> A bérlő-specifikus végpont a hitelesítést egy egyszerű szolgáltatás használata esetén szükséges. 
> 
> A bérlő-specifikus-végpont esetében az Önt a hitelesítéshez integrált hitelesítés használata kötelező, de ajánlott. Azonban is használhatja az Azure AD közös végpontot. A közös végpont összegyűjtéséhez felületet, ha nincs megadva egy adott bérlő általános hitelesítő adatokat biztosít. A közös végpont `https://login.microsoftonline.com/common`.
>
>

Az Azure AD-végpontok kapcsolatos további információkért lásd: [hitelesítési forgatókönyvek az Azure AD][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Kötegelt erőforrás végpont

Használja a **Azure Batch erőforrás végpont** a Batch szolgáltatás kérelmek hitelesítéséhez jogkivonat beszerzése:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Az alkalmazás regisztrálása a bérlő

Az első lépés az Azure AD hitelesítéséhez van az alkalmazás regisztrálása az Azure AD-bérlő. Az alkalmazás regisztrálása lehetővé teszi az Azure hívja [Active Directory Authentication Library] [ aad_adal] (ADAL) a felhasználói kódból. Az adal-t az API-k biztosít hitelesítéséhez az Azure AD az alkalmazásból. Az alkalmazás regisztrálása kell hogy tervezi integrált hitelesítést vagy egy egyszerű szolgáltatást.

Ha regisztrálja az alkalmazást, adja meg információkat az Azure AD az alkalmazással kapcsolatos. Az Azure AD majd biztosít, amelyekkel az alkalmazás társítása az Azure AD futásidőben Azonosítóját. Az Alkalmazásazonosító kapcsolatos további információkért lásd: [alkalmazás és szolgáltatás egyszerű objektumok az Azure Active Directoryban](../active-directory/develop/active-directory-application-objects.md).

A kötegelt alkalmazás regisztrálásához kövesse a [egy alkalmazás hozzáadása](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) szakasz [alkalmazások integrálása az Azure Active Directory][aad_integrate]. Natív alkalmazás regisztrálnia az alkalmazást, ha bármilyen érvényes URI-JÁNAK megadhatja a **átirányítási URI-**. Nem kell egy valódi végpontot bizonyult.

Az alkalmazás regisztrálása után megjelenik az alkalmazás azonosítója:

![A kötegelt alkalmazás regisztrálása az Azure ad szolgáltatással](./media/batch-aad-auth/app-registration-data-plane.png)

Egy alkalmazás regisztrálása az Azure ad-vel kapcsolatos további információkért lásd: [hitelesítési forgatókönyvek az Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Az Active Directory Bérlőazonosító beszerzése

A bérlő azonosítója azonosítja az Azure AD-bérlő számára az alkalmazás hitelesítési szolgáltatásokat biztosít. Ahhoz, hogy a bérlő azonosítója, kövesse az alábbi lépéseket:

1. Az Azure-portálon válassza ki az Active Directory.
2. Kattintson a **Tulajdonságok** elemre.
3. A megadott könyvtár-azonosítóhoz GUID-érték másolása Ez az érték rövidítése a bérlő azonosítója.

![Másolja a könyvtár-azonosítója](./media/batch-aad-auth/aad-directory-id.png)


## <a name="use-integrated-authentication"></a>Integrált hitelesítés használata

Hitelesítés integrált hitelesítés, kell biztosítania az alkalmazás engedélyeit a Batch szolgáltatás API való kapcsolódáshoz. Ez a lépés lehetővé teszi, hogy az alkalmazás az Azure ad-vel a Batch szolgáltatás API hívások hitelesítése.

Miután megismerte [az alkalmazás regisztrálva](#register-your-application-with-an-azure-ad-tenant), kövesse az alábbi lépéseket az Azure-portálon való hozzáférést, a Batch szolgáltatás:

1. Az Azure portál bal oldali navigációs ablaktábláján válassza **minden szolgáltatás**. Kattintson a **App regisztrációk**.
2. Keresse meg az alkalmazás regisztrációk a listában az alkalmazás nevét:

    ![Keresse meg az alkalmazás neve](./media/batch-aad-auth/search-app-registration.png)

3. Nyissa meg a **beállítások** panel az alkalmazáshoz. Az a **API-hozzáférés** szakaszban jelölje be **szükséges engedélyek**.
4. Az a **szükséges engedélyek** panelen kattintson a **Hozzáadás** gombra.
5. 1. lépésben a keresse meg a kötegelt API. Keressen rá az egyes karakterláncokra, addig amíg meg nem találja az API-t:
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. Újabb Azure AD-bérlők ezt a nevet használhatják.
    3. A **ddbf3205-c6bd-46ae-8127-60eb93363864** a Batch API azonosítója. 
6. Miután megtalálta a kötegelt API-t, jelölje ki, majd kattintson a **válasszon** gombra.
6. A 2. lépésben, jelölje be a jelölőnégyzetet a **Azure Batch szolgáltatás** , és kattintson a **válasszon** gombra.
7. Kattintson a **végzett** gombra.

A **szükséges engedélyek** most mutat be, hogy az Azure AD alkalmazás fér hozzá az adal-t, mind a Batch szolgáltatás API panelen. Engedélyekkel az adal-ra automatikusan regisztrációt az alkalmazás az Azure ad-val.

![Támogatás API engedélyei](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Egy egyszerű szolgáltatás használata 

Hitelesítést végezni egy alkalmazás, amely futtatja a felügyelet nélküli, használhat egy egyszerű szolgáltatást. Az alkalmazás regisztrálása után kövesse az alábbi lépéseket egy egyszerű szolgáltatás konfigurálása az Azure portálon:

1. Egy alkalmazás titkos kulcs kérelmet.
2. Az RBAC szerepkör hozzárendelése az alkalmazáshoz.

### <a name="request-a-secret-key-for-your-application"></a>A kérelem egy alkalmazás titkos kulcs

Ha az alkalmazás egy egyszerű szolgáltatás végzi a hitelesítést, elküld az Alkalmazásazonosítót és a titkos kulcsot az Azure AD. Meg kell létrehozni, és másolja át a titkos kulcsot a kódból használni.

Kövesse az alábbi lépéseket az Azure-portálon:

1. Az Azure portál bal oldali navigációs ablaktábláján válassza **minden szolgáltatás**. Kattintson a **App regisztrációk**.
2. Keresse meg az alkalmazás regisztrációk a listában az alkalmazás nevét.
3. Megjelenítés a **beállítások** panelen. Az a **API-hozzáférés** szakaszban jelölje be **kulcsok**.
4. A kulcs létrehozásához adja meg a kulcs leírását. Ezután válassza ki a kulcs egy vagy két éves időtartammal. 
5. Kattintson a **mentése** gombra kattintva hozzon létre, és megjeleníti a kulcsot. Másolja egy biztonságos helyre, a kulcs értékét, akkor nem fog tudni férni újra Ha kilép a panelen. 

    ![A titkos kulcs létrehozása](./media/batch-aad-auth/secret-key.png)

### <a name="assign-an-rbac-role-to-your-application"></a>Az RBAC szerepkör hozzárendelése az alkalmazáshoz

Egy egyszerű szolgáltatásnév végzett hitelesítéshez, akkor hozzá kell rendelni RBAC szerepet az alkalmazás. Kövesse az alábbi lépéseket:

1. Az Azure-portálon lépjen a Batch-fiók, amelyet az alkalmazás.
2. Az a **beállítások** a Batch-fiók, jelölje be a panelt **hozzáférés-vezérlés (IAM)**.
3. Kattintson a **Hozzáadás** gombra. 
4. Az a **szerepkör** legördülő listából válassza a _közreműködő_ vagy _olvasó_ szerepkör az alkalmazáshoz. Ezek a szerepkörök további információkért lásd: [szerepköralapú hozzáférés-vezérlés az Azure-portálon az első lépései](../active-directory/role-based-access-control-what-is.md).  
5. Az a **válasszon** mezőbe írja be az alkalmazás nevét. Válassza ki az alkalmazást a listából, és kattintson a **mentése**.

Az alkalmazás ekkor meg kell jelennie a hozzáférés-vezérlési beállításokkal hozzárendelt RBAC szerepkörrel rendelkező. 

![Az RBAC szerepkör hozzárendelése az alkalmazáshoz](./media/batch-aad-auth/app-rbac-role.png)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Az Azure Active Directory Bérlőazonosító beszerzése

A bérlő azonosítója azonosítja az Azure AD-bérlő számára az alkalmazás hitelesítési szolgáltatásokat biztosít. Ahhoz, hogy a bérlő azonosítója, kövesse az alábbi lépéseket:

1. Az Azure-portálon válassza ki az Active Directory.
2. Kattintson a **Tulajdonságok** elemre.
3. A megadott könyvtár-azonosítóhoz GUID-érték másolása Ez az érték rövidítése a bérlő azonosítója.

![Másolja a könyvtár-azonosítója](./media/batch-aad-auth/aad-directory-id.png)


## <a name="code-examples"></a>Kódpéldák

Ebben a szakaszban szereplő példák bemutatják, hogyan hitelesítéséhez az Azure AD integrált hitelesítésen keresztül, és egy egyszerű szolgáltatást. A Kódminták .NET használja, de a fogalmakat hasonló, ha a többi nyelvet.

> [!NOTE]
> Egy Azure AD hitelesítési tokent egy óra múlva lejár. A hosszú élettartamú használatakor **BatchClient** objektum, azt javasoljuk, hogy visszaállíthatja a jogkivonat az ADAL halasztása minden kérelemnél annak érdekében, hogy mindig legyen egy érvényes tokent. 
>
>
> Ennek érdekében a .NET olyan metódus írása, amely a token kikeresi az Azure AD, és adja át az adott metódust egy **BatchTokenCredentials** meghatalmazottként objektum. A delegált metódus lehívásra kerül halasztása minden kérelemnél a Batch szolgáltatás biztosításához, hogy egy érvényes jogkivonat van-e megadva. Alapértelmezés szerint a ADAL jogkivonatokat, gyorsítótárazza, így egy új jogkivonatot az Azure AD kéri le a rendszer csak akkor, ha szükséges. Az Azure AD-jogkivonatokat kapcsolatos további információkért lásd: [hitelesítési forgatókönyvek az Azure AD][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Példa: az Azure AD hitelesítési integrálva Batch .NET

A hitelesítéshez a Batch .NET-integrált hitelesítés hivatkozik a [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) csomag és a [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) csomag.

Adja meg a következőket `using` utasítások a kódban:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Útmutató az Azure AD-végpont a kódban, beleértve a bérlő azonosítója. Bérlői azonosító lekéréséhez kövesse a témakörben ismertetett lépéseket [bérlői azonosító lekérése az Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

A Batch szolgáltatás erőforrás végpont – referencia:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

A Batch-fiók hivatkozási:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Adja meg az alkalmazás Azonosítóját (ügyfél-azonosító) az alkalmazáshoz. Az Alkalmazásazonosítót a alkalmazás regisztrálása az Azure-portálon a érhető el:

```csharp
private const string ClientId = "<application-id>";
```

Az átirányítási URI-t a regisztráció során megadott szeretne másolni. Az átirányítási URI a kódban megadott meg kell egyeznie az átirányítási URI-t az alkalmazás regisztrálásakor megadott:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Az Azure AD a hitelesítési jogkivonat beszerzése az egy visszahívási metódus írása. A **GetAuthenticationTokenAsync** visszahívási metódus itt látható a hívások adal-t hitelesítéshez a felhasználó, aki az alkalmazással együtt dolgozik. A **AcquireTokenAsync** ADAL által megadott metódus bekéri a felhasználótól a hitelesítő adataikat, és az alkalmazás telepítése folytatódik, ha a felhasználó eszközeivel (kivéve, ha már gyorsítótárazott hitelesítő adatok):

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

Összeállíthatja a **BatchTokenCredentials** objektum, amely a delegált paramétert fogad. Ezen hitelesítő adatok segítségével nyissa meg a **BatchClient** objektum. Is használhatja, amely **BatchClient** -objektumot a Batch szolgáltatás további műveleteket:

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

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Példa: az Azure AD szolgáltatás egyszerű Batch .NET használatával

Egy egyszerű szolgáltatást Batch .NET hitelesítés, hivatkozzon a [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) csomag és a [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) csomag.

Adja meg a következőket `using` utasítások a kódban:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Útmutató az Azure AD-végpont a kódban, beleértve a bérlő azonosítója. Egy egyszerű szolgáltatás használata esetén meg kell adnia egy bérlő-specifikus végpontot. Bérlői azonosító lekéréséhez kövesse a témakörben ismertetett lépéseket [bérlői azonosító lekérése az Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

A Batch szolgáltatás erőforrás végpont – referencia:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

A Batch-fiók hivatkozási:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Adja meg az alkalmazás Azonosítóját (ügyfél-azonosító) az alkalmazáshoz. Az Alkalmazásazonosítót a alkalmazás regisztrálása az Azure-portálon a érhető el:

```csharp
private const string ClientId = "<application-id>";
```

Adja meg a titkos kulcsot az Azure portálról másolt:

```csharp
private const string ClientKey = "<secret-key>";
```

Az Azure AD a hitelesítési jogkivonat beszerzése az egy visszahívási metódus írása. A **GetAuthenticationTokenAsync** visszahívási metódus hívások az adal TÁRAT a felügyelet nélküli hitelesítéshez itt látható:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Összeállíthatja a **BatchTokenCredentials** objektum, amely a delegált paramétert fogad. Ezen hitelesítő adatok segítségével nyissa meg a **BatchClient** objektum. Ezután használhatja, amely **BatchClient** -objektumot a Batch szolgáltatás további műveleteket:

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

## <a name="next-steps"></a>További lépések

Az Azure AD kapcsolatos további tudnivalókért tekintse meg a [Azure Active Directory dokumentációjának](https://docs.microsoft.com/azure/active-directory/). Részletes példa bemutatja, hogyan adal-t használó érhetők el a [Azure mintakódok](https://azure.microsoft.com/resources/samples/?service=active-directory) könyvtár.

Szolgáltatásnevekről kapcsolatos további információkért lásd: [alkalmazás és szolgáltatás egyszerű objektumok az Azure Active Directoryban](../active-directory/develop/active-directory-application-objects.md). Az Azure portál használatával egyszerű szolgáltatás létrehozása: [használata portal létrehozása az Active Directory erőforrásokat elérő alkalmazás és szolgáltatás egyszerű](../resource-group-create-service-principal-portal.md). A szolgáltatás egyszerű PowerShell vagy az Azure parancssori felület is létrehozhat.

Kötegelt felügyeleti alkalmazások az Azure AD hitelesíti, lásd: [hitelesítéséhez kötegelt megoldásokat az Active Directory](batch-aad-auth-management.md).

A Python példa bemutatja, hogyan hozzon létre egy kötegelt ügyfél hitelesítése az Azure AD token használatával, tekintse meg a [Azure Active Directory-hitelesítéssel](http://azure-sdk-for-python.readthedocs.io/en/latest/batch.html#azure-active-directory-authentication) Python-dokumentáció az Azure SDK-minta.

[aad_about]: ../active-directory/active-directory-whatis.md "Mi az Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Az Azure Active Directory hitelesítési forgatókönyvei"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Alkalmazások integrálása az Azure Active Directoryval"
[azure_portal]: http://portal.azure.com
