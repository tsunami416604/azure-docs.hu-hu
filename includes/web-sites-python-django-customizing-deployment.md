**Ha az alábbi két feltétel igaz**, az Azure megállapítja, hogy az alkalmazása a Pythont használja:

* a requirements.txt fájl a gyökérmappában
* bármely .py fájl a gyökérmappában VAGY egy runtime.txt fájl, amely a Pythont adja meg

Ebben az esetben egy Python-specifikus üzembe helyezési parancsfájllal elvégzi a fájlok szabványos szinkronizálását, valamint egyéb Python-műveleteket, többek között:

* A virtuális környezet automatikus felügyeletét
* A requirements.txt fájlban felsorolt csomagok pippel történő telepítését
* A megfelelő web.config létrehozását a kiválasztott Python-verzió alapján.
* A statikus fájlok összegyűjtését a Django-alkalmazások számára

Az alapértelmezett üzembe helyezés lépéseinek bizonyos aspektusait a parancsfájl testreszabása nélkül is vezérelheti.

A Pythonnal kapcsolatos összes lépés kihagyásához hozza létre ezt az üres fájlt:

    \.skipPythonDeployment

Ha ki szeretné hagyni a Django-alkalmazáshoz a statikus fájlok összegyűjtését, használja ezt:

    \.skipDjango 

Ha az üzembe helyezést még nagyobb mértékben szeretné irányítani, akkor a következő fájlok létrehozásával felülírhatja az alapértelmezett üzembe helyezési parancsfájlt:

    \.deployment
    \deploy.cmd

A fájlokat az [Azure parancssori felületén][Azure parancssori felületén] hozhatja létre.  Futtassa a következő parancsot a projektmappából:

    azure site deploymentscript --python

Ha ezek a fájlok nem léteznek, az Azure létrehoz, majd futtat egy ideiglenes üzembe helyezési parancsfájlt.  Ez megegyezik azzal, amelyet a fenti paranccsal hozhat létre.

[Azure parancssori felületén]: http://azure.microsoft.com/downloads/
