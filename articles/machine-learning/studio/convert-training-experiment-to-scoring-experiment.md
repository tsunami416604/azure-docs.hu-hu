---
title: "A modell Azure Machine Learning Studio központi telepítésének előkészítése |} Microsoft Docs"
description: "Hogyan készíti elő a betanított modell a központi telepítés webszolgáltatásként alakítja át a Machine Learning Studio tanítási kísérletet egy prediktív kísérletté."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: eb943c45-541a-401d-844a-c3337de82da6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: garye
ms.openlocfilehash: 96434d6c5a2cf73e425c02d35d546ab33b7ac13d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio"></a>A modell Azure Machine Learning Studio központi telepítésének előkészítése

Az Azure Machine Learning Studio biztosítja az eszközöket, a prediktív elemzés modellezése, és ezután egy Azure webes szolgáltatás telepítésével azok kell.

Ehhez a kísérlet létrehozásának - nevű használhatja Studio egy *tanítási kísérletet* – amelyen betanítása, pontozása, és szerkesztheti a modell. Ha már elégedett, kap a modell a tanítási kísérletet, és átalakításával üzembe helyezésére egy *prediktív kísérletté* pontszám felhasználói adatok megfelelően van konfigurálva.

Ez a folyamat egy példa látható [forgatókönyv: a hitelkockázat értékelésére az Azure Machine Learning a prediktív elemzési megoldás fejlesztése](walkthrough-develop-predictive-solution.md).

Ez a cikk egy részletes bemutatója a részleteinek hogyan lekérdezi a egy tanítási kísérletet egy prediktív kísérletté alakítja át, és hogyan legyen telepítve központilag, hogy prediktív kísérletté vesz igénybe. Ezen adatok megismerni, ismerje meg a központilag telepített modell abba, hogy hatékonyabb konfigurálása.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="overview"></a>Áttekintés 

A folyamat egy tanítási kísérletet egy prediktív kísérletté átalakítása három lépést foglal magában:

1. Cserélje le a gépi tanulási algoritmus modulok a betanított modell.
2. Trim a kísérletet, és csak a szükséges pontozási modulok. A tanítási kísérletet, amelyek szükségesek a képzési, de nem szükségesek, ha a modell betanítása modulok számos tartalmaz.
3. Határozza meg, hogy a modell a webes felhasználói adatokat fogad, és milyen adatok visszaadásához fog.

> [!TIP]
> A tanítási kísérletet, a képzési és a saját adatok használata a modell pontozása már megtörtént. De központi telepítésének végrehajtása után felhasználók új adatokat küld a modell, és előrejelzés eredményeket ad vissza. Igen a tanítási kísérletet átalakítása egy prediktív kísérletté, hogy előkészítse a telepítéshez, mivel tartsa szem előtt a modell mások által használt hogyan.
> 
> 

## <a name="set-up-web-service-button"></a>Webes szolgáltatások beállítása gomb
A kísérlet futtatása után (kattintson **futtatása** a kísérletvászonra alján), kattintson a **webes szolgáltatások beállítása** gomb (válassza ki a **prediktív webszolgáltatás** lehetőséget). **Webes szolgáltatások beállítása** hajt végre, a tanítási kísérletet egy prediktív kísérletté átalakítása három lépést:

1. A betanított modell menti a **betanított modellek** a modulpalettán (bal oldalán a kísérletvászonra) szakasza. Felülírja a gépi tanulási algoritmus és [tanítási modell] [ train-model] a mentett betanított modell rendelkező modulok.
2. Elemzi a kísérletet, és eltávolítja a modult, amely egyértelműen használták csak képzési és már nem szükséges.
3. Beilleszti _webszolgáltatás bemenetét_ és _kimeneti_ modulok importálása a kísérlet során (ezek a modulok fogadja el, és térjen vissza a felhasználói adatok) alapértelmezett helyét.

Például a következő kísérlet betanítja két osztályú súlyozott döntési fa-modellben nyilvántartásba mintaadatok:

![Tanítási kísérletet][figure1]

A modul a kísérletben alapvetően négy különböző feladatokat látnak el:

![A modul funkciók][figure2]

A tanítási kísérletet egy prediktív kísérletté alakításakor néhányat ezek a modulok már nem szükséges, vagy most más célt szolgál:

* **Adatok** -pontozási nem használják a minta adatkészlet adatainak – a felhasználó a webszolgáltatás pontozni adatokat fogja adni. A metaadatok ehhez az adatkészlethez, adattípusok, például a azonban a betanított modell használatával. Ezért meg kell tartani az adatkészletet a prediktív kísérletté, akkor több a metaadatok.

* **Előkészítő** – attól függően, hogy a felhasználói adatokat, amelyek ezeket a modulokat is, vagy nem lehet feldolgozni a bejövő adatok szükséges pontozó, elküldve. A **webes szolgáltatások beállítása** gomb nem touch ezek – el kell döntenie, hogyan szeretné kezelni azokat.
  
    Például az ebben a példában a minta-adatkészleteken érték hiányzik, ezért lehet egy [Clean Missing Data] [ clean-missing-data] modul megtalálható azokkal kapcsolatban. A minta-adatkészleteken is oszlopok esetén nem szükséges a modell betanításához. Ezért egy [Select Columns in Dataset] [ select-columns] modul megtalálható-e felesleges oszlopok kizárása az adatfolyam. Ha tudja, hogy a pontozó a webszolgáltatás keresztül küldött adatok nem lesznek a hiányzó értékeket, akkor eltávolíthatja az [Clean Missing Data] [ clean-missing-data] modul. Mivel azonban a [Select Columns in Dataset] [ select-columns] modul segítségével határozza meg, amely a betanított modell vár az oszlopokat, modult meg kell maradnia.

* **A vonat** -ezek a modulok a modell betanításához használandó. Amikor rákattint **webes szolgáltatások beállítása**, ezek a modulok egyetlen modult tartalmaz a modell betanítása Ön helyett. Ez a modul menti a **betanított modellek** a modulpalettán szakasza.

* **Pontszám** – ebben a példában a [Split Data] [ split] modul szolgál az adatfolyamot felosztani a Tesztadatok és betanítási adata. A prediktív kísérletben azt még nem betanítása többé, így [Split Data] [ split] távolíthatja el. Hasonlóképpen, a második [Score Model] [ score-model] modul és a [modell kiértékelése] [ evaluate-model] modul segítségével, hasonlítsa össze a Tesztadatok eredményei Ezek a modulok nem szükségesek a prediktív kísérletben. A fennmaradó [Score Model] [ score-model] modul, azonban szükség van a webszolgáltatás keresztül pontszám eredményének.

Ez a példa megjelenésének kattintás után **webes szolgáltatások beállítása**:

![Prediktív kísérletté konvertálni][figure3]

Dolgozott **webes szolgáltatások beállítása** elegendő lehet, hogy készítse elő a kísérletet, és egy webszolgáltatás telepíthető. Azonban érdemes lehet néhány további munkáját jellemző a kísérlet során.

### <a name="adjust-input-and-output-modules"></a>Módosítsa a bemeneti és kimeneti modulok
A tanítási kísérletet használt betanítási adatok, és majd volt az egyes feldolgozási oly módon, hogy a gépi tanulási algoritmus szükséges adatok megszerzéséhez. A webes szolgáltatás fogadni várt adatok nem kell a feldolgozás, ha megkerüléséhez azt: csatlakozás kimenetét a **webszolgáltatás bemeneti modul** egy másik modul a kísérletben a. A felhasználó adatait most érkeznek ezen a helyen a modellben.

Alapértelmezés szerint például **webes szolgáltatások beállítása** helyezi a **webszolgáltatás bemenetét** modul felső részén a adatfolyam, a fenti ábrán látható módon. De azt manuálisan is elhelyezheti a **webszolgáltatás bemenetét** múltbeli adatok feldolgozása modulok:

![A webszolgáltatás bemenetét áthelyezése][figure4]

A webszolgáltatás megadott bemeneti adatok most fogja továbbítani közvetlenül a Score Model modulba bármely előfeldolgozása nélkül.

Hasonlóképpen, alapértelmezés szerint **webes szolgáltatások beállítása** helyezi a webes szolgáltatás kimenetigyorsítótár-modul a adatfolyama alján. Ebben a példában a webszolgáltatás fog térni a felhasználó a kimenetét a [Score Model] [ score-model] modult, amely a bemeneti adatok teljes vektor mellett a pontozási eredményeinek is.
Azonban ha valami más vissza szeretne, majd hozzá további modulok előtt a **webes szolgáltatás kimeneti** modul. 

Például csak a pontozási eredményeinek és a bemeneti adatok nem a teljes vektort adnak vissza, vegye fel a [Select Columns in Dataset] [ select-columns] a pontozási eredményeinek kivételével az összes oszlop kizárásához modul. Ezután lépjen a **webes szolgáltatás kimeneti** modul kimenetével a [Select Columns in Dataset] [ select-columns] modul. A kísérlet így néz ki:

![A webes szolgáltatás kimeneti áthelyezése][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>Adja hozzá, vagy távolítsa el a további adatok feldolgozása modulok
Ha a kísérletben, amelyek biztosan pontozási során nem szükséges további modulok, ezek távolíthatja el. Például mert helyeztük a **webszolgáltatás bemenetét** modult a pont után az adatok feldolgozása modulok, azt is távolítsa el a [Clean Missing Data] [ clean-missing-data] modult a prediktív kísérletté.

A prediktív kísérletté most néz ki:

![További modul eltávolítása][figure6]


### <a name="add-optional-web-service-parameters"></a>Adja hozzá a választható paraméterek: Web Service
Bizonyos esetekben érdemes lehet a felhasználó a webszolgáltatás modulok működésének módosításához, ha a szolgáltatás hozzáfér. *Webszolgáltatási paramétereket* lehetővé teszi ezt.

Ilyenek például beállítását egy [és adatokat importálhat] [ import-data] modul, ezért a felhasználó az üzembe helyezett webszolgáltatás adhat meg egy másik adatforráshoz, a webszolgáltatás elérésekor. Vagy konfigurálása egy [adatok exportálása] [ export-data] modul, hogy egy másik cél adható meg.

Adja meg a webszolgáltatás-paramétereket, és rendelje hozzá őket egy vagy több modulja paramétert, és megadhatja, hogy azok kötelező vagy választható. A felhasználó a webszolgáltatás értékeket biztosít ezeket a paramétereket, amikor hozzáfér a szolgáltatáshoz, és ennek megfelelően módosítják a modul műveletek.

Mik azok a webszolgáltatás-paramétereket és a használatukat kapcsolatos további információkért lásd: [használata Azure Machine Learning webszolgáltatás-paramétereket][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>A prediktív kísérletté egy webszolgáltatás-bővítmény telepítése
Most, hogy a prediktív kísérletté megfelelően elő van készítve, telepítheti azt egy Azure webszolgáltatásként. A webszolgáltatással, felhasználók adatokat küldhet a modell, és a modell az előrejelzés ad vissza.

A teljes telepítési folyamat további információkért lásd: [az Azure Machine Learning webszolgáltatás telepítése][deploy]

[deploy]: publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
