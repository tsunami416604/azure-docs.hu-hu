---
title: A Machine Learning Studio-webszolgáltatás üzembe helyezése
titleSuffix: Azure Machine Learning Studio
description: Betanítási kísérlet átalakítása prediktív kísérletté, telepítésének előkészítése, majd helyezze üzembe az Azure Machine Learning Studio-webszolgáltatás, hogyan lehet.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 67d0ef88072985141c05d9da77377e5d4228a669
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270317"
---
# <a name="deploy-an-azure-machine-learning-studio-web-service"></a>Az Azure Machine Learning Studio-webszolgáltatás üzembe helyezése

Az Azure Machine Learning Studio összeállításához és teszteléséhez a prediktív elemzési megoldás lehetővé teszi. A megoldás telepítheti majd webszolgáltatásként.

Machine Learning Studio-webszolgáltatások illesztőfelületet biztosítanak között az alkalmazások és a Machine Learning Studio munkafolyamatának pontozási modelljével. Egy külső alkalmazás a Machine Learning Studio munkafolyamatának pontozási modelljével valós időben kommunikálhat. A Machine Learning Studio webszolgáltatás hívása visszaadják az előrejelzés eredményét a külső alkalmazásnak. A webszolgáltatások felé irányuló hívások indításához át kell adnia azt az API-kulcsot, amelyet a webszolgáltatás üzembe helyezésekor hozott létre. Machine Learning Studio webszolgáltatás a webprogramozási projektekben népszerű architektúra választott REST alapul.

Az Azure Machine Learning Studio webszolgáltatások két típusa van:

* Kérés-válasz szolgáltatás (RRS): Egy alacsony késleltetésű, nagy mértékben skálázható szolgáltatás, amely egyetlen rekord pontszámmodell.
* Kötegelt végrehajtási szolgáltatás (BES): Aszinkron szolgáltatás, amely a batch az adatfelderítési rekordok.

A BES bemeneti adatai az RRS-ben használt bemeneti adatokhoz hasonlóak. A fő különbség az, hogy a BES több különböző forrásból olvassa be az adatköteget, például az Azure Blob Storage-ból és Azure Table Storage-ből, az Azure SQL Database-ből, a HDInsightból (Hive-lekérdezés) és HTTP-forrásokból.


Magas szintű pont-az-nézetben helyezi üzembe a modellt három lépésben:

* **[Betanítási kísérlet létrehozása]**  – a Studióban, akkor is taníthat vagy tesztelhet egy prediktív elemzési modellt használ a betanítási adatok, hozzá kell adni, rengeteg beépített, gépi tanulási algoritmusok használatával.
* **[Alakítsa át a prediktív kísérletté]**  – Miután a modell rendelkezik betanítva a adatokkal, és készen áll az új adatok pontozása céljából, előkészítése, és korszerűsítheti az kísérlet ismeretekkel.
* **[Helyezze üzembe webszolgáltatásként]**  -a prediktív kísérletet, telepíthet egy *klasszikus* vagy *új* Azure webes (Resource Manager-alapú) szolgáltatás. A felhasználók adatokat küldeni a modell és a modell-előrejelzéseket kap.



## <a name="create-a-training-experiment"></a>Betanítási kísérlet létrehozása
Prediktív elemzési modellek betanítása, használhatja az Azure Machine Learning Studio betanítási kísérlet létrehozásához vegye fel a betanítási adatok betöltése, szükség esetén az adatok előkészítése, alkalmazza a gépi tanulási algoritmusok és az eredmények kiértékelése különböző modulok. Egy kísérlet iterálása, és próbálja meg a különböző gépi tanulási algoritmusokat és kiértékelése az eredmények összehasonlítása.

A folyamat létrehozásának és kezelésének képzési kísérletek alaposabban máshol vonatkozik. További információval a következő cikkek szolgálnak:

* [Egyszerű kísérlet létrehozása az Azure Machine Learning Studióban](create-experiment.md)
* [Prediktív megoldás fejlesztése az Azure Machine Learning Studióban](tutorial-part1-credit-risk.md)
* [A betanítási adatok importálása az Azure Machine Learning studióba](import-data.md)
* [Kísérlet ismétléseinek kezelése az Azure Machine Learning Studióban](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>A betanítási kísérlet átalakítása prediktív kísérletté
Ha a modell már betanított, készen áll a betanítási kísérlet átalakítása egy prediktív kísérletet új adatok pontozása céljából.

Egy prediktív kísérletet áttérve, hogy kiadáskezelője a betanított modell pontozási webszolgáltatásként üzembe helyezni. Felhasználók a webszolgáltatás bemeneti adatokat küldhet a modellt, és a modell vissza előrejelzési eredményeket küldi. Mivel konvertál egy prediktív kísérletet, tartsa szem előtt hogyan várhatóan mások is használhassák a modell.

A betanítási kísérlet átalakítása prediktív kísérletté, kattintson a **futtatása** a kísérlet vászon alján kattintson **webszolgáltatás beállítása**, majd **prediktív webszolgáltatás**.

![Átalakítás relevanciává kísérlet](./media/publish-a-machine-learning-web-service/figure-1.png)

Az átalakítás módjáról további információkért lásd: [hogyan készülhet fel a modell üzembe helyezése az Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

Az alábbi lépések bemutatják, hogy egy prediktív kísérletet új webszolgáltatásként üzembe helyezéséhez. A kísérlet klasszikus webszolgáltatásként is telepítheti.

## <a name="deploy-it-as-a-web-service"></a>Helyezze üzembe webszolgáltatásként

A prediktív kísérletet új webszolgáltatásként, vagy a klasszikus webszolgáltatásként telepítheti.

### <a name="deploy-the-predictive-experiment-as-a-new-web-service"></a>A prediktív kísérletté üzembe egy új webszolgáltatásként
Most, hogy a prediktív kísérletté elő van készítve, telepíthet egy új Azure webszolgáltatásként. A webszolgáltatás segítségével felhasználók küldhetnek adatokat a modell és a modell az előrejelzéseket adja vissza.

A prediktív kísérletté üzembe helyezéséhez kattintson **futtatása** a kísérlet vászon alján. Kattintson a kísérlet befejezését követően, miután **webszolgáltatás üzembe helyezése** válassza **webszolgáltatás üzembe helyezése [új]**.  Megnyílik az üzembe helyezés lap a Machine Learning Studio webszolgáltatás-portál.

> [!NOTE] 
> Egy új webszolgáltatás üzembe helyezéséhez rendelkeznie megfelelő engedélyekkel, amelyhez az előfizetésben, a web Service szolgáltatásának telepítése. További információ: [egy webszolgáltatás, az Azure Machine Learning Web Services portál használata kezelheti](manage-new-webservice.md). 

#### <a name="machine-learning-studio-web-service-portal-deploy-experiment-page"></a>Machine Learning Studio-webszolgáltatás portál üzembe helyezése kísérlet lap
A kísérlet üzembe helyezése lapon adja meg a webszolgáltatás nevét.
Válasszon egy díjcsomagot. Ha rendelkezik egy meglévő díjcsomaghoz kiválaszthatja azt, egyébként csomagot kell létrehoznia egy új ár a szolgáltatáshoz.

1. Az a **árképzési csomag** legördülő mezőben válassza egy meglévő csomagot, vagy válassza ki a **válassza új terv** lehetőséget.
2. A **csomagnév**, adjon meg egy nevet, amely azonosítja a tervet a számlán.
3. Válassza ki az egyik a **csomag havi szinten**. A terv szinten alapértelmezés szerint a csomagok alapértelmezett régiójához, és a webszolgáltatás telepítve van a régióban.

Kattintson a **telepítés** és a **rövid** megnyílik a webszolgáltatást.

A web service gyors létrehozásának oldala áttekintést nyújt a hozzáférés és az útmutató egy webszolgáltatás létrehozása után elvégzendő általános feladatokat. Itt egyszerűen hozzáférhessenek a tesztelési lapot és a felhasználás oldal.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

#### <a name="test-your-new-web-service"></a>Az új webszolgáltatás teszteléséhez
Az új webszolgáltatás teszteléséhez kattintson **webszolgáltatás teszteléséhez** a gyakori feladatok. A tesztelése oldalon tesztelheti a webszolgáltatást, egy kérelem-válasz szolgáltatás (RRS) és a egy kötegelt végrehajtási szolgáltatás (BES).

Az RRS tesztlap jeleníti meg a bemenetek, kimenetek és a globális paramétereket, amikor a kísérlet. A webszolgáltatás teszteléséhez, manuálisan is adja meg a megfelelő értékeket a bemenet vagy adjon meg egy vesszővel tagolt adatfájlból (CSV) formázott tartalmazó fájlt a tesztértékeket.

RRS használatával teszteléséhez, a lista megtekintési módokat, adjon meg megfelelő értékeket a bemeneti adatok, és kattintson **kérés-válasz tesztelése**. Az előrejelzés eredményét a bal oldali kimeneti oszlop megjelenítése.

![A webszolgáltatás üzembe helyezése](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

A BES teszteléséhez kattintson **Batch**. A Batch teszt lapon kattintson a Tallózás gombra a bemenetet a következők szerint, és válassza ki a megfelelő mintaértékek tartalmazó CSV-fájl. Ha nem rendelkezik egy CSV-fájlt, és a prediktív kísérletet a Machine Learning Studio használatával létrehozott, töltse le az adathalmaz a prediktív kísérletet, és használja azt.

Töltse le az adatkészlet, nyissa meg a Machine Learning Studióban. Nyissa meg a prediktív kísérletet, és kattintson a jobb gombbal a bemenetet a kísérlethez. A helyi menüből válassza ki a **adatkészlet** majd **letöltése**.

![A webszolgáltatás üzembe helyezése](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Kattintson a **teszt**. A kötegelt végrehajtási feladat állapotának megjelenítése mellett jobbra **teszt kötegelt feladatok**.

![A webszolgáltatás üzembe helyezése](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Az a **konfigurációs** lapon módosíthatja a leírást, cím, frissítheti a tárfiók kulcsát, és a webszolgáltatás mintaadatokat engedélyezése.

![A webszolgáltatás konfigurálása](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

A web service telepítése után, a következőket teheti:

* **Hozzáférés** , a web service API-n keresztül.
* **Kezelése** azt Azure Machine Learning Studio web services portálon keresztül.
* **Frissítés** , ha a modell módosul.

#### <a name="access-your-new-web-service"></a>Hozzáférés az új webszolgáltatáshoz
A Machine Learning Studio webszolgáltatást telepít, miután adatokat küldeni a szolgáltatás, és válaszokat kaphatnak programozott módon.

A **felhasználás** oldal nyújt a webszolgáltatás eléréséhez szükséges összes információt. Például az API-kulcsot, hogy a szolgáltatás a hitelesített hozzáférést biztosítunk.

A Machine Learning Studio webszolgáltatás elérésével kapcsolatos további információkért lásd: [használata az Azure Machine Learning Studio-webszolgáltatás service](consume-web-services.md).

#### <a name="manage-your-new-web-service"></a>Az új webszolgáltatás kezelése
A webes szolgáltatások új Machine Learning Studio-webszolgáltatások portálon kezelheti. Az a [portál főoldalára](https://services.azureml-test.net/), kattintson a **webszolgáltatások**. A webes szolgáltatások lapon törölheti, vagy másolja egy szolgáltatás. Egy adott szolgáltatás figyelése, kattintson a szolgáltatásra, és kattintson a **irányítópult**. A web service társított kötegelt feladatok figyeléséhez kattintson **kötegelt kérelem napló**.

### <a name="deploy-the-predictive-experiment-as-a-classic-web-service"></a>A prediktív kísérletté klasszikus webszolgáltatás üzembe helyezése

Most, hogy a prediktív kísérletté megfelelően elő van készítve, telepítheti az Azure klasszikus webszolgáltatásként. A webszolgáltatás segítségével felhasználók küldhetnek adatokat a modell és a modell az előrejelzéseket adja vissza.

A prediktív kísérletté üzembe helyezéséhez kattintson **futtatása** alján, a kísérlet vászonra, és kattintson a **webszolgáltatás üzembe helyezése**. A web service be van állítva, és a webszolgáltatás irányítópultján helyezte.

![A webszolgáltatás üzembe helyezése](./media/publish-a-machine-learning-web-service/figure-2.png)

#### <a name="test-your-classic-web-service"></a>A klasszikus webszolgáltatás teszteléséhez

A Machine Learning Studio Web Services portál vagy a Machine Learning Studio teszteléssel a web service.

A kérelem-válasz webszolgáltatás teszteléséhez kattintson a **tesztelése** gomb a webszolgáltatás irányítópultján. Kérje meg a bemeneti adatokat a szolgáltatás a felugró párbeszédpanel. Ezek azok az oszlopok a pontozási kísérlet által várt. Adjon meg egy adatkészletet, és kattintson a **OK**. A webszolgáltatás által generált eredmények jelennek meg az irányítópult alján.

Kattintson a **tesztelése** előzetes hivatkozásra a szolgáltatás tesztelése az Azure Machine Learning Studio Web Services portálon korábban az új webes szolgáltatás szakaszban látható módon.

A kötegelt végrehajtási szolgáltatás teszteléséhez kattintson **tesztelése** előzetes hivatkozásra. A Batch teszt lapon kattintson a Tallózás gombra a bemenetet a következők szerint, és válassza ki a megfelelő mintaértékek tartalmazó CSV-fájl. Ha nem rendelkezik egy CSV-fájlt, és a prediktív kísérletet a Machine Learning Studio használatával létrehozott, töltse le az adathalmaz a prediktív kísérletet, és használja azt.

![A webszolgáltatás teszteléséhez](./media/publish-a-machine-learning-web-service/figure-3.png)

Az a **konfigurációs** lapon módosíthatja a szolgáltatás megjelenített nevét, és adjon meg egy leírást. A név és leírás megjelenik a [az Azure portal](https://portal.azure.com/) a webszolgáltatások kezelheti.

Megadhat egy leírást a bemeneti adatokat, a kimeneti adatok és a webes paraméterek az egyes oszlopok szerinti karakterlánc beírásával **bemeneti SÉMA**, **kimeneti SÉMÁJA**, és **WEBSZOLGÁLTATÁS A paraméter**. Ezeket a leírásokat a webszolgáltatás található minta code dokumentáció használhatók.

Engedélyezheti a naplózást, és diagnosztizálhatja a hibákat, amelyek lát, ha hozzáfér a webszolgáltatást. További információkért lásd: [naplózás engedélyezése a Machine Learning Studio-webszolgáltatások](web-services-logging.md).

![A webszolgáltatás konfigurálása](./media/publish-a-machine-learning-web-service/figure-4.png)

Az előzőleg az új webes szolgáltatás szakaszban bemutatott eljárás hasonló az Azure Machine Learning Web Services portálon is konfigurálhatja a végpontok a webszolgáltatáshoz. A lehetőségek különböznek, adja hozzá, vagy módosítsa a szolgáltatás leírása, a naplózásának engedélyezése és a mintaadatok engedélyezése teszteléshez.

#### <a name="access-your-classic-web-service"></a>A klasszikus webszolgáltatás eléréséhez
A Machine Learning Studio webszolgáltatást telepít, miután adatokat küldeni a szolgáltatás, és válaszokat kaphatnak programozott módon.

Az irányítópult biztosít a webes szolgáltatás eléréséhez szükséges összes információt. Például, hogy a szolgáltatás a hitelesített hozzáférést biztosított az API-kulcsot, és API-k segítségével biztosított használatának megkezdése a kódírás.

A Machine Learning Studio webszolgáltatás elérésével kapcsolatos további információkért lásd: [használata az Azure Machine Learning Studio-webszolgáltatás service](consume-web-services.md).

#### <a name="manage-your-classic-web-service"></a>A klasszikus webszolgáltatás kezelése
Nincsenek különböző műveleteket hajthat végre webszolgáltatások figyeléséhez. Frissítse, és törölje azt. Az alapértelmezett végpont létrehozásakor az üzembe helyezés mellett klasszikus webszolgáltatás hozzáadhat további végpontokat is.

További információkért lásd: [egy Azure Machine Learning Studio-munkaterület kezelése](manage-workspace.md) és [egy webszolgáltatás, az Azure Machine Learning Studio Web Services portál használata kezelheti](manage-new-webservice.md).

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning Studio web service endpoints using the REST API, see **Azure Machine Learning Studio web service endpoints**.
-->

## <a name="update-the-web-service"></a>A web service frissítése
Hajtsa végre a módosításokat, a modell frissítése a további betanítási adatok, például a web Service, és helyezze üzembe újra, írja felül az eredeti webszolgáltatás.

A web service frissítéséhez nyissa meg a web Service szolgáltatásának telepítése és a egy szerkeszthető Másolás gombra kattintva győződjön meg arról, hogy követte az eredeti prediktív kísérletté **SAVE AS**. Hajtsa végre a módosításokat, és kattintson a **webszolgáltatás üzembe helyezése**.

Mivel ez a kísérlet előtt üzembe helyezte, felülírása (klasszikus webszolgáltatás) vagy (új webszolgáltatás) frissítse a meglévő szolgáltatást szeretne rendszer. Kattintson a **Igen** vagy **frissítés** a meglévő webes szolgáltatás leáll, és telepíti az új prediktív kísérletet van üzembe helyezve a helyére.

> [!NOTE]
> Az eredeti webszolgáltatás konfigurációs változtatásokat hajtott végre, ha például megad egy új megjelenített név vagy leírás, szüksége lesz, írja be újra ezeket az értékeket.

A webszolgáltatás frissítéséhez az egyik lehetőség, hogy a modell programozott újratanítás. További információkért lásd: [Retrain Machine Learning Studio-modellek](retrain-models-programmatically.md).

## <a name="next-steps"></a>További lépések

* Üzembe helyezés működésével kapcsolatos további technikai részletek: [hogyan egy Machine Learning Studio modell különböző fázisokon halad kísérletből-szolgáltatáscsomagot Web Service](model-progression-experiment-to-web-service.md).

* Felkészülés a modell üzembe helyezése a részletekért lásd: [hogyan készülhet fel a modell üzembe helyezése az Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

* A REST API felhasználására, illetve a webszolgáltatás elérésére számos megoldás áll rendelkezésre. Lásd: [használata az Azure Machine Learning Studio webszolgáltatás](consume-web-services.md).


<!-- internal links -->
[Betanítási kísérlet létrehozása]: #create-a-training-experiment
[Alakítsa át a prediktív kísérletté]: #convert-the-training-experiment-to-a-predictive-experiment
[Helyezze üzembe webszolgáltatásként]: #deploy-it-as-a-web-service
[új]: #deploy-the-predictive-experiment-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
