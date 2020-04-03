---
title: Node.js Mongoose alkalmazás csatlakoztatása az Azure Cosmos DB-hez
description: Ismerje meg, hogyan használhatja a Mongoose keretrendszer adatok tárolására és kezelésére az Azure Cosmos DB.Ismerje meg, hogyan használhatja a Mongúz-keretadatok tárolására és kezelésére az Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: ff4455571aa5cfa5c9214bdf18af1853b0cef352
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585403"
---
# <a name="connect-a-nodejs-mongoose-application-to-azure-cosmos-db"></a>Node.js Mongoose alkalmazás csatlakoztatása az Azure Cosmos DB-hez

Ez az oktatóanyag bemutatja, hogyan használhatja a [Mongúz keretadatok](https://mongoosejs.com/) cosmos DB adatok tárolására. Ehhez a forgatókönyvhez az Azure Cosmos DB MongoDB-hoz való API-ját használjuk. Ha nem ismerné, a Mongoose egy objektummodellezési keretrendszer a MongoDB-hez Node.js-ben, és egyszerű, sémaalapú megoldást biztosít az alkalmazásadatok modellezéséhez.

A Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Gyorsan hozhat létre és kérdezhet le dokumentumokat, kulcs-érték és gráf-adatbázisokat, amelyek mindegyike a Cosmos DB központi magjában lévő globális terjesztési és horizontális méretezési képességek előnyeit élvezi.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/)-verzió: 0.10.29-s vagy újabb.

## <a name="create-a-cosmos-account"></a>Cosmos-fiók létrehozása

Hozzunk létre egy Cosmos-fiókot. Ha már rendelkezik egy használni kívánt fiókkal, folytassa A Node.js-alkalmazás beállítása című lépéssel. Ha az Azure Cosmos DB-emulátort használja, kövesse az [Azure Cosmos DB-emulátor](local-emulator.md) lépéseit az emulátor beállításához, és ugorjon előre a Node.js alkalmazás beállítása.

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

### <a name="create-a-database"></a>Adatbázis létrehozása 
Ebben az alkalmazásban az Azure Cosmos DB-ben kétféle gyűjtemény létrehozását fedjük le: 
- **Az egyes objektummodellek külön gyűjteményben történő**tárolása : Javasoljuk, hogy [hozzon létre egy dedikált átviteli fokkal rendelkező adatbázist.](set-throughput.md#set-throughput-on-a-database) Ennek a kapacitásmodellnek a használata jobb költséghatékonyságot eredményez.

    :::image type="content" source="./media/mongodb-mongoose/db-level-throughput.png" alt-text="Node.js oktatóanyag – Képernyőkép az Azure Portalról, amely bemutatja, hogyan hozhat létre adatbázist az Adatkezelőben egy Azure Cosmos DB-fiókhoz, a Mongoose csomópont modullal való használatra":::

- **Az összes objektummodell egyetlen Cosmos DB-gyűjteményben való tárolása:** Ha az összes modellt egyetlen gyűjteményben szeretné tárolni, egyszerűen létrehozhat egy új adatbázist az átviteli átbocsátás lehetőség kiválasztása nélkül. Ennek a kapacitásmodellnek a használatával minden gyűjtemény saját átviteli kapacitással rendelkezik minden objektummodellhez.

Az adatbázis létrehozása után az alábbi környezeti `COSMOSDB_DBNAME` változóban a nevet fogja használni.

## <a name="set-up-your-nodejs-application"></a>A Node.js-alkalmazás beállítása

>[!Note]
> Ha nem szeretné beállítani magát az alkalmazást, csak a mintakódon szeretne végighaladni, klónozza az oktatóanyaghoz használt [mintát](https://github.com/Azure-Samples/Mongoose_CosmosDB), és hozza létre a Node.js Mongoose alkalmazást az Azure Cosmos DB-ben.

1. Node.js-alkalmazás létrehozásához a kiválasztott mappában futtassa az alábbi parancsot egy csomópont parancssorban.

    ```npm init```

    Válaszoljon a kérdésekre, és a projekt indulásra kész.

2. Vegyen fel a mappába egy új fájlt, és adja neki az ```index.js``` nevet.
3. Telepítse a szükséges csomagokat az egyik ```npm install``` lehetőséggel:
   * Mongoose: ```npm install mongoose@5 --save```

     > [!Note]
     > Az alábbi Mongoose példakapcsolat a Mongoose 5+ szolgáltatáson alapul, amely a korábbi verziók óta megváltozott.
    
   * Dotenv (ha a titkos kulcsokat egy .env fájlból szeretné betölteni): ```npm install dotenv --save```

     >[!Note]
     > A ```--save``` jelző hozzáadja a függőséget a package.json fájlhoz.

4. Importálja a függőségeket az index.js fájljába.

    ```JavaScript
   var mongoose = require('mongoose');
   var env = require('dotenv').config();   //Use the .env file to load the variables
    ```

5. Vegye fel a Cosmos DB kapcsolati sztringjét és a Cosmos DB-nevet a ```.env``` fájlba. Cserélje le a helyőrzőket {cosmos-account-name} és {dbname} saját Cosmos-fióknévre és adatbázisnévre, a kapcsoszárójel-szimbólumok nélkül.

    ```JavaScript
   # You can get the following connection details from the Azure portal. You can find the details on the Connection string pane of your Azure Cosmos account.

   COSMODDB_USER = "<Azure Cosmos account's user name, usually the database account name>"
   COSMOSDB_PASSWORD = "<Azure Cosmos account password, this is one of the keys specified in your account>"
   COSMOSDB_DBNAME = "<Azure Cosmos database name>"
   COSMOSDB_HOST= "<Azure Cosmos Host name>"
   COSMOSDB_PORT=10255
    ```

6. Csatlakozzon a Cosmos DB-hez a Mongúz-keretrendszer használatával, és adja hozzá a következő kódot az index.js végéhez.
    ```JavaScript
   mongoose.connect("mongodb://"+process.env.COSMOSDB_HOST+":"+process.env.COSMOSDB_PORT+"/"+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb", {
      auth: {
        user: process.env.COSMODDB_USER,
        password: process.env.COSMOSDB_PASSWORD
      }
    })
    .then(() => console.log('Connection to CosmosDB successful'))
    .catch((err) => console.error(err));
    ```
    >[!Note]
    > Itt a környezeti változók a process.env{variableName} segítségével, a „dotenv” npm-csomaggal tölthetők be.

    Ha már csatlakozott az Azure Cosmos DB-hez, elkezdheti az objektummodellek beállítását a Mongoose-ban.

## <a name="best-practices-for-using-mongoose-with-cosmos-db"></a>A Mongoose és a Cosmos DB használatának ajánlott eljárások

A Mongúz minden létrehozott modellhez létrehoz egy új gyűjteményt. Ez a legjobb, ha az [adatbázisszintű átviteli áteresztőkapcsolóval](set-throughput.md#set-throughput-on-a-database)foglalkozunk, amelyről korábban tárgyaltunk. Egyetlen gyűjtemény használatához mongúz [diszkriminátorokat kell használnia.](https://mongoosejs.com/docs/discriminators.html) A diszkriminátorok sémaöröklési mechanizmusok. Lehetővé teszik, hogy egy alapul szolgáló MongoDB-gyűjteményre több modell épüljön átfedő sémákkal.

A különböző adatmodelleket tárolhatja ugyanabban a gyűjteményben, majd a lekérdezés időpontjában alkalmazhat egy szűrőfeltételt, hogy csak a szükséges adatokat kérje le. Nézzük át az egyes modellek.

### <a name="one-collection-per-object-model"></a>Objektummodellenként egy gyűjtemény

Ez a szakasz azt ismerteti, hogyan érheti el ezt az Azure Cosmos DB MongoDB-hoz való API-jával. Ez a módszer az ajánlott megközelítésünk, mivel lehetővé teszi a költségek és a kapacitás ellenőrzését. Ennek eredményeképpen az adatbázisban lévő kérelemegységek mennyisége nem függ az objektummodellek számától. Ez a Mongúz alapértelmezett működési modellje, így lehet, hogy ismeri ezt.

1. Nyissa meg újra az ```index.js``` fájlt.

1. Hozzon létre egy sémadefiníciót a „Family” (Család) elemhez.

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

1. Hozzon létre egy objektumot a „Family” (Család) elemhez.

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. Végül mentsük az objektumot a Cosmos DB-re. Ezzel létrehoz egy gyűjteményt a háttérben.

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. Most hozzon létre egy másik sémát és objektumot. Ez alkalommal hozzon létre egy „Vacation Destinations” (Nyaralási úticélok) elemet a családokat esetlegesen érdeklő célpontokhoz.
   1. Hozza létre a sémát, akárcsak a legutóbb
      ```JavaScript
      const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
       name: String,
       country: String
      }));
      ```

   1. Hozzon létre egy mintaobjektumot (ehhez a sémához több objektumot is hozzáadhat), majd mentse azt.
      ```JavaScript
      const vacaySpot = new VacationDestinations({
       name: "Honolulu",
       country: "USA"
      });

      vacaySpot.save((err, saveVacay) => {
       console.log(JSON.stringify(saveVacay));
      });
      ```

1. Most az Azure Portalon, két, a Cosmos DB-ben létrehozott gyűjteményt észlel.

    ![Node.js oktatóanyag – Képernyőkép az Azure Portalról, amelyen egy Azure Cosmos DB-fiók látható, több gyűjteménynév kiemelve – Node adatbázis][multiple-coll]

1. Végül olvassuk el a Cosmos DB adatait. Mivel az alapértelmezett Mongoose működési modellt használjuk, az olvasások megegyeznek bármely más Mongoose olvasással.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Adatok tárolása egyetlen gyűjteményben Mongoose diszkriminátorok használatával

Ebben a módszerben [a Mongúz diszkriminátorokat használjuk](https://mongoosejs.com/docs/discriminators.html) az egyes gyűjtemények költségeinek optimalizálásához. A diszkriminátorok lehetővé teszik egy megkülönböztető „kulcs” meghatározását, amely segítségével tárolhatja, megkülönböztetheti és szűrheti a különböző objektummodelleket.

Itt létrehozunk egy alapszintű objektummodellt, meghatározunk egy megkülönböztető kulcsot, majd felvesszük az alapszintű modell kiterjesztéseként a „Family” (Család) és a „VacationDestinations” (Nyaralási úti célok) sémát.

1. Állítsuk be az alapszintű konfigurációt, és határozzuk meg a diszkriminátorkulcsot.

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

1. Ezután határozzuk meg a közös objektummodellt

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

1. Most meghatározzuk a „Family” (Család) modellt. Vegye észre, hogy a ```mongoose.model``` helyett a ```commonModel.discriminator``` elemet használjuk. Ezen felül hozzáadjuk az alapszintű konfigurációt is a Mongoose sémához. Itt a discriminatorKey (diszkriminátorkulcs) tehát ```FamilyType```.

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

1. Vegyük fel ugyanígy a másik sémát, ez alkalommal a „VacationDestinations” (Nyaralási úti célok) nevűt. A discriminatorKey (diszkriminátorkulcs) itt ```VacationDestinationsType```.

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. Végezetül hozzunk létre objektumokat a modellhez, majd mentsük azt.
   1. Vegyünk fel objektumo(ka)t a „Family” (Család) modellhez.
      ```JavaScript
      const family_common = new Family_common({
       lastName: "Volum",
       parents: [
           { firstName: "Thomas" },
           { firstName: "Mary Kay" }
       ],
       children: [
           { firstName: "Ryan", gender: "male", grade: 8 },
           { firstName: "Patrick", gender: "male", grade: 7 }
       ],
       pets: [
           { givenName: "Blackie" }
       ],
       address: { country: "USA", state: "WA", city: "Seattle" }
      });

      family_common.save((err, saveFamily) => {
       console.log("Saved: " + JSON.stringify(saveFamily));
      });
      ```

   1. Ezután vegyünk fel objektumo(ka)t a „VacationDestinations” (Nyaralási úti cél) modellhez, majd mentsük azt.
      ```JavaScript
      const vacay_common = new Vacation_common({
       name: "Honolulu",
       country: "USA"
      });

      vacay_common.save((err, saveVacay) => {
       console.log("Saved: " + JSON.stringify(saveVacay));
      });
      ```

1. Ha most visszatér az Azure Portalra, láthatja, hogy egyetlen gyűjteménye van ```alldata``` néven, amelyben a „Family” (Család) és a „VacationDestinations” (Nyaralási úti célok) adatai is szerepelnek.

    ![Node.js oktatóanyag – Képernyőkép az Azure Portalról, amelyen egy Azure Cosmos DB-fiók látható, kiemelt gyűjteménynévvel – Node adatbázis][alldata]

1. Azt is észreveheti, hogy minden objektum rendelkezik egy másik, ```__type``` nevű attribútummal, amely segít megkülönböztetni a két különböző objektummodellt.

1. Végezetül most olvassuk be az Azure Cosmos DB-ben tárolt adatokat. A Mongoose gondoskodik a modellen alapuló adatok szűréséről. Így semmit sem kell másképp csinálnia az adatok olvasásakor. Csak adja meg a modellt (ebben az esetben: ```Family_common```), és a Mongoose kezeli a szűrést a „DiscriminatorKey” alapján.

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

Amint láthatja, a Mongoose diszkriminátorok könnyen használhatók. Így ha van egy alkalmazás, amely a Mongúz-keretrendszer, ez az oktatóanyag egy módja annak, hogy az alkalmazás az Azure Cosmos Api-mongoDB használata nélkül túl sok módosítást.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan használhatja a [Studio 3T-t](mongodb-mongochef.md) az Azure Cosmos DB MongoDB-hoz való API-jával.
- Ismerje meg, hogyan használhatja a [Robo 3T-t](mongodb-robomongo.md) az Azure Cosmos DB MongoDB-hoz való API-jával.
- Fedezze fel a [MongoDB-mintákat](mongodb-samples.md) az Azure Cosmos DB MongoDB-hoz kapcsolódó API-jával.

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[multiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png
[dbleveltp]: ./media/mongodb-mongoose/db-level-throughput.png
