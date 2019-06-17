---
title: Machine Learning az Azure IoT Edge részletes bemutatója |} A Microsoft Docs
description: Magas szintű oktatóanyag, amely végigvezeti a különböző feladatok egy teljes körű, gépi tanulási, a peremhálózati forgatókönyv létrehozásához szükséges.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 916e48752431be41ff150c2ac84e66eb1e98e81f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057749"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Oktatóanyag: Egy teljes körű megoldás az Azure Machine Learning és az IoT Edge használatával

Gyakran IoT-alkalmazásokat szeretne az intelligens felhőben és az intelligens peremhálózat előnyeit. Ez az oktatóanyag részletesen egy gépi tanulási modellt az adatok a felhőben, IoT-eszközökről gyűjtött képzési helyezi üzembe a modellt, IoT Edge segítségével, és karbantartásával és a modell rendszeres időközönként finomítása.

Ebben az oktatóanyagban elsődleges célja, a machine learning, az Edge-ben a kifejezetten az IoT-adatok feldolgozása bevezetni. Machine learning-munkafolyamat általános számos szempontból touch azt, miközben ebben az oktatóanyagban nem készült részletes bemutatása, Machine learning. Olyan esetben, hogy ne kísérelje meg a használati esetekhez magas szinten optimalizált modell létrehozása – csak végzünk elegendő ábrázolni a folyamatot, létrehozásának és a egy működőképes modell segítségével az IoT-adatok feldolgozása.

## <a name="target-audience-and-roles"></a>Célközönség és szerepkörök

Ezen IoT-fejlesztés vagy a machine learning a korábbi tapasztalatok nélkül a fejlesztők számára készült. Üzembe helyezése a machine learning a peremhálózaton technológiák számos kapcsolódás ismeretek szükségesek. Ezért ez az oktatóanyag bemutatja egy teljes, végpontok közötti forgatókönyv bemutatásához az egyik módszer, ezek a technológiák együttesen az IoT-megoldás csatlakoztatása. Valós környezetben ezeket a feladatokat előfordulhat, hogy a különböző szakterületek több felhasználó között elosztva. Például a fejlesztők lenne koncentrálhat, eszköz vagy a felhőbeli kódja, miközben kínál adatelemző szakembereknek megtervezni a szövegelemzési modelleket. Ahhoz, hogy ez az oktatóanyag sikeres elvégzéséhez egy egyéni fejlesztői, az insights kiegészítő útmutató biztosítunk, és Reméljük, hogy további információkra mutató hivatkozásokat is elegendő tudni, mi történik, valamint hogy miért.

Azt is megteheti előfordulhat, hogy csapat munkatársaival a eltérő szerepkörök együtt, kövesse az oktatóanyag teljes szaktudását viselik, így és csapatként megtudhatja, hogyan dolgot működnek együtt.

Mindkét esetben segítse a az olvasó, hogy minden egyes cikk ebben az oktatóanyagban azt jelzi, hogy a felhasználó szerepkörét. Ezeket a szerepköröket a következők:

* Felhőalapú fejlesztés (beleértve a fejlesztési és üzemeltetési kapacitásban dolgozó fejlesztők felhő)
* Adatelemzés

## <a name="use-case-predictive-maintenance"></a>Használati eset: Prediktív karbantartás

Ebben a forgatókönyvben azt alapján 2008 Prognostics és állapot-felügyeleti (PHM08) konferencián bemutatott használati eset. A célja a fennmaradó hasznos élettartamának (RUL) turbofan repülőgép-hajtóművek áll. Ezek az adatok C-MAPSS, kereskedelmi verziójának MAPSS (moduláris Aero-Propulsion rendszer szimuláció) használatával lett létrehozva. Ez a szoftver kényelmesen szimulálása a állapotát, ellenőrzési és motor paraméterek rugalmas turbofan engine szimuláció környezetet biztosít.

Ebben az oktatóanyagban használt adatok forrása a [Turbofan engine degradation szimuláció adatkészlet](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

A fontos fájlból:

***Kísérleti forgatókönyv***

*Adatkészletek többváltozós idősorozatban állnak. Minden adathalmaz tanítási és tesztelési részekre osztott. Minden egyes különböző motor – azaz, az adatok lehessen venni egy járműflotta azonos típusú motorok lennie ahhoz. Mindegyik motor kezdeti motorkopási és a felhasználó számára ismeretlen variation gyártási különböző fok kezdődik. Ez motorkopási és változásairól számít normál, azaz, egy tartalék feltétel nem minősül. Nincsenek három működési beállításait, amelyek a motor teljesítményének jelentős hatással lehet. Ezek a beállítások is szerepelnek az adatokat. Az adatok az érzékelő zaj szennyezett.*

*A motor minden egyes elején megfelelően működik, és az adatsorozat során valamikor hiba fejleszt. A betanítási készlet a tartalék növekszik nagyságrendűek rendszerhiba-ig. A teszt készletbe a time series véget ér, néhány esetben, amikor a rendszer hibája. A verseny célja a teszt készletbe, azaz a működési ciklusokat, amelyek a motor továbbra is megfelelően működjenek a legutóbbi ciklust követően száma meghibásodása előtt hátralévő működési ciklusok megjósolható, hogy. A teszt adatai is biztosítja a valódi fennmaradó hasznos élettartama (RUL) értékek vektor.*

Mivel az adatok versenyregisztráció lett közzétéve, számos megközelítés hogy a machine learning-modellek egymástól függetlenül közzétéve. Úgy találtuk, hogy tanulmányozza a példák akkor lehet hasznos, a folyamat megismeréséhez, és mintafelismerési részt vesz egy adott machine learning-modellek létrehozását. Lásd például:

[Repülőgép motor hibája előrejelzési modell](https://github.com/jancervenka/turbofan_failure) a GitHub felhasználói jancervenka.

[Turbofan engine degradation](https://github.com/hankroark/Turbofan-Engine-Degradation) a GitHub felhasználói hankroark.

## <a name="process"></a>Process

Az alábbi képen a nyers lépéseket fogjuk követni, ebben az oktatóanyagban mutatja be:

![A folyamat lépéseivel architektúra ábrája](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. **Betanítási adatok összegyűjtése**: A folyamat elindul a betanítási adatok gyűjtésével. Bizonyos esetekben az adatok már összegyűjtött és egy adatbázist, vagy adatfájlok formában érhető el. Különösen az IoT-forgatókönyveket, más esetekben az adatokat kell IoT-eszközök és érzékelők és a felhőben tárolt.

   Feltételezzük, hogy nem rendelkezik turbofan motorok gyűjteménye, így a projektfájlok tartalmaznak egy egyszerű készülékszimulátort, amely a NASA eszköz adatokat küld a felhőbe.

1. **Adatok előkészítése**. A legtöbb esetben a nyers adatok, eszközök és érzékelők begyűjtött előkészítés a machine Learning szükséges. Ebben a lépésben is igénybe vehet adatok karbantartása, adatok újraformázást, vagy további információk a gépi tanulás a beillesztendő előfeldolgozása is kulcs ki.

   Az repülőgépes motor gép adatokhoz adat-előkészítési magában foglalja a kifejezett idő – hiba többször a minta az adatok tényleges megfigyelések alapján minden adatponthoz tartozó kiszámítása. Ez az információ lehetővé teszi, hogy a gépi tanulási algoritmus tényleges érzékelő adatmintákhoz szűrősablonokat és a motor fennmaradó várható élettartama közötti összefüggések keresésére. Ez a lépés rendkívül tartomány-specifikus nem.

1. **Hozhat létre egy gépi tanulási modellt**. Az előkészített adatok alapján, hogy most már kísérletezhet a különböző gépi tanulási algoritmusok és -modellek betanításához, és hasonlítsa össze az eredményeket egy másik parameterizations.

   Ebben az esetben tesztelési összehasonlítva az előre jelzett serkenti az eredményt a modell által megfigyelt különböző motorok valós eredményekkel számított. Az Azure Machine Learning modellek létrehozunk egy modellt beállításjegyzékben különböző ismétlésének lehet kezelni.

1. **A modell üzembe helyezése**. Amint rendelkezésünkre áll egy modellt, amely eleget tesz a sikeresség feltétele, továbbléphet a telepítéshez. Amely magában foglalja a modell alkalmazásburkoló webalkalmazásba szolgáltatás, amely REST-hívások használatával is lehet adatkéréseket és elemzési eredményeket adja vissza. A web service-alkalmazás majd egy docker-tároló, amely ezután is üzembe helyezhetők a felhőben vagy az IoT Edge-modul be van csomagolva. Ebben a példában koncentrálunk üzembe helyezés IoT Edge-ben.

1. **Karbantartása és finomíthatja a modell**. A munkahelyi nem történik meg, a modell üzembe helyezése után. Sok esetben szeretnénk továbbra is az adatok gyűjtése és rendszeres időközönként tölthet fel adatokat a felhőbe. Használhatjuk ezeket az adatokat ezután újratanítás és finomíthatja a modell, amely azt majd is ismételt üzembe helyezése IoT Edge-ben.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez hozzáférésre van szüksége, amelyben megfelelő jogosultságokkal rendelkezik erőforrások létrehozása Azure-előfizetéssel. Ebben az oktatóanyagban használt szolgáltatások számos számítunk fel Azure-szolgáltatások díjait. Ha Ön még nem rendelkezik Azure-előfizetéssel, előfordulhat, hogy kell használatának első lépései egy [ingyenes Azure-fiók](https://azure.microsoft.com/offers/ms-azr-0044p/).

A PowerShell használatával is futtathat parancsfájlokat, állítsa be egy Azure virtuális gépként a fejlesztői gépére telepíteni kell egy gép is.

Ebben a dokumentumban a következő eszközök készletét használjuk:

* Az Azure IoT hub, az adatváltozások rögzítése

* A fő előtér adat-előkészítési és a machine learning-kísérletezés, Azure jegyzetfüzeteket. Futó python kódját történő használatát egy minta részhalmazt egy nagyszerű lehetőséget kínál a gyors, iteratív és interaktív válaszidejű, optimalizált adat-előkészítés során. Jupyter-notebookok is használható, készítse elő a parancsfájlok futtatását a horizontális leskálázás számítási háttérrendszernek.

* Az Azure Machine Learning gépi tanulási ipari méretekben, és a machine learning oldalháttérkép-létrehozás háttérmoduljaként. Azt a meghajtó a parancsfájlok előkészített és tesztelt, a Jupyter notebookok használata az Azure Machine Learning-háttérrendszer.

* Az Azure IoT Edge alkalmazás kikapcsolása felhőben a machine learning-lemezkép

Természetesen más lehetőség érhető el. Bizonyos helyzetekben például IoT-központ használható alternatíva kód nélküli IoT-eszközök kezdeti betanítási adatok rögzítése.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag a következő szakaszokra oszlik:

1. Állítsa be a fejlesztési számítógép és az Azure-szolgáltatásokat.
2. Hozzon létre a betanítási adatok, a gépi tanulási modul számára.
3. Betanítása és a machine learning modul üzembe helyezése.
4. A transzparens átjáróként működő IoT Edge-eszköz konfigurálása.
5. Hozzon létre, és üzembe helyezése IoT Edge-modulok.
6. Adatokat küld az IoT Edge-eszköz.

Továbbra is a következő cikk állítsa be a fejlesztői gépen, és az Azure-erőforrások kiépítése.

> [!div class="nextstepaction"]
> [A machine learning, az IoT Edge-környezet beállítása](tutorial-machine-learning-edge-02-prepare-environment.md)
