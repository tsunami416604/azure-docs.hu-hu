---
title: 'Oktatóanyag: egyéni képességek létrehozása és üzembe helyezése Azure Machine Learning'
titleSuffix: Azure Cognitive Search
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre és helyezhet üzembe egyéni képességeket az Azure Cognitive Search AI-bővítési folyamatának Azure Machine Learning használatával.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/10/2020
ms.openlocfilehash: f673fd4b49a33c2faf6bc8b489520f2a877b0689
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2020
ms.locfileid: "85513805"
---
# <a name="tutorial-build-and-deploy-a-custom-skill-with-azure-machine-learning"></a>Oktatóanyag: egyéni szakértelem létrehozása és üzembe helyezése Azure Machine Learning 

Ebben az oktatóanyagban a [Hotel Reviews adatkészletet](https://www.kaggle.com/datafiniti/hotel-reviews) fogja használni (amelyet a Creative Commons Licence [CC-NC-SA 4,0](https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode.txt)) használ, hogy [Egyéni képességeket](https://docs.microsoft.com/azure/search/cognitive-search-aml-skill) hozzon létre a Azure Machine learning használatával, hogy kinyerje a véleményekből származó Aspect-alapú véleményét. Ez lehetővé teszi a pozitív és negatív érzelmek hozzárendelését ugyanabban a felülvizsgálatban, hogy helyesen legyenek megjelölve az azonosított entitásokhoz, például a személyzethez, a helyiséghez, a lobbyhoz vagy a készlethez.

A Azure Machine Learning aspektus-alapú hangulati modell betanításához az [NLP receptek tárházat](https://github.com/microsoft/nlp-recipes/tree/master/examples/sentiment_analysis/absa)fogja használni. Ezután a modell egy Azure Kubernetes-fürtön végpontként lesz üzembe helyezve. Az üzembe helyezést követően a végpontot a Cognitive Search szolgáltatás általi használatra vonatkozó pénzmosás-képességként adja hozzá a dúsítási folyamathoz.

Két adatkészlet van megadva. Ha saját maga szeretné betanítani a modellt, a hotel_reviews_1000.csv fájlra van szükség. Szeretné kihagyni a betanítási lépést? Töltse le a hotel_reviews_100.csv.

> [!div class="checklist"]
> * Azure Cognitive Search-példány létrehozása
> * Azure Machine Learning munkaterület létrehozása (a keresési szolgáltatásnak és a munkaterületnek ugyanahhoz az előfizetéshez kell tartoznia)
> * Modell betanítása és üzembe helyezése Azure Kubernetes-fürtön
> * AI-dúsítási folyamat összekapcsolása az üzembe helyezett modellel
> * Az üzembe helyezett modell kimenetének beolvasása egyéni képességként

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [ingyenes előfizetés](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)beszerzése.
* [Cognitive Search szolgáltatás](https://docs.microsoft.com/azure/search/search-get-started-arm)
* [Erőforrás Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows)
* [Azure Storage-fiók](https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal)
* [Azure Machine Learning-munkaterület](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)

## <a name="setup"></a>Telepítés

* A [minta tárház](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)tartalmának klónozása vagy letöltése.
* Tartalom kinyerése, ha a letöltés zip-fájl. Győződjön meg arról, hogy a fájlok írhatók és írhatók.
* Az Azure-fiókok és-szolgáltatások beállítása közben másolja a neveket és a kulcsokat egy könnyen elérhető szövegfájlba. A rendszer hozzáadja a neveket és a kulcsokat a jegyzetfüzet első cellájához, ahol a változók az Azure-szolgáltatások eléréséhez vannak meghatározva.
* Ha nem ismeri a Azure Machine Learning és a hozzá tartozó követelményeket, érdemes áttekintenie ezeket a dokumentumokat az első lépések előtt:
 * [Fejlesztési környezet konfigurálása Azure Machine Learninghoz](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment)
 * [Azure Machine Learning munkaterületek létrehozása és kezelése a Azure Portal](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)
 * A Azure Machine Learning fejlesztési környezetének konfigurálásakor érdemes lehet a [felhőalapú számítási példányt](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#compute-instance) használni a gyors és egyszerű használat érdekében.
* Töltse fel az adatkészlet-fájlt egy tárolóba a Storage-fiókban. A nagyobb fájlra akkor van szükség, ha szeretné végrehajtani a betanítási lépést a jegyzetfüzetben. Ha szeretné kihagyni a betanítási lépést, a kisebb fájl használata javasolt.

## <a name="open-notebook-and-connect-to-azure-services"></a>Jegyzetfüzet megnyitása és kapcsolódás az Azure-szolgáltatásokhoz

1. Helyezze el az összes szükséges információt azokhoz a változókhoz, amelyek engedélyezik az Azure-szolgáltatásokhoz való hozzáférést az első cellán belül, és futtatják a cellát.
1. A második cella futtatása megerősíti, hogy csatlakozott a keresési szolgáltatáshoz az előfizetéséhez.
1. A 1,1 – 1,5. szakaszban a Search szolgáltatás adattárát, készségkészlet, indexét és indexét fogja létrehozni.

Ezen a ponton kihagyhatja a betanítási adatkészlet létrehozásához szükséges lépéseket, és kísérletezhet a Azure Machine Learningban, és közvetlenül is kihagyhatja a GitHub-tárház models (modellek) mappájában megadott két modell regisztrálását. Ha kihagyja ezeket a lépéseket, a jegyzetfüzetben a 3,5 szakaszra ugorjon, és írjon pontozási szkriptet. Ekkor időt takaríthat meg; az adatok letöltése és a feltöltés lépései akár 30 percet is igénybe vehetnek.

## <a name="creating-and-training-the-models"></a>Modellek létrehozása és betanítása

A 2. szakasz hat cellát tartalmaz, amelyek letöltik a kesztyű beágyazási fájlját az NLP receptek tárházból. A letöltés után a rendszer feltölti a fájlt a Azure Machine Learning adattárba. A. zip fájl körülbelül 2G, és eltarthat egy ideig a feladatok végrehajtásához. A feltöltés után a rendszer kinyeri az betanítási adatgyűjtést, és most már készen áll a 3. szakaszra való áttérésre.

## <a name="train-the-aspect-based-sentiment-model-and-deploy-your-endpoint"></a>A aspektuson alapuló hangulati modell betanítása és a végpont üzembe helyezése

A jegyzetfüzet 3. szakasza a 2. szakaszban létrehozott modelleket fogja betanítani, regisztrálja ezeket a modelleket, és az Azure Kubernetes-fürtben végpontként telepíti őket. Ha nem ismeri az Azure Kubernetes, javasoljuk, hogy tekintse át a következő cikkeket, mielőtt megpróbál létrehozni egy következtetési fürtöt:

* [Az Azure Kubernetes szolgáltatás áttekintése](https://docs.microsoft.com/azure/aks/intro-kubernetes)
* [Az Azure Kubernetes Service (ak) Kubernetes alapvető fogalmai](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads)
* [Kvóták, virtuális gépek méretére vonatkozó korlátozások és a régió elérhetősége az Azure Kubernetes szolgáltatásban (ak)](https://docs.microsoft.com/azure/aks/quotas-skus-regions)

A következtetési fürt létrehozása és üzembe helyezése akár 30 percet is igénybe vehet. A webszolgáltatás tesztelése a végső lépésekre való áttérés előtt, a készségkészlet frissítése és az indexelő futtatása előtt ajánlott.

## <a name="update-the-skillset"></a>A készségkészlet frissítése

A jegyzetfüzet 4. szakasza négy olyan cellával rendelkezik, amelyek frissítik a készségkészlet és az indexelő. Azt is megteheti, hogy a portál használatával kijelöli és alkalmazza az új képességet a készségkészlet, majd a keresési szolgáltatás frissítéséhez futtatja az indexelő.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Active-Learning-with-Azure-Cognitive-Search/player#time=19m35s:paused/03/player]

A portálon lépjen a Készségkészlet elemre, és válassza ki a Készségkészlet-definíció (JSON) hivatkozást. A portál megjeleníti a készségkészlet tartozó JSON-t, amely a jegyzetfüzet első cellájában lett létrehozva. A megjelenítéstől jobbra legördülő menü látható, ahol kiválaszthatja a szakértelem-definíciós sablont. Válassza ki a Azure Machine Learning (pénzmosás) sablont. adja meg az Azure ML-munkaterület nevét és a végpontot, amelyet a rendszer a következtetési fürtön üzembe helyezett modellhez használt. A sablon a végpont URI-ja és kulcsa alapján lesz frissítve.

> [!div class="mx-imgBorder"]
> ![Készségkészlet-definíciós sablon](media/cognitive-search-aml-skill/portal-aml-skillset-definition.png)

Másolja a készségkészlet sablont az ablakból, és illessze be a készségkészlet-definícióba a bal oldalon. Szerkessze a sablont, hogy a hiányzó értékeket adja meg a következőhöz:

* Name (Név)
* Leírás
* Környezet
* "bemenetek" neve és forrása
* "outputs" neve és targetName

Mentse a készségkészlet.

A készségkészlet mentése után lépjen az indexelő elemre, és válassza ki az indexelő definíció (JSON) hivatkozást. A portál megjeleníti a jegyzetfüzet első cellájában létrehozott indexelő JSON-t. A kimeneti mezők leképezéseit további mező-hozzárendelésekkel kell frissíteni, hogy az indexelő képes legyen megfelelően kezelni és továbbítani azokat. Mentse a módosításokat, majd válassza a Futtatás lehetőséget. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az egyéni ügyességi webes API áttekintése](https://docs.microsoft.com/azure/search/cognitive-search-custom-skill-web-api) 
>  [További információ az egyéni szaktudás hozzáadásáról a dúsítási folyamathoz](https://docs.microsoft.com/azure/search/cognitive-search-custom-skill-interface)