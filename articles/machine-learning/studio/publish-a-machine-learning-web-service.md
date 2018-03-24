---
title: A Machine Learning webszolgáltatás telepítése |} Microsoft Docs
description: Hogyan egy tanítási kísérletet átalakítása egy prediktív kísérletté, a telepítés előkészítéséhez, majd az Azure Machine Learning webszolgáltatás üzembe.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 73a3e9c6-00d0-41d4-8cf1-2ec87713867e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.openlocfilehash: 3ce4104040e90a4740442d7692b3bf4a0789bde0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="deploy-an-azure-machine-learning-web-service"></a>Azure Machine Learning webszolgáltatás üzembe helyezése
Az Azure Machine Learning segítségével létrehozása, tesztelése és telepítése a prediktív elemzési megoldásokat.

A pont-az-áttekintése látható ez történik, a három lépést:

* **[Hozzon létre egy tanítási kísérletet]**  -Azure Machine Learning Studio olyan együttműködési Látványelem-fejlesztési környezet, amelyekkel betanítása és tesztelése egy prediktív elemzési modell betanítási adatok, hozzá kell adni.
* **[Alakítsa át egy prediktív kísérletté]**  – Miután a modell még betanítva meglévő adatokkal, és készen áll az új adatok pontozása céljából, előkészítése, és az előrejelzés kísérletbe egyszerűsítésére.
* **[A webszolgáltatás üzembe]**  -, a prediktív kísérletté telepítése egy [új] vagy [klasszikus] Azure webszolgáltatás. Felhasználók adatokat küldeni a modell és a modell előrejelzéseket fogadására.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="create-a-training-experiment"></a>Hozzon létre egy tanítási kísérletet
Egy prediktív elemzési modell betanításához segítségével Azure Machine Learning Studióban hozzon létre egy tanítási kísérletet vegye különböző modulok betanítási adatok betöltése, készítse elő a szükséges adatokat, alkalmazza a gépi tanulási algoritmusok és eredmények értékelése. A kísérlet többször, és próbálja meg a különböző gépi tanulási algoritmusok összehasonlíthatja és eredmények értékelése.

A folyamat létrehozásának és kezelésének képzési kísérletek alaposabban máshol van. További információval a következő cikkek szolgálnak:

* [Egy egyszerű kísérlet létrehozása az Azure Machine Learning Studióban](create-experiment.md)
* [Azure Machine Learning a prediktív megoldás kifejlesztése](walkthrough-develop-predictive-solution.md)
* [A betanítási adatok importálása az Azure Machine Learning Studióban](import-data.md)
* [Az Azure Machine Learning Studióban kísérletismétlések kezelése](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>A tanítási kísérletet átalakítása egy prediktív kísérletté
Ha a modell már betanítása, készen áll a tanítási kísérletet alakítani egy prediktív kísérletté új adatok pontozása céljából.

A prediktív kísérletté átalakításával kap a betanított modell pontozási webszolgáltatásként telepítésre kész. A webszolgáltatás felhasználók bemeneti adatokat küldhet a modell, és a modell vissza az előrejelzés eredmények küldi. Egy prediktív kísérletté konvertálásakor, vegye figyelembe a mások által használandó modell várt hogyan.

A tanítási kísérletet egy prediktív kísérletté alakításához kattintson **futtatása** a kísérletvászonra alján kattintson **webes szolgáltatások beállítása**, majd jelölje be **prediktív webszolgáltatás**.

![Kísérlet pontozási átalakítása](./media/publish-a-machine-learning-web-service/figure-1.png)

Az átalakításhoz végrehajtásával kapcsolatos további információkért lásd: [a modell előkészítése az Azure Machine Learning Studióban telepítési](convert-training-experiment-to-scoring-experiment.md).

A következő lépések bemutatják egy prediktív kísérletté egy új webszolgáltatás üzembe helyezése. A kísérlet klasszikus webszolgáltatásként is telepíthet.

## <a name="deploy-it-as-a-web-service"></a>Üzembe egy webszolgáltatás-bővítmény

A prediktív kísérletté telepíthet egy új webszolgáltatás-bővítmény vagy klasszikus webszolgáltatásként.

### <a name="deploy-the-predictive-experiment-as-a-new-web-service"></a>A prediktív kísérletté egy új webszolgáltatás-bővítmény telepítése
Most, hogy a prediktív kísérletté elő van készítve, telepíthet egy új Azure webszolgáltatás. A webszolgáltatással, felhasználók adatokat küldhet a modell, és a modell az előrejelzés ad vissza.

A prediktív kísérletté telepítéséhez kattintson **futtatása** a kísérletvászonra alján. A kísérlet futása befejeződött, kattintson **webes szolgáltatás telepítése** válassza **webes szolgáltatás telepítése [új]**.  Megnyílik a telepítési oldal, a Machine Learning webszolgáltatás portál.

> [!NOTE] 
> Egy új webszolgáltatás-bővítmény telepítése, megfelelő engedélyekkel kell rendelkeznie, amelyhez az előfizetést, a webszolgáltatás telepítése. További információ: [kezelése az Azure Machine Learning webszolgáltatások portál használatával egy webszolgáltatás-bővítmény](manage-new-webservice.md). 

#### <a name="machine-learning-web-service-portal-deploy-experiment-page"></a>Machine Learning webszolgáltatásba portal telepítés kísérlet lap
A kísérletben telepítése lapon adja meg a webszolgáltatás nevét.
Válasszon egy tarifacsomagot. Ha egy meglévő tarifacsomagot is kiválaszthatja, ellenkező esetben kell létrehoznia egy új ár tervet a szolgáltatás.

1. Az a **ár terv** legördülő listán, jelöljön ki egy meglévő terv vagy a **jelölje be új csomagot** lehetőséget.
2. A **neve**, adjon meg egy nevet, amely azonosítja a számlázási csomagot.
3. Válasszon egyet a a **havi megtervezése rétegek**. Az alapértelmezett régiójától és a webszolgáltatás terv rétegek alapértelmezett régió van telepítve.

Kattintson a **telepítés** és a **gyors üzembe helyezés** a webszolgáltatáshoz lap nyílik meg.

A webes szolgáltatás gyors üzembe helyezés lap lehetővé teszi az access és útmutatást a leggyakoribb feladatokat kell végrehajtania egy webszolgáltatás-bővítmény létrehozása után. Itt egyszerűen hozzáférhet a tesztlap és a felhasználás lap.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

#### <a name="test-your-new-web-service"></a>Az új webes szolgáltatás tesztelése
Az új webszolgáltatás teszteléséhez kattintson **webszolgáltatás tesztelése** a közös műveletek területen. Az tesztelése oldalon tesztelheti a webes szolgáltatás, egy kérés-válasz szolgáltatás (RR-EKET) vagy egy kötegelt végrehajtási szolgáltatás (BES).

Az RRS tesztoldalt a bemenetek, kimenetek és a globális paramétereket a kísérleti fázisú funkciókat megadott jeleníti meg. A webszolgáltatás teszteléséhez megadhatja manuálisan megfelelő értékeket a bemenetek, illetve adjon meg egy vesszővel tagolt adatfájlból (CSV) formátumú fájl tesztelése értékeket tartalmazó.

Teszteléséhez RR-EKET használja, a lista nézet üzemmódról adja meg az megfelelő értékeket a bemenetek, majd kattintson **tesztelése kérés-válasz**. Az előrejelzés eredmények megjelenítik a bal oldali kimeneti oszlop.

![A webszolgáltatás üzembe helyezése](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

A BES teszteléséhez kattintson **kötegelt**. A kötegelt tesztelése oldalon kattintson a Tallózás gombra a bemeneti alatt, és válassza ki a megfelelő mintaértékek tartalmazó CSV-fájl. Ha egy CSV-fájl nem rendelkezik, és a használata a Machine Learning Studio prediktív kísérletté létrehozott, a prediktív kísérletté, az adatkészlet töltse le, és használja azt.

Az adatkészlet letöltéséhez nyissa meg a Machine Learning Studio. Nyissa meg a prediktív kísérletté, és kattintson a jobb gombbal a bemeneti a kísérleti fázisú funkciókat. A helyi menüből válassza ki a **dataset** majd **letöltése**.

![A webszolgáltatás üzembe helyezése](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Kattintson a **teszt**. A kötegelt végrehajtási feladatának állapotát jeleníti meg, a jobb **teszt kötegelt feladatok**.

![A webszolgáltatás üzembe helyezése](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Az a **konfigurációs** lapon módosíthatja a leírást, cím, a tárfiók kulcsának frissítése, és mintaadatokat engedélyezése a webszolgáltatáshoz.

![A webszolgáltatás konfigurálása](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

A webszolgáltatás telepítése után, ha teheti:

* **Hozzáférés** azt a webes szolgáltatás API-n keresztül.
* **Kezelése** az Azure Machine Learning web services portálon keresztül.
* **Frissítés** , ha a modell megváltozik.

#### <a name="access-your-new-web-service"></a>Az új webes szolgáltatás
A webszolgáltatás a Machine Learning Studio telepít, miután adatokat küldeni a szolgáltatás, és válaszokat programozott módon kapni.

A **felhasználás** lap biztosít a webes szolgáltatás eléréséhez szükséges összes adatot. Például az API-kulcsot a szolgáltatás a hitelesített hozzáférést megadott.

A Machine Learning webszolgáltatásba elérésével kapcsolatos további információkért lásd: [hogyan kell használni az Azure Machine Learning Web service](consume-web-services.md).

#### <a name="manage-your-new-web-service"></a>Az új webes szolgáltatás kezelése
A új szolgáltatások Machine Learning webszolgáltatások webportál kezelheti. Az a [portál fő lapján](https://services.azureml-test.net/), kattintson a **webszolgáltatások**. A webes szolgáltatások lapon törölheti vagy másolja át a szolgáltatást. Ha figyelni szeretné egy adott szolgáltatáshoz, kattintson a szolgáltatás, és kattintson a **irányítópult**. A webszolgáltatás társított kötegelt feladat figyeléséhez kattintson **kötegelt kérelmek napló**.

### <a name="deploy-the-predictive-experiment-as-a-classic-web-service"></a>A prediktív kísérletté egy klasszikus webszolgáltatás telepítése

Most, hogy a prediktív kísérletté megfelelően elő van készítve, telepítheti azt a klasszikus Azure webszolgáltatásként. A webszolgáltatással, felhasználók adatokat küldhet a modell, és a modell az előrejelzés ad vissza.

A prediktív kísérletté telepítéséhez kattintson **futtatása** a lap alján a kísérlet vászonra, és kattintson a **webes szolgáltatás telepítése**. A webszolgáltatás be van állítva, és a webes szolgáltatás irányítópultot kerülnek.

![A webszolgáltatás üzembe helyezése](./media/publish-a-machine-learning-web-service/figure-2.png)

#### <a name="test-your-classic-web-service"></a>A klasszikus webes szolgáltatás tesztelése

A webszolgáltatás a Machine Learning webszolgáltatások portál vagy a Machine Learning Studio tesztelheti.

A kérelem-válasz webszolgáltatás teszteléséhez kattintson a **tesztelése** gombra a webes szolgáltatás irányítópultján. Egy párbeszédpanel jelenik meg, és kérje meg a szolgáltatás a bemeneti adatok. Ezek azok a pontozási kísérlet által várt oszlopokat. Adja meg az adatok, és kattintson a **OK**. A webszolgáltatás által generált eredmények az irányítópult alján jelennek meg.

Kattintson a **tesztelése** preview hivatkozás a szolgáltatás tesztelése az Azure Machine Learning webszolgáltatások portálon korábban az új webes szolgáltatás szakaszban látható.

A kötegelt végrehajtási szolgáltatás teszteléséhez kattintson **tesztelése** preview hivatkozásra. A kötegelt tesztelése oldalon kattintson a Tallózás gombra a bemeneti alatt, és válassza ki a megfelelő mintaértékek tartalmazó CSV-fájl. Ha egy CSV-fájl nem rendelkezik, és a használata a Machine Learning Studio prediktív kísérletté létrehozott, a prediktív kísérletté, az adatkészlet töltse le, és használja azt.

![A webszolgáltatás tesztelése](./media/publish-a-machine-learning-web-service/figure-3.png)

Az a **konfigurációs** lapon módosítsa a szolgáltatás megjelenített nevét, és adjon neki egy leírást. A név és leírás megjelenik-e a [Azure-portálon](https://portal.azure.com/) a webszolgáltatások kezelhetik.

Megadhatja egy leírást a bemeneti adatok, a kimeneti adatok és a webes szolgáltatás paraméterei egy karakterláncot adjon meg minden oszlop alapján a **bemeneti SÉMÁT**, **kimeneti SÉMÁVAL**, és **WEBSZOLGÁLTATÁS A paraméter**. A leírások használt minta kód dokumentációjában a webszolgáltatáshoz.

Engedélyezheti a naplózás, amely a webszolgáltatás elérésekor is lát hibák diagnosztizálása érdekében. További információkért lásd: [naplózását a Machine Learning webszolgáltatások](web-services-logging.md).

![A webszolgáltatás konfigurálása](./media/publish-a-machine-learning-web-service/figure-4.png)

Beállíthatja a végpontok a webszolgáltatás az Azure Machine Learning webszolgáltatások portálon hasonló az eljárást, korábban már az új webes szolgáltatás szakaszban látható. A lehetőségek különböznek, adja hozzá, vagy módosítsa a szolgáltatás leírása, a naplózásának engedélyezése és a mintaadatok engedélyezése teszteléshez.

#### <a name="access-your-classic-web-service"></a>A klasszikus webes szolgáltatáshoz
A webszolgáltatás a Machine Learning Studio telepít, miután adatokat küldeni a szolgáltatás, és válaszokat programozott módon kapni.

Az irányítópultról a webes szolgáltatás eléréséhez szükséges összes adatot. Például az API-kulcsot a szolgáltatás a hitelesített hozzáférést biztosít, és az API súgóoldalak szolgálnak programozás a kezdéshez.

A Machine Learning webszolgáltatásba elérésével kapcsolatos további információkért lásd: [hogyan kell használni az Azure Machine Learning Web service](consume-web-services.md).

#### <a name="manage-your-classic-web-service"></a>A klasszikus webszolgáltatás kezelése
Nincsenek különböző műveleteket hajthat végre egy webszolgáltatás figyeléséhez. A frissítést, és törölje azt. Azt is megteheti további végpontok mellett az alapértelmezett végpont annak regisztrálásakor létrehozott klasszikus webszolgáltatáshoz.

További információkért lásd: [kezelése az Azure Machine Learning-munkaterület](manage-workspace.md) és [kezelése az Azure Machine Learning webszolgáltatások portál használatával egy webszolgáltatás-bővítmény](manage-new-webservice.md).

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

## <a name="update-the-web-service"></a>A webes szolgáltatás frissítése
A modell frissítése további betanítási adatok, például a webszolgáltatás módosítja, és telepítse újra, felülírja az eredeti webszolgáltatás.

A webszolgáltatás frissítéséhez nyissa meg a webszolgáltatás üzembe helyezése és szerkeszthető másolat kattintva használt az eredeti prediktív kísérletté **SAVE AS**. A módosításokat, és kattintson a **webes szolgáltatás telepítése**.

Telepítése előtt a kísérlet, mert megkérdezi, hogy szeretné-e felülírása (klasszikus webes szolgáltatás), vagy (új webszolgáltatás) a meglévő szolgáltatásnak a frissítésére. Kattintson a **Igen** vagy **frissítés** a meglévő webes szolgáltatás leáll, és telepíti az új prediktív kísérletté üzembe van helyezve a helyére.

> [!NOTE]
> Ha az eredeti webszolgáltatás konfigurációs változtatásokat hajtott végre, például megad egy új megjelenítendő név vagy leírás, akkor írja be újra ezeket az értékeket.
> 
> 

A webszolgáltatás frissítéséhez egy lehetőség egy a modell programozott módon működik. További információ: [Retrain Machine Learning models programmatically](retrain-models-programmatically.md) (Machine Learning-modellek szoftveres átképezése).

<!-- internal links -->
[Hozzon létre egy tanítási kísérletet]: #create-a-training-experiment
[Alakítsa át egy prediktív kísérletté]: #convert-the-training-experiment-to-a-predictive-experiment
[A webszolgáltatás üzembe]: #deploy-it-as-a-web-service
[új]: #deploy-the-predictive-experiment-as-a-new-Web-service
[klasszikus]: #deploy-the-predictive-experiment-as-a-new-Web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
