---
title: Microsoft identity platform Python démon | Azure
description: Ismerje meg, hogyan kaphat egy Python-folyamat hozzáférési jogkivonatot, és hogyan hívhat meg egy Microsoft identity platform-végpont által védett API-t az alkalmazás saját identitásának használatával
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Python
ms.openlocfilehash: ecbed58eabd2e835d8fa202916829d1da91210e7
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991058"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-python-console-app-using-apps-identity"></a>Rövid útmutató: Jogkivonat beszerzése és Microsoft Graph API hívása Python-konzolalkalmazásból az alkalmazás identitásának használatával

Ebben a rövid útmutatóban írjon egy Python-alkalmazást, amely az alkalmazás identitásával kap egy hozzáférést, majd meghívja a Microsoft Graph API-t a címtárban lévő [felhasználók listájának](https://docs.microsoft.com/graph/api/user-list) megjelenítéséhez. Ez a forgatókönyv olyan helyzetekben hasznos, ahol a fej nélküli, felügyelet nélküli feladat vagy a Windows-szolgáltatás kell futtatni egy alkalmazás identitás, a felhasználó identitása helyett.

> [!div renderon="docs"]
> ![Megmutatja, hogyan működik az ez a rövid útmutató által létrehozott mintaalkalmazás](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához a következőkre van szükség:

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) vagy [Python 3+](https://www.python.org/downloads/release/python-364/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése

> [!div renderon="docs" class="sxs-lookup"]
>
> A rövid útmutató elindításához két lehetőség van: Expressz (alább 1. lehetőség) és Manuális (2. lehetőség)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése
>
> 1. Nyissa meg az új [Azure Portal – Alkalmazásregisztrációk](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonDaemonQuickstartPage/sourceType/docs) ablaktáblát.
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
> 1. Keresse meg a Microsoft identity platform ot a fejlesztőknek [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapon.
> 1. Válassza **az Új regisztráció lehetőséget.**
> 1. Amikor megjelenik a **Jelentkezés iszatának regisztrálása** lap, adja meg a jelentkezési lap regisztrációs adatait. 
> 1. A **Név szakaszban** adjon meg egy értelmes alkalmazásnevet, amely megjelenik az alkalmazás felhasználói számára, például `Daemon-console`a lehetőséget, majd válassza a **Regisztráció** lehetőséget az alkalmazás létrehozásához.
> 1. A regisztrációt követően válassza a **Tanúsítványok & titkos kulcsok menüt.**
> 1. Az **Ügyféltitkok csoportban**válassza a **+ Új ügyféltitok**lehetőséget. Adjon neki nevet, és válassza **a Hozzáadás**lehetőséget. Másolja a titkot egy biztonságos helyre. Szüksége lesz rá, hogy használja a kódot.
> 1. Most válassza az **API-engedélyek menüt,** válassza **a + Engedély hozzáadása** gombot, és válassza a Microsoft **Graph**lehetőséget .
> 1. Válassza **az Alkalmazásengedélyek lehetőséget.**
> 1. A **Felhasználói** csomópont csoportban válassza a **User.Read.All ( Felhasználó. olvasás)** lehetőséget, majd az Engedélyek **hozzáadása** lehetőséget.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>A rövid útmutató alkalmazás letöltése és konfigurálása
> 
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>1. lépés: Az alkalmazás konfigurálása az Azure Portalon
> A gyorsútmutató működéséhez a kódminta működéséhez létre kell hoznia egy ügyféltitkot, és hozzá kell adnia a Graph API **User.Read.All** alkalmazásengedélyét.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Hajtsa végre ezeket a módosításokat nekem]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-netcore-daemon/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-your-python-project"></a>2. lépés: A Python-projekt letöltése

> [!div renderon="docs"]
> [A Python démonprojekt letöltése](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [A kódminta letöltése](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-your-python-project"></a>3. lépés: A Python-projekt konfigurálása
> 
> 1. Csomagolja ki a zip-fájlt egy helyi mappába a lemez gyökerének közelében (például: **C:\Azure-Samples**).
> 1. Keresse meg az **1-Call-MsGraph-WithSecret almappát"**.
> 1. Írja be **a parameters.json parancsot,** és cserélje le a mezők `authority`értékeit, `client_id`valamint `secret` a következő kódrészletre:
>
>    ```json
>    "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
>    "client_id": "Enter_the_Application_Id_Here",
>    "secret": "Enter_the_Client_Secret_Here"
>    ```
>    Az elemek magyarázata:
>    - `Enter_the_Application_Id_Here` – ez a regisztrált alkalmazáshoz tartozó **Alkalmazás (ügyfél) azonosítója** érték.
>    - `Enter_the_Tenant_Id_Here`- cserélje le ezt az értéket a **bérlőazonosító** vagy **a bérlő nevére** (például contoso.microsoft.com)
>    - `Enter_the_Client_Secret_Here`- cserélje le ezt az értéket az 1.
>
> > [!TIP]
> > **Az alkalmazás (ügyfél) azonosító**, **címtár (bérlői) azonosító**értékek et, keresse fel az alkalmazás **áttekintése** lapot az Azure Portalon. Új kulcs létrehozásához nyissa meg **a Tanúsítványok & titkos kulcsok** lapot.
    
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>3. lépés: Rendszergazdai hozzájárulás

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>4. lépés: Rendszergazdai hozzájárulás

Ha ezen a ponton próbálja meg futtatni az alkalmazást, *http 403 – Tiltott* hibaüzenet jelenik meg: `Insufficient privileges to complete the operation`. Ez a hiba azért fordul elő, mert minden *csak alkalmazásengedélyhez* rendszergazdai hozzájárulás szükséges: a címtár globális rendszergazdájának beleegyezését kell adnia az alkalmazáshoz. A szerepkörtől függően válasszon az alábbi lehetőségek közül:

##### <a name="global-tenant-administrator"></a>Globális bérlői rendszergazda

> [!div renderon="docs"]
> Ha Globális bérlői rendszergazda, lépjen az **API-engedélyek** lapra az Azure Portal alkalmazásregisztrációjában (előzetes verzió), és válassza a **Rendszergazdai hozzájárulás megadása {Tenant Name} (ahol** {Tenant Name} a címtár neve).

> [!div renderon="portal" class="sxs-lookup"]
> Ha Ön globális rendszergazda, nyissa meg **az API-engedélyek** lapot, és válassza **a Rendszergazdai hozzájárulás megadása Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Ugrás az API-engedélyek lapra]()

##### <a name="standard-user"></a>Általános jogú felhasználó

Ha Ön a bérlő általános jogú felhasználója, akkor meg kell kérnie egy globális rendszergazdát, hogy adja meg az alkalmazás rendszergazdai beleegyezését. Ehhez adja meg a következő URL-címet a rendszergazdának:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Az elemek magyarázata:
>> * `Enter_the_Tenant_Id_Here`- cserélje le ezt az értéket a **bérlőazonosító** vagy **a bérlő nevére** (például contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` – ez a regisztrált alkalmazáshoz tartozó **Alkalmazás (ügyfél) azonosítója** érték.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>4. lépés: Az alkalmazás futtatása

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>5. lépés: Az alkalmazás futtatása

A minta függőségeit egyszer kell telepítenie.

```console
pip install -r requirements.txt
```

Ezután futtassa az alkalmazást parancssorból vagy konzolon keresztül:

```console
python confidential_client_secret_sample.py parameters.json
```

Meg kell jelennie a konzol kimenetén néhány Json-töredék, amely az Azure AD-címtárban lévő felhasználók listáját képviseli.

> [!IMPORTANT]
> Ez a rövid útmutató alkalmazás egy ügyféltitkos kulcsot használ, hogy bizalmas ügyfélként azonosítsa magát. Mivel az ügyféltitkos kulcsot egyszerű szövegként adja hozzá a projektfájlokhoz, biztonsági okokból ajánlott az alkalmazás éles alkalmazásként való felhasználása előtt az ügyféltitok helyett tanúsítványt használni. A tanúsítványok használatáról további információt az [alábbi útmutatókban,](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/README.md) a minta azonos GitHub-tárházában talál, de a második mappában, a **2-Call-MsGraph-WithCertificate mappában.**

## <a name="more-information"></a>További információ

### <a name="msal-python"></a>MSAL Python

[Az MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) a felhasználók bejelentkezéséhez és a Microsoft identity platform által védett API-k eléréséhez használt jogkivonatok kéréséhez használt tár. A mint ismertetjük, ez a gyorsindítás jogkivonatokat kér az alkalmazás saját identitásának használatával a delegált engedélyek helyett. Az ebben az esetben használt hitelesítési folyamat *[az ügyfél hitelesítő adatainak oauth](v2-oauth2-client-creds-grant-flow.md)* flow-ja. Az MSAL Python démonalkalmazásokkal való használatáról ebben [a cikkben olvashat bővebben.](scenario-daemon-overview.md)

 Az MSAL Python a következő pip parancs futtatásával telepíthető.

```powershell
pip install msal
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

Az MSAL-re mutató hivatkozás hozzáadásához adja hozzá az alábbi kódot:

```Python
import msal
```

Ezután inicializálja az MSAL-t az alábbi kóddal:

```Python
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"])
```

> | Az elemek magyarázata: ||
> |---------|---------|
> | `config["secret"]` | Az alkalmazáshoz az Azure Portalon létrehozott ügyféltitok. |
> | `config["client_id"]` | Az Azure Portalon regisztrált alkalmazás **alkalmazásazonosítója (ügyfél-azonosítója)**. Ezt az értéket az alkalmazás **Áttekintés** oldalán találja az Azure Portalon. |
> | `config["authority"]`    | A felhasználó által hitelesítendő STS-végpont. Általában <https://login.microsoftonline.com/{tenant}> a nyilvános felhőben, ahol {tenant} a bérlő vagy a bérlőazonosító neve.|

További információkért kérjük, olvassa el [a `ConfidentialClientApplication` ](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)

### <a name="requesting-tokens"></a>Jogkivonatok lekérése

Ha az alkalmazás identitásával szeretne tokeneket igényelni, használja `AcquireTokenForClient` a módszert:

```Python
result = None
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

> |Az elemek magyarázata:| |
> |---------|---------|
> | `config["scope"]` | A kért hatóköröket tartalmazza. A bizalmas ügyfelek esetében ez a `{Application ID URI}/.default` formátum ot kell használnia annak jelzésére, hogy a kért hatókörök statikusan definiálva `{Application ID URI}` vannak `https://graph.microsoft.com`az Azure Portalon beállított alkalmazásobjektumban (a Microsoft Graph esetében a rámutat). Az egyéni webes `{Application ID URI}` API-k, az Azure Portal alkalmazásregisztráció (előzetes verzió) **Elérhetővé egy API szakaszban** van definiálva. |

További információkért kérjük, olvassa el [a `AcquireTokenForClient` ](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a démonalkalmazásokról, tekintse meg a forgatókönyv céloldalát

> [!div class="nextstepaction"]
> [Webes API-kat meghívjaó démonalkalmazás](scenario-daemon-overview.md)

A démonalkalmazás oktatóanyagát lásd:

> [!div class="nextstepaction"]
> [Daemon Python konzol bemutató](https://github.com/Azure-Samples/ms-identity-python-daemon)

További információ az engedélyekről és a hozzájárulásról:

> [!div class="nextstepaction"]
> [Engedélyek és hozzájárulás](v2-permissions-and-consent.md)

Ha többet szeretne tudni az ebben a forgatókönyvben szereplő hitelesítési folyamatról, olvassa el az Oauth 2.0 ügyfél hitelesítő adatainak folyamatát:

> [!div class="nextstepaction"]
> [Ügyfél hitelesítő adatai Oauth-folyamat](v2-oauth2-client-creds-grant-flow.md)
