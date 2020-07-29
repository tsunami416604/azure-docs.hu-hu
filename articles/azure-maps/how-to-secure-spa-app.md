---
title: Egyoldalas alkalmazások biztonságossá tétele nem interaktív bejelentkezéssel
titleSuffix: Azure Maps
description: Egyoldalas alkalmazások konfigurálása nem interaktív Azure AD szerepköralapú hozzáférés-vezérléssel és Azure Maps web SDK-val.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-javascript
ms.openlocfilehash: 5b7f26a03c117620be7c16abaf689763e370e5ba
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285696"
---
# <a name="how-to-secure-a-single-page-application-with-non-interactive-sign-in"></a>Egyoldalas alkalmazások biztonságossá tétele nem interaktív bejelentkezéssel

Az alábbi útmutató egy olyan alkalmazásra vonatkozik, amely a Azure Active Directory (Azure AD) használatával biztosít hozzáférési jogkivonatot Azure Maps alkalmazások számára, ha a felhasználó nem tud bejelentkezni az Azure AD-be. Ehhez a folyamathoz olyan webszolgáltatások üzemeltetésére van szükség, amelyet csak az egyoldalas webalkalmazáshoz kell biztosítani. Több implementáció is van, amelyek elérhetik az Azure AD-vel való hitelesítést. Ez az útmutató kihasználja a terméket, az Azure-függvényt hozzáférési tokenek beszerzéséhez.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Az Azure Maps szolgáltatás a felhasználói bejelentkezést és interaktív folyamatokat támogató hozzáférési jogkivonatokat is támogat. Az interaktív folyamatok a hozzáférés-visszavonási és a titkos felügyelet szűkebb hatókörét teszik lehetővé.

## <a name="create-azure-function"></a>Azure-függvény létrehozása

Hozzon létre egy biztonságos webszolgáltatási alkalmazást, amely felelős az Azure AD-vel való hitelesítéshez. 

1. Hozzon létre egy függvényt a Azure Portalban. További információt az Azure- [függvény létrehozása](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function)című témakörben talál.

2. Konfigurálja az Azure-függvény CORS szabályzatát, hogy az egyetlen oldal webalkalmazása is elérhető legyen. Ez biztosítja a böngésző ügyfelei számára a webalkalmazás engedélyezett eredetét. Lásd: [CORS funkció hozzáadása](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-rest-api#add-cors-functionality).

3. [Adjon hozzá egy rendszerhez rendelt identitást](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity) az Azure-függvényhez, hogy lehetővé váljon egy egyszerű szolgáltatásnév létrehozása az Azure ad-ben való hitelesítéshez.  

4. Szerepkör-alapú hozzáférés biztosítása a rendszer által hozzárendelt identitáshoz a Azure Maps fiókhoz. Részletekért lásd: [szerepköralapú hozzáférés engedélyezése](#grant-role-based-access) .

5. Kód írása az Azure-függvény számára Azure Maps hozzáférési tokenek beszerzéséhez a rendszer által hozzárendelt identitással az egyik támogatott mechanizmus vagy a REST protokoll használatával. Lásd: [tokenek beszerzése az Azure-erőforrásokhoz](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity)

    Példa A példa REST protokollra:

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    Példa a válaszra:

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. Az Azure Function HttpTrigger biztonságának konfigurálása

   * [Függvény-hozzáférési kulcs létrehozása](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#authorization-keys)
   * [Biztonságos http-végpont](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production) az Azure-függvény számára az éles környezetben.
   
7. Webalkalmazás-Azure Maps webes SDK konfigurálása. 

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

## <a name="grant-role-based-access"></a>Szerepkör-alapú hozzáférés engedélyezése

*Szerepköralapú hozzáférés-vezérlést* (RBAC) biztosít a rendszerhez rendelt identitás hozzárendelésével egy vagy több Azure-szerepkör-definícióhoz. Azure Maps számára elérhető RBAC-szerepkör-definíciók megtekintéséhez lépjen a **hozzáférés-vezérlés (iam)** elemre. Válassza ki a **szerepkörök**elemet, majd keressen rá a *Azure Maps*kezdetű szerepkörökre.

1. Nyissa meg **Azure Maps-fiókját**. Válassza a **hozzáférés-vezérlés (iam)**  >  **szerepkör-hozzárendelés**elemet.

    > [!div class="mx-imgBorder"]
    > ![RBAC megadása](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. A **szerepkör-hozzárendelések** lap **szerepkör**területén válassza ki a beépített Azure Maps szerepkör-definíciót, például **Azure Maps Adatolvasó** vagy **Azure Maps adatközreműködő**. **A hozzáférés társítása**területen válassza a **függvényalkalmazás**lehetőséget. Válassza ki a résztvevőt név alapján. Ez után válassza a **Mentés** lehetőséget.

   * A [szerepkör-hozzárendelések hozzáadása vagy eltávolítása](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)című témakörben talál további információt.

> [!WARNING]
> Azure Maps beépített szerepkör-definíciók nagyon nagy engedélyezési hozzáférést biztosítanak számos Azure Maps REST API-hoz. Az API-k minimális hozzáférésének korlátozásához tekintse meg az [Egyéni szerepkör-definíció létrehozása és a rendszerhez rendelt identitás hozzárendelése](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) az egyéni szerepkör-definícióhoz című témakört. Ez lehetővé teszi az alkalmazás számára a Azure Maps eléréséhez szükséges legalacsonyabb jogosultságot.

## <a name="next-steps"></a>További lépések

Az egyoldalas alkalmazás forgatókönyvének további megértése:
> [!div class="nextstepaction"]
> [Egyoldalas alkalmazás](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-overview)

A Azure Maps-fiók API-használati metrikáinak megkeresése:
> [!div class="nextstepaction"]
> [Használati metrikák megtekintése](how-to-view-api-usage.md)

Ismerkedjen meg az Azure AD és a Azure Maps integrációját bemutató példákkal:
> [!div class="nextstepaction"]
> [Azure Maps minták](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)
