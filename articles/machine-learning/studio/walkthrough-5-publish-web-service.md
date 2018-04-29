---
title: '5. lépés: A Machine Learning webszolgáltatás üzembe helyezése |} Microsoft Docs'
description: 'A Develop egy prediktív megoldás útmutató 5. lépés: központi telepítése egy prediktív kísérletben a Machine Learning Studióban webszolgáltatásként.'
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 3fca74a3-c44b-4583-a218-c14c46ee5338
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 6472b795c9b3651160af4d1520bfd1c90a500b5f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-web-service"></a>Az útmutató 5. lépése: Az Azure Machine Learning webszolgáltatás üzembe helyezése
Ez a forgatókönyv ötödik lépését az [az Azure Machine Learning a prediktív elemzési megoldás fejlesztése](walkthrough-develop-predictive-solution.md)

1. [Machine Learning-munkaterület létrehozása](walkthrough-1-create-ml-workspace.md)
2. [Meglévő adatok feltöltése](walkthrough-2-upload-data.md)
3. [Új kísérlet létrehozása](walkthrough-3-create-new-experiment.md)
4. [A modellek betanítása és kiértékelése](walkthrough-4-train-and-evaluate-models.md)
5. **A webszolgáltatás üzembe helyezése**
6. [Hozzáférés a webszolgáltatáshoz](walkthrough-6-access-web-service.md)

- - -
Mások arra, hogy a forgatókönyv során korábban kidolgoztunk a prediktív modellt használja, biztosítva az Azure-on webszolgáltatásként telepíthetünk azt.

A pontig azt korábban már ki a modell betanítása. Azonban az üzemelő szolgáltatás már nem lesz betanítási ehhez,-új előrejelzéseket előállításához a modell alapján a felhasználói bevitel pontozási által lesz. Bizonyos előkészítés konvertálni a kísérlet az ehhez az oktatóanyagban módosítjuk, egy ***képzési*** való egy ***prediktív*** kipróbálásához. 

Ez az egy három lépéses folyamat:  

1. Távolítsa el az egyik modell
2. Alakítsa át a *tanítási kísérletet* történő létrehoztunk Önnek egy *prediktív kísérletté*
3. A prediktív kísérletté egy webszolgáltatás-bővítmény telepítése

## <a name="remove-one-of-the-models"></a>Távolítsa el az egyik modell

Először igazolnia kell a kísérletben kissé trim le. A kísérletben jelenleg van két különböző modell, de azt csak egy modell azt telepítheti az webszolgáltatásként használni szeretne.  

Tegyük fel, hogy döntött, hogy a súlyozott fa modell jobban teljesített, mint a SVM modell. Így az első lépés, távolítsa el a [két osztályú támogatást vektoros gép] [ two-class-support-vector-machine] modul és a képzési azt használt modulok. Érdemes lehet másolatot készít a kísérlet először kattintva **Mentés másként** a kísérletvászonra alján.

A következő modulok törölni kell:  

* [Két osztályú támogatást vektoros gép][two-class-support-vector-machine]
* [Modell betanításához] [ train-model] és [Score Model] [ score-model] hozzá csatlakozó modulok
* [Adatok normalizálása] [ normalize-data] (mindkettő)
* [Értékelje ki a modell] [ evaluate-model] (mert a rendszer befejezte a modell kiértékelése)

Minden modul és a Delete billentyűt, vagy kattintson a jobb gombbal a modult és válasszon **törlése**. 

![Eltávolítja a SVM modell][3a]

A modell most hasonlóan kell kinéznie ezt:

![Eltávolítja a SVM modell][3]

Most már készen a modell szabályzatsablonokat a [két osztályú súlyozott döntési fa][two-class-boosted-decision-tree].

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>A tanítási kísérletet átalakítása egy prediktív kísérletté

Ez a modell Felkészülés a központi telepítés, a tanítási kísérletet átalakítása egy prediktív kísérletté kell. Ez a három lépést foglal magában:

1. Mentse a modellt, hogy be van tanítva, és akkor cserélje le a képzési modulok
2. A kísérletet, és távolítsa el a képzés csak szükséges modulokat vágása
3. Adja meg, ahol a webszolgáltatás bemenetet fogad, és ahol a kimeneti generál

Azt megteheti ezt manuálisan, de Szerencsére minden három lépést is elvégezhető kattintva **webes szolgáltatások beállítása** a kísérletvászonra alján (, majd válassza a **prediktív webszolgáltatás** lehetőséget).

> [!TIP]
> Ha további adatokra van szüksége a Mi történik, ha egy tanítási kísérletet konvertál egy prediktív kísérletté című [a modell előkészítése az Azure Machine Learning Studióban telepítési](convert-training-experiment-to-scoring-experiment.md).

Amikor rákattint **webes szolgáltatások beállítása**, események történnek:

* A betanított modell alakítja át egyetlen **Trained Model** modul és a modulpalettán bal oldalán a kísérletvászonra tárolt (található e **betanított modellek**)
* A betanításhoz használt modulok eltávolításuk; pontosabban:
  * [Két osztályú súlyozott döntési fája][two-class-boosted-decision-tree]
  * [Tanítási modell][train-model]
  * [Adatok][split]
  * a második [R-parancsfájl végrehajtása] [ execute-r-script] a Tesztadatok használt modul
* A mentett betanított modell kerül be a kísérlet
* **Webalkalmazás-szolgáltatás bemeneti** és **webes szolgáltatás kimeneti** modulokat adja hozzá a (ezek segítségével azonosíthatók a felhasználói adatok hol adja meg a modell, és milyen adatokat ad vissza, ha hozzáfér a webes szolgáltatás)

> [!NOTE]
> Láthatja, hogy a kísérletet a kísérlet vászon tetején hozzáadott lapon szerepelnek két részből menti. Az eredeti tanítási kísérletet van a lapon **tanítási kísérletet**, és az újonnan létrehozott prediktív kísérletté alatt áll **prediktív kísérletté**. A prediktív kísérletté, a webszolgáltatásként üzembe azt egy.

Az ehhez a kísérlethez egy további lépéssel kell.
Két hozzáadott [R-parancsfájl végrehajtása] [ execute-r-script] modulok arra, hogy az adatok súlyozási függvényt. Csak a képzési és a teszteléshez, hogy változtatnunk körben, amely volt, azt is vegye ki azokat a végső modell modulokat.
A Machine Learning Studio eltávolítani egy [R-parancsfájl végrehajtása] [ execute-r-script] modul eltávolításakor azt a [vegyes] [ split] modul. Távolítsa el a másik és csatlakozás is [metaadat-szerkesztő] [ metadata-editor] közvetlenül [Score Model][score-model].    

A kísérletben most példához hasonló:  

![A betanított modell pontozása][4]  

> [!NOTE]
> Előfordulhat, hogy lehet szeretné megtudni, hogy miért azt a UCI német hitelkártya adatok dataset maradnak a prediktív kísérletté. A szolgáltatás a felhasználó adatait, nem az eredeti adathalmazból pontozása, így miért hagyja meg az eredeti adathalmazból a modell lesz?
> 
> IGAZ, hogy a szolgáltatás nem kell az eredeti hitelkártya-adatokhoz. Azonban az adatok, amely tartalmaz információkat, például hogy hány oszlopok vannak, és mely oszlopok numerikus azt kell a sémát. A sémaadatok szükség a felhasználói adatok értelmezése. Ezeket az összetevőket úgy, hogy a pontozási modul az adathalmaz-sémát a szolgáltatás futása közben csatlakozik hagyja azt. Az adatok nem használ, csak a sémát.  
> 
> 

Egyszer utoljára kísérlet futtatása (kattintson **futtatása**.) Ha szeretné ellenőrizni, hogy a modell továbbra is működik, kattintson a kimenetét a [Score Model] [ score-model] modul, és válassza ki **eredmények megtekintése**. Láthatja, hogy az eredeti adatok megjelenik-e, amely a jóváírás kockázati érték ("pontozott címkék") és a pontozási valószínűségi érték ("pontozza a mennyiségeket valószínűség".) 

## <a name="deploy-the-web-service"></a>A webszolgáltatás üzembe helyezése
A kísérlet vagy a klasszikus webes szolgáltatás, vagy egy új webes szolgáltatás, amely alapul, az Azure Resource Manager telepítheti.

### <a name="deploy-as-a-classic-web-service"></a>Egy klasszikus webszolgáltatás telepítése
A kísérletben származó klasszikus webes szolgáltatás telepítéséhez kattintson a **webes szolgáltatás telepítése** a vászonra, és válasszon alább **webes szolgáltatás telepítése [klasszikus]**. A Machine Learning Studio telepíti a kísérlet webszolgáltatásként, és elérheti az irányítópultot, hogy a webes szolgáltatás. Ezen a lapon visszatérhet a kísérlet (**nézet pillanatkép** vagy **legújabb megtekintése**) és a webes szolgáltatás egyszerű teszt futtatása (lásd: **tesztelése a webszolgáltatás** alatt). Itt férhetnek hozzá a fájlhoz társított (további sablonokat, amelyek a jelen útmutató a következő lépésben) alkalmazások létrehozására vonatkozó információkat is van.

![Webes szolgáltatás irányítópult][6]

A szolgáltatás kattintva konfigurálhatja a **konfigurációs** fülre. Itt módosíthatja a szolgáltatás nevét (az adott kísérlet neve alapértelmezés szerint), és adjon neki egy leírást. A bemeneti és kimeneti adatok több rövid címkék is biztosíthat.  

![A webszolgáltatás konfigurálása][5]  

### <a name="deploy-as-a-new-web-service"></a>Egy új webszolgáltatás-bővítmény telepítése

> [!NOTE] 
> Egy új webszolgáltatás-bővítmény telepítése megfelelő engedélyekkel kell rendelkeznie, amely a webes szolgáltatás telepíti az előfizetést. További információkért lásd: [kezelése az Azure Machine Learning webszolgáltatások portál használatával egy webszolgáltatás-bővítmény](manage-new-webservice.md). 

Egy új webszolgáltatás-bővítmény telepítése származó a kísérlet:

1. Kattintson a **webes szolgáltatás telepítése** a vászonra, és válasszon alább **[Új] webes szolgáltatás telepítése**. A Machine Learning Studio továbbítja az Azure Machine Learning webszolgáltatások **telepítése kísérlet** lap.

2. Adja meg a webszolgáltatás nevét. 

3. A **ár terv**, után egy meglévő tarifacsomagot választani, vagy válassza az "Új létrehozása", és nevezze el az új tervet is havi terv választja. Az alapértelmezett régiójától és a webszolgáltatás terv rétegek alapértelmezett régió van telepítve.

4. Kattintson a **telepítése**.

Néhány perc múlva a **gyors üzembe helyezés** a webszolgáltatáshoz lap nyílik meg.

A szolgáltatás kattintva konfigurálhatja a **konfigurálása** fülre. Itt módosíthatja a szolgáltatás title, és adjon neki egy leírást. 

A webszolgáltatás teszteléséhez kattintson a **tesztelése** lap (lásd: **tesztelése a webszolgáltatás** alatt). Létrehozásával kapcsolatos információkat a webszolgáltatás elérhető alkalmazásokat, kattintson a **felhasználás** (Ez a forgatókönyv a következő lépésben kerül, részletesebb) fülre.

> [!TIP]
> A webszolgáltatás telepítése után után frissítheti. Például, ha módosítani kívánja a modell, és szerkesztheti a tanítási kísérletet, végeznünk a modell paramétereket, majd kattintson **webes szolgáltatás telepítése**, kiválasztásával **webes szolgáltatás telepítése [klasszikus]** vagy **[Új] webes szolgáltatás telepítése**. Ha újra telepíti a kísérlet, a webszolgáltatás, most már a frissített modellel váltja fel.  
> 
> 

## <a name="test-the-web-service"></a>A webszolgáltatás tesztelése

Kerül, amikor a webes szolgáltatás, a felhasználói adatok keresztül a **webszolgáltatás bemenetét** ahol átadva a modul a [Score Model] [ score-model] modul és a program pontozza a mennyiségeket. A prediktív kísérletté létrehoztunk hogy módon, a modell adatok vár a ugyanabban a formában, mint az eredeti jóváírás kockázat adatkészletet.
Az eredményeket a rendszer adja vissza a felhasználó keresztül a webszolgáltatás a **webes szolgáltatás kimeneti** modul.

> [!TIP]
> A teljes eredménye a úgy van konfigurálva, a prediktív kísérletté a [Score Model] [ score-model] modul ad vissza. Ez magában foglalja a bemeneti adatok plusz a jóváírás kockázati érték és a pontozási valószínűség. Azonban Visszatérés egy másik, ha azt szeretné,-például csak a jóváírás kockázati érték visszaadhatja. Ehhez beszúrása egy [Projektoszlopok] [ project-columns] közötti modul [Score Model] [ score-model] és a **webes szolgáltatás kimeneti** elkerülése érdekében az oszlopok nem szeretné, hogy a webszolgáltatás való visszatéréshez. 
> 
> 

Tesztelheti a klasszikus webes szolgáltatás, vagy **Machine Learning Studio** vagy a **Azure Machine Learning webszolgáltatások** portál.
Tesztelheti egy új webes szolgáltatás csak a **Machine Learning webszolgáltatások** portálon.

> [!TIP]
> Az Azure Machine Learning webszolgáltatások portálon tesztelésekor akkor is, amelyek segítségével a kérés-válasz szolgáltatás tesztelése mintaadatok létrehozása a portálon. Az a **konfigurálása** lapon, válassza ki a "Yes" a **minta adatok engedélyezve?**. Az a kérelem-válasz lap megnyitásakor az **teszt** lap, a portál jellemzően az az eredeti jóváírás kockázat adatkészletből végrehajtott mintaadatok.

### <a name="test-a-classic-web-service"></a>A klasszikus webszolgáltatás tesztelése

A klasszikus webszolgáltatás tesztelheti a Machine Learning Studióban vagy a Machine Learning webszolgáltatások portálon. 

#### <a name="test-in-machine-learning-studio"></a>A Machine Learning Studióban tesztelése

1. Az a **IRÁNYÍTÓPULT** a webszolgáltatás lapján kattintson a **teszt** gombra kattint, a **alapértelmezett végpont**. Egy párbeszédpanel jelenik meg, és kéri a szolgáltatás a bemeneti adatok. Ezek a jelentek meg az eredeti jóváírás kockázat adatkészlet oszlopát.  

2. Adja meg az adatok, és kattintson a **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>A Machine Learning webszolgáltatások portálon tesztelése

1. Az a **IRÁNYÍTÓPULT** a webszolgáltatás lapján kattintson a **teszt preview** hivatkozásra **alapértelmezett végpont**. A tesztlap a webszolgáltatási végpontot az Azure Machine Learning webszolgáltatások portálon megnyílik, és kéri a szolgáltatás a bemeneti adatok. Ezek a jelentek meg az eredeti jóváírás kockázat adatkészlet oszlopát.

2. Kattintson a **kérés-válasz tesztelése**. 

### <a name="test-a-new-web-service"></a>Egy új webszolgáltatás-bővítmény tesztelése

Egy új webszolgáltatás-bővítmény kizárólag a Machine Learning webszolgáltatások portálon tesztelheti.

1. Az a [Azure Machine Learning webszolgáltatások](https://services.azureml.net/quickstart) portálon kattintson **teszt** az oldal tetején. A **teszt** lap nyílik meg, és azt is adja meg a szolgáltatás adatokat. Jelenik meg a beviteli mezők megegyeznek az oszlopot, amely az eredeti jóváírás kockázat adatkészlet jelent meg. 

2. Adja meg az adatok, és kattintson a **teszt kérés-válasz**.

A vizsgálat eredményeinek jelenik meg a kimeneti oszlop a lap jobb oldalán. 


## <a name="manage-the-web-service"></a>A webszolgáltatás kezelése

Ha a webszolgáltatás telepítése után, hogy klasszikus vagy új, kezelheti az a [Microsoft Azure Machine Learning webszolgáltatások](https://services.azureml.net/quickstart) portálon.

A webszolgáltatás teljesítményének figyelésére:

1. Jelentkezzen be a [Microsoft Azure Machine Learning webszolgáltatások](https://services.azureml.net/quickstart) portál
2. Kattintson a **webszolgáltatások**
3. Kattintson a webszolgáltatás
4. Kattintson a **irányítópult**

- - -
**Következő: [fér hozzá a webszolgáltatáshoz](walkthrough-6-access-web-service.md)**

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
