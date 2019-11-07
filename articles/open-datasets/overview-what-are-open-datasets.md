---
title: Mik azok a nyitott adatkészletek? Válogatott nyilvános adatkészletek
titleSuffix: Azure Open Datasets
description: Ismerje meg az Azure Open-adatkészleteket, a nyilvános tartományból származó kurátori adatkészleteket, például az időjárási, a népszámlálási, a munkaszüneti és a tartózkodási helyet a prediktív megoldások gazdagítása érdekében.
ms.service: open-datasets
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 11/04/2019
ms.openlocfilehash: fd5697f9c325dc4ad866c333ce1b20e008ebfa24
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606159"
---
# <a name="what-are-azure-open-datasets-and-how-can-you-use-them"></a>Mik azok az Azure Open-adatkészletek, és hogyan használhatók?

Az [Azure Open-adatkészletek](https://azure.microsoft.com/services/open-datasets/) olyan beszerzett nyilvános adatkészletek, amelyekkel pontosabb modelleket adhat hozzá a gépi tanulási megoldásokhoz. A nyílt adatkészletek a felhőben vannak Microsoft Azure és integrálva vannak a Azure Machine Learningba, és könnyen elérhetők Azure Databricks és Machine Learning Studio (klasszikus) számára. Az adatkészleteket API-kon keresztül is elérheti, és más termékekben, például Power BI és Azure Data Factory is használhatja.

Az adatkészletek olyan nyilvános tartományi adatokat foglalnak magukban, mint az időjárás, a népszámlálás, az ünnepnapok, a közbiztonság és a gépi tanulási modellek betanítását és a prediktív megoldások bővítését segítő hely. Az Azure Open-adatkészleteken is megoszthatja nyilvános adatkészleteit. 

![Azure Open-adatkészletek összetevői](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>Kurátorok által előkészített adathalmazok
Az Azure Open-adatkészleteket használó, nyitott nyilvános adatkészletek a gépi tanulási munkafolyamatokban való felhasználásra vannak optimalizálva. 

Az adatszakértők általában az idő nagy részében elköltik az adattisztítást, és előkészítik a speciális elemzéseket. A megnyitott adatkészletek az Azure-felhőbe kerülnek, és a rendszer elődolgozta az idő megtakarítását. A rendszeres időközönként az adatok a forrásokból származnak, például egy FTP-kapcsolattal az országos óceáni és légköri adminisztráció (NOAA) felé. Ezt követően a rendszer strukturált formátumban elemzi az adatelemzéseket, majd a megfelelő funkciókkal gazdagítja a következő funkciókat: ZIP-kód vagy a legközelebbi meteorológiai állomás helye.

Az adatkészletek az Azure felhőalapú számítási feladatokkal vannak ellátva, így egyszerűbbé válik a hozzáférés és a manipuláció.  

A következő példák az elérhető adatkészletekre mutatnak. 

### <a name="weather-data"></a>Időjárási adatszolgáltatások
 
|Adatkészlet         | Notebookok     | Leírás                                    |
|----------------|---------------|------------------------------------------------|
|[NOAA integrált Surface-adatszolgáltatások (ISD)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | Világszerte óránkénti időjárási adatok a NOAA-ből a legjobb térbeli lefedettséggel Észak-Amerika, Európa, Ausztrália és Ázsia egyes részeiben. Naponta frissül. |
|[NOAA globális előrejelzési rendszere (GFS)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | 15 napos US óránkénti időjárás-előrejelzési adat a NOAA-től. Naponta frissül. |

### <a name="calendar-data"></a>Naptáradatok

|Adatkészlet         | Notebookok     | Leírás                                    |
|----------------|---------------|------------------------------------------------|
|[Ünnepnapok](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | Világszerte nyilvános ünnepnapok adatai, amelyek 41 országokat vagy régiókat mutatnak be 1970 és 2099 között. Magában foglalja az országot és azt, hogy a legtöbb ember fizetett-e időt. |

## <a name="access-to-datasets"></a>Adatkészletek elérése  
Egy Azure-fiókkal a nyílt adatkészleteket kód vagy az Azure-szolgáltatás felületén keresztül érheti el. Az adatok az Azure felhőalapú számítási erőforrásaival együtt használhatók a gépi tanulási megoldásban való használatra.  

A megnyitott adatkészletek a Azure Machine Learning felhasználói felületen és az SDK-n keresztül érhetők el. A nyílt adatkészletek Azure Notebooks és Azure Databricks jegyzetfüzeteket is biztosít, amelyekkel az adatokat Azure Machine Learninghoz és Azure Databrickshoz is kapcsolódhat. Az adatkészletek egy Python SDK-n keresztül is elérhetők. 

Azonban nincs szükség Azure-fiókra a nyitott adatkészletek eléréséhez. ezeket bármilyen Python-környezetből elérheti Spark használatával vagy anélkül.

## <a name="request-or-contribute-datasets"></a>Adatkészletek kérése vagy hozzájárulása

Ha nem találja a kívánt adatokat, küldje el nekünk e-mailben, hogy [adatkészletet kérjen](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) , vagy [egy adatkészletet](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A). 

## <a name="next-steps"></a>További lépések
* [Minta notebook](samples.md)
* [Oktatóanyag: regressziós modellezés a New York-i taxival](/azure/machine-learning/service/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [Python SDK a nyílt adatkészletekhez](/python/api/azureml-opendatasets/?view=azure-ml-py)
