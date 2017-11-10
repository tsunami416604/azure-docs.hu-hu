---
title: "Az Azure ML munkaterület kibocsátási megjegyzések a sprint 2017. November 1."
description: "Ez a dokumentum részletesen a frissítéseket az Azure ML sprint 1 kiadása"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/06/2017
ms.openlocfilehash: 2b2f35b3241bd1700bb1fb3319d38fdfda2545f9
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2017
---
# <a name="sprint-1---november-2017"></a>1 - November 2017 súlyadatainak nyomtatása 

**Verziószám: 0.1.1710.31013**

Üdvözli az Azure Machine Learning-munkaterület második frissítésére. Biztonság, a stabilitás és a karbantartási követelmények fejlesztéseket végezze el a munkaterület-alkalmazást, a parancssori felület és a háttér-szolgáltatásaihoz réteg továbbra is. Köszönjük, hogy nagyon küld nekünk smiles és frowns. Hány a frissítések alatt végrehajtott visszajelzése közvetlen eredményeként. Adjon válaszadás hamarosan!

## <a name="notable-new-features"></a>Fontos új szolgáltatások
- Az Azure ML mostantól két új Azure-régiók: **Nyugat-Európában** és **Délkelet-Ázsia**. Azok az előző régióiba csatlakoztatását **USA keleti régiója 2**, **nyugati középső Régiójában**, és **Kelet-Ausztrália**, régiók száma kapcsolásának telepített öt.
- Jelenleg engedélyezve van a Python kód szintaxisa a munkaterület alkalmazás könnyebb elolvashassák és szerkeszthessék a Python forráskód kiemelve. 
- Most elindíthatja a kedvenc IDE közvetlenül a fájl, nem pedig az egész projektet.  A fájl megnyitása munkaterület, és válassza a "Szerkesztés" elindítja a (jelenleg Visual STUDIO Code és PyCharm támogatottak) IDE az aktuális fájl és a projekt.  A munkaterület szövegszerkesztőben a fájl szerkesztése a Szerkesztés gomb melletti nyílra is kattinthat.  Fájlok írásvédettek, amíg véletlen módosításainak megakadályozza a Szerkesztés gombra kattint.
- A népszerű felrajzolási könyvtár `matplotlib` 2.1.0 verziója most szállított a munkaterületet üzemeltető alkalmazással.
- Az adatok előkészítő motor 2.0 azt frissíteni a .NET Core verzióját. Ez eltávolítja a brew-telepítés openssl szükségessége macOS alkalmazás telepítése során. Azt is paves több izgalmas adatok módon lesz a közeljövőben előkészítő szolgáltatásokat. 
- A verzió-specifikus alkalmazás kezdőlap azt rendelkezik engedélyezett, ezért több megfelelő kibocsátási megjegyzések lekérése és frissítése az aktuális alkalmazás verziójától függően kér.
- Ha a helyi felhasználói név azt a helyet, az alkalmazás most már sikeresen telepíthető. 

## <a name="detailed-updates"></a>Részletes frissítések
Alább a sprint az Azure gépi tanulás összetevő területenként részletes frissítések listájának van.

### <a name="installer"></a>Telepítő
- Telepítő most törli azokat a telepítési könyvtár az alkalmazás régebbi verziója hozta létre.
- Egy hiba, részletes útmutatást az első 100 %-a macOS magas Sierra Beragadt telepítő rögzített.
- Most közvetlen kapcsolat van a telepítő könyvtár a felhasználót, hogy tekintse át a telepítési naplókat, abban az esetben, ha telepítés sikertelen lesz.
- Most már működik a felhasználók számára, hogy a felhasználó nevében területtel rendelkezik-e telepíteni.

### <a name="workbench-authentication"></a>Munkaterület-hitelesítés
- Támogatja a hitelesítés a Proxy-kezelőben.
- Most már a bejelentkezés sikeres, ha felhasználói egy tűzfal mögött található. 
- Ha felhasználói fiókok kísérletezhet több Azure-régiók, és ha történik egy régió tartozik, nem érhető el, az alkalmazás már nem válaszol.
- Hitelesítés nem fejeződött be, és a hitelesítési párbeszédpanelt továbbra is látható, alkalmazás már nem megpróbálja munkaterület betölteni a helyi gyorsítótárból.

### <a name="workbench-app"></a>Munkaterület-alkalmazás
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

### <a name="data-preparation"></a>Adatok előkészítése 
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

### <a name="job-execution"></a>Feladat végrehajtása
- Argumentum most már szerepel a futtatási előzményei rekord.
- Kezdődött el feladatok CLI most megjelenik a Futtatás előzmények feladat panelen automatikusan.
- Feladat panelen látható vendég felhasználók felvétele az AAD-bérlőt az által létrehozott feladatok.
- Szakítsa meg a feladat panelen, és stabilabbá törlési műveletek naplóját.
- Ha a Futtatás gombra kattint, hibaüzenet akkor váltódik ki, mostantól, ha a konfigurációs fájlok hibás formátumú.
- Lezáró alkalmazás már nem zavarja a feladatok a CLI kezdődött el.
- Kezdődött el feladatok CLI most továbbra is felosztás kimenő szabvány kibővített végrehajtás egy óra után is.
- Ha adatok előkészítő csomag futtatása nem sikerült a Python/PySpark jobb hibaüzenetek jelennek meg.
- `az ml experiment clean`most már a szükségtelenné vált Docker-lemezképek, valamint a távoli virtuális gépen.
- `az ml experiment clean`most működik megfelelően a helyi cél macOS.
- Hibaüzenetek a helyi vagy távoli Docker célzó futtatásakor megtisztítja és könnyebben olvasható.
- Jobb hibaüzenet jelenik meg, amikor a HDInsight fürt átjárócsomópontjához neve nem megfelelő formátumú csatolt egy végrehajtási célként.
- Jobb hibaüzenet látható a hitelesítő adatok szolgáltatásban nem található a titkos kulcsot. 
- Támogatási Apache Spark 2.2 MMLSpark library nem frissíti.
- MMLSpark között már elérhető a tulajdonos kódolási átalakítás (háló kódolás) orvosi dokumentumokhoz.
- `matplotlib`2.1.0 verziója most szállított out-kiszállítási a munkaterületre.

### <a name="jupyter-notebook"></a>Jupyter notebook
- Notebook neve Keresés most már megfelelően működjön a notebookok nézetben.
- Most törölheti a Notebook a notebookok nézetben.
- Új magic `%upload_artifact` jelenik meg az előállított fájlok feltöltése a Notebook végrehajtási környezet futtatási előzményei adattárba.
- Kernel hibák most illesztett a könnyebb hibakeresés Notebook feladat állapota.
- Jupyter kiszolgáló most már megfelelően kikapcsol, amikor a felhasználó bejelentkezik az alkalmazásból.

### <a name="azure-portal"></a>Azure Portal
- Kísérletezhet fiók és a modell felügyeleti fiókja most hozhatók létre két új Azure-régiók: Nyugat-Európában és Délkelet-Ázsia.
- Modell fiók DevTest séma most csak érhető el az első sablon kiválasztásával hozhatók létre az előfizetés esetén. 
- Az Azure portálon lévő Súgó hivatkozásra frissül, hogy a helyes dokumentációs oldal mutasson.
- Leírás mezője eltávolítják Docker kép részleteit megjelenítő oldalon, mivel nem alkalmazható.
- A szolgáltatás részletei weblap információt talál többek között az appinsights által biztosított és az automatikus méretezése beállítások kerülnek.
- Modell kezelése lap most Ez a beállítás akkor is, ha a külső cookie-k le vannak tiltva, a böngészőben. 

### <a name="operationalization"></a>Operationalization
- Webszolgáltatás, amelynek neve "pontszám" már nem.
- Felhasználó most létrehozhat egy központi telepítési környezetben csak egy Azure erőforráscsoport tulajdonosának hozzáférést. Már nincs szükség a teljes előfizetés tulajdonosa a hozzáférést.
- Most CLI operationalization automatikus-kiegészítést Linux élvez.
- Konstrukció képszolgáltatás mostantól támogatja az épület képek Azure IoT-szolgáltatások vagy eszközök.

### <a name="sample-projects"></a>A minta-projektek
- [_Iris zárolásának_ ](./tutorial-classifying-iris-part-1.md) mintaprojektet:
    - `iris_pyspark.py`a rendszer átnevezi `iris_spark.py`.
    - `iris_score.py`a rendszer átnevezi `score_iris.py`.
    - `iris.dprep`és `iris.dsource` a legfrissebb adatok előkészítő kártevőfrissítések megfelelően frissül.
    - `iris.ipynb`A HDInsight-fürt működéséhez notebook módosul.
    - Futtatási előzményei be van kapcsolva a `iris.ipynb` Notebook cella.
- [_Speciális kerékpárt megosztás adatokkal adatok előkészítő_ ](./tutorial-bikeshare-dataprep.md) rögzített "Kezelni hiba érték" lépés mintaprojektet.
- [_Felnőtt nyilvántartásba adatokon MMLSpark_ ](https://github.com/Azure/MachineLearningSamples-mmlspark) mintaprojektet `docker.runconfig` YAM frissítve JSON formátumban.
- [_Elosztott Hyperparameter hangolása_ ](./scenario-distributed-tuning-of-hyperparameters.md) mintaprojektet`docker.runconfig` YAM frissítve JSON formátumban.
- Új mintaprojektet [ _CNTK használatával kép besorolás_](./scenario-image-classification-using-cntk.md).
