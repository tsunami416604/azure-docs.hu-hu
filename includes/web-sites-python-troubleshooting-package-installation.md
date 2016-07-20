Előfordulhat, hogy egyes csomagok nem települnek a pip használatával, ha Azure-on futtatja őket.  Lehetséges, hogy a csomag egyszerűen nem érhető el a Python-csomagindexben.  Lehet, hogy egy fordító szükséges (a fordító nem érhető el azon a gépen, amelyik a webalkalmazást futtatja az Azure App Service-ben).

Ebben a szakaszban megnézzük, hogyan kezelhető ez a probléma.

### Kerekek kérése

Ha a csomag telepítéséhez fordító szükséges, akkor próbálja meg felvenni a kapcsolatot a csomag tulajdonosával, és kérje meg, hogy tegyen elérhetővé kerekeket a csomaghoz.

A nemrégiben elérhetővé vált [Microsoft Visual C++ Compiler for Python 2.7][] megkönnyíti a natív Python 2.7-kóddal rendelkező csomagok építését.

### Kerekek építése (Windows rendszert igényel)

Megjegyzés: Ha ezt a lehetőséget választja, ne felejtse el lefordítani a csomagot az Azure App Services-szolgáltatásban a webalkalmazásban használt platformmal/architektúrával/verzióval (Windows/32 bites/2.7 vagy 3.4) megegyező Python-környezetben.

Ha a csomag telepítése sikertelen, mert fordító szükséges hozzá, akkor telepítse a fordítót a helyi gépén, és építsen egy kereket a csomaghoz, amelyet majd belefoglalhat a tárházba.

Mac-/Linux-felhasználók: Ha nincs hozzáférése egy Windows-rendszert futtató géphez, a [Windows rendszerű virtuális gép létrehozása][] című cikkből megtudhatja, hogy hogyan hozhat létre egy virtuális gépet az Azure-ban.  Ezzel létrehozhatja a kerekeket, hozzáadhatja őket a tárházhoz, a virtuális gépet pedig igény szerint elvetheti. 

Python 2.7 esetén telepítheti a [Microsoft Visual C++ Compiler for Python 2.7][] eszközt.

Python 3.4 esetén telepítheti a [Microsoft Visual C++ 2010 Express][] eszközt.

A kerekek építéséhez szüksége lesz a kerékcsomagra:

    env\scripts\pip install wheel

A `pip wheel` segítségével fordíthat le egy függőséget:

    env\scripts\pip wheel azure==0.8.4

Ez létrehoz egy .whl fájlt a \wheelhouse mappában.  Adja hozzá a \wheelhouse mappát és a kerékfájlokat a tárházhoz.

Szerkessze a requirements.txt fájlt, és adja hozzá a `--find-links` kapcsolót a felső részen. A pip ebből tudni fogja, hogy a helyi mappában kell pontos egyezést keresnie, mielőtt áttér a Python-csomagindexre.

    --find-links wheelhouse
    azure==0.8.4

Ha azt szeretné, hogy az összes függősége a \wheelhouse mappában szerepeljen, és a Python-csomagindexet egyáltalán nem kívánja használni, akkor a `--no-index` kapcsolót a requirements.txt fájl tetejéhez adva kényszerítheti a pipet, hogy hagyja figyelmen kívül a csomagindexet.

    --no-index

### A telepítés testreszabása

Az üzembe helyezési parancsfájl testreszabásával egy alternatív telepítővel, például az easy\_install segítségével telepíthet csomagokat a virtuális környezetben.  A deploy.cmd fájlban megtekinthet egy megjegyzésként szereplő példát.  Ügyeljen arra, hogy a requirements.txt fájlban ne szerepeljenek ilyen jellegű csomagok, hogy a pip ne telepítse őket.

Adja hozzá a következőt az üzembe helyezési parancsfájlhoz:

    env\scripts\easy_install somepackage

Lehetséges, hogy az easy\_install segítségével egy exe-telepítőből is végrehajthatja a telepítést (némelyikük zip-kompatibilis, ezért az easy\_install támogatja őket).  Adja hozzá a telepítőt a tárházhoz, majd az elérési utat a végrehajtható fájlnak átadva hívja meg az easy\_install telepítőt.

Adja hozzá a következőt az üzembe helyezési parancsfájlhoz:

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### A virtuális környezet felvétele a tárházba (Windowst igényel)

Megjegyzés: Ha ezt a lehetőséget választja, ne felejtsen el olyan virtuális környezetet használni, amely megegyezik az Azure App Services-szolgáltatásban a webalkalmazásban használt platformmal/architektúrával/verzióval (Windows/32 bites/2.7 vagy 3.4).

Ha felveszi a virtuális környezetet a tárházba, egy üres fájl létrehozásával megakadályozhatja, hogy az üzembe helyezési parancsfájl virtuáliskörnyezet-felügyeletet folytasson:

    .skipPythonDeployment

Javasoljuk, hogy törölje a létező virtuális környezetet az alkalmazásban, hogy ne maradjanak vissza fájlok akkorról, amikor a virtuális környezet felügyelete automatikus volt.


[Windows rendszerű virtuális gép létrehozása]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-hero-tutorial/
[Microsoft Visual C++ Compiler for Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949



<!--HONumber=Jun16_HO2-->


