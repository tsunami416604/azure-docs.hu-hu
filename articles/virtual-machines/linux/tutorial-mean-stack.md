---
title: Oktatóanyag – MEAN-verem létrehozása Linux rendszerű virtuális gépen az Azure-ban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre MongoDB-, Express-, AngularJS- és Node.js- (MEAN-) vermet Linux rendszerű virtuális gépen az Azure-ban.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/08/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 97734ad6119e89c00634035b1646bc29ac6549bd
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237857"
---
# <a name="tutorial-create-a-mongodb-express-angularjs-and-nodejs-mean-stack-on-a-linux-virtual-machine-in-azure"></a>Oktatóanyag: Az Azure-ban Linux rendszerű virtuális gépen a MongoDB, Express, AngularJS és Node.js (mean) készlet-verem létrehozása

Ez az oktatóanyag bemutatja, hogyan valósítható meg MongoDB-, Express-, AngularJS- és Node.js- (MEAN-) verem Linux rendszerű virtuális gépen az Azure-ban. A létrehozott MEAN-készlet lehetővé teszi a könyvek hozzáadását, törlését és felsorolását egy adatbázison belül. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Linux rendszerű virtuális gép létrehozása
> * A Node.js telepítése
> * A MongoDB telepítése és a kiszolgáló beállítása
> * Az Express telepítése és útvonalak beállítása a kiszolgálóhoz
> * Az útvonalak elérése AngularJS segítségével
> * Az alkalmazás futtatása

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).


## <a name="create-a-linux-vm"></a>Linux rendszerű virtuális gép létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group), illetve egy Linux rendszerű virtuális gépet az [az vm create](https://docs.microsoft.com/cli/azure/vm) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában egy *myResourceGroupMEAN* nevű erőforráscsoportot hozunk létre az *eastus* helyen az Azure CLI használatával. Ekkor létrejön egy *myVM* nevű virtuális gép SSH-kulcsokkal, ha azok még nem léteznek a kulcsok alapértelmezett helyén. Ha konkrét kulcsokat szeretné használni, használja az --ssh-key-value beállítást.

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

A virtuális gép létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg: 

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

Használja az alábbi parancsot egy SSH-munkamenet létrehozásához a virtuális géphez. Győződjön meg arról, hogy a megfelelő nyilvános IP-címet használja. A fenti példában az IP-cím a következő volt: 13.72.77.9.

```bash
ssh azureuser@13.72.77.9
```

## <a name="install-nodejs"></a>A Node.js telepítése

A [Node.js](https://nodejs.org/en/) egy JavaScript futtatókörnyezet, amely a Chrome 8-as verziójú JavaScript-motorján alapul. Ebben az oktatóanyagban a Node.js használatával állíthat be Express-útvonalakat és AngularJS-vezérlőket.

Telepítse a Node.js futtatókörnyezetet a virtuális gépen az SSH-val megnyitott bash-felületen.

```bash
sudo apt-get install -y nodejs
```

## <a name="install-mongodb-and-set-up-the-server"></a>A MongoDB telepítése és a kiszolgáló beállítása
A [MongoDB](http://www.mongodb.com) rugalmas, JSON-szerű dokumentumokban tárolja az adatokat. Az adatbázisok mezői dokumentumtól függően eltérőek lehetnek, az adatstruktúra pedig idővel változhat. A példaalkalmazás esetében könyvbejegyzéseket fogunk hozzáadni a MongoDB-hez, amelyek tartalmazzák a könyv nevét, ISBN-számát, szerzőjét és az oldalszámot. 

1. A virtuális gépen állítsa be a MongoDB-kulcsot az SSH-val megnyitott bash-felületen.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
    echo "deb [ arch=amd64 ] http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
    ```

2. Frissítse a csomagkezelőt a kulccsal.
  
    ```bash
    sudo apt-get update
    ```

3. Telepítse a MongoDB-t.

    ```bash
    sudo apt-get install -y mongodb
    ```

4. Indítsa el a kiszolgálót.

    ```bash
    sudo service mongodb start
    ```

5. Emellett a [body-parser](https://www.npmjs.com/package/body-parser-json) csomagot is telepíteni kell, amely segít a kiszolgálóhoz továbbított kérésekben szereplő JSON feldolgozásában.

    Telepítse az npm csomagkezelőt.

    ```bash
    sudo apt-get install npm
    ```

    Telepítse a body-parser csomagot.
    
    ```bash
    sudo npm install body-parser
    ```

6. Hozzon létre egy *Books* (Könyvek) nevű mappát, és adjon hozzá egy a webkiszolgáló konfigurációját tartalmazó, *server.js* nevű fájlt.

    ```javascript
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

## <a name="install-express-and-set-up-routes-to-the-server"></a>Az Express telepítése és útvonalak beállítása a kiszolgálóhoz

Az [Express](https://expressjs.com) egy minimális igényű és rugalmas Node.js webalkalmazási keretrendszer, amely különféle funkciókat biztosít a web- és mobilalkalmazások számára. Ebben az oktatóanyagban az Express használatával fogjuk továbbítani a könyvadatokat a MongoDB adatbázisból, illetve az adatbázis felé. A [Mongoose](http://mongoosejs.com) magától értetődő, sémaalapú megoldást biztosít az alkalmazásadatok modellezéséhez. Ebben az oktatóanyagban a Mongoose használatával fogjuk megadni a könyv sémáját az adatbázis számára.

1. Telepítse az Express és Mongoose szolgáltatásokat.

    ```bash
    sudo npm install express mongoose
    ```

2. A *Books* mappában hozzon létre egy *apps* (alkalmazások) nevű mappát, és adjon hozzá egy *routes.js* nevű fájlt, amelyben meg vannak határozva az Express-útvonalak.

    ```javascript
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

3. Az *apps* mappában hozzon létre egy *models* (modellek) nevű mappát, és adjon hozzá egy *book.js* nevű fájlt, amelyben meg van határozva a könyvmodell.  

    ```javascript
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

## <a name="access-the-routes-with-angularjs"></a>Az útvonalak elérése AngularJS segítségével

Az [AngularJS](https://angularjs.org) egy webes keretrendszert biztosít a dinamikus nézetek létrehozásához a webalkalmazáson belül. Ebben az oktatóanyagban az AngularJS használatával csatlakoztatjuk a weboldalt az Express szolgáltatáshoz, és műveleteket hajtunk végre a könyvadatbázison.

1. Módosítsa a könyvtár biztonsági mentésének helyét a *Books* (`cd ../..`) mappára, majd hozzon létre egy *public* (nyilvános) nevű mappát, és adjon hozzá egy *script.js* nevű fájlt, amelyben meg van határozva a vezérlő konfigurációja.

    ```javascript
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
    
2. A *public* mappában hozzon létre egy *index.html* nevű fájlt, amelyben meg van határozva a weboldal.

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

1. Módosítsa a könyvtár biztonsági mentésének helyét a *Books* (`cd ..`) mappára, és indítsa el a kiszolgálót az alábbi parancs futtatásával:

    ```bash
    nodejs server.js
    ```

2. Nyisson meg egy webböngészőt, és ugorjon a virtuális géphez rögzített címre. Például: *http://13.72.77.9:3300*. Az alábbihoz hasonló oldalt kell látnia:

    ![Könyvrekord](media/tutorial-mean/meanstack-init.png)

3. Adjon meg adatokat a szöveges mezőkben, és kattintson az **Add** (Hozzáadás) elemre. Példa:

    ![Könyvrekord hozzáadása](media/tutorial-mean/meanstack-add.png)

4. Az oldal frissítése után az alábbihoz hasonló oldalt kell látnia:

    ![Könyvrekordok listája](media/tutorial-mean/meanstack-list.png)

5. A **Delete** (Törlés) gombra kattintva eltávolíthatja a könyvrekordot az adatbázisból.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy webalkalmazást, amely nyomon követi a könyvrekordokat egy MEAN-készlet használatával egy Linux rendszerű virtuális gépen. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Linux rendszerű virtuális gép létrehozása
> * A Node.js telepítése
> * A MongoDB telepítése és a kiszolgáló beállítása
> * Az Express telepítése és útvonalak beállítása a kiszolgálóhoz
> * Az útvonalak elérése AngularJS segítségével
> * Az alkalmazás futtatása

Folytassa a következő oktatóanyaggal, amelyből megismerheti, hogyan tehetők biztonságossá a webkiszolgálók SSL-tanúsítványok használatával.

> [!div class="nextstepaction"]
> [Webkiszolgáló biztonságossá tétele SSL használatával](tutorial-secure-web-server.md)
