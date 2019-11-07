---
title: Webszolgáltatás üzembe helyezése
titleSuffix: ML Studio (classic) Azure
description: Betanítási kísérlet előkészítése prediktív kísérletre, előkészítés üzembe helyezéshez, majd Azure Machine Learning Studio (klasszikus) webszolgáltatásként való üzembe helyezése.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 846b356d98a68000f48609de1f19ff2f7de3cab9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693461"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Azure Machine Learning Studio (klasszikus) webszolgáltatás üzembe helyezése

A Azure Machine Learning Studio (klasszikus) lehetővé teszi prediktív elemzési megoldások készítését és tesztelését. Ezután webszolgáltatásként üzembe helyezheti a megoldást.

A Machine Learning Studio (klasszikus) webszolgáltatások felületet biztosítanak egy alkalmazás és egy Machine Learning Studio (klasszikus) munkafolyamat pontozási modellje között. Egy külső alkalmazás valós időben tud kommunikálni egy Machine Learning Studio (klasszikus) munkafolyamat-pontozási modellel. Egy Machine Learning Studio (klasszikus) webszolgáltatás hívása visszaadja az előrejelzési eredményeket egy külső alkalmazásnak. A webszolgáltatások felé irányuló hívások indításához át kell adnia azt az API-kulcsot, amelyet a webszolgáltatás üzembe helyezésekor hozott létre. A Machine Learning Studio (klasszikus) webszolgáltatás egy REST-alapú, népszerű architektúra a webes programozási projektekhez.

Azure Machine Learning Studio klasszikus verziója két típusú webszolgáltatással rendelkezik:

* Kérelem-válasz szolgáltatás (RR): alacsony késésű, rugalmasan méretezhető szolgáltatás, amely egyetlen adatrekordot mutat be.
* Batch végrehajtási szolgáltatás (BES): egy aszinkron szolgáltatás, amely adatrekordok kötegét szerzi be.

A BES bemeneti adatai az RRS-ben használt bemeneti adatokhoz hasonlóak. A fő különbség az, hogy a BES több különböző forrásból olvassa be az adatköteget, például az Azure Blob Storage-ból és Azure Table Storage-ből, az Azure SQL Database-ből, a HDInsightból (Hive-lekérdezés) és HTTP-forrásokból.

A magas szintű nézetből három lépésben helyezheti üzembe a modelljét:

* **[Képzési kísérlet létrehozása]** – a Studio klasszikus verziójában egy prediktív elemzési modellt is betaníthat, és tesztelheti az Ön által megadott betanítási adatmennyiséget a beépített gépi tanulási algoritmusok használatával.
* **[Átalakítás prediktív kísérletre]** – ha a modell már meglévő adataival lett betanítva, és készen áll arra, hogy új adatgyűjtést végezzen, előkészíti és egyszerűsíti a kísérletet az előrejelzésekhez.
* **Üzembe helyezés** **[Új webszolgáltatás]** vagy **[Klasszikus webszolgáltatás]** – ha a prediktív kísérletet Azure webszolgáltatásként helyezi üzembe, a felhasználók elküldhetik az adatait a modellbe, és megkapják a modell előrejelzéseit.

## <a name="create-a-training-experiment"></a>Képzési kísérlet létrehozása

A prediktív elemzési modellek betanításához a Azure Machine Learning Studio klasszikus verzióját használja egy képzési kísérlet létrehozásához, ahol különféle modulokat tartalmaz a betanítási adatgyűjtés betöltéséhez, a szükséges adatelőkészítéshez, a gépi tanulási algoritmusok alkalmazásához, és értékelje ki az eredményeket. Megismételheti a kísérletet, és különböző gépi tanulási algoritmusok kipróbálásával összehasonlíthatja és értékelheti az eredményeket.

A képzési kísérletek létrehozásának és kezelésének folyamata máshol még alaposabban szerepel. További információval a következő cikkek szolgálnak:

* [Egyszerű kísérlet létrehozása Azure Machine Learning Studio (klasszikus)](create-experiment.md)
* [Prediktív megoldás fejlesztése Azure Machine Learning Studio (klasszikus)](tutorial-part1-credit-risk.md)
* [Betanítási adatai importálása Azure Machine Learning Studioba (klasszikus)](import-data.md)
* [Kísérletezési ismétlések kezelése Azure Machine Learning Studio (klasszikus)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>A betanítási kísérlet átalakítása prediktív kísérletre

Ha betanítja a modellt, készen áll arra, hogy a betanítási kísérletet egy prediktív kísérletbe konvertálja, hogy az új adatait felhasználja.

A prediktív kísérletre való áttéréssel a betanított modell készen áll a pontozási webszolgáltatásként való üzembe helyezésre. A webszolgáltatás felhasználói bemeneti adatokat küldhetnek a modellbe, és a modell visszaküldi az előrejelzési eredményeket. A prediktív kísérletre való áttérés során ne feledje, hogy a modelleket mások is használják.

A betanítási kísérlet prediktív kísérletre való átalakításához kattintson a **Futtatás** gombra a kísérlet vászon alján, kattintson a **webszolgáltatás beállítása**, majd a **prediktív webszolgáltatás**elemre.

![Konvertálás pontozási kísérletre](./media/publish-a-machine-learning-web-service/figure-1.png)

További információ a konverzió végrehajtásáról: [modell előkészítése a telepítéshez Azure Machine learning Studio (klasszikus)](convert-training-experiment-to-scoring-experiment.md).

A következő lépések leírják, hogyan helyezhet üzembe egy prediktív kísérletet új webszolgáltatásként. A kísérletet klasszikus webszolgáltatásként is üzembe helyezheti.

## <a name="deploy-it-as-a-new-web-service"></a>Üzembe helyezés új webszolgáltatásként

Most, hogy előkészítette a prediktív kísérletet, üzembe helyezheti új (Resource Manager-alapú) Azure-webszolgáltatásként. A webszolgáltatás használatával a felhasználók elküldhetik az adatait a modellbe, és a modell visszaadja az előrejelzéseit.

A prediktív kísérlet üzembe helyezéséhez kattintson a **Run (Futtatás** ) gombra a kísérlet vászon alján. A kísérlet befejezését követően kattintson a **webszolgáltatás üzembe helyezése** lehetőségre, és válassza a **webszolgáltatás [új]telepítése**lehetőséget.  Megnyílik a Machine Learning Studio (klasszikus) webszolgáltatás-portál üzembe helyezés lapja.

> [!NOTE] 
> Új webszolgáltatás telepítéséhez megfelelő engedélyekkel kell rendelkeznie ahhoz az előfizetéshez, amelyhez a webszolgáltatást telepíti. További információ: [webszolgáltatások kezelése a Azure Machine learning webszolgáltatások portálján](manage-new-webservice.md). 

### <a name="web-service-portal-deploy-experiment-page"></a>Webszolgáltatási portál üzembe helyezési kísérlet lapja

A kísérlet telepítése lapon adja meg a webszolgáltatás nevét.
Válasszon árképzési tervet. Ha meglévő díjszabási csomaggal rendelkezik, akkor kiválaszthatja azt, ellenkező esetben létre kell hoznia egy új díjcsomagot a szolgáltatáshoz.

1. Az **árlista** legördülő menüben válasszon ki egy meglévő csomagot, vagy válassza az **új csomag kiválasztása** lehetőséget.
2. A **csomag neve**mezőben adjon meg egy nevet, amely azonosítja a csomagot a számlán.
3. Válassza ki a **havi díjcsomag**egyikét. A csomag alapértelmezett értéke az alapértelmezett régió csomagjai, a webszolgáltatás pedig az adott régióban van üzembe helyezve.

Kattintson a **telepítés** elemre, és **megnyílik a webszolgáltatás** rövid útmutató lapja.

A webszolgáltatás gyors üzembe helyezése oldalon a webszolgáltatás létrehozása után elvégezhető leggyakoribb feladatokhoz férhet hozzá. Innen könnyedén elérheti a tesztoldal és a használat oldalát is.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Az új webszolgáltatás tesztelése

Az új webszolgáltatás teszteléséhez kattintson a gyakori feladatok területen a **webszolgáltatás tesztelése** elemre. A teszt lapon tesztelheti a webszolgáltatást a kérelem-válasz szolgáltatás (RR) vagy egy batch-végrehajtási szolgáltatás (BES) használatával.

Az ERŐFORRÁSREKORDOK tesztelése oldalon megjelennek a kísérlethez megadott bemenetek, kimenetek és a globális paraméterek. A webszolgáltatás teszteléséhez manuálisan megadhatja a bemenetek megfelelő értékeit, vagy megadhatja a tesztelési értékeket tartalmazó vesszővel tagolt (CSV) formátumú fájlt.

Ha az ERŐFORRÁSREKORDOK használatával szeretne tesztelni, a listanézet módból írja be a megfelelő értékeket a bemenetekhez, és kattintson a **kérelem-válasz tesztelése**elemre. Az előrejelzés eredményei a kimenet oszlopban a bal oldalon jelennek meg.

![Adja meg a megfelelő értékeket a webszolgáltatás teszteléséhez](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

A BES teszteléséhez kattintson a **Batch**elemre. A Batch test lapon kattintson a Tallózás elemre a bevitel alatt, és válasszon ki egy CSV-fájlt, amely tartalmazza a megfelelő értékeket. Ha nem rendelkezik CSV-fájllal, és a prediktív kísérletet a Machine Learning Studio klasszikus verziójával hozta létre, akkor letöltheti a prediktív kísérlet adatkészletét, és használhatja azt.

Az adathalmaz letöltéséhez nyissa meg a Machine Learning Studio klasszikus verzióját. Nyissa meg a prediktív kísérletet, és kattintson a jobb gombbal a kísérlethez tartozó bemenetre. A helyi menüben válassza az **adatkészlet** elemet, majd válassza a **Letöltés**lehetőséget.

![Az adatkészlet letöltése a Studio (klasszikus) vászonról](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Kattintson a **teszt**gombra. A Batch-végrehajtási feladat állapota a **teszt batch-feladatok**területen a jobb oldalon jelenik meg.

![A Batch-végrehajtási feladatok tesztelése a webszolgáltatás-portálon](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

A **konfiguráció** lapon módosíthatja a leírást, a címet, a Storage-fiók kulcsát, és engedélyezheti a webszolgáltatáshoz tartozó mintaadatok beküldését.

![Webszolgáltatás konfigurálása](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Hozzáférés az új webszolgáltatáshoz

Miután központilag telepítette a webszolgáltatását a Machine Learning Studio klasszikus verziójáról, az adatok elküldését a szolgáltatásba küldheti, és programozott módon fogadhatja a válaszokat.

A felhasználat oldal a webszolgáltatáshoz való hozzáféréshez szükséges összes információt tartalmazza. Az API-kulcs például a szolgáltatáshoz való jogosult hozzáférés engedélyezésére szolgál.

A Machine Learning Studio (klasszikus) webszolgáltatás elérésével kapcsolatos további információkért lásd: [Azure Machine learning Studio (klasszikus) webszolgáltatás felhasználása](consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Az új webszolgáltatás kezelése

Kezelheti az új webszolgáltatások Machine Learning Studio (klasszikus) webszolgáltatások portálját. A [fő portál lapon](https://services.azureml-test.net/)kattintson a **webszolgáltatások**elemre. A webszolgáltatások lapon törölheti vagy másolhatja a szolgáltatást. Egy adott szolgáltatás figyeléséhez kattintson a szolgáltatásra, majd az **irányítópult**elemre. A webszolgáltatáshoz társított batch-feladatok figyeléséhez kattintson a **Batch-kérelmek naplója**elemre.

### <a id="multi-region"></a>Az új webszolgáltatás üzembe helyezése több régióban

Egyszerűen üzembe helyezhet egy új webszolgáltatást több régióban, anélkül, hogy több előfizetést vagy munkaterületet kellene használnia.

A díjszabás régió-specifikus, ezért meg kell határoznia egy számlázási tervet minden olyan régióhoz, amelyben telepíteni fogja a webszolgáltatást.

#### <a name="create-a-plan-in-another-region"></a>Csomag létrehozása egy másik régióban

1. Jelentkezzen be [Microsoft Azure Machine learning webszolgáltatásba](https://services.azureml.net/).
2. Kattintson a **csomagok** menüpontra.
3. A megjelenő csomagok nézet lapon kattintson az **új**elemre.
4. Az **előfizetés** legördülő listából válassza ki azt az előfizetést, amelyben az új terv fog szerepelni.
5. A **régió** legördülő listából válassza ki az új csomag régióját. A kiválasztott régióhoz tartozó csomag beállításai a lap **csomag beállításai** szakaszában jelennek meg.
6. Az **erőforráscsoport** legördülő listából válassza ki a csomaghoz tartozó erőforráscsoportot. További információ az erőforráscsoportok használatáról: [Azure Resource Manager Overview (áttekintés](../../azure-resource-manager/resource-group-overview.md)).
7. A **csomag neve** mezőbe írja be a csomag nevét.
8. A **terv beállításai**területen kattintson az új csomag számlázási szintjére.
9. Kattintson a **Létrehozás** elemre.

#### <a name="deploy-the-web-service-to-another-region"></a>Webszolgáltatás üzembe helyezése egy másik régióban

1. A Microsoft Azure Machine Learning webszolgáltatások lapon kattintson a **webszolgáltatások** menüpontra.
2. Válassza ki az új régióba telepítendő webszolgáltatást.
3. Kattintson a **Másolás**gombra.
4. A **webszolgáltatás neve**mezőbe írja be a webszolgáltatás új nevét.
5. A **webszolgáltatás leírása**mezőbe írja be a webszolgáltatás leírását.
6. Az **előfizetés** legördülő listából válassza ki azt az előfizetést, amelyben az új webszolgáltatást tárolni fogja.
7. Az **erőforráscsoport** legördülő menüben válasszon ki egy erőforráscsoportot a webszolgáltatáshoz. További információ az erőforráscsoportok használatáról: [Azure Resource Manager Overview (áttekintés](../../azure-resource-manager/resource-group-overview.md)).
8. A **régió** legördülő listából válassza ki azt a régiót, amelyben a webszolgáltatást telepíteni kívánja.
9. A **Storage-fiók** legördülő listából válassza ki azt a Storage-fiókot, amelyben a webszolgáltatást tárolni szeretné.
10. Az **árlista** legördülő menüben válasszon ki egy csomagot a 8. lépésben kiválasztott régióban.
11. Kattintson a **Másolás**gombra.

## <a name="deploy-it-as-a-classic-web-service"></a>Üzembe helyezés klasszikus webszolgáltatásként

Most, hogy a prediktív kísérlet megfelelően elkészült, telepítheti klasszikus Azure-alapú webszolgáltatásként. A webszolgáltatás használatával a felhasználók elküldhetik az adatait a modellbe, és a modell visszaadja az előrejelzéseit.

A prediktív kísérlet üzembe helyezéséhez kattintson a **Futtatás** gombra a kísérlet vászon alján, majd kattintson a **webszolgáltatás telepítése**parancsra. A webszolgáltatás be van állítva, és a webszolgáltatás irányítópultra kerül.

![Webszolgáltatások üzembe helyezése a studióból (klasszikus)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>A klasszikus webszolgáltatás tesztelése

A webszolgáltatást a Machine Learning Studio (klasszikus) webszolgáltatások portálon vagy Machine Learning Studio (klasszikus) is tesztelheti.

A kérelem válasz webszolgáltatásának teszteléséhez kattintson a **teszt** gombra a webszolgáltatás irányítópultján. Megjelenik egy párbeszédpanel, amely rákérdez a szolgáltatás bemeneti adatára. Ezek a pontozási kísérlet által várt oszlopok. Adjon meg egy adatkészletet, majd kattintson **az OK**gombra. A webszolgáltatás által generált eredmények az irányítópult alján jelennek meg.

A **tesztelési** előnézet hivatkozásra kattintva tesztelheti a szolgáltatást a Azure Machine learning Studio webszolgáltatások portál klasszikus verziójában, ahogyan az az új webszolgáltatás szakaszban is látható.

A Batch-végrehajtási szolgáltatás teszteléséhez kattintson a **teszt** előnézet hivatkozásra. A Batch test lapon kattintson a Tallózás elemre a bevitel alatt, és válasszon ki egy CSV-fájlt, amely tartalmazza a megfelelő értékeket. Ha nem rendelkezik CSV-fájllal, és a prediktív kísérletet a Machine Learning Studio klasszikus verziójával hozta létre, akkor letöltheti a prediktív kísérlet adatkészletét, és használhatja azt.

![Webszolgáltatás tesztelése](./media/publish-a-machine-learning-web-service/figure-3.png)

A **konfiguráció** lapon módosíthatja a szolgáltatás megjelenítendő nevét, és megadhatja a leírását. A név és a leírás a webszolgáltatások kezelése [Azure Portalban](https://portal.azure.com/) jelenik meg.

Megadhatja a bemeneti adatok, a kimeneti adatok és a webszolgáltatás paramétereinek leírását, ha megad egy karakterláncot minden oszlophoz a **bemeneti séma**, a **kimeneti séma**és a **webszolgáltatás paraméter**alatt. Ezeket a leírásokat a webszolgáltatáshoz megadott mintakód-dokumentációban lehet használni.

Engedélyezheti a naplózást a webszolgáltatás elérésekor észlelt hibák diagnosztizálásához. További információt a [Machine learning Studio (klasszikus) webszolgáltatások naplózásának engedélyezése](web-services-logging.md)című témakörben talál.

![A naplózás engedélyezése a webszolgáltatások portálján](./media/publish-a-machine-learning-web-service/figure-4.png)

A webszolgáltatáshoz tartozó végpontokat a Azure Machine Learning webszolgáltatások portálon is konfigurálhatja, hasonlóan az új webszolgáltatás szakaszban bemutatott eljáráshoz. A lehetőségek különbözőek, hozzáadhat vagy módosíthat a szolgáltatás leírását, engedélyezheti a naplózást, és engedélyezheti a mintaadatok tesztelését.

### <a name="access-your-classic-web-service"></a>Hozzáférés a klasszikus webszolgáltatáshoz

Miután központilag telepítette a webszolgáltatását a Machine Learning Studio klasszikus verziójáról, az adatok elküldését a szolgáltatásba küldheti, és programozott módon fogadhatja a válaszokat.

Az irányítópulton a webszolgáltatáshoz való hozzáféréshez szükséges összes információ elérhető. Az API-kulcs például lehetővé teszi, hogy engedélyezze a hozzáférést a szolgáltatáshoz, és az API-Súgó lapjai segítséget nyújtanak a kód megírásában.

A Machine Learning Studio (klasszikus) webszolgáltatás elérésével kapcsolatos további információkért lásd: [Azure Machine learning Studio (klasszikus) webszolgáltatás felhasználása](consume-web-services.md).

### <a name="manage-your-classic-web-service"></a>A klasszikus webszolgáltatás kezelése

A webszolgáltatás figyeléséhez számos művelet hajtható végre. Frissítheti, és törölheti is. Emellett további végpontokat is hozzáadhat egy klasszikus webszolgáltatáshoz, a központi telepítéskor létrehozott alapértelmezett végpont mellett.

További információ: [Azure Machine learning Studio (klasszikus) munkaterület kezelése](manage-workspace.md) és [webszolgáltatás kezelése a Azure Machine learning Studio (klasszikus) webszolgáltatások portálján](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Webszolgáltatás frissítése
Módosíthatja a webszolgáltatást, például frissítheti a modellt további betanítási adataival, és újra telepítheti az eredeti webszolgáltatás felülírásával.

A webszolgáltatás frissítéséhez nyissa meg a webszolgáltatás üzembe helyezéséhez használt eredeti prediktív kísérletet, és végezze el a szerkeszthető másolást a **Mentés másként lehetőségre**kattintva. Végezze el a módosításokat, majd kattintson a **webszolgáltatás üzembe helyezése**lehetőségre.

Mivel korábban már üzembe helyezte ezt a kísérletet, a rendszer megkérdezi, hogy szeretné-e felülírni (klasszikus webszolgáltatás) vagy a meglévő szolgáltatás frissítését (új webszolgáltatás). Az **Igen** vagy a **frissítés** gombra kattintva leállítja a meglévő webszolgáltatást, és üzembe helyezi az új prediktív kísérletet.

> [!NOTE]
> Ha módosította az eredeti webszolgáltatás konfigurációjának módosításait, például egy új megjelenítendő név vagy leírás beírását, akkor újra meg kell adnia ezeket az értékeket.

A webszolgáltatás frissítésének egyik lehetősége a modell programozott módon történő újratanítása. További információ: [Machine learning Studio (klasszikus) modellek programozott módon történő átképzése](/azure/machine-learning/studio/retrain-machine-learning-model).

## <a name="next-steps"></a>További lépések

* Az üzembe helyezés módjával kapcsolatos további technikai részletekért lásd: [how a Machine learning Studio (klasszikus) modell egy kísérletből egy működőképes webszolgáltatásba halad](model-progression-experiment-to-web-service.md).

* A modell telepítésre való előkészítésének részletes ismertetését lásd: [a modell előkészítése az üzembe helyezéshez Azure Machine learning Studio (klasszikus)](convert-training-experiment-to-scoring-experiment.md).

* A REST API felhasználására, illetve a webszolgáltatás elérésére számos megoldás áll rendelkezésre. Lásd: [Azure Machine learning Studio (klasszikus) webszolgáltatás felhasználása](consume-web-services.md).

<!-- internal links -->
[Képzési kísérlet létrehozása]: #create-a-training-experiment
[Átalakítás prediktív kísérletre]: #convert-the-training-experiment-to-a-predictive-experiment
[Új webszolgáltatás]: #deploy-it-as-a-new-web-service
[Klasszikus webszolgáltatás]: #deploy-it-as-a-classic-web-service
[új]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
