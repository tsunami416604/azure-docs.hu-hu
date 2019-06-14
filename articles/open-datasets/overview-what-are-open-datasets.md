---
title: Mik azok a nyitott adatkészletek? Válogatott nyilvános adatkészletek
titleSuffix: Azure Open Datasets (preview)
description: További információ az Azure nyitott adatkészletek (előzetes verzió), amely készen áll a machine learning és elemzési megoldások használandó nyilvános összeválogatott adatkészletek. Az adatkészletek közé tartozik a nyilvános adatok, például az időjárás, a népszámlálási, a munkaszüneti napok és a prediktív megoldások gazdagabbá teheti a helyet.
ms.service: open-datasets
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 05/02/2019
ms.openlocfilehash: b01ad5a4fd8808c1e841ba2fb47ea06ece011010
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051034"
---
# <a name="what-are-azure-open-datasets-preview-and-how-can-you-use-them"></a>Mik azok a nyissa meg az Azure-adatkészletek (előzetes verzió), és hogyan is használhatja őket?

[Az Azure Open adatkészletek](https://azure.microsoft.com/services/open-datasets/) válogatott nyilvános adatkészleteket, amelyek a segítségével pontosabb modelleket a gépi tanulási megoldások forgatókönyv-specifikus szolgáltatások hozzáadása is. Nyissa meg az adatkészletek a felhőben a Microsoft Azure-ban, és azonnal elérhetők az Azure Databricks, a Machine Learning szolgáltatás és a Machine Learning Studióban. Az adatkészletek elérése a API-kon keresztül is, és egyéb termékek, például a Power BI és az Azure Data Factory használni őket.

Adatkészletek időjárás, a népszámlálási, a munkaszüneti napok, a közbiztonság szabadon adatait tartalmazzák, és a helyre, amely segít machine learning-modellek betanításához, és a prediktív megoldások bővítését. A nyilvános adatkészleteket az Azure Open adatkészleteken is megoszthatja. 

![Az Azure Open adatkészletek összetevők](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>Válogatott, előkészített adatkészletek
Válogatott megnyitott nyilvános adatkészleteket az Azure nyissa meg a adatkészletek mértékét a machine learning-munkafolyamatok vannak optimalizálva. 

Az adatszakértők gyakran költségek a legtöbb idejüket a tisztítás és a fejlett analitikai adatok előkészítése. Nyissa meg az adatkészletek az Azure felhőbe másolják és üzenetfájlrekordok időt takaríthat meg. Rendszeres időközönként adatok strukturált formátumban értelmezni az együttműködési és légköri felügyeleti NOAA FTP-kapcsolatot, mint például a forrásból kéri le, és megfelelő, például az irányítószám vagy helyét, majd bővített a legközelebbi időjárásjelző.

Adatkészlet lehet cohosted felhőalapú számítási az Azure és a hozzáférés és egyszerűbb kezelését.  

Az alábbiakban példákat az adatkészletek rendelkezésre. 

### <a name="weather-data"></a>Időjárási adatok
 
|Adathalmaz         | Notebookok     | Leírás                                    |
|----------------|---------------|------------------------------------------------|
|[Integrált NOAA Surface-adat (ISD)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | Világszerte óránkénti a NOAA időjárási adatai Észak-Amerika, Európa, Ausztrália és Ázsia részei a legjobb térbeli lefedettséggel rendelkező. Naponta frissíti. |
|[Globális NOAA előrejelzési System (GFS)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | 15 napos USA óránkénti időjárás-előrejelzés származó adatok NOAA. Naponta frissíti. |

### <a name="calendar-data"></a>Naptári adatok

|Adathalmaz         | Notebookok     | Leírás                                    |
|----------------|---------------|------------------------------------------------|
|[Munkaszüneti](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | 41 országokból vagy régiókból származó 1970 2099, amely világszerte munkaszüneti adatok. Ország és hogy a legtöbb ember fizetett idő tartalmaz. |

## <a name="access-to-datasets"></a>Az adatkészletek a hozzáférést  
Egy Azure-fiókjával kódot használó nyílt adatkészletek elérése vagy az Azure-on keresztül szolgáltatás felület is. Az adatok a közös elhelyezésű, az Azure felhőalapú számítási erőforrások használható a machine learning-megoldását.  

Nyissa meg az adatkészletek adatok csatlakozhat az Azure Machine Learning szolgáltatás és az Azure Databricks használatával Azure notebookok és az Azure Databricks-jegyzetfüzetek biztosít. Az adatkészletek a Python SDK-n keresztül is elérhető. 

Azonban nem kell egy Azure-fiók eléréséhez nyissa meg az adatkészletek; bármely Python-környezet nélkül, vagy Spark nélküli elérheti őket.

## <a name="request-or-contribute-datasets"></a>Kérelem vagy adatkészletek közreműködés

Ha nem találja az adatok kívánt, e-mail-címre való [adatkészlet kérelem](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) vagy [adatkészlet közreműködés](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A). 

## <a name="next-steps"></a>További lépések
* [Minta notebook](samples.md)
* [Oktatóanyag: Modellezés NY-i taxik adatait az regresszió](tutorial-opendatasets-automl.md)
* [Python SDK nyílt adatkészletek esetében](https://aka.ms/open-datasets-api)
