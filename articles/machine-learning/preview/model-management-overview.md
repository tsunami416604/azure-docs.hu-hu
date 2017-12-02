---
title: "Elméleti áttekintése Azure Machine Learning modell kezelése |} Microsoft Docs"
description: Ez a dokumentum az Azure Machine Learning modell fogalmait ismerteti.
services: machine-learning
author: nk773
ms.author: neerajkh, padou
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 0474e7ae77128a46fc6218de814913d07aa63d97
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2017
---
# <a name="azure-machine-learning-model-management"></a>Azure Machine Learning Modellkezelés

Az Azure Machine Learning modell Management használatával kezelheti és telepítheti a gépi tanulásra munkafolyamatok és -modellek teszi lehetővé. 

Modell-kezelési képességeket biztosítja:
- Modell versioning
- Éles környezetben követési modellek
- Üzemi környezetben keresztül AzureML számítási környezetben a modellek telepítéséről [Azure Tárolószolgáltatás](https://azure.microsoft.com/services/container-service/) és [Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- A modellek Docker-tároló létrehozása, és helyi tesztelés
- Automatikus modell átképezési
- A gyakorlatban használható elemzések modell telemetria rögzítését. 

Az Azure Machine Learning modell kezelése a modell verziók egy jegyzéket biztosít. Automatizált munkafolyamatok csomagolás és a REST API-k, a Machine Learning tárolók telepítése is tartalmazza. A modellek és futásidejű függősége vannak csomagolva, előrejelzés API Linux-alapú Docker-tárolóban. 

Az Azure Machine Learning-számítási környezetek beállítása és kezelése az üzemeltetési modellek méretezhető fürtök segítségével. A számítási környezet Azure tárolószolgáltatások alapul. Azure tárolószolgáltatások biztosít a Machine Learning API-k automatikus kitettség REST API-végpontokon a következő szolgáltatásokkal:

- Authentication
- Terheléselosztás
- Automatikus kibővített
- Titkosítás

Az Azure Machine Learning modell Management további olyan funkciókat kínál, a parancssori felület API és az Azure-portálon keresztül. 

Az Azure Machine Learning modell management használja a következő információkat:

 - Modell fájl vagy könyvtár a modell fájljai
 - Felhasználó létrehozott egy pontozó függvény modell megvalósításához Python-fájl
 - Conda függőségi fájl futásidejű függőségek listázása
 - Futásidejű környezet választás, és 
 - Fájl API-paraméterek 

Ezt az információt használja a következő műveletek végrehajtása során:

- A modell regisztrálása
- A jegyzékfájl létrehozása, amely használatos, ha a tároló létrehozása
- A Docker tároló lemezkép
- A tároló üzembe az Azure Tárolószolgáltatás
 
Az alábbi ábra a modellek regisztrált és a fürt helyezett hogyan áttekintését tartalmazza. 

![](media/model-management-overview/modelmanagement.png)

## <a name="create-and-manage-models"></a>Hozzon létre és modellek kezelése 
Az Azure Machine Learning modell Management modellek regisztrál követési modellverziók éles környezetben. A könnyű reprodukálhatósági és irányítási a szolgáltatás összes függőségeket és társított információit rögzíti. A teljesítmény mérőszámokhoz rögzítheti a modell telemetriai adatokat a megadott SDK használatával. A felhasználó által megadott tárolási modell telemetriai archivált. A modell telemetriai később modell teljesítményének elemzése, átképezési és a saját üzleti elemzések eszközöket is használható.

## <a name="create-and-manage-manifests"></a>Hozzon létre és kezelheti a jegyzékfájlban 
Modellek szükséges további összetevők telepítése éles környezetben. A rendszer lehetővé teszi a jegyzékfájl, amely magában foglalja a modell, a függőségek, a megállapítás parancsfájlból (más néven pontozási), a mintaadatok, a séma stb létrehozásához. A jegyzékfájl egy Docker-tároló lemezkép létrehozása receptet funkcionál. Vállalatok automatikusan előállítja a jegyzék, különböző verziói létrehozása és kezelése a jegyzékfájlban. 

## <a name="create-and-manage-docker-container-images"></a>Hozzon létre és Docker-tároló lemezképek kezelése 
Az előző lépésben a jegyzék segítségével a saját környezetükben tároló Docker-alapú lemezképeket. A tárolóalapú, Docker-alapú lemezképek ezeket a lemezképeket futtassa a következő számítási környezetek rugalmassága vállalatok biztosítják:

- [Kubernetes alapú Azure Tárolószolgáltatásban](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- A helyi tároló szolgáltatások
- Fejlesztési környezetekben
- Az IoT-eszközök

A Docker-alapú indexelése lemezképek az összes szükséges függőségek Előrejelzés létrehozásához szükséges. 

## <a name="deploy-docker-container-images"></a>Docker-tároló lemezképek központi telepítése 
Az Azure Machine Learning modell Management használatával telepíthet egy parancs képeket tároló Docker-alapú ML számítási környezet kezeli az Azure Tárolószolgáltatás számára. A központi telepítéseket jönnek létre az előtér-kiszolgálót, amely a következő szolgáltatásokat biztosítja:

- Kis késleltetésű előrejelzéseket léptékű
- Terheléselosztás
- Az automatikus skálázás ML-végpontok
- API-kulcs engedélyezési
- API swagger-dokumentum

A központi telepítés méretezés és telemetriai adatokat a következő beállítások segítségével szabályozhatja:

- Rendszer-naplózás és a modell telemetriai minden webes szolgáltatási szint. Ha engedélyezve van, az összes stdout napló átvitt [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Modell telemetriai archiválja a rendszer az Ön által biztosított storage. 
- Automatikus méretezése és a feldolgozási korlátok. Ezek a beállítások automatikusan az üzembe helyezett tárolók alapján a meglévő fürt a terhelés számának növeléséhez. Akkor is szabályozhatja, az átviteli sebesség és előrejelzés késés konzisztenciáját.

## <a name="consumption"></a>Használat 
Az Azure Machine Learning modell Management REST API-t a swagger-dokumentum együtt telepített modell hoz létre. Hívása a REST API-kat API-val kulcs, és az előrejelzés lekérni a sor üzleti alkalmazások részeként bemenetek modell által telepített modellek is felhasználhatnak. A mintakód érhető el a Githubon Java, nyelven [Python](https://github.com/CortanaAnalyticsGallery-Int/digit-recognition-cnn-tf/blob/master/client.py), és a C# REST API-k hívásakor. Az Azure Machine Learning modell felügyeleti parancssori felület segítségével egyszerűen a REST API-khoz. Az API-k használata curl használatával, vagy egyetlen parancssori paranccsal, a swagger-kompatibilis alkalmazások is felhasználhatnak. 

## <a name="retraining"></a>Átképezési 
Az Azure Machine Learning modell Management biztosít, amelyek segítségével a modellek újratanítása API-k. Az API-k segítségével is telepítéseit frissíteni a modell frissített verzióit. A adatelemezési munkafolyamatot részeként akkor hozza létre újra a modellt a kísérleti környezetben. Ezután a modell regisztrálása modell kezelése, és frissítse a meglévő telepítések. Frissítések megtörténik egy frissítési CLI parancs használatával. A frissítés parancs frissíti a meglévő telepítések API URL-CÍMÉT vagy a kulcs módosítása nélkül. A modell fel az alkalmazások továbbra is a kód módosítása nélkül, és új modell használatával jobb előrejelzéseket lekérésének elindítása.

Az alábbi ábrán a teljes munkafolyamat ezekről a fogalmakról leíró rögzített:

![](media/model-management-overview/modelmanagementworkflow.png)

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK) 
- Milyen adatok típusok támogatottak? Is NumPy tömbök is át közvetlenül webszolgáltatás bemeneteként?

   Ha meg van adva a következő sémafájl generate_schema SDK használatával létrehozott, majd átadhatók NumPy és/vagy Pandas DF. Bármilyen szerializálható JSON-bemenet is átadhatja. Kép átadhatók, valamint a bináris kódolású karakterlánc.

- A webszolgáltatás támogatja a több bemeneti adatokat vagy más bemeneti adatok elemzése? 

   Igen, több bemeneti szótár, egy JSON-kérelmi csomagolt is igénybe vehet. Minden egyes bemeneti egyedi szótárkulcs volna meg.

- A hívás a webes kérelem által aktivált szolgáltatás blokkoló hívás vagy egy aszinkron hívás?

   Ha a valós idejű funkcióval a CLI-t vagy API-t részeként szolgáltatás lett létrehozva, akkor célszerű blokkolja/aszinkron hívás. Azt kellene lennie a valós idejű gyors. Bár az ügyféloldalon hívása aszinkron HTTP-könyvtár használatával elkerülése érdekében az ügyféloldali szál blokkolja.

- Hány kérésnek a webszolgáltatás egyidejűleg kezelheti?

   A fürt és a webes szolgáltatás skála függ. Ki lehet terjeszteni a replikák 100 x szolgáltatást, és ezután is képes kezelni hány kérésnek egyidejűleg. Beállíthatja az egyidejű kérések maximális száma replika szolgáltatás átviteli sebesség növelése.

- Hány kérésnek a webszolgáltatás várólistán tárolható?

   Is konfigurálható. Alapértelmezés szerint 10 ~ / replikához értékűre van állítva, de akkor is növelése vagy csökkentése, az alkalmazás igényeinek megfelelően. Általában azt növelése sorba állított kérelmek száma növeli a szolgáltatás átviteli sebességét, de lehetővé teszi a késések ami még rosszabb sebességnél nagyobb százalékos érték. És konzisztens a késések fordulnak elő, ha szeretné állítani a queuing alacsony értékre (1-5), az átviteli sebesség kezelésére replikák számának növeléséhez. Engedélyezheti az automatikus skálázás értékének replikákat száma alapján automatikusan legyen a terhelés. 

- Számítógép vagy a fürt használható több webszolgáltatás-végpontok?

   Abszolút. A szolgáltatások vagy végpontok 100 x futtathatja ugyanazon a fürtön. 

## <a name="next-steps"></a>Következő lépések
Első lépések a modell kezelése, lásd: [konfigurálásához modellben felügyeleti](deployment-setup-configuration.md).
