---
title: "MongoDB, Angular és Node oktatóanyag az Azure-hoz – 2. rész | Microsoft Docs"
description: "A MongoDB-alkalmazások Azure Cosmos DB adatbázison Angular és Node használatával, a MongoDB-hez használt API-kkal való létrehozását ismertető oktatóanyag-sorozat 2. része."
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
ms.translationtype: HT
ms.sourcegitcommit: 4eb426b14ec72aaa79268840f23a39b15fee8982
ms.openlocfilehash: 48b7da57b23dbd16571c8fa179efd900cdcf21ad
ms.contentlocale: hu-hu
ms.lasthandoff: 09/06/2017

---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-2-create-a-nodejs-express-app-with-the-angular-cli"></a>MongoDB-alkalmazás létrehozása az Angular és az Azure Cosmos DB használatával – 2. rész: Node.js Express alkalmazás létrehozása az Angular CLI parancssori felülettel 

Ez a többrészes oktatóanyag bemutatja, hogyan hozhat létre egy Node.js-ben írt új [MongoDB API](mongodb-introduction.md)-alkalmazást az Express, az Angular és az Azure Cosmos DB-adatbázis használatával.

Az oktatóanyag 2. része a [bevezető részre](tutorial-develop-mongodb-nodejs.md) épül, és az alábbi feladatokat ismerteti:

> [!div class="checklist"]
> * Az Angular CLI és a TypeScript telepítése
> * Új projekt létrehozása az Angular használatával
> * Az alkalmazás felépítése az Express keretrendszer használatával
> * Az alkalmazás tesztelése a Postmanben

## <a name="video-walkthrough"></a>Bemutató videó

> [!VIDEO https://www.youtube.com/embed/lIwJIYcGSUg]

## <a name="prerequisites"></a>Előfeltételek

Ennek a résznek a megkezdése előtt mindenképp tekintse meg a [bevezető videót](tutorial-develop-mongodb-nodejs.md).

Az oktatóanyaghoz a következőkre is szükség lesz: 
* A [Node.js](https://nodejs.org/) 8.4.0 vagy újabb verziója.
* [Postman](https://www.getpostman.com/)
* A [Visual Studio Code](https://code.visualstudio.com/) vagy egyéb előnyben részesített kódszerkesztő.

> [!TIP]
> Az oktatóanyag lépésről lépésre végigvezeti az alkalmazás létrehozásának lépésein. Ha le szeretné tölteni a kész projektet, a kész alkalmazást az [angular-cosmosdb adattárból](https://github.com/Azure-Samples/angular-cosmosdb) töltheti le a GitHubról.

## <a name="install-the-angular-cli-and-typescript"></a>Az Angular CLI és a TypeScript telepítése

1. Nyisson meg egy Windows-parancssort vagy Mac-terminálablakot, és telepítse az Angular CLI parancssori felületet.

    ```bash
    npm install -g @angular/cli
    ```

2. A parancssorban az alábbi parancs megadásával telepítse a TypeScriptet. 

    ```bash
    npm install -g typescript
    ```

## <a name="use-the-angular-cli-to-create-a-new-project"></a>Az Angular CLI használata új projekt létrehozásához

1. A parancssorban váltson arra a mappára, ahol az új projektet létre szeretné hozni, majd futtassa az alábbi parancsot. A parancs létrehoz egy új mappát és egy angular-cosmosdb elnevezésű projektet, és telepíti az új alkalmazáshoz szükséges Angular-összetevőket. Emellett a forráskódot is telepíti az src/client mappába (-sd src/client), a minimális beállítást alkalmazza (--minimal), és meghatározza, hogy a projekt az Sass nyelvet használja (egy CSS-szerű szintaxist, a --style scss jelölővel).

    ```bash
    ng new angular-cosmosdb -sd src/client --minimal --style scss
    ```

2. Miután a parancs végrehajtása befejeződött, váltson a src/client mappára.

    ```bash
    cd angular-cosmosdb
    ```

3. Nyissa meg a mappát a Visual Studio Code-ban.

    ```bash
    code .
    ```

## <a name="build-the-app-using-the-express-framework"></a>Az alkalmazás létrehozása az Express keretrendszer használatával

1. A Visual Studio Code-ban az **Explorer** panelen kattintson jobb gombbal az **src** mappára, kattintson a **New Folder** (Új mappa) elemre, és adja a *server* nevet az új mappának.

2. Az **Explorer** panelen kattintson jobb gombbal a **server** mappára, majd a **New File** (Új fájl) elemre, és adja az *index.js* nevet az új fájlnak.

3. Lépjen ismét a parancssorra, és a következő paranccsal telepítse a törzselemzőt. Ez segít az alkalmazásnak elemezni az API-kon keresztül átadott JSON-adatokat.

    ```bash
    npm i express body-parser --save
    ```

4. A Visual Studio Code-ban másolja az alábbi kódot az index.js fájlba. Ez a kód:
    * Hivatkozik az Expressre
    * Bekéri a törzselemzőt a JSON-adatok olvasásához a kérések törzsében
    * Az útvonal elnevezésű beépített szolgáltatást használja
    * Beállítja a gyökérváltozókat, hogy könnyebb legyen megtalálni a kód helyét
    * Beállít egy portot
    * Beindítja az Expresst
    * Megadja az alkalmazásnak, hogyan használja a kiszolgáló kiszolgálására használni kívánt közbenső szoftvert
    * Kiszolgál mindent, ami a dist mappában található, azaz a majdani statikus tartalmat
    * Kiszolgálja az alkalmazást, és szolgáltatja az index.html fájlt minden olyan GET kéréshez, amely nem a kiszolgálón található (mélyhivatkozások esetén)
    * Elindítja a kiszolgálót az app.listen használatával
    * Egy nyíl funkció használatával naplózza, hogy a port aktív-e
    
   ```node
   const express = require('express');
   const bodyParser = require('body-parser');
   const path = require('path');
   const routes = require('./routes');

   const root = './';
   const port = process.env.PORT || '3000';
   const app = express();

   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(express.static(path.join(root, 'dist')));
   app.use('/api', routes);
   app.get('*', (req, res) => {
     res.sendFile('dist/index.html', {root});
   });

   app.listen(port, () => console.log(`API running on localhost:${port}`));
   ```

5. A Visual Studio Code-ban az **Explorer** panelen kattintson jobb gombbal a **server** mappára, majd kattintson a **New File** (Új fájl) elemre. Adja a *routes.js* nevet az új fájlnak. 

6. Másolja az alábbi kódot a **routes.js** fájlba. Ez a kód:
   * Hivatkozik az Express útválasztóra
   * Lekéri a fő képeket
   * Visszaküldi a JSON-t egy megadott fő képnek

   ```node
   const express = require('express');
   const router = express.Router();

   router.get('/heroes', (req, res) => {
    res.send(200, [
       {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    ])
   });

   module.exports=router;
   ```

7. Menti az összes módosított fájlt. 

8. A Visual Studio Code-ban kattintson a **Debug** (Hibakeresés) gombra ![Hibakeresés ikon a Visual Studio Code-ban](./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png), kattintson a fogaskerék gombra ![Fogaskerék gomb a Visual Studio Code-ban](./media/tutorial-develop-mongodb-nodejs-part2/gear-button.png), majd válassza a **Node.js** elemet egy konfiguráció létrehozásához.

   Az új launch.json fájl megnyílik a Visual Studio Code-ban.

8. A launch.json fájl 11. sorában módosítsa a `"program": "${file}"` részt a `"program": "${workspaceRoot}/src/server/index.js"` kódra, és mentse a fájlt.

9. Kattintson a **Start Debugging** (Hibakeresés indítása) gombra ![Hibakeresés ikon a Visual Studio Code-ban](./media/tutorial-develop-mongodb-nodejs-part2/start-debugging-button.png) az alkalmazás futtatásához.

    Az alkalmazásnak hiba nélkül kell futnia.

## <a name="use-postman-to-test-the-app"></a>Az alkalmazás tesztelése a Postman használatával

1. Most nyissa meg a Postmant, és másolja a `http://localhost:3000/api/heroes` címet a GET mezőbe. 

2. Kattintson az **Send** (Küldés) gombra, és várja meg az alkalmazástól érkező JSON-választ. 

    A válaszból kiderül, hogy az alkalmazás működik és helyileg fut. 

    ![Postman képe a kéréssel és a válasszal](./media/tutorial-develop-mongodb-nodejs-part2/azure-cosmos-db-postman.png)


## <a name="next-steps"></a>Következő lépések

Az oktatóanyagnak ebben a részében a következőket hajtotta végre:

> [!div class="checklist"]
> * Létrehozott egy Node.js-projektet az Angular CLI parancssori felülettel
> * Tesztelte az alkalmazást a Postman segítségével

Továbbléphet az oktatóanyag következő részére, amelyben létrehozza a felhasználói felületet.

> [!div class="nextstepaction"]
> [A felhasználói felület létrehozása az Angular használatával](tutorial-develop-mongodb-nodejs-part3.md)

