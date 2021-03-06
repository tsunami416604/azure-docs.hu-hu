---
title: 'Gyors útmutató: bejelentkezés felvétele a Microsofttal Java-webalkalmazásba | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan implementálhatja a Microsoft-bejelentkezést egy Java-webalkalmazásban az OpenID Connect használatával.
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
ms.openlocfilehash: e188c00840a4d043e94f94f9db565e2d4e06aaba
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031062"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Gyors útmutató: bejelentkezés felvétele a Microsofttal egy Java-webalkalmazásba

Ebben a rövid útmutatóban egy olyan kódrészletet tölt le és futtat, amely bemutatja, hogyan jelentkezhet be a Java-webalkalmazás a felhasználókba, és hogyan hívhatja meg a Microsoft Graph API-t. Bármely Azure Active Directory (Azure AD-) szervezet felhasználói bejelentkezhetnek az alkalmazásba.

 Nézze meg, [Hogyan működik a minta](#how-the-sample-works) egy ábrán.

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
> 1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
> 1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
> 1. Keresse meg és válassza ki az **Azure Active Directoryt**.
> 1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
> 1. Adja meg az alkalmazás **nevét** , például: `java-webapp` . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
> 1. Válassza a **Regisztráció** lehetőséget.
> 1. Az **Áttekintés** lapon jegyezze fel az **alkalmazás (ügyfél) azonosítóját** és a **címtár (bérlő) azonosítóját** későbbi használatra.
> 1. A **kezelés** területen válassza a **hitelesítés** lehetőséget.
> 1. Válassza **a platform**  >  **web** hozzáadása lehetőséget.
> 1. Az **átirányítási URI** -k szakaszban adja hozzá a következőt: `https://localhost:8443/msal4jsample/secure/aad` .
> 1. Válassza a **Konfigurálás** lehetőséget.
> 1. A **webes** szakaszban adja hozzá `https://localhost:8443/msal4jsample/graph/me` második **átirányítási URI**-ként.
> 1. A **kezelés** területen válassza a **tanúsítványok & Secrets** elemet. Az **ügyfél titkai** szakaszban válassza az **új ügyfél titka** elemet.
> 1. Írja be a kulcs leírását (például az alkalmazás titkos kódjához), hagyja meg az alapértelmezett lejáratot, és válassza a **Hozzáadás** lehetőséget.
> 1. Jegyezze fel az **ügyfél titkos kulcsának** **értékét** későbbi használatra.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: az alkalmazás konfigurálása a Azure Portalban
>
> Ahhoz, hogy a rövid útmutatóhoz tartozó mintakód működjön, a következőket kell tennie:
>
> 1. Válasz URL-címek hozzáadása a `https://localhost:8443/msal4jsample/secure/aad` és a `https://localhost:8443/msal4jsample/graph/me`
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

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
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
> Kimenet:
>
> - `Enter_the_Application_Id_here` – ez a regisztrált alkalmazás alkalmazásazonosítója.
> - `Enter_the_Client_Secret_Here`– a **tanúsítványokban létrehozott &** **titkos kulcs** a regisztrált alkalmazáshoz.
> - `Enter_the_Tenant_Info_Here` – a regisztrált alkalmazás **címtár-(bérlői) azonosítójának** értéke.
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

Ha IDE-ből futtatja a webalkalmazást, válassza a Futtatás lehetőséget, majd navigáljon a projekt kezdőlapján. Ehhez a mintához a standard Kezdőlap URL-címe: https://localhost:8443 .

1. A kezdőlapon kattintson a **Bejelentkezés** gombra a Azure Active Directory átirányításához, és kérje meg a felhasználót a hitelesítő adataik megadására.

1. A felhasználó hitelesítése után a rendszer átirányítja a következőre: *https://localhost:8443/msal4jsample/secure/aad* . Most bejelentkeznek, és az oldalon megjelennek a bejelentkezett fiókra vonatkozó információk. A minta felhasználói felület a következő gombokkal rendelkezik:
    - *Kijelentkezés*: aláírja az aktuális felhasználót az alkalmazásból, és átirányítja őket a kezdőlapra.
    - *Felhasználói információk megjelenítése*: jogkivonatot kér Microsoft Graph és meghívja Microsoft Graph a tokent tartalmazó kérelemmel, amely a bejelentkezett felhasználó alapvető információit adja vissza.

##### <a name="running-from-tomcat"></a>Futtatás tomcat-ből

Ha a webes mintát a Tomcat szolgáltatásban szeretné üzembe helyezni, néhány módosítást is el kell végeznie a forráskódban.

1. Nyissa meg az MS-Identity-Java-WebApp/pom.xml
    - A `<name>msal-web-sample</name>` Hozzáadás alatt `<packaging>war</packaging>`

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

        ```xml
        <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
        ```

4. Nyisson meg egy parancssort, lépjen a minta gyökérkönyvtárára (ahol a pom.xml fájl található), és futtassa a parancsot a `mvn package` projekt felépítéséhez.
    - Ekkor létrejön egy `msal-web-sample-0.1.0.war` fájl a/Targets könyvtárban.
    - A fájl átnevezése `msal4jsample.war`
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

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

A Microsoft Identity platform felhasználóit bejelentkező webalkalmazások fejlesztésének alaposabb megvitatására a többrészes forgatókönyv sorozat:

> [!div class="nextstepaction"]
> [Forgatókönyv: a felhasználók által bejelentkezett webalkalmazás](scenario-web-app-sign-user-overview.md?tabs=java)
