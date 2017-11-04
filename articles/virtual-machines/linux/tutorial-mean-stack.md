---
title: "Hozzon létre egy Linux virtuális Gépet az Azure-ban a verem |} Microsoft Docs"
description: "Ismerje meg, hogyan lehet létrehozni a MongoDB, az Express, az AngularJS és a Node.js (átlag) verem egy Linux virtuális Gépet az Azure-ban."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/08/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: 1d74ead08dfb63276afb08bdcb7f4e3e3db5bfd3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-mongodb-express-angularjs-and-nodejs-mean-stack-on-a-linux-vm-in-azure"></a>A MongoDB, az Express, az AngularJS és a Node.js (átlag) verem létrehozni a Linux virtuális gép az Azure-ban

Az oktatóanyag bemutatja, hogyan kell beállítani a MongoDB, az Express, az AngularJS és a Node.js (átlag) verem egy Linux virtuális Gépet az Azure-ban. Az Ön által létrehozott átlagos verem lehetővé teszi, hogy hozzáadásával, törlésével és egy adatbázisban könyvek listázása. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Linux rendszerű virtuális gép létrehozása
> * A Node.js telepítése
> * A MongoDB telepítése és a kiszolgáló beállítása
> * Express telepítése és a kiszolgáló-útvonalak beállítása
> * Hozzáférés a AngularJS rendelkező útvonalak
> * Az alkalmazás futtatása

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez az oktatóanyag van szükség, hogy futnak-e az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).


## <a name="create-a-linux-vm"></a>Linux rendszerű virtuális gép létrehozása

Hozzon létre egy erőforráscsoportot a a [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) parancsot, és a Linux virtuális gép létrehozása a [az virtuális gép létrehozása](https://docs.microsoft.com/cli/azure/vm#az_vm_create) parancsot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Az alábbi példában az Azure parancssori felület nevű erőforráscsoport létrehozása *myResourceGroupMEAN* a a *eastus* helyét. A virtuális gép kerül létrehozásra elnevezett *myVM* az SSH-kulcsok, ha még nem léteznek a kulcs alapértelmezett helye. Egy adott készletét kulcsok használatához a--ssh-kulcs-érték lehetőséget.

```azurecli-interactive
az group create --name myResourceGroupMEAN --location eastus
az vm create \
    --resource-group myResourceGroupMEAN \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password 'Azure12345678!' \
    --generate-ssh-keys
az vm open-port --port 3300 --resource-group myResourceGroupMEAN --name myVM
```

A virtuális gép létrehozásakor az Azure parancssori felület információkat jeleníti meg az alábbi példához hasonló: 

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroupMEAN/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.72.77.9",
  "resourceGroup": "myResourceGroupMEAN"
}
```
Jegyezze fel a `publicIpAddress` értékét. Ez a cím használható a virtuális gép eléréséhez.

A következő parancs segítségével a virtuális gép SSH-munkamenetet létrehozni. Győződjön meg arról, hogy a megfelelő nyilvános IP-cím használatára. Ebben a példában a fenti az IP cím volt 13.72.77.9.

```bash
ssh azureuser@13.72.77.9
```

## <a name="install-nodejs"></a>A Node.js telepítése

[NODE.js](https://nodejs.org/en/) a JavaScript futásidejű Chrome tartozó V8 JavaScript motor épül. NODE.js szolgál ebben az oktatóanyagban az Express útvonalak és AngularJS vezérlők beállítása.

A virtuális gépet a rendszerhéjakba az SSH-megnyitott telepítse a Node.js.

```bash
sudo apt-get install -y nodejs
```

## <a name="install-mongodb-and-set-up-the-server"></a>A MongoDB telepítése és a kiszolgáló beállítása
[MongoDB](http://www.mongodb.com) rugalmas, JSON-jellegű dokumentumok tárolja az adatokat. Az adatbázis mezőit eltérőek lehetnek a dokumentum dokumentum, és adatszerkezet időbeli módosíthatók. Például az alkalmazás azt hozzá rendszertöltő rekord MongoDB, amelyek tartalmazzák a neve, isbn számát, Szerző és lapok száma. 

1. A virtuális Gépre használja a rendszerhéjakba megnyitott az SSH-kulcsát állítsa a mongodb-Protokolltámogatással.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
    echo "deb [ arch=amd64 ] http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
    ```

2. A csomag kezelőt frissíti, a kulccsal.
  
    ```bash
    sudo apt-get update
    ```

3. A MongoDB telepítése.

    ```bash
    sudo apt-get install -y mongodb
    ```

4. A kiszolgáló elindítására.

    ```bash
    sudo service mongodb start
    ```

5. Azt is telepítenie kell a [törzs-elemző](https://www.npmjs.com/package/body-parser-json) segítsen feldolgozni a kéréseket a kiszolgáló továbbított JSON-csomagot.

    A npm package manager telepítéséhez.

    ```bash
    sudo apt-get install npm
    ```

    A szervezet elemző telepítéséhez.
    
    ```bash
    sudo npm install body-parser
    ```

6. Hozza létre a *könyvek* és felvétele a fájlt nevű *server.js* , amely tartalmazza a konfiguráció a webkiszolgálón.

    ```node.js
    var express = require('express');
    var bodyParser = require('body-parser');
    var app = express();
    app.use(express.static(__dirname + '/public'));
    app.use(bodyParser.json());
    require('./apps/routes')(app);
    app.set('port', 3300);
    app.listen(app.get('port'), function() {
        console.log('Server up: http://localhost:' + app.get('port'));
    });
    ```

## <a name="install-express-and-set-up-routes-to-the-server"></a>Express telepítése és a kiszolgáló-útvonalak beállítása

[Express](https://expressjs.com) van egy minimális igényű és rugalmas Node.js webes alkalmazás-keretrendszer, amely funkciókat biztosít a webes és mobilalkalmazásokhoz. Express ebben az oktatóanyagban használt továbbítani felé és felől a MongoDB adatbázis foglalható le. [Mongoose](http://mongoosejs.com) az alkalmazás adatok egyszerű feladat, a séma-alapú megoldást kínál. Mongoose segítségével ebben az oktatóanyagban biztosítsa a könyv sémát az adatbázisban.

1. Telepítse az Express és a mongoose bővítményt.

    ```bash
    sudo npm install express mongoose
    ```

2. A a *könyvek* mappában hozza létre a *alkalmazások* és nevű fájl hozzáadása *routes.js* meghatározott expressz felé mutató útvonalakat.

    ```node.js
    var Book = require('./models/book');
    module.exports = function(app) {
      app.get('/book', function(req, res) {
        Book.find({}, function(err, result) {
          if ( err ) throw err;
          res.json(result);
        });
      }); 
      app.post('/book', function(req, res) {
        var book = new Book( {
          name:req.body.name,
          isbn:req.body.isbn,
          author:req.body.author,
          pages:req.body.pages
        });
        book.save(function(err, result) {
          if ( err ) throw err;
          res.json( {
            message:"Successfully added book",
            book:result
          });
        });
      });
      app.delete("/book/:isbn", function(req, res) {
        Book.findOneAndRemove(req.query, function(err, result) {
          if ( err ) throw err;
          res.json( {
            message: "Successfully deleted the book",
            book: result
          });
        });
      });
      var path = require('path');
      app.get('*', function(req, res) {
        res.sendfile(path.join(__dirname + '/public', 'index.html'));
      });
    };
    ```

3. A a *alkalmazások* mappában hozza létre a *modellek* , és adja hozzá a nevű fájl *book.js* definiált könyv modell beállítások használatával.  

    ```node.js
    var mongoose = require('mongoose');
    var dbHost = 'mongodb://localhost:27017/test';
    mongoose.connect(dbHost);
    mongoose.connection;
    mongoose.set('debug', true);
    var bookSchema = mongoose.Schema( {
      name: String,
      isbn: {type: String, index: true},
      author: String,
      pages: Number
    });
    var Book = mongoose.model('Book', bookSchema);
    module.exports = mongoose.model('Book', bookSchema); 
    ```

## <a name="access-the-routes-with-angularjs"></a>Hozzáférés a AngularJS rendelkező útvonalak

[AngularJS](https://angularjs.org) webes keretet biztosít a dinamikus nézetek létrehozása a webes alkalmazások. Ebben az oktatóanyagban a AngularJS csatlakozzanak a weblap expressz, és elvégezheti a kapcsolódó műveleteket a könyv adatbázis használjuk.

1. A könyvtár biztonsági másolatot a módosítás *könyvek* (`cd ../..`), és ezután hozza létre a *nyilvános* , és adja hozzá a nevű fájl *script.js* definiált vezérlő beállítások használatával.

    ```node.js
    var app = angular.module('myApp', []);
    app.controller('myCtrl', function($scope, $http) {
      $http( {
        method: 'GET',
        url: '/book'
      }).then(function successCallback(response) {
        $scope.books = response.data;
      }, function errorCallback(response) {
        console.log('Error: ' + response);
      });
      $scope.del_book = function(book) {
        $http( {
          method: 'DELETE',
          url: '/book/:isbn',
          params: {'isbn': book.isbn}
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
      $scope.add_book = function() {
        var body = '{ "name": "' + $scope.Name + 
        '", "isbn": "' + $scope.Isbn +
        '", "author": "' + $scope.Author + 
        '", "pages": "' + $scope.Pages + '" }';
        $http({
          method: 'POST',
          url: '/book',
          data: body
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
    });
    ```
    
2. Az a *nyilvános* mappa, hozzon létre egy fájlt *index.html* definiált weblapon.

    ```html
    <!doctype html>
    <html ng-app="myApp" ng-controller="myCtrl">
      <head>
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
        <script src="script.js"></script>
      </head>
      <body>
        <div>
          <table>
            <tr>
              <td>Name:</td> 
              <td><input type="text" ng-model="Name"></td>
            </tr>
            <tr>
              <td>Isbn:</td>
              <td><input type="text" ng-model="Isbn"></td>
            </tr>
            <tr>
              <td>Author:</td> 
              <td><input type="text" ng-model="Author"></td>
            </tr>
            <tr>
              <td>Pages:</td>
              <td><input type="number" ng-model="Pages"></td>
            </tr>
          </table>
          <button ng-click="add_book()">Add</button>
        </div>
        <hr>
        <div>
          <table>
            <tr>
              <th>Name</th>
              <th>Isbn</th>
              <th>Author</th>
              <th>Pages</th>
            </tr>
            <tr ng-repeat="book in books">
              <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
              <td>{{book.name}}</td>
              <td>{{book.isbn}}</td>
              <td>{{book.author}}</td>
              <td>{{book.pages}}</td>
            </tr>
          </table>
        </div>
      </body>
    </html>
    ```

##  <a name="run-the-application"></a>Az alkalmazás futtatása

1. A könyvtár biztonsági másolatot a módosítás *könyvek* (`cd ..`) és a kiszolgáló elindítására a következő parancs futtatásával:

    ```bash
    nodejs server.js
    ```

2. Nyissa meg a címet, amely a virtuális gép rögzített egy webböngészőben. Például *http://13.72.77.9:3300*. A következő lap hasonlót kell megjelennie:

    ![Rendszertöltő rekord](media/tutorial-mean/meanstack-init.png)

3. Adja meg az adatokat a szövegmezőből, majd kattintson a **Hozzáadás**. Példa:

    ![Rendszertöltő rekord hozzáadása](media/tutorial-mean/meanstack-add.png)

4. A lap frissítésével, megtekintheti az ezen a lapon hasonlót:

    ![Lista könyv rekordok](media/tutorial-mean/meanstack-list.png)

5. Sikerült kattintson **törlése** és a rendszertöltő rekord eltávolítja az adatbázisból.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy webes alkalmazás, amely nyomon követi a rekordok könyv egy létrehozott egy Linux virtuális Gépet a verem. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Linux rendszerű virtuális gép létrehozása
> * A Node.js telepítése
> * A MongoDB telepítése és a kiszolgáló beállítása
> * Express telepítése és a kiszolgáló-útvonalak beállítása
> * Hozzáférés a AngularJS rendelkező útvonalak
> * Az alkalmazás futtatása

A következő oktatóanyag megtudhatja, mennyire biztonságos SSL-tanúsítványokkal webkiszolgálók továbblépés.

> [!div class="nextstepaction"]
> [Biztonságos webkiszolgáló SSL](tutorial-secure-web-server.md)
