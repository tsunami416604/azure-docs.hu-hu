---
title: Tranzakciók küldése az Azure Blockchain szolgáltatással
description: Útmutató arról, hogyan használható az Azure Blockchain Service egy intelligens szerződés üzembe helyezésére és egy privát tranzakció elküldésére.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 3cfbbdc5b95d1607738b132980320d2ff7c99788
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698386"
---
# <a name="tutorial-send-transactions-using-azure-blockchain-service"></a>Oktatóanyag: Tranzakciók küldése az Azure Blockchain szolgáltatással

Ebben az oktatóanyagban tranzakciós csomópontokat kell létrehoznia a szerződés és a tranzakció-adatvédelem teszteléséhez.  A szarvasgomba helyi fejlesztési környezetet hoz létre, és egy intelligens szerződést helyez üzembe, és privát tranzakciót küld.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Tranzakciós csomópontok hozzáadása
> * A szarvasgomba használata intelligens szerződés üzembe helyezéséhez
> * Tranzakció küldése
> * Tranzakció-adatvédelem ellenőrzése

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Blockchain-tag létrehozása a Azure Portal használatával](create-member.md)
* Teljes [gyors útmutató: A szarvasgomba használata a konzorciumi hálózathoz való kapcsolódáshoz](connect-truffle.md)
* A [szarvasgomba](https://github.com/trufflesuite/truffle)telepítése. A szarvasgombához több eszközt kell telepíteni, beleértve a [Node. js](https://nodejs.org)-t, a [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)-t.
* Telepítse a [Python-2.7.15](https://www.python.org/downloads/release/python-2715/). A Python szükséges a Web3.
* A [Visual Studio Code](https://code.visualstudio.com/Download) telepítése
* A [Visual Studio Code soliding bővítmény](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity) telepítése

## <a name="create-transaction-nodes"></a>Tranzakciós csomópontok létrehozása

Alapértelmezés szerint egy tranzakciós csomóponttal rendelkezik. További kettőt fogunk hozzáadni. Az egyik csomópont részt vesz a privát tranzakcióban. A másik nem szerepel a privát tranzakcióban.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon az Azure Blockchain-taghoz, és válassza a **tranzakciós csomópontok > Hozzáadás**elemet.
1. Hajtsa végre a nevű `alpha`új tranzakciós csomópont beállításait.

    ![Tranzakciós csomópont létrehozása](./media/send-transaction/create-node.png)

    | Beállítás | Érték | Leírás |
    |---------|-------|-------------|
    | Name (Név) | `alpha` | Tranzakciós csomópont neve. A név a tranzakciós csomópont végpontjának DNS-címeinek létrehozására szolgál. Például: `alpha-mymanagedledger.blockchain.azure.com`. |
    | Windows 10 | Erős jelszó | A jelszó a tranzakciós csomópont végpontjának egyszerű hitelesítéssel való elérésére szolgál.

1. Kattintson a **Létrehozás** gombra.

    Az új tranzakciós csomópont kiépítés körülbelül 10 percet vesz igénybe.

1. Ismételje meg a 2 – 4. lépést egy nevű `beta`tranzakciós csomópont hozzáadásához.

A csomópontok kiépítés közben folytathatja az oktatóanyagot. Ha elkészült a kiépítés, három tranzakciós csomóponttal fog rendelkezni.

## <a name="open-truffle-console"></a>A szarvasgomba konzol megnyitása

1. Nyisson meg egy Node. js parancssort vagy rendszerhéjat.
1. Módosítsa a szarvasgomba projekt könyvtárának elérési útját [az előfeltételként szükséges rövid útmutatóból: A szarvasgomba segítségével csatlakozhat egy konzorciumi](connect-truffle.md)hálózathoz. Például:

    ```bash
    cd truffledemo
    ```

1. Az alapértelmezett tranzakciós csomóponthoz való kapcsolódáshoz használja a szarvasgomba konzolt.

    ``` bash
    truffle console --network defaultnode
    ```

    A szarvasgomba csatlakozik az alapértelmezett tranzakciós csomóponthoz, és egy interaktív konzolt biztosít.

## <a name="create-ethereum-account"></a>Ethereum-fiók létrehozása

A Web3 használatával kapcsolódjon az alapértelmezett tranzakciós csomóponthoz, és hozzon létre egy Ethereum-fiókot. A Web3 objektum metódusait hívhatja a tranzakciós csomóponttal való interakcióhoz.

1. Hozzon létre egy új fiókot az alapértelmezett tranzakciós csomóponton. Cserélje le a Password paramétert a saját erős jelszavára.

    ```bash
    web3.eth.personal.newAccount("1@myStrongPassword");
    ```

    Jegyezze fel a visszaadott fiók címet és a jelszót. A következő szakaszban a Ethereum fiókjának címe és jelszava szükséges.

1. Lépjen ki a szarvasgomba fejlesztői környezetből.

    ```bash
    .exit
    ```

## <a name="configure-truffle-project"></a>Szarvasgomba-projekt konfigurálása

A szarvasgomba projekt konfigurálásához szükség van néhány tranzakciós csomópontra a Azure Portal.

### <a name="transaction-node-public-key"></a>Tranzakciós csomópont nyilvános kulcsa

Minden tranzakciós csomóponthoz tartozik egy nyilvános kulcs. A nyilvános kulcs lehetővé teszi, hogy privát tranzakciót küldjön a csomópontnak. Ahhoz, hogy tranzakciót küldjön az alapértelmezett tranzakciós csomópontról az *Alpha* Transaction csomópontra, szüksége lesz az *Alpha* tranzakciós csomópont nyilvános kulcsára.

A nyilvános kulcsot a tranzakciós csomópontok listájából kérheti le. Másolja az Alpha csomópont nyilvános kulcsát, és mentse az értéket később az oktatóanyagban.

![Tranzakciós csomópontok listája](./media/send-transaction/node-list.png)

### <a name="transaction-node-endpoint-addresses"></a>Tranzakciós csomópont végpontjának címei

1. A Azure Portal navigáljon minden tranzakciós csomóponthoz, és válassza a **tranzakciós csomópontok >** a kapcsolódási karakterláncok lehetőséget.
1. Másolja és mentse a végponti URL-címet a https-ről **(1. hozzáférési kulcs)** minden tranzakciós csomóponthoz. Az oktatóanyag későbbi részében szüksége lesz az intelligens szerződés konfigurációs fájljához tartozó végponti címekre.

    ![Tranzakció végpontjának címe](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>Konfigurációs fájl szerkesztése

1. Indítsa el a Visual Studio Code-ot, és nyissa meg a szarvasgomba Project Directory mappát a **fájl > mappa megnyitása** menü használatával.
1. Nyissa meg a szarvasgomba konfigurációs fájlt `truffle-config.js`.
1. Cserélje le a fájl tartalmát a következő konfigurációs adatokra. Adja hozzá a végpontok címét és a fiókadatok adatait tartalmazó változókat. Cserélje le a szögletes zárójelet tartalmazó szakaszt az előző fejezetekben összegyűjtött értékekre.

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
          gasPrice: 0,
          from: myAccount
        },
        alpha: {
          provider: new Web3.providers.HttpProvider(alpha),
          network_id: "*",
        },
        beta: {
          provider: new Web3.providers.HttpProvider(beta),
          network_id: "*",
        }
      },
      compilers: {
        solc: {
          evmVersion: "byzantium"
        }
      }
    }
    ```

1. Mentse a módosításokat a `truffle-config.js`következőre:.

## <a name="create-smart-contract"></a>Intelligens szerződés létrehozása

1. A **szerződések** mappában hozzon létre egy nevű `SimpleStorage.sol`új fájlt. Adja hozzá a következő kódot.

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
    
1. Az **áttelepítés** mappában hozzon létre egy nevű `2_deploy_simplestorage.js`új fájlt. Adja hozzá a következő kódot.

    ```solidity
    var SimpleStorage = artifacts.require("SimpleStorage.sol");
    
    module.exports = function(deployer) {
    
      // Pass 42 to the contract as the first constructor parameter
      deployer.deploy(SimpleStorage, "42", {privateFor: ["<alpha node public key>"], from:"<Ethereum account address>"})  
    };
    ```

1. Cserélje le az értékeket a szögletes zárójelek között.

    | Value | Leírás
    |-------|-------------
    | \<Alfa-csomópont nyilvános kulcsa\> | Az Alpha csomópont nyilvános kulcsa
    | \<Ethereum-fiók címe\> | Az alapértelmezett tranzakciós csomópontban létrehozott Ethereum-fiók címe

    Ebben a példában a **storeData** érték kezdeti értéke 42-re van állítva.

    a **privateFor** meghatározza azokat a csomópontokat, amelyekre a szerződés elérhető. Ebben a példában az alapértelmezett tranzakciós csomópont fiókja létrehozhat privát tranzakciókat az **Alpha** -csomópontba. Nyilvános kulcsokat adhat hozzá az összes privát tranzakció résztvevője számára. Ha nem tartalmazza a **privateFor:** és a **következőt:** , az intelligens szerződési tranzakciók nyilvánosak, és minden konzorcium tagjai láthatják.

1. Mentse az összes fájlt a **fájl > az összes mentése**lehetőség kiválasztásával.

## <a name="deploy-smart-contract"></a>Intelligens szerződés üzembe helyezése

A szarvasgomba használatával `SimpleStorage.sol` telepítse az alapértelmezett tranzakciós csomópont-hálózatot.

```bash
truffle migrate --network defaultnode
```

A szarvasgomba Először lefordítja, majd üzembe helyezi a **SimpleStorage** intelligens szerződést.

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

## <a name="validate-contract-privacy"></a>A szerződés adatainak ellenőrzése

A szerződéses adatok védelme miatt a **privateFor**csak az általunk deklarált csomópontokból lehet lekérdezni. Ebben a példában lekérdezjük az alapértelmezett tranzakciós csomópontot, mert a fiók létezik ebben a csomópontban. 

1. A szarvasgomba konzol használatával kapcsolódjon az alapértelmezett tranzakciós csomóponthoz.

    ```bash
    truffle console --network defaultnode
    ```

1. A szarvasgomba konzolon hajtsa végre a szerződés példányának értékét visszaadó kódot.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Ha az alapértelmezett tranzakciós csomópont lekérdezése sikeres, a 42 értéket adja vissza. Példa:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network defaultnode
    truffle(defaultnode)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    '42'
    ```

1. Lépjen ki a szarvasgomba konzolból.

    ```bash
    .exit
    ```

Mivel a **privateFor**-ben deklaráljuk az **Alpha** Node nyilvános kulcsát, lekérhetjük az **Alpha** csomópontot.

1. A szarvasgomba konzol használatával kapcsolódjon az **Alpha** -csomóponthoz.

    ```bash
    truffle console --network alpha
    ```

1. A szarvasgomba konzolon hajtsa végre a szerződés példányának értékét visszaadó kódot.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Ha az **Alpha** -csomópont lekérdezése sikeres, a 42 értéket adja vissza. Példa:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network alpha
    truffle(alpha)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    '42'
    ```

1. Lépjen ki a szarvasgomba konzolból.

    ```bash
    .exit
    ```

Mivel nem deklaráljuk a **béta** -csomópont nyilvános kulcsát a **privateFor**-ben, nem tudjuk lekérdezni a **béta** -csomópontot a szerződés adatainak védelme miatt.

1. A szarvasgomba konzol használatával kapcsolódjon a **bétaverziós** csomóponthoz.

    ```bash
    truffle console --network beta
    ```

1. Hajtson végre egy olyan kódot, amely visszaadja a szerződés példányának értékét.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

1. A **béta** -csomópont lekérdezése sikertelen, mert a szerződés privát. Példa:

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

1. Lépjen ki a szarvasgomba konzolból.

    ```bash
    .exit
    ```
    
## <a name="send-a-transaction"></a>Tranzakció küldése

1. Hozzon létre egy `sampletx.js`nevű fájlt. Mentse a projekt gyökerébe.
1. A következő parancsfájl a szerződés **storedData** változó értékét 65-re állítja. Adja hozzá a kódot az új fájlhoz.

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

    Cserélje le a szögletes zárójelek értékeit, majd mentse a fájlt.

    | Value | Leírás
    |-------|-------------
    | \<Alfa-csomópont nyilvános kulcsa\> | Az Alpha csomópont nyilvános kulcsa
    | \<Ethereum-fiók címe\> | Az alapértelmezett tranzakciós csomópontban létrehozott Ethereum-fiók címe.

    a **privateFor** azokat a csomópontokat határozza meg, amelyekhez a tranzakció elérhető. Ebben a példában az alapértelmezett tranzakciós csomópont fiókja létrehozhat privát tranzakciókat az **Alpha** -csomópontba. Nyilvános kulcsokat kell hozzáadnia az összes privát tranzakció résztvevője számára.

1. A szarvasgomba használatával hajtsa végre az alapértelmezett tranzakciós csomópont parancsfájlját.

    ```bash
    truffle exec sampletx.js --network defaultnode
    ```

1. A szarvasgomba konzolon hajtsa végre a szerződés példányának értékét visszaadó kódot.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Ha a tranzakció sikeres volt, a rendszer a 65 értéket adja vissza. Példa:
    
    ```
    Getting deployed version of SimpleStorage...
    Setting value to 65...
    Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
    Finished!
    ```

1. Lépjen ki a szarvasgomba konzolból.

    ```bash
    .exit
    ```
    
## <a name="validate-transaction-privacy"></a>Tranzakció-adatvédelem ellenőrzése

A tranzakciós adatvédelem miatt a tranzakciókat csak a **privateFor**-ben deklarált csomópontokon lehet végrehajtani. Ebben a példában tranzakciókat lehet elvégezni, mivel az **Alpha** csomópont nyilvános kulcsát a **privateFor**-ben deklaráljuk. 

1. A szarvasgomba használatával hajtsa végre a tranzakciót az **Alpha** -csomóponton.

    ```bash
    truffle exec sampletx.js --network alpha
    ```
    
1. A szerződési példány értékét visszaadó kód végrehajtása.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```
    
    Ha a tranzakció sikeres volt, a rendszer a 65 értéket adja vissza. Példa:

    ```
    Getting deployed version of SimpleStorage...
    Setting value to 65...
    Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
    Finished!
    ```
    
1. Lépjen ki a szarvasgomba konzolból.

    ```bash
    .exit
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti az erőforrásokat az Azure Blockchain `myResourceGroup` szolgáltatás által létrehozott erőforráscsoport törlésével.

Az erőforráscsoport törlése:

1. A Azure Portalban navigáljon az **erőforráscsoporthoz** a bal oldali navigációs ablaktáblán, és válassza ki a törölni kívánt erőforráscsoportot.
1. Válassza az **Erőforráscsoport törlése** elemet. A törlés ellenőrzéséhez írja be az erőforráscsoport nevét, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban két tranzakciós csomópontot adott hozzá a szerződés és a tranzakciós adatvédelem bemutatásához. Az alapértelmezett csomópontot használta egy privát intelligens szerződés üzembe helyezéséhez. Az adatvédelmet a szerződések értékeinek lekérdezésével és a blockchain tranzakcióinak elküldésével tesztelte.

> [!div class="nextstepaction"]
> [Blockchain-alkalmazások fejlesztése az Azure Blockchain Service használatával](develop.md)
