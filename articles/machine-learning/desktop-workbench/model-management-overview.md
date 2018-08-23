---
title: Az Azure Machine Learning Modellkezelés fogalmi áttekintése |} A Microsoft Docs
description: Ez a dokumentum az Azure Machine Learning Modellkezelés fogalmakat ismerteti.
services: machine-learning
author: hjerezmsft
ms.author: hjerez
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 041f7147f171514d941555ff2f6144bac2062b06
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "42056699"
---
# <a name="azure-machine-learning-model-management"></a>Azure Machine Learning Modellkezelés

Az Azure Machine Learning a Modellkezelés lehetővé teszi a machine-learning munkafolyamatok és a modellek üzembe helyezheti és kezelheti. 

Modellkezelési képességeket biztosít:
- Modell-verziókezelés
- Követési modellek éles környezetben
- Az éles környezetben keresztül AzureML számítási környezetet a modellek üzembe helyezéséhez [az Azure Container Service](https://azure.microsoft.com/services/container-service/) és [Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- A modellek a Docker-tárolók létrehozása, és helyi tesztelés
- Automatizált modellbetanítás
- A rögzítés modell telemetriai adatokat a gyakorlatban is használható. 

Az Azure Machine Learning Modellkezelés a modell verzióját egy jegyzéket biztosít. Automatizált munkafolyamatok csomagolás és a REST API-k, Machine Learning-tárolók üzembe helyezése is tartalmazza. A modellek és a futtatókörnyezeti függőségek a Linux-alapú Docker-tárolót az előrejelzési API vannak csomagolva. 

Az Azure Machine Learning COMPUTE számítási Környezeteihez beállítását, és a modellek üzemeltetéséhez skálázható fürtök kezelése érdekében. A számítási környezet alapján történik az Azure Container Service szolgáltatásait. Az Azure Container Service biztosítja az automatikus kitettség Machine Learning API-k REST API-végpontokon a következő funkciókkal:

- Hitelesítés
- Terheléselosztás
- Az automatikus horizontális felskálázás
- Titkosítás

Az Azure Machine Learning Modellkezelés biztosítja ezeket a képességeket a parancssori felület, API és az Azure Portalon keresztül. 

Az Azure Machine Learning modellkezelés használja a következő információkat:

 - Modell-fájl vagy könyvtár a modell fájljai
 - Felhasználó által létrehozott Python-fájlt egy pontozó függvény modell megvalósítása
 - Conda függőségi fájl futtatókörnyezeti függőségek listázása
 - Futásidejű környezet választási lehetőség, és 
 - A sémafájl API paraméterek 

Ezt az információt használja a következő műveletek végrehajtása során:

- A modell regisztrálása
- A jegyzékfájl létrehozása, amely használatos, amikor egy tároló létrehozásához
- A Docker tároló rendszerképének létrehozása
- Üzembe helyezni egy tárolót az Azure Container Service-ben
 
Az alábbi ábra a modellek regisztrálása és a fürtben üzembe helyezett áttekintése látható. 

![](media/model-management-overview/modelmanagement.png)

## <a name="create-and-manage-models"></a>Hozzon létre, és a modellek kezelése 
Modellek regisztrálhatja az Azure Machine Learning Modellkezelés, a nyomon követési modell verzióit éles környezetben. Könnyű megismételhetőség és irányítása a szolgáltatás minden függőségeket és társított információk rögzíti. Mélyebb betekintést a teljesítmény, a modell telemetriai adatokat a megadott SDK használatával rögzítheti. Modell telemetriai adatokat a felhasználó által megadott tárolási archiválja a rendszer. A modell telemetriát később használható modellek teljesítményének elemzése, átképezési és üzleti következtetéseket.

## <a name="create-and-manage-manifests"></a>Hozzon létre és kezelheti a jegyzékek 
Modellek éles üzembe helyezés további összetevőkhöz szükséges. A rendszer lehetővé teszi, hogy hozzon létre egy jegyzéket, amely magában foglalja a modell, a függőségeket, a következtetésekhez parancsfájl (más néven pontozó szkript), a mintaadatokat, a séma stb. A jegyzékfájl létrehozása egy Docker-tároló rendszerképét a recept funkcionál. Vállalatok jegyzékfájl automatikus létrehozása, hozzon létre különböző verzióit, és a jegyzékek kezelése. 

## <a name="create-and-manage-docker-container-images"></a>Hozzon létre és kezelheti a tárolólemezképeket a Docker 
Az előző lépésben a jegyzék használatával hozhat létre tárolórendszerképeket Docker-alapú saját környezetükben lévő. A tárolóalapú, Docker-alapú lemezképek rugalmasan futtathatja a lemezképek a következő számítási környezetet adja meg a vállalatok számára:

- [Az Azure Container Service Kubernetes-alapú](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- A helyszíni tárolószolgáltatások
- Fejlesztési környezetek
- IoT-eszközök

A Docker-alapú tárolóalapú lemezképek olyan önálló az előrejelzések létrehozásához szükséges az összes szükséges függőséget. 

## <a name="deploy-docker-container-images"></a>Docker-tárolórendszerképekhez üzembe helyezése 
Az Azure Machine Learning Modellkezelésben az ML COMPUTE számítási környezet által felügyelt Azure Container Service Docker-alapú tárolólemezképeket egyetlen paranccsal telepítheti. Ezeket az üzemelő példányokat jönnek létre az előtér-kiszolgálót, amely a következő szolgáltatásokat biztosítja:

- Közel valós idejű előrejelzéseket ipari méretekben
- Terheléselosztás
- Automatikus skálázás Machine Learning-végpont
- Hitelesítési API
- API swagger-dokumentumok

Szabályozhatja, hogy a központi telepítési méretezés és a telemetriai adatok segítségével a következő beállításokat:

- Rendszer-naplózási és modell telemetriai minden webes szolgáltatási szint. Ha engedélyezve van, minden stdout-naplókat a rendszer streamként továbbítja [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Modell telemetriai adatokat archiválja a rendszer az Ön által megadott storage-ban. 
- Automatikus méretezést és egyidejűségi korlátok. Ezek a beállítások a meglévő fürt a terhelés alapján üzembe helyezett tárolók száma automatikusan növelhető. Akkor is szabályozhatja az átviteli sebesség és a konzisztencia előrejelzési késés.

## <a name="consumption"></a>Használat 
Az Azure Machine Learning Modellkezelési REST API-t hoz létre az üzembe helyezett modelljét, és a swagger-dokumentumok. Ezzel a hívással a REST API-k API-val kulcsra, és modellezheti a bemeneti adatok az előrejelzés beolvasása a sor üzleti alkalmazások részeként üzembe helyezett modellnél használhatnak fel. A mintakód érhető el a GitHub, a Java nyelven [Python](https://github.com/CortanaAnalyticsGallery-Int/digit-recognition-cnn-tf/blob/master/client.py), és a C# REST API-k meghívására szolgáló. Az Azure Machine Learning minta felügyeleti parancssori ezek REST API-khoz egy egyszerűbb megoldást kínál. Az API-k egyetlen parancssori paranccsal, egy swagger-kompatibilis alkalmazások vagy a curl használatával használhatja fel. 

## <a name="retraining"></a>Átképezési 
Az Azure Machine Learning Modellkezelés biztosít, amellyel a modellek újratanítása API-k. Az API-k segítségével is a modell frissített verzióval frissíti a meglévő telepítések. Az adatelemzési munkafolyamathoz részeként, hozza létre újra a modellt a Kísérletezési környezetében. Ezután regisztrálja a modellt a Modellkezelés, és frissítse a meglévő telepítések. Frissítések történik, egyetlen frissítési CLI-paranccsal. Az UPDATE parancs frissíti a meglévő telepítések az API URL-cím vagy a kulcs módosítása nélkül. A modell felhasználása az alkalmazások továbbra is minden olyan kód módosítása nélkül működnek, és megkezdődik az új modell használatával jobb javaslatok érdekében.

Az alábbi ábrán a kész munkafolyamat ezeket a fogalmakat ismertető rögzített:

![](media/model-management-overview/modelmanagementworkflow.png)

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK) 
- **Milyen adatok típusok támogatottak? Adhat NumPy tömbök is át bemenetként közvetlenül web Service?**

   Ha meg van adva a sémafájl generate_schema SDK használatával létrehozott, majd adhat át NumPy és/vagy Pandas DF. Is átadhat a szerializálható JSON bemenetet. Kép, valamint bináris kódolású karakterláncként adhat át.

- **Nem a web service támogatja a több bemenet és elemezni a különböző adatbevitelek?**

   Igen, több bemenet egy szótár, egy JSON-kérelmi csomagolva is igénybe vehet. Minden egyes bemenet lenne felel meg egyetlen egyedi szótár kulcsot.

- **A hívás a webes kérelem által aktivált szolgáltatás blokkoló hívást vagy egy aszinkron hívás?**

   Szolgáltatás valós idejű lehetőséggel a parancssori felület vagy az API részeként jött létre, majd, hogy blokkolja a/synchronního volání. Valós idejű gyors is várható. Bár az ügyfél oldalán meg lehet hívni az aszinkron HTTP-kódtár használatával elkerülése érdekében a szál blokkolása az ügyfél.

- **Hány kérésnek is a web service egyidejűleg kezelni?**

   Attól függ, a fürt és a web service skálázást. Ki lehet terjeszteni a 100-szor replikák szolgáltatást, és ezután képes kezelni sok kérelem egyidejűleg. Beállíthatja az egyidejű kérések maximális replikánként szolgáltatás átviteli sebesség növelése érdekében.

- **Hány kérésnek is a web service várólistára helyezését?**

   A nem konfigurálható. Alapértelmezés szerint egyetlen replikánként ~ 10 értékre van állítva, de Ön is növelheti/csökkentheti, az alkalmazás igényeinek megfelelően. Általában, növekvő sorba állított kérelmek száma növeli a szolgáltatás átviteli sebesség, de lehetővé teszi az késleltetések még rosszabb, magasabb percentilisei. És konzisztens az késleltetések, előfordulhat, hogy beállítja az üzenetsor-kezelési alacsony értékre (1-5), és az átviteli sebesség kezelésére replikák számának növelése. Az automatikus skálázás ahhoz, hogy a módosításával replikák száma alapján automatikusan terhelés is bekapcsolhatja. 

- **Ugyanazon fürt vagy gép használható több webszolgáltatás-végpontok?**

   Abszolút. 100-szor szolgáltatások/végpontok futtathatja ugyanazon a fürtön. 

## <a name="next-steps"></a>További lépések
Első lépések a Modellkezelés, lásd: [Modellkezelési konfigurálása](deployment-setup-configuration.md).
