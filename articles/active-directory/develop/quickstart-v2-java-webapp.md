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
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java
ms.openlocfilehash: 0046443bef0e71215157dfe89aaae45b2a91c330
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200276"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Gyors útmutató: bejelentkezés felvétele a Microsofttal egy Java-webalkalmazásba

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan integrálhat egy Java-webalkalmazást a Microsoft Identity platformmal. Az alkalmazás bejelentkezik egy felhasználóval, hozzáférési jogkivonatot kap a Microsoft Graph API meghívásához, és kérelmet küld a Microsoft Graph API-nak.

A rövid útmutató elvégzése után az alkalmazás elfogadja a személyes Microsoft-fiókok (például a outlook.com, a live.com és mások) és a munkahelyi vagy iskolai fiókok bejelentkezési adatait bármely olyan vállalattól vagy szervezettől, amely Azure Active Directoryt használ.

![Bemutatja, hogyan működik a rövid útmutatóban létrehozott minta alkalmazás](media/quickstart-v2-java-webapp/java-quickstart.svg)

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához a következőkre lesz szüksége:

- A [Java Development Kit (JDK)](https://openjdk.java.net/) 8 vagy újabb, valamint a [Maven](https://maven.apache.org/).
- Egy Azure Active Directory (Azure AD) bérlő. Az Azure AD-bérlő beszerzésével kapcsolatos további információkért lásd: [Azure ad-bérlő beszerzése](https://azure.microsoft.com/documentation/articles/active-directory-howto-tenant/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> A gyors üzembe helyezési alkalmazás elindításához két lehetőség közül választhat: Express (1. lehetőség) vagy manuális (2. lehetőség)
> 
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1\. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése
> 
> 1. Nyissa meg a [Azure Portal-Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Az új alkalmazás letöltéséhez és automatikus konfigurálásához kövesse az utasításokat.
> 
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2\. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása
> 
> #### <a name="step-1-register-your-application"></a>1\. lépés: Alkalmazás regisztrálása
> 
> Az alkalmazás regisztrálásához és a megoldáshoz az alkalmazás regisztrációs adatainak manuális hozzáadásához kövesse az alábbi lépéseket:
> 
> 1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
> 
> 1. Navigáljon a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](/azure/active-directory/develop/) oldalára.
> 1. Válassza az **új regisztráció**lehetőséget.
> 1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
>    - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `java-webapp`).
>    - Hagyja üresen az **átirányítási URI** -t, majd válassza a **regisztráció**lehetőséget.
> 1. Az **Áttekintés** oldalon keresse meg az alkalmazás **(ügyfél) azonosítóját** és a **könyvtár (bérlő) azonosító** értékeit. Másolja ezeket az értékeket később.
> 1. Válassza ki a **hitelesítést** a menüben, majd adja hozzá a következő adatokat:
>    - Az **átirányítási URI**-k területen adja hozzá a `http://localhost:8080/msal4jsamples/secure/aad` és `http://localhost:8080/msal4jsamples/graph/me`.
>    - A **Speciális beállítások**területen adja hozzá `https://localhost:8080/msal4jsample/sign-out` a **KIJELENTKEZÉSI URL-címhez**.
>    - Kattintson a **Mentés** gombra.
> 1. Válassza ki a **tanúsítványokat & a titkokat** a menüben, majd az **ügyfél titkai** szakaszban kattintson az **új ügyfél titka**lehetőségre:
> 
>    - Írja be a kulcs leírását (például az alkalmazás titkos kulcsaként).
>    - Válassza ki a kulcs időtartamát **1 év**múlva.
>    - A kulcs értéke a **Hozzáadás**gombra kattintva fog megjelenni.
>    - Másolja később a kulcs értékét. Ez a kulcs nem jelenik meg újra, és semmilyen más módon nem kérhető le, ezért jegyezze fel, amint a Azure Portal látható.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1\. lépés: az alkalmazás konfigurálása a Azure Portalban
> 
> Ahhoz, hogy a rövid útmutatóhoz tartozó mintakód működjön, a következőket kell tennie:
> 
> 1. Adja hozzá a válasz URL-címeket `http://localhost:8080/msal4jsamples/secure/aad` és `http://localhost:8080/msal4jsamples/graph/me`ként.
> 1. Hozzon létre egy ügyfél titkot.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítások elvégzése]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-aspnet-webapp/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-code-sample"></a>2\. lépés: a mintakód letöltése

 [A mintakód letöltése](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

#### <a name="step-3-configure-the-code-sample"></a>3\. lépés: a mintakód konfigurálása

 1. Bontsa ki a zip-fájlt egy helyi mappába.
 1. Ha integrált fejlesztési környezetet használ, nyissa meg a mintát a kedvenc IDE (opcionális).

 1. Nyissa meg az Application. properties fájlt, amely a src/Main/Resources/mappában található, és cserélje le az *HRE. clientId*, *HRE. Authority* és *HRE. secretKey* mezők értékét az alkalmazás- **azonosító**megfelelő értékeivel. A **bérlő azonosítója** és az **ügyfél titkos kulcsa** a következő:

    ```file
    aad.clientId=Enter_the_Application_Id_here
    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
    aad.secretKey=Enter_the_Client_Secret_Here
    aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
    aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
    ```

> [!div renderon="docs"]
> Helyszín:
>
> - `Enter_the_Application_Id_here` – ez a regisztrált alkalmazás alkalmazásazonosítója.
> - `Enter_the_Client_Secret_Here` – a **tanúsítványok & Secrets** szolgáltatásban a regisztrált alkalmazáshoz létrehozott **titkos ügyfél** .
> - `Enter_the_Tenant_Info_Here` – a regisztrált alkalmazás **címtár-(bérlői) azonosítójának** értéke.

#### <a name="step-4-run-the-code-sample"></a>4\. lépés: a kód mintájának futtatása

A projekt futtatásához a következők közül választhat:

Futtassa közvetlenül az IDE-ből a beágyazott rugós rendszerindítási kiszolgáló használatával, vagy csomagolja ki egy háborús fájlba a [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) használatával, és telepítse azt egy J2EE-tároló megoldásba, például az [Apache Tomcat](http://tomcat.apache.org/)-be.

##### <a name="running-from-ide"></a>Futtatás IDE-ből

Ha IDE-ből futtatja a webalkalmazást, kattintson a Futtatás gombra, és keresse meg a projekt kezdőlapját. Ehhez a mintához a szabványos Kezdőlap URL-címe http://localhost:8080

1. A kezdőlapon kattintson a **Bejelentkezés** gombra a Azure Active Directory átirányításához, és kérje meg a felhasználót a hitelesítő adataik megadására.

1. A felhasználó hitelesítése után a rendszer átirányítja *http://localhost:8080/msal4jsamples/secure/aad* . Most bejelentkeznek, és az oldalon megjelennek a bejelentkezett fiókra vonatkozó információk. A minta felhasználói felület a következő gombokkal rendelkezik:
    - *Kijelentkezés*: aláírja az aktuális felhasználót az alkalmazásból, és átirányítja őket a kezdőlapra.
    - *Felhasználói információk megjelenítése*: jogkivonatot kér Microsoft Graph és meghívja Microsoft Graph a tokent tartalmazó kérelemmel, amely a bejelentkezett felhasználó alapvető információit adja vissza.

> [!IMPORTANT]
> Ez a rövid útmutató alkalmazás egy ügyfél titkos kulcsát használja, amely bizalmas ügyfélként azonosítja magát. Mivel a rendszer az ügyfél titkos kulcsát egyszerű szövegként adja hozzá a projektfájlok számára, biztonsági okokból javasolt a tanúsítvány használata az ügyfél titkos kulcsa helyett, mielőtt az alkalmazást éles alkalmazásként venné fontolóra. A tanúsítványok használatáról a [tanúsítvány hitelesítő adatai az alkalmazás hitelesítéséhez](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials)című témakörben olvashat bővebben.

## <a name="more-information"></a>További információ

### <a name="getting-msal"></a>MSAL beolvasása

A MSAL4J a Microsoft Identity platform által védett API-k eléréséhez használt Java-könyvtár.

Vegyen fel MSAL4J az alkalmazásba a Maven vagy a Gradle használatával a függőségek kezeléséhez azáltal, hogy az alkalmazás Pom. XML (Maven) vagy Build. Gradle (Gradle) fájljában módosítja a következő módosításokat.

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>0.6.0-preview</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '0.6.0-preview'
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

Adjon hozzá egy hivatkozást a MSAL4J, ehhez adja hozzá a következő kódot a fájl elejéhez, ahol a MSAL4J fogja használni:

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Következő lépések

További információ az engedélyekről és a beleegyezik:

> [!div class="nextstepaction"]
> [Engedélyek és beleegyezik](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

Ha többet szeretne megtudni a forgatókönyv hitelesítési folyamatáról, tekintse meg a OAuth 2,0 engedélyezési kód folyamatát:

> [!div class="nextstepaction"]
> [Engedélyezési kód OAuth folyamata](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)

Segítsen nekünk a Microsoft Identity platform fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdéses felmérés végrehajtásával.

> [!div class="nextstepaction"]
> [Microsoft Identity platform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
