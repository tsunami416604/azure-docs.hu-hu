---
title: Az Azure Blockchain-szolgáltatás használatával tranzakciók küldése
description: Arra vonatkozó útmutató, hogyan használhatja az Azure Blockchain-szolgáltatás üzembe helyezése egy intelligens szerződés és a egy privát tranzakció küldése.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 9037c7b5498a5e0a37b05e5ee09891bf8066393d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417487"
---
# <a name="tutorial-send-transactions-using-azure-blockchain-service"></a>Oktatóanyag: Az Azure Blockchain-szolgáltatás használatával tranzakciók küldése

Ebben az oktatóanyagban megismerkedhet létrehozása tranzakció-csomópontok szerződés és a tranzakciós adatvédelmi teszteléséhez.  Hozzon létre egy helyi fejlesztési környezetet, és üzembe helyezése egy intelligens szerződés és egy privát tranzakció küldése Truffle fogja használni.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Tranzakció csomópontok hozzáadása
> * Truffle használatával olyan intelligens szerződés üzembe helyezése
> * Egy tranzakció küldése
> * Tranzakció adatvédelmi ellenőrzése

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Teljes [az Azure portal használatával a blockchain-tag létrehozása](create-member.md)
* Teljes [a rövid útmutató: Csatlakozhat egy consortium network Truffle használatával](connect-truffle.md)
* Telepítés [Truffle](https://github.com/trufflesuite/truffle). Truffle igényel a telepítendő eszközöket is beleértve [Node.js](https://nodejs.org), [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Telepítés [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python weben 3 van szükség.
* Telepítés [Visual Studio Code-ot](https://code.visualstudio.com/Download)
* Telepítés [Visual Studio Code Solidity bővítmény](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity)

## <a name="create-transaction-nodes"></a>Hozzon létre tranzakciós csomópontok

Alapértelmezés szerint egy tranzakció csomópontja rendelkezik. Adjon hozzá két további fogunk. Az egyik csomópontot a privát tranzakcióban részt vesz. A másik nem szerepel a privát tranzakció.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Az Azure Blockchain tag keresse meg és válassza **tranzakció csomópontok > Hozzáadás**.
1. Végezze el a beállításokat egy új tranzakció csomópont nevű `alpha`.

    ![Tranzakció csomópont létrehozása](./media/send-transaction/create-node.png)

    | Beállítás | Value (Díj) | Leírás |
    |---------|-------|-------------|
    | Name (Név) | `alpha` | Tranzakció csomópont neve. A név szolgál a DNS-címe, a tranzakciós csomópont-végpont létrehozása. Például: `alpha-mymanagedledger.blockchain.azure.com`. |
    | Jelszó | erős jelszó | A jelszót a tranzakció-csomópont végpontot az egyszerű hitelesítéssel használatos.

1. Kattintson a **Létrehozás** gombra.

    Új tranzakció csomópont kiépítése körülbelül 10 percet vesz igénybe.

1. Ismételje meg a 2 – 4 nevű tranzakció csomópont hozzáadása `beta`.

Folytathatja az oktatóanyag során a csomópontok lettek létrehozva. Amikor a kiépítés befejeződött, akkor három tranzakció-csomópont.

## <a name="open-truffle-console"></a>Truffle konzol megnyitása

1. Nyissa meg a Node.js parancssort vagy a rendszerhéjat.
1. Az elérési utat módosítsa Truffle projektkönyvtárára az előfeltételként felsorolt [a rövid útmutató: Truffle segítségével csatlakoztathatók a consortium](connect-truffle.md). Például:

    ```bash
    cd truffledemo
    ```

1. Interaktív fejlesztői konzol indítása Truffle.

    ``` bash
    truffle develop
    ```

    Truffle létrehoz egy helyi fejlesztési blockchain, és a egy interaktív konzolt kínál.

## <a name="create-ethereum-account"></a>Ethereum-fiók létrehozása

Weben 3 segítségével az alapértelmezett tranzakció a csomóponthoz csatlakozás és a egy Ethereum-fiók létrehozása. Megtekintheti a weben 3 kapcsolati karakterláncot az Azure Portalról.

1. Az Azure Portalon keresse meg az alapértelmezett tranzakció csomópont, és válassza ki **tranzakció csomópontok > mintakód > weben 3**.
1. Másolja a JavaScript a **HTTPS (hozzáférési kulcs: 1)** ![weben 3 mintakód](./media/send-transaction/web3-code.png)

1. Az alapértelmezett tranzakció csomópont a weben 3 JavaScript-kódokat illessze be a Truffle interaktív fejlesztői konzolt. A kód létrehoz egy weben 3-objektumot, amely az Azure Blockchain-szolgáltatás tranzakció csomópont csatlakozik.

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    ```

    Módszerek segítségével meghívhatja a tranzakció csomópont kommunikáljon a weben 3 objektumon.

1. Hozzon létre egy új fiókot az alapértelmezett tranzakció csomóponton. Cserélje le a saját erős jelszót a password paramétert.

    ```bash
    web3.eth.personal.newAccount("1@myStrongPassword");
    ```

    Győződjön meg arról, jegyezze fel, hogy a visszaadott fiók címét és jelszavát. Az Ethereum-fiók címét és jelszavát a következő szakaszban van szüksége.

1. Lépjen ki a Truffle fejlesztési környezethez.

    ```bash
    .exit
    ```

## <a name="configure-truffle-project"></a>Truffle projekt konfigurálása

Konfigurálja a Truffle, információra van szüksége bizonyos tranzakció csomópont az Azure Portalról.

### <a name="transaction-node-public-key"></a>Tranzakció csomópont nyilvános kulcs

Minden tranzakció csomópont nyilvános kulccsal rendelkezik. A nyilvános kulcs lehetővé teszi, hogy a csomópont egy privát tranzakció küldhet. Egy tranzakció az alapértelmezett tranzakció csomópontból történő küldéséhez a *alpha* tranzakció csomópont, szüksége a *alpha* tranzakció csomópont nyilvános kulcsot.

A nyilvános kulcsot beszerezheti a tranzakció csomópont listából. Másolja a nyilvános kulcsot az alfa csomóponthoz, és mentse az értéket az oktatóanyag későbbi részében.

![Tranzakció node list](./media/send-transaction/node-list.png)

### <a name="transaction-node-endpoint-addresses"></a>Tranzakció csomópont végpontcímeket

1. Az Azure Portalon minden egyes tranzakció csomópontjának, és válassza ki **tranzakció csomópontok > kapcsolati karakterláncok**.
1. Másolja ki és mentse a végponti URL-cím a **HTTPS (hozzáférési kulcs: 1)** minden tranzakció csomópont esetében. Az oktatóanyag későbbi részében szüksége az intelligens szerződés konfigurációs fájlt a végponti címeket.

    ![Tranzakció-végpont címe](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>Konfigurációs fájl szerkesztése

1. Indítsa el a Visual Studio Code-ot, és nyissa meg a Truffle projektet könyvtár mappa a a **fájl > mappa megnyitása** menü.
1. Nyissa meg a Truffle konfigurációs fájlt `truffle-config.js`.
1. Cserélje le a fájl tartalmát a következő konfigurációs adatokat. Adja hozzá a végpontok címek és a fiók adatait tartalmazó változókat. A korábbi szakaszokban összegyűjtött értékeket cserélje le a csúcsos zárójel szakaszok.

    ``` javascript
    var defaultnode = "<default transaction node connection string>";
    var alpha = "<alpha transaction node connection string>";
    var beta = "<beta transaction node connection string>";
    
    var myAccount = "<Ethereum account address>";
    var myPassword = "<Ethereum account password>";
    
    var Web3 = require("web3");
    
    module.exports = {
      networks: {
        defaultnode: {
          provider:(() =>  {
          const AzureBlockchainProvider = new Web3.providers.HttpProvider(defaultnode);
    
          const web3 = new Web3(AzureBlockchainProvider);
          web3.eth.personal.unlockAccount(myAccount, myPassword);
    
          return AzureBlockchainProvider;
          })(),
    
          network_id: "*",
          gas: 0,
          gasPrice: 0,
          from: myAccount
        },
        alpha: {
          provider: new Web3.providers.HttpProvider(alpha),
          network_id: "*",
          gas: 0,
          gasPrice: 0
        },
        beta: {
          provider: new Web3.providers.HttpProvider(beta),
          network_id: "*",
          gas: 0,
          gasPrice: 0
        }
      }
    }
    ```

1. A módosítások mentése `truffle-config.js`.

## <a name="create-smart-contract"></a>Az intelligens szerződés létrehozása

1. Az a **szerződések** mappában hozzon létre egy új fájlt `SimpleStorage.sol`. Adja hozzá a következő kódot.

    ```solidity
    pragma solidity >=0.4.21 <0.6.0;
    
    contract SimpleStorage {
        string public storedData;
    
        constructor(string memory initVal) public {
            storedData = initVal;
        }
    
        function set(string memory x) public {
            storedData = x;
        }
    
        function get() view public returns (string memory retVal) {
            return storedData;
        }
    }
    ```
    
1. Az a **áttelepítések** mappában hozzon létre egy új fájlt `2_deploy_simplestorage.js`. Adja hozzá a következő kódot.

    ```solidity
    var SimpleStorage = artifacts.require("SimpleStorage.sol");
    
    module.exports = function(deployer) {
    
      // Pass 42 to the contract as the first constructor parameter
      deployer.deploy(SimpleStorage, "42", {privateFor: ["<alpha node public key>"], from:"<Ethereum account address>"})  
    };
    ```

1. Cserélje le a csúcsos zárójeleket értékeit.

    | Érték | Leírás
    |-------|-------------
    | \<az alfa csomópont nyilvános kulcs\> | Az alfa csomópont nyilvános kulcs
    | \<Ethereum fiók címe\> | Az alapértelmezett tranzakció csomópontjában létrehozott Ethereum fiók címe

    Ebben a példában a kezdeti értéke, a **storeData** 42 értékre van állítva.

    **privateFor** határozza meg a csomópontok, amelyhez a szerződést érhető el. Ebben a példában a fiók az alapértelmezett tranzakció csomópont leadott is privát tranzakciók a **alpha** csomópont. Nyilvános kulcsok a résztvevők saját tranzakció adja hozzá. Ha nem adja meg **privateFor:** és **származó:** , az intelligens szerződés tranzakciók nyilvános, és minden consortium tag számára láthatók.

1. Mentse a fájlokat kiválasztásával **fájl > Mentés összes**.

## <a name="deploy-smart-contract"></a>Az intelligens szerződés üzembe helyezése

Truffle segítségével üzembe `SimpleStorage.sol` alapértelmezett tranzakció csomópont hálózathoz.

```bash
truffle migrate --network defaultnode
```

Truffle először összeállítja és majd üzembe helyezi a **SimpleStorage** intelligens szerződés.

Példa a kimenetre:

```
admin@desktop:/mnt/c/truffledemo$ truffle migrate --network defaultnode

2_deploy_simplestorage.js
=========================

   Deploying 'SimpleStorage'
   -------------------------
   > transaction hash:    0x3f695ff225e7d11a0239ffcaaab0d5f72adb545912693a77fbfc11c0dbe7ba72
   > Blocks: 2            Seconds: 12
   > contract address:    0x0b15c15C739c1F3C1e041ef70E0011e641C9D763
   > account:             0x1a0B9683B449A8FcAd294A01E881c90c734735C3
   > balance:             0
   > gas used:            0
   > gas price:           0 gwei
   > value sent:          0 ETH
   > total cost:          0 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:                   0 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0 ETH
```

## <a name="validate-contract-privacy"></a>Szerződéses adatvédelmi ellenőrzése

Szerződéses adatvédelmi, mert szerződés értéke csak kérdezhetők le azt a deklarált csomópontjáról **privateFor**. Ebben a példában azt is lekérdezni az alapértelmezett tranzakció csomópont, mert a fiók létezik csomópontban. 

1. Az alapértelmezett tranzakció a csomóponthoz csatlakozás az Truffle konzol használatával.

    ```bash
    truffle console --network defaultnode
    ```

1. A Truffle konzolon végrehajtható egy programkód, amely a szerződés példány értékét adja vissza.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Ha az alapértelmezett tranzakció csomópont lekérdezése sikeres volt, a 42 értéket adja vissza. Példa:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network defaultnode
    truffle(defaultnode)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    '42'
    ```

1. Lépjen ki a Truffle konzol.

    ```bash
    .exit
    ```

Mivel hogy **alpha** csomópont található nyilvános kulcs **privateFor**, lekérdezheti, hogy a **alpha** csomópont.

1. A Truffle konzol használatával csatlakozni a **alpha** csomópont.

    ```bash
    truffle console --network alpha
    ```

1. A Truffle konzolon végrehajtható egy programkód, amely a szerződés példány értékét adja vissza.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Ha a lekérdezés a **alpha** csomópont művelet sikeres, a 42 értéket adja vissza. Példa:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network alpha
    truffle(alpha)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    '42'
    ```

1. Lépjen ki a Truffle konzol.

    ```bash
    .exit
    ```

Mivel mi nem deklarálható **béta** csomópont található nyilvános kulcs **privateFor**, nem fogjuk tudni lekérdezni a **béta** csomópont szerződéses adatvédelmi miatt.

1. A Truffle konzol használatával csatlakozni a **béta** csomópont.

    ```bash
    truffle console --network beta
    ```

1. Hajtsa végre a kódot, amely a szerződés példány értékét adja vissza.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

1. Lekérdezés a **béta** csomópont meghibásodik, mivel a szerződés nem nyilvános. Példa:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network beta
    truffle(beta)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    Thrown:
    Error: Returned values aren't valid, did it run Out of Gas?
        at XMLHttpRequest._onHttpResponseEnd (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:345:8)
        at XMLHttpRequest._setReadyState (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:219:8)
        at XMLHttpRequestEventTarget.dispatchEvent (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request-event-target.ts:44:13)
        at XMLHttpRequest.request.onreadystatechange (/mnt/c/truffledemo/node_modules/web3-providers-http/src/index.js:96:13)
    ```

1. Lépjen ki a Truffle konzol.

    ```bash
    .exit
    ```
    
## <a name="send-a-transaction"></a>Egy tranzakció küldése

1. Hozzon létre egy fájlt nevű `sampletx.js`. Mentse a projekt gyökérkönyvtárában található.
1. A következő szkript állítja be a szerződés **storedData** 65 változó értékét. Adja hozzá a kódot az új fájlt.

    ```javascript
    var SimpleStorage = artifacts.require("SimpleStorage");
    
    module.exports = function(done) {
      console.log("Getting deployed version of SimpleStorage...")
      SimpleStorage.deployed().then(function(instance) {
        console.log("Setting value to 65...");
        return instance.set("65", {privateFor: ["<alpha node public key>"], from:"<Ethereum account address>"});
      }).then(function(result) {
        console.log("Transaction:", result.tx);
        console.log("Finished!");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

    Cserélje le a csúcsos zárójeleket értékeit, majd mentse a fájlt.

    | Érték | Leírás
    |-------|-------------
    | \<az alfa csomópont nyilvános kulcs\> | Az alfa csomópont nyilvános kulcs
    | \<Ethereum fiók címe\> | Ethereum-fiók cím alapértelmezett tranzakció csomópontjában létrehozott.

    **privateFor** határozza meg a csomópontok, amelyhez a tranzakció áll rendelkezésre. Ebben a példában a fiók az alapértelmezett tranzakció csomópont leadott is privát tranzakciók a **alpha** csomópont. Az összes titkos tranzakció résztvevők nyilvános kulcsok hozzá kell.

1. A szkript az alapértelmezett tranzakció csomóponthoz Truffle használja.

    ```bash
    truffle exec sampletx.js --network defaultnode
    ```

1. A Truffle konzolon végrehajtható egy programkód, amely a szerződés példány értékét adja vissza.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Ha a tranzakció sikeres volt, a 65 értéket adja vissza. Példa:
    
    ```
    Getting deployed version of SimpleStorage...
    Setting value to 65...
    Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
    Finished!
    ```

1. Lépjen ki a Truffle konzol.

    ```bash
    .exit
    ```
    
## <a name="validate-transaction-privacy"></a>Tranzakció adatvédelmi ellenőrzése

Miatt adatvédelmi tranzakció, tranzakciók csak végezhető azt deklarálva a csomópontokon **privateFor**. Ebben a példában is végrehajthatjuk tranzakciók óta hogy **alpha** csomópont található nyilvános kulcs **privateFor**. 

1. Hajtsa végre a tranzakció Truffle használatával a **alpha** csomópont.

    ```bash
    truffle exec sampletx.js --network alpha
    ```
    
1. Hajtsa végre a kódot, amely a szerződés példány értékét adja vissza.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```
    
    Ha a tranzakció sikeres volt, a 65 értéket adja vissza. Példa:

    ```
    Getting deployed version of SimpleStorage...
    Setting value to 65...
    Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
    Finished!
    ```
    
1. Lépjen ki a Truffle konzol.

    ```bash
    .exit
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti az erőforrások törlése a `myResourceGroup` létrehozott erőforráscsoportot az Azure Blockchain-szolgáltatás.

Az erőforráscsoport törléséhez:

1. Az Azure Portalon lépjen **erőforráscsoport** a bal oldali navigációs panelen, és válassza ki a törölni kívánt erőforráscsoportot.
1. Válassza az **Erőforráscsoport törlése** elemet. Az erőforráscsoport nevének megadásával erősítse meg a törlést, és válassza ki **törlése**.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a szerződés és a tranzakciós adatvédelmi bemutatása kettőre tranzakciók hozzá. Az alapértelmezett csomópont egy privát intelligens szerződés üzembe helyezéséhez használt. Adatvédelmi lekérdezését szerződés értékek és a blockchain teljesítő tranzakciók által tesztelt.

> [!div class="nextstepaction"]
> [Blockchain-alkalmazások fejlesztéséhez az Azure Blockchain-szolgáltatás használatával](develop.md)
