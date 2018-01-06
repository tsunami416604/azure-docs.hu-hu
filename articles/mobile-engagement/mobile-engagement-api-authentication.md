---
title: Azokkal a Mobilmarketing REST API-k
description: "Ismerteti, hogyan hitelesítheti az Azure Mobile Engagement REST API-k"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: da82cb36-957a-4e19-a805-b44733cf6597
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: 66bcd738b86f846eae3499b289a6629323009a44
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Azokkal a Mobilmarketing REST API-k

## <a name="overview"></a>Áttekintés

Ez a dokumentum ismerteti, hogyan kérhet egy érvényes Azure AD Oauth jogkivonat a Mobile Engagement REST API-k szolgáltatással való hitelesítésre.

Feltételezzük, hogy egy érvényes Azure-előfizetéssel rendelkezik, és a Mobile Engagement-alkalmazáshoz egyikének használatával hozott létre a [fejlesztői oktatóanyagok](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Hitelesítés

A Microsoft Azure Active Directory jogkivonat-hitelesítéshez használt OAuth-alapú. 

Az API-k hitelesítési kérelemre érdekében egy engedélyezési fejléc fel kell venni minden kérelemnél, amely a következő formátumban:

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Az Azure Active Directory-jogkivonatokat egy óra múlva lejár.
> 
> 

Több módon is szolgáltatáshitelesítést egy token. Az API-kat az egy felhőszolgáltatás nevezik, mivel használandó API-kulcs. API-kulcs Azure terminológia a szolgáltatás egyszerű jelszó nevezik. Az alábbi eljárást a manuális beállítása csak egyik módját ismerteti.

### <a name="one-time-setup-using-script"></a>Egyszeri telepítő (parancsfájl használatával)

Kövesse a használatával egy PowerShell-parancsfájlt, ami a telepítés minimális időt vesz igénybe, de a legtöbb megengedett alapértelmezett beállításokat használja a telepítés végrehajtásához az alábbi utasítások készlete. Szükség esetén is követheti utasításait a [manuális telepítési módra](mobile-engagement-api-authentication-manual.md) mindezt a közvetlenül az Azure portálon, és tegye egyeztetését.

1. Az Azure PowerShell legújabb verziójának [Itt](http://aka.ms/webpi-azps). További információ a letöltési utasítás erre úgy tekinthet [hivatkozás](/powershell/azure/overview).
2. Azure PowerShell telepítése után a következő parancsok segítségével győződjön meg arról, hogy a **Azure modul** telepítve:

    a. Ellenőrizze, hogy az Azure PowerShell modul érhető el a rendelkezésre álló modulok listáját.

        Get-Module –ListAvailable

    ![Elérhető az Azure-modulok][1]

    b. Ha az Azure PowerShell modul nem találja a fenti listában, majd meg kell futtatnia:

        Import-Module Azure
3. Jelentkezzen be az Azure erőforrás-kezelő a Powershellből a következő parancs futtatásával, és a felhasználónév és jelszó megadása az Azure-fiókjával: 

        Login-AzureRmAccount
4. Ha több előfizetéssel rendelkezik, akkor kell futtatnia:

    a. Az előfizetések listáját, és másolja át a használni kívánt előfizetést előfizetés-azonosítójával. Ellenőrizze, hogy ez az előfizetés azonos olyanra, amelyben a Mobile Engagement-alkalmazáshoz, amely az API-k használatával kommunikálni fog. 

        Get-AzureRmSubscription

    b. A következő parancsot az előfizetés-azonosító megadása az előfizetéshez használandó konfigurálása.

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. Másolatot a a [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) parancsfájl-a helyi gépére, és mentse egy PowerShell-parancsmag (pl. `APIAuth.ps1`), majd hajtsa végre az `.\APIAuth.ps1`.
6. A parancsfájl ekkor megkérdezi, hogy adja meg a bemeneti **egyszerű név**. Adjon meg egy megfelelő nevet, hogy szeretné-e hozhat létre az Active Directory-alkalmazás (pl. APIAuth). 
7. A parancsfájl befejezése után megjelenik-e a következő négy érték, amely az ad-val programozott módon hitelesíteni kell ezért győződjön meg arról, hogy másolja őket. 

    **A TenantId**, **SubscriptionId**, **ApplicationId**, és **titkos**.

    Fogja használni, a TenantId `{TENANT_ID}`, mint ApplicationId `{CLIENT_ID}` és titkos, `{CLIENT_SECRET}`.

   > [!NOTE]
   > Az alapértelmezett biztonsági házirend letiltása a PowerShell-parancsfájlok futtatásakor. Ha igen, a végrehajtási házirend parancsfájl végrehajtása a következő parancs használatával ideiglenesen konfigurálása:
   > 
   > Set-ExecutionPolicy RemoteSigned
8. Ez hogyan PS parancsmagok készlete jelenne meg.
    ![][3]
9. Az Azure portálon, nyissa meg az Active Directory, kattintson a **App regisztrációk** , és keresse meg az alkalmazást, győződjön meg arról, hogy létezik-e![][4]

### <a name="steps-to-get-a-valid-token"></a>Érvényes token első lépéseit

1. Az API a következő paraméterekkel, és győződjön meg arról, hogy a BÉRLŐ\_azonosító, az ügyfél\_azonosító és az ügyfél\_titkos kulcs:
   
   * **Kérelem URL-CÍMÉT** ,`https://login.microsoftonline.com/{TENANT_ID}/oauth2/token`
   * **HTTP Content-Type fejléc** ,`application/x-www-form-urlencoded`
   * **HTTP-kérelem törzse** ,`grant_type=client\_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F`
     
    Az alábbiakban látható egy példa egy kérelem:
    ```
    POST /{TENANT_ID}/oauth2/token HTTP/1.1
    Host: login.microsoftonline.com
    Content-Type: application/x-www-form-urlencoded
    grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
    urce=https%3A%2F%2Fmanagement.core.windows.net%2F
    ```
    Íme egy példa egy válasz:
    ```
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Content-Length: 1234

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
    5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
    ```
     Ebben a példában szereplő URL-kódolást a FELADÁS egy vagy több paramétert `resource` érték `https://management.core.windows.net/`. Ügyeljen arra, hogy is URL kódolása `{CLIENT_SECRET}` , különleges karaktereket tartalmazhat.

     > [!NOTE]
     > Tesztelési, használhatja egy HTTP-ügyfél eszköz, például az [Fiddler](http://www.telerik.com/fiddler) vagy [Chrome Postman bővítmény](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) 
     > 
     > 
2. Most már minden API-hívás a olyan kérés hitelesítési fejlécéhez:
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    Ha egy adott vissza 401-es állapotkód, meg a választörzset, akkor előfordulhat, hogy meghatározzák a jogkivonat érvényessége lejárt. Ebben az esetben be egy új token.

## <a name="using-the-apis"></a>Az API-k használatával
Most, hogy egy érvényes tokent, készen áll az API-hívások.

1. Az egyes API-kérések szüksége lesz az előző szakaszban beszerzett érvényes, nem lejárt token átadni.
2. Szüksége lesz a beépülő modul a kérelem URI, amely azonosítja az alkalmazást az egyes paraméterek. A kérelem URI a következőképpen néznek
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    A paraméterek lekérése, kattintson az alkalmazás neve, és kattintson az irányítópulton, és a rendszer az összes 3 paraméterhez a következő lap jelenik meg.
   
   * **1** `{subscription-id}`
   * **2** `{app-collection}`
   * **3** `{app-resource-name}`
   * **4** az erőforráscsoport neve lesz **MobileEngagement** kivéve, ha létrehozott egy új. 

> [!NOTE]
> <br/>
> 
> 1. Ez volt az előző API-k esetében, hagyja figyelmen kívül az API legfelső szintű cím.<br/>
> 2. Ha az alkalmazás Azure-portál használatával hozott létre majd szeretné használni az alkalmazás erőforrás nevét, amely eltér attól az alkalmazás nevét, magát. Ha az alkalmazás az Azure-portálon létrehozott majd használja az alkalmazás nevére, saját magát (nincs alkalmazás-Erőfforás neve és az új portálon létrehozott alkalmazások alkalmazásnév közötti különbséget tenni).  
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/search-app.png



