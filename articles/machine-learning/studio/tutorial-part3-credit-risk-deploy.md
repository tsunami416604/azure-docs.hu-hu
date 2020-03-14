---
title: '3\. Oktatóanyag: hitelkockázat-modell üzembe helyezése'
titleSuffix: Azure Machine Learning Studio (classic)
description: Részletes oktatóanyag, amely bemutatja, hogyan hozhat létre a hitelkockázat értékelésére szolgáló prediktív elemzési megoldást Azure Machine Learning Studio (klasszikus). Ez az oktatóanyag egy háromrészes oktatóanyag-sorozat harmadik része. Bemutatja, hogyan helyezhet üzembe egy modellt webszolgáltatásként.
keywords: hitelkockázat, prediktív elemzési megoldás, kockázatértékelés, üzembe helyezés, webszolgáltatás
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 9fb0b59374edf322e5e2221b90e912ee2c665bac
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204153"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio-classic"></a>3\. Oktatóanyag: kredit kockázati modell üzembe helyezése – Azure Machine Learning Studio (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Ez az oktatóanyag részletesen bemutatja a prediktív elemzési megoldások fejlesztésének folyamatát. Egy egyszerű modellt fejleszt Machine Learning Studio (klasszikus).  Ezután Azure Machine Learning webszolgáltatásként helyezheti üzembe a modellt.  Ez a központilag telepített modell új adatelemzést végezhet az előrejelzésekhez. Ez az oktatóanyag **egy háromrészes oktatóanyag-sorozat harmadik**része.

Tegyük fel, hogy előrejelzést kell készíteni egy személy hitelkockázatáról az általa kitöltött hitelkérelemben megadott adatok alapján.  

A hitelkockázat-értékelés összetett probléma, de ez az oktatóanyag egy kicsit egyszerűbbé válik. Példaként fogja használni, hogyan hozhat létre prediktív elemzési megoldást Microsoft Azure Machine Learning Studio (klasszikus) használatával. Ehhez a megoldáshoz Azure Machine Learning Studio (klasszikus) és egy Machine Learning webszolgáltatást fog használni. 

Ebben a három részből álló oktatóanyagban nyilvánosan elérhető hitelkockázat-adatforrásokkal kezdheti meg.  Ezután fejlesztheti és betaníthatja a prediktív modellt.  Végül üzembe helyezi a modellt webszolgáltatásként.

[Az oktatóanyag első részében](tutorial-part1-credit-risk.md)létrehozott egy Machine learning Studio (klasszikus) munkaterületet, feltöltött és létrehozott egy kísérletet.

[Az oktatóanyag második részében](tutorial-part2-credit-risk-train.md)betanított és kiértékelt modelleket.

Az oktatóanyag ezen részében:

> [!div class="checklist"]
> * Üzembe helyezés előkészítése
> * A webszolgáltatás üzembe helyezése
> * A webszolgáltatás teszteléséhez
> * A webszolgáltatás kezelése
> * Hozzáférés a webszolgáltatáshoz

## <a name="prerequisites"></a>Előfeltételek

Fejezze be [az oktatóanyag második részét](tutorial-part2-credit-risk-train.md).

## <a name="prepare-for-deployment"></a>Üzembe helyezés előkészítése
Annak érdekében, hogy a jelen oktatóanyagban fejlesztett prediktív modellt az Azure-ban, webszolgáltatásként is üzembe helyezheti.

Ez a pont már kísérletezett a modell betanításával. Azonban az üzemelő szolgáltatás már nem fogja képzési lehetőségek - fogja létrehozni új előrejelzések szerint a felhasználói bevitel alapján a modell pontozása. Ezért elvégezünk némi felkészülést, hogy a kísérletet egy ***betanítási*** kísérletből egy ***prediktív*** kísérletbe alakítsa át. 

Az üzembe helyezés előkészítése egy három lépésből álló folyamat:  

1. Távolítsa el a következő modell valamelyikét
1. A létrehozott *betanítási kísérlet* átalakítása *prediktív kísérletbe*
1. A prediktív kísérletté üzembe webszolgáltatásként

### <a name="remove-one-of-the-models"></a>Távolítsa el a következő modell valamelyikét

Először el kell távolítania ezt a kísérletet egy kicsit. Jelenleg két különböző modellel rendelkezik a kísérletben, de csak egyetlen modellt szeretne használni, amikor webszolgáltatásként telepíti ezt.  

Tegyük fel, hogy úgy döntött, hogy a felerősített famodell jobban teljesített, mint a SVM modell. Ezért először távolítsa el a [kétosztályos támogatású vektoros gépi][two-class-support-vector-machine] modult és a képzéshez használt modulokat. Először is érdemes lehet másolatot készíteni a kísérletről a kísérlet vászon alján a **Mentés másként** lehetőségre kattintva.

törölnie kell a következő modulokat:  

* [Kétosztályos támogatású vektoros gép][two-class-support-vector-machine]
* A [betanítási modell][train-model] és a [pontszám modellhez][score-model] kapcsolódó modulok
* [Az Adatnormalizálás][normalize-data] (mindkettő)
* [Modell kiértékelése][evaluate-model] (mivel elkészült a modellek kiértékelésével)

Válassza ki az egyes modulokat, és nyomja le a DELETE billentyűt, vagy kattintson a jobb gombbal a modulra, és válassza a **Törlés**lehetőséget. 

![Kiemeli, hogy mely modulok törlődjenek a támogatási vektoros gép modell eltávolításához](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

A modell kell kinéznie ehhez hasonló:

![Eredményül kapott kísérlet a támogatási vektoros gép modelljének törlésekor](./media/tutorial-part3-credit-risk-deploy/publish3.png)

Most már készen áll a modell üzembe helyezésére a [kétosztályos felerősítő döntési fában][two-class-boosted-decision-tree].

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>A betanítási kísérlet átalakítása prediktív kísérletté

Ahhoz, hogy ez a modell készen álljon a telepítésre, át kell alakítania a betanítási kísérletet egy prediktív kísérletbe. Ez a három lépésből áll:

1. Mentse a betanított modellt, majd cserélje ki a betanítási modulokat
1. Trim a kísérletet, amely csak volt szükség a betanításhoz modulok eltávolítása
1. Ahol a webszolgáltatás bemeneti fogad, és ahol állít elő a kimeneti megadása

Ezt manuálisan is megteheti, de szerencsére mind a három lépés elvégezhető a kísérlet vászon alján található **webszolgáltatás beállítása** elemre kattintva (és a **prediktív webszolgáltatás** lehetőség kiválasztásával).

> [!TIP]
> Ha többet szeretne megtudni arról, hogy mi történik, ha egy képzési kísérletet prediktív kísérletre alakít át, tekintse meg a [modell előkészítése az üzembe helyezéshez Azure Machine learning Studio (klasszikus)](convert-training-experiment-to-scoring-experiment.md)című témakört.

Ha a **webszolgáltatás beállítása**gombra kattint, több dolog történik:

* A betanított modellt egyetlen **betanított modell** -modulra konvertáljuk, és a modul palettáján, a kísérlet vászontól balra tároljuk (a **betanított**modellekben található)
* A betanításhoz használt modulok törlődnek; pontosabban:
  * [Kétosztályos, megnövelt döntési fa][two-class-boosted-decision-tree]
  * [Betanítási modell][train-model]
  * [Adatfelosztás][split]
  * az adatteszteléshez használt második [R-parancsfájl][execute-r-script] -modul
* A mentett betanított modell adnak vissza a kísérletbe
* **Webszolgáltatások bemeneti** és **webszolgáltatás-kimeneti** moduljai (ezek azonosítják, hogy a felhasználó mely adatokat írja be a modellbe, és milyen adatokat ad vissza a rendszer a webszolgáltatás elérésekor)

> [!NOTE]
> Láthatja, hogy a kísérletet a kísérlet vászon tetején hozzáadott lapon szerepelnek, két részben menti. Az eredeti betanítási kísérlet a lap **betanítási kísérlete**alatt áll, az újonnan létrehozott prediktív kísérlet pedig **prediktív kísérlet**. A prediktív kísérlet egy webszolgáltatásként üzembe helyezhető.

Ehhez az adott kísérlethez még egy lépést kell elvégeznie.
két [végrehajtási R parancsfájl][execute-r-script] -modult adott hozzá, amelyek súlyozási funkciót biztosítanak az adathoz. Ez csak egy, a képzéshez és a teszteléshez szükséges trükk volt, így az utolsó modellben is elvégezheti ezeket a modulokat.
Machine Learning Studio (klasszikus) eltávolított egy [Execute R script][execute-r-script] modult, amikor eltávolította a [felosztott][split] modult. Most már eltávolíthatja a másikat, és közvetlenül is összekapcsolhat [metaadat-szerkesztőt][metadata-editor] a [pontszám modellhez][score-model].    

A kísérlet kell kinéznie:  

![A betanított modell pontozása](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> Előfordulhat, hogy kíváncsi, hogy miért hagyta el az UCI német hitelkártya-adatkészletet a prediktív kísérlet során. A szolgáltatás a felhasználó adatait, nem az eredeti adathalmazból pontszám, hogy miért hagyja meg az eredeti adatkészletre a modellben történik?
> 
> IGAZ, hogy a szolgáltatás nem kell az eredeti adatok. De azt a sémát, amely tartalmaz adatokat, például hogy hány oszlopok vannak, és mely oszlopok numerikus adatokat szükséges. Az adatbázisséma-információk szükség a felhasználói adatok értelmezése. ezeket az összetevőket nem kell csatlakoztatni, hogy a pontozási modul a szolgáltatás futása során a DataSet sémával rendelkezik. Az adatokat nem használja, csak a sémát.  
> 
>A rendszer egyik lényeges tudnivaló, hogy vegye figyelembe, hogy ha az eredeti adatkészlet a címkét, majd a várt séma a webes bemeneti fog is hatással vannak a címkével ellátott oszlop! Ezt úgy, hogy távolítsa el a címkét, és minden egyéb adatot, a betanítási adatkészletet volt, de mielőtt csatlakozna a webes bemeneti és a betanítási adatkészletet azokat egy közös modult a webes bemenetei között nem lesz. 
> 

A kísérlet utolsó futtatása (kattintson a **Futtatás**gombra.) Ha szeretné ellenőrizni, hogy a modell továbbra is működik-e, kattintson a [pontszám modell][score-model] modul kimenetére, és válassza az **eredmények megtekintése**lehetőséget. Láthatja, hogy az eredeti adatok megjelenik-e, és a kredit kockázati érték ("pontozott címkék") és a pontozási valószínűségi érték ("pontozott valószínűség".) 

## <a name="deploy-the-web-service"></a>A webszolgáltatás üzembe helyezése
Telepítheti a kísérletet, vagy új webszolgáltatásként, amely az Azure Resource Manager-alapú vagy klasszikus webszolgáltatásként.

### <a name="deploy-as-a-classic-web-service"></a>Klasszikus webszolgáltatásként üzembe helyezése
A kísérletből származtatott klasszikus webszolgáltatás üzembe helyezéséhez kattintson az **webszolgáltatás üzembe helyezése** a vászon alatt lehetőségre, majd válassza a **webszolgáltatás telepítése [klasszikus]** lehetőséget. Machine Learning Studio (klasszikus) a kísérletet webszolgáltatásként helyezi üzembe, és az adott webszolgáltatás irányítópultján viszi át. Ezen a lapon visszatérhet a kísérlethez (**Pillanatkép megtekintése** vagy **legutóbbi megtekintése**), és futtathatja a webszolgáltatás egyszerű tesztjét (lásd az alábbi **webszolgáltatások tesztelését** ). Itt tájékozódhat a webszolgáltatáshoz hozzáférő alkalmazások létrehozásáról is (erről bővebben az oktatóanyag következő lépésében talál).

![Webszolgáltatás irányítópultján](./media/tutorial-part3-credit-risk-deploy/publish6.png)


A szolgáltatás konfigurálásához kattintson a **konfiguráció** fülre. Itt módosíthatja a szolgáltatás nevét (alapértelmezés szerint a kísérlet nevét adja meg), és leírást adhat hozzá. A bemeneti és kimeneti adatok további valódi címkéket is adhat.  

![A webszolgáltatás konfigurálása](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Új webszolgáltatásként üzembe helyezése

> [!NOTE] 
> Egy új webszolgáltatás üzembe helyezése megfelelő engedélyekkel kell rendelkeznie az előfizetésben, amely a webszolgáltatást telepíti. További információ: [webszolgáltatások kezelése a Azure Machine learning webszolgáltatások portálján](manage-new-webservice.md). 

Egy új webszolgáltatás üzembe helyezéséhez származó a kísérlet:

1. Kattintson a **webszolgáltatások üzembe helyezése** a vászon alatt lehetőségre, majd válassza a **webszolgáltatás üzembe helyezése [új]** lehetőséget. Machine Learning Studio (klasszikus) átviszi Önt a Azure Machine Learning webszolgáltatások **üzembe helyezési kísérlet** lapjára.

1. Adja meg a webszolgáltatás nevét. 

1. A díjszabáshoz kiválaszthat egy meglévő árképzési csomagot, vagy kiválaszthatja az "új létrehozása" **lehetőséget, és**megadhatja az új terv nevét, és kiválaszthatja a havi csomag beállítást. A terv szinten alapértelmezés szerint a csomagok alapértelmezett régiójához, és a webszolgáltatás telepítve van a régióban.

1. Kattintson a **telepítés**elemre.

Néhány perc elteltével megnyílik a webszolgáltatás rövid **útmutató lapja.**

A szolgáltatás konfigurálásához kattintson a configure ( **Konfigurálás** ) fülre. Itt módosíthatja a szolgáltatás címét, és leírást adhat hozzá. 

A webszolgáltatás teszteléséhez kattintson a **teszt** fülre (lásd az alábbi **webszolgáltatás tesztelését** ). A webszolgáltatáshoz hozzáférő alkalmazások létrehozásával kapcsolatos információkért kattintson a felhasználás **lapra (az oktatóanyag** következő lépése részletesebben jelenik meg).

> [!TIP]
> Miután telepítette, akkor frissítheti a web service. Ha például módosítani szeretné a modellt, akkor szerkesztheti a betanítási kísérletet, megkeresheti a modell paramétereit, majd kattintson a webszolgáltatás **telepítése**lehetőségre, válassza a **webszolgáltatás telepítése [klasszikus]** vagy a **webszolgáltatás telepítése [új]** lehetőséget. Amikor újra üzembe a kísérletet, a webszolgáltatás, most már a frissített modell váltja fel.  
> 
> 

## <a name="test-the-web-service"></a>A webszolgáltatás teszteléséhez

A webszolgáltatáshoz való hozzáféréskor a felhasználó **adatbevitele a webszolgáltatás bemeneti** modulján keresztül történik, ahol a rendszer átadja a [pontszám modell][score-model] modulnak, és lelőtte azt. A prediktív kísérlet beállításának módja, a modell az eredeti hitelkockázat-adatkészlettel megegyező formátumban várja az adatokat.
Az eredményeket a webszolgáltatás **kimeneti** modulján keresztül adja vissza a rendszer a webszolgáltatásnak.

> [!TIP]
> Ahogy a prediktív kísérlet konfigurálva van, a [pontszám modell][score-model] modul teljes eredményét adja vissza. Ez magában foglalja a bemeneti adatok plusz a kredit kockázati érték és a pontozási valószínűség. Azonban, térhet vissza egy másik, ha azt szeretné,-például csak a kredit kockázati érték eredményezhetnek. Ehhez helyezzen be egy [Select Columns][select-columns] modult a [score Model][score-model] és a **webszolgáltatás kimenete** között, hogy eltávolítsa azokat az oszlopokat, amelyeket nem kíván visszaadni a webszolgáltatásnak. 
> 
> 

A klasszikus webszolgáltatás **Machine learning Studio (klasszikus)** vagy a **Azure Machine learning webszolgáltatások** portálján is tesztelhető.
Egy új webszolgáltatást csak a **Machine learning webszolgáltatások** portálon lehet tesztelni.

> [!TIP]
> Ha teszteli az Azure Machine Learning Web Services portálon, akkor is, amellyel tesztelheti a kérés-válasz szolgáltatás mintaadatok létrehozása a portálon. A **configure (Konfigurálás** ) lapon válassza az "igen" lehetőséget a **mintaadatok engedélyezéséhez?** . Amikor megnyitja a kérelem-válasz fület a **teszt** lapon, a portál kitölti az eredeti hitelkockázat-adatkészletből vett mintaadatok adatait.

### <a name="test-a-classic-web-service"></a>Klasszikus webszolgáltatás teszteléséhez

A klasszikus webszolgáltatások tesztelését Machine Learning Studio (klasszikus) vagy a Machine Learning webszolgáltatások portálján végezheti el. 

#### <a name="test-in-machine-learning-studio-classic"></a>Tesztelés Machine Learning Studio (klasszikus)

1. A webszolgáltatás **irányítópult** lapján kattintson a **teszt** gombra az **alapértelmezett végpont**alatt. Egy párbeszédpanel megjelenik, és kéri a bemeneti adatokat a szolgáltatás. Ezek a ugyanazokat az oszlopokat, amelyek az eredeti kredit kockázati adatkészlet jelent meg.  

1. Adjon meg egy adatkészletet, majd kattintson **az OK**gombra. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Tesztelése a Machine Learning Web Services portálon

1. A webszolgáltatáshoz tartozó **irányítópult** lapon kattintson az **alapértelmezett végpont**alatti **teszt előnézet** hivatkozásra. A tesztlap az Azure Machine Learning Web Services portálon a web service-végpont megnyílik, és kéri a bemeneti adatokat a szolgáltatáshoz. Ezek a ugyanazokat az oszlopokat, amelyek az eredeti kredit kockázati adatkészlet jelent meg.

2. Kattintson a **kérelem tesztelése – válasz**elemre. 

### <a name="test-a-new-web-service"></a>Új webszolgáltatásként tesztelje

Csak a Machine Learning Web Services portálon tesztelheti egy új webszolgáltatásként.

1. A [Azure Machine learning webszolgáltatások](https://services.azureml.net/quickstart) portálján kattintson a **teszt** gombra az oldal tetején. Megnyílik a **teszt** lap, és beírhat adatokat a szolgáltatáshoz. A beviteli mezőket jelenít meg, amely az eredeti kredit kockázati adatkészlet jelent meg az oszlopok felelnek meg. 

1. Adjon meg egy adatkészletet, majd kattintson a **kérelem tesztelése – válasz**elemre.

A teszt eredményei a kimeneti oszlopban az oldal jobb oldalán jelennek meg. 


## <a name="manage-the-web-service"></a>A webszolgáltatás kezelése

Miután telepítette a webszolgáltatást, akár klasszikus, akár új, felügyelheti azt a [Microsoft Azure Machine learning webszolgáltatások](https://services.azureml.net/quickstart) portálján.

A webszolgáltatás teljesítményének figyelésére:

1. Bejelentkezés a [Microsoft Azure Machine learning Web Services](https://services.azureml.net/quickstart) portálra
1. Kattintson a **webszolgáltatások** elemre.
1. Kattintson a webszolgáltatás
1. Kattintson az **irányítópultra**

## <a name="access-the-web-service"></a>Hozzáférés a webszolgáltatáshoz

Az oktatóanyag előző lépésében üzembe helyezett egy webszolgáltatást, amely a hitelkockázat-előrejelzési modellt használja. Most, a felhasználók adatokat küldeni és fogadni az eredményeket. 

A Web service egy Azure-alapú webes szolgáltatás, amely képes fogadni, és REST API-k használatával a két módszer egyikével adatokat adja vissza:  

* **Kérés/válasz** – a felhasználó egy vagy több hitelkeretet küld a szolgáltatásnak egy HTTP protokoll használatával, és a szolgáltatás egy vagy több eredménnyel válaszol.
* **Kötegelt végrehajtás** – a felhasználó egy vagy több jóváírási adatokat tárol egy Azure-blobban, majd elküldi a blob helyét a szolgáltatásnak. A szolgáltatás az adatok a sorok pontszámmodell a bemeneti BLOB tárolja az eredményeket egy másik blob és URL-címét, hogy a tároló adja vissza.  

További információ a webszolgáltatás eléréséről és használatáról: [Azure Machine learning webszolgáltatások felhasználása webalkalmazás-sablonnal](/azure/machine-learning/studio/consume-web-services).



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következő lépéseket végezte el:

> [!div class="checklist"]
> * Üzembe helyezés előkészítése
> * A webszolgáltatás üzembe helyezése
> * A webszolgáltatás teszteléséhez
> * A webszolgáltatás kezelése
> * Hozzáférés a webszolgáltatáshoz

Hozzáférés a webszolgáltatáshoz, R, a megadott alapszintű kód segítségével egyéni alkalmazásokat is fejleszthet C#, és a Python programozási nyelveket.

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
