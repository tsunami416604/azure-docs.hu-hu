Kövesse az alábbi lépéseket, telepítheti és futtathatja a mongodb-hez Windows Server rendszerű virtuális gépen.

> [!IMPORTANT]
> MongoDB biztonsági funkciói, például a hitelesítést és az IP-cím kötés, alapértelmezés szerint nem engedélyezettek. Biztonsági funkciókat engedélyezni kell a mongodb-hez a éles környezetben való üzembe helyezés előtt.  További információkért lásd: [biztonság és hitelesítés](http://www.mongodb.org/display/DOCS/Security+and+Authentication).
>
>

1. Miután csatlakozott a virtuális gép távoli asztali kapcsolattal, nyissa meg az Internet Explorer hozzáférését a **Start** menü a virtuális gépen.
2. Válassza ki a **eszközök** gombra a jobb felső sarokban.  A **Internetbeállítások**, jelölje be a **biztonsági** lapra, és válassza ki a **megbízható helyek** ikonra, és végül kattintson a **helyek** gombra. Adjon hozzá *https://\*. mongodb.org* a megbízható helyek listájához.
3. Lépjen a [letöltések – MongoDB](https://www.mongodb.com/download-center#community).
4. Keresse meg a **aktuális stabil kiadási** , **Community Server**, válassza ki a legújabb **64 bites** verziója a Windows oszlopban. Töltse le, majd futtassa az MSI-telepítőt.
5. MongoDB általában a C:\Program Files\MongoDB van telepítve. Keresse meg a környezeti változók az asztalon, és a MongoDB bináris fájljainak elérési út hozzáadása a PATH változóhoz. Például előfordulhat, hogy található a bináris fájlok C:\Program Files\MongoDB\Server\3.4\bin a gépen.
6. MongoDB-adat- és naplófájlok könyvtárak létrehozása az adatlemez (például meghajtó **F:**) az előző lépésekben létrehozott. A **Start**válassza **parancssor** , nyisson meg egy parancssori ablakot.  Típus:

        C:\> F:
        F:\> mkdir \MongoData
        F:\> mkdir \MongoLogs
7. Az adatbázis futtatásához futtassa:

        F:\> C:
        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

    Az összes naplózási üzeneteket irányítja a *F:\MongoLogs\mongolog.log* fájlok mongod.exe kiszolgáló elindul, és preallocates Adatbázisnapló-fájlok. A mongodb-hez az adatbázisnapló-fájlok készletméret, és megkezdeni a figyelést kapcsolatok több percig is eltarthat. A parancssor használatával Ez a feladat összpontosítanak marad, a MongoDB-példány futtatása közben.
8. A mongodb-hez felügyeleti rendszerhéj elindításához nyisson meg egy másik parancssori ablakban az **Start** , és írja be a következő parancsokat:

        C:\> cd \my_mongo_dir\bin  
        C:\my_mongo_dir\bin> mongo  
        >db  
        test
        > db.foo.insert( { a : 1 } )  
        > db.foo.find()  
        { _id : ..., a : 1 }  
        > show dbs  
        ...  
        > show collections  
        ...  
        > help  

    Az insert hozza létre az adatbázist.
9. Másik lehetőségként telepítheti mongod.exe szolgáltatásként:

        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install

    A szolgáltatás telepítve van a MongoDB nevű "Mongo DB" leírását. A `--logpath` beállítást kell használni, adja meg a naplófájlt, mert a futó szolgáltatás nem rendelkezik egy kimeneti megjeleníthető parancsablakot.  A `--logappend` beállítás megadja, hogy a szolgáltatás újraindítását okozza-e a kimeneti hozzáfűzése a meglévő naplófájlt.  A `--dbpath` lehetőség a adatkönyvtárat helyét adja meg. A szolgáltatással kapcsolatos további parancssori kapcsolókat lásd: [parancssori kapcsolók szolgáltatásokkal kapcsolatos][MongoWindowsSvcOptions].

    Indítsa el a szolgáltatást, a következő parancs futtatásával:

        C:\> net start MongoDB
10. Most, hogy a mongodb-hez telepítve van és fut, meg kell nyitnia egy portot a Windows tűzfal, távolról csatlakozhat a mongodb-hez.  Az a **Start** menüjében válassza **felügyeleti eszközök** , majd **fokozott biztonságú Windows tűzfal**.
11. a) a bal oldali panelén válassza **bejövő szabályok**.  Az a **műveletek** válassza a jobb oldali panelen **új szabály...** .

    ![Windows tűzfal][Image1]

    (b) a a **új bejövő szabály varázsló**válassza **Port** majd **tovább**.

    ![Windows tűzfal][Image2]

    c) a select **TCP** , majd **adott helyi portok**.  Adjon meg egy portot a "27017" (az alapértelmezett port a MongoDB figyeli), és kattintson a **tovább**.

    ![Windows tűzfal][Image3]

    d) a select **engedélyezze a kapcsolatot** kattintson **tovább**.

    ![Windows tűzfal][Image4]

    e) kattintson **tovább** újra.

    ![Windows tűzfal][Image5]

    f) adjon meg egy nevet a szabálynak, például a "MongoPort", és kattintson a **Befejezés**.

    ![Windows tűzfal][Image6]

12. Ha nem konfigurál egy végpontot a mongodb-hez, a virtuális gép létrehozásakor, ezt most megteheti. A tűzfalszabály, mind a végpontot, hogy tudni távolról csatlakozhat a mongodb-hez van szüksége.

  Az Azure Portalon kattintson a **virtuális gépek (klasszikus)**, kattintson az új virtuális gép nevét, majd **végpontok**.

    ![Végpontok][Image7]

13. Kattintson a **Hozzáadás** parancsra.

14. "Mongo", a protokoll nevét a végpont hozzáadásának **TCP**, és mindkét **nyilvános** és **privát** portok "27017" értékre. Ez a port megnyitása lehetővé teszi, hogy távolról is elérhető a mongodb-hez.

    ![Végpontok][Image9]

> [!NOTE]
> A port 27017 MongoDB által használt alapértelmezett port. Az alapértelmezett port megadásával módosíthatja a `--port` paraméter a mongod.exe kiszolgáló indításakor. Ellenőrizze, hogy ugyanazt a portszámot a tűzfal és az előző utasítások a "Mongo" végpontot.
>
>

[MongoDownloads]: http://www.mongodb.org/downloads

[MongoWindowsSvcOptions]: http://www.mongodb.org/display/DOCS/Windows+Service


[Image1]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall1.png
[Image2]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall2.png
[Image3]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall3.png
[Image4]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall4.png
[Image5]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall5.png
[Image6]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall6.png
[Image7]: ./media/install-and-run-mongo-on-win2k8-vm/menusendpointadd.png
<!-- Removed 03/08/2017. Not in new portal. -->
<!-- [Image8]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint2.png
-->
[Image9]: ./media/install-and-run-mongo-on-win2k8-vm/newendpointdetails.png
