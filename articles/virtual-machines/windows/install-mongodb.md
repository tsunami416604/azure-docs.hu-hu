---
title: A MongoDB telepítése a Windows Azure-beli virtuális gép |} A Microsoft Docs
description: Megtudhatja, hogyan telepítheti a MongoDB-beli virtuális gépen, a Resource Manager üzemi modellel létrehozott Windows Server 2012 R2 rendszerű.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 53faf630-8da5-4955-8d0b-6e829bf30cba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: afd8e6b47fb86985acde062af1fb38ec3af4e902
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998497"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Telepítse és konfigurálja a MongoDB egy Windows virtuális gépen az Azure-ban
[MongoDB](https://www.mongodb.org) egy népszerű nyílt forráskódú, nagy teljesítményű NoSQL-adatbázis. Ez a cikk végigvezeti telepítése és konfigurálása a mongodb-hez a Windows Server 2016 virtuális gépen (VM) az Azure-ban. Emellett [a MongoDB telepítése az Azure-beli Linuxos virtuális gép](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Előfeltételek
Mielőtt telepítése és konfigurálása a MongoDB, hozzon létre egy virtuális Gépet, és ideális, adatlemez hozzáadása, szüksége. Hozzon létre egy virtuális Gépet, és adjon hozzá egy adatlemezt a következő cikkekben talál:

* Hozzon létre egy Windows Server rendszerű virtuális gép [az Azure Portalon](quick-create-portal.md) vagy [Azure PowerShell-lel](quick-create-powershell.md).
* Adatlemez csatolása a Windows Server rendszerű virtuális gép használatával [az Azure Portalon](attach-managed-disk-portal.md) vagy [Azure PowerShell-lel](attach-disk-ps.md).

Telepítése és konfigurálása a MongoDB, a kezdéshez [jelentkezzen be a Windows Server rendszerű virtuális gép](connect-logon.md) távoli asztal használatával.

## <a name="install-mongodb"></a>A MongoDB telepítése
> [!IMPORTANT]
> MongoDB biztonsági funkciói, például a hitelesítést és az IP-cím kötés, alapértelmezés szerint nem engedélyezettek. Biztonsági funkciókat engedélyezni kell a mongodb-hez a éles környezetben való üzembe helyezés előtt. További információkért lásd: [MongoDB biztonság és hitelesítés](https://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. Miután csatlakozott a virtuális géphez távoli asztali kapcsolattal, nyissa meg az Internet Explorer a tálcáról.
2. Válassza ki **az ajánlott biztonsági, adatvédelmi és kompatibilitási beállítások** amikor az Internet Explorer először nyitja meg, és kattintson a **OK**.
3. Az Internet Explorer fokozott biztonsági beállításai alapértelmezés szerint engedélyezve van. A MongoDB-webhely hozzáadása az engedélyezett helyek listájához:
   
   * Válassza ki a **eszközök** jobb felső sarokban lévő ikonra.
   * A **Internetbeállítások**, jelölje be a **biztonsági** lapra, és válassza ki a **megbízható helyek** ikonra.
   * Kattintson a **helyek** gombra. Adjon hozzá *https://\*. mongodb.com* a listához, és a megbízható helyek, majd zárja be a párbeszédpanelt.
     
     ![Az Internet Explorer biztonsági beállítások konfigurálása](./media/install-mongodb/configure-internet-explorer-security.png)
4. Keresse meg a [MongoDB - letöltések](https://www.mongodb.com/downloads) lap (https://www.mongodb.com/downloads).
5. Ha szükséges, válassza ki a **Community Server** edition, és válassza ki a legújabb aktuális stabil kibocsátási*Windows Server 2008 R2 64 bites és újabb verziók*. A telepítő letöltéséhez kattintson **letöltése (msi)**.
   
    ![Töltse le a MongoDB-telepítő](./media/install-mongodb/download-mongodb.png)
   
    Futtassa a telepítőt, a letöltés befejezése után.
6. Olvassa el és fogadja el a licencszerződést. Amikor a rendszer kéri, válassza ki a **Complete** telepítése.
7. Ha szükséges, válassza a Compass nevű listáján, a mongodb-hez egy grafikus felülettel is telepítheti.
8. A végső képernyőn kattintson a **telepítése**.

## <a name="configure-the-vm-and-mongodb"></a>Állítsa be a virtuális gép és a mongodb-hez
1. Az elérésiút-változókat nem frissülnek a MongoDB Installer. A MongoDB nélkül `bin` a path változóban egységekhez, meg kell adnia a teljes elérési útja minden alkalommal, amikor egy mongodb-hez végrehajtható fájlt használja. A hely hozzáadása a path változóban:
   
   * Kattintson a jobb gombbal a **Start** menüben, és válassza a **rendszer**.
   * Kattintson a **Speciális rendszerbeállítások**, és kattintson a **környezeti változók**.
   * A **rendszerváltozók**válassza **elérési út**, és kattintson a **szerkesztése**.
     
     ![ELÉRÉSIÚT-változókat konfigurálása](./media/install-mongodb/configure-path-variables.png)
     
     Az elérési út hozzáadása a mongodb-hez `bin` mappát. MongoDB általában telepítve van a *C:\Program Files\MongoDB*. Ellenőrizze a telepítési útvonalat a virtuális Gépen. Az alábbi példa hozzáadja a MongoDB telepítése a hely alapértelmezett a `PATH` változó:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > Adja hozzá a vezető pontosvessző (`;`) jelzi, hogy felveszi azt a helyet, a `PATH` változó.

2. Hozzon létre MongoDB-adat- és naplófájlok könyvtárakat az adatokat lemezen. Az a **Start** menüjében válassza **parancssor**. Az alábbi példák a könyvtárak létrehozása F: meghajtón
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Indítsa el a MongoDB-példányhoz a következő paranccsal, az adatok elérési útja módosításával, és ennek megfelelően jelentkezzen könyvtárak:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    A mongodb-hez az adatbázisnapló-fájlok lefoglalni, és megkezdeni a figyelést kapcsolatok több percig is eltarthat. Az összes naplózási üzeneteket irányítja a *F:\MongoLogs\mongolog.log* fájlt úgy `mongod.exe` kiszolgáló elindul, és lefoglalja az adatbázisnapló-fájlok.
   
   > [!NOTE]
   > A parancssor használatával Ez a feladat összpontosítanak marad, a MongoDB-példány futtatása közben. Hagyja nyitva a továbbra is fut a mongodb-hez a parancssori ablakot. Vagy telepítheti a MongoDB-szolgáltatás, a következő lépésben leírtaknak megfelelően.

4. Egy robusztusabb MongoDB-felület, telepítse a `mongod.exe` szolgáltatásként. Szolgáltatás létrehozása azt jelenti, hogy nem kell, hogy minden alkalommal, amikor a mongodb-hez használni kívánt futtató parancsot a parancssorba. A szolgáltatást a következőképpen hozhat létre, ennek megfelelően módosítani az adat- és naplófájlok könyvtárak elérési útja:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    Az előző parancs létrehoz egy szolgáltatást, MongoDB, nevű "Mongo DB" leírását. A következő paramétereket is meg vannak adva:
   
   * A `--dbpath` lehetőség a adatkönyvtárat helyét adja meg.
   * A `--logpath` kell beállítással adja meg a naplófájlt, mert a futó szolgáltatás nem rendelkezik egy kimeneti megjeleníthető parancsablakot.
   * A `--logappend` beállítás megadja, hogy a szolgáltatás újraindítását okozza-e a kimeneti hozzáfűzése a meglévő naplófájlt.
   
   A MongoDB-szolgáltatás elindításához futtassa a következő parancsot:
   
    ```
    net start MongoDB
    ```
   
    A MongoDB-szolgáltatás létrehozásával kapcsolatos további információkért lásd: [konfigurálhat egy Windows-szolgáltatást a mongodb-hez](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>A MongoDB-példányban tesztelése
A MongoDB egy példányban fut, vagy a szolgáltatásként telepített mostantól létrehozásáról és használatáról az adatbázisokat. A mongodb-hez felügyeleti rendszerhéj elindításához nyisson meg egy másik parancssori ablakban, a **Start** menüben, és adja meg a következő parancsot:

```
mongo  
```

Az adatbázisok listázhatja a `db` parancsot. Adatok beszúrása a következőképpen:

```
db.foo.insert( { a : 1 } )
```

Adatok keresése a következő:

```
db.foo.find()
```

A kimenet a következő példához hasonló:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Kilépés a `mongo` konzol a következőképpen:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Tűzfal- és hálózati biztonsági csoport szabályainak konfigurálása
Most, hogy a mongodb-hez telepítve és fut, nyissa meg egy portot a Windows tűzfal, távolról csatlakozhat a mongodb-hez. Hozzon létre egy új bejövő szabályt, hogy a TCP-port 27017, nyisson meg egy rendszergazdai PowerShell-parancssort, és adja meg a következő parancsot:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

A szabály használatával is létrehozhat a **fokozott biztonságú Windows tűzfal** grafikus kezelőeszköz. Hozzon létre egy új bejövő szabályt, hogy a TCP-port 27017.

Szükség esetén hozzon létre egy hálózati biztonsági csoport szabályt, amely engedélyezi a hozzáférést a mongodb-t a kívül a meglévő Azure virtuális hálózat alhálózatához. A hálózati biztonsági csoportszabályok használatával is létrehozhat a [az Azure portal](nsg-quickstart-portal.md) vagy [Azure PowerShell-lel](nsg-quickstart-powershell.md). Csakúgy, mint a Windows tűzfal-szabályok lehetővé teszik a 27017 TCP-portot, a virtuális hálózati adapter virtuálisgép-mongodb-hez.

> [!NOTE]
> TCP-port 27017 MongoDB által használt alapértelmezett port. Ez a port használatával módosíthatja a `--port` paraméter indításakor `mongod.exe` manuálisan, vagy egy szolgáltatás. Ha módosítja a port, ügyeljen arra, hogy az előző lépésekben a Windows tűzfal- és hálózati biztonsági csoport szabályainak frissítése.


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtudhatta, hogyan telepítése és konfigurálása a mongodb-hez a Windows virtuális Gépen. Most már elérheti mongodb-hez a Windows virtuális Gépen, a következő speciális témakörei a [MongoDB dokumentációt](https://docs.mongodb.com/manual/).

