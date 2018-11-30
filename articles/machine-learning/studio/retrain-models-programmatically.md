---
title: Modellek újbóli betanítása Machine Learning Studio – Azure |} A Microsoft Docs
description: Ismerje meg, hogyan programozott módon modellek szoftveres átképezése és frissíteni a webszolgáltatást a újonnan betanított modell használata az Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 7ae4f977-e6bf-4d04-9dde-28a66ce7b664
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: b38143fe6f1f1cf3e65a2989e1b0a71c28530b2a
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52313385"
---
# <a name="retrain-machine-learning-studio-models-programmatically"></a>A Machine Learning Studio-modellek újbóli betanítása
Ebben a forgatókönyvben megtudhatja, hogyan való programozott újratanítás PowerShell használatával az Azure Machine Learning Studio webes szolgáltatás C# és a Machine Learning Batch Execution service.

Ha a modell rendelkezik retrained, a következő forgatókönyvek bemutatják, hogyan a prediktív webszolgáltatásait a modell frissítése:

* Ha telepítette a Machine Learning Web Services portálon klasszikus webszolgáltatás, lásd: [klasszikus webszolgáltatás újratanítása](retrain-a-classic-web-service.md). 
* Ha egy új webszolgáltatás üzembe helyezte, lásd: [a Machine Learning-kezelési parancsmagok használatával új webszolgáltatás újratanítása](retrain-new-web-service-using-powershell.md).

A megőrzési folyamat áttekintését lásd: [egy gépi tanulási modellek szoftveres Átképezése](retrain-machine-learning-model.md).

Ha el szeretné indítani a meglévő új Azure Resource Manager-alapú webes szolgáltatáshoz, talál [meglévő prediktív webszolgáltatás újratanítása](retrain-existing-resource-manager-based-web-service.md).

## <a name="create-a-training-experiment"></a>Betanítási kísérlet létrehozása
Ebben a példában használandó "5 minta: Train, tesztelési, kiértékelése a bináris osztályozás: felnőtt adatkészlet" a Microsoft Azure Machine Learning-minták. 

A kísérlet létrehozása:

1. Jelentkezzen be a Microsoft Azure Machine Learning Studióba. 
2. Kattintson a jobb alsó sarokban az irányítópult **új**.
3. Jelölje ki a Microsoft Samples minta 5.
4. Nevezze át a kísérletet a kísérlet vászon tetején válassza a kísérlet neve "5. példa: a bináris osztályozási Train, tesztelési, Evaluate: felnőtt adatkészlet".
5. Írja be a modell népszámlálási.
6. A kísérlet vászon alján kattintson **futtatása**.
7. Kattintson a **Set Up webszolgáltatás** válassza **webszolgáltatás Átképezési**. 

Az alábbiakban látható a kezdeti kísérletet.
   
   ![Kezdeti kísérlet.][2]


## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>Egy prediktív kísérletet létrehozása és közzététele webszolgáltatásként
Ezután hozzon létre egy Predicative kísérletet.

1. A kísérlet vászon alján kattintson **webszolgáltatás beállítása** válassza **prediktív webszolgáltatás**. Ez a modell menti a betanított modell, és hozzáadja a webes szolgáltatás bemeneti és kimeneti modulok. 
2. Kattintson a **Run** (Futtatás) parancsra. 
3. A kísérlet befejezését követően, miután kattintson **Web Service telepítése [klasszikus]** vagy **[Új] Web Service telepítése**.

> [!NOTE] 
> Egy új webszolgáltatás üzembe helyezéséhez rendelkeznie megfelelő engedélyekkel, amelyhez az előfizetésben, a web Service szolgáltatásának telepítése. További információ: [egy webszolgáltatás, az Azure Machine Learning Web Services portál használata kezelheti](manage-new-webservice.md). 

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>A betanítási kísérlet üzembe webszolgáltatásként, amely képzés
Újratanítás PowerShell a betanított modell, a tanítási kísérlet Retraining webszolgáltatásként létrehozott kell telepíteni. A webszolgáltatás szüksége van egy *webes szolgáltatás kimeneti* csatlakoztatott a *[tanítási modell] [ train-model]* modult kell tudnia mutatni új betanított modellek.

1. Térjen vissza a tanítási kísérlet, a kísérletek ikonra a bal oldali ablaktáblán kattintson, majd kattintson a kísérlet népszámlálási modell nevű.  
2. A kísérlet elemek keresése a keresőmezőbe írja be a webszolgáltatást. 
3. Húzza a *webes bemenet* modult alakzatot a kísérletet, és a kimenetét, hogy csatlakozzon a *Clean Missing Data* modul.  Ez biztosítja, hogy a megőrzési adatok ugyanúgy, mint az eredeti betanítási adatok feldolgozása.
4. Húzza a két *webszolgáltatás kimeneti* modulok a kísérlet vászonra. Csatlakozzon a kimenetét a *tanítási modell* kimenetét és a egy modult a *Evaluate Model* modult, a másik. A web service kimenete **tanítási modell** biztosít számunkra az új betanított modell. A kimenet csatolt **Evaluate Model** adja vissza az adott modul kimeneti, azaz a teljesítményt.
5. Kattintson a **Run** (Futtatás) parancsra. 

Ezután telepítenie kell a tanítási kísérlet webes szolgáltatás, amely a betanított modell és a modell kiértékelésének eredménye hoz létre. Ehhez a következő műveletkészletet függenek az e dolgozik egy klasszikus webszolgáltatásról vagy új webszolgáltatásként.  

**Klasszikus webszolgáltatás**

A kísérlet vászon alján kattintson **webszolgáltatás beállítása** válassza **Web Service telepítése [klasszikus]**. A Web Service **irányítópult** kötegelt végrehajtáshoz az API-kulcsot és az API help oldal jelenik meg. Csak a kötegelt végrehajtási módszer betanított modellek létrehozására használható.

**Új webszolgáltatás**

A kísérlet vászon alján kattintson **webszolgáltatás beállítása** válassza **[Új] Web Service telepítése**. A webes szolgáltatás az Azure Machine Learning webszolgáltatások portál megnyitja az üzembe helyezés webszolgáltatás oldalát. Adjon meg egy nevet a webszolgáltatás és a egy támogatási csomag kiválasztása, majd kattintson **telepítés**. Csak a kötegelt végrehajtási módszer használható betanított modellek létrehozása

Mindkét esetben kísérlet befejeztével fut, az eredményül kapott munkafolyamat kell hasonlítania:

![Eredményül kapott a munkafolyamat futtatása után.][4]



## <a name="retrain-the-model-with-new-data-using-bes"></a>Újratanítás PowerShell a modell új adatokkal, BES használatával
Ebben a példában használja C# a megőrzési alkalmazás létrehozásához. Ennek a feladatnak is használhatja a Python vagy R mintakódot.

Az Átképezési API-k meghívásához:

1. Hozzon létre egy C# konzolalkalmazást a Visual Studióban: **új** > **projekt** > **Visual C#** > **Windows Klasszikus asztal** > **Console App (.NET Framework)**.
2. Jelentkezzen be a Machine Learning Web Service portálhoz.
3. Ha a klasszikus webszolgáltatás dolgozik, kattintson a **klasszikus webszolgáltatások**.
   1. Kattintson a web service dolgozik.
   2. Kattintson az alapértelmezett végpont.
   3. Kattintson a **felhasználása**.
   4. Alsó részén a **felhasználás** lap a **mintakód** területén kattintson **Batch**.
   5. Folytassa az eljárás 5. lépés.
4. Ha egy új webszolgáltatás dolgozik, kattintson a **webszolgáltatások**.
   1. Kattintson a web service dolgozik.
   2. Kattintson a **felhasználása**.
   3. At a felhasználás alján lap a **mintakód** területén kattintson **Batch**.
5. Másolja a minta C# kötegelt végrehajtáshoz a kódot, és illessze be a Program.cs fájl gondoskodik róla, hogy a névtér helye változatlan marad.

Adja hozzá a Microsoft.AspNet.WebApi.Client Nuget-csomagot a megjegyzések megadott módon. Microsoft.WindowsAzure.Storage.dll mutató hivatkozás hozzáadásához először meg kell telepítenie az ügyféloldali kódtár a Microsoft Azure storage-szolgáltatásokban. További információkért lásd: [Windows tárolószolgáltatások](https://www.nuget.org/packages/WindowsAzure.Storage).

### <a name="update-the-apikey-declaration"></a>Frissítés a apikey tulajdonsággal végzett tesztelése nyilatkozat
Keresse meg a **apikey tulajdonsággal végzett tesztelése** nyilatkozatot.

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Az a **alapvető fogyasztási adatai** szakaszában a **felhasználás** lapon, keresse meg az elsődleges kulcsot, és másolja a **apikey tulajdonsággal végzett tesztelése** nyilatkozatot.

### <a name="update-the-azure-storage-information"></a>Az Azure Storage-adatok frissítése
A BES-mintakód feltölt egy fájlt egy helyi meghajítóról (például "C:\temp\CensusIpnput.csv") az Azure Storage, feldolgozza őket, majd az eredmények visszaírja az Azure Storage.  

Ennek a feladatnak, le kell kérnie a Storage-fiók nevét, a kulcs és a tároló adatait a tárfiók a klasszikus Azure portál és a frissítés megfelelő értékeket a kódban. 

1. Jelentkezzen be a klasszikus Azure portálon.
2. A bal oldali navigációs oszlopban kattintson **tárolási**.
3. Storage-fiókok listájából válassza ki a retrained modell tárolásához.
4. Kattintson a lap alján **hozzáférési kulcsok kezelése**.
5. Másolja ki és mentse a **elsődleges elérési kulcs** és a párbeszédpanel bezárásához. 
6. Kattintson a lap tetején **tárolók**.
7. Jelöljön ki egy meglévő tárolót, vagy hozzon létre egy újat, és mentse a nevét.

Keresse meg a *StorageAccountName*, *StorageAccountKey*, és *StorageContainerName* nyilatkozatok és frissítése az Azure Portalról mentett értékekkel.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name

Is biztosítania kell a kódban megadott helyen a bemeneti fájl érhető el. 

### <a name="specify-the-output-location"></a>Adja meg a kimeneti helyet
A kimeneti helyet a kérelem hasznos adatainak megadásakor a kiterjesztést a fájl megadott *RelativeLocation* ilearner kell megadni. 

Lásd a következő példát:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

> [!NOTE]
> A kimeneti helyek nevének Ez az útmutató a webes szolgáltatás kimeneti modulok hozzá sorrendje alapján eltérő lehet. Mivel ez a két kimenetek betanítási kísérlet állít be, az eredmények tartalmazzák a tárolási hely adatainak őket.  
> 
> 

![Kimeneti átképezési][6]

4. ábra: A kimeneti Átképezési.

## <a name="evaluate-the-retraining-results"></a>Megőrzési eredmények értékelése
Az alkalmazás futtatásakor a kimenete tartalmazza az értékelési eredmények eléréséhez szükséges URL-CÍMÉT és a SAS jogkivonat.

Megtekintheti a retrained modell teljesítmény eredményeinek kombinálásával a *BaseLocation*, *RelativeLocation*, és *SasBlobToken* a kimenetieredményei*output2* (ahogyan az az előző megőrzési kimeneti képen látható), és illessze be a teljes URL-CÍMÉT a böngésző címsorába.  

Vizsgálja meg az eredményeket megállapítani, hogy az újonnan betanított modell végrehajt elég jól lecserélheti a meglévőt.

Másolás a *BaseLocation*, *RelativeLocation*, és *SasBlobToken* a kimeneti eredmények között használhatja őket a megőrzési folyamat során.

## <a name="next-steps"></a>További lépések
Ha telepítette a prediktív webszolgáltatás kattintva **Web Service telepítése [klasszikus]**, lásd: [klasszikus webszolgáltatás újratanítása](retrain-a-classic-web-service.md).

Ha telepítette a prediktív webszolgáltatás kattintva **[Új] Web Service telepítése**, lásd: [a Machine Learning-kezelési parancsmagok használatával új webszolgáltatás újratanítása](retrain-new-web-service-using-powershell.md).

<!-- Retrain a New web service using the Machine Learning Management REST API -->


[1]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
