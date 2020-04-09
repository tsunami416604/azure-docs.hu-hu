---
title: Microsoft identity platform Java webapp rövid útmutató | Azure
description: A Microsoft Bejelentkezés java webalkalmazáson történő megvalósítása az OpenID Connect használatával
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java
ms.openlocfilehash: 42b478b598fe3034f1a97fe41662a38bc265074d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875979"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Rövid útmutató: Bejelentkezés hozzáadása a Microsofttal egy Java webalkalmazáshoz

Ebben a rövid útmutatóban megtudhatja, hogyan integrálhat egy Java webalkalmazást a Microsoft identitáskezelési platformjával. Az alkalmazás bejelentkezik egy felhasználóba, kap egy hozzáférési jogkivonatot a Microsoft Graph API hívásához, és kérést küld a Microsoft Graph API-hoz.

A rövid útmutató befejezése után az alkalmazás elfogadja a személyes Microsoft-fiókok (beleértve a outlook.com, live.com és másokat) és az Azure Active Directoryt használó vállalatok vagy szervezetek munkahelyi vagy iskolai fiókjainak bejelentkezését. (Lásd: [Hogyan működik a minta](#how-the-sample-works) egy illusztrációhoz.)

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához a következőkre lesz szükség:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 vagy nagyobb, és [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> A rövid útmutató elindításához két lehetőség közül választhat: expressz (1. lehetőség) vagy manuális (2. lehetőség)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése
>
> 1. Nyissa meg az [Azure Portal – Az alkalmazásregisztrációk rövid útmutatóját.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs)
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse a portál rövid útmutatójában található utasításokat az automatikusan konfigurált alkalmazáskód letöltéséhez.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
>
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs adatainak manuális hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
>
> 1. Keresse meg a Microsoft identity platform ot a fejlesztőknek [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapon.
> 1. Válassza **az Új regisztráció lehetőséget.**
> 1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
>    - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `java-webapp`).
>    - Kattintson a **Register** (Regisztrálás) elemre.
> 1. Az **Áttekintés** lapon keresse meg az **alkalmazás (ügyfél) azonosítóját** és az alkalmazás **címtár (bérlői)** azonosítóértékeit. Másolja ezeket az értékeket későbbre.
> 1. Válassza ki a **hitelesítést** a menüből, majd adja meg a következő adatokat:
>    - Adja hozzá a **webes** platform konfigurációját.  Adja `https://localhost:8080/msal4jsample/secure/aad` hozzá `https://localhost:8080/msal4jsample/graph/me` ezeket, és **átirányítási URI-kként**..
>    - Kattintson a **Mentés** gombra.
> 1. Válassza ki a **tanúsítványok & titkos kulcsokat** a menüből, és az **Ügyfél titkos kulcsok** szakaszban kattintson az **Új ügyféltitok**elemre:
>
>    - Írja be a kulcs leírását (például alkalmazástitkos ítmény).
>    - Válasszon egy kulcs **időtartamot egy év alatt.**
>    - A kulcs értéke akkor jelenik meg, ha a Hozzáadás lehetőséget **választja.**
>    - Másolja a kulcs értékét későbbre. Ez a kulcsérték nem jelenik meg újra, és nem visszakereshető más módon, így rögzítse, amint látható az Azure Portalon.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: Az alkalmazás konfigurálása az Azure Portalon
>
> A rövid útmutató működéséhez a kódmintához a következőket kell elvégeznie:
>
> 1. VálaszURL-címek hozzáadása `https://localhost:8080/msal4jsample/secure/aad` `https://localhost:8080/msal4jsample/graph/me`a és a néven
> 1. Ügyféltitok létrehozása.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Hajtsa végre ezeket a módosításokat nekem]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-aspnet-webapp/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-code-sample"></a>2. lépés: A kódminta letöltése
> [!div renderon="docs"]
> [A kódminta letöltése](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Töltse le a projektet, és bontsa ki a zip fájlt egy helyi mappába, amely közelebb van a gyökérmappához - például **C:\Azure-Samples**
>
> A https és a localhost használatához töltse ki a server.ssl.key tulajdonságokat. Önaláírt tanúsítvány létrehozásához használja a (JRE részét képező) kulcseszköz segédprogramot.
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
>   Helyezze a létrehozott keystore fájlt az "erőforrások" mappába.

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [A kódminta letöltése](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>3. lépés: A kódminta konfigurálása
> 1. Csomagolja ki a zip-fájlt egy helyi mappába.
> 1. Ha integrált fejlesztői környezetet használ, nyissa meg a mintát a kedvenc IDE-ben (nem kötelező).
> 1. Nyissa meg az application.properties fájlt, amely megtalálható az src/main/resources/ mappában, és cserélje le az *aad.clientId,* *aad.authority* és *aad.secretKey* mezők értékét az **Alkalmazásazonosító**, **a bérlőazonosító** és az **ügyféltitok** megfelelő értékeivel a következőképpen:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8080/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8080/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
> Az elemek magyarázata:
>
> - `Enter_the_Application_Id_here` – ez a regisztrált alkalmazás alkalmazásazonosítója.
> - `Enter_the_Client_Secret_Here`- az **ügyféltitok,** amelyet a **tanúsítványokban & titokban** hozott létre a regisztrált alkalmazáshoz.
> - `Enter_the_Tenant_Info_Here`- a regisztrált alkalmazás **címtár-azonosítóértéke.**
> 1. A https és a localhost használatához töltse ki a server.ssl.key tulajdonságokat. Önaláírt tanúsítvány létrehozásához használja a (JRE részét képező) kulcseszköz segédprogramot.
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
>   Helyezze a létrehozott keystore fájlt az "erőforrások" mappába.


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>3. lépés: A kódminta futtatása
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>4. lépés: A kódminta futtatása

A projekt futtatásához a következőket teheti:

Futtassa közvetlenül az IDE segítségével a beágyazott tavaszi boot szerver vagy a csomag, hogy egy WAR fájlt [maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) és telepíteni egy J2EE konténer megoldás, mint az [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-from-ide"></a>Futás IDE-ről

Ha a webalkalmazást IDE-ből futtatja, kattintson a futtatásra, majd keresse meg a projekt kezdőlapját. Ebben a példában a kezdőlap https://localhost:8080szokásos URL-címe .

1. A főoldalon válassza a **Bejelentkezés** gombot az Azure Active Directoryra való átirányításhoz, és kérje meg a felhasználónak a hitelesítő adatait.

1. A felhasználó hitelesítése után a rendszer *https://localhost:8080/msal4jsample/secure/aad*átirányítja őket a rendszerbe. Most már be vannak jelentkezve, és a lapon megjelennek a bejelentkezett fiókkal kapcsolatos információk. A minta felhasználói felülete a következő gombokkal rendelkezik:
    - *Kijelentkezés:* Kijelentkezés: Kijelentkezteti az aktuális felhasználót az alkalmazásból, és átirányítja a kezdőlapra.
    - *Felhasználói adatok megjelenítése*: Jogkivonat beszerzése a Microsoft Graph számára, és meghívja a Microsoft Graph-ot a jogkivonatot tartalmazó kéréssel, amely a bejelentkezett felhasználó alapvető adatait adja vissza.

##### <a name="running-from-tomcat"></a>Futás A Tomcat

Ha szeretné telepíteni a webes mintát Tomcat, meg kell, hogy egy pár változtatást a forráskódot.

1. Ms-identity-java-webapp/pom.xml megnyitása
    - A `<name>msal-web-sample</name>` Hozzáadás csoportban`<packaging>war</packaging>`
    - Függőség hozzáadása:

         ```xml
         <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-tomcat</artifactId>
          <scope>provided</scope>
         </dependency>
         ```

2. Ms-identity-java-webapp/src/main/java/com.microsoft.azure.msalwebsample/MsalWebSampleApplication megnyitása

    - Törölje az összes forráskódot, és cserélje le a következőkre:

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

3. Nyisson meg egy parancssort, lépjen a projekt gyökérmappájába, és futtassa`mvn package`
    - Ez létrehoz `msal-web-sample-0.1.0.war` egy fájlt a /targets könyvtárban.
    - A fájl átnevezése`ROOT.war`
    - Telepítse ezt a háborús fájlt a Tomcat vagy bármely más J2EE tárolómegoldás használatával.
        - A Tomcat-tárolóban való telepítéshez másolja a .war fájlt a Tomcat-telepítés alatti webapps mappába, majd indítsa el a Tomcat-kiszolgálót.

Ez a háború automatikusan házigazdája . https://localhost:8080/

> [!IMPORTANT]
> Ez a rövid útmutató alkalmazás egy ügyféltitkos kulcsot használ, hogy bizalmas ügyfélként azonosítsa magát. Mivel az ügyféltitkos kulcsot egyszerű szövegként adja hozzá a projektfájlokhoz, biztonsági okokból ajánlott az alkalmazás éles alkalmazásként való felhasználása előtt az ügyféltitok helyett tanúsítványt használni. A tanúsítványok használatáról a [Tanúsítványhitelesítő adatok az alkalmazás hitelesítéséhez](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials)című témakörben talál további információt.

## <a name="more-information"></a>További információ

### <a name="how-the-sample-works"></a>A minta működése
![Megmutatja, hogyan működik az ez a rövid útmutató által létrehozott mintaalkalmazás](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="getting-msal"></a>Első MSAL

Az MSAL java (MSAL4J) a Felhasználók bejelentkezésére és a Microsoft identity Platform által védett API-k eléréséhez használt jogkivonatok igénylésére használt Java-könyvtár.

Adja hozzá az MSAL4J-t az alkalmazáshoz a Maven vagy a Gradle használatával a függőségek kezeléséhez az alkalmazás pom.xml (Maven) vagy build.gradle (Gradle) fájljának következő módosításával.

A pom.xml fájlban:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

Build.gradle nyelven:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

Hivatkozás hozzáadása az MSAL for Java alkalmazáshoz a következő kód hozzáadásával a fájl tetejéhez, ahol az MSAL4J-t fogja használni:

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Következő lépések

További információ az engedélyekről és a hozzájárulásról:

> [!div class="nextstepaction"]
> [Engedélyek és hozzájárulás](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

Ha többet szeretne tudni az ebben a forgatókönyvben szereplő hitelesítési folyamatról, olvassa el az Oauth 2.0 engedélyezési kód folyamatát:

> [!div class="nextstepaction"]
> [Engedélyezési kód Oauth-folyamat](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)

Segítsen nekünk a Microsoft identitásplatformjának fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdésű felmérés kitöltésével.

> [!div class="nextstepaction"]
> [Microsoft-identitásplatform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
