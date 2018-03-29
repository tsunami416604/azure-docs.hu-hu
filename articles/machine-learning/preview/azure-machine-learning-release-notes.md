---
title: What's new in Azure Machine Learning
description: Ez a dokumentum részletesen Azure Machine Learning frissítéseit.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.service: machine-learning
ms.workload: data-services
ms.topic: reference
ms.date: 03/28/2018
ms.openlocfilehash: 9d16606665bf043e094bebdfbbce973910135f1a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="whats-new-in-azure-machine-learning"></a>Az Azure Machine Learning újdonságai

Ebből a cikkből megtudhatja, az új verziókról [Azure Machine Learning szolgáltatás](overview-what-is-azure-ml.md). 

## <a name="2018-03-sprint-4"></a>2018-03 (sprint 4)
**Verziószám**: 0.1.1801.24353 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([találnia, amelynek a](known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))


Üdvözli az Azure Machine Learning-munkaterület ötödik frissítést. A következő frissítéseket számos visszajelzése közvetlen eredményeként történik. Adjon válaszadás hamarosan!

**Fontos új funkciók és módosítások**

- A parancsfájlok virtuális gépeken futó távoli Ubuntu natív módon a saját környezete mellett távoli-docker támogatása végrehajtási alapján.
- Új környezet munkaterület alkalmazásban lehetővé teszi a számítási céljainak létrehozása, és futtassa a konfiguráció a CLI-alapú felhasználói élmény mellett.
![Környezetek lap](media/azure-machine-learning-release-notes/environment-page.png)
- Testre szabható futtatása előzmények jelentések ![új futtatása jelentések képe](media/azure-machine-learning-release-notes/new-run-history-reports.png)

**Részletes frissítések**

Az alábbiakban az Azure Machine Learning a sprint a minden összetevő területén részletes frissítések listáját.

### <a name="workbench-ui"></a>Felhasználói felület munkaterület
- Testre szabható futtatása jelentések
  - Jelentések futtatása előzmények továbbfejlesztett diagram konfigurációjának
    - A használt entrypoints módosítható.
    - Legfelső szintű szűrők felvehető és módosított ![szűrők hozzáadása](media/azure-machine-learning-release-notes/add-filters.jpg)
    - A diagramok és a statisztikák hozzá vagy módosították (és fogd és vidd nem rendezhetők át).
    ![Új diagramok létrehozása](media/azure-machine-learning-release-notes/configure-charts.png)

  - Jelentések futtatása előzmények CRUD
  - Az összes meglévő futtatási előzményei listanézet configs kiszolgálóoldali jelentések, amely a folyamatok egyfajta futtatja a kijelölt belépési pontok áthelyezték.

- Környezetek lap
  - Egyszerűen adja hozzá az új számítási cél, és futtassa a konfigurációs fájlokat a projekthez ![új számítási cél](media/azure-machine-learning-release-notes/add-new-environments.png)
  - Kezelése és egy egyszerű, űrlapalapú UX segítségével konfigurációs fájljainak frissítése
  - A végrehajtási környezetet előkészítéséhez új gomb

- Teljesítménnyel kapcsolatos fejlesztések a lista az oldalsávon fájlok

### <a name="data-preparation"></a>Adatok előkészítése 
- Az Azure Machine Learning-munkaterület mostantól lehetővé teszi egy ismert oszlopnév használatával oszlop megkereshetik.


### <a name="experimentation"></a>Kísérletezés
- Az Azure Machine Learning-munkaterület mostantól támogatja a natív módon fut a parancsfájlokat a saját python vagy pyspark környezetben. Ez a funkció a felhasználó hoz létre, és kezeli a saját környezet a távoli virtuális Gépre, és Azure Machine Learning-munkaterület segítségével hajtható végre a parancsfájlok, hogy a célkiszolgáló olyan. Ellenőrizze a [Azure Machine Learning kísérletezhet szolgáltatás konfigurálása](experimentation-service-configuration.md) 

### <a name="model-management"></a>Modellkezelés
- A telepített tárolók testreszabása támogatása: lehetővé teszi, hogy a tároló lemezkép testreszabása tételével telepítési külső szalagtárak használatával apt get, stb. Már nem korlátozódik a pip telepíthető könyvtárak. Tekintse meg a [dokumentáció](model-management-custom-container.md) vonatkozó információ.
  - Használja a `--docker-file myDockerStepsFilename` a jegyzék, a lemezkép vagy a szolgáltatás létrehozása parancsok jelzőt és a fájl nevét.
  - Vegye figyelembe, hogy az alapjául szolgáló lemezképhez Ubuntu-e, és nem módosítható.
  - Példa parancs: 
  
      ```shell
      $ az ml image create -n myimage -m mymodel.pkl -f score.py --docker-file mydockerstepsfile
      ```



## <a name="2018-01-sprint-3"></a>2018-01 (sprint 3) 
**Verziószám**: 0.1.1712.18263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([találnia, amelynek a](known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Az alábbi táblázat a frissítéseket és a sprint javítását. Ezek a frissítések számos felhasználói visszajelzés közvetlen következményeként történik. 


Az alábbiakban az Azure Machine Learning a sprint a minden összetevő területén részletes frissítések listáját.

- A hitelesítési verem frissítések arra kényszeríti az indításkor a bejelentkezési és a fiók kiválasztása

### <a name="workbench"></a>Workbench
- A alkalmazást a Programok telepítése/törlése telepítési/eltávolítási képessége
- A hitelesítési verem frissítések arra kényszeríti az indításkor a bejelentkezési és a fiók kiválasztása
- Továbbfejlesztett egyszeri bejelentkezés (SSO) élmény Windows rendszeren
- Több bérlő más hitelesítő adatokkal tartozó felhasználók jelentkezhetnek be munkaterület lesz

### <a name="ui"></a>UI
- Általános fejlesztések és hibajavítások

### <a name="notebooks"></a>Notebookok
- Általános fejlesztések és hibajavítások

### <a name="data-preparation"></a>Adatok előkészítése 
- Továbbfejlesztett automatikus javaslatok minta alapján átalakítások végrehajtása közben
- A minta gyakoriság inspector továbbfejlesztett algoritmus
- Minta alapján átalakítások végrehajtása közben a mintaadatok és visszajelzés küldése ![küldési visszajelzési hivatkozásra kattintva képe származnia oszlop átalakítás](media/azure-machine-learning-release-notes/SendFeedbackFromDeriveColumn.png)
- Spark futásidejű fejlesztései
- Scala Pyspark váltja fel
- Zárja be az adatok nem alkalmazható az az idő adatsorozat Inspector rögzített nem tudja 
- Rögzített adatok Prep végrehajtása HDI lefagy ideje

### <a name="model-management-cli-updates"></a>Minta felügyeleti CLI frissítések 
  - Az előfizetés tulajdonjogának átruházása már nem szükséges erőforrások történő üzembe helyezéséhez. Az erőforráscsoport közreműködői elérésére elegendő a környezet beállításához lesz.
  - Engedélyezett a helyi környezet beállítása az ingyenes előfizetések 

## <a name="2017-12-sprint-2-qfe"></a>2017 12 (2 QFE súlyadatainak nyomtatása) 
**Verziószám**: 0.1.1711.15323 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([találnia, amelynek a](known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Ez az a QFE (gyorsjavítást mérnöki) kiadási kisebb kiadási. Több telemetriai problémákat, és segít a termékért felelős csoport jobb megértése érdekében a termék felhasználási módjának. A Tudásbázis későbbi próbálkozások a termék felhasználói élmény javítására is kísérhet. 

Emellett nincsenek két fontos frissítések:

- Programhiba rögzített adatok előkészítő, amely miatt nem sikerült a idő adatsorozat inspector előkészítése csomagokban megjelenítését.
- A parancssori eszköz már nem kell lennie egy Azure-előfizetés tulajdonos Machine Learning számítási ACS-fürtök kiépítéséhez. 

## <a name="2017-12-sprint-2"></a>2017 – 12 (sprint 2)
**Verziószám**: 0.1.1711.15263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([találnia, amelynek a](known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Üdvözli az Azure Machine Learning harmadik frissítése. A frissítés a munkaterület-alkalmazást, a parancssori felület (CLI) és a háttér-szolgáltatásaihoz tartalmaz fejlesztéseket. Köszönjük, hogy túlságosan azok küldését a smiles és frowns. A következő frissítéseket számos visszajelzése közvetlen eredményeként történik. 

**Fontos új szolgáltatások**
- [SQL Server és az Azure SQL Database adatforrásként támogatása](data-prep-appendix2-supported-data-sources.md#types) 
- [A részletes Learning Spark és GPU-támogatás MMLSpark használatával](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [Az összes AML tároló kompatibilisek Azure IoT peremeszközök (nem szükséges további lépések) telepítésekor](http://aka.ms/aml-iot-edge-blog)
- Regisztrált modellhez lista és a részletek nézetek elérhető Azure-portálon
- Felhasználónév/jelszó-alapú hozzáférés mellett SSH-alapú hitelesítést használó számítási célok eléréséhez. 
- Az adatok az új mintát gyakoriság Inspector élmény előkészítése. 

**Frissítések részletes** az alábbiakban látható a sprint az Azure gépi tanulás összetevő területenként részletes frissítések listáját.

### <a name="installer"></a>Telepítő
- Telepítő automatikus frissítés is, így, amely hibák elhárítása a javítások és új funkciók támogatható nélkül felhasználónak nem kell újra kell telepítenie

### <a name="workbench-authentication"></a>Munkaterület-hitelesítés
- Hitelesítési rendszer több javítását. Tudassa velünk, ha továbbra is problémát bejelentkezési problémák.
- Felhasználói felület módosítások, amelyek révén könnyebben Proxykezelő beállítások megkereséséhez.

### <a name="workbench"></a>Workbench
- Csak olvasható fájl nézet már világos kék háttér
- Áthelyezett abba, hogy több felderíthető jobb Szerkesztés gomb.
- "dsource", "dprep" és "ipynb" fájlformátumok most megjeleníthetők nyers szöveg formátumban
- A munkaterület most már rendelkezik egy olyan új szerkesztési felület, amely végigvezeti a felhasználók felé külső IDEs használatával parancsfájlok szerkesztheti, és segítségével munkaterület csak fájltípus esetében, amelyek egy gazdag szerkesztési élményt (például notebookok, adatforrások, az adatok előkészítése csomagok) szerkesztése
- A munkaterületek és a felhasználó rendelkezik hozzáférési jogosultsággal projektek betöltésekor mostantól jelentősen gyorsabb

### <a name="data-preparation"></a>Adatok előkészítése 
- Egy minta gyakoriság Inspector minták megtekintése a karakterlánc egy oszlopban. Ezek a minták használata esetén az adatok is végezhet. Megjelenik egy nézetet a érték száma inspector hasonló. A különbség, hogy mintát gyakoriságát mutatja száma. az adatok egyedi kombinációját ahelyett, hogy a számát is egyedi adatokat. Bejövő vagy kimenő egy bizonyos minta lefedett összes sort is végezhet.

![Minta gyakoriság inspector termék számára képe](media/azure-machine-learning-release-notes/pattern-inspector-product-number.png)

- Közben ajánló edge-re történő tekintse át a "oszlop származnia példa alapján" átalakítása a teljesítménnyel kapcsolatos fejlesztések

- [SQL Server és az Azure SQL Database adatforrásként támogatása](data-prep-appendix2-supported-data-sources.md#types) 

![Egy új SQL server-adatforrás létrehozása képe](media/azure-machine-learning-release-notes/sql-server-data-source.png)

- Engedélyezett és oszlopszámának "pillantással" megtekintése

![Sorok oszlop számát, a türelmi képe](media/azure-machine-learning-release-notes/row-col-count.png)

- Adatok prep engedélyezve van minden számítási környezetben
- Egy SQL Server-adatbázist használó adatforrások engedélyezve van az összes számítási környezetek
- Adatok előkészítése rács oszlopok adattípus szerint szűrhetők
- Rögzített problémát több oszlop átalakítása dátuma
- Rögzített probléma, hogy a felhasználó sikerült bejelölésével kimeneti oszlop származnia oszlop szerint példában forrásként felhasználó módosította a speciális módban kimeneti oszlop neve.

### <a name="job-execution"></a>Feladat végrehajtása
Mostantól létrehozhat és hozzáférni a remotedocker vagy a fürt számítási céljának SSH-alapú hitelesítés következő lépések segítségével:
- A következő paranccsal CLI számítási target csatolása

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
>[!NOTE]
>a parancs -k (vagy---azureml-ssh-kulcs használata) beállítás megadja, hogy hozhat létre és használhat SSH-kulcs.

- Azure ML munkaterület hozzon létre egy nyilvános kulcsot, és a kimeneti, amely a konzolon. Jelentkezzen be a számítási célt a felhasználónévvel és a nyilvános kulcs ~/.ssh/authorized_keys fájl hozzáfűzni kívánt táblát.

- Készítse elő a számítási célként, és a végrehajtási használni és az Azure ML munkaterület fogja használni ezt a kulcsot a hitelesítéshez.  

Számítási célok létrehozásáról további információk: [Azure Machine Learning kísérletezhet szolgáltatás konfigurálása](experimentation-service-configuration.md)

### <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools for AI
- Támogatása az [AI Visual Studio eszközök](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017). 

### <a name="command-line-interface-cli"></a>Parancssori felület (CLI)
- Hozzáadott `az ml datasource create` parancs lehetővé teszi, hogy egy adatforrás fájljának létrehozása a parancssorból

### <a name="model-management-and-operationalization"></a>Modell kezelése és Operationalization
- [Az összes AML tároló kompatibilisek Azure IoT peremeszközök amikor operationalized (nem szükséges további lépések)](http://aka.ms/aml-iot-edge-blog) 
- A o16n CLI a hibaüzenetek fejlesztései
- A modell felügyeleti portál UX hibajavítások  
- Egységes betűvel kis-és a modell felügyeleti attribútumokat a Részletek lap
- Valós idejű pontozási hívások időtúllépés értéke 60 másodperc
- Regisztrált modellhez lista és a Részletek nézet használható az Azure-portálon

![a portál modell részletei](media/azure-machine-learning-release-notes/model-list.jpg)

![a portál modell áttekintése](media/azure-machine-learning-release-notes/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- Spark és Learning mély [GPU-támogatás](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- Egyszerű erőforrások telepítése Resource Manager-sablonok támogatása
- A SparklyR ökoszisztéma támogatása
- [AZTK integráció](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

### <a name="sample-projects"></a>A minta-projektek
- [IRIS](https://github.com/Azure/MachineLearningSamples-Iris) és [MMLSpark](https://github.com/Azure/mmlspark) Azure ML SDK új verziójával frissíti minták

### <a name="breaking-changes"></a>Módosítások megszakítása
- Előléptetni a `--type` kapcsoló `az ml computetarget attach` egy alárendelt parancs számára. 

    - `az ml computetarget attach --type remotedocker` most már `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` most már `az ml computetarget attach cluster`

## <a name="2017-11-sprint-1"></a>2017-11 (sprint 1) 
**Verziószám**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([találnia, amelynek a](known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Ebben a kiadásban biztonság, a stabilitás és a karbantartási követelmények a munkaterület-alkalmazást, a parancssori felület és a háttér-szolgáltatásaihoz réteg fejlesztéseket hajtottunk. Köszönjük, hogy nagyon küld nekünk smiles és frowns. Hány a frissítések alatt végrehajtott visszajelzése közvetlen eredményeként. Válaszadás hamarosan!

### <a name="notable-new-features"></a>Fontos új szolgáltatások
- Az Azure ML mostantól két új Azure-régiók: **Nyugat-Európában** és **Délkelet-Ázsia**. Azok az előző régióiba csatlakoztatását **USA keleti régiója 2**, **nyugati középső Régiójában**, és **Kelet-Ausztrália**, régiók száma kapcsolásának telepített öt.
- Jelenleg engedélyezve van a Python kód szintaxisa a munkaterület alkalmazás könnyebb elolvashassák és szerkeszthessék a Python forráskód kiemelve. 
- Most elindíthatja a kedvenc IDE közvetlenül a fájl, nem pedig az egész projektet.  A fájl megnyitása munkaterület, és válassza a "Szerkesztés" elindítja a (jelenleg Visual STUDIO Code és PyCharm támogatottak) IDE az aktuális fájl és a projekt.  A munkaterület szövegszerkesztőben a fájl szerkesztése a Szerkesztés gomb melletti nyílra is kattinthat.  Fájlok írásvédettek, amíg véletlen módosításainak megakadályozza a Szerkesztés gombra kattint.
- A népszerű felrajzolási könyvtár `matplotlib` 2.1.0 verziója most szállított a munkaterületet üzemeltető alkalmazással.
- Az adatok előkészítő motor 2.0 azt frissíteni a .NET Core verzióját. Ez eltávolítja a brew-telepítés openssl szükségessége macOS alkalmazás telepítése során. Azt is paves több izgalmas adatok módon lesz a közeljövőben előkészítő szolgáltatásokat. 
- A verzió-specifikus alkalmazás kezdőlap azt rendelkezik engedélyezett, ezért több megfelelő kibocsátási megjegyzések lekérése és frissítése az aktuális alkalmazás verziójától függően kér.
- Ha a helyi felhasználói név azt a helyet, az alkalmazás most már sikeresen telepíthető. 

### <a name="detailed-updates"></a>Részletes frissítések
Alább a sprint az Azure gépi tanulás összetevő területenként részletes frissítések listájának van.

#### <a name="installer"></a>Telepítő
- Telepítő most törli azokat a telepítési könyvtár az alkalmazás régebbi verziója hozta létre.
- Egy hiba, részletes útmutatást az első 100 %-a macOS magas Sierra Beragadt telepítő rögzített.
- Most közvetlen kapcsolat van a telepítő könyvtár a felhasználót, hogy tekintse át a telepítési naplókat, abban az esetben, ha telepítés sikertelen lesz.
- Most már működik a felhasználók számára, hogy a felhasználó nevében területtel rendelkezik-e telepíteni.

#### <a name="workbench-authentication"></a>Munkaterület-hitelesítés
- Támogatja a hitelesítés a Proxy-kezelőben.
- Most már a bejelentkezés sikeres, ha felhasználói egy tűzfal mögött található. 
- Ha felhasználói fiókok kísérletezhet több Azure-régiók, és ha történik egy régió tartozik, nem érhető el, az alkalmazás már nem válaszol.
- Hitelesítés nem fejeződött be, és a hitelesítési párbeszédpanelt továbbra is látható, alkalmazás már nem megpróbálja munkaterület betölteni a helyi gyorsítótárból.

#### <a name="workbench-app"></a>Munkaterület-alkalmazás
- Python kódját szintaxis kiemelését szövegszerkesztőben engedélyezve van.
- A Szerkesztés gombra a szövegszerkesztőben szerkesztését teszi a fájl vagy egy IDE (Visual STUDIO Code és PyCharm támogatottak) vagy a beépített szövegszerkesztőben.
- Szövegszerkesztőben alapértelmezés szerint csak olvasható módban van. 
- Után az aktuális fájl mentett, és ezért nem inkonzisztencia gomb ablakállapotát most módosításainak mentése le van tiltva.
- Menti a munkaterületet üzemeltető _összes_ nem mentett fájlok futtató kezdeményezésekor.
- Munkaterület mentette az adatait az elmúlt használja munkaterület a helyi gépen, így nyílik meg automatikusan.
- Csak egyetlen példány futhat munkaterület most engedélyezett. Korábban több példány sikerült elindítani okozó problémák működő meg ugyanabban a projektben.
- Átnevezett fájl menü "Megnyitás projekt..." gombra "Hozzáadása meglévő mappát, projekt..." 
- A lapon átvált már sokkal gyorsabb.
- Súgóhivatkozások hozzáadódnak a IDE konfigurálása párbeszédpanel.
- A visszajelzési űrlap most emlékszik a legutóbbi e-mail címet.
- Smiles és frowns űrlap szöveg terület mostantól nagyobb, így meg lehet további visszajelzést küldhet! 
- A `--owner` Súgó szöveget váltani `az ml workspace create` kijavításáig.
- Hozzáadott egy "Készül" párbeszédpanel segítségével a felhasználók könnyen megtekintése és másolása az alkalmazás verziószámát.
- "A szolgáltatás javaslatot" menüpont hozzáadódik a Súgó menü.
- Már látható az alkalmazás címsor, az alkalmazás neve "Azure Machine Learning-munkaterület" megelőző kísérletezhet fióknevet.
- A verzió-specifikus alkalmazás kezdőlap jelenik meg, az észlelt alkalmazás verziója most alapján.

#### <a name="data-preparation"></a>Adatok előkészítése 
- Térkép Inspector potenciális biztonsági problémák elkerülése érdekében a külső webhely már nem tölthetők be.
- Hisztogram és értékek száma ellenőrök most már a Logaritmikus skála graph megjelenítését.
- Amikor számítás folyamatban, a minőség adatsáv egy másik színt, hogy jelezze az "kiszámítása" állapota látható.
- Oszlop metrikák most kategorikus értéket oszlopokhoz statisztika megjelenítése.
- Az adatforrás neve utolsó karaktere nem a függvény egésszé csonkítja.
- Előkészítő adatcsomag most nyitva marad lapokat, ami észrevehető teljesítménynövekedést érhet el váltáskor.
- Az adatforrásban, adatokat és metrika nézetben közötti váltáskor most oszlopok sorrendjének már változik.
- Érvénytelen megnyitása `.dprep` vagy `.dsource` fájl már nem okoz munkaterület összeomolhat.
- Előkészítő adatcsomag most is használja a relatív elérési út kimeneti _írni a fürt megosztott kötetei szolgáltatás_ átalakító.
- _Oszlop megtartása_ átalakító mostantól lehetővé teszi a felhasználó további oszlopokat szerkesztésekor.
- _Lecseréli a_ menü most ténylegesen elindítja _értéket lecseréli_ párbeszédpanel megnyitásához.
- _Cserélje le a érték_ most funkciók átalakítási hiba volt helyett várt módon.
- Előkészítő adatcsomag most abszolút elérési utat használja, való hivatkozáskor az adatfájlok környezetben történő futtatásra a csomag helyi abszolút elérési úttal rendelkező az adatfájl lehetővé teszik a projektmappa kívül.
- _Teljes fájl_ mintavételi stratégia most támogatja használata az Azure blob-adatforrásként.
- Python-kódot (előkészítő adatcsomag) előállított most hordoz magában, ha mind a CR LF, így rövid a Windows.
- _Válassza ki a metrikák_ legördülő most elrejti a tulajdonságot, amikor átvált az adatnézet.
- Munkaterület mostantól a folyamat parquet fájlok Python-futtatókörnyezet használata esetén is. Korábban csak a Spark használhatja parquet fájlok feldolgozása során. 
- Az oszlop értékeinek kiszűrése _dátum_ adattípus már nem okoz összeomlási adatok előkészítő motort.
- Metrika nézet most tiszteletben tartja a mintavételi stratégia frissítések.
- Távoli feladatok most mintavételi megfelelően működik.

#### <a name="job-execution"></a>Feladat végrehajtása
- Argumentum most már szerepel a futtatási előzményei rekord.
- Kezdődött el feladatok CLI most megjelenik a Futtatás előzmények feladat panelen automatikusan.
- Feladat panelen látható vendég felhasználók felvétele az Azure AD bérlője által létrehozott feladatok.
- Szakítsa meg a feladat panelen, és stabilabbá törlési műveletek naplóját.
- Ha a Futtatás gombra kattint, hibaüzenet akkor váltódik ki, mostantól, ha a konfigurációs fájlok hibás formátumú.
- Lezáró alkalmazás már nem zavarja a feladatok a CLI kezdődött el.
- Kezdődött el feladatok CLI most továbbra is felosztás kimenő szabvány kibővített végrehajtás egy óra után is.
- Ha adatok előkészítő csomag futtatása nem sikerült a Python/PySpark jobb hibaüzenetek jelennek meg.
- `az ml experiment clean` most már a szükségtelenné vált Docker-lemezképek, valamint a távoli virtuális gépen.
- `az ml experiment clean` most működik megfelelően a helyi cél macOS.
- Hibaüzenetek a helyi vagy távoli Docker célzó futtatásakor megtisztítja és könnyebben olvasható.
- Jobb hibaüzenet jelenik meg, amikor a HDInsight fürt átjárócsomópontjához neve nem megfelelő formátumú csatolt egy végrehajtási célként.
- Jobb hibaüzenet látható a hitelesítő adatok szolgáltatásban nem található a titkos kulcsot. 
- Támogatási Apache Spark 2.2 MMLSpark library nem frissíti.
- MMLSpark között már elérhető a tulajdonos kódolási átalakítás (háló kódolás) orvosi dokumentumokhoz.
- `matplotlib` 2.1.0 verziója most szállított out-kiszállítási a munkaterületre.

#### <a name="jupyter-notebook"></a>Jupyter notebook
- Notebook neve Keresés most már megfelelően működjön a notebookok nézetben.
- Most törölheti a Notebook a notebookok nézetben.
- Új magic `%upload_artifact` jelenik meg az előállított fájlok feltöltése a Notebook végrehajtási környezet futtatási előzményei adattárba.
- Kernel hibák most illesztett a könnyebb hibakeresés Notebook feladat állapota.
- Jupyter kiszolgáló most már megfelelően kikapcsol, amikor a felhasználó bejelentkezik az alkalmazásból.

#### <a name="azure-portal"></a>Azure Portal
- Kísérletezhet fiók és a modell felügyeleti fiókja most hozhatók létre két új Azure-régiók: Nyugat-Európában és Délkelet-Ázsia.
- Modell fiók DevTest séma most csak érhető el az első sablon kiválasztásával hozhatók létre az előfizetés esetén. 
- Az Azure portálon lévő Súgó hivatkozásra frissül, hogy a helyes dokumentációs oldal mutasson.
- Leírás mezője eltávolítják Docker kép részleteit megjelenítő oldalon, mivel nem alkalmazható.
- A szolgáltatás részletei weblap információt talál többek között az appinsights által biztosított és az automatikus méretezése beállítások kerülnek.
- Modell kezelése lap most Ez a beállítás akkor is, ha a külső cookie-kat a böngészőben le vannak tiltva. 

#### <a name="operationalization"></a>Operationalization
- Webszolgáltatás, amelynek neve "pontszám" már nem.
- Felhasználó mostantól létrehozhat egy környezet csak közreműködői hozzáférést az Azure-erőforráscsoportot, vagy az előfizetés. Már nincs szükség a teljes előfizetés tulajdonosa a hozzáférést.
- Most CLI operationalization automatikus-kiegészítést Linux élvez.
- Konstrukció képszolgáltatás mostantól támogatja az épület képek Azure IoT-szolgáltatások vagy eszközök.

#### <a name="sample-projects"></a>A minta-projektek
- [_Iris zárolásának_ ](./tutorial-classifying-iris-part-1.md) mintaprojektet:
    - `iris_pyspark.py` a rendszer átnevezi `iris_spark.py`.
    - `iris_score.py` a rendszer átnevezi `score_iris.py`.
    - `iris.dprep` és `iris.dsource` a legfrissebb adatok előkészítő kártevőfrissítések megfelelően frissül.
    - `iris.ipynb` A HDInsight-fürt működéséhez notebook módosul.
    - Futtatási előzményei be van kapcsolva a `iris.ipynb` Notebook cella.
- [_Speciális kerékpárt megosztás adatokkal adatok előkészítő_ ](./tutorial-bikeshare-dataprep.md) rögzített "Kezelni hiba érték" lépés mintaprojektet.
- [_Felnőtt nyilvántartásba adatokon MMLSpark_ ](https://github.com/Azure/MachineLearningSamples-mmlspark) mintaprojektet `docker.runconfig` YAM frissítve JSON formátumban.
- [_Elosztott Hyperparameter hangolása_ ](./scenario-distributed-tuning-of-hyperparameters.md) mintaprojektet`docker.runconfig` YAM frissítve JSON formátumban.
- Új mintaprojektet [ _CNTK használatával kép besorolás_](./scenario-image-classification-using-cntk.md).


## <a name="2017-10-sprint-0"></a>2017 10-es (sprint 0) 
**Verziószám**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([találnia, amelynek a](known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Üdvözli az Azure Machine Learning munkaterület kezdeti nyilvános előzetes verzióban ezek a Microsoft ignite-on 2017 konferencián követő első frissítésére. A fő ebben a kiadásban frissítései megbízhatóság és a stabilitás javítja.  A kritikus problémákra azt többek között:

### <a name="new-features"></a>Új funkciók
- macOS magas Sierra mostantól támogatott.

### <a name="bug-fixes"></a>Hibajavítások
#### <a name="workbench-experience"></a>Munkaterület-élmény
- A fogd és vidd egy fájlt a munkaterületre hatására az összeomlás-munkaterület Telepítésmegosztási.
- A konfigurált egy IDE munkaterület nem ismeri fel a Visual STUDIO Code terminálablakot _az ml_ parancsok.

#### <a name="workbench-authentication"></a>Munkaterület-hitelesítés
Jelentett különböző bejelentkezési és hitelesítési hibák javító frissítés számos előre meghatározott jellemzőkkel azt.
- Hitelesítési ablak tartja a popping felfelé, különösen akkor, ha nincs stabil internetkapcsolat.
- Továbbfejlesztett megbízhatósági hibákat elhárító hitelesítési jogkivonat lejáratáról körül.
- Bizonyos esetekben hitelesítési ablak kétszer.
- Munkaterület főablaka továbbra is megjeleníti a "hitelesítés" üzenetet a hitelesítési folyamat befejezését és az előugró párbeszédpanelen már.
- Ha nincs internetkapcsolat, a hitelesítési párbeszéd ugrik fel üres képernyőt.

#### <a name="data-preparation"></a>Adatok előkészítése 
- Ha egy konkrét értékre vannak leszűrve, hibákat és a hiányzó értékeket is ki van szűrve.
- Mintavételi stratégia módosítása eltávolítja az ezt követő meglévő összekapcsolási műveletek.
- Hiányzó érték cseréje átalakító nem figyelembe NaN.
- Dátum következtetésének kivételt okoz, amikor null értéket talált.

#### <a name="job-execution"></a>Feladat végrehajtása
- Ha feladat végrehajtása nem sikerült feltölteni a projektmappa, mert az túllépte a maximális mérete, nincs egyértelmű hiba üzenet.
- Ha a felhasználó Python-parancsfájl módosítja a munkakönyvtárat, a fájlok írása kimenetek mappák nem követi nyomon. 
- Ha az aktív Azure-előfizetés eltérő az aktuális projektben tartozik, a feladat elküldése eredmények 403-as hiba.
- Amikor Docker nincs jelen, ha a felhasználó megpróbál Docker egy végrehajtási célként használandó nem egyértelmű hibaüzenetet adott vissza.
- .runconfig fájl nem menti automatikusan, amikor a felhasználó a hivatkozásra kattint _futtatása_ gombra.

#### <a name="jupyter-notebook"></a>Jupyter notebook
- Notebook kiszolgáló nem indítható el, ha a felhasználó bejelentkezési bizonyos használ.
- Notebook server hibaüzenetek jelent a naplókban felhasználó számára látható.

#### <a name="azure-portal"></a>Azure Portal
- Az Azure portál sötét téma kiválasztásának hatására a modell-felügyelet panel megjeleníteni egy fekete mezőt.

#### <a name="operationalization"></a>Operationalization
- A beépített véletlenszerű nevű új Docker-lemezkép újbóli felhasználása a jegyzék egy webszolgáltatás-bővítmény frissítése hatására.
- Web service naplóit Kubernetes fürt nem lehet beolvasni.
- Félrevezető hibaüzenet van nyomtatva, amikor a felhasználó megpróbál létrehozni egy modell felügyeleti fiókja vagy egy ML számítási fiók, és engedélyekkel kapcsolatos probléma lép fel.

## <a name="next-steps"></a>További lépések

Olvassa el a áttekintése [Azure Machine Learning](overview-what-is-azure-ml.md).