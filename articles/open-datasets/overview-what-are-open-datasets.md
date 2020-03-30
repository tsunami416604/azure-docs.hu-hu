---
title: Mik azok a nyitott adatkészletek? Válogatott nyilvános adatkészletek
titleSuffix: Azure Open Datasets
description: Ismerje meg az Azure Open datasets, válogatott adatkészletek a nyilvános tartományból, például az időjárás, a népszámlálás, a munkaszüneti napok és a hely a prediktív megoldások gazdagítása.
ms.service: open-datasets
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 11/04/2019
ms.openlocfilehash: fd5697f9c325dc4ad866c333ce1b20e008ebfa24
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73606159"
---
# <a name="what-are-azure-open-datasets-and-how-can-you-use-them"></a>Mik azok az Azure Open adatkészletek, és hogyan használhatja őket?

[Az Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) olyan nyilvános adatkészletek, amelyek segítségével forgatókönyv-specifikus funkciókat adhat a gépi tanulási megoldásokhoz a pontosabb modellek érdekében. Az open datasets a Microsoft Azure felhőjében található, és integrálva van az Azure Machine Learningbe, és könnyen elérhető az Azure Databricks és a Machine Learning Studio (klasszikus) számára. Az adatkészleteket API-kon keresztül is elérheti, és más termékekben, például a Power BI-ban és az Azure Data Factoryban is használhatja őket.

Az adatkészletek közé tartoznak az időjárásra, a népszámlálásra, az ünnepekre, a közbiztonságra és a helyre vonatkozó nyilvános adatok, amelyek segítenek a gépi tanulási modellek betanításában és a prediktív megoldások gazdagításában. Nyilvános adatkészleteit az Azure Open datasets-en is megoszthatja. 

![Az Azure Open Datasets összetevői](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>Válogatott, előkészített adatkészletek
Az Azure Open datasets által válogatott nyílt nyilvános adatkészletek gépi tanulási munkafolyamatokban való felhasználásra vannak optimalizálva. 

Az adatszakértők gyakran töltik idejük nagy részét az adatok tisztításával és előkészítésével a fejlett elemzések hez. A nyitott adatkészletek másolása az Azure-felhőbe történik, és előre feldolgozva időt takarít meg. Rendszeres időközönként adatokat kell lekérik a forrásokból, például egy FTP-kapcsolat a National Oceanic and Atmospheric Administration (NOAA). Ezután az adatokat strukturált formátumba elemzi a rendszer, majd megfelelő módon gazdagítja olyan funkciókkal, mint például az irányítószám vagy a legközelebbi meteorológiai állomás helye.

Az azure-beli felhőalapú számítási rendszer rel közösüzemelteti az adatkészleteket, amelyek megkönnyítik a hozzáférést és a kezelést.  

Az alábbiakban példákat talál a rendelkezésre álló adatkészletek. 

### <a name="weather-data"></a>Időjárási adatok
 
|Adatkészlet         | Notebookok     | Leírás                                    |
|----------------|---------------|------------------------------------------------|
|[NOAA integrált felületi adatok (ISD)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | Világszerte óránkénti időjárási adatok noaa a legjobb térbeli lefedettség Észak-Amerikában, Európában, Ausztráliában és Ázsia egyes részein. Naponta frissítve. |
|[NOAA globális előrejelzési rendszer (GFS)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | 15 napos amerikai óránkénti időjárás-előrejelzési adatok NOAA. Naponta frissítve. |

### <a name="calendar-data"></a>Naptáradatai

|Adatkészlet         | Notebookok     | Leírás                                    |
|----------------|---------------|------------------------------------------------|
|[Munkaszüneti](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | Világszerte munkaszüneti napok adatai, amelyek 1970 és 2099 között 41 országra vagy régióra vonatkoznak. Tartalmazza az országot, és azt, hogy a legtöbb ember fizetett-e szabadságot. |

## <a name="access-to-datasets"></a>Hozzáférés az adatkészletekhez  
Egy Azure-fiókkal a nyílt adatkészletek kód vagy az Azure-szolgáltatás felületén keresztül érhetők el. Az adatok az Azure felhőbeli számítási erőforrásaival együtt vannak elhelyezve a gépi tanulási megoldásban való használatra.  

A nyitott adatkészletek az Azure Machine Learning felhasználói felületén és SDK-n keresztül érhetők el. Az Open Datasets azure-jegyzetfüzeteket és Azure Databricks-jegyzetfüzeteket is biztosít, amelyekkel adatokat csatlakoztathat az Azure Machine Learninghez és az Azure Databrickshez. Adatkészletek is érhető el egy Python SDK-n keresztül. 

A nyílt adatkészletek eléréséhez azonban nincs szükség Azure-fiókra; bármilyen Python-környezetből elérheti őket a Sparkkal vagy anélkül.

## <a name="request-or-contribute-datasets"></a>Adatkészletek kérése vagy közreműködése

Ha nem találja a kívánt adatokat, írjon nekünk [adatkészlet igényléséhez](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) vagy [adathalmazhoz.](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) 

## <a name="next-steps"></a>További lépések
* [Mintajegyzetfüzet](samples.md)
* [Oktatóanyag: Regressziós modellezés NEW taxi adatokkal](/azure/machine-learning/service/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [Python SDK nyílt adatkészletekhez](/python/api/azureml-opendatasets/?view=azure-ml-py)
