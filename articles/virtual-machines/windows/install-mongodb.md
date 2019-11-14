---
title: A MongoDB telepítése egy Windows rendszerű virtuális gépen az Azure-ban
description: Ismerje meg, hogyan telepítheti a MongoDB-t egy, a Resource Manager-alapú üzemi modellel létrehozott Windows Server 2012 R2 rendszert futtató Azure-beli virtuális gépre.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 53faf630-8da5-4955-8d0b-6e829bf30cba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 37c1b58d364e7eadb33803ce7eac1f2b956ec1b6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038551"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>A MongoDB telepítése és konfigurálása az Azure-beli Windows rendszerű virtuális gépen
A [MongoDB](https://www.mongodb.org) egy népszerű, nyílt forráskódú, nagy teljesítményű NoSQL-adatbázis. Ez a cikk végigvezeti a MongoDB telepítésének és konfigurálásának a Windows Server 2016 virtuális gépen (VM) az Azure-ban. A [MongoDB-et Linux rendszerű virtuális gépre is telepítheti az Azure-ban](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Előfeltételek
A MongoDB telepítése és konfigurálása előtt létre kell hoznia egy virtuális gépet, és ideális esetben hozzá kell adnia egy adatlemezt. A következő cikkekből megtudhatja, hogyan hozhat létre virtuális gépet, és hogyan adhat hozzá adatlemezeket:

* Hozzon létre egy Windows Server rendszerű virtuális gépet [a Azure Portal vagy a](quick-create-portal.md) [Azure PowerShell](quick-create-powershell.md)használatával.
* Adatlemez csatlakoztatása Windows Server rendszerű virtuális géphez [a Azure Portal](attach-managed-disk-portal.md) vagy [Azure PowerShell](attach-disk-ps.md)használatával.

A MongoDB telepítésének és konfigurálásának megkezdéséhez [Jelentkezzen be a Windows Server rendszerű virtuális gépre](connect-logon.md) távoli asztal használatával.

## <a name="install-mongodb"></a>A MongoDB telepítése
> [!IMPORTANT]
> A MongoDB biztonsági funkciói, például a hitelesítés és az IP-cím kötése alapértelmezés szerint nincs engedélyezve. A biztonsági funkciókat engedélyezni kell a MongoDB éles környezetbe való telepítése előtt. További információ: [MongoDB biztonság és hitelesítés](https://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. Miután a Távoli asztal használatával kapcsolódott a virtuális géphez, nyissa meg az Internet Explorert a tálcáról.
2. Válassza az **ajánlott biztonsági, adatvédelmi és kompatibilitási beállítások használata** az Internet Explorer első megnyitásakor lehetőséget, majd kattintson **az OK**gombra.
3. Az Internet Explorer fokozott biztonsági beállításai alapértelmezés szerint engedélyezve vannak. Adja hozzá a MongoDB webhelyét az engedélyezett helyek listájához:
   
   * Válassza az **eszközök** ikont a jobb felső sarokban.
   * Az **Internetbeállítások**területen válassza a **Biztonság** fület, majd válassza a **megbízható helyek** ikont.
   * Kattintson a **helyek** gombra. Adja hozzá a *https://\*. mongodb.com* a megbízható helyek listájához, majd a párbeszédpanel bezárásához.
     
     ![Az Internet Explorer biztonsági beállításainak konfigurálása](./media/install-mongodb/configure-internet-explorer-security.png)
4. Keresse meg a [MongoDB-letöltések](https://www.mongodb.com/downloads) lapot (https://www.mongodb.com/downloads).
5. Ha szükséges, válassza ki a **közösségi kiszolgáló** kiadását, majd válassza ki a*Windows server 2008 R2 64-bit és újabb*verziójának legújabb stabil kiadását. A telepítő letöltéséhez kattintson a **Letöltés (MSI)** elemre.
   
    ![MongoDB-telepítő letöltése](./media/install-mongodb/download-mongodb.png)
   
    A letöltés befejezése után futtassa a telepítőt.
6. Olvassa el és fogadja el a licencszerződést. Ha a rendszer kéri, válassza a **teljes** telepítés lehetőséget.
7. Ha kívánja, dönthet úgy is, hogy az iránytűt is telepíti, amely egy grafikus felület a MongoDB számára.
8. Az utolsó képernyőn kattintson a **telepítés**gombra.

## <a name="configure-the-vm-and-mongodb"></a>A virtuális gép és a MongoDB konfigurálása
1. Az MongoDB-telepítő nem frissíti az elérésiút-változókat. Ha a MongoDB nem `bin` helyet a Path változóban, minden alkalommal meg kell adnia a teljes elérési utat, amikor egy MongoDB-végrehajtható fájlt használ. A hely hozzáadása a Path változóhoz:
   
   * Kattintson a jobb gombbal a **Start** menüre, és válassza a **System (rendszerek**) lehetőséget.
   * Kattintson a **Speciális rendszerbeállítások**elemre, majd kattintson a **környezeti változók**elemre.
   * A **rendszerváltozók**területen válassza az **elérési út**lehetőséget, majd kattintson a **Szerkesztés**gombra.
     
     ![ELÉRÉSIÚT-változók konfigurálása](./media/install-mongodb/configure-path-variables.png)
     
     Adja hozzá az elérési utat a MongoDB `bin` mappához. A MongoDB általában a *C:\Program Files\MongoDB*-ben települ. Ellenőrizze a telepítési útvonalat a virtuális gépen. A következő példa hozzáadja a MongoDB alapértelmezett telepítési helyét a `PATH` változóhoz:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > Ügyeljen arra, hogy a vezető pontosvesszőt (`;`) adja hozzá, hogy jelezze, ha helyet ad hozzá a `PATH` változóhoz.

2. Hozzon létre MongoDB-és naplózási könyvtárakat az adatlemezen. A **Start** menüben válassza a **parancssor**lehetőséget. Az alábbi példák az F meghajtón hozza létre a címtárakat:
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Indítson el egy MongoDB-példányt a következő paranccsal, és ennek megfelelően állítsa be az elérési utat az adataihoz és a naplók könyvtáraihoz:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    Több percet is igénybe vehet, amíg a MongoDB le nem osztja a naplófájlokat, és megkezdheti a kapcsolatok figyelését. Az összes naplózási üzenet a *F:\MongoLogs\mongolog.log* -fájlra van irányítva, mivel `mongod.exe` kiszolgáló elindul, és lefoglalja a napló fájljait.
   
   > [!NOTE]
   > A parancs erre a feladatra koncentrál, amíg a MongoDB-példány fut. A MongoDB futtatásának folytatásához hagyja nyitva a parancssorablakot. Vagy telepítse a MongoDB as Service-t a következő lépésben részletezett módon.

4. A robusztus MongoDB-élmény érdekében telepítse a `mongod.exe` szolgáltatást. A szolgáltatás létrehozása azt jelenti, hogy nem kell minden alkalommal futtatnia a parancssort, amikor a MongoDB-t szeretné használni. A következő módon hozza létre a szolgáltatást, és ennek megfelelően állítsa be az elérési utat az adataihoz és a naplók könyvtáraihoz:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    Az előző parancs létrehoz egy MongoDB nevű szolgáltatást a "Mongo DB" leírásával. A következő paraméterek is meg vannak adva:
   
   * A `--dbpath` beállítás megadja az adatkönyvtár helyét.
   * A `--logpath` kapcsolót kell használni a naplófájl megadásához, mert a futó szolgáltatás nem tartalmaz parancssori ablakot a kimenet megjelenítéséhez.
   * A `--logappend` beállítás azt adja meg, hogy a szolgáltatás újraindítása a kimenet hozzáfűzését okozza a meglévő naplófájlhoz.
   
   A MongoDB szolgáltatás elindításához futtassa a következő parancsot:
   
    ```
    net start MongoDB
    ```
   
    A MongoDB szolgáltatás létrehozásával kapcsolatos további információkért lásd: [Windows-szolgáltatás konfigurálása a MongoDB-hez](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Az MongoDB-példány tesztelése
Ha a MongoDB önálló példányként vagy szolgáltatásként van telepítve, mostantól megkezdheti az adatbázisok létrehozását és használatát. A MongoDB felügyeleti rendszerhéj elindításához nyisson meg egy másik parancssori ablakot a **Start** menüből, és írja be a következő parancsot:

```
mongo  
```

Az adatbázisokat az `db` parancs használatával listázhatja. Szúrjon be néhányat az alábbi módon:

```
db.foo.insert( { a : 1 } )
```

Az alábbi módon keresheti meg az adatkeresést:

```
db.foo.find()
```

A kimenet a következő példához hasonló:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

A következőképpen lépjen ki a `mongo`-konzolról:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>A tűzfal és a hálózati biztonsági csoport szabályainak konfigurálása
Most, hogy a MongoDB telepítve van és fut, nyisson meg egy portot a Windows tűzfalon, hogy távolról is csatlakozhasson a MongoDB. Egy új, a 27017-es TCP-portot engedélyező Bejövő szabály létrehozásához nyisson meg egy rendszergazdai parancssort, és írja be a következő parancsot:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

A szabályt a **fokozott biztonságú Windows tűzfal** -kezelő eszköz használatával is létrehozhatja. Hozzon létre egy új bejövő szabályt a 27017-es TCP-port engedélyezéséhez.

Ha szükséges, hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt, amely lehetővé teszi, hogy a MongoDB a meglévő Azure virtuális hálózat alhálózatán kívülről is hozzáférhessen. A hálózati biztonsági csoport szabályait a [Azure Portal](nsg-quickstart-portal.md) vagy [Azure PowerShell](nsg-quickstart-powershell.md)használatával hozhatja létre. A Windows tűzfal szabályaihoz hasonlóan engedélyezze a 27017-es TCP-portot a MongoDB virtuális gép virtuális hálózati adapteréhez.

> [!NOTE]
> A MongoDB által használt alapértelmezett port a 27017-es TCP-port. Ezt a portot a `--port` paraméterrel módosíthatja, ha `mongod.exe` manuálisan vagy egy szolgáltatásból indítja el. Ha módosítja a portot, ügyeljen arra, hogy frissítse a Windows tűzfal és a hálózati biztonsági csoport szabályait az előző lépésekben.


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtanulta, hogyan telepítheti és konfigurálhatja a MongoDB a Windows rendszerű virtuális gépen. Most már elérheti a MongoDB a Windows rendszerű virtuális gépen a [MongoDB dokumentációjában](https://docs.mongodb.com/manual/)található speciális témakörök követésével.

