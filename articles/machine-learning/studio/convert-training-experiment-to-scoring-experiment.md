---
title: Modell előkészítése üzembe helyezéshez
titleSuffix: ML Studio (classic) - Azure
description: Hogyan készítse elő a betanított modellt webszolgáltatásként való üzembe helyezésre, ha átalakítja a Machine Learning Studio (klasszikus) betanítási kísérletet egy prediktív kísérletbe.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/28/2017
ms.openlocfilehash: 061c340f8c4952d5a0f2a3873f7475e4f733c290
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204511"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>Modell előkészítése a Azure Machine Learning Studio (klasszikus) üzembe helyezéséhez

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Azure Machine Learning Studio (klasszikus) megadja a prediktív elemzési modell kialakításához szükséges eszközöket, majd működővé tenni azt egy Azure-webszolgáltatásként való üzembe helyezésével.

Ennek elvégzéséhez a Studio (klasszikus) használatával hozzon létre egy kísérletet, amelyet egy *képzési kísérletnek* nevezünk – itt taníthatja ki, szerzi be és szerkesztheti a modelljét. Ha meggyőződött arról, hogy a modell készen áll a telepítésre, alakítsa át a betanítási kísérletet egy *prediktív kísérletre* , amely a felhasználói adatgyűjtésre van konfigurálva.

Ennek a folyamatnak egy példáját láthatja az [1. oktatóanyagban: hitelkockázat előrejelzése](tutorial-part1-credit-risk.md).

Ez a cikk részletes hogyan alakulnak át betanítási kísérlet be egy prediktív kísérletet, és a prediktív kísérletté telepítési módját mélyreható vesz igénybe. Megismerésével ezeket az adatokat, ismerje meg az üzembe helyezett modell hatékonyabbá tétele konfigurálása.



## <a name="overview"></a>Áttekintés 

A betanítási kísérlet átalakítása prediktív kísérletté folyamat három lépésből áll:

1. Cserélje le a gépi tanulási algoritmus-modulok a betanított modell.
2. Trim csak a szükséges ahhoz, hogy pontozási modulok a kísérletet. Betanítási kísérlet számos, amelyek szükségesek a képzés, de nincs rájuk szükség, ha a modell tanítása.
3. Határozza meg, hogy a modell a webes felhasználói adatokat fogad, és milyen adatokat vissza kell.

> [!TIP]
> A betanítási kísérlet során már az érintett a tanítási és pontozási a modell használatával saját adatai. De üzembe helyezését követően felhasználók küld-e az új adatokat a modellbe, és előrejelzési eredményeket adja vissza. Úgy ahogy a betanítási kísérlet átalakítása egy prediktív kísérletet, hogy előkészítse a telepítéshez, tartsa szem előtt a modell mások általi felhasználási módjáról.
> 
> 

## <a name="set-up-web-service-button"></a>Webszolgáltatás beállítása gomb
A kísérlet futtatása után (a kísérlet vászon alján kattintson a **Futtatás** gombra), kattintson a **webszolgáltatás beállítása** gombra (válassza ki a **prediktív webszolgáltatás** lehetőséget). A **webszolgáltatás beállítása** a betanítási kísérlet prediktív kísérletre történő átalakításának három lépését végzi el:

1. Elmenti a betanított modellt a modul palettájának **betanított modellek** szakaszában (a kísérlet vászontól balra). Ezután lecseréli a Machine learning-algoritmust és [betanítja a modell][train-model] modulokat a mentett betanított modellel.
2. Az eszköz elemzi a kísérlethez, és eltávolítja a modult, amely egyértelműen használták csak képzés, és már nincs szükség.
3. A _webszolgáltatások bemeneti_ és _kimeneti_ moduljait a kísérlet alapértelmezett helyeire szúrja be (ezek a modulok elfogadják és visszaadják a felhasználói adatokat).

Ha például az alábbi kísérlet betanítja népszámlálási mintaadatokkal kétosztályos gyorsított döntési fa modell:

![Betanítási kísérlet](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

A kísérletben a modulok alapvetően négy különböző funkciók hajtsa végre:

![A modul funkciók](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

A betanítási kísérlet átalakítása prediktív kísérletté, ha ezeket a modulokat némelyike már nincs rá szükség, vagy már eltérő célra szolgál:

* **Adat** – a minta adatkészletben lévő adatokat a rendszer nem használja pontozás közben – a webszolgáltatás felhasználója megadja az adatokat. Ez az adatkészlet, adattípusok, például metaadataiból azonban a betanított modell használják. Így kell tartani az adatkészletet a prediktív kísérletté úgy, hogy ezeket a metaadatokat.

* **PREP** – attól függően, hogy milyen felhasználói adat lesz elküldve a pontozáshoz, ezek a modulok esetleg nem szükségesek a bejövő adat feldolgozásához. A **webszolgáltatás beállítása** gomb nem érinti ezeket – el kell döntenie, hogyan szeretné kezelni őket.
  
    Ebben a példában például a minta adatkészlet hiányzó értékekkel rendelkezhet, ezért a rendszer egy [tiszta hiányzó][clean-missing-data] adatmodult tartalmazott a velük való kezeléshez. A minta adatkészlet is oszlopokat, amelyeket nem szükségesek a modell betanításához. Így az [adatkészlet modul Select oszlopai][select-columns] is kizárhatók a további oszlopoknak az adatfolyamatból való kizárásával. Ha tudja, hogy a webszolgáltatáson keresztüli pontozásra elküldött adatok nem rendelkeznek hiányzó értékekkel, akkor eltávolíthatja a [hiányzó adatok][clean-missing-data] törlése modult. Mivel azonban az [adatkészlet modul Select oszlopai][select-columns] segítenek meghatározni a betanított modell által várt adatoszlopokat, a modulnak meg kell maradnia.

* **Betanítás** – ezek a modulok a modell betanítására szolgálnak. Ha a **webszolgáltatás beállítása**elemre kattint, a rendszer lecseréli ezeket a modulokat egyetlen modulra, amely tartalmazza a betanított modellt. Ezt az új modult a modul paletta **betanított modellek** szakasza menti.

* **Pontszám** – ebben a [példában az adatstreamek a][split] tesztelési és a betanítási adatként való felosztására szolgálnak. A prediktív kísérletben nem dolgozunk fel többé, ezért a [felosztott adatvesztést][split] el lehet távolítani. Hasonlóképpen, a második [pontszámot modellező][score-model] modul és a [modell kiértékelése][evaluate-model] modul a vizsgálati adatok eredményeinek összehasonlítására szolgál, így ezek a modulok nem szükségesek a prediktív kísérlet során. A fennmaradó [pontszám modell][score-model] modulnak azonban a webszolgáltatáson keresztüli pontszám eredményét kell visszaadnia.

A példa a **webszolgáltatás beállítása**elemre való kattintás után a következőképpen néz ki:

![A konvertált prediktív kísérletté](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

A **webszolgáltatások beállításával** végzett munka elegendő lehet a kísérlet webszolgáltatásként való üzembe helyezésének előkészítéséhez. Azonban érdemes néhány adott kísérletét, további munkát.

### <a name="adjust-input-and-output-modules"></a>Módosítsa a bemeneti és kimeneti modulok
A betanítási kísérlet során használt betanítási adatok egy készletét, és ezután volt a feldolgozást egy képernyő, amely a machine learning algoritmus szükséges az adatok beolvasásához. Ha a webszolgáltatásban várhatóan megjelenő adatokra nem lesz szükség a feldolgozásra, megkerülheti azt: a **webszolgáltatások bemeneti moduljának** kimenetét a kísérlet egy másik moduljának összekapcsolásával is elvégezheti. A felhasználó adatait most érkezik a modellben, ezen a helyen.

Alapértelmezés szerint például a **webszolgáltatás beállítása** a **webszolgáltatás bemeneti** modulját az adatfolyamat tetején helyezi el, ahogy az a fenti ábrán is látható. Azonban manuálisan is elhelyezheti a **webszolgáltatás bemenetét** az adatfeldolgozó moduloknál:

![A web service bemeneti áthelyezése](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

A bemeneti adatokat a web service keresztül közvetlenül a Score Model-modul mostantól továbbítja a bármely előfeldolgozás nélkül.

Hasonlóképpen, alapértelmezés szerint a **webszolgáltatás** az adatfolyam alján helyezi üzembe a webszolgáltatás kimeneti modulját. Ebben a példában a webszolgáltatás visszaadja a felhasználónak a [pontszám modell][score-model] modul kimenetét, amely magában foglalja a teljes bemeneti adatvektort, valamint a pontozás eredményét.
Ha azonban inkább egy másikat szeretne visszaadni, akkor a **webszolgáltatás kimeneti** modulja előtt további modulokat is hozzáadhat. 

Ha például csak a pontozás eredményét szeretné visszaadni, és nem a bemeneti adatok teljes vektorát, vegyen fel egy [Select oszlopot az adatkészlet][select-columns] modulban az összes oszlop kizárásához, kivéve a pontozás eredményét. Ezután helyezze át a **webszolgáltatás kimeneti** modulját a [Select oszlopok][select-columns] kimenetére az adatkészlet modulban. A kísérlet így néz ki:

![A web service kimeneti áthelyezése](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Adja hozzá, vagy távolítsa el a további adatokat feldolgozó modulok
Ha a kísérlet során, hogy ismeri a kiértékelés során nem szükséges további modulok, ezek távolíthatja el. Mivel például áthelyezte a **webszolgáltatás bemeneti** modulját az adatfeldolgozási modulok utáni pontra, eltávolíthatja a [tiszta hiányzó][clean-missing-data] adatmodult a prediktív kísérletből.

A prediktív kísérletet most néz ki:

![További modul eltávolítása](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Nem kötelező webszolgáltatás-paraméterek hozzáadása
Bizonyos esetekben érdemes, hogy a felhasználó a webszolgáltatás-modulok működésének módosításához, ha hozzáfér a szolgáltatáshoz. A *webszolgáltatás paraméterei* lehetővé teszik ezt.

Gyakori példa egy [adatimportálási][import-data] modul beállítása, hogy a telepített webszolgáltatás felhasználója más adatforrást is megadhat a webszolgáltatáshoz való hozzáféréskor. Vagy az [exportálási][export-data] modul konfigurálása, hogy egy másik célhely is megadható legyen.

Webszolgáltatás-paraméterek megadása, és rendelje azokat egy vagy több modulja paraméter, és megadhatja, hogy azok a szükséges és választható. A felhasználó a webszolgáltatás értékek a szolgáltatás érhető el, és a modul műveletek módosítani ezeket a paramétereket biztosít.

A webszolgáltatás-paraméterekkel és azok használatának módjával kapcsolatos további információkért lásd: [Azure Machine learning webszolgáltatás paramétereinek használata][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>A prediktív kísérletté üzembe webszolgáltatásként
Most, hogy a prediktív kísérletté megfelelően elő van készítve, telepíthet egy Azure-webszolgáltatásként. A webszolgáltatás segítségével felhasználók küldhetnek adatokat a modell és a modell az előrejelzéseket adja vissza.

További információ a teljes telepítési folyamatról: [Azure Machine learning webszolgáltatás üzembe helyezése][deploy]

[deploy]: deploy-a-machine-learning-web-service.md

<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
