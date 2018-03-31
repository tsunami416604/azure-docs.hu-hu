---
title: Azokkal a Mobilmarketing REST API-k
description: Ismerteti, hogyan hitelesítheti az Azure Mobile Engagement REST API-k
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: da82cb36-957a-4e19-a805-b44733cf6597
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: 5979ded9afaa31054f835b5f16fe525809f5730d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Azokkal a Mobilmarketing REST API-k
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

## <a name="overview"></a>Áttekintés

Ez a dokumentum ismerteti, hogyan kérhet egy érvényes Azure Active Directory (Azure AD) OAuth jogkivonat a Mobile Engagement REST API-k szolgáltatással való hitelesítésre.

Ez az eljárás feltételezi, hogy egy érvényes Azure-előfizetéssel rendelkezik, és a Mobile Engagement-alkalmazáshoz létrehozott egyikének használatával a [fejlesztői oktatóanyagok](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Hitelesítés

A Microsoft Azure Active Directory-alapú OAuth jogkivonat-hitelesítéshez használt. 

Egy API-kérelem hitelesítéséhez, egy engedélyezési fejléc számára minden kérelemnél hozzá kell adni. Az engedélyezési fejléc a következő formátumban kell megadni:

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Az Azure Active Directory-jogkivonatokat egy óra múlva lejár.
> 
> 

Több módon is szolgáltatáshitelesítést egy token. Az API-k nevezzük az egy felhőszolgáltatás, mert használandó API-kulcs. API-kulcs Azure terminológia a szolgáltatás egyszerű jelszó nevezik. Az alábbi eljárás manuális beállítása csak egyik módját ismerteti.

### <a name="one-time-setup-using-a-script"></a>Egyszeri telepítő (parancsfájl használatával)

A telepítés végrehajtásához egy PowerShell-parancsfájl használatával, a hálózatról a lépéseket az alábbi utasításokat. Egy PowerShell-parancsfájl telepítő idő a lehető legkevesebb igényel, de a legtöbb megengedett alapértelmezett beállításokat használja. 

Szükség esetén is követheti utasításait a [manuális telepítési módra](mobile-engagement-api-authentication-manual.md) ehhez az Azure portálról közvetlenül. Azure-portálról állít be, amikor egy részletes konfigurációs teheti meg.

1. A legújabb verzióra által az Azure PowerShell [letöltheti](http://aka.ms/webpi-azps). A letöltési utasításokat kapcsolatos további információkért lásd: [Ez az Áttekintés](/powershell/azure/overview).

2. PowerShell telepítése után a következő parancsok segítségével győződjön meg arról, hogy a **Azure modul** telepítve:

    a. Ellenőrizze, hogy az Azure PowerShell modul érhető el a rendelkezésre álló modulok listáját.

        Get-Module –ListAvailable

    ![Elérhető az Azure-modulok][1]

    b. Ha a fenti listán szereplő nem találja az Azure PowerShell-modult, majd meg kell futtatnia:

        Import-Module Azure
3. Jelentkezzen be az Azure Resource Manager powershellből a következő parancs futtatásával. Adja meg a felhasználónevet és jelszót az Azure-fiókjával: 

        Login-AzureRmAccount
4. Ha több előfizetéssel rendelkezik, a következő lépéseket:

    a. Az előfizetések listájának lekérése. Másolja a **SubscriptionId** a használni kívánt előfizetést. Ellenőrizze, hogy ehhez az előfizetéshez a Mobile Engagement-alkalmazást. Az alkalmazás használatához az API-k együttműködhet fog. 

        Get-AzureRmSubscription

    b. A következő parancsot. Adja meg a **SubscriptionId** konfigurálása az előfizetést, akkor fog használni:

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. Másolatot a a [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) parancsfájl a helyi számítógépre. Mentse a PowerShell-parancsmag (például `APIAuth.ps1`), és futtassa azt.

         `.\APIAuth.ps1`.

6. A parancsprogram kéri, hogy adja meg a bemeneti **egyszerű név**. Adjon meg egy megfelelő nevet, amely az Active Directory-alkalmazás (például APIAuth) használni kívánt. 

7. Miután a parancsfájl befejezése után történik, a következő négy értékek jeleníti meg. Ügyeljen arra, hogy másolja őket, mert már szükség szoftveres hitelesítésére és az Active Directory: 

   - **TenantId**
   - **SubscriptionId**
   - **ApplicationId**
   - **Titkos kód**

   Használhatja, mint a TenantId `{TENANT_ID}`, mint ApplicationId `{CLIENT_ID}` és titkos, `{CLIENT_SECRET}`.

   > [!NOTE]
   > Az alapértelmezett biztonsági házirend blokkolhatják a PowerShell-parancsfájlok futtatásakor. Ha igen, a következő parancs használatával ideiglenesen a parancsfájl végrehajtása a végrehajtási házirend konfigurálása:
   > 
   > Set-ExecutionPolicy RemoteSigned
8. Ez a PowerShell-parancsmagok készlete megjelenését.
    ![PowerShell-parancsmagok][3]
9. Az Active Directoryban, válassza ki az Azure-portálon lépjen **App regisztrációk**, és majd keresse meg az alkalmazást, győződjön meg arról, hogy létezik-e.
    ![Keresse meg az alkalmazást][4]

### <a name="steps-to-get-a-valid-token"></a>Érvényes token első lépéseit

1. Hívja az API-t a következő paraméterekkel. Győződjön meg arról, hogy **BÉRLŐI\_azonosító**, **ügyfél\_azonosító**, és **ügyfél\_titkos**:
   
   * **Kérelem URL-CÍMÉT** , `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token`

   * **HTTP Content-Type fejléc** , `application/x-www-form-urlencoded`
   
   * **HTTP-kérelem törzse** , `grant_type=client\_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F`
     
    Az alábbiakban látható egy példa egy kérelem:
    ```
    POST /{TENANT_ID}/oauth2/token HTTP/1.1
    Host: login.microsoftonline.com
    Content-Type: application/x-www-form-urlencoded
    grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
    urce=https%3A%2F%2Fmanagement.core.windows.net%2F
    ```
    Az alábbiakban látható egy példa egy válasz:
    ```
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Content-Length: 1234

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
    5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
    ```
     Ez a példa tartalmazza a FELADÁS egy vagy több paramétert, amelyben az URL-kódolást `resource` érték `https://management.core.windows.net/`. Ügyeljen arra, hogy az URL-cím kódolása is `{CLIENT_SECRET}`, mert különleges karaktereket tartalmaz.

     > [!NOTE]
     > Tesztelési, eszközzel egy HTTP ügyfél például [Fiddler](http://www.telerik.com/fiddler) vagy [Chrome Postman bővítmény](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop). 
     > 
     > 
2. Most már minden API-hívás a olyan kérés hitelesítési fejlécéhez:
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    A kérés a 401-es állapotkód adja vissza, ha meg a választörzset. Akkor lehet, hogy jelzi, hogy a jogkivonat érvényessége lejárt. Ebben az esetben be egy új token.

## <a name="use-the-apis"></a>Az API-k
Most, hogy egy érvényes tokent, készen áll az API-hívások.

1. Az egyes API-kérések kell átadni egy érvényes, nem lejárt jogkivonatot. A fennmaradó lexikális elem szerepel az előző szakaszban beszerzett.

2. Beépülő modul a kérelem URI-azonosítója, amely azonosítja az alkalmazás egyes paramétereket. A kérelem URI néz ki a következő kódot:
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    Ahhoz, hogy a paraméterek, válassza ki az alkalmazás nevét. Válassza ki **irányítópult**. Egy lapon minden három paramétert is – lásd a következőket:
   
   * **1** `{subscription-id}`
   * **2** `{app-collection}`
   * **3** `{app-resource-name}`
   * **4** az erőforráscsoport neve lesz **MobileEngagement** kivéve, ha létrehozott egy új. 

> [!NOTE]
> Figyelmen kívül hagyja az API legfelső szintű cím, mert volt az előző API-k esetében.
> 
> Ha az alkalmazás az Azure portál használatával hozott létre, majd szeretné az alkalmazás erőforrás-név, amely eltér az alkalmazás maga használja. Ha létrehozta az alkalmazást az Azure portálon, majd használja az alkalmazás nevére. (Nincs különbséget tenni az alkalmazás-Erőfforás neve és az alkalmazás nevére az új portálon létrehozott alkalmazások között.)
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/search-app.png



