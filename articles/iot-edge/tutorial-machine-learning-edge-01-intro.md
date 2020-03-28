---
title: 'Oktatóanyag: A Machine Learning részletes forgatókönyve az Azure IoT Edge-en'
description: Egy magas szintű oktatóanyag, amely végigvezeti a különböző feladatok létrehozásához szükséges egy teljes körű, gépi tanulás a peremhálózaton forgatókönyv.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 965c420fa29c4cf82517148c01e17d6d7dd6ea97
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74106503"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Oktatóanyag: Teljes körű megoldás az Azure Machine Learning és az IoT Edge használatával

Az IoT-alkalmazások gyakran szeretnék kihasználni az intelligens felhő és az intelligens peremhálózat előnyeit. Ebben az oktatóanyagban bemutatjuk a gépi tanulási modell betanítását a felhőben lévő IoT-eszközökről gyűjtött adatokkal, a modell üzembe helyezésével az IoT Edge-re, és rendszeres időközönként karbantartjuk és finomítjuk a modellt.

Ez az oktatóanyag elsődleges célja, hogy bemutassa az IoT-adatok feldolgozása a gépi tanulás, különösen a szélén. Bár egy általános gépi tanulási munkafolyamat számos aspektusát megérintjük, ez az oktatóanyag nem a gépi tanulás részletes bemutatása. Példaként szolgálunk, hogy nem próbálunk meg egy rendkívül optimalizált modellt létrehozni a használati esethez – csak eleget teszünk ahhoz, hogy bemutassuk az IoT-adatfeldolgozás életképes modelljének létrehozásának és használatának folyamatát.

## <a name="target-audience-and-roles"></a>Célközönség és szerepkörök

Ez a cikkkészlet olyan fejlesztők nek készült, akiknek nem volt korábbi tapasztalatuk az IoT-fejlesztés vagy a gépi tanulás terén. A gépi tanulás üzembe helyezése a csúcsterületen a technológiák széles körének összekapcsolásához szükséges ismereteket igényel. Ezért ez az oktatóanyag egy teljes körű forgatókönyvet fed le, hogy bemutassa az ioT-megoldáshoz való együttes csatlakozás egyik módját. Valós környezetben ezek a feladatok több különböző szakterülettel rendelkező között is eloszthatók. A fejlesztők például az eszközre vagy a felhőkódra összpontosítanak, míg az adatszakértők az elemzési modelleket tervezték. Annak érdekében, hogy egy egyéni fejlesztő sikeresen elvégezhesse ezt az oktatóanyagot, kiegészítő útmutatást nyújtottunk a további információkhoz való hivatkozásokhoz, amelyek reményünk szerint elegendőek ahhoz, hogy megértsük, mi történik, és miért.

Azt is megteheti, hogy összeáll a különböző szerepkörökkel rendelkező munkatársakkal, hogy kövessék az oktatóanyagot, és teljes körű szakértelmet hozzanak létre, és csapatként megtanulják, hogyan illeszkednek egymáshoz a dolgok.

Mindkét esetben az olvasó(k) tájolása érdekében az oktatóanyag minden egyes cikke a felhasználó szerepét jelzi. Ezek a szerepek a következők:

* Felhőfejlesztés (beleértve egy DevOps-kapacitással dolgozó felhőfejlesztőt is)
* Adatelemzés

## <a name="use-case-predictive-maintenance"></a>Használati eset: Prediktív karbantartás

Ezt a forgatókönyvet a 2008-as Prognosztikai és Egészségirányítási Konferencián (PHM08) bemutatott használati esetre alapoztuk. A cél az, hogy megjósolni fennmaradó hasznos élettartama (RUL) egy sor turbofan repülőgép motorok. Ezeket az adatokat a C-MAPSS, a MAPSS (Modular Aero-Propulsion System Simulation) szoftver kereskedelmi verziójával hozták létre. Ez a szoftver egy rugalmas turbóventilátoros motor szimulációs környezetkényelmesen szimulálja az egészségügyi, ellenőrzési, és a motor paramétereit.

Az ebben az oktatóanyagban használt adatok a [Turbofan motor degradációs szimulációs adatkészletéből származnak.](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan)

A fájlból:

***Kísérleti forgatókönyv***

*Az adatkészletek több többváltozós idősorozatból állnak. Minden adatkészlet tovább van osztva betanítási és tesztelési részhalmazok. Minden idősorozat egy másik motorból származik – azaz az adatok azonos típusú motorflottából származnak. Minden motor különböző kezdeti kopási és gyártási eltéréssel kezdődik, ami a felhasználó számára ismeretlen. Ez a kopás és eltérés normálisnak tekinthető, azaz nem tekinthető hibaállapotnak. Három működési beállítás van, amelyek jelentős hatást gyakorolnak a motor teljesítményére. Ezek a beállítások az adatokban is szerepelnek. Az adatok az érzékelők zajával szennyezettek.*

*A motor minden idősorozat elején normálisan működik, és a sorozat egy bizonyos pontján hibát fejt ki. A betanítási készletben a hiba a rendszer meghibásodásáig növekszik. A tesztkészletben az idősorozat a rendszer meghibásodása előtt egy idővel véget ér. A verseny célja, hogy megjósolja a fennmaradó működési ciklusok számát a vizsgálati sorozat meghibásodása előtt, azaz az utolsó ciklus utáni működési ciklusok számát, amelyet a motor továbbra is működtetni fog. A vizsgálati adatokhoz a valódi hátralévő hasznos élettartam (RUL) értékek vektora is rendelkezésre áll.*

Mivel az adatok at tettek közzé egy verseny, több módszer a gépi tanulási modellek származtatása egymástól függetlenül közzétett. Megállapítottuk, hogy a példák tanulmányozása hasznos egy adott gépi tanulási modell létrehozásában részt vevő folyamat és érvelés megértésében. Lásd például:

[Repülőgép-hajtómű meghibásodása előrejelzési modell](https://github.com/jancervenka/turbofan_failure) GitHub felhasználó jancervenka.

[Turbofan motor degradáció](https://github.com/hankroark/Turbofan-Engine-Degradation) a GitHub felhasználó hankroark.

## <a name="process"></a>Folyamat

Az alábbi képen látható a durva lépéseket követünk ebben a bemutató:

![Architektúradiagram a folyamatlépésekhez](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. **Képzési adatok gyűjtése**: A folyamat a betanítási adatok gyűjtésével kezdődik. Bizonyos esetekben az adatok már összegyűjtésre kerültek, és adatbázisban vagy adatfájlok formájában érhetők el. Más esetekben, különösen az IoT-forgatókönyvek esetében, az adatokat ioT-eszközökről és -érzékelőkből kell gyűjteni, és a felhőben kell tárolni.

   Feltételezzük, hogy nincs turbofan motorgyűjteménye, így a projektfájlok tartalmaznak egy egyszerű eszközszimulátort, amely elküldi a NASA eszközadatait a felhőbe.

1. **Adatok előkészítése**. A legtöbb esetben az eszközökről és érzékelőkről gyűjtött nyers adatok at a gépi tanulás előkészítéséhez kell igényelni. Ez a lépés magában foglalhatja az adatok karbantartását, az adatok újraformázását, vagy a gépi tanulás további információk befecskendezésének előfeldolgozását.

   A repülőgép-hajtómű gépadatai esetében az adatelőkészítés magában foglalja a minta minden adatpontjának kifejezett meghibásodáselőtti idejének kiszámítását az adatoktényleges megfigyelései alapján. Ez az információ lehetővé teszi, hogy a gépi tanulási algoritmus korrelációkat találjon a tényleges érzékelő adatmintái és a motor várható hátralévő élettartama között. Ez a lépés nagymértékben tartományspecifikus.

1. **Gépi tanulási modell létrehozása.** Az előkészített adatok alapján most már kísérletezhetünk különböző gépi tanulási algoritmusokkal és paraméterezésekkel a modellek betanításához és az eredmények összehasonlításához.

   Ebben az esetben a teszteléshez összehasonlítjuk a modell által kiszámított előre jelzett eredményt a motorok készletén megfigyelt valós eredménnyel. Az Azure Machine Learningben a modellbeállításjegyzékben létrehozott modellek különböző iterációit kezelheti.

1. **Telepítse a modellt.** Amint rendelkezünk egy olyan modellel, amely megfelel a sikerkritériumainknak, átléphetünk az üzembe helyezésre. Ez magában foglalja a modell csomagolását egy webszolgáltatás-alkalmazásba, amely rest-hívásokkal és a visszatérési elemzési eredményekkel adagzható adatokkal. A webszolgáltatás-alkalmazás ezután egy docker-tárolóba van csomagolva, amely viszont telepíthető a felhőben vagy egy IoT Edge-modulban. Ebben a példában az IoT Edge üzembe helyezésére összpontosítunk.

1. **A modell karbantartása és finomítása**. A mi munkánk nem történik meg, ha a modell telepítve van. Sok esetben továbbra is szeretnénk adatokat gyűjteni, és rendszeresidőközönként feltölteni ezeket az adatokat a felhőbe. Ezután használhatja ezeket az adatokat a modell újratanításához és finomításához, amelyet aztán újratelepíthetünk az IoT Edge-re.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez hozzá kell férnem egy Azure-előfizetéshez, amelyben jogokkal rendelkezik az erőforrások létrehozásához. Az oktatóanyagban használt szolgáltatások közül több azure-díjat is felszámít. Ha még nem rendelkezik Azure-előfizetéssel, előfordulhat, hogy elkezdheti az [Ingyenes Azure-fiókot.](https://azure.microsoft.com/offers/ms-azr-0044p/)

Szüksége van egy olyan gépre is, amelyen telepítve van a PowerShell, ahol parancsfájlok futtatásával egy Azure virtuális gép van a fejlesztői gép.

Ebben a dokumentumban a következő eszközöket használjuk:

* Azure IoT-központ az adatrögzítéshez

* Az Azure Notebookok az adatok előkészítésének és a gépi tanulási kísérletezésfőfrontjának. A python-kód futtatása a jegyzetfüzetben a mintaadatok egy részhalmazán egy nagyszerű módja annak, hogy gyors iteratív és interaktív átfutási idő adatelőkészítése során. Jupyter notebookok is használható parancsfájlok előkészítése a számítási háttérben nagy méretekben futtatható.

* Az Azure Machine Learning háttérrendszerként a gépi tanulás nagy méretekben és a gépi tanulási rendszerkép-generáláshoz. Az Azure Machine Learning-háttérszolgáltatást a Jupyter-jegyzetfüzetekben készített és tesztelt parancsfájlok használatával hajtjuk.

* Azure IoT Edge egy gépi tanulási lemezkép felhőn kívüli alkalmazásához

Nyilvánvalóan, vannak más választások elérhető. Bizonyos esetekben például az IoT Central kód nélküli alternatívaként használható az IoT-eszközök kezdeti betanítási adatainak rögzítésére.

## <a name="next-steps"></a>További lépések

Ez a bemutató a következő szakaszokra oszlik:

1. Állítsa be a fejlesztői gépet és az Azure-szolgáltatásokat.
2. A gépi tanulási modul betanítási adatainak létrehozása.
3. A gépi tanulási modul betanítása és üzembe helyezése.
4. Konfigurálja az IoT Edge-eszközt, hogy egy átlátszó átjáróként működjön.
5. IoT Edge-modulok létrehozása és üzembe helyezése.
6. Adatok küldése az IoT Edge-eszközre.

Folytassa a következő cikket egy fejlesztői gép beállításához és az Azure-erőforrások kiépítéséhez.

> [!div class="nextstepaction"]
> [Az IoT Edge-en a gépi tanulás környezetének beállítása](tutorial-machine-learning-edge-02-prepare-environment.md)
