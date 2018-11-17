---
title: Hogyan válik az Azure Machine Learning-modell, egy webszolgáltatás |} A Microsoft Docs
description: Beállítás esetén hogyan az Azure Machine Learning modell különböző fázisokon halad a fejlesztési experiment-szolgáltatáscsomagot Web Service áttekintése.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.custom: (previous ms.author yahajiza)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 25e0c025-f8b0-44ab-beaf-d0f2d485eb91
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 82c9573e014d18fa52dbcb4441f8f939cede604a
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51820518"
---
# <a name="how-a-machine-learning-model-progresses-from-an-experiment-to-an-operationalized-web-service"></a>Hogyan egy Machine Learning-modellek útja a kísérlettől kísérlet egy szolgáltatáscsomagot Web Service
Az Azure Machine Learning Studio biztosítja egy interaktív vászonra, amely lehetővé teszi a fejlesztést, futtassa, tesztelése és ismételt futtatásával egy ***kísérletezhet*** jelölő prediktív elemzési modellek. Nincsenek modulok érhető el, amelyek segítségével számos:

* A bemeneti adatokat a kísérletbe
* Az adatok kezelése
* Gépi tanulási algoritmusok használatával modell betanítása
* A modell pontozása
* Eredmények értékelése
* Kimeneti végső értékek

Ha már elégedett is futtathatja a kísérletet, telepítheti azt egy ***klasszikus Azure Machine Learning Web service*** vagy egy ***új Azure Machine Learning Web service*** , hogy a felhasználók küldhetnek bele adatokat és kap vissza eredmények.

Ez a cikk a idejéről hogyan-szolgáltatáscsomagot webszolgáltatás a gépi tanulási modell különböző fázisokon halad a fejlesztési kísérletezést áttekintést ad a.

> [!NOTE]
> Egyéb módon fejleszthet és helyezhet üzembe a machine learning-modellek, de ez a cikk a Machine Learning Studio használatának összpontosít. Például egy klasszikus prediktív webszolgáltatás létrehozása az r nyelv leírása olvasható, tekintse meg a következő blogbejegyzésben: [Build & üzembe prediktív Web Apps használatával RStudio és az Azure Machine Learning](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).
> 
> 

Bár az Azure Machine Learning Studióban az célja, hogy a fejlesztés és üzembe helyezéséhez egy *prediktív elemzési modellek*, lehetséges, egy kísérlet, amely nem tartalmazza a prediktív elemzési modellek fejlesztése a Studio használatával. Például egy kísérlet előfordulhat, hogy csak a bemeneti adatokat, kezelheti és majd az eredményeket. Csakúgy, mint egy prediktív elemzési kísérletet telepítheti a nem prediktív kísérletté webszolgáltatásként, de azért egyszerűbb, mert a kísérlet nem képzés, vagy egy machine learning-modell pontozása. Nem érhető el a Studióval ily módon a tipikus, amíg azt fogjuk foglalja bele a hozzászólás, hogy hogyan működik a Studio teljes leírását biztosítani tudjuk.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Fejlesztés és a egy prediktív webszolgáltatás üzembe helyezése
Az alábbiakban a tipikus-megoldások fejlesztése és üzembe helyezni a Machine Learning Studio használatával a következő szakaszokban:

![Üzembe helyezési folyamat](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*1. ábra – egy tipikus prediktív elemzési modellek fázisa*

### <a name="the-training-experiment"></a>A betanítási kísérlet
A ***betanítási kísérlet*** fejlesztése a Machine Learning Studio webszolgáltatás első fázisa. A betanítási kísérlet célja, hogy egy helyen történő fejlesztéséhez, teszteléséhez, ismételt futtatásával, és végül a machine learning-modell betanításához. Akkor is még több-modellek betanításához egyszerre, a legjobb megoldást keres, de miután elkészült kísérletezés, ki kell választania egy betanított modell, és megszüntetheti a többi a kísérletből. Egy prediktív elemzési kísérletet fejlesztése egy példa: [a hitelkockázatok értékeléséhez az Azure Machine Learning prediktív elemzési megoldás fejlesztése](walkthrough-develop-predictive-solution.md).

### <a name="the-predictive-experiment"></a>A prediktív kísérletté
Miután a betanított modell a tanítási kísérlet során, kattintson a **webszolgáltatás beállítása** válassza **prediktív webszolgáltatás** a Machine Learning Studióban, elindítja a folyamatot a tanítási átalakítása kísérletezést egy ***prediktív kísérletté***. A prediktív kísérletté célja a betanított modell használata a cél az, hogy idővel egyre üzembe helyezte azt egy Azure-webszolgáltatásként, új adatok pontozása céljából.

Ez a konverzió, készen áll az alábbi lépéseket:

* Átalakítás moduljai olyan egyetlen modulba képzéshez készletét, és mentse a betanított modell
* Felesleges beolvasások modulokat nem kapcsolódik a pontozás kiküszöbölése
* Adja hozzá a végleges webes szolgáltatás által használt bemeneti és kimeneti portok

Előfordulhat, hogy ha azt szeretné beolvasni a prediktív kísérletté üzembe webszolgáltatásként, amely további módosításokat. Például ha azt szeretné, hogy a webszolgáltatás számára, a kimeneti eredmények csak egy részhalmazát, hozzáadhat egy szűrési modul kimeneti portját előtt.

A konvertálási folyamat során nem a rendszer elveti a tanítási kísérlet. A folyamat befejeződése után a Studióban rendelkezik két lap található: egy a tanítási kísérlet, egy, a prediktív kísérlet. Ezzel a módszerrel, módosíthatja a tanítási kísérlet építse újra a prediktív kísérletté és a Web Service szolgáltatásának telepítése előtt. Vagy mentheti egy másolatát a tanítási kísérlet egy másik sort kísérletezésre fordított időt.

> [!NOTE]
> Amikor rákattint **prediktív webszolgáltatás** a betanítási kísérlet átalakítása prediktív kísérletté automatikus folyamat indítja el, és ez a legtöbb esetben működik jól. Ha a betanítási kísérlet túl összetett (például, hogy több elérési út, amely csatlakozik a együtt képzéshez), előfordulhat, hogy inkább manuálisan ehhez az átalakításhoz. További információkért lásd: [hogyan készülhet fel a modell üzembe helyezése az Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).
> 
> 

### <a name="the-web-service"></a>A webszolgáltatás
Ha már elégedett, hogy a prediktív kísérletté készen áll, a szolgáltatás vagy klasszikus webszolgáltatásként üzembe helyezhet, vagy egy új webszolgáltatás-alapú Azure Resource Manager. Való telepítésével, mint a modell üzembe helyezése egy *klasszikus Machine Learning Web service*, kattintson a **webszolgáltatás üzembe helyezése** válassza **Web Service telepítése [klasszikus]**. Szeretné üzembe helyezni, *új Machine Learning webszolgáltatás*, kattintson a **webszolgáltatás üzembe helyezése** válassza **[Új] Web Service telepítése**. Felhasználók mostantól adatokat küldeni a modellt, a Web service REST API használatával, és vissza az eredményeket kapni. További információ: [Az Azure Machine Learning webszolgáltatásainak használata](consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>A nem általában ez a helyzet: egy nem prediktív webszolgáltatás létrehozása
Ha a kísérlet során nem betanítást prediktív elemzési modellek, akkor nem kell létrehoznia egy tanítási kísérletet és a egy pontozó kísérletet – csak egy kísérlet van, és webszolgáltatásként üzembe. A Machine Learning Studio észleli, hogy tartalmaz-e a kísérlet egy prediktív modellt a használt modulok elemzésével.

Miután már többször is meg is futtathatja a kísérletet, és elégedett:

1. Kattintson a **webszolgáltatás beállítása** válassza **webszolgáltatás Átképezési** – a bemeneti és kimeneti csomópontok automatikusan bekerülnek
2. Kattintson a **futtatása**
3. Kattintson a **webszolgáltatás üzembe helyezése** válassza **Web Service telepítése [klasszikus]** vagy **[Új] Web Service telepítése** , amely számára telepíteni kívánja a környezettől függően.

A webszolgáltatás üzembe helyezve, és elérheti és ugyanúgy kezelheti, mint egy prediktív webszolgáltatás.

## <a name="updating-your-web-service"></a>A Web service frissítése
Most, hogy üzembe helyezte az kísérlet webszolgáltatásként, mi történik, ha szeretné frissíteni?

Attól függ, hogy frissíteni szükséges:

**A bemeneti vagy kimeneti módosítani szeretné, vagy szeretné módosítani, hogy a webszolgáltatás adatokat kezel**

Ha nem módosítja a modellt, de csak változnak, hogyan kezeli a Web service a adatokat, szerkesztheti a prediktív kísérletet, és kattintson a **webszolgáltatás üzembe helyezése** válassza **Web Service telepítése [klasszikus]** vagy **[Új] Web Service telepítése** újra. A webes szolgáltatás le van állítva, a frissített prediktív kísérletté telepíti, és a webes szolgáltatás újraindításakor.

Íme egy példa: Tegyük fel, hogy a prediktív kísérletet az előre jelzett eredménye a bemeneti adatok az egész sort adja vissza. Dönthet úgy, hogy szeretné-e a Web service egyszerűen vissza az eredményt. Így adhat hozzá egy **Projektoszlopok** modulban, a prediktív kísérletet, közvetlenül a kimeneti portra, az eredmény eltérő oszlopok kizárása előtt. Kattintva **webszolgáltatás üzembe helyezése** válassza **Web Service telepítése [klasszikus]** vagy **[Új] Web Service telepítése** újra, a Web service frissül.

**A modell új adatokkal újratanítás szeretné**

Ha meg szeretné tartani a teljes gépi tanulási modellt, de szeretné azt az új adatokkal újratanítása, két lehetősége van:

1. **A modell újratanítása, miközben a webszolgáltatás fut.** -újratanítása a modellt, a prediktív webszolgáltatás futtatása közben szeretne, ha ezt megteheti is által végzett módosítások néhány, hogy a tanítási kísérlet egy ***átképezési Kísérletezzen***, majd üzembe helyezheti, egy  ***megőrzési webes* szolgáltatás**. Ehhez az utasításokért lásd: [Retrain Machine Learning-modellek](retrain-models-programmatically.md).
2. **Lépjen vissza az eredeti tanítási kísérletet, és különböző betanítási adatok használata a modell fejlesztéséhez** – a Web Service a prediktív kísérletté kapcsolódik, de a tanítási kísérlet nem közvetlenül kapcsolódó ily módon. Ha módosítja az eredeti tanítási kísérletet, és kattintson a **webszolgáltatás beállítása**, hoz létre egy *új* prediktív, amellyel kísérletezhet telepítésekor létrehoz egy *új* webes a szolgáltatás. Csak akkor nem frissíti az eredeti webszolgáltatás.
   
   Ha módosítania kell a tanítási kísérlet, nyissa meg, és kattintson a **Mentés másként** másolását. Ezzel érintetlenül hagyja az eredeti betanítási kísérlet, prediktív kísérletet, és webes szolgáltatás. Mostantól létrehozhat egy új webszolgáltatás a módosításokat. Miután üzembe helyezte az új webes szolgáltatás, majd eldöntheti, hogy állítsa le az előző webes szolgáltatás, vagy a zavartalan működés mellett az újat.

**Szeretne egy másik modell betanítása**

Szeretné módosítja az eredeti prediktív kísérletet, például adjon meg egy másik gépi tanulási algoritmusba, próbálkozzon egy másik képzési metódust, stb., akkor el kell végezni a második eljárás a modell átképezési fent ismertetett: Nyissa meg a betanítási kísérlet, kattintson a **Mentés másként** készítsen róla egy másolatot, és indítsa el az új elérési utat a modell fejlesztéséhez, a prediktív kísérletté létrehozásának és a web Service szolgáltatásának telepítése le. Ezzel létrehoz egy új webes szolgáltatás egymástól független az eredeti kapcsolatot – eldöntheti, melyik vagy mindkettő futtatásának folytatásához.

## <a name="next-steps"></a>További lépések
Fejlesztés és a kísérlet a további részletekért tekintse meg a következő cikkeket:

* a kísérlet - átalakítás [hogyan készülhet fel a modell üzembe helyezése az Azure Machine Learning Studióban](convert-training-experiment-to-scoring-experiment.md)
* Deploying a Web service - [egy Azure Machine Learning webszolgáltatás üzembe helyezése](publish-a-machine-learning-web-service.md)
* a modell - átképezési [Retrain Machine Learning-modellek](retrain-models-programmatically.md)

A teljes folyamat példákért lásd:

* [Machine learning-oktatóanyag: az első kísérlet létrehozása az Azure Machine Learning Studióban](create-experiment.md)
* [Forgatókönyv: A hitelkockázat értékelése az Azure Machine Learning a prediktív elemzési megoldás fejlesztése](walkthrough-develop-predictive-solution.md)

