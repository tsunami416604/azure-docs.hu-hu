---
title: "A rugó rendszerindító alapszintű használata az Azure Cosmos DB DocumentDB API-k"
description: "Útmutató a rugó rendszerindító inicializáló Azure Cosmos DB DocumentDB API-val készített alkalmazások konfigurálása."
services: cosmos-db
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
keywords: "Rugó, rugó rendszerindító Starter, Cosmos DB"
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/08/2017
ms.author: robmcm;yungez;kevinzha
ms.openlocfilehash: 273cc750857c5e466882060a38ac0f3475811e98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-spring-boot-starter-with-azure-cosmos-db-documentdb-api"></a>A rugó rendszerindító alapszintű használata Azure Cosmos DB DocumentDB API

## <a name="overview"></a>Áttekintés

A  **[rugó keretrendszer]**  egy nyílt forráskódú megoldás, amely a fejlesztőket Java vállalati szintű alkalmazásokat. A beépített több népszerű projektek a platform egyik [rugó rendszerindító], amely lehetővé teszi egy egyszerűsített megközelítés önálló Java-alkalmazások létrehozása. Ismerkedés a rugó rendszerindító fejlesztők érdekében több minta rugó rendszerindító csomagok elérhetők, <https://github.com/spring-guides/>. Alapszintű rugó rendszerindító projektek listája mellett a  **[rugó Initializr]**  a fejlesztőket az egyéni rendszerindító rugó-alkalmazások létrehozásának első lépései.

Azure Cosmos-adatbázis egy adatbázis globálisan elosztott szolgáltatás, amely lehetővé teszi a fejlesztők a szabványos API-k, például a DocumentDB MongoDB, Graph vagy tábla API-k többféle adatok. A Microsoft rugó rendszerindító alapszintű lehetővé teszi a fejlesztők rugó rendszerindító alkalmazások használatát, amelyek könnyen integrálhatja Azure Cosmos DB DocumentDB API-k használatával.

Ez a cikk bemutatja, hogy az Azure portál használatával, majd a használatával egy Azure Cosmos-adatbázis létrehozása a **rugó Initializr** egyéni java-alkalmazások létrehozásához, és majd a rugó rendszerindító alapszintű funkciókat adnak hozzá az egyéni alkalmazás az adatokat tárolhatnak, és az Azure Cosmos DB a DocumentDB API-jával adatainak lekérése.

## <a name="prerequisites"></a>Előfeltételek

A következő szoftverek szükségesek ahhoz, hogy kövesse a cikkben található lépéseket:

* Azure-előfizetés; Ha még nem rendelkezik Azure-előfizetéssel, aktiválhatja a [MSDN-előfizetői előnyeit] vagy regisztráljon egy [ingyenes Azure-fiókot].

* A [Java fejlesztői készlet (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/), 1.7 vagy újabb verziója.

* [Apache Maven](http://maven.apache.org/), 3.0-s vagy újabb verziója.

## <a name="create-an-azure-cosmos-db-by-using-the-azure-portal"></a>Egy Azure Cosmos-adatbázis létrehozása az Azure portál használatával

1. Keresse meg az Azure portálon, a <https://portal.azure.com/> kattintson **+ új**.

   ![Azure Portal][AZ01]

1. Kattintson a **adatbázisok**, és kattintson a **Azure Cosmos DB**.

   ![Azure Portal][AZ02]

1. Az a **Azure Cosmos DB** lapján adja meg a következő adatokat:

   * Adjon meg egy egyedi **azonosító**, mert ezzel fogja az URI-azonosítóként az adatbázis számára. Például: *wingtiptoysdata.documents.azure.com*.
   * Válasszon **SQL (DB dokumentum)** az API-hoz.
   * Válassza ki a **előfizetés** az adatbázishoz használni kívánt.
   * Adja meg, hogy hozzon létre egy új **erőforráscsoport** az adatbázishoz, vagy válasszon egy meglévő erőforráscsoportot.
   * Adja meg a **hely** az adatbázis számára.
   
   Ezek a beállítások megadását, kattintson a **létrehozása** az adatbázis létrehozásához.

   ![Azure Portal][AZ03]

1. Az adatbázis létrehozásakor az szerepel az Azure **irányítópult**, valamint a a **összes erőforrás** és **Azure Cosmos DB** lapokat. Kattintson bármelyik azokon a helyeken, a gyorsítótár Tulajdonságok lapjának megnyitásához az adatbázishoz.

   ![Azure Portal][AZ04]

1. Ha a Tulajdonságok lapon az adatbázis megjelenik, kattintson a **hívóbetűk** és a URI és hozzáférési kulcsokkal az adatbázis másolása; használhatja ezeket az értékeket az rugó rendszerindító alkalmazásban.

   ![Azure Portal][AZ05]

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>A rugó Initializr hozzon létre egy egyszerű rugó rendszerindító alkalmazást

1. Keresse meg a <https://start.spring.io/>.

1. Adja meg, hogy szeretné-e létrehozni egy **Maven** a projekt **Java**, adja meg a **csoport** és **összetevő** az alkalmazás nevét és Kattintson a gombra kattintva **készítése a projekt**.

   ![Basic rugó Initializr beállítások][SI01]

   > [!NOTE]
   >
   > A rugó Initializr használja a **csoport** és **összetevő** nevek létrehozása a csomag nevét; például: *com.example.wintiptoys*.
   >

1. Amikor a rendszer kéri, töltse le a projekt egy elérési utat a helyi számítógépen.

   ![Egyéni rugó rendszerindító-projekt letöltése][SI02]

1. A helyi számítógépen a fájlok kibontását követően az egyszerű rugó rendszerindító alkalmazás készen áll a szerkesztésre lesz.

   ![Egyéni rugó rendszerindító project fájlok][SI03]

## <a name="configure-your-spring-boot-app-to-use-the-azure-spring-boot-starter"></a>A rugó rendszerindító alkalmazás használatához az Azure rugó rendszerindító alapszintű konfigurálása

1. Keresse meg a *pom.xml* fájl a könyvtárban, az alkalmazás; például:

   `C:\SpringBoot\wingtiptoys\pom.xml`

   – vagy –

   `/users/example/home/wingtiptoys/pom.xml`

   ![Keresse meg a pom.xml fájlt][PM01]

1. Nyissa meg a *pom.xml* fájlt egy szövegszerkesztőben, és vegye fel a következő sorokat `<dependencies>`:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-documentdb-spring-boot-starter</artifactId>
      <version>0.1.4</version>
   </dependency>
   ```

   ![A pom.xml fájlt szerkesztése][PM02]

1. Mentse és zárja be a *pom.xml* fájlt.

## <a name="configure-your-spring-boot-app-to-use-your-azure-cosmos-db"></a>A rugó rendszerindító alkalmazás használatához az Azure Cosmos DB konfigurálása

1. Keresse meg a *application.properties* fájlt a *erőforrások* az alkalmazás könyvtárába, például:

   `C:\SpringBoot\wingtiptoys\src\main\resources\application.properties`

   – vagy –

   `/users/example/home/wingtiptoys/src/main/resources/application.properties`

   ![Keresse meg a application.properties fájlt][RE01]

1. Nyissa meg a *application.properties* fájlt egy szövegszerkesztőben, és adja hozzá a következő sorokat a fájlhoz, és cserélje le a minta értékek a megfelelő adatbázis tulajdonságainak megadása a:

   ```yaml
   # Specify the DNS URI of your Azure Cosmos DB.
   azure.documentdb.uri=https://wingtiptoys.documents.azure.com:443/

   # Specify the access key for your database.
   azure.documentdb.key=57686f6120447564652c20426f6220526f636b73==

   # Specify the name of your database.
   azure.documentdb.database=wingtiptoysdata
   ```

   ![A application.properties fájl szerkesztése][RE02]

1. Mentse és zárja be a *application.properties* fájlt.

## <a name="add-sample-code-to-implement-basic-database-functionality"></a>Hozzáadása alapszintű adatbázisokkal kapcsolatos funkciók végrehajtásához mintakód

Ebben a szakaszban hoz létre két Java-osztályok a felhasználói adatok tárolásához, és módosíthatja az alkalmazás osztályt létrehozni a felhasználói osztály egy példányát, és mentse azokat az adatbázisba.

### <a name="define-a-basic-class-for-storing-user-data"></a>Adja meg a felhasználói adatok tárolásához alapvető osztály

1. Hozzon létre egy új fájlt *User.java* a fő alkalmazásfájl Java ugyanabban a könyvtárban.

1. Nyissa meg a *User.java* fájlt egy szövegszerkesztőben, és adja hozzá a következő sorokat a fájlhoz egy általános felhasználói osztály, amely tárolja, és az adatbázis értékek lekérését meghatározásához:

   ```java
   package com.example.wingtiptoys;

   public class User {
      private String id;
      private String firstName;
      private String lastName;
 
      public User(String id, String firstName, String lastName) {
         this.id = id;
         this.firstName = firstName;
         this.lastName = lastName;
      }
   
      public String getId() {
         return this.id;
      }

      public void setId(String id) {
         this.id = id;
      }

      public String getFirstName() {
         return firstName;
      }

      public void setFirstName(String firstName) {
         this.firstName = firstName;
      }

      public String getLastName() {
         return lastName;
      }

      public void setLastName(String lastName) {
         this.lastName = lastName;
      }

      @Override
      public String toString() {
         return String.format("User: %s %s", firstName, lastName);
      }
   }
   ```

1. Mentse és zárja be a *User.java* fájlt.

### <a name="define-a-data-repository-interface"></a>A tárház adatillesztő meghatározása

1. Hozzon létre egy új fájlt *UserRepository.java* a fő alkalmazásfájl Java ugyanabban a könyvtárban.

1. Nyissa meg a *UserRepository.java* fájlt egy szövegszerkesztőben, és adja hozzá a következő sorokat a fájlhoz egy felhasználó alapértelmezett DocumentDB tárház felületet tárház felület meghatározása:

   ```java
   package com.example.wingtiptoys;

   import com.microsoft.azure.spring.data.documentdb.repository.DocumentDbRepository;
   import org.springframework.stereotype.Repository;

   @Repository
   public interface UserRepository extends DocumentDbRepository<User, String> {}   
   ```

1. Mentse és zárja be a *UserRepository.java* fájlt.

### <a name="modify-the-main-application-class"></a>A fő alkalmazásosztály módosítása

1. Keresse meg a fő alkalmazásfájl Java a csomag könyvtárában, az alkalmazás; Példa:

   `C:\SpringBoot\wingtiptoys\src\main\java\com\example\wingtiptoys\WingtiptoysApplication.java`

   – vagy –

   `/users/example/home/wingtiptoys/src/main/java/com/example/wingtiptoys/WingtiptoysApplication.java`

   ![Keresse meg az alkalmazás Java fájlt][JV01]

1. Nyissa meg az alkalmazás Java fájlt egy szövegszerkesztőben, és adja hozzá a fájlhoz a következő sorokat:

   ```java
   package com.example.wingtiptoys;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.CommandLineRunner;

   @SpringBootApplication
   public class WingtiptoysApplication implements CommandLineRunner {

      @Autowired
      private UserRepository repository;
    
      public static void main(String[] args) {
         SpringApplication.run(WingtiptoysApplication.class, args);
      }

      public void run(String... var1) throws Exception {
         final User testUser = new User("testId", "testFirstName", "testLastName");

         repository.deleteAll();
         repository.save(testUser);

         final User result = repository.findOne(testUser.getId());

         System.out.printf("\n\n%s\n\n",result.toString());
      }
   }
   ```

1. Mentse és zárja be a fő alkalmazásfájl Java.

## <a name="build-and-test-your-app"></a>Hozza létre, és az alkalmazás tesztelése

1. Nyisson meg egy parancssort, és módosítsa a könyvtárat a mappához adott a *pom.xml* -fájl, például:

   `cd C:\SpringBoot\wingtiptoys`

   – vagy –

   `cd /users/example/home/wingtiptoys`

1. A rugó rendszerindító alkalmazás Maven létrehozása és futtatása. Példa:

   ```shell
   mvn package
   java -jar target/wingtiptoys-0.0.1-SNAPSHOT.jar
   ```

1. Az alkalmazás több futásidejű üzenet jelenik meg, és az üzenet `User: testFirstName testLastName` annak jelzésére, hogy értékek sikeresen tárolt és az adatbázisból beolvasott jelenik meg.

   ![Az alkalmazás sikeres kimenete][JV02]

1. Választható lehetőség: Az Azure portál segítségével kattintva tekintheti meg az Azure Cosmos DB a tulajdonságai lapon tartalmát az adatbázis **dokumentumkezelő**, és jelölje be, és a kijelzett lista elem található tartalom megtekintéséhez.

   ![A dokumentum Explorer segítségével megtekintheti az adatokat][JV03]

## <a name="next-steps"></a>Következő lépések

Azure Cosmos DB és Java használatával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Az Azure Cosmos DB dokumentáció].

* [Az Azure Cosmos DB: Összeállítása a DocumentDB API-alkalmazást Java és az Azure-portálon][Build a DocumentDB API app with Java]

Azure rugó rendszerindító alkalmazások használatával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Rugó rendszerindító DocumenDB alapszintű az Azure-bA](https://github.com/Microsoft/azure-spring-boot-starters/tree/master/azure-documentdb-spring-boot-starter-sample)

* [Az Azure App Service egy rugó rendszerindító alkalmazás központi telepítése](../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)

* [Az az Azure Tárolószolgáltatásban Kubernetes fürt rugó rendszerindító alkalmazást futtat](../container-service/container-service-deploy-spring-boot-app-on-kubernetes.md)

Az Azure Javával való használatáról további információ: [Azure Java fejlesztői központ] és [Java-eszközök a Visual Studio Team Serviceshez].

<!-- URL List -->

[Az Azure Cosmos DB dokumentáció]: /azure/cosmos-db/
[Azure Java fejlesztői központ]: https://azure.microsoft.com/develop/java/
[Build a DocumentDB API app with Java]: https://docs.microsoft.com/azure/cosmos-db/create-documentdb-java
[ingyenes Azure-fiókot]: https://azure.microsoft.com/pricing/free-trial/
[Java-eszközök a Visual Studio Team Serviceshez]: https://java.visualstudio.com/
[MSDN-előfizetői előnyeit]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[rugó rendszerindító]: http://projects.spring.io/spring-boot/
[rugó Initializr]: https://start.spring.io/
[rugó keretrendszer]: https://spring.io/

<!-- IMG List -->

[AZ01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ01.png
[AZ02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ02.png
[AZ03]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ03.png
[AZ04]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ04.png
[AZ05]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ05.png

[SI01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/SI01.png
[SI02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/SI02.png
[SI03]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/SI03.png

[RE01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/RE01.png
[RE02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/RE02.png

[PM01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/PM01.png
[PM02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/PM02.png

[JV01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/JV01.png
[JV02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/JV02.png
[JV03]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/JV03.png
