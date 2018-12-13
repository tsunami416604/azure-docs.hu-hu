---
title: Újdonságok – Azure Machine Learning Studióban |} A Microsoft Docs
description: Az Azure Machine Learning Studióban elérhető új szolgáltatások.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2018
ms.openlocfilehash: 456e95540a69b4b7b504e98296f917db2826834e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53082101"
---
# <a name="whats-new-in-azure-machine-learning-studio"></a>Mi az új Azure Machine Learning studióban?

## <a name="october-2018"></a>2018. október

Az R nyelv motor a [R-szkript végrehajtása](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script) modul új R modul verzióját – a Microsoft R Open (MRO) 3.4.4-e hozzáadni. MRO 3.4.4 nyílt forráskódú CRAN R 3.4.4 alapul, és ezért kompatibilis az r adott verziójával működik-csomagok  További információ a támogatott R-csomagok a cikk "[Azure Machine Learning Studio által támogatott R-csomagok](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning#bkmk_List)".

## <a name="march-2017"></a>2017. március 
Ebben a kiadásban a Microsoft Azure Machine Learning-frissítések biztosítja a következő szolgáltatást:

* Az Azure Machine Learning BES feladatok dedikált kapacitás

    A Machine Learning Batch-készlet használ feldolgozása a [Azure Batch](../../batch/batch-technical-overview.md) szolgáltatást, hogy az ügyfél által felügyelt méretezési adja meg az Azure Machine Learning kötegelt végrehajtási szolgáltatás. Batch-készlet feldolgozási, amelyen a batch-feladatok elküldése és kiszámítható módon hajtható végre őket az Azure Batch-készletek létrehozását teszi lehetővé.

    További információkért lásd: [Machine Learning-feladatok Azure Batch szolgáltatás](dedicated-capacity-for-bes-jobs.md).


## <a name="august-2016"></a>2016. augusztus 
Ebben a kiadásban a Microsoft Azure Machine Learning-frissítések az alábbi szolgáltatásokat biztosítja:
* Klasszikus webszolgáltatásoknál ekkortól felügyelhető az új [a Microsoft Azure Machine Learning webszolgáltatások](https://services.azureml.net/) portál, amely egyetlen helyen, a webszolgáltatás minden szempontjának kezeléséhez biztosít.    
  * Webszolgáltatás biztosító [kihasználtságának statisztikai adatai](manage-new-webservice.md).
  * Egyszerűbbé teszi a tesztelés az Azure Machine Learning távoli-kérelem hívásokat mintaadatokkal.
  * Kötegelt végrehajtási szolgáltatás új tesztoldalt révén a mintául szolgáló adatokat és a feladat beküldése előzményei.
  * Végpont könnyebb kezelést biztosít.

## <a name="july-2016"></a>2016. július 
Ebben a kiadásban a Microsoft Azure Machine Learning-frissítések az alábbi szolgáltatásokat biztosítja:
* Webszolgáltatások csoportként kezelhető Azure-erőforrások kezelhetők [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) felületek, ami lehetővé teszi az alábbi fejlesztéseket tartalmazzák:
  * Nincsenek új [REST API-k](https://msdn.microsoft.com/library/azure/Dn950030.aspx) üzembe helyezése és kezelése a Resource Manager-alapú webszolgáltatások.
  * Egy új [a Microsoft Azure Machine Learning webszolgáltatások](https://services.azureml.net/) portál, amely egyetlen helyen, a webszolgáltatás minden szempontjának kezeléséhez biztosít.
* Magában foglalja egy új előfizetés-alapú, többrégiós web service telepítési modell használatával a Resource Manager-alapú webszolgáltatások kihasználva a Resource Manager erőforrás-szolgáltató API-k.
* Vezet be új [díjszabások](https://azure.microsoft.com/pricing/details/machine-learning/) és felügyeleti képességek az új Resource Manager-RP használatával a számlázási csomag.
  * Mostantól [a webszolgáltatás üzembe helyezése több régióban](how-to-deploy-to-multiple-regions.md) anélkül, hogy minden régióban hozzon létre egy előfizetést.
* Webszolgáltatás biztosít [kihasználtságának statisztikai adatai](manage-new-webservice.md).
* Egyszerűbbé teszi a tesztelés az Azure Machine Learning távoli-kérelem hívásokat mintaadatokkal.
* Kötegelt végrehajtási szolgáltatás új tesztoldalt révén a mintául szolgáló adatokat és a feladat beküldése előzményei.

A Machine Learning Studio emellett frissítve lett, hogy az új webes szolgáltatás modellre telepíthet, vagy továbbra is a klasszikus modellt a Web service telepítése. 

