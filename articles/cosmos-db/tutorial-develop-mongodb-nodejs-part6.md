---
title: "MongoDB, Angular és Node oktatóanyag az Azure-hoz – 6. rész | Microsoft Docs"
description: "A MongoDB-alkalmazások Azure Cosmos DB adatbázison Angular és Node használatával, a MongoDB-hez használt API-kkal való létrehozását ismertető oktatóanyag-sorozat 6. része."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 09/05/2017
ms.author: mimig
ms.openlocfilehash: 4dee49f99118cc99c21ce23e32db3686c58cf4a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-6-add-post-put-and-delete-functions-to-the-app"></a>MongoDB-alkalmazás létrehozása az Angular és az Azure Cosmos DB használatával – 6. rész: Post, Put és Delete függvények hozzáadása az alkalmazáshoz

Ez a többrészes oktatóanyag bemutatja, hogyan hozhat létre egy Node.js-ben írt új [MongoDB API](mongodb-introduction.md)-alkalmazást az Express és az Angular használatával, és hogyan csatlakoztathatja az Azure Cosmos DB-adatbázisához.

Az oktatóanyag 6. része az [5. részre](tutorial-develop-mongodb-nodejs-part5.md) épül, és az alábbi feladatokat ismerteti:

> [!div class="checklist"]
> * Post, Put és Delete függvények létrehozása a főképszolgáltatáshoz
> * Az alkalmazás futtatása

> [!VIDEO https://www.youtube.com/embed/Y5mdAlFGZjc]

## <a name="prerequisites"></a>Előfeltételek

Ennek a résznek a megkezdése előtt mindenképp végezze el az oktatóanyag [5. részében](tutorial-develop-mongodb-nodejs-part5.md) ismertetett lépéseket.

> [!TIP]
> Az oktatóanyag lépésről lépésre végigvezeti az alkalmazás létrehozásának lépésein. Ha le szeretné tölteni a kész projektet, a kész alkalmazást az [angular-cosmosdb adattárból](https://github.com/Azure-Samples/angular-cosmosdb) töltheti le a GitHubról.

## <a name="add-a-post-function-to-the-hero-service"></a>Post függvény hozzáadása a főképszolgáltatáshoz

1. A Visual Studio Code-ban nyissa meg a **routes.js** és a **hero.service.js** fájlt egymás mellett a **Split Editor** (Osztott szerkesztő) gomb megnyomásával ![Osztott szerkesztő gomb a Visual Studióban](./media/tutorial-develop-mongodb-nodejs-part6/split-editor-button.png).

    Láthatja, hogy a routes.js 7. sora meghívja a `getHeroes` függvényt a **hero.service.js** 5. sorában.  Ugyanilyen párosítást kell létrehoznunk a Post, Put és Delete függvényekhez. 

    ![A routes.js és a hero.service.js a Visual Studio Code-ban](./media/tutorial-develop-mongodb-nodejs-part6/routes-heroservicejs.png)
    
    Kezdjük a főképszolgáltatás kódjával. 

2. Másolja az alábbi kódot a **hero.service.js** fájlba a `getHeroes` függvény után és a `module.exports` elé. Ez a kód:  
   * A főképmodell használatával közzétesz egy új fő képet.
   * Figyeli a válaszokat, hogy nincsenek-e hibák, és visszaad egy 500-as állapotértéket.

   ```javascript
   function postHero(req, res) {
     const originalHero = { id: req.body.id, name: req.body.name, saying: req.body.saying };
     const hero = new Hero(originalHero);
     hero.save(error => {
       if (checkServerError(res, error)) return;
       res.status(201).json(hero);
       console.log('Hero created successfully!');
     });
   }

   function checkServerError(res, error) {
     if (error) {
       res.status(500).send(error);
       return error;
     }
   }
   ```

3. A **hero.service.js** fájlban frissítse a `module.exports` részt, hogy tartalmazza az új `postHero` függvényt. 

    ```javascript
    module.exports = {
      getHeroes,
      postHero
    };
    ```

4. A **routes.js** fájlban adjon hozzá egy útválasztót a `post` függvény számára a `get` útválasztó után. Ez az útválasztó egyszerre egy fő képet tesz közzé. Ha az útválasztó fájlt így strukturálja, egyértelműen látható lesz az összes elérhető API-végpont, és a tényleges munka a **hero.service.js** fájlra marad.

    ```javascript
    router.post('/hero', (req, res) => {
      heroService.postHero(req, res);
    });
    ```

5. Az alkalmazást futtatva ellenőrizze, hogy minden működik-e. A Visual Studio Code-ban mentse az összes módosítást, kattintson a **Debug** (Hibakeresés) gombra ![Hibakeresés ikon a Visual Studio Code-ban](./media/tutorial-develop-mongodb-nodejs-part6/debug-button.png) a bal oldalon, majd a **Start Debugging** (Hibakeresés indítása) gombra ![Hibakeresés indítása ikon a Visual Studio Code-ban](./media/tutorial-develop-mongodb-nodejs-part6/start-debugging-button.png).

6. Most lépjen vissza a webböngészőjére, és nyissa meg a Fejlesztői eszközök Hálózat lapját – ehhez a legtöbb gépen az F12 billentyűt kell lenyomni. Lépjen a [http://localhost:3000](http://localhost:3000) helyre, és tekintse meg a hálózaton keresztül végzett hívásokat.

    ![Hálózat lap a Chrome böngészőben a hálózati tevékenységgel](./media/tutorial-develop-mongodb-nodejs-part6/add-new-hero.png)

7. Adjon hozzá egy új fő képet az **Add New Hero** (Új fő kép hozzáadása) gombra kattintva. Adja meg a „999” azonosítót, a „Fred” nevet és a „Hello” üzenetet, majd kattintson a **Save** (Mentés) gombra. A Hálózat lapon látható, hogy POST-kérést küldött egy új fő képre vonatkozóan. 

    ![Hálózat lap a Chrome böngészőben a Get és Post függvények hálózati tevékenységével](./media/tutorial-develop-mongodb-nodejs-part6/post-new-hero.png)

    Most térjünk vissza a szerkesztőre, és adjuk hozzá a Put és Delete függvényeket az alkalmazáshoz.

## <a name="add-the-put-and-delete-functions"></a>A Put és Delete függvények hozzáadása

1. A **routes.js** fájlban adja hozzá a `put` és `delete` útválasztókat a post útválasztó után.

    ```javascript
    router.put('/hero/:id', (req, res) => {
      heroService.putHero(req, res);
    });

    router.delete('/hero/:id', (req, res) => {
      heroService.deleteHero(req, res);
    });
    ```

2. Másolja az alábbi kódot a **hero.service.js** fájlba a `checkServerError` függvény után. Ez a kód:
   * Létrehozza a `put` és `delete` függvényeket
   * Ellenőrzi, hogy a fő kép megtalálható-e
   * Elvégzi a hibakezelést 

   ```javascript
   function putHero(req, res) {
     const originalHero = {
       id: parseInt(req.params.id, 10),
       name: req.body.name,
       saying: req.body.saying
     };
     Hero.findOne({ id: originalHero.id }, (error, hero) => {
       if (checkServerError(res, error)) return;
       if (!checkFound(res, hero)) return;

      hero.name = originalHero.name;
       hero.saying = originalHero.saying;
       hero.save(error => {
         if (checkServerError(res, error)) return;
         res.status(200).json(hero);
         console.log('Hero updated successfully!');
       });
     });
   }

   function deleteHero(req, res) {
     const id = parseInt(req.params.id, 10);
     Hero.findOneAndRemove({ id: id })
       .then(hero => {
         if (!checkFound(res, hero)) return;
         res.status(200).json(hero);
         console.log('Hero deleted successfully!');
       })
       .catch(error => {
         if (checkServerError(res, error)) return;
       });
   }

   function checkFound(res, hero) {
     if (!hero) {
       res.status(404).send('Hero not found.');
       return;
     }
     return hero;
   }
   ```

3. A **hero.service.js** fájlban exportálja az új modulokat:

   ```javascript
    module.exports = {
      getHeroes,
      postHero,
      putHero,
      deleteHero
    };
    ```

4. Most, hogy frissítettük a kódot, kattintson az **Restart** (Újraindítás) gombra ![Újraindítás gomb a Visual Studio Code-ban](./media/tutorial-develop-mongodb-nodejs-part6/restart-debugger-button.png) a Visual Studio Code-ban.

5. Frissítse az oldalt a webböngészőben, és kattintson az **Add New Hero** (Új fő kép hozzáadása) gombra. Adjon hozzá egy új fő képet a „9” azonosítóval, a „Starlord” névvel és a „Hi” üzenettel. Az új fő kép mentéséhez kattintson a **Save** (Mentés) gombra.

6. Most jelölje ki a **Starlord** fő képet, és a „Hi” üzenet helyett adja meg a „Bye” üzenetet, majd kattintson a **Save** (Mentés) gombra. 

    Most a Hálózat lapon az azonosítót kiválasztva megtekintheti a csomag hasznos adatait. A hasznos adatokban láthatja, hogy az üzenet most már a „Bye”.

    ![Fő képek alkalmazás és a Hálózat lap a hasznos adatokkal](./media/tutorial-develop-mongodb-nodejs-part6/put-hero-function.png) 

    A felhasználói felületen törölheti is az egyik fő képet, és láthatja, hogy a törlési műveletek végrehajtása mennyi ideig tart. Ennek kipróbálásához kattintson a „Delete” (Törlés) gombra a „Fred” nevű fő képnél.

    ![Főképalkalmazás és a Hálózat lap a függvények végrehajtásához szükséges időkkel](./media/tutorial-develop-mongodb-nodejs-part6/times.png) 

    Ha frissíti az oldalt, a Hálózat lap a fő képek lekéréséhez szükséges időt mutatja. Bár az itt látható idők rövidek, rengeteg múlik azon, hogy az adatok a világ mely részén vannak tárolva, és hogy azokat a felhasználókhoz közeli helyekre tudja-e georeplikálni. A georeplikációról további információkat a nemsokára elérhető következő oktatóanyagban talál majd.

## <a name="next-steps"></a>Következő lépések

Az oktatóanyagnak ebben a részében a következőket hajtotta végre:

> [!div class="checklist"]
> * Post, Put és Delete függvényeket adott hozzá az alkalmazáshoz 

Térjen vissza később az oktatóanyag-sorozat következő videoanyagaiért.

