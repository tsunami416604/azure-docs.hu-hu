---
title: A MongoDB telepítése Windows virtuális gépre az Azure-ban
description: Megtudhatja, hogy miként telepítheti a MongoDB-t egy Windows Server 2012 R2 rendszert futtató, az Erőforrás-kezelő telepítési modellel létrehozott Azure-gépre.
documentationcenter: ''
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: how-to
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: a5ba7d7fce3f3eabd223956ca8d9cc824fbd0c5f
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869452"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>A MongoDB telepítése és konfigurálása Windows virtuális gépen az Azure-ban
[MongoDB](https://www.mongodb.org) egy népszerű nyílt forráskódú, nagy teljesítményű NoSQL adatbázis. Ez a cikk végigvezeti a MongoDB telepítésén és konfigurálásán egy Windows Server 2016 virtuális gépen (VM) az Azure-ban. A [MongoDB linuxos virtuális gépre is telepíthető az Azure-ban.](../linux/install-mongodb.md)

## <a name="prerequisites"></a>Előfeltételek
A MongoDB telepítése és konfigurálása előtt létre kell hoznia egy virtuális gép, és ideális esetben hozzá kell adnia egy adatlemezt. A virtuális gép létrehozásáról és adatlemez hozzáadásáról az alábbi cikkekben adhat meg adatokat:

* Hozzon létre egy Windows Server virtuális gép [az Azure Portalon](quick-create-portal.md) vagy az [Azure PowerShell](quick-create-powershell.md)használatával.
* Adatlemez csatolása Windows Server virtuális géphez [az Azure Portalon](attach-managed-disk-portal.md) vagy az [Azure PowerShellen](attach-disk-ps.md)keresztül.

A MongoDB telepítésének és konfigurálásának [megkezdéséhez jelentkezzen be a Windows Server virtuális gépre a](connect-logon.md) Távoli asztal használatával.

## <a name="install-mongodb"></a>A MongoDB telepítése
> [!IMPORTANT]
> A MongoDB biztonsági szolgáltatásai, például a hitelesítés és az IP-címkötés alapértelmezés szerint nincsenek engedélyezve. A biztonsági funkciókat engedélyezni kell, mielőtt a MongoDB-t éles környezetbe telepítenék. További információ: [MongoDB Security and Authentication](https://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. Miután a Távoli asztal lal csatlakozott a virtuális géphez, nyissa meg az Internet Explorert a tálcáról.
2. Jelölje be **Az Ajánlott biztonsági, adatvédelmi és kompatibilitási beállítások használata** az Internet Explorer első megnyitásakor jelölőnégyzetet, majd kattintson az **OK**gombra.
3. Az Internet Explorer fokozott biztonsági beállításai alapértelmezés szerint engedélyezve vannak. Adja hozzá a MongoDB webhelyet az engedélyezett webhelyek listájához:
   
   * Válassza az **Eszközök** ikont a jobb felső sarokban.
   * Az **Internetbeállítások párbeszédpanelen**válassza a **Biztonság** lapot, majd a **Megbízható helyek** ikont.
   * Kattintson a **Webhelyek** gombra. Vegye fel *https://\*.mongodb.com-t* a megbízható helyek listájára, majd zárja be a párbeszédpanelt.
     
     ![Az Internet Explorer biztonsági beállításainak konfigurálása](./media/install-mongodb/configure-internet-explorer-security.png)
4. Keresse meg a [MongoDB - Letöltések](https://www.mongodb.com/downloads) oldalt (https://www.mongodb.com/downloads).
5. Szükség esetén válassza ki a **Community Server** kiadást, majd válassza ki a Windows*Server 2008 R2 64 bites és újabb*rendszerének legújabb stabil kiadását. A telepítő letöltéséhez kattintson a **LETÖLTÉS (msi) gombra.**
   
    ![A MongoDB telepítő letöltése](./media/install-mongodb/download-mongodb.png)
   
    Futtassa a telepítőt a letöltés befejezése után.
6. Olvassa el és fogadja el a licencszerződést. Amikor a rendszer kéri, válassza a **Telepítés befejezése** lehetőséget.
7. Ha szükséges, akkor választhat, hogy is telepíteni Compass, grafikus felület MongoDB.
8. A végső képernyőn kattintson a **Telepítés gombra.**

## <a name="configure-the-vm-and-mongodb"></a>A virtuális gép és a MongoDB konfigurálása
1. Az elérési út változóit a MongoDB telepítője nem frissíti. A MongoDB `bin` hely nélkül az elérési út változójában meg kell adnia a teljes elérési utat minden alkalommal, amikor MongoDB végrehajtható fájlt használ. A hely hozzáadása az elérési út változóhoz:
   
   * Kattintson a jobb gombbal a **Start** menüre, és válassza **a Rendszer parancsot.**
   * Kattintson **a Speciális rendszerbeállítások gombra,** majd a **Környezeti változók parancsra.**
   * A **Rendszerváltozók csoportban**válassza a **Görbe**lehetőséget, majd kattintson a **Szerkesztés**gombra.
     
     ![PATH-változók konfigurálása](./media/install-mongodb/configure-path-variables.png)
     
     Adja hozzá az elérési utat `bin` a MongoDB mappához. A MongoDB általában a *C:\Program Files\MongoDB*mappában van telepítve. Ellenőrizze a telepítési elérési utat a virtuális gépen. A következő példa hozzáadja az alapértelmezett MongoDB telepítési helyet a `PATH` változóhoz:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > Ügyeljen arra, hogy a`;`kezdő pontosvesszőt ( ) adja `PATH` hozzá annak jelzésére, hogy helyet ad hozzá a változóhoz.

2. MongoDB-adatokat hozhat létre, és naplókönyvtárat hozhat létre az adatlemezen. A **Start** menüben válassza a **Parancssor parancsot**. A következő példák az F meghajtón hozzák létre a könyvtárakat:
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Indítsa el a MongoDB példányt a következő paranccsal, ennek megfelelően állítsa be az adatok és naplókönyvtárak elérési útját:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    A MongoDB több percig is eltarthat a naplófájlok lefoglalása és a kapcsolatok figyelésének megkezdése. A rendszer minden naplóüzenetet az *F:\MongoLogs\mongolog.log* fájlba irányít, ahogy `mongod.exe` a kiszolgáló elindul és lefoglalja a naplófájlokat.
   
   > [!NOTE]
   > A parancssor a MongoDB-példány futása közben is erre a feladatra összpontosít. A MongoDB futtatásának folytatásához hagyja nyitva a parancssorablakot. Vagy telepítse a MongoDB-t szolgáltatásként, a következő lépésben részletezett módon.

4. A mongoDB robusztusabb élményérdekében telepítse a `mongod.exe` szolgáltatást. A szolgáltatás létrehozása azt jelenti, hogy nem kell minden alkalommal futtatnia a parancssort, amikor használni szeretné a MongoDB-t. Hozza létre a szolgáltatást az alábbiak szerint, ennek megfelelően igazítva az adatok elérési útját, és ennek megfelelően naplózza a könyvtárakat:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    Az előző parancs létrehoz egy MongoDB nevű szolgáltatást a "Mongo DB" leírásával. A következő paraméterek is meg vannak adva:
   
   * A `--dbpath` beállítás az adatkönyvtár helyét adja meg.
   * A `--logpath` beállítással naplófájlt kell megadni, mert a futó szolgáltatásnak nincs parancsablaka a kimenet megjelenítéséhez.
   * A `--logappend` beállítás azt határozza meg, hogy a szolgáltatás újraindítása okozza a kimenet hozzáfűzése a meglévő naplófájlhoz.
   
   A MongoDB szolgáltatás elindításához futtassa a következő parancsot:
   
    ```
    net start MongoDB
    ```
   
    A MongoDB szolgáltatás létrehozásáról a [Windows-szolgáltatás konfigurálása a MongoDB-hoz](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service)című témakörben talál további információt.

## <a name="test-the-mongodb-instance"></a>A MongoDB-példány tesztelése
Ha a MongoDB egyetlen példányként fut, vagy szolgáltatásként van telepítve, most már elkezdheti létrehozni és használni az adatbázisokat. A MongoDB felügyeleti rendszerhéj elindításához nyisson meg egy másik parancssorablakot a **Start** menüből, és írja be a következő parancsot:

```
mongo  
```

Az adatbázisokat a `db` paranccsal is felsorolhatja. Szúrjon be néhány adatot az alábbiak szerint:

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

Lépjen `mongo` ki a konzolból az alábbiak szerint:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Tűzfal- és hálózati biztonsági csoportszabályok konfigurálása
Most, hogy a MongoDB telepítve van és fut, nyisson meg egy portot a Windows tűzfalban, így távolról csatlakozhat a MongoDB-hoz. A 27017-es TCP-port engedélyezéséhez új bejövő szabályt szeretne létrehozni, nyisson meg egy felügyeleti PowerShell-parancssort, és írja be a következő parancsot:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

A szabályt a **Fokozott biztonságú Windows tűzfal** grafikus kezelőeszközével is létrehozhatja. Hozzon létre egy új bejövő szabályt a 27017-es TCP-port engedélyezéséhez.

Ha szükséges, hozzon létre egy hálózati biztonsági csoport szabályt, amely lehetővé teszi a hozzáférést a MongoDB-hoz a meglévő Azure virtuális hálózati alhálózaton kívülről. A hálózati biztonsági csoport szabályait az [Azure Portalvagy](nsg-quickstart-portal.md) az [Azure PowerShell](nsg-quickstart-powershell.md)használatával hozhatja létre. A Windows tűzfal szabályaihoz ugyanúgy engedélyezze a 27017-es TCP-portot a MongoDB virtuális gép virtuális gépének virtuális hálózati felületére.

> [!NOTE]
> A 27017-es TCP-port a MongoDB által használt alapértelmezett port. Ezt a portot a `--port` paraméter `mongod.exe` használatával módosíthatja, amikor manuálisan vagy egy szolgáltatásból indul. Ha módosítja a portot, győződjön meg arról, hogy az előző lépésekben frissíti a Windows tűzfal és a hálózati biztonsági csoport szabályait.


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulta, hogyan telepítheti és konfigurálhatja a MongoDB-t a Windows virtuális gépén. Most már elérheti a MongoDB-t a Windows virtuális gépén, a [MongoDB dokumentációjának](https://docs.mongodb.com/manual/)speciális témaköreit követve.

