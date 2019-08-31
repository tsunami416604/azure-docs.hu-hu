---
title: A Microsoft Identity platform Java-webalkalmazásának rövid útmutatója | Azure
description: Ismerje meg, hogyan implementálhatja a Microsoft bejelentkezést egy Java-webalkalmazásban az OpenID Connect használatával
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/11/2019
ms.author: sagonzal
ms.custom: aaddev
ms.openlocfilehash: fa3ad71dcc7738169f78f4dab94bd9bdf4d4cc89
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182969"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Gyors útmutató: Bejelentkezés felvétele a Microsofttal egy Java-webalkalmazásba

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan integrálhat egy Java-webalkalmazást a Microsoft Identity platformmal. Az alkalmazás bejelentkezik egy felhasználóval, hozzáférési jogkivonatot kap a Microsoft Graph API meghívásához, és kérelmet küld a Microsoft Graph API-nak. 

Az útmutató elvégzése után az alkalmazás elfogadja a személyes Microsoft-fiókok (például a outlook.com, a live.com és mások) és a munkahelyi vagy iskolai fiókok bejelentkezési adatait bármely olyan vállalattól vagy szervezettől, amely Azure Active Directoryt használ.

![Bemutatja, hogyan működik a rövid útmutatóban létrehozott minta alkalmazás](media/quickstart-v2-java-webapp/java-quickstart.svg)

> ## <a name="prerequisites"></a>Előfeltételek
> A minta futtatásához a következőkre lesz szüksége: 
> - A Java Development Kit (JDK) 8 vagy újabb, illetve Maven.
> - Egy Azure Active Directory (Azure AD) bérlő. Az Azure AD-bérlő beszerzésével kapcsolatos további információkért lásd: [Azure ad-bérlő beszerzése.](https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-create-new-tenant)
>
> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> A gyors üzembe helyezési alkalmazás elindításához két lehetőség közül választhat: Express (1. lehetőség) vagy manuális (2. lehetőség)
> 
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1\. lehetőség: Regisztráljon és automatikusan konfigurálja az alkalmazást, majd töltse le a kód mintáját
>
> 1. Nyissa meg a [Azure Portal-Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Az új alkalmazás letöltéséhez és automatikus konfigurálásához kövesse az utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2\. lehetőség: Alkalmazás-és kód-minta regisztrálása és manuális konfigurálása
> 
>
> #### <a name="step-1-download-the-code-sample"></a>1\. lépés: A mintakód letöltése
> 
> - [A mintakód letöltése](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
>
> #### <a name="step-2-open-applicationproperties"></a>2\. lépés: Alkalmazás megnyitása. properties
>
> 1. Bontsa ki a zip-fájlt egy helyi mappába.
> 1. Választható Ha integrált fejlesztési környezetet használ, nyissa meg a mintát a kedvenc IDE-ben.
> 1. Nyissa meg az *Application. properties* fájlt. A következő lépésben be kell `aad.clientId`szúrnia az `aad.secretKey` alkalmazáshoz tartozó `aad.authority`értékeket, és ha regisztrálja az alkalmazást.


> #### <a name="step-3-register-your-application"></a>3\. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és a megoldáshoz az alkalmazás regisztrációs adatainak manuális hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
> 1. Navigáljon a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) oldalára.
> 1. Válassza az **új regisztráció**lehetőséget.
> 1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
>    - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `java-webapp`).
>    - Hagyja üresen az **átirányítási URI** -t, majd válassza a **regisztráció**lehetőséget.
> 1. Az alkalmazás **alkalmazás-(ügyfél-) azonosító** értékének megkeresése. Frissítse a értéket `Enter_the_Application_Id_here` az *Application. properties* fájlban.
> 1. Az alkalmazás **címtár-(bérlői) azonosító** értékének megkeresése. Frissítse a értéket `Enter_the_Tenant_Info_Here` az *Application. properties* fájlban. 
> 1. Válassza a **hitelesítés** menüt, majd adja hozzá a következő adatokat:
>    - Az **átirányítási URI**- `http://localhost:8080/msal4jsamples/secure/aad` k `https://localhost:8080/msal4jsamples/graph/users`területen adjon hozzá és.
>    - Kattintson a **Mentés** gombra.
> 1. A bal oldali menüben válassza a **tanúsítványok & Secrets** elemet, majd kattintson az **új ügyfél titkára** az **ügyfél titkai** szakaszban:
>     
>    - Írja be a kulcs leírását (a példány-alkalmazás titkos kulcsa).
>    - Adja **meg az 1 év**kulcsának időtartamát.
>    - Ha a **Hozzáadás**gombra kattint, megjelenik a kulcs értéke. 
>    - Másolja a kulcs értékét. Nyissa meg a korábban letöltött *Application. properties* fájlt, és frissítse a értékét `Enter_the_Client_Secret_Here` a kulcs értékével. 
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1\. lépés: Az alkalmazás konfigurálása a Azure Portalban
> Ahhoz, hogy a rövid útmutatóhoz tartozó mintakód működjön, a következőket kell tennie:
> 1. Adja hozzá a válasz `http://localhost:8080/msal4jsamples/secure/aad` URL `https://localhost:8080/msal4jsamples/graph/users`-címeit és a-t.
> 1. Hozzon létre egy ügyfél titkot.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás alkalmazása]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-aspnet-webapp/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.
> 
> #### <a name="step-2-download-the-code-sample"></a>2\. lépés: A mintakód letöltése
> 
> - [A mintakód letöltése](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
> 
> #### <a name="step-3-configure-the-code-sample"></a>3\. lépés: A mintakód konfigurálása 
> 
> 1. Bontsa ki a zip-fájlt egy helyi mappába.
> 1. Ha integrált fejlesztési környezetet használ, nyissa meg a mintát a kedvenc IDE (opcionális).
> 1. Nyissa meg az **Application. properties** fájlt, amely megtalálható az *src/Main/Resources/* .
> 1. Az alkalmazás tulajdonságainak cseréje
>   1. Megkeresheti `aad.clientId` és frissítheti `Enter_the_Application_Id_here` a regisztrált alkalmazás **alkalmazás-(ügyfél-) azonosítójának** értékét. 
>   1. A `aad.authority` regisztrált alkalmazás **címtár-(bérlői) azonosító** értékének megkeresése és frissítése `Enter_the_Tenant_Name_Here` .
>   1. Megkeresheti `aad.secretKey` és frissítheti `Enter_the_Client_Secret_Here` a tanúsítványokban létrehozott **&** **titkos** kulcs értékét a regisztrált alkalmazáshoz.

#### <a name="step-4-run-the-code-sample"></a>4\. lépés: A kód mintájának futtatása
1. Futtassa a kód mintát, és nyisson meg egy böngészőt, *http://localhost:8080* és navigáljon a következőhöz:.
1. A kezdőlapon egy **bejelentkezési** gomb található. Kattintson a **Bejelentkezés** gombra a Azure Active Directory átirányításához. A rendszer kérni fogja a felhasználót a hitelesítő adataik megadására.  
1. A Azure Active Directory sikeres hitelesítését követően a rendszer átirányítja a *http://localhost:8080/msal4jsamples/secure/aad* következőre:. Hivatalosan be vannak jelentkezve az alkalmazásba, és az oldalon meg kell jeleníteni a bejelentkezett fiókra vonatkozó információkat. Emellett a következő gombokat is tartalmazza: 
    - *Kijelentkezés*: Kiírja az aktuális felhasználót az alkalmazásból, és átirányítja őket a kezdőlapra.
    - *Felhasználók megjelenítése*: A lekéri a Microsoft Graph jogkivonatát, majd meghívja a kéréshez csatolt Microsoft Graph a bérlő összes felhasználójának beszerzéséhez.


## <a name="more-information"></a>További információ

### <a name="getting-msal"></a>MSAL beolvasása
A MSAL4J az a könyvtár, amellyel a felhasználók bejelentkezhetnek, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kérhetnek. Az alkalmazáshoz a Maven vagy a Gradle használatával adhat hozzá MSAL4J a függőségek kezeléséhez azáltal, hogy a következő változtatásokat hajtja végre a Pom. XML vagy a Build. Gradle fájlban az alkalmazásban. 

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>0.5.0-preview</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '0.5.0-preview'
```


### <a name="msal-initialization"></a>Msal inicializálása
A MSAL4J mutató hivatkozás hozzáadásához adja hozzá a következő kódot a fájl elejéhez, ahol a MSAL4J fogja használni: 

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>További lépések

További információ az engedélyekről és a beleegyezik:

> [!div class="nextstepaction"]
> [Engedélyek és beleegyezik](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-permissions-and-consent)

Ha többet szeretne megtudni a forgatókönyv hitelesítési folyamatáról, tekintse meg a OAuth 2,0 engedélyezési kód folyamatát:

> [!div class="nextstepaction"]
> [Engedélyezési kód OAuth folyamata](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-auth-code-flow)

Segítsen nekünk a Microsoft Identity platform fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdéses felmérés végrehajtásával.

> [!div class="nextstepaction"]
> [Microsoft Identity platform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
