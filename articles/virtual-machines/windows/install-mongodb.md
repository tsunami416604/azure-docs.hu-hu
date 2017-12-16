---
title: "A MongoDB telepítése a Windows Azure-ban |} Microsoft Docs"
description: "Útmutató a MongoDB telepítése egy Azure virtuális gépen futó Windows Server 2012 R2 a Resource Manager üzembe helyezési modellel létrehozott."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 53faf630-8da5-4955-8d0b-6e829bf30cba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: f3fe9751467a1fc34f4e9d02855c4aff307424a3
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2017
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Telepítse és konfigurálja a Windows Azure-ban mongodb-Protokolltámogatással
[MongoDB](http://www.mongodb.org) egy népszerű nyílt forráskódú, nagy teljesítményű NoSQL-adatbázis. Ez a cikk végigvezeti telepítése és konfigurálása a MongoDB a Windows Server 2016-os virtuális gépen (VM) az Azure-ban. Emellett [a MongoDB telepítése egy Linux virtuális gépre az Azure-ban](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Előfeltételek
Előtt telepítése és konfigurálása a MongoDB, hozzon létre egy virtuális Gépet, és ideális esetben hozzá adatlemezt rá szüksége. Hozzon létre egy virtuális Gépet, és hozzá adatlemezt a következő cikkekben talál:

* Hozzon létre egy Windows Server virtuális gép az [az Azure-portálon](quick-create-portal.md) vagy [Azure PowerShell](quick-create-powershell.md).
* Egy Windows Server virtuális gép használata adatlemezzel [az Azure-portálon](attach-managed-disk-portal.md) vagy [Azure PowerShell](attach-disk-ps.md).

MongoDB telepítése és konfigurálása, megkezdéséhez [jelentkezzen be a Windows Server virtuális gép](connect-logon.md) távoli asztal használatával.

## <a name="install-mongodb"></a>A MongoDB telepítése
> [!IMPORTANT]
> Alapértelmezés szerint nem engedélyezettek a MongoDB biztonsági funkciók, például hitelesítés és az IP-cím kötés. Biztonsági szolgáltatások éles környezetben MongoDB telepítése előtt engedélyezni kell. További információkért lásd: [MongoDB biztonsági és hitelesítési](http://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. Miután csatlakozott a virtuális géphez a távoli asztal használatával, nyissa meg az Internet Explorer a tálcán.
2. Válassza ki **az ajánlott biztonsági, adatvédelmi és kompatibilitási beállítások** Ha Internet Explorer először, majd kattintson az **OK**.
3. Internet Explorer fokozott biztonsági beállításai alapértelmezés szerint engedélyezve van. A MongoDB-webhely hozzáadása az engedélyezett helyek listájához:
   
   * Válassza ki a **eszközök** a jobb felső sarokban látható ikonra.
   * A **Internetbeállítások**, jelölje be a **biztonsági** lapra, majd válassza ki a **megbízható helyek** ikonra.
   * Kattintson a **helyek** gombra. Adja hozzá *https://\*. mongodb.com* a megbízható helyek listáján, majd zárja be a párbeszédpanelt.
     
     ![Internet Explorer biztonsági beállításainak konfigurálása](./media/install-mongodb/configure-internet-explorer-security.png)
4. Keresse meg a [tölti le a MongoDB -](http://www.mongodb.com/downloads) (http://www.mongodb.com/downloads) lap.
5. Ha szükséges, jelölje be a **közösségi Server** edition, és válassza ki a legújabb aktuális stabil kiadását*Windows Server 2008 R2 64 bites és a későbbi*. A telepítő letöltéséhez kattintson **letöltési (msi)**.
   
    ![Töltse le a MongoDB-telepítő](./media/install-mongodb/download-mongodb.png)
   
    A telepítő futtatásához a letöltés befejezése után.
6. Olvassa el és fogadja el a licencszerződést. Amikor a rendszer kéri, válassza ki a **Complete** telepítése.
7. Igény szerint kiválaszthatja: körvonal, a mongodb-protokolltámogatással egy grafikus felülettel is telepítheti.
8. Az utolsó képernyő, kattintson **telepítése**.

## <a name="configure-the-vm-and-mongodb"></a>A virtuális gép és a MongoDB konfigurálása
1. Az elérésiút-változók nem frissülnek a MongoDB-telepítővel. A MongoDB nélkül `bin` helyre a path változóban, meg kell adni a teljes elérési útja a MongoDB végrehajtható fájl használata során. A hely hozzáadása a path változóban:
   
   * Kattintson a jobb gombbal a **Start** menüben, és válassza a **rendszer**.
   * Kattintson a **Speciális rendszerbeállítások**, és kattintson a **környezeti változók**.
   * A **rendszerváltozók**, jelölje be **elérési**, és kattintson a **szerkesztése**.
     
     ![Az ELÉRÉSIÚT-változókat konfigurálása](./media/install-mongodb/configure-path-variables.png)
     
     Az elérési út hozzáadása a MongoDB `bin` mappa. MongoDB telepítése általában a *C:\Program Files\MongoDB*. Ellenőrizze a telepítési útvonalat a virtuális gépen. A következő példakóddal felveheti a MongoDB telepítési helyet az alapértelmezett a `PATH` változó:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > Vegye fel a vezető pontosvessző (`;`) jelzi, hogy egy helyet a hozzáadni kívánt a `PATH` változó.

2. Az adatok a lemezen létrehozni a MongoDB adatainak és naplókönyvtárainak könyvtárak. Az a **Start** menüjében válassza **parancssor**. Az alábbi példák a könyvtárak létrehozása F: meghajtón
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Indítsa el a MongoDB-példány beállítása az adatok elérési útja, a következő paranccsal, és ennek megfelelően jelentkezzen könyvtárak:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    A napló fájlok hozzárendelni, és indítsa el a kapcsolatfigyelést mongodb több percig is eltarthat. Összes naplózási üzenetek vannak átirányítva a *F:\MongoLogs\mongolog.log* tárolási `mongod.exe` server elindul, és lefoglalja a Adatbázisnapló-fájlok.
   
   > [!NOTE]
   > A parancssor marad összpontosítanak ezt a feladatot, a MongoDB-példány futtatása közben. Hagyja nyitva a továbbra is fut a MongoDB a parancssori ablakot. Vagy telepítse a MongoDB szolgáltatásként, ahogy az a következő lépésben.

4. Robusztusabb MongoDB élmény érdekében telepítse a `mongod.exe` szolgáltatásként. Szolgáltatás létrehozása azt jelenti, hogy nem kell minden alkalommal, amikor a MongoDB használni kívánt futtató parancssorba hagyja. A szolgáltatás létrehozása az alábbiak szerint ennek megfelelően beállítja a adatainak és naplókönyvtárainak könyvtár elérési útja:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    Az előző parancs létrehoz egy "Mongo DB" leíró MongoDB, nevű szolgáltatást. A következő paraméterek is meg vannak adva:
   
   * A `--dbpath` lehetőséget a data könyvtárának helyét adja meg.
   * A `--logpath` kell beállítással adja meg a naplófájlt, mert a futó szolgáltatás nem rendelkezik egy parancsablakot kimenet megjelenítése.
   * A `--logappend` beállítás megadja, hogy a szolgáltatás újraindítását okozza-e a kimeneti hozzáfűzése a meglévő naplófájl.
   
   A MongoDB szolgáltatás elindításához futtassa a következő parancsot:
   
    ```
    net start MongoDB
    ```
   
    A MongoDB szolgáltatás létrehozásával kapcsolatos további információkért lásd: [egy Windows-szolgáltatás konfigurálása a mongodb-protokolltámogatással](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>A MongoDB-példány tesztelése
Egy példányban fut, vagy a szolgáltatásként telepített mongodb programot, és most elindíthatja létrehozása és használata az adatbázisokat. A MongoDB felügyeleti rendszerhéj elindításához nyisson meg egy másik parancssort a a **Start** menü, és adja meg a következő parancsot:

```
mongo  
```

Az adatbázisok listázhatja a `db` parancsot. Adatok beszúrása az alábbiak szerint:

```
db.foo.insert( { a : 1 } )
```

Adatok keresése az alábbiak szerint:

```
db.foo.find()
```

A kimenet a következő példához hasonló:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Kilépés a `mongo` konzolon a következőképpen:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Konfigurálja a tűzfal és a hálózati biztonsági csoportszabályok
Most, hogy a MongoDB telepítve és fut, nyissa meg a portot a Windows tűzfal így távolról csatlakozhatnak a MongoDB. Hozzon létre egy új bejövő szabály, amely engedélyezi a TCP-port 27017, nyisson meg egy rendszergazda PowerShell-parancssort, és adja meg a következő parancsot:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

A szabály használatával is létrehozhat a **fokozott biztonságú Windows tűzfal** grafikus kezelőeszköz. Hozzon létre egy új bejövő szabály, amely engedélyezi a TCP-port 27017.

Szükség esetén hozzon létre egy hálózati biztonsági csoport szabályt, amely a mongodb-t a hozzáférést a meglévő Azure virtuális hálózati alhálózat kívül. A hálózati biztonsági csoportszabályok segítségével létrehozható a [Azure-portálon](nsg-quickstart-portal.md) vagy [Azure PowerShell](nsg-quickstart-powershell.md). A Windows tűzfal-szabályokat a 27017 TCP-portot, a virtuális hálózati adapter a MongoDB VM engedélyezése.

> [!NOTE]
> TCP-port 27017 MongoDB által használt alapértelmezett port. A port használatával módosíthatja a `--port` paraméter indításakor `mongod.exe` manuálisan, vagy egy szolgáltatást. Ha módosítja a port, módosítsa úgy az előző lépéseket a Windows tűzfal és a hálózati biztonsági csoport szabályainak.


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtudta, hogyan telepítheti és konfigurálhatja a Windows virtuális gép MongoDB. Most már elérheti az MongoDB a Windows virtuális Gépre, a következő speciális témakörei a [MongoDB dokumentációt](https://docs.mongodb.com/manual/).

