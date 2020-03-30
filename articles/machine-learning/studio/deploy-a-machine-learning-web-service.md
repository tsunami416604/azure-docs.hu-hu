---
title: Webszolgáltatás üzembe helyezése
titleSuffix: ML Studio (classic) - Azure
description: Hogyan konvertálhat egy betanítási kísérletet prediktív kísérletté, előkészítheti az üzembe helyezésre, majd üzembe helyezheti egy Azure Machine Learning Studio (klasszikus) webszolgáltatásként.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 6c81e50560de69f7702e852d4602680fde7f01f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218131"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Azure Machine Learning Studio (klasszikus) webszolgáltatás üzembe helyezése

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Az Azure Machine Learning Studio (klasszikus) lehetővé teszi egy prediktív analitikus megoldás létrehozásához és teszteléséhez. Ezután telepítheti a megoldást webszolgáltatásként.

A Machine Learning Studio (klasszikus) webes szolgáltatásai egy alkalmazás és egy Machine Learning Studio (klasszikus) munkafolyamat-pontozási modell közötti felületet biztosítanak. Egy külső alkalmazás valós időben kommunikálhat a Machine Learning Studio (klasszikus) munkafolyamat-pontozási modellel. A Machine Learning Studio (klasszikus) webszolgáltatás hívása előrejelzési eredményeket ad vissza egy külső alkalmazásnak. A webszolgáltatások felé irányuló hívások indításához át kell adnia azt az API-kulcsot, amelyet a webszolgáltatás üzembe helyezésekor hozott létre. A Machine Learning Studio (klasszikus) webszolgáltatás a REST-en alapul, amely a webes programozási projektek népszerű architektúrája.

Az Azure Machine Learning Studio (klasszikus) kétféle webszolgáltatással rendelkezik:

* Kérelem-válasz szolgáltatás (RRS): Alacsony késésű, jól méretezhető szolgáltatás, amely egyetlen adatrekordot szerez.
* Batch-végrehajtási szolgáltatás (BES): Adatrekordok kötegét feltöltő aszinkron szolgáltatás.

A BES bemeneti adatai az RRS-ben használt bemeneti adatokhoz hasonlóak. A fő különbség az, hogy a BES több különböző forrásból olvassa be az adatköteget, például az Azure Blob Storage-ból és Azure Table Storage-ből, az Azure SQL Database-ből, a HDInsightból (Hive-lekérdezés) és HTTP-forrásokból.

Magas szintű nézőpontból három lépésben telepítheti a modellt:

* **[Képzési kísérlet létrehozása]** – A Studio (klasszikus), betaníthatja és tesztelheti a prediktív elemzési modell betanítási adatok, amelyek a megadott, beépített gépi tanulási algoritmusok széles készletének használatával.
* **[Alakítsa át egy prediktív kísérlet]** – Miután a modell betanítása a meglévő adatokkal, és készen áll arra, hogy használja az új adatok pontozására, előkészíti és racionalizálja a kísérletet az előrejelzésekhez.
* **Üzembe helyezése** **[új webszolgáltatásként]** vagy **[klasszikus webszolgáltatásként]** – Ha a prediktív kísérletet Azure-webszolgáltatásként telepíti, a felhasználók adatokat küldhetnek a modellnek, és fogadhatják a modell előrejelzéseit.

## <a name="create-a-training-experiment"></a>Képzési kísérlet létrehozása

Prediktív elemzési modell betanításához az Azure Machine Learning Studio (klasszikus) használatával hozzon létre egy betanítási kísérletet, ahol különböző modulokat tartalmaz a betanítási adatok betöltéséhez, az adatok szükség szerinti előkészítéséhez, a gépi tanulási algoritmusok alkalmazásához és a Eredmények. Egy kísérlet ismétlése, és próbálja meg a különböző gépi tanulási algoritmusok összehasonlítani és értékelni az eredményeket.

A képzési kísérletek létrehozásának és kezelésének folyamatát máshol alaposabban lefedik. További információval a következő cikkek szolgálnak:

* [Egyszerű kísérlet létrehozása az Azure Machine Learning Studio-ban (klasszikus)](create-experiment.md)
* [Prediktív megoldás fejlesztése az Azure Machine Learning Studio segítségével (klasszikus)](tutorial-part1-credit-risk.md)
* [A betanítási adatok importálása az Azure Machine Learning Studióba (klasszikus)](import-data.md)
* [Kísérletismétlések kezelése az Azure Machine Learning Studio-ban (klasszikus)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>A betanítási kísérlet átalakítása prediktív kísérletté

Miután betanította a modellt, készen áll arra, hogy a betanítási kísérletet prediktív kísérletté alakítsa át az új adatok pontozásához.

A prediktív kísérletre való konvertálásával a betanított modell készen áll a pontozási webszolgáltatásként való üzembe helyezésre. A webszolgáltatás felhasználói bemeneti adatokat küldhetnek a modellnek, és a modell visszaküldi az előrejelzési eredményeket. A prediktív kísérletté való áttéréskor tartsa szem előtt, hogy a modellt hogyan kell mások használnia.

Ha a betanítási kísérletet prediktív kísérletté szeretné alakítani, kattintson a **Futtatás** gombra a kísérleti vászon alján, kattintson a **Webszolgáltatás beállítása**parancsra, majd a **Prediktív webszolgáltatás parancsra.**

![Konvertálás pontozási kísérletté](./media/publish-a-machine-learning-web-service/figure-1.png)

A konvertálás végrehajtásáról a [Modell előkészítése az Azure Machine Learning Studio (klasszikus) üzembe helyezésére](convert-training-experiment-to-scoring-experiment.md)című témakörben talál további információt.

A következő lépések egy prediktív kísérlet új webszolgáltatásként való üzembe helyezését ismertetik. A kísérlet klasszikus webszolgáltatásként is üzembe helyezhető.

## <a name="deploy-it-as-a-new-web-service"></a>Új webszolgáltatásként való üzembe helyezése

Most, hogy a prediktív kísérlet elkészült, üzembe helyezheti, mint egy új (Resource Manager-alapú) Azure-webszolgáltatás. A webszolgáltatás használatával a felhasználók adatokat küldhetnek a modellnek, és a modell visszaadja az előrejelzéseket.

A prediktív kísérlet üzembe helyezéséhez kattintson a **Futtatás** gombra a kísérleti vászon alján. Miután a kísérlet futása befejeződött, kattintson **a Webszolgáltatás telepítése gombra,** és válassza **a Webszolgáltatás [új]telepítése**lehetőséget.  Megnyílik a Machine Learning Studio (klasszikus) webszolgáltatási portál központi telepítési lapja.

> [!NOTE] 
> Új webszolgáltatás üzembe helyezéséhez megfelelő engedélyekkel kell rendelkeznie abban az előfizetésben, amelyre a webszolgáltatást telepítette. További információt a [Webszolgáltatás kezelése az Azure Machine Learning WebServices portálon című témakörben talál.](manage-new-webservice.md) 

### <a name="web-service-portal-deploy-experiment-page"></a>A webszolgáltatás-portál kísérleti lap telepítése

A Kísérlet telepítése lapon adja meg a webszolgáltatás nevét.
Válasszon ki egy árképzési tervet. Ha meglévő díjszabási csomaggal rendelkezik, kiválaszthatja azt, ellenkező esetben új ártervet kell létrehoznia a szolgáltatáshoz.

1. Az **Árterv** legördülő menüben válasszon egy meglévő tervet, vagy válassza az **Új terv kiválasztása** lehetőséget.
2. A **Terv neve**mezőbe írja be a számlán a terv azonosítására szolgáló nevet.
3. Válasszon egyet a **Havi csomag csomagok közül.** A csomagrétegek alapértelmezés szerint az alapértelmezett régió ra vonatkozó tervek, és a webszolgáltatás telepítve van az adott régióban.

Kattintson **a Telepítés gombra,** és megnyílik a webszolgáltatás **gyorsútmutató** lapja.

A webszolgáltatás rövid útmutató lapja hozzáférést és útmutatást nyújt a webszolgáltatás létrehozása után végrehajtendő leggyakoribb feladatokhoz. Itt könnyedén elérheti a Teszt oldalt és a Felhasználás oldalt is.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Az új webszolgáltatás tesztelése

Az új webszolgáltatás teszteléséhez kattintson a **Webszolgáltatás tesztelése** a gyakori feladatok alatt elemre. A Teszt lapon tesztelheti a webszolgáltatást kérelem-válasz szolgáltatásként (RRS) vagy batch execution szolgáltatásként (BES).

Az RRS tesztlap megjeleníti a bemeneteket, kimeneteket és a kísérlethez megadott globális paramétereket. A webszolgáltatás teszteléséhez manuálisan is megadhatja a bemenetek megfelelő értékeit, vagy a tesztértékeket tartalmazó, vesszővel elválasztott (CSV) formátumú fájlt adhat meg.

Az RRS használatával történő teszteléshez a listanézet-módban adja meg a bemenetek megfelelő értékeit, és kattintson a **Tesztkérelem-válasz gombra.** Az előrejelzési eredmények a bal oldali kimeneti oszlopban jelennek meg.

![Adja meg a megfelelő értékeket a webszolgáltatás teszteléséhez](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

A BES teszteléséhez kattintson a **Köteg**gombra. A Kötegteszt lapon kattintson a Tallózás gombra a bevitel alatt, és jelöljön ki egy megfelelő mintaértékeket tartalmazó CSV-fájlt. Ha nem rendelkezik CSV-fájllal, és a prediktív kísérletet a Machine Learning Studio (klasszikus) használatával hozta létre, letöltheti a prediktív kísérlet adatkészletét, és használhatja azt.

Az adatkészlet letöltéséhez nyissa meg a Machine Learning Studio (klasszikus) alkalmazást. Nyissa meg a prediktív kísérletet, és kattintson a jobb gombbal a kísérlet bemenetére. A helyi menüben válassza az **adatkészletet,** majd a **Letöltés parancsot.**

![Az adatkészlet letöltése a Studio (klasszikus) vászonról](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Kattintson a **Teszt gombra.** A Kötegelt végrehajtási feladat állapota a **Tesztkötegelési feladatok**csoportban jobbra jelenik meg.

![A kötegelt végrehajtási feladat tesztelése a webszolgáltatás-portállal](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

A **KONFIGURÁCIÓ** lapon módosíthatja a leírást, a címet, frissítheti a tárfiók kulcsát, és engedélyezheti a mintaadatokat a webszolgáltatáshoz.

![A webszolgáltatás konfigurálása](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Az új webszolgáltatás elérése

Miután üzembe helyezte a webszolgáltatást a Machine Learning Studio (klasszikus) rendszerből, adatokat küldhet a szolgáltatásnak, és programozott módon fogadhat válaszokat.

A **Felhasználás** lap minden olyan információt tartalmaz, amely a webszolgáltatás eléréséhez szükséges. Például az API-kulcs biztosított, hogy a szolgáltatás hoz való engedélyezett hozzáférést.

A Machine Learning Studio (klasszikus) webszolgáltatások eléréséről az [Azure Machine Learning Studio (klasszikus) webszolgáltatás felhasználása](consume-web-services.md)című témakörben talál további információt.

### <a name="manage-your-new-web-service"></a>Az új webszolgáltatás kezelése

Az új webszolgáltatásokat a Machine Learning Studio (klasszikus) webszolgáltatások portálhasználatával kezelheti. A [főportál lapon](https://services.azureml.net/)kattintson a **WebServices**gombra. A webszolgáltatások lapról törölhet vagy másolhat egy szolgáltatást. Egy adott szolgáltatás figyeléséhez kattintson a szolgáltatásra, majd az **Irányítópult parancsra.** A webszolgáltatáshoz társított kötegelt feladatok figyeléséhez kattintson a **Kötegelt kérelemnapló gombra.**

### <a name="deploy-your-new-web-service-to-multiple-regions"></a><a id="multi-region"></a>Az új webszolgáltatás telepítése több régióban

Az új webszolgáltatást egyszerűen üzembe helyezheti több régióban anélkül, hogy több előfizetésre vagy munkaterületre lenne szüksége.

A díjszabás régióspecifikus, ezért minden olyan régióhoz meg kell határoznia egy számlázási tervet, amelyben a webszolgáltatást telepíti.

#### <a name="create-a-plan-in-another-region"></a>Terv létrehozása másik régióban

1. Jelentkezzen be a [Microsoft Azure Machine Learning webservices szolgáltatásba.](https://services.azureml.net/)
2. Kattintson a **Séma** menügombra.
3. A Tervek nézet felett lapon kattintson az **Új gombra.**
4. Az **Előfizetés** legördülő menüben válassza ki azt az előfizetést, amelyben az új csomag található.
5. A **Régió** legördülő menüből válassza ki az új csomag régióját. A kijelölt terület Terv beállításai az oldal **Terv beállítások** szakaszában jelennek meg.
6. Az **Erőforráscsoport** legördülő menüből válasszon ki egy erőforráscsoportot a tervhez. Az erőforráscsoportokról az [Azure Resource Manager áttekintése című témakörben olvashat bővebben.](../../azure-resource-manager/management/overview.md)
7. A **Terv neve** mezőbe írja be a terv nevét.
8. A **Terv beállításai csoportban**kattintson az új csomag számlázási szintjére.
9. Kattintson **a Létrehozás gombra.**

#### <a name="deploy-the-web-service-to-another-region"></a>A webszolgáltatás telepítése egy másik régióba

1. A Microsoft Azure Machine Learning webszolgáltatások lapon kattintson a **Webszolgáltatások** menügombra.
2. Válassza ki azt a webszolgáltatást, amelyet egy új régióban telepít.
3. Kattintson **a Másolás gombra.**
4. A **Webszolgáltatás neve**mezőbe írja be a webszolgáltatás új nevét.
5. A **webszolgáltatás leírásában**írja be a webszolgáltatás leírását.
6. Az **Előfizetés** legördülő menüben válassza ki azt az előfizetést, amelyben az új webszolgáltatás található.
7. Az **Erőforráscsoport** legördülő menüből válasszon ki egy erőforráscsoportot a webszolgáltatáshoz. Az erőforráscsoportokról az [Azure Resource Manager áttekintése című témakörben olvashat bővebben.](../../azure-resource-manager/management/overview.md)
8. A **régió** legördülő menüben válassza ki azt a régiót, amelyben a webszolgáltatást telepíteni szeretné.
9. A **Tárfiók** legördülő menüből válassza ki azt a tárfiókot, amelyben a webszolgáltatást tárolni szeretné.
10. Az **Árterv** legördülő menüből válasszon ki egy tervet a 8.
11. Kattintson **a Másolás gombra.**

## <a name="deploy-it-as-a-classic-web-service"></a>Üzembe helyezése klasszikus webszolgáltatásként

Most, hogy a prediktív kísérlet megfelelően előkészített, klasszikus Azure-webszolgáltatásként üzembe helyezheti. A webszolgáltatás használatával a felhasználók adatokat küldhetnek a modellnek, és a modell visszaadja az előrejelzéseket.

A prediktív kísérlet üzembe helyezéséhez kattintson a **Futtatás** gombra a kísérletvászon alján, majd kattintson a **Webszolgáltatás telepítése**parancsra. A webszolgáltatás be van állítva, és ön a webszolgáltatás irányítópultjára kerül.

![Webszolgáltatás üzembe helyezése a Studio-ból (klasszikus)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>A klasszikus webszolgáltatás tesztelése

A webszolgáltatást a Machine Learning Studio (klasszikus) WebServices portálon vagy a Machine Learning Studio (klasszikus) szolgáltatásban tesztelheti.

A Kérelemválasz webszolgáltatás teszteléséhez kattintson a **teszt** gombra a webszolgáltatás irányítópultján. Megjelenik egy párbeszédablak, amely a szolgáltatás bemeneti adatait kéri. Ezek azok az oszlopok, amelyeket a pontozási kísérlet elvár. Adja meg az adathalmazt, majd kattintson az **OK gombra.** A webszolgáltatás által generált eredmények az irányítópult alján jelennek meg.

A **Teszt** előzetes hivatkozásra kattintva tesztelheti a szolgáltatást az Azure Machine Learning Studio (klasszikus) webszolgáltatások portálján, ahogy az korábban az Új webszolgáltatás szakaszban látható.

A Kötegelt végrehajtási szolgáltatás teszteléséhez kattintson a **Teszt** előnézeti hivatkozáshivatkozásra. A Kötegteszt lapon kattintson a Tallózás gombra a bevitel alatt, és jelöljön ki egy megfelelő mintaértékeket tartalmazó CSV-fájlt. Ha nem rendelkezik CSV-fájllal, és a prediktív kísérletet a Machine Learning Studio (klasszikus) használatával hozta létre, letöltheti a prediktív kísérlet adatkészletét, és használhatja azt.

![A webszolgáltatás tesztelése](./media/publish-a-machine-learning-web-service/figure-3.png)

A **KONFIGURÁCIÓ** lapon módosíthatja a szolgáltatás megjelenítendő nevét, és leírást adhat neki. A név és a leírás az [Azure Portalon](https://portal.azure.com/) jelenik meg, ahol a webszolgáltatásokat kezeli.

A bemeneti adatok, a kimeneti adatok és a webszolgáltatás paramétereinek leírását úgy adhathatja meg, hogy az **INPUT SCHEMA**, output **schema**és **webservice parameter**( minden oszlophoz ) karakterláncot ad meg. Ezek a leírások a webszolgáltatáshoz biztosított mintakód-dokumentációban használatosak.

A naplózás engedélyezésével diagnosztizálhatja azokat a hibákat, amelyeket a webszolgáltatás elérésekor lát. További információ: [Naplózás engedélyezése a Klasszikus webszolgáltatásokhoz.](web-services-logging.md)

![Naplózás engedélyezése a webszolgáltatási portálon](./media/publish-a-machine-learning-web-service/figure-4.png)

Az Azure Machine Learning Web Services portálon konfigurálhatja a webszolgáltatás végpontjait is, hasonlóan az Új webszolgáltatás szakaszban korábban bemutatott eljáráshoz. A beállítások eltérőek, hozzáadhatja vagy módosíthatja a szolgáltatás leírását, engedélyezheti a naplózást, és engedélyezheti a mintaadatokat a teszteléshez.

### <a name="access-your-classic-web-service"></a>A klasszikus webszolgáltatás elérése

Miután üzembe helyezte a webszolgáltatást az Azure Machine Learning Studio (klasszikus) szolgáltatásból, adatokat küldhet a szolgáltatásnak, és programozott módon fogadhat válaszokat.

Az irányítópult minden olyan információt tartalmaz, amely a webszolgáltatás eléréséhez szükséges. Például az API-kulcs biztosított, hogy a szolgáltatás hoz való engedélyezett hozzáférést, és API súgólapok állnak rendelkezésre, hogy segítsen a kód írásának megkezdéséhez.

A Machine Learning Studio (klasszikus) webszolgáltatások eléréséről az [Azure Machine Learning Studio (klasszikus) webszolgáltatás felhasználása](consume-web-services.md)című témakörben talál további információt.

### <a name="manage-your-classic-web-service"></a>A klasszikus webszolgáltatás kezelése

A webszolgáltatás figyeléséhez számos műveletet hajthat végre. Frissítheti és törölheti. További végpontokat is hozzáadhat egy klasszikus webszolgáltatáshoz az alapértelmezett végpont mellett, amely a központi telepítéskor jön létre.

További információ: [Manage an Azure Machine Learning Studio (classic) workspace](manage-workspace.md) and [Manage a web service using the Azure Machine Learning Studio (classic) Web Services portal.](manage-new-webservice.md)

## <a name="update-the-web-service"></a>A webszolgáltatás frissítése
Módosíthatja a webszolgáltatást, például a modell frissítését további betanítási adatokkal, majd újra üzembe helyezheti, felülírva az eredeti webszolgáltatást.

A webszolgáltatás frissítéséhez nyissa meg a webszolgáltatás üzembe helyezéséhez használt eredeti prediktív kísérletet, és készítsen szerkeszthető másolatot a **MENTÉS MÁSKÉNT**gombra kattintva. Hajtsa végre a módosításokat, majd kattintson **a Webszolgáltatás telepítése gombra.**

Mivel ezt a kísérletet már korábban telepítette, a rendszer megkérdezi, hogy felül kívánja-e írni (Klasszikus webszolgáltatás) vagy frissíteni (Új webszolgáltatás) a meglévő szolgáltatást. Az **IGEN** vagy a **Frissítés** gombra kattintva leállítja a meglévő webszolgáltatást, és az új prediktív kísérlet telepítve van a helyén.

> [!NOTE]
> Ha konfigurációs módosításokat hajtott végre az eredeti webszolgáltatásban, például új megjelenítendő nevet vagy leírást adott meg, újra meg kell adnia ezeket az értékeket.

A webszolgáltatás frissítésének egyik lehetősége a modell programozott újratanítása. További információ: [A Machine Learning Studio (klasszikus) modellek programozott programozással.](/azure/machine-learning/studio/retrain-machine-learning-model)

## <a name="next-steps"></a>További lépések

* A központi telepítés működésével kapcsolatos további részleteka [Machine Learning Studio (klasszikus) modell előrehaladásának módjában a kísérletből egy működőképes webszolgáltatássá.](model-progression-experiment-to-web-service.md)

* A modell üzembe helyezésre való előkészítéséről a [Modell előkészítése az Azure Machine Learning Studio (klasszikus) üzembe helyezésre](convert-training-experiment-to-scoring-experiment.md)című témakörben talál további részleteket.

* A REST API felhasználására, illetve a webszolgáltatás elérésére számos megoldás áll rendelkezésre. Lásd: [Egy Azure Machine Learning Studio (klasszikus) webszolgáltatás felhasználása.](consume-web-services.md)

<!-- internal links -->
[Képzési kísérlet létrehozása]: #create-a-training-experiment
[Prediktív kísérletté alakítása]: #convert-the-training-experiment-to-a-predictive-experiment
[Új webes szolgáltatás]: #deploy-it-as-a-new-web-service
[Klasszikus webszolgáltatás]: #deploy-it-as-a-classic-web-service
[Új]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
