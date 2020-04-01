---
title: '3. oktatóanyag: Hitelkockázati modell telepítése'
titleSuffix: Azure Machine Learning Studio (classic)
description: Részletes oktatóanyag, amely bemutatja, hogyan hozhat létre prediktív elemzési megoldást a hitelkockázat-értékeléshez az Azure Machine Learning Studio (klasszikus) környezetében. Ez az oktatóanyag egy háromrészes oktatóanyag-sorozat harmadik része. Bemutatja, hogyan telepíthet egy modellt webszolgáltatásként.
keywords: hitelkockázat, prediktív elemzési megoldás, kockázatelemzés, üzembe helyezés, webszolgáltatás
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 9fb0b59374edf322e5e2221b90e912ee2c665bac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79204153"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio-classic"></a>3. oktatóanyag: Hitelkockázati modell üzembe helyezése – Azure Machine Learning Studio (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Ebben az oktatóanyagban tekintse meg a prediktív elemzési megoldás kifejlesztésének folyamatát. Egy egyszerű modellt fejleszt a Machine Learning Studio (klasszikus) alkalmazásban.  Ezután üzembe helyezi a modellt egy Azure Machine Learning-webszolgáltatásként.  Ez az üzembe helyezett modell új adatok használatával előrejelzéseket készíthet. Ez az oktatóanyag **egy háromrészes oktatóanyag-sorozat harmadik része.**

Tegyük fel, hogy előrejelzést kell készíteni egy személy hitelkockázatáról az általa kitöltött hitelkérelemben megadott adatok alapján.  

Hitelkockázat értékelése egy összetett probléma, de ez a bemutató egyszerűsíti azt egy kicsit. Ezt fogja használni példaként arra, hogyan hozhat létre prediktív elemzési megoldást a Microsoft Azure Machine Learning Studio (klasszikus) használatával. Ehhez a megoldáshoz az Azure Machine Learning Studio (klasszikus) és egy Machine Learning-webszolgáltatás használható. 

Ebben a három részes oktatóanyagban a nyilvánosan elérhető hitelkockázati adatokkal kezdheti.  Ezután kifejleszt és betanít egy prediktív modellt.  Végül a modellt webszolgáltatásként telepíti.

Az [oktatóanyag első részében](tutorial-part1-credit-risk.md)létrehozott egy Machine Learning Studio (klasszikus) munkaterületet, feltöltött adatokat, és létrehozott egy kísérletet.

Az [oktatóanyag második részében](tutorial-part2-credit-risk-train.md)betanított és értékelt modelleket.

Ebben a részben a bemutató, hogy:

> [!div class="checklist"]
> * Felkészülés az üzembe helyezésre
> * A webszolgáltatás üzembe helyezése
> * A webszolgáltatás tesztelése
> * A webszolgáltatás kezelése
> * Hozzáférés a webszolgáltatáshoz

## <a name="prerequisites"></a>Előfeltételek

Teljes [második rész a bemutató](tutorial-part2-credit-risk-train.md).

## <a name="prepare-for-deployment"></a>Felkészülés az üzembe helyezésre
Ha lehetőséget szeretne adni másoknak az oktatóanyagban kifejlesztett prediktív modell használatára, webszolgáltatásként telepítheti az Azure-ban.

Eddig a pontig kísérleteztél a modellünk képzésével. De az üzembe helyezett szolgáltatás már nem fog csinálni a képzés - ez fog generálni új előrejelzések a felhasználó bemeneti a modell alapján. Szóval előkészítjük a kísérletet ***egy kiképzési*** kísérletből ***egy prediktív*** kísérletté. 

A telepítés előkészítése három lépésből áll:  

1. Az egyik modell eltávolítása
1. A létrehozott *betanítási kísérlet* átalakítása *prediktív kísérletté*
1. A prediktív kísérlet telepítése webszolgáltatásként

### <a name="remove-one-of-the-models"></a>Az egyik modell eltávolítása

Először is, meg kell vágni ezt a kísérletet egy kicsit. jelenleg két különböző modell a kísérletben, de csak akkor szeretné használni egy modellt, ha telepíti ezt a webszolgáltatásként.  

Tegyük fel, hogy úgy döntött, hogy a kiemelt fa modell jobban teljesített, mint az SVM modell. Tehát az első dolog, hogy távolítsa el a [két osztály támogatás vektor gép][two-class-support-vector-machine] modul és a modulokat, amelyeket a képzéshez használt. Előfordulhat, hogy először másolatot szeretne készíteni a kísérletről, ha a kísérletvászon alján a **Mentés másként** gombra kattint.

a következő modulokat kell törölnie:  

* [Kétosztályos támogató vektorgép][two-class-support-vector-machine]
* A hozzá csatlakoztatott [modell-][train-model] és [scoremodell-modulok][score-model] betanítása
* [Adatok normalizálása][normalize-data] (mindkettő)
* [Modell kiértékelése][evaluate-model] (mert befejeztük a modellek kiértékelését)

Jelölje ki az egyes modulokat, és nyomja le a Delete billentyűt, vagy kattintson a jobb gombbal a modulra, és válassza a **Törlés parancsot.** 

![Kiemeli, hogy mely modulokat kell törölni a Support Vector Machine modell eltávolításához](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

A modellünknek most valahogy így kell kinéznie:

![Az eredményül kapott kísérlet a Support Vector Machine modell törlésekor](./media/tutorial-part3-credit-risk-deploy/publish3.png)

Most már készen áll, hogy telepítse ezt a modellt a [kétosztályos súlyozott döntési fa.][two-class-boosted-decision-tree]

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>A betanítási kísérlet átalakítása prediktív kísérletté

Ahhoz, hogy ez a modell készen áll az üzembe helyezésre, ezt a betanítási kísérletet prediktív kísérletté kell konvertálnia. Ez három lépést foglal magában:

1. Mentse el a betanított modellt, majd cserélje ki az edzésmodulokat
1. Vágja le a kísérletet, hogy eltávolítsa azokat a modulokat, amelyek csak az edzéshez szükségesek
1. Annak meghatározása, hogy a webszolgáltatás hol fogadja el a bemenetet, és hol hozza létre a kimenetet

ezt manuálisan is megteheti, de szerencsére mindhárom lépés a **webszolgáltatás beállítása** gombra kattintva érhető el a kísérletvászon alján (és a **Prediktív webszolgáltatás** lehetőség kiválasztása).

> [!TIP]
> Ha további részleteket szeretne megtudni arról, hogy mi történik, amikor egy betanítási kísérletet prediktív kísérletté alakít át, olvassa [el a Modell előkészítése az Azure Machine Learning Studio (klasszikus) üzembe helyezéséhez című témakört.](convert-training-experiment-to-scoring-experiment.md)

Ha a **Webszolgáltatás beállítása**gombra kattint, több dolog is történhet:

* A betanított modell egyetlen **betanított modell** modullá alakul át, és a modulpalettán, a kísérletvászon bal oldalán tárolódik (a **betanított modellek**alatt található)
* A képzéshez használt modulokat eltávolítjuk; Különösen:
  * [Kétosztályos súlyozott döntési fa][two-class-boosted-decision-tree]
  * [Vonat modell][train-model]
  * [Adatok felosztása][split]
  * a tesztadatokhoz használt második [R-parancsfájl-végrehajtás][execute-r-script] modul
* A mentett betanított modell visszakerül a kísérletbe
* **Webszolgáltatás bemeneti** és **webszolgáltatás kimeneti** modulok egészül ki (ezek azonosítják, ahol a felhasználó adatai be a modell, és milyen adatokat ad vissza, amikor a webszolgáltatás elérése)

> [!NOTE]
> Láthatja, hogy a kísérlet két részre van mentve a kísérletvászon tetején hozzáadott lapok alatt. Az eredeti betanítási kísérlet a **Képzési kísérlet**fül alatt található , az újonnan létrehozott prediktív kísérlet pedig **a Prediktív kísérlet**alatt áll . A prediktív kísérlet az, amelyet webszolgáltatásként fog telepíteni.

még egy lépést kell tennie ezzel a kísérlettel.
két [R-parancsfájl-végrehajtás][execute-r-script] modult adott hozzá, hogy súlyozási funkciót biztosítson az adatokhoz. Ez csak egy trükk volt, amire szüksége volt a képzéshez és a teszteléshez, így kiveheti azokat a modulokat a végső modellbe.
Machine Learning Studio (klasszikus) eltávolított egy [Végrehajtási R Script][execute-r-script] modul, amikor eltávolította a [Split][split] modul. Most már eltávolíthatja a másikat, és a [Metaadat-szerkesztőt][metadata-editor] közvetlenül a Score Model -hez [csatlakoztathatja.][score-model]    

Kísérletünknek így kell kinéznie:  

![A betanított modell pontozása](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> Lehet, hogy vajon miért hagyta az UCI német hitelkártya-adatok adatkészleta a prediktív kísérlet. A szolgáltatás a felhasználó adatait fogja pontszámozni, nem az eredeti adatkészletet, akkor miért hagyja az eredeti adatkészletet a modellben?
> 
> Igaz, hogy a szolgáltatás nem kell az eredeti hitelkártya adatait. De szüksége van az adatok sémájára, amely olyan információkat tartalmaz, mint például, hogy hány oszlop van, és mely oszlopok numerikus. Ez a sémainformáció szükséges a felhasználó adatainak értelmezéséhez. ezeket az összetevőket csatlakoztatva hagyja, hogy a pontozási modul a szolgáltatás futásakor az adatkészletsémával rendelkezik. Az adatok nem használatosak, csak a séma.  
> 
>Az egyik fontos dolog megjegyezni, hogy ha az eredeti adatkészlet tartalmazta a címkét, akkor a várt séma a webes bemenetis vár egy oszlopot a címkét! Ez körül, hogy távolítsa el a címkét, és minden más adat, amely a betanítási adatkészletben, de nem lesz a webes bemenetek, mielőtt csatlakoztatja a webes bemeneti és betanítási adatkészlet egy közös modulhoz. 
> 

Futtassa a kísérletet még egyszer utoljára (kattintson a **Futtatás**gombra.) Ha ellenőrizni szeretné, hogy a modell még működik-e, kattintson a [Score Model][score-model] modul kimenetére, és válassza az **Eredmények megtekintése**lehetőséget. Láthatja, hogy az eredeti adatok megjelennek a hitelkockázati értékkel ("Pontozott címkék") és a pontozási valószínűségértékkel ("Pontozott valószínűség".) 

## <a name="deploy-the-web-service"></a>A webszolgáltatás üzembe helyezése
A kísérlet üzembe helyezheti akár egy klasszikus webszolgáltatás, vagy egy új webszolgáltatás, amely az Azure Resource Manager alapul.

### <a name="deploy-as-a-classic-web-service"></a>Üzembe helyezés klasszikus webszolgáltatásként
A kísérletünkből származó klasszikus webszolgáltatás üzembe helyezéséhez kattintson a vászon alatti **Webszolgáltatás telepítése** elemre, és válassza a **Webszolgáltatás telepítése [Klasszikus] lehetőséget.** A Machine Learning Studio (klasszikus) webszolgáltatásként telepíti a kísérletet, és az adott webszolgáltatás irányítópultjára viszi. Ezen az oldalon visszatérhet a kísérlethez **(Pillanatkép megtekintése** vagy **A legfrissebb megtekintése**) és futtathat egy egyszerű tesztet a webszolgáltatásról (lásd alább a **Webszolgáltatás tesztelése).** Van is információ itt létrehozására alkalmazások, amelyek hozzáférhetnek a webszolgáltatás (bővebben, hogy a következő lépésben ez az oktatóanyag).

![Webszolgáltatás irányítópultja](./media/tutorial-part3-credit-risk-deploy/publish6.png)


A szolgáltatást a **KONFIGURÁCIÓ** fülre kattintva állíthatja be. Itt módosíthatja a szolgáltatás nevét (alapértelmezés szerint megkapja a kísérlet nevét), és leírást adhat neki. A bemeneti és kimeneti adatokhoz barátságosabb címkéket is adhat.  

![A webszolgáltatás konfigurálása](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Üzembe helyezés új webszolgáltatásként

> [!NOTE] 
> Új webszolgáltatás üzembe helyezéséhez megfelelő engedélyekkel kell rendelkeznie abban az előfizetésben, amelyre a webszolgáltatást telepíti. További információt a [Webszolgáltatás kezelése az Azure Machine Learning Web Services portálon című témakörben talál.](manage-new-webservice.md) 

A kísérletünkből származó új webszolgáltatás üzembe helyezése:

1. Kattintson a **webszolgáltatás telepítése** a vászon alatt, és válassza **a Webszolgáltatás telepítése [Új]** lehetőséget. A Machine Learning Studio (klasszikus) átviszi az Azure Machine Learning webszolgáltatások **kísérleti telepítése** lapra.

1. Adja meg a webszolgáltatás nevét. 

1. Az **Árterv**ben választhat egy meglévő árképzési tervet, vagy válassza az "Új létrehozása" lehetőséget, és adjon nevet az új tervnek, és válassza a havi csomag lehetőséget. A csomagrétegek alapértelmezés szerint az alapértelmezett régió ra vonatkozó tervek, és a webszolgáltatás telepítve van az adott régióban.

1. Kattintson **a Telepítés gombra.**

Néhány perc múlva megnyílik a webszolgáltatás **gyorsútmutató** lapja.

A szolgáltatást a Beállítás fülre kattintva **állíthatja** be. Itt módosíthatja a szolgáltatás címét, és leírást adhat neki. 

A webszolgáltatás teszteléséhez kattintson a **Teszt** fülre (lásd alább **a Webszolgáltatás tesztelése** című témakört). A webszolgáltatáshoz hozzáférő alkalmazások létrehozásáról a **Felhasználás** fülre kattintva (az oktatóanyag következő lépése részletesebben ismerteti).

> [!TIP]
> A webszolgáltatás üzembe helyezése után frissítheti. Ha például módosítani szeretné a modellt, szerkesztheti a betanítási kísérletet, módosíthatja a modell paramétereit, majd a **Webszolgáltatás telepítése**gombra, a **Webszolgáltatás telepítése [Klasszikus]** vagy **a Webszolgáltatás telepítése [Új] parancsra**kattintva. Amikor újra telepíti a kísérletet, az felülírja a webszolgáltatást, most a frissített modell használatával.  
> 
> 

## <a name="test-the-web-service"></a>A webszolgáltatás tesztelése

A webszolgáltatás elérésekor a felhasználó adatai a **webszolgáltatás bemeneti** modulon keresztül jutnak el, ahol a [score modell][score-model] modulnak adják át, és pontozzák. A prediktív kísérlet beállításának módja, a modell az eredeti hitelkockázati adatkészlettel megegyező formátumú adatokat vár.
Az eredmények a webszolgáltatás **kimeneti** modulon keresztül jutnak vissza a felhasználónak.

> [!TIP]
> A prediktív kísérlet konfigurálásának módja, a [score modell][score-model] modul teljes eredményeit adja vissza. Ez magában foglalja az összes bemeneti adatok, valamint a hitelkockázati érték és a pontozási valószínűség. De akkor vissza valami mást, ha akarod - például, akkor vissza csak a hitelkockázati érték. Ehhez szúrjon be egy [Oszlopok kijelölése modult][select-columns] a [Score Model][score-model] és a **webszolgáltatás kimenete** közé, hogy megszüntesse azokat az oszlopokat, amelyeket nem szeretne, hogy a webszolgáltatás visszatérjen. 
> 
> 

A klasszikus webszolgáltatást a **Machine Learning Studio (klasszikus)** vagy az **Azure Machine Learning Web Services** portálon tesztelheti.
Az új webszolgáltatást csak a **Machine Learning webszolgáltatások** portálján tesztelheti.

> [!TIP]
> Az Azure Machine Learning Web Services portálon végzett tesztelés során beállíthatja, hogy a portál hozzon létre mintaadatokat, amelyek segítségével tesztelheti a kérelem-válasz szolgáltatást. A **Konfigurálás** lapon válassza az "Igen" lehetőséget **az Engedélyezve mintaadatokhoz?**. Amikor megnyitja a Kérelem-válasz lapot a **Teszt** lapon, a portál kitölti az eredeti hitelkockázati adatkészletből vett mintaadatokat.

### <a name="test-a-classic-web-service"></a>Klasszikus webszolgáltatás tesztelése

A klasszikus webszolgáltatást a Machine Learning Studio (klasszikus) vagy a Machine Learning Web Services portálon tesztelheti. 

#### <a name="test-in-machine-learning-studio-classic"></a>Teszt a Machine Learning Studióban (klasszikus)

1. A webszolgáltatás **DASHBOARD** lapján kattintson a **Teszt** gombra az **Alapértelmezett végpont csoportban**. Megjelenik egy párbeszédablak, amely a szolgáltatás bemeneti adatait kéri. Ezek ugyanazok az oszlopok, amelyek az eredeti hitelkockázati adatkészletben jelentek meg.  

1. Adja meg az adathalmazt, majd kattintson az **OK gombra.** 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Tesztelés a Machine Learning webszolgáltatások portálján

1. A webszolgáltatás **DASHBOARD** lapján kattintson a **Teszt előnézeti** hivatkozásra az **Alapértelmezett végpont csoportban.** Megnyílik az Azure Machine Learning Web Services-portál on the web service endpoint, és kéri a bemeneti adatokat a szolgáltatás. Ezek ugyanazok az oszlopok, amelyek az eredeti hitelkockázati adatkészletben jelentek meg.

2. Kattintson **a Tesztkérelem-válasz gombra.** 

### <a name="test-a-new-web-service"></a>Új webszolgáltatás tesztelése

Az új webszolgáltatást csak a Machine Learning webszolgáltatások portálján tesztelheti.

1. Az [Azure Machine Learning Web Services](https://services.azureml.net/quickstart) portálon kattintson a **teszt** a lap tetején. Megnyílik **a Teszt** lap, és adatokat adhat meg a szolgáltatáshoz. A megjelenített beviteli mezők megfelelnek az eredeti hitelkockázati adatkészletben megjelenő oszlopoknak. 

1. Adja meg az adatok készletét, majd kattintson a **Tesztkérelem-válasz gombra.**

A teszt eredményei az oldal jobb oldalán jelennek meg a kimeneti oszlopban. 


## <a name="manage-the-web-service"></a>A webszolgáltatás kezelése

Miután üzembe helyezte a webszolgáltatást, legyen az klasszikus vagy új, kezelheti azt a [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) portálon.

A webszolgáltatás teljesítményének figyelése:

1. Bejelentkezés a [Microsoft Azure Machine Learning webszolgáltatások](https://services.azureml.net/quickstart) portáljára
1. Kattintson **a Webservices elemre**
1. Kattintson a webszolgáltatásra
1. Kattintson az **irányítópultra**

## <a name="access-the-web-service"></a>Hozzáférés a webszolgáltatáshoz

Az oktatóanyag előző lépésében üzembe helyezett egy webszolgáltatást, amely a hitelkockázat-előrejelzési modellt használja. Most a felhasználók képesek adatokat küldeni, és eredményeket kapni. 

A webszolgáltatás egy Azure-webszolgáltatás, amely a REST API-k használatával kétféleképpen fogadhat és adhat vissza adatokat:  

* **Kérés/válasz** – A felhasználó egy vagy több sor kreditadatot küld a szolgáltatásnak egy HTTP protokoll használatával, és a szolgáltatás egy vagy több eredményhalmazsal válaszol.
* **Batch-végrehajtás** – A felhasználó egy vagy több sor kreditadatokat tárol egy Azure blobban, majd elküldi a blob helyét a szolgáltatásnak. A szolgáltatás a bemeneti blob ban lévő összes adatsort felszámítja, az eredményeket egy másik blobban tárolja, és a tároló URL-címét adja vissza.  

A webszolgáltatás eléréséről és felhasználásáról további információt az [Azure Machine Learning webszolgáltatás használata webalkalmazássablonnal című](/azure/machine-learning/studio/consume-web-services)témakörben talál.



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az alábbi lépéseket hajtotta végre:

> [!div class="checklist"]
> * Felkészülés az üzembe helyezésre
> * A webszolgáltatás üzembe helyezése
> * A webszolgáltatás tesztelése
> * A webszolgáltatás kezelése
> * Hozzáférés a webszolgáltatáshoz

Egyéni alkalmazást is fejleszthet a webszolgáltatás eléréséhez az R, C# és Python programozási nyelvekben megadott kezdőkód használatával.

> [!div class="nextstepaction"]
> [Azure Machine Learning webszolgáltatás felhasználása](consume-web-services.md)

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
