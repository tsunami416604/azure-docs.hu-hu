---
title: "Az Azure ML munkaterület kibocsátási megjegyzések a sprint 2017. December 2."
description: "Ez a dokumentum részletesen a frissítéseket az Azure ml sprint 2 kiadásban"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/04/2017
ms.openlocfilehash: cb620040f8c82fd2015f93963d99739d38ec6db3
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="sprint-2---december-2017"></a>Sprint 2 – 2017. december 

#### <a name="version-number-01171115263"></a>Verziószám: 0.1.1711.15263

>Ez hogyan lehetséges [verziószámának](known-issues-and-troubleshooting-guide.md).

Üdvözli az Azure Machine Learning-munkaterület harmadik frissítésére. A frissítés a munkaterület-alkalmazást, a parancssori felület (CLI) és a háttér-szolgáltatásaihoz tartalmaz fejlesztéseket. Köszönjük, hogy túlságosan azok küldését a smiles és frowns. A következő frissítéseket számos visszajelzése közvetlen eredményeként történik. 

## <a name="notable-new-features"></a>Fontos új szolgáltatások
- [SQL Server és az Azure SQL Database adatforrásként támogatása](data-prep-appendix2-supported-data-sources.md#types) 
- [A részletes Learning Spark és GPU-támogatás MMLSpark használatával](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [Az összes AML tároló kompatibilisek Azure IoT peremeszközök (nem szükséges további lépések) telepítésekor](http://aka.ms/aml-iot-edge-blog)
- Regisztrált modellhez lista és a részletek nézetek elérhető Azure-portálon
- Felhasználónév/jelszó-alapú hozzáférés mellett SSH-alapú hitelesítést használó számítási célok eléréséhez. 
- Az adatok az új mintát gyakoriság Inspector élmény előkészítése. 

## <a name="detailed-updates"></a>Részletes frissítések
Az alábbiakban az Azure Machine Learning a sprint a minden összetevő területén részletes frissítések listáját.

### <a name="installer"></a>Telepítő
- Telepítő automatikus frissítés is, így, amely hibák elhárítása a javítások és új funkciók támogatható nélkül felhasználónak nem kell újra kell telepítenie

### <a name="workbench-authentication"></a>Munkaterület-hitelesítés
- Hitelesítési rendszer több javítását. Tudassa velünk, ha továbbra is problémát bejelentkezési problémák.
- Felhasználói felület módosítások, amelyek révén könnyebben Proxykezelő beállítások megkereséséhez.

### <a name="workbench"></a>Workbench
- Csak olvasható fájl nézet már világos kék háttér
- Áthelyezett abba, hogy több felderíthető jobb Szerkesztés gomb.
- "dsource", "dprep" és "ipynb" fájlformátumok most megjeleníthetők nyers szöveg formátumban
- A munkaterület most már rendelkezik egy olyan új szerkesztési felület, amely végigvezeti a felhasználók felé külső IDE használatával parancsfájlok szerkesztheti, és segítségével munkaterület csak fájltípus esetében, amelyek egy gazdag szerkesztési élményt (például notebookok, adatforrások, az adatok előkészítése csomagok) szerkesztése
- A munkaterületek és a felhasználó rendelkezik hozzáférési jogosultsággal projektek betöltésekor mostantól jelentősen gyorsabb

### <a name="data-preparation"></a>Adatok előkészítése 
- Egy minta gyakoriság Inspector minták megtekintése a karakterlánc egy oszlopban. Ezek a minták használata esetén az adatok is végezhet. Megjelenik egy nézetet a érték száma inspector hasonló. A különbség, hogy mintát gyakoriságát mutatja száma. az adatok egyedi kombinációját ahelyett, hogy a számát is egyedi adatokat. Bejövő vagy kimenő egy bizonyos minta lefedett összes sort is végezhet.

![Minta gyakoriság inspector termék számára képe](media/release-notes-sprint-2/pattern-inspector-product-number.png)

- Közben ajánló edge-re történő tekintse át a "oszlop származnia példa alapján" átalakítása a teljesítménnyel kapcsolatos fejlesztések

- [SQL Server és az Azure SQL Database adatforrásként támogatása](data-prep-appendix2-supported-data-sources.md#types) 

![Egy új SQL server-adatforrás létrehozása képe](media/release-notes-sprint-2/sql-server-data-source.png)

- Engedélyezett és oszlopszámának "pillantással" megtekintése

![Sorok oszlop számát, a türelmi képe](media/release-notes-sprint-2/row-col-count.png)

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
[!NOTE] a parancs -k (vagy---azureml-ssh-kulcs használata) beállítás megadja, hogy hozhat létre és használhat SSH-kulcs.

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

![a portál modell részletei](media/release-notes-sprint-2/model-list.jpg)

![a portál modell áttekintése](media/release-notes-sprint-2/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- Spark és Learning mély [GPU-támogatás](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- Egyszerű erőforrások telepítése Resource Manager-sablonok támogatása
- A SparklyR ökoszisztéma támogatása
- [AZTK integráció](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

### <a name="sample-projects"></a>A minta-projektek
- [IRIS](https://github.com/Azure/MachineLearningSamples-Iris) és [MMLSpark](https://github.com/Azure/mmlspark) Azure ML SDK új verziójával frissíti minták

## <a name="breaking-changes"></a>MÓDOSÍTÁSOK MEGSZAKÍTÁSA
- Előléptetni a `--type` kapcsoló `az ml computetarget attach` egy alárendelt parancs. 

    - `az ml computetarget attach --type remotedocker` most már `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` most már `az ml computetarget attach cluster`
