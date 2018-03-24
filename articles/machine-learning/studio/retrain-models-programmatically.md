---
title: Machine Learning modellek szoftveres újratanítása |} Microsoft Docs
description: Megtudhatja, hogyan programozott módon modell működik, és frissíti a az újonnan betanított modell használatára az Azure Machine Learning webszolgáltatás.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 7ae4f977-e6bf-4d04-9dde-28a66ce7b664
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 276ee051cf927197c026b0fb0850239a048ace41
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="retrain-machine-learning-models-programmatically"></a>Azure Machine Learning-modellek szoftveres átképezése
Ebben a bemutatóban, megtudhatja, hogyan való programozott módon működik az Azure Machine Learning webszolgáltatás C# és a Machine Learning kötegelt végrehajtási szolgáltatás használatával.

A modell rendelkezik retrained, a következő forgatókönyvek a prediktív webszolgáltatás a modell frissítése megjelenni:

* Ha a Machine Learning webszolgáltatások portálon klasszikus webszolgáltatás központi telepítése, lásd: [egy klasszikus webszolgáltatás újratanítása](retrain-a-classic-web-service.md). 
* Ha egy új webszolgáltatás-bővítmény központi telepítése, lásd: [egy új webszolgáltatás-bővítmény, a Machine Learning Management-parancsmagok használatával újratanítása](retrain-new-web-service-using-powershell.md).

A megőrzési folyamat áttekintését lásd: [a gépi tanulási modellek újratanítása](retrain-machine-learning-model.md).

Ha el szeretné indítani a meglévő új Azure Resource Manager-alapú webes szolgáltatással, lásd: [meglévő prediktív webszolgáltatás újratanítása](retrain-existing-resource-manager-based-web-service.md).

## <a name="create-a-training-experiment"></a>Hozzon létre egy tanítási kísérletet
Ehhez a példához használandó "minta 5: vonat, tesztelési, Evaluate bináris osztályozási: felnőtt Dataset" a Microsoft Azure Machine Learning minták. 

A kísérlet létrehozása:

1. Jelentkezzen be a Microsoft Azure gép Learning Studio. 
2. Kattintson a jobb alsó sarkában az irányítópulton, **új**.
3. Válassza ki a Microsoft Samples minta 5.
4. Nevezze át a kísérletet a kísérlet vászon tetején jelölje ki a kísérlet neve "minta 5: vonat, tesztelési, Evaluate bináris osztályozási: felnőtt Dataset".
5. Típus nyilvántartásba modell.
6. Kattintson a kísérletvászon alján **futtatása**.
7. Kattintson a **Set Up webszolgáltatás** válassza **webszolgáltatás Átképezési**. 

Az alábbiakban látható a kezdeti kísérlet.
   
   ![Kezdeti kísérlet.][2]


## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>A prediktív kísérletté létrehozása és egy webszolgáltatás közzététele
Ezután hozzon létre egy Predicative kísérletet.

1. A kísérlet vászon alján kattintson **webes szolgáltatások beállítása** válassza **prediktív webszolgáltatás**. Ez a modell menti modell betanítását, és hozzáadja a webes szolgáltatás bemeneti és kimeneti modulok. 
2. Kattintson a **Run** (Futtatás) parancsra. 
3. Miután a kísérlet futása befejeződött, kattintson **webes szolgáltatás telepítése [klasszikus]** vagy **[Új] webes szolgáltatás telepítése**.

> [!NOTE] 
> Egy új webszolgáltatás-bővítmény telepítése, megfelelő engedélyekkel kell rendelkeznie, amelyhez az előfizetést, a webszolgáltatás telepítése. További információ: [kezelése az Azure Machine Learning webszolgáltatások portál használatával egy webszolgáltatás-bővítmény](manage-new-webservice.md). 

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>A tanítási kísérletet egy képzési webszolgáltatás telepítése
A betanított modell működik, a tanítási kísérletet Retraining webszolgáltatásként létrehozott kell telepítenie. A webszolgáltatás kell egy *webes szolgáltatás kimeneti* modul csatlakozik a *[tanítási modell] [ train-model]* modul kell tudnia mutatni az új betanítása modellek.

1. Térjen vissza a tanítási kísérletet, a kísérletek ikonra a bal oldali ablaktáblán kattintson, majd a nyilvántartásba modell nevű kísérlet.  
2. A keresési kísérlet elemek keresési mezőbe írja be a webes szolgáltatás. 
3. Húzza a *webszolgáltatás bemenetét* modult a kísérletvászonra, és kösse a kimenetét a *Clean Missing Data* modul.  Ez biztosítja, hogy a megőrzési adatot ugyanúgy, mint az eredeti tanítási adatokat dolgoz fel.
4. Húzza a két *webszolgáltatás kimeneti* modulok a kísérlet vászonra. Csatlakoztassa a kimenetét a *tanítási modell* modul egy és kimenetét a *modell kiértékelése* modul a másikra. A webes szolgáltatás kimenetét **tanítási modell** biztosítanak számunkra az új betanított modell. A kimeneti csatolva **modell kiértékelése** modult kimenetneve, vagyis a teljesítményeredmények visszaadása.
5. Kattintson a **Run** (Futtatás) parancsra. 

Ezután telepítenie kell a tanítási kísérletet egy webszolgáltatás, amely létrehozza a modell betanítását és modell kiértékelésének eredménye. Ennek megvalósítása érdekében a következő műveletek készletét függenek e dolgozik egy klasszikus webszolgáltatás-bővítmény vagy egy új webszolgáltatás-bővítmény.  

**Klasszikus webszolgáltatás**

A kísérlet vászon alján kattintson **webes szolgáltatások beállítása** válassza **webes szolgáltatás telepítése [klasszikus]**. A webszolgáltatás **irányítópult** jelenik meg, amely az API-kulcsot és az API súgólap a kötegelt végrehajtás. Csak a kötegelt végrehajtási módszer betanított modellek létrehozásakor használható.

**Új webszolgáltatás**

A kísérlet vászon alján kattintson **webes szolgáltatások beállítása** válassza **[Új] webes szolgáltatás telepítése**. A webes szolgáltatás Azure Machine Learning webszolgáltatások portál megnyitja a telepítés webszolgáltatás oldalát. Adjon meg egy nevet a webszolgáltatáshoz és támogatási csomag kiválasztása, majd kattintson **telepítés**. Csak a kötegelt végrehajtási módszer használható betanított modellek létrehozásához

Mindkét esetben után kísérlet befejeződött, az eredményül kapott munkafolyamat kell hasonlítania:

![Eredményül kapott munkafolyamat futtatása után.][4]



## <a name="retrain-the-model-with-new-data-using-bes"></a>A modell újratanítása BES használatával új adatokkal
Ehhez a példához segítségével végzi a C# a megőrzési alkalmazás létrehozása. A Python vagy R mintakód segítségével ennek a feladatnak.

Az Átképezési API-k meghívásához:

1. Hozzon létre egy C# konzolalkalmazást a Visual Studio: **új** > **projekt** > **Visual C#** > **klasszikus Windows asztal** > **Konzolalkalmazás (.NET-keretrendszer)**.
2. Jelentkezzen be a Machine Learning webszolgáltatás portálra.
3. Ha egy klasszikus webszolgáltatás dolgozik, kattintson a **klasszikus webszolgáltatások**.
   1. Kattintson a webszolgáltatás dolgozik.
   2. Kattintson az alapértelmezett végpont.
   3. Kattintson a **felhasználásához**.
   4. Alján a **felhasználás** lap a **mintakód** kattintson **kötegelt**.
   5. Folytassa az eljárás 5. lépés.
4. Ha egy új webszolgáltatás-bővítmény dolgozik, kattintson a **webszolgáltatások**.
   1. Kattintson a webszolgáltatás dolgozik.
   2. Kattintson a **felhasználásához**.
   3. At a felhasználás alján lap a **mintakód** kattintson **kötegelt**.
5. A kötegelt végrehajtás C# mintakód másolja és illessze be a Program.cs fájl meggyőződött arról, hogy a névtér nem módosulnak.

A Megjegyzések a Microsoft.AspNet.WebApi.Client Nuget-csomag hozzáadása A hivatkozás Microsoft.WindowsAzure.Storage.dll hozzáadásához először meg kell az ügyféloldali kódtár a Microsoft Azure storage szolgáltatásainak telepítéséhez. További információkért lásd: [Windows tárolószolgáltatások](https://www.nuget.org/packages/WindowsAzure.Storage).

### <a name="update-the-apikey-declaration"></a>Frissítés a apikey nyilatkozat
Keresse meg a **apikey** nyilatkozatot.

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Az a **alapvető fogyasztási adatai** szakasza a **felhasználás** lapon keresse meg az elsődleges kulcsot, és másolja a **apikey** nyilatkozatot.

### <a name="update-the-azure-storage-information"></a>Az Azure Storage-adatainak módosítása
A BES mintakód feltölt egy fájlt egy helyi meghajtó (például "C:\temp\CensusIpnput.csv") az Azure Storage, folyamatokat engedélyez, és kiírja az eredményeket vissza az Azure Storage.  

Ennek a feladatnak le kell kérni a Tárfiók nevét, a kulcs és a tároló adatait a tárfiók a klasszikus Azure portál és a frissítés megfelelő értékeket a kódban. 

1. Jelentkezzen be a klasszikus Azure portálon.
2. A bal oldali oszlopban kattintson **tárolási**.
3. Storage-fiókok a listában válassza ki a retrained modell tárolásához.
4. Kattintson a lap alján **elérési kulcsok kezelése**.
5. Másolja ki és mentse a **elsődleges elérési kulcsot** és a párbeszédpanel bezárásához. 
6. Kattintson a lap tetején **tárolók**.
7. Jelöljön ki egy meglévő tárolót, vagy hozzon létre egy újat, és mentse a nevét.

Keresse meg a *StorageAccountName*, *StorageAccountKey*, és *StorageContainerName* deklarációk és az Azure-portálról mentett értékeinek frissítéséhez.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name

Emellett győződjön meg a bemeneti fájl érhető el a kódban megadott helyen. 

### <a name="specify-the-output-location"></a>Adja meg a kimeneti helyet
A kimeneti helyen a kérelem hasznos, megadásakor a kiterjesztést a fájl megadott *RelativeLocation* ilearner kell megadni. 

Tekintse meg a következő példát:

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
> A kimeneti helyek nevének ebben a bemutatóban a webes szolgáltatás kimeneti modulok hozzá sorrendjében eltérő lehet. A tanítási kísérletet két kimenettel állít be, mert az eredmények tartalmazzák a Tartózkodásihely-adatok tárolási helyezni őket.  
> 
> 

![Kimeneti átképezési][6]

4. ábra: A kimeneti Átképezési.

## <a name="evaluate-the-retraining-results"></a>Megőrzési eredmények értékelése
Az alkalmazás futtatásakor a parancs kimenete az értékelés eredményét eléréséhez szükséges URL-cím és a SAS tokent.

A teljesítmény eredmények retrained modell kombinálásával láthatók a *BaseLocation*, *RelativeLocation*, és *SasBlobToken* a kimeneti eredmények a *output2* (az előző megőrzési kimeneti ábrának megfelelően), és be a teljes URL-CÍMÉT a böngésző címsorába.  

Vizsgálja meg az eredményeket a meghatározásához, hogy az újonnan betanított modell végez elégséges helyettesítse a meglévőt.

Másolás a *BaseLocation*, *RelativeLocation*, és *SasBlobToken* a kimeneti eredmények használhatja azokat a megőrzési során.

## <a name="next-steps"></a>További lépések
Ha telepítette a prediktív webszolgáltatás kattintva **webes szolgáltatás telepítése [klasszikus]**, lásd: [egy klasszikus webszolgáltatás újratanítása](retrain-a-classic-web-service.md).

Ha telepítette a prediktív webszolgáltatás kattintva **[Új] webes szolgáltatás telepítése**, lásd: [egy új webszolgáltatás-bővítmény, a Machine Learning Management-parancsmagok használatával újratanítása](retrain-new-web-service-using-powershell.md).

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
