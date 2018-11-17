---
title: '5. lépés: A Machine Learning webszolgáltatás üzembe helyezése |} A Microsoft Docs'
description: 'A fejlesztés egy prediktív megoldás bemutatója 5. lépés: egy prediktív kísérletet a Machine Learning Studióban webszolgáltatásként üzembe helyezése.'
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.custom: (previous ms.author yahajiza)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 3fca74a3-c44b-4583-a218-c14c46ee5338
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 81375dd1a6321482b8d80ad248643fe9a98f8483
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51822415"
---
# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-web-service"></a>Az útmutató 5. lépése: Az Azure Machine Learning webszolgáltatás üzembe helyezése
Ez a forgatókönyv az minden ötödik lépés a [az Azure Machine Learning prediktív elemzési megoldás fejlesztése](walkthrough-develop-predictive-solution.md)

1. [Machine Learning-munkaterület létrehozása](walkthrough-1-create-ml-workspace.md)
2. [Meglévő adatok feltöltése](walkthrough-2-upload-data.md)
3. [Új kísérlet létrehozása](walkthrough-3-create-new-experiment.md)
4. [A modellek betanítása és kiértékelése](walkthrough-4-train-and-evaluate-models.md)
5. **A webszolgáltatás üzembe helyezése**
6. [Hozzáférés a webszolgáltatáshoz](walkthrough-6-access-web-service.md)

- - -
Biztosíthat mások is kidolgoztunk egy ebben az útmutatóban a prediktív modellt használja, azt is üzembe webszolgáltatásként, amely az Azure-ban.

Addig a pontig, hogy már megtörtént kísérleteztek az a modell betanítása. Azonban az üzemelő szolgáltatás már nem fogja képzési lehetőségek - fogja létrehozni új előrejelzések szerint a felhasználói bevitel alapján a modell pontozása. Tehát ez a kísérlet átalakítása néhány előkészítő tennie egy ***képzési*** kísérletezést egy ***prediktív*** kísérletezhet. 

Ez az egy három lépéses folyamat:  

1. Távolítsa el a következő modell valamelyikét
2. Konvertálja a *betanítási kísérlet* hoztunk létre, azokat egy *prediktív kísérletté*
3. A prediktív kísérletté üzembe webszolgáltatásként

## <a name="remove-one-of-the-models"></a>Távolítsa el a következő modell valamelyikét

Először létre kell ehhez a kísérlethez kissé le azokat. A kísérletben jelenleg van két különböző modell, de azt szeretné, hogy csak egy modell használatához, amikor ez webszolgáltatásként üzembe.  

Tegyük fel, hogy döntöttünk, hogy a gyorsított fa modell jobban teljesített, mint a SVM modell. Így az első teendő távolítsa el a [Kétosztályos támogató Vektorgép] [ two-class-support-vector-machine] modul, és az oktatási azt használt modulok. Érdemes először készítsen róla egy másolatot a kísérlet kattintva **Mentés másként** a kísérlet vászon alján.

A következő modulok törölni kell:  

* [Kétosztályos Tartóvektor-gép][two-class-support-vector-machine]
* [Modell betanításához] [ train-model] és [Score Model] [ score-model] ahhoz csatlakozó modulok
* [Adatok optimalizálása] [ normalize-data] (mindkettő)
* [Modell értékelése] [ evaluate-model] (mivel befejeződött, a modell kiértékelése)

Válassza ki az egyes modulok, és nyomja le a Delete billentyűt, vagy kattintson a jobb gombbal a modul, és válassza ki **törlése**. 

![Eltávolítja a SVM modell][3a]

A modell kell kinéznie ehhez hasonló:

![Eltávolítja a SVM modell][3]

Most már készen állunk a modell üzembe a [két osztályú súlyozott döntési fa][two-class-boosted-decision-tree].

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>A betanítási kísérlet átalakítása prediktív kísérletté

Ez a modell üzembe helyezés kész lekéréséhez a betanítási kísérlet átalakítása prediktív kísérletté kell. Ez a három lépésből áll:

1. Mentse a modellt, hogy adataink, és ezután cserélje le a képzési modulok
2. Trim a kísérletet, amely csak volt szükség a betanításhoz modulok eltávolítása
3. Ahol a webszolgáltatás bemeneti fogad, és ahol állít elő a kimeneti megadása

Javítanunk kell ezt manuálisan, de Szerencsére minden három lépésben végezhető el kattintva **webszolgáltatás beállítása** a kísérlet vászon alján (és kiválasztja a **prediktív webszolgáltatás** lehetőséget).

> [!TIP]
> Ha szeretne további részleteket a Mi történik, ha egy betanítási kísérlet átalakítása prediktív kísérletté, lásd: [hogyan készülhet fel a modell üzembe helyezése az Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

Amikor rákattint **webszolgáltatás beállítása**, több dolog történik:

* Egyetlen alakítja át a betanított modell **betanított modell** modul és a bal oldalán a kísérletvászonra a modulpaletta tárolt (annak a **betanított modellek**)
* A betanításhoz használt modulok törlődnek; pontosabban:
  * [Kétosztályos gyorsított döntési fa][two-class-boosted-decision-tree]
  * [Tanítási modell][train-model]
  * [Adatok felosztása][split]
  * a második [R-szkript végrehajtása] [ execute-r-script] modul, amely a Tesztadatok lett megadva
* A mentett betanított modell adnak vissza a kísérletbe
* **Webalkalmazás-bemenet** és **webes szolgáltatás kimeneti** modulok hozzáadásakor (ezek segítségével azonosíthatók a felhasználó adatait, adja meg a modellt, és milyen adatokat ad vissza, ha a webszolgáltatás)

> [!NOTE]
> Láthatja, hogy a kísérletet a kísérlet vászon tetején hozzáadott lapon szerepelnek, két részben menti. A lap alatt áll az eredeti betanítási kísérlet **betanítási kísérlet**, és alatt áll az újonnan létrehozott prediktív kísérletté **prediktív kísérletté**. A prediktív kísérletté lesz helyezünk üzembe webszolgáltatásként.

Ezzel a kísérlettel együtt egy további lépést is kell.
Hozzáadott két [R-szkript végrehajtása] [ execute-r-script] modulokat biztosítanak az adatok súlyozási függvény. Csak egy trükk volt szükségünk ahhoz, és tesztelésre, így igénybe ezeket a kész modell modulok ki volt.
A Machine Learning Studio eltávolítani egy [R-szkript végrehajtása] [ execute-r-script] modul eltávolításakor, a [Split] [ split] modul. Most azt az egyiket, és csatlakozzon [metaadat-szerkesztő] [ metadata-editor] közvetlenül [Score Model][score-model].    

A kísérlet kell kinéznie:  

![A betanított modell pontozása][4]  

> [!NOTE]
> Most felmerülhet Önben, miért érdemes a UCI német adatok adatkészlet azt left az a prediktív kísérletet. A szolgáltatás a felhasználó adatait, nem az eredeti adathalmazból pontszám, hogy miért hagyja meg az eredeti adatkészletre a modellben történik?
> 
> IGAZ, hogy a szolgáltatás nem kell az eredeti adatok. De azt a sémát, amely tartalmaz adatokat, például hogy hány oszlopok vannak, és mely oszlopok numerikus adatokat szükséges. Az adatbázisséma-információk szükség a felhasználói adatok értelmezése. Ezek az összetevők, hogy a pontozási modul az adathalmaz-séma rendelkezik, ha fut a szolgáltatás kapcsolódó hagyjuk. Az adatokat nem használja, csak a sémát.  
> 
>A rendszer egyik lényeges tudnivaló, hogy vegye figyelembe, hogy ha az eredeti adatkészlet a címkét, majd a várt séma a webes bemeneti fog is hatással vannak a címkével ellátott oszlop! Ezt úgy, hogy távolítsa el a címkét, és minden egyéb adatot, a betanítási adatkészletet volt, de mielőtt csatlakozna a webes bemeneti és a betanítási adatkészletet azokat egy közös modult a webes bemenetei között nem lesz. 
> 

Futtassa a kísérletet, egyszer utoljára (kattintson **futtatása**.) Ha szeretné ellenőrizni, hogy továbbra is működik-e a modell, kattintson a kimenetét a [Score Model] [ score-model] modul, és válassza ki **eredmények megtekintése**. Láthatja, hogy az eredeti adatok megjelenik-e, és a kredit kockázati érték ("pontozott címkék") és a pontozási valószínűségi érték ("pontozott valószínűség".) 

## <a name="deploy-the-web-service"></a>A webszolgáltatás üzembe helyezése
Telepítheti a kísérletet, vagy új webszolgáltatásként, amely az Azure Resource Manager-alapú vagy klasszikus webszolgáltatásként.

### <a name="deploy-as-a-classic-web-service"></a>Klasszikus webszolgáltatásként üzembe helyezése
Származtatott kísérletből a klasszikus webszolgáltatások üzembe helyezéséhez kattintson **webszolgáltatás üzembe helyezése** a vásznon, majd válassza alább **Web Service telepítése [klasszikus]**. A Machine Learning Studio helyez üzembe webszolgáltatásként, amely a kísérletet, és megnyílik az irányítópult webes szolgáltatás. Erről az oldalról visszatérhet a kísérlet (**nézet pillanatkép** vagy **legújabb megtekintése**) és a webszolgáltatás egyszerű teszt futtatása (lásd: **a webszolgáltatás teszteléséhez** alább). Információ az alkalmazások eléréséhez (bővebben a következő lépésben, a jelen próbaüzemben) webszolgáltatás létrehozásához itt is van.

![Webszolgáltatás irányítópultján][6]

A szolgáltatás kattintva konfigurálhatja a **konfigurációs** fülre. Itt módosíthatja a szolgáltatás nevét (Ez a példa adott a kísérlet neve alapértelmezés szerint), és adjon meg egy leírást. A bemeneti és kimeneti adatok további valódi címkéket is adhat.  

![A webszolgáltatás konfigurálása][5]  

### <a name="deploy-as-a-new-web-service"></a>Új webszolgáltatásként üzembe helyezése

> [!NOTE] 
> Egy új webszolgáltatás üzembe helyezése megfelelő engedélyekkel kell rendelkeznie az előfizetésben, amely a webszolgáltatást telepíti. További információkért lásd: [egy webszolgáltatás, az Azure Machine Learning Web Services portál használata kezelheti](manage-new-webservice.md). 

Egy új webszolgáltatás üzembe helyezéséhez származó a kísérlet:

1. Kattintson a **webszolgáltatás üzembe helyezése** a vásznon, majd válassza alább **[Új] Web Service telepítése**. A Machine Learning Studio továbbítja az Azure Machine Learning webszolgáltatások **kísérlet üzembe helyezése** lapot.

2. Adja meg a webszolgáltatás nevét. 

3. A **árképzési csomag**, egy meglévő tarifacsomagot választani, vagy válassza az "Új létrehozása", és nevezze el az új csomag és a havi csomag lehetőséget válassza. A terv szinten alapértelmezés szerint a csomagok alapértelmezett régiójához, és a webszolgáltatás telepítve van a régióban.

4. Kattintson a **üzembe helyezése**.

Néhány perc múlva a **rövid** megnyílik a webszolgáltatást.

A szolgáltatás kattintva konfigurálhatja a **konfigurálása** fülre. Itt módosíthatja a szolgáltatás cím, és adjon meg egy leírást. 

A webszolgáltatás teszteléséhez kattintson a **tesztelése** lapon (lásd: **a webszolgáltatás teszteléséhez** alább). Az alkalmazásokat, amelyek hozzáférhetnek a webszolgáltatás létrehozásával kapcsolatos információkért kattintson a **felhasználás** (a következő lépéssel, ez az útmutató részletesen lépnek) fülre.

> [!TIP]
> Miután telepítette, akkor frissítheti a web service. Például ha módosítani szeretné a modellt, majd szerkesztheti a tanítási kísérlet, a Teljesítménybeállítások a modell paramétereket, majd kattintson **webszolgáltatás üzembe helyezése**és **Web Service telepítése [klasszikus]** vagy **[Új] a Web Service szolgáltatásának telepítése**. Amikor újra üzembe a kísérletet, a webszolgáltatás, most már a frissített modell váltja fel.  
> 
> 

## <a name="test-the-web-service"></a>A webszolgáltatás teszteléséhez

A web service érhető el, amikor a felhasználó adatait keresztül kerül a **bemenet webes** modul, ahol felhasználáskor a [Score Model] [ score-model] modul és pontozunk. Beállítottuk a prediktív kísérletté módja, a modell adatait vár a eredeti kredit kockázati adatkészletként ugyanebben a formátumban.
Az eredményeket a rendszer adja vissza a felhasználónak keretében a web service a **webes szolgáltatás kimeneti** modul.

> [!TIP]
> A módszer van konfigurálva, a prediktív kísérletet a teljes eredménye a [Score Model] [ score-model] a modul adja vissza. Ez magában foglalja a bemeneti adatok plusz a kredit kockázati érték és a pontozási valószínűség. Azonban, térhet vissza egy másik, ha azt szeretné,-például csak a kredit kockázati érték eredményezhetnek. Ehhez beszúrása egy [Projektoszlopok] [ project-columns] közötti modul [Score Model] [ score-model] és a **webes szolgáltatás kimeneti**kiküszöbölése az oszlopok nem szeretné a web service való visszatéréshez. 
> 
> 

Klasszikus webes tesztelheti a szolgáltatást **Machine Learning Studio** vagy a a **Azure Machine Learning webszolgáltatások** portálon.
Tesztelheti egy új webes szolgáltatás csak a **Machine Learning webszolgáltatások** portálon.

> [!TIP]
> Ha teszteli az Azure Machine Learning Web Services portálon, akkor is, amellyel tesztelheti a kérés-válasz szolgáltatás mintaadatok létrehozása a portálon. Az a **konfigurálása** lapra, jelölje be az "Igen" a **minta adatok engedélyezve van?**. Ha a kérés-válasz lapjának megnyitása a **teszt** , a portál ekkor kitölti a mintaadatokat az eredeti kredit kockázati adathalmazból származó.

### <a name="test-a-classic-web-service"></a>Klasszikus webszolgáltatás teszteléséhez

Klasszikus webszolgáltatás tesztelheti a Machine Learning Studióban, vagy a Machine Learning Web Services portálon. 

#### <a name="test-in-machine-learning-studio"></a>A Machine Learning Studióban tesztelése

1. Az a **IRÁNYÍTÓPULT** a web service oldalára, kattintson a **teszt** gomb alatt **alapértelmezett végpont**. Egy párbeszédpanel megjelenik, és kéri a bemeneti adatokat a szolgáltatás. Ezek a ugyanazokat az oszlopokat, amelyek az eredeti kredit kockázati adatkészlet jelent meg.  

2. Adjon meg egy adatkészletet, és kattintson a **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Tesztelése a Machine Learning Web Services portálon

1. Az a **IRÁNYÍTÓPULT** a web service oldalára, kattintson a **teszt előzetes** mellett kapcsolni **alapértelmezett végpont**. A tesztlap az Azure Machine Learning Web Services portálon a web service-végpont megnyílik, és kéri a bemeneti adatokat a szolgáltatáshoz. Ezek a ugyanazokat az oszlopokat, amelyek az eredeti kredit kockázati adatkészlet jelent meg.

2. Kattintson a **kérés-válasz tesztelése**. 

### <a name="test-a-new-web-service"></a>Új webszolgáltatásként tesztelje

Csak a Machine Learning Web Services portálon tesztelheti egy új webszolgáltatásként.

1. Az a [Azure Machine Learning webszolgáltatások](https://services.azureml.net/quickstart) portálon kattintson **teszt** az oldal tetején. A **teszt** lap nyílik meg, és megadhatja, hogy a szolgáltatás adatokat. A beviteli mezőket jelenít meg, amely az eredeti kredit kockázati adatkészlet jelent meg az oszlopok felelnek meg. 

2. Adjon meg egy adatkészletet, és kattintson a **tesztelési kérelem-válasz**.

A teszt eredményei a kimeneti oszlopban az oldal jobb oldalán jelennek meg. 


## <a name="manage-the-web-service"></a>A webszolgáltatás kezelése

Miután üzembe helyezte a webszolgáltatást, akár klasszikus, akár új, hogy kezelhető a [a Microsoft Azure Machine Learning webszolgáltatások](https://services.azureml.net/quickstart) portálon.

A webszolgáltatás teljesítményének figyelésére:

1. Jelentkezzen be a [a Microsoft Azure Machine Learning webszolgáltatások](https://services.azureml.net/quickstart) portál
2. Kattintson a **webszolgáltatások**
3. Kattintson a webszolgáltatás
4. Kattintson a **irányítópult**

- - -
**A következő: [hozzáférés a webszolgáltatáshoz](walkthrough-6-access-web-service.md)**

[3]: ./media/walkthrough-5-publish-web-service/publish3.png
[3a]: ./media/walkthrough-5-publish-web-service/publish3a.png
[4]: ./media/walkthrough-5-publish-web-service/publish4.png
[5]: ./media/walkthrough-5-publish-web-service/publish5.png
[6]: ./media/walkthrough-5-publish-web-service/publish6.png


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
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
