Kövesse az alábbi lépéseket, telepítése és futtatása a MongoDB Windows Server rendszerű virtuális gépen.

> [!IMPORTANT]
> Alapértelmezés szerint nem engedélyezettek a MongoDB biztonsági funkciók, például hitelesítés és az IP-cím kötés. Biztonsági szolgáltatások éles környezetben MongoDB telepítése előtt engedélyezni kell.  További információkért lásd: [biztonsági és hitelesítési](http://www.mongodb.org/display/DOCS/Security+and+Authentication).
>
>

1. Miután csatlakozott a virtuális géphez a távoli asztal használatával, nyissa meg az Internet Explorer hozzáférését a **Start** menü a virtuális gépen.
2. Válassza ki a **eszközök** gombra a jobb felső sarokban.  A **Internetbeállítások**, jelölje be a **biztonsági** lapra, majd válassza ki a **megbízható helyek** ikonra, végül kattintson a **helyek** gombra. Adja hozzá *https://\*. mongodb.org* a megbízható helyek listájához.
3. Ugrás a [letölti – MongoDB](https://www.mongodb.com/download-center#community).
4. Található a **jelenlegi stabil kiadásban** a **közösségi Server**, válassza ki a legújabb **64 bites** verziója a Windows oszlopban. Töltse le, majd futtassa az MSI telepítő.
5. MongoDB telepítése általában a C:\Program Files\MongoDB. Keresse meg a környezeti változók az asztalon, és vegye fel a MongoDB bináris fájlok elérési útját a PATH változóban. Például bizonyára hasznosnak találja a bináris fájljai a C:\Program Files\MongoDB\Server\3.4\bin a számítógépen.
6. Az adatlemez MongoDB adatainak és naplókönyvtárainak könyvtárak létrehozása (például meghajtó **F:**) az előző lépésekben létrehozott. A **Start**, jelölje be **parancssor** való nyisson meg egy parancssori ablakot.  Típus:

        C:\> F:
        F:\> mkdir \MongoData
        F:\> mkdir \MongoLogs
7. Futtassa az adatbázis futtatásához:

        F:\> C:
        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

    Összes naplózási üzenetek vannak átirányítva a *F:\MongoLogs\mongolog.log* mongod.exe server elindul, és Adatbázisnapló-fájlok preallocates fájlt. A napló fájlok készletméret, és elindítja a kapcsolatfigyelést mongodb több percig is eltarthat. A parancssor marad összpontosítanak ezt a feladatot, a MongoDB-példány futtatása közben.
8. A MongoDB felügyeleti rendszerhéj elindításához nyisson meg egy másik parancs ablakot az **Start** és írja be a következő parancsokat:

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

    Az adatbázist a beszúrást hozta létre.
9. Másik lehetőségként telepítése mongod.exe szolgáltatásként:

        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install

    A szolgáltatást, MongoDB nevű "Mongo DB" leírását. A `--logpath` egy naplófájlba, azóta a futó szolgáltatás nem rendelkezik egy parancsablakot kimenet megjelenítése a beállítást kell használni.  A `--logappend` beállítás megadja, hogy a szolgáltatás újraindítását okozza-e a kimeneti hozzáfűzése a meglévő naplófájl.  A `--dbpath` lehetőséget a data könyvtárának helyét adja meg. A szolgáltatással kapcsolatos további parancssori kapcsolókat lásd: [parancssori kapcsolók szolgáltatással kapcsolatos][MongoWindowsSvcOptions].

    A szolgáltatás elindításához futtassa ezt a parancsot:

        C:\> net start MongoDB
10. Most, hogy a MongoDB telepítve van és fut, meg kell nyitnia egy portot a Windows tűzfal, távolról csatlakozhat MongoDB.  Az a **Start** menü **felügyeleti eszközök** , majd **fokozott biztonságú Windows tűzfal**.
11. a) a bal oldali ablaktáblában jelölje ki **bejövő szabályok**.  Az a **műveletek** ablaktáblán a jobb oldali select **új szabály létrehozása...** .

    ![A Windows tűzfal][Image1]

    b) a a **új bejövő szabály varázsló**, jelölje be **Port** majd **következő**.

    ![A Windows tűzfal][Image2]

    c) válassza **TCP** , majd **adott helyi portok**.  Adjon meg egy portot az "27017" (az alapértelmezett porton figyel MongoDB), és kattintson a **következő**.

    ![A Windows tűzfal][Image3]

    d) válassza **a kapcsolat engedélyezéséhez** kattintson **következő**.

    ![A Windows tűzfal][Image4]

    e) kattintson **következő** újra.

    ![A Windows tűzfal][Image5]

    f) adja meg, például a "MongoPort", a szabály nevét, és kattintson a **Befejezés**.

    ![A Windows tűzfal][Image6]

12. A virtuális gép létrehozása után nem konfigurálta a végpont mongodb, most is elvégezhető. A tűzfalszabály és a MongoDB távolról csatlakozni tudjanak a végpont van szüksége.

  Az Azure portálon kattintson **virtuális gépek (klasszikus)**, kattintson az új virtuális gép nevét, majd **végpontok**.

    ![Végpontok][Image7]

13. Kattintson az **Add** (Hozzáadás) parancsra.

14. "Mongo" protokoll nevű végpont hozzáadása **TCP**, és mindkét **nyilvános** és **titkos** portok "27017" értékre. Ez a port megnyitása lehetővé teszi, hogy a MongoDB távoli elérését.

    ![Végpontok][Image9]

> [!NOTE]
> A MongoDB által használt alapértelmezett port az 27017 portot használja. Az alapértelmezett port megadásával módosíthatja a `--port` paraméter a mongod.exe kiszolgáló indításakor. Ügyeljen arra, hogy ugyanazt a portszámot a tűzfal és a "Mongo" végpont az előző utasítások.
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
