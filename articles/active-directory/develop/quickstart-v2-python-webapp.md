---
title: A Microsoft Identity platform Python-webalkalmazásának rövid útmutatója | Azure
description: Ismerje meg, hogyan valósítható meg a Microsoft bejelentkezés egy Python-webalkalmazásban a OAuth2 használatával
services: active-directory
documentationcenter: dev-center-name
author: abhidnya13
editor: ''
ms.assetid: 9551f0b5-04f2-44d7-87b5-756409180fe9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: 254a1fd8644015de33855e13f78ab122d28f1e35
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817110"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Gyors útmutató: bejelentkezés felvétele a Microsofttal egy Python-webalkalmazásba

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan integrálhat egy Python-webalkalmazást a Microsoft Identity platformmal. Az alkalmazás bejelentkezik egy felhasználóval, hozzáférési jogkivonatot kap a Microsoft Graph API meghívásához, és kérelmet küld a Microsoft Graph API-nak.

Az útmutató elvégzése után az alkalmazás elfogadja a személyes Microsoft-fiókok (például a outlook.com, a live.com és mások) és a munkahelyi vagy iskolai fiókok bejelentkezési adatait bármely olyan vállalattól vagy szervezettől, amely Azure Active Directoryt használ.

![Bemutatja, hogyan működik a rövid útmutatóban létrehozott minta alkalmazás](media/quickstart-v2-python-webapp/python-quickstart.svg)

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához a következőkre lesz szüksége:

- [Python 2.7 +](https://www.python.org/downloads/release/python-2713) vagy [Python 3 +](https://www.python.org/downloads/release/python-364/)
- [Lombik](http://flask.pocoo.org/), [lombik-munkamenet](https://pythonhosted.org/Flask-Session/), [kérelmek](https://requests.kennethreitz.org//en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
>
> A gyors üzembe helyezési alkalmazás elindításához két lehetőség közül választhat: Express (1. lehetőség) és manuális (2. lehetőség)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1\. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése
>
> 1. Nyissa meg a [Azure Portal-Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Válassza az **új regisztráció**lehetőséget.
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Az új alkalmazás letöltéséhez és automatikus konfigurálásához kövesse az utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2\. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása
>
> #### <a name="step-1-register-your-application"></a>1\. lépés: Alkalmazás regisztrálása
>
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
> 1. Navigáljon a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) oldalára.
> 1. Válassza az **új regisztráció**lehetőséget.
> 1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
>      - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `python-webapp`).
>      - A **támogatott fiókok típusai**területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
>      - Az **átirányítási URI** szakasz legördülő listájában válassza ki a webplatformot, majd állítsa be az értéket a **következőre** : `http://localhost:5000/getAToken`.
>      - Kattintson a **Register** (Regisztrálás) elemre. Az alkalmazás **áttekintése** lapon jegyezze fel az **alkalmazás (ügyfél) azonosítójának** értékét későbbi használatra.
> 1. A bal oldali menüben válassza a **tanúsítványok & Secrets** elemet, majd kattintson az **új ügyfél titkára** az **ügyfél titkai** szakaszban:
>
>      - Írja be a kulcs leírását (a példány-alkalmazás titkos kulcsa).
>      - Adja **meg az 1 év**kulcsának időtartamát.
>      - Ha a **Hozzáadás**gombra kattint, megjelenik a kulcs értéke.
>      - Másolja a kulcs értékét. Erre később még szüksége lesz.
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>1\. lépés: Az alkalmazás konfigurálása az Azure Portalon
>
> Ahhoz, hogy a rövid útmutatóhoz tartozó mintakód működjön, a következőket kell tennie:
>
> 1. Adjon hozzá egy válasz URL-címet `http://localhost:5000/getAToken` értékként.
> 1. Hozzon létre egy ügyfél titkot.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás alkalmazása]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-aspnet-webapp/green-check.png) Az alkalmazása már konfigurálva van ezzel az attribútummal

#### <a name="step-2-download-your-project"></a>2\. lépés: A projekt letöltése

[A mintakód letöltése](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

#### <a name="step-3-configure-the-application"></a>3\. lépés: az alkalmazás konfigurálása

1. Csomagolja ki a zip-fájlt egy helyi mappába a gyökérmappa közelében (például: **C:\Azure-Samples**)
1. Ha integrált fejlesztési környezetet használ, nyissa meg a mintát a kedvenc IDE (opcionális).
1. Nyissa meg a **app_config.** file fájlt, amely megtalálható a gyökérkönyvtárban, és cserélje le a következő kódrészletre:

```python
CLIENT_ID = "Enter_the_Application_Id_here"
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
```

> [!div renderon="docs"]
> Helyszín:
>
> - `Enter_the_Application_Id_here` – ez a regisztrált alkalmazás alkalmazásazonosítója.
> - `Enter_the_Client_Secret_Here` – a **tanúsítványok & Secrets** szolgáltatásban a regisztrált alkalmazáshoz létrehozott **titkos ügyfél** .
> - `Enter_the_Tenant_Name_Here` – a regisztrált alkalmazás **címtár-(bérlői) azonosítójának** értéke.

#### <a name="step-4-run-the-code-sample"></a>4\. lépés: a kód mintájának futtatása

1. Telepítenie kell a MSAL Python Library, a lombik Framework, a többtényezős munkamenet-kezelés és a pip-t használó kérelmeket a következő módon:

   ```Shell
   pip install -r requirements.txt
   ```

2. App.py futtatása a rendszerhéjból vagy parancssorból:

   ```Shell
   python app.py
   ```
   > [!IMPORTANT]
   > Ez a rövid útmutató alkalmazás egy ügyfél titkos kulcsát használja, amely bizalmas ügyfélként azonosítja magát. Mivel az ügyfél titkos kulcsát egyszerű szövegként adja hozzá a Project-fájlokhoz, biztonsági okokból javasolt a tanúsítvány használata az ügyfél titkos kulcsa helyett, mielőtt az alkalmazást éles alkalmazásként venné fontolóra. A tanúsítványok használatáról a következő [utasításokban](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-certificate-credentials)talál további információt.

   ## <a name="more-information"></a>További információ

   ### <a name="getting-msal"></a>MSAL beolvasása
   A MSAL az a könyvtár, amellyel a felhasználók bejelentkezhetnek, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kérhetnek.
   A pip használatával hozzáadhat MSAL Pythont az alkalmazáshoz.

   ```Shell
   pip install msal
   ```

   ### <a name="msal-initialization"></a>Az MSAL inicializálása
   A MSAL Pythonra mutató hivatkozást úgy adhatja hozzá, hogy hozzáadja a következő kódot a fájl elejéhez, ahol a MSAL fogja használni:

   ```Python
   import msal
   ```

## <a name="next-steps"></a>Következő lépések

További információ a felhasználókat bejelentkező webalkalmazásokról, majd a webes API-k meghívásáról:

> [!div class="nextstepaction"]
> [Forgatókönyv: felhasználói bejelentkezésre használt webalkalmazások](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
