---
title: A Microsoft Identity platform Java-webalkalmazásának rövid útmutatója | Azure
description: Ismerje meg, hogyan implementálhatja a Microsoft bejelentkezést egy Java-webalkalmazásban az OpenID Connect használatával
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: 10ae1c76d48c1cedbb915fec66177ac3612feea0
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115220"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Gyors útmutató: bejelentkezés felvétele a Microsofttal egy Java-webalkalmazásba

Ebből a rövid útmutatóból megtudhatja, hogyan integrálhat egy Java-webalkalmazást a Microsoft Identity platformmal. Az alkalmazás bejelentkezik egy felhasználóval, hozzáférési jogkivonatot kap a Microsoft Graph API meghívásához, és kérelmet küld a Microsoft Graph API-nak.

A rövid útmutató elvégzése után az alkalmazás elfogadja a személyes Microsoft-fiókok (például a outlook.com, a live.com és mások) és a munkahelyi vagy iskolai fiókok bejelentkezési adatait bármely olyan vállalattól vagy szervezettől, amely Azure Active Directoryt használ. (Lásd: [Hogyan működik a minta](#how-the-sample-works) egy ábrán.)

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához a következőkre lesz szüksége:

- A [Java Development Kit (JDK)](https://openjdk.java.net/) 8 vagy újabb, valamint a [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> A gyors üzembe helyezési alkalmazás elindításához két lehetőség közül választhat: Express (1. lehetőség) vagy manuális (2. lehetőség)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése
>
> 1. Lépjen a [Azure Portal-Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs) gyors üzembe helyezési élményhez.
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Az automatikusan konfigurált alkalmazás kódjának letöltéséhez kövesse a portál gyors üzembe helyezési felületének utasításait.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
>
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs adatainak manuális hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
>
> 1. Navigáljon a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) oldalára.
> 1. Válassza az **új regisztráció**lehetőséget.
> 1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
>    - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `java-webapp`).
>    - Válassza a **Regisztráció** lehetőséget.
> 1. Az **Áttekintés** oldalon keresse meg az alkalmazás **(ügyfél) azonosítóját** és a **könyvtár (bérlő) azonosító** értékeit. Másolja ezeket az értékeket később.
> 1. Válassza ki a **hitelesítést** a menüben, majd adja hozzá a következő adatokat:
>    - Adja hozzá **a** webplatform-konfigurációt.  Adja hozzá ezeket `https://localhost:8443/msal4jsample/secure/aad` és `https://localhost:8443/msal4jsample/graph/me` az **átirányítási URI-ket**.
>    - Kattintson a **Mentés** gombra.
> 1. Válassza ki a **tanúsítványokat & a titkokat** a menüben, majd az **ügyfél titkai** szakaszban kattintson az **új ügyfél titka**lehetőségre:
>
>    - Írja be a kulcs leírását (például az alkalmazás titkos kulcsaként).
>    - Válassza ki a kulcs időtartamát **1 év**múlva.
>    - A kulcs értéke a **Hozzáadás**gombra kattintva fog megjelenni.
>    - Másolja később a kulcs értékét. Ez a kulcs nem jelenik meg újra, és semmilyen más módon nem kérhető le, ezért jegyezze fel, amint a Azure Portal látható.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: az alkalmazás konfigurálása a Azure Portalban
>
> Ahhoz, hogy a rövid útmutatóhoz tartozó mintakód működjön, a következőket kell tennie:
>
> 1. Válasz URL-címek hozzáadása a `https://localhost:8443/msal4jsample/secure/aad` és a`https://localhost:8443/msal4jsample/graph/me`
> 1. Hozzon létre egy ügyfél titkot.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítások elvégzése]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-aspnet-webapp/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-code-sample"></a>2. lépés: a mintakód letöltése
> [!div renderon="docs"]
> [A mintakód letöltése](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Töltse le a projektet, és bontsa ki a zip-fájlt egy helyi mappába a gyökérkönyvtárhoz közelebb – például **C:\Azure-Samples**
>
> Ha a HTTPS-t a localhost használatával szeretné használni, töltse ki a Server. SSL. Key tulajdonságokat. Önaláírt tanúsítvány létrehozásához használja a (JRE részét képező) eszköz segédprogramját.
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Helyezze a generált rendszertároló fájlt a "Resources" (erőforrások) mappába.

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [A mintakód letöltése](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>3. lépés: a mintakód konfigurálása
> 1. Csomagolja ki a zip-fájlt egy helyi mappába.
> 1. Ha integrált fejlesztési környezetet használ, nyissa meg a mintát a kedvenc IDE (opcionális).
> 1. Nyissa meg az Application. properties fájlt, amely a src/Main/Resources/mappában található, és cserélje le a *HRE. clientId*, *HRE. Authority* és *HRE. secretKey* mezők értékét az alkalmazás- **azonosító**, a **bérlői azonosító** és az **ügyfél titkos kulcsának** megfelelő értékekre az alábbiak szerint:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
> Ebben a példában:
>
> - `Enter_the_Application_Id_here` – ez a regisztrált alkalmazás alkalmazásazonosítója.
> - `Enter_the_Client_Secret_Here`– a **tanúsítványokban létrehozott &** **titkos kulcs** a regisztrált alkalmazáshoz.
> - `Enter_the_Tenant_Info_Here`– a regisztrált alkalmazás **címtár-(bérlői) azonosítójának** értéke.
> 1. Ha a HTTPS-t a localhost használatával szeretné használni, töltse ki a Server. SSL. Key tulajdonságokat. Önaláírt tanúsítvány létrehozásához használja a (JRE részét képező) eszköz segédprogramját.
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Helyezze a generált rendszertároló fájlt a "Resources" (erőforrások) mappába.


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>3. lépés: a kód mintájának futtatása
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>4. lépés: a kód mintájának futtatása

A projekt futtatásához a következők közül választhat:

Futtassa közvetlenül az IDE-ből a beágyazott rugós rendszerindítási kiszolgáló használatával, vagy csomagolja ki egy háborús fájlba a [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) használatával, és telepítse azt egy J2EE-tároló megoldásba, például az [Apache Tomcat](http://tomcat.apache.org/)-be.

##### <a name="running-from-ide"></a>Futtatás IDE-ből

Ha IDE-ből futtatja a webalkalmazást, kattintson a Futtatás gombra, és keresse meg a projekt kezdőlapját. Ehhez a mintához a szabványos Kezdőlap URL-címehttps://localhost:8443

1. A kezdőlapon kattintson a **Bejelentkezés** gombra a Azure Active Directory átirányításához, és kérje meg a felhasználót a hitelesítő adataik megadására.

1. A felhasználó hitelesítése után a rendszer átirányítja a következőre: *https://localhost:8443/msal4jsample/secure/aad* . Most bejelentkeznek, és az oldalon megjelennek a bejelentkezett fiókra vonatkozó információk. A minta felhasználói felület a következő gombokkal rendelkezik:
    - *Kijelentkezés*: aláírja az aktuális felhasználót az alkalmazásból, és átirányítja őket a kezdőlapra.
    - *Felhasználói információk megjelenítése*: jogkivonatot kér Microsoft Graph és meghívja Microsoft Graph a tokent tartalmazó kérelemmel, amely a bejelentkezett felhasználó alapvető információit adja vissza.

##### <a name="running-from-tomcat"></a>Futtatás tomcat-ből

Ha a webes mintát a Tomcat szolgáltatásban szeretné üzembe helyezni, néhány módosítást is el kell végeznie a forráskódban.

1. Nyissa meg az MS-Identity-Java-WebApp/pom.xml
    - A `<name>msal-web-sample</name>` Hozzáadás alatt`<packaging>war</packaging>`

2. Nyissa meg a MS-Identity-Java-WebApp/src/Main/Java/com. microsoft. Azure. msalwebsample/MsalWebSampleApplication

    - Törölje az összes forráskódot, és cserélje le a következőre:

   ```Java
    package com.microsoft.azure.msalwebsample;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.boot.builder.SpringApplicationBuilder;
    import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

    @SpringBootApplication
    public class MsalWebSampleApplication extends SpringBootServletInitializer {

     public static void main(String[] args) {
      SpringApplication.run(MsalWebSampleApplication.class, args);
     }

     @Override
     protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
      return builder.sources(MsalWebSampleApplication.class);
     }
    }
   ```

3.   A Tomcat alapértelmezett HTTP-portja 8080, de a 8443-es porton keresztül HTTPS-kapcsolat szükséges. A konfigurálásához:
        - Ugrás a Tomcat/conf/server.xml
        - Keresse meg a `<connector>` címkét, és cserélje le a meglévő összekötőt a következőre:
        ```
        <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
        ``` 
       
4. Nyisson meg egy parancssort, lépjen a minta gyökérkönyvtárára (ahol a pom.xml fájl található), és futtassa a parancsot a `mvn package` projekt felépítéséhez.
    - Ekkor létrejön egy `msal-web-sample-0.1.0.war` fájl a/Targets könyvtárban.
    - A fájl átnevezése`msal4jsample.war`
    - A War-fájlt a Tomcat vagy bármely más J2EE-tároló megoldás használatával helyezheti üzembe.
        - A telepítéséhez másolja a msal4jsample. War fájlt a `/webapps/` tomcat-telepítés könyvtárába, majd indítsa el a Tomcat-kiszolgálót.

5. Üzembe helyezés után lépjen a https://localhost:8443/msal4jsample böngészőben


> [!IMPORTANT]
> Ez a rövid útmutató alkalmazás egy ügyfél titkos kulcsát használja, amely bizalmas ügyfélként azonosítja magát. Mivel a rendszer az ügyfél titkos kulcsát egyszerű szövegként adja hozzá a projektfájlok számára, biztonsági okokból javasolt a tanúsítvány használata az ügyfél titkos kulcsa helyett, mielőtt az alkalmazást éles alkalmazásként venné fontolóra. A tanúsítványok használatáról a [tanúsítvány hitelesítő adatai az alkalmazás hitelesítéséhez](./active-directory-certificate-credentials.md)című témakörben olvashat bővebben.

## <a name="more-information"></a>További információ

### <a name="how-the-sample-works"></a>A minta működése
![Bemutatja, hogyan működik a rövid útmutatóban létrehozott minta alkalmazás](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="getting-msal"></a>MSAL beolvasása

A MSAL for Java (MSAL4J) a Microsoft Identity platform által védett API-hoz való hozzáféréshez használt Java-könyvtár.

Vegyen fel MSAL4J az alkalmazásba a Maven vagy a Gradle használatával a függőségek kezeléséhez azáltal, hogy az alábbi módosításokat hajtja végre az alkalmazás pom.xml (Maven) vagy a Build. Gradle (Gradle) fájlon.

pom.xml:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

A Build. gradle:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

Vegyen fel egy hivatkozást a Java-MSAL a következő kód hozzáadásával a fájl elejére, ahol a MSAL4J fogja használni:

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Következő lépések

További információ az engedélyekről és a beleegyezik:

> [!div class="nextstepaction"]
> [Engedélyek és beleegyezik](./v2-permissions-and-consent.md)

Ha többet szeretne megtudni a forgatókönyv hitelesítési folyamatáról, tekintse meg a OAuth 2,0 engedélyezési kód folyamatát:

> [!div class="nextstepaction"]
> [Engedélyezési kód OAuth folyamata](./v2-oauth2-auth-code-flow.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]