---
title: Bejelentkezés hozzáadása a Microsofttal egy Microsoft identity platformPython webalkalmazáshoz | Azure
description: A Microsoft bejelentkezés megvalósítása Python-webappokon az OAuth2 használatával
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: c0220a226b2095a4ec54bc3469abee08b8b29f4c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536029"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Rövid útmutató: Bejelentkezés hozzáadása a Microsofttal egy Python-webalkalmazáshoz

Ebben a rövid útmutatóban megtudhatja, hogyan integrálhat egy Python-webalkalmazást a Microsoft identity platformmal. Az alkalmazás bejelentkezik egy felhasználóba, kap egy hozzáférési jogkivonatot a Microsoft Graph API hívásához, és kérést küld a Microsoft Graph API-hoz.

Miután befejezte az útmutatót, az alkalmazás elfogadja a személyes Microsoft-fiókok (beleértve a outlook.com, live.com és másokat) és az Azure Active Directoryt használó vállalatok vagy szervezetek munkahelyi vagy iskolai fiókjainak bejelentkezését. (Lásd: [Hogyan működik a minta](#how-the-sample-works) egy illusztrációhoz.)


## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához a következőkre lesz szükség:

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) vagy [Python 3+](https://www.python.org/downloads/release/python-364/)
- [Lombik,](http://flask.pocoo.org/) [lombik-session,](https://pythonhosted.org/Flask-Session/) [kérések](https://requests.kennethreitz.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
>
> A rövid útmutató elindításához két lehetőség van: expressz (1. lehetőség) és manuális (2. lehetőség)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése
>
> 1. Nyissa meg az [Azure Portal – Alkalmazásregisztrációk.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonQuickstartPage/sourceType/docs)
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az utasításokat az új alkalmazás letöltéséhez és automatikus konfigurálásához.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
>
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
> 1. Keresse meg a Microsoft identity platform ot a fejlesztőknek [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapon.
> 1. Válassza **az Új regisztráció lehetőséget.**
> 1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
>      - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `python-webapp`).
>      - A **Támogatott fióktípusok csoportban**válassza a Fiókok lehetőséget **bármely szervezeti címtárban és személyes Microsoft-fiókban.**
>      - Kattintson a **Register** (Regisztrálás) elemre.
>      - Az alkalmazás **áttekintése** lapon jegyezze fel az **alkalmazás (ügyfél) azonosító** értékét későbbi használatra.
> 1. Válassza ki a **hitelesítést** a menüből, majd adja meg a következő adatokat:
>    - Adja hozzá a **webes** platform konfigurációját. Hozzáadás `http://localhost:5000/getAToken` **átirányítási URI-kként**.
>    - Kattintson a **Mentés** gombra.
> 1. A bal oldali menüben válassza a **Tanúsítványok & titkoskulcsok at,** és kattintson az **Új ügyféltitok** elemre az **Ügyféltitkok** szakaszban:
>
>      - Írja be a kulcs leírását (a példányalkalmazás titkos kulcsa).
>      - Válassza ki a legfontosabb időtartama **Az 1 év .**
>      - Ha a **Hozzáadás gombra**kattint, a kulcsértéke megjelenik.
>      - Másolja a kulcs értékét. Erre később még szüksége lesz.
> 1. Az **API-engedélyek** szakasz kijelölése
>
>      - Kattintson az **Engedély hozzáadása** gombra, majd
>      - Annak ellenőrzése, hogy a **Microsoft API-k** lap ki van-e jelölve
>      - A *Gyakran használt Microsoft API-k* szakaszban kattintson a **Microsoft Graph**
>      - A **Delegált engedélyek** szakaszban győződjön meg arról, hogy a megfelelő engedélyek be vannak jelölve: **User.ReadBasic.All**. Szükség esetén használja a keresőmezőt.
>      - Az **Engedélyek hozzáadása** gomb kijelölése
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>1. lépés: Az alkalmazás konfigurálása az Azure Portalon
>
> A rövid útmutató működéséhez a kódmintához a következőket kell elvégeznie:
>
> 1. Válasz URL-címének hozzáadása a következőként: `http://localhost:5000/getAToken`.
> 1. Ügyféltitok létrehozása.
> 1. Adja hozzá a Microsoft Graph API User.ReadBasic.All delegált engedélyét.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Hajtsa végre ezeket a módosításokat nekem]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-aspnet-webapp/green-check.png) Az alkalmazása már konfigurálva van ezzel az attribútummal

#### <a name="step-2-download-your-project"></a>2. lépés: A projekt letöltése
> [!div renderon="docs"]
> [A kódminta letöltése](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Töltse le a projektet, és bontsa ki a zip fájlt egy helyi mappába, amely közelebb van a gyökérmappához - például **C:\Azure-Samples**
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [A kódminta letöltése](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-application"></a>3. lépés: Az alkalmazás konfigurálása
>
> 1. Csomagolja ki a zip-fájlt egy helyi mappába a gyökérmappa közelében (például: **C:\Azure-Samples**)
> 1. Ha integrált fejlesztői környezetet használ, nyissa meg a mintát a kedvenc IDE-ben (nem kötelező).
> 1. Nyissa meg a **app_config.py** fájlt, amely a gyökérmappában található, és cserélje ki a következő kódrészletre:
>
> ```python
> CLIENT_ID = "Enter_the_Application_Id_here"
> CLIENT_SECRET = "Enter_the_Client_Secret_Here"
> AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
> ```
> Az elemek magyarázata:
>
> - `Enter_the_Application_Id_here` – ez a regisztrált alkalmazás alkalmazásazonosítója.
> - `Enter_the_Client_Secret_Here`- az **ügyféltitok,** amelyet a **tanúsítványokban & titokban** hozott létre a regisztrált alkalmazáshoz.
> - `Enter_the_Tenant_Name_Here`- a regisztrált alkalmazás **címtár-azonosítóértéke.**

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>3. lépés: A kódminta futtatása

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>4. lépés: A kódminta futtatása

1. Telepítenie kell az MSAL Python könyvtárat, a Flask keretrendszert, a Lombik-munkameneteket a kiszolgálóoldali munkamenet-kezeléshez, és a kéréseket a pip használatával az alábbiak szerint:

    ```Shell
    pip install -r requirements.txt
    ```

2. Futtassa app.py héjból vagy parancssorból:

    ```Shell
    python app.py
    ```
   > [!IMPORTANT]
   > Ez a rövid útmutató alkalmazás egy ügyféltitkos kulcsot használ, hogy bizalmas ügyfélként azonosítsa magát. Mivel az ügyféltitkos kulcsot egyszerű szövegként adja hozzá a projektfájlokhoz, biztonsági okokból ajánlott az alkalmazás éles alkalmazásként való felhasználása előtt az ügyféltitok helyett tanúsítványt használni. A tanúsítványok használatával kapcsolatos további tudnivalókért olvassa el [az alábbi utasításokat.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials)

## <a name="more-information"></a>További információ

### <a name="how-the-sample-works"></a>A minta működése
![Megmutatja, hogyan működik az ez a rövid útmutató által létrehozott mintaalkalmazás](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>Első MSAL
Az MSAL a felhasználók bejelentkezéséhez és a Microsoft identity Platform által védett API-k eléréséhez használt jogkivonatok kéréséhez használt tár.
A Pip használatával msal python-t adhat hozzá az alkalmazáshoz.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>Az MSAL inicializálása
A hivatkozást hozzáadhatja az MSAL Pythonhoz, ha a következő kódot adja hozzá a fájl tetejéhez, ahol az MSAL-t fogja használni:

```Python
import msal
```

## <a name="next-steps"></a>További lépések

További információ a felhasználókat bejelentkező, webes API-kat megíró webalkalmazásokról:

> [!div class="nextstepaction"]
> [Eset: A felhasználókat bejelentkező webalkalmazások](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
