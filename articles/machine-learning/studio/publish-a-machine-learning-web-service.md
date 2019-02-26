---
title: A Machine Learning Studio-webszolgáltatás üzembe helyezése
titleSuffix: Azure Machine Learning Studio
description: Betanítási kísérlet átalakítása prediktív kísérletté, telepítésének előkészítése, majd helyezze üzembe az Azure Machine Learning Studio-webszolgáltatás, hogyan lehet.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: b77c21b75147a9bb5203effb20379dc208122d05
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819141"
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
* **Üzembe helyezése** , mint egy **[új webszolgáltatás]** vagy egy **[klasszikus webszolgáltatás]** – Ha a prediktív kísérletet, telepít egy Az Azure web service, a felhasználók adatokat küldeni a modell és a modell-előrejelzéseket kap.

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

## <a name="deploy-it-as-a-new-web-service"></a>Helyezze üzembe, egy új webszolgáltatás

Most, hogy a prediktív kísérletté elő van készítve, telepíthet új (Resource Manager-alapú) Azure webszolgáltatásként. A webszolgáltatás segítségével felhasználók küldhetnek adatokat a modell és a modell az előrejelzéseket adja vissza.

A prediktív kísérletté üzembe helyezéséhez kattintson **futtatása** a kísérlet vászon alján. Kattintson a kísérlet befejezését követően, miután **webszolgáltatás üzembe helyezése** válassza **webszolgáltatás üzembe helyezése [új]**.  Megnyílik az üzembe helyezés lap a Machine Learning Studio webszolgáltatás-portál.

> [!NOTE] 
> Egy új webszolgáltatás üzembe helyezéséhez rendelkeznie megfelelő engedélyekkel, amelyhez az előfizetésben, a web Service szolgáltatásának telepítése. További információ: [egy webszolgáltatás, az Azure Machine Learning Web Services portál használata kezelheti](manage-new-webservice.md). 

### <a name="machine-learning-studio-web-service-portal-deploy-experiment-page"></a>Machine Learning Studio-webszolgáltatás portál üzembe helyezése kísérlet lap

A kísérlet üzembe helyezése lapon adja meg a webszolgáltatás nevét.
Válasszon egy díjcsomagot. Ha rendelkezik egy meglévő díjcsomaghoz kiválaszthatja azt, egyébként csomagot kell létrehoznia egy új ár a szolgáltatáshoz.

1. Az a **árképzési csomag** legördülő mezőben válassza egy meglévő csomagot, vagy válassza ki a **válassza új terv** lehetőséget.
2. A **csomagnév**, adjon meg egy nevet, amely azonosítja a tervet a számlán.
3. Válassza ki az egyik a **csomag havi szinten**. A terv szinten alapértelmezés szerint a csomagok alapértelmezett régiójához, és a webszolgáltatás telepítve van a régióban.

Kattintson a **telepítés** és a **rövid** megnyílik a webszolgáltatást.

A web service gyors létrehozásának oldala áttekintést nyújt a hozzáférés és az útmutató egy webszolgáltatás létrehozása után elvégzendő általános feladatokat. Itt egyszerűen hozzáférhessenek a tesztelési lapot és a felhasználás oldal.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Az új webszolgáltatás teszteléséhez

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

### <a name="access-your-new-web-service"></a>Hozzáférés az új webszolgáltatáshoz

A Machine Learning Studio webszolgáltatást telepít, miután adatokat küldeni a szolgáltatás, és válaszokat kaphatnak programozott módon.

A **felhasználás** oldal nyújt a webszolgáltatás eléréséhez szükséges összes információt. Például az API-kulcsot, hogy a szolgáltatás a hitelesített hozzáférést biztosítunk.

A Machine Learning Studio webszolgáltatás elérésével kapcsolatos további információkért lásd: [használata az Azure Machine Learning Studio-webszolgáltatás service](consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Az új webszolgáltatás kezelése

A webes szolgáltatások új Machine Learning Studio-webszolgáltatások portálon kezelheti. Az a [portál főoldalára](https://services.azureml-test.net/), kattintson a **webszolgáltatások**. A webes szolgáltatások lapon törölheti, vagy másolja egy szolgáltatás. Egy adott szolgáltatás figyelése, kattintson a szolgáltatásra, és kattintson a **irányítópult**. A web service társított kötegelt feladatok figyeléséhez kattintson **kötegelt kérelem napló**.

### <a id="multi-region"></a> Az új webszolgáltatás üzembe helyezése több régióban

Könnyedén telepíthet egy új webszolgáltatás több régióban több előfizetéseket vagy munkaterületeket nélkül.

Díjszabás szolgáltatásé, ezért meg kell határoznia egy számlázási csomagot minden olyan régióhoz, amelyben a webszolgáltatást telepíti.

#### <a name="create-a-plan-in-another-region"></a>Hozzon létre egy csomagot egy másik régióban

1. Jelentkezzen be a [Microsoft Azure Machine Learning-webszolgáltatások](https://services.azureml.net/).
2. Kattintson a **csomagok** menüpont.
3. A tervek keresztül nézet lap, kattintson **új**.
4. Az a **előfizetés** legördülő menüben válassza ki az előfizetést, amelyben létre kívánja hozni az új csomag.
5. Az a **régió** legördülő menüben válassza ki a régiót az új csomag. A kiválasztott régióban megtervezése beállítások jelennek meg a **beállítások megtervezése** lap részében.
6. Az a **erőforráscsoport** legördülő menüben válassza egy erőforráscsoport, a csomag számára. További információ az erőforráscsoportokkal látható [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md).
7. A **csomagnév** írja be a csomag nevét.
8. A **csomagokkal**, kattintson az új csomag a számlázási szintet.
9. Kattintson a **Create** (Létrehozás) gombra.

#### <a name="deploy-the-web-service-to-another-region"></a>A webszolgáltatás üzembe helyezése egy másik régióba

1. A Microsoft Azure Machine Learning webszolgáltatások lapon kattintson a **webszolgáltatások** menüpont.
2. Válassza ki a webszolgáltatás telepítésekor egy új régióban.
3. Kattintson a **másolási**.
4. A **webszolgáltatás neve**, írja be a web service új nevét.
5. A **webalkalmazás-szolgáltatás leírása**, írja be a webszolgáltatás leírását.
6. Az a **előfizetés** legördülő menüben válassza ki az előfizetést, amelyben létre kívánja hozni az új webszolgáltatásként.
7. Az a **erőforráscsoport** legördülő menüben válassza egy erőforráscsoport, a webszolgáltatás. További információ az erőforráscsoportokkal látható [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md).
8. Az a **régió** legördülő menüben válassza ki a régiót, amelyben a webszolgáltatás üzembe helyezéséhez.
9. Az a **tárfiók** legördülő menüben válassza ki a storage-fiók, amely a web service tárolja.
10. Az a **árképzési csomag** legördülő menüben válassza ki azt a csomagot a 8. lépésben kiválasztott régióban.
11. Kattintson a **másolási**.

## <a name="deploy-it-as-a-classic-web-service"></a>Klasszikus webszolgáltatásként üzembe helyezése

Most, hogy a prediktív kísérletté megfelelően elő van készítve, telepítheti az Azure klasszikus webszolgáltatásként. A webszolgáltatás segítségével felhasználók küldhetnek adatokat a modell és a modell az előrejelzéseket adja vissza.

A prediktív kísérletté üzembe helyezéséhez kattintson **futtatása** alján, a kísérlet vászonra, és kattintson a **webszolgáltatás üzembe helyezése**. A web service be van állítva, és a webszolgáltatás irányítópultján helyezte.

![A webszolgáltatás üzembe helyezése](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>A klasszikus webszolgáltatás teszteléséhez

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

### <a name="access-your-classic-web-service"></a>A klasszikus webszolgáltatás eléréséhez

A Machine Learning Studio webszolgáltatást telepít, miután adatokat küldeni a szolgáltatás, és válaszokat kaphatnak programozott módon.

Az irányítópult biztosít a webes szolgáltatás eléréséhez szükséges összes információt. Például, hogy a szolgáltatás a hitelesített hozzáférést biztosított az API-kulcsot, és API-k segítségével biztosított használatának megkezdése a kódírás.

A Machine Learning Studio webszolgáltatás elérésével kapcsolatos további információkért lásd: [használata az Azure Machine Learning Studio-webszolgáltatás service](consume-web-services.md).

### <a name="manage-your-classic-web-service"></a>A klasszikus webszolgáltatás kezelése

Nincsenek különböző műveleteket hajthat végre webszolgáltatások figyeléséhez. Frissítse, és törölje azt. Az alapértelmezett végpont létrehozásakor az üzembe helyezés mellett klasszikus webszolgáltatás hozzáadhat további végpontokat is.

További információkért lásd: [egy Azure Machine Learning Studio-munkaterület kezelése](manage-workspace.md) és [egy webszolgáltatás, az Azure Machine Learning Studio Web Services portál használata kezelheti](manage-new-webservice.md).

## <a name="update-the-web-service"></a>A web service frissítése
Hajtsa végre a módosításokat, a modell frissítése a további betanítási adatok, például a web Service, és helyezze üzembe újra, írja felül az eredeti webszolgáltatás.

A web service frissítéséhez nyissa meg a web Service szolgáltatásának telepítése és a egy szerkeszthető Másolás gombra kattintva győződjön meg arról, hogy követte az eredeti prediktív kísérletté **SAVE AS**. Hajtsa végre a módosításokat, és kattintson a **webszolgáltatás üzembe helyezése**.

Mivel ez a kísérlet előtt üzembe helyezte, felülírása (klasszikus webszolgáltatás) vagy (új webszolgáltatás) frissítse a meglévő szolgáltatást szeretne rendszer. Kattintson a **Igen** vagy **frissítés** a meglévő webes szolgáltatás leáll, és telepíti az új prediktív kísérletet van üzembe helyezve a helyére.

> [!NOTE]
> Az eredeti webszolgáltatás konfigurációs változtatásokat hajtott végre, ha például megad egy új megjelenített név vagy leírás, szüksége lesz, írja be újra ezeket az értékeket.

A webszolgáltatás frissítéséhez az egyik lehetőség, hogy a modell programozott újratanítás. További információkért lásd: [Retrain Machine Learning Studio-modellek](/azure/machine-learning/studio/retrain-machine-learning-model).

## <a name="next-steps"></a>További lépések

* Üzembe helyezés működésével kapcsolatos további technikai részletek: [hogyan egy Machine Learning Studio modell különböző fázisokon halad kísérletből-szolgáltatáscsomagot Web Service](model-progression-experiment-to-web-service.md).

* Felkészülés a modell üzembe helyezése a részletekért lásd: [hogyan készülhet fel a modell üzembe helyezése az Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

* A REST API felhasználására, illetve a webszolgáltatás elérésére számos megoldás áll rendelkezésre. Lásd: [használata az Azure Machine Learning Studio webszolgáltatás](consume-web-services.md).


<!-- internal links -->
[Betanítási kísérlet létrehozása]: #create-a-training-experiment
[Alakítsa át a prediktív kísérletté]: #convert-the-training-experiment-to-a-predictive-experiment
[Új webszolgáltatás]: #deploy-it-as-a-new-web-service
[Klasszikus webszolgáltatás]: #deploy-it-as-a-classic-web-service
[új]: #deploy-the-predictive-experiment-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
