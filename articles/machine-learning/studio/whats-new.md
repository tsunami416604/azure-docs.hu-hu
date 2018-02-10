---
title: What's new in Azure Machine Learning |} Microsoft Docs
description: "Az Azure Machine Learning elérhető új szolgáltatások."
services: machine-learning
documentationcenter: 
author: garyericson
manager: raymondl
editor: 
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: raymondl
ms.openlocfilehash: 0e97a8906bf0e5ea790725efbef16b883138c87a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="whats-new-in-azure-machine-learning"></a>Az Azure Machine Learning újdonságai

### <a name="the-march-2017-release-of-microsoft-azure-machine-learning-updates-provides-the-following-feature"></a>A Microsoft Azure Machine Learning frissítések március 2017 kiadása a következő funkcióval rendelkezik:



* Az Azure Machine Learning BES feladatok dedikált kapacitás

    Számítógép által használt feldolgozása tanulási Batch-készlet a [Azure Batch](../../batch/batch-technical-overview.md) ügyfél által felügyelt méretezési biztosít az Azure Machine Learning kötegelt végrehajtási szolgáltatás szolgáltatás. Készlet kötegfeldolgozási hozhat létre Azure Batch készletek amelyen kötegelt feladatok elküldéséhez és azok kiszámítható módon hajtható végre.

    További információkért lásd: [Azure Batch szolgáltatás gépi tanulási feladatok](dedicated-capacity-for-bes-jobs.md).


### <a name="the-august-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>A Microsoft Azure Machine Learning frissítések 2016 augusztusától kiadás az alábbi szolgáltatásokat biztosítja:
* Klasszikus webszolgáltatások mostantól kezelhető az új [Microsoft Azure Machine Learning webszolgáltatások](https://services.azureml.net/) portál, amely egy helyen kezelhetők a webes szolgáltatás minden elemét.    
  * Webszolgáltatás biztosító [kihasználtságának statisztikai adatai](manage-new-webservice.md).
  * Leegyszerűsíti az Azure Machine Learning távoli-kérelem hívásokon mintaadatok vizsgálatát.
  * Új kötegelt végrehajtási szolgáltatás tesztoldalt biztosít a minta adatok és a feladat elküldése előzményeit.
  * Egyszerűbb végpont-felügyeletet biztosít.

### <a name="the-july-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>A Microsoft Azure Machine Learning frissítések 2016. július kiadásában az alábbi szolgáltatásokat biztosítja:
* Webszolgáltatások most már felügyelt, Azure-erőforrások kezelhetők [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) felületek, ami a következő fejlesztéseket:
  * Új [REST API-k](https://msdn.microsoft.com/library/azure/Dn950030.aspx) telepítéséhez és kezeléséhez a Resource Manager-alapú webes szolgáltatások.
  * Új [Microsoft Azure Machine Learning webszolgáltatások](https://services.azureml.net/) portál, amely egy helyen kezelhetők a webes szolgáltatás minden elemét.
* A szerződés magában foglalja egy új előfizetés-alapú, több területi webes szolgáltatás telepítési modell erőforrás-kezelő használatával az erőforrás-kezelő erőforrás-szolgáltató kihasználva webszolgáltatások API-k alapján.
* Bevezeti az új [díjszabások](https://azure.microsoft.com/pricing/details/machine-learning/) tervezze meg az új erőforrás-kezelő RP használ számlázási felügyeleti képességek.
  * Most [a webszolgáltatás telepítése több régióba](how-to-deploy-to-multiple-regions.md) anélkül, hogy minden régióban egy előfizetés létrehozása.
* Webszolgáltatás biztosít [kihasználtságának statisztikai adatai](manage-new-webservice.md).
* Leegyszerűsíti az Azure Machine Learning távoli-kérelem hívásokon mintaadatok vizsgálatát.
* Új kötegelt végrehajtási szolgáltatás tesztoldalt biztosít a minta adatok és a feladat elküldése előzményeit.

Emellett a Machine Learning Studio léptethet érvénybe az új webes modell környezetébe, vagy továbbra is a hagyományos webes modell telepíteni. 

