---
title: Modell előkészítése üzembe helyezéshez
titleSuffix: ML Studio (classic) Azure
description: Hogyan készítse elő a betanított modellt webszolgáltatásként való üzembe helyezésre, ha átalakítja a Machine Learning Studio (klasszikus) betanítási kísérletet egy prediktív kísérletbe.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 03/28/2017
ms.openlocfilehash: e24393783dac0f918009f3138f31bdda98bbd22e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684859"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>Modell előkészítése a Azure Machine Learning Studio (klasszikus) üzembe helyezéséhez

Azure Machine Learning Studio (klasszikus) megadja a prediktív elemzési modell kialakításához szükséges eszközöket, majd működővé tenni azt egy Azure-webszolgáltatásként való üzembe helyezésével.

Ennek elvégzéséhez a Studio klasszikus verziójával hozzon létre egy kísérletet, amelyet egy *képzési kísérletnek* nevezünk. Ha meggyőződött arról, hogy a modell készen áll a telepítésre, alakítsa át a betanítási kísérletet egy *prediktív kísérletre* , amely a felhasználói adatgyűjtésre van konfigurálva.

Ennek a folyamatnak egy példáját láthatja az [1. oktatóanyagban: hitelkockázat előrejelzése](tutorial-part1-credit-risk.md).

Ez a cikk részletesen bemutatja, hogyan alakítható át egy képzési kísérlet a prediktív kísérletbe, és hogyan történik a prediktív kísérlet üzembe helyezése. Ezen információk megismerésével megtudhatja, hogyan konfigurálhatja a telepített modellt, hogy hatékonyabbá váljon.



## <a name="overview"></a>Áttekintés 

A betanítási kísérlet prediktív kísérletre való átalakításának folyamata három lépést tesz szükségessé:

1. Cserélje le a Machine learning-algoritmus moduljait a betanított modellre.
2. A kísérletet csak azokra a modulokra vágja, amelyek a pontozáshoz szükségesek. A betanítási kísérlet több modult is tartalmaz, amelyek szükségesek a képzéshez, de nem szükségesek a modell betanítása után.
3. Határozza meg, hogy a modell hogyan fogadja el az adatok elfogadását a webszolgáltatás-felhasználótól, és hogy milyen adatok lesznek visszaadva.

> [!TIP]
> A betanítási kísérlet során a saját adatai alapján a modell betanításával és pontozásával foglalkozott. Az üzembe helyezést követően azonban a felhasználók új adatait küldenek a modellbe, és az előrejelzési eredményeket fogják visszaadni. Tehát, ahogy átalakítja a betanítási kísérletet egy prediktív kísérletre, hogy készen álljon az üzembe helyezésre, ne feledje, hogy a modelleket mások is használják.
> 
> 

## <a name="set-up-web-service-button"></a>Webszolgáltatás beállítása gomb
A kísérlet futtatása után (a kísérlet vászon alján kattintson a **Futtatás** gombra), kattintson a **webszolgáltatás beállítása** gombra (válassza ki a **prediktív webszolgáltatás** lehetőséget). A **webszolgáltatás beállítása** a betanítási kísérlet prediktív kísérletre történő átalakításának három lépését végzi el:

1. Elmenti a betanított modellt a modul palettájának **betanított modellek** szakaszában (a kísérlet vászontól balra). Ezután lecseréli a Machine learning-algoritmust és [betanítja a modell][train-model] modulokat a mentett betanított modellel.
2. Elemzi a kísérletet, és eltávolítja azokat a modulokat, amelyeket egyértelműen csak képzésre használtak, és amelyekre már nincs szükség.
3. A _webszolgáltatások bemeneti_ és _kimeneti_ moduljait a kísérlet alapértelmezett helyeire szúrja be (ezek a modulok elfogadják és visszaadják a felhasználói adatokat).

Az alábbi kísérlet például egy kétosztályos, kibővített döntési famodellt alkalmaz a minta-számbavételi adataival:

![Betanítási kísérlet](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

A kísérlet moduljai alapvetően négy különböző funkciót látnak el:

![Modul függvények](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Ha ezt a betanítási kísérletet prediktív kísérletre konvertálja, ezeknek a moduloknak már nincs rá szüksége, vagy már más célt szolgálnak:

* **Adat** – a minta adatkészletben lévő adatokat a rendszer nem használja pontozás közben – a webszolgáltatás felhasználója megadja az adatokat. A betanított modell azonban a metaadatokat, például az adattípusokat használja. Ezért meg kell őriznie az adatkészletet a prediktív kísérletben, hogy meg tudja adni ezt a metaadatokat.

* **PREP** – attól függően, hogy milyen felhasználói adat lesz elküldve a pontozáshoz, ezek a modulok esetleg nem szükségesek a bejövő adat feldolgozásához. A **webszolgáltatás beállítása** gomb nem érinti ezeket – el kell döntenie, hogyan szeretné kezelni őket.
  
    Ebben a példában például a minta adatkészlet hiányzó értékekkel rendelkezhet, ezért a rendszer egy [tiszta hiányzó][clean-missing-data] adatmodult tartalmazott a velük való kezeléshez. Emellett a minta adatkészlet olyan oszlopokat is tartalmaz, amelyek nem szükségesek a modell betanításához. Így az [adatkészlet modul Select oszlopai][select-columns] is kizárhatók a további oszlopoknak az adatfolyamatból való kizárásával. Ha tudja, hogy a webszolgáltatáson keresztüli pontozásra elküldött adatok nem rendelkeznek hiányzó értékekkel, akkor eltávolíthatja a [hiányzó adatok][clean-missing-data] törlése modult. Mivel azonban az [adatkészlet modul Select oszlopai][select-columns] segítenek meghatározni a betanított modell által várt adatoszlopokat, a modulnak meg kell maradnia.

* **Betanítás** – ezek a modulok a modell betanítására szolgálnak. Ha a **webszolgáltatás beállítása**elemre kattint, a rendszer lecseréli ezeket a modulokat egyetlen modulra, amely tartalmazza a betanított modellt. Ezt az új modult a modul paletta **betanított modellek** szakasza menti.

* **Pontszám** – ebben a [példában az adatstreamek a][split] tesztelési és a betanítási adatként való felosztására szolgálnak. A prediktív kísérletben nem dolgozunk fel többé, ezért a [felosztott adatvesztést][split] el lehet távolítani. Hasonlóképpen, a második [pontszámot modellező][score-model] modul és a [modell kiértékelése][evaluate-model] modul a vizsgálati adatok eredményeinek összehasonlítására szolgál, így ezek a modulok nem szükségesek a prediktív kísérlet során. A fennmaradó [pontszám modell][score-model] modulnak azonban a webszolgáltatáson keresztüli pontszám eredményét kell visszaadnia.

A példa a **webszolgáltatás beállítása**elemre való kattintás után a következőképpen néz ki:

![Átalakított prediktív kísérlet](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

A **webszolgáltatások beállításával** végzett munka elegendő lehet a kísérlet webszolgáltatásként való üzembe helyezésének előkészítéséhez. Azonban érdemes lehet elvégezni egy további, a kísérlethez kapcsolódó munkát.

### <a name="adjust-input-and-output-modules"></a>Bemeneti és kimeneti modulok módosítása
A betanítási kísérlet során a betanítási adatkészletet használta, majd elvégezte a feldolgozást, hogy a gépi tanulási algoritmust igénylő űrlapon beolvassa az adatait. Ha a webszolgáltatásban várhatóan megjelenő adatokra nem lesz szükség a feldolgozásra, megkerülheti azt: a **webszolgáltatások bemeneti moduljának** kimenetét a kísérlet egy másik moduljának összekapcsolásával is elvégezheti. A felhasználó ekkor megérkezik a modellbe ezen a helyen.

Alapértelmezés szerint például a **webszolgáltatás beállítása** a **webszolgáltatás bemeneti** modulját az adatfolyamat tetején helyezi el, ahogy az a fenti ábrán is látható. Azonban manuálisan is elhelyezheti a **webszolgáltatás bemenetét** az adatfeldolgozó moduloknál:

![Webszolgáltatás bemenetének áthelyezése](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

A webszolgáltatáson keresztül megadott bemeneti adatok mostantól közvetlenül a pontszám modell modulba kerülnek, előfeldolgozás nélkül.

Hasonlóképpen, alapértelmezés szerint a **webszolgáltatás** az adatfolyam alján helyezi üzembe a webszolgáltatás kimeneti modulját. Ebben a példában a webszolgáltatás visszaadja a felhasználónak a [pontszám modell][score-model] modul kimenetét, amely magában foglalja a teljes bemeneti adatvektort, valamint a pontozás eredményét.
Ha azonban inkább egy másikat szeretne visszaadni, akkor a **webszolgáltatás kimeneti** modulja előtt további modulokat is hozzáadhat. 

Ha például csak a pontozás eredményét szeretné visszaadni, és nem a bemeneti adatok teljes vektorát, vegyen fel egy [Select oszlopot az adatkészlet][select-columns] modulban az összes oszlop kizárásához, kivéve a pontozás eredményét. Ezután helyezze át a **webszolgáltatás kimeneti** modulját a [Select oszlopok][select-columns] kimenetére az adatkészlet modulban. A kísérlet így néz ki:

![Webszolgáltatás kimenetének áthelyezése](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>További adatfeldolgozási modulok hozzáadása vagy eltávolítása
Ha a kísérletben több modul is van, de nem lesz szükség a pontozásra, ezeket a rendszer eltávolítja. Mivel például áthelyezte a **webszolgáltatás bemeneti** modulját az adatfeldolgozási modulok utáni pontra, eltávolíthatja a [tiszta hiányzó][clean-missing-data] adatmodult a prediktív kísérletből.

A prediktív kísérlet így néz ki:

![További modul eltávolítása](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Opcionális webszolgáltatás-paraméterek hozzáadása
Bizonyos esetekben előfordulhat, hogy lehetővé szeretné tenni a webszolgáltatás felhasználójának, hogy megváltoztassa a modulok viselkedését a szolgáltatás elérésekor. A *webszolgáltatás paraméterei* lehetővé teszik ezt.

Gyakori példa egy [adatimportálási][import-data] modul beállítása, hogy a telepített webszolgáltatás felhasználója más adatforrást is megadhat a webszolgáltatáshoz való hozzáféréskor. Vagy az [exportálási][export-data] modul konfigurálása, hogy egy másik célhely is megadható legyen.

Meghatározhatja a webszolgáltatás paramétereit, és társíthatja azokat egy vagy több modul-paraméterrel, és megadhatja, hogy kötelező vagy opcionális-e. A webszolgáltatás felhasználója ezeket a paramétereket adja meg, ha a szolgáltatás elérhető, és a modul műveletei ennek megfelelően módosulnak.

A webszolgáltatás-paraméterekkel és azok használatának módjával kapcsolatos további információkért lásd: [Azure Machine learning webszolgáltatás paramétereinek használata][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>A prediktív kísérlet webszolgáltatásként való üzembe helyezése
Most, hogy a prediktív kísérlet megfelelően elkészült, üzembe helyezhető Azure-webszolgáltatásként. A webszolgáltatás használatával a felhasználók elküldhetik az adatait a modellbe, és a modell visszaadja az előrejelzéseit.

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
