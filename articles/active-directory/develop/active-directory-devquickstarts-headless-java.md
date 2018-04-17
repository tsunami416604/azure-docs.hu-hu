---
title: Ismerkedés az Azure AD Java parancssori |} Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egy Java parancssori alkalmazás, amely a felhasználó bejelentkezik az API-k eléréséhez.
services: active-directory
documentationcenter: java
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 51e1a8f9-6ff0-4643-a350-0ba794e26fd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 01/23/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: a0e12711e4a7e67861d61ae4575c4956531cf841
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="using-java-command-line-app-to-access-an-api-with-azure-ad"></a>Az Azure AD az API-k elérésére használt Java parancssori alkalmazás
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Az Azure AD teszi egyszerű és magától értetődő kihelyező a webalkalmazás az Identitáskezelés, így egyetlen be- és kijelentkezési rendelkező csak néhány sornyi kódot.  A Java-webalkalmazások Ez elvégezhető a közösségi szerkesztésű ADAL4J Microsoft végrehajtásának használatával.

  Itt a ADAL4J fogjuk használni:

* A felhasználó jelentkezzen be az alkalmazást az Azure AD az identitás-szolgáltatóként.
* A felhasználói információkat jelenítsen meg.
* Jelentkezzen ki az alkalmazásból a felhasználó.

Ehhez szüksége:

1. Alkalmazás regisztrálása az Azure ad szolgáltatással
2. Az alkalmazás beállítása a ADAL4J könyvtárban.
3. A ADAL4J könyvtár segítségével be- és kijelentkezési kérések kiállítása az Azure ad Szolgáltatásba.
4. Nyomtassa ki a felhasználóval kapcsolatos adatokat.

A kezdéshez [töltse le az alkalmazás vázat](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip) vagy [töltse le az elkészült mintát](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\\/archive/complete.zip).  Biztosítani kell az Azure AD-bérlő, amelyben az alkalmazás regisztrálásához.  Ha Ön nem rendelkezik ilyennel, [beszerzéséről egy](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>1.  Alkalmazás regisztrálása az Azure ad szolgáltatással
Ahhoz, hogy az alkalmazás a felhasználók hitelesítésére, először egy új alkalmazás regisztrálásához az Ön bérelt szolgáltatásának.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A felső eszköztáron kattintson a fiókhoz, majd a a **Directory** menüben válassza ki az Active Directory-bérlőt, ahová be szeretné-e az alkalmazás regisztrálásához.
3. Kattintson a **minden szolgáltatás** a bal oldali navigációs válassza **Azure Active Directory**.
4. Kattintson a **App regisztrációk** válassza **Hozzáadás**.
5. Kövesse az utasításokat, és hozzon létre egy új **webalkalmazás és/vagy WebAPI**.
  * A **neve** az alkalmazás ismerteti az alkalmazást a végfelhasználók számára
  * A **bejelentkezési URL-cím** az alkalmazás alap URL-címe.  A vázat alapértelmezett érték a `http://localhost:8080/adal4jsample/`.
6. Miután végrehajtotta a regisztráció, AAD fogja hozzárendelni az alkalmazás egy egyedi azonosítót.  Ez az érték kell a következő szakaszokban lévő, másolja az alkalmazás lapján.
7. Az a **beállítások** -> **tulajdonságok** az alkalmazás lapján frissítse a App ID URI. A **App ID URI** az alkalmazás egyedi azonosítója.  Az egyezmény használandó `https://<tenant-domain>/<app-name>`, pl. `http://localhost:8080/adal4jsample/`.

Egyszer a portálon, az alkalmazás létrehozása egy **kulcs** a a **beállítások** lapon az alkalmazáshoz, és másolja le.  Erre hamarosan szüksége lesz.

## <a name="2-set-up-your-app-to-use-adal4j-library-and-prerequisites-using-maven"></a>2. Az alkalmazás beállítása ADAL4J könyvtár és előfeltételek Maven használatával
Itt konfigurálását végezzük el ADAL4J az OpenID Connect hitelesítési protokoll használatára.  Be- és kijelentkezési kérések kiállítása, a felhasználói munkamenet kezelésére, és többek között a felhasználó adatai ADAL4J fogja használni.

* A projekt gyökérkönyvtárában, megnyitni vagy létrehozni `pom.xml` , és keresse meg a `// TODO: provide dependencies for Maven` , és cserélje le a következőre:

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>public-client-adal4j-sample</artifactId>
    <packaging>jar</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>public-client-adal4j-sample</name>
    <url>http://maven.apache.org</url>
    <properties>
        <spring.version>3.0.5.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>adal4j</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>com.nimbusds</groupId>
            <artifactId>oauth2-oidc-sdk</artifactId>
            <version>4.5</version>
        </dependency>
        <dependency>
            <groupId>org.json</groupId>
            <artifactId>json</artifactId>
            <version>20090211</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.5</version>
        </dependency>
</dependencies>
    <build>
        <finalName>public-client-adal4j-sample</finalName>
        <plugins>
                <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>install</id>
                        <phase>install</phase>
                        <goals>
                            <goal>sources</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>2.5</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
        </plugins>
    </build>

</project>


```



## <a name="3-create-the-java-publicclient-file"></a>3. A Java PublicClient fájl létrehozása
A fentiek alapján fogjuk használni a Graph API a bejelentkezett felhasználó kapcsolatos adatok eléréséhez. Ez lesz az USA könnyen mindkét fájlt képviselő azt létrehoznia egy **könyvtárobjektum** és képviselő fájlhoz a **felhasználói** , hogy a Java OO mintáját használható.

* Hozzon létre egy nevű fájlt `DirectoryObject.java` , amelyek segítségével bármely DirectoryObject (, nyugodtan később használhatja más diagramhoz lekérdezések a következőkre) kapcsolatos alapvető adatok tárolásához. Akkor is kivágás-beillesztés ezt az alábbi lehetőségek közül:

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


## <a name="compile-and-run-the-sample"></a>Fordítsa le, és futtathatja a
A gyökérkönyvtár vissza kimenő módosítsa, majd futtassa a következő parancsot a minta csak állapotba használata `maven`. Ez a beállítás használja a `pom.xml` függőségek megírt fájlt.

`$ mvn package`

Most már rendelkeznie kell egy `adal4jsample.war` fájlt a `/targets` könyvtár. Előfordulhat, hogy telepíteni, amely a Tomcat tárolóban, és látogasson el az URL-cím 

`http://localhost:8080/adal4jsample/`

> [!NOTE]
> Rendkívül egyszerűen telepíthető a legújabb Tomcat kiszolgálókkal WAR. Egyszerűen lépjen `http://localhost:8080/manager/` feltöltésével kapcsolatos kövesse az utasításokat a `adal4jsample.war` fájlt. A következőket hajtja végre autodeploy meg a helyes végpontját.
> 
> 

## <a name="next-steps"></a>További lépések
Gratulálunk! Most már rendelkezik egy Java-alkalmazás, amely képes biztonságosan hitelesíti a felhasználókat, működő hívja fel a webes API-k, az OAuth 2.0 verziót használja, és a felhasználó alapszintű adatainak beolvasása.  Ha még nem tette meg, most már az egyes felhasználóival a bérlő feltölti idő.

Az elkészült mintát (a konfigurációs értékek nélkül) referenciaként [is letöltheti a .zip Itt](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip), vagy a Githubból is klónozhatja:

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```

