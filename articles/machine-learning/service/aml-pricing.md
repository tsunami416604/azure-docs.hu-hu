---
title: Árak és számlázás az Azure Machine Learning-szolgáltatások
description: Ez a cikk tartalmazza az árak és számlázás Azure Machine Learning előzetes verziójú funkciók a gyakori kérdésekre.
services: machine-learning
author: j-martens
ms.author: jmartens
manager: cgronlund
ms.reviewer: mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 03/30/2018
ms.openlocfilehash: 5e057f3fe4a4ff06e0acac29b3dcd11fa901fc40
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="pricing-and-billing-for-azure-machine-learning-services"></a>Árak és számlázás Azure Machine Learning szolgáltatások

Részletes, vagy egy minta számlázási megtekintéséhez keresse fel a [Azure díjszabása](https://azure.microsoft.com/pricing/details/machine-learning-services/).  

Az alábbiakban a számlázással és a díjszabással körül leggyakrabban feltett kérdésekre.

## <a name="can-i-use-azure-machine-learning-for-free-during-preview"></a>Használható Azure Machine Learning ingyenes előzetes?    

Az Azure Machine Learning-munkaterület alkalmazást az Azure-előfizetők ingyenes.

A kísérleti szolgáltatás és a modell kezelése kínál szabad rétegek mellett fizetett szinteken, nyilvános előzetes kedvezménnyel érhető el.

## <a name="am-i-charged-based-on-how-many-experiments-i-run"></a>Vagyok I felszámított futtattam hány kísérletek alapján?

Nem. A kísérletezés szolgáltatásban korlátlan számú kísérlet elvégezhető, a fizetendő díjat pedig kizárólag a felhasználók száma határozza meg. A kísérletezéshez felhasznált számítási erőforrásokért külön díjat számolunk fel.  Javasoljuk, hogy a megoldás a legjobb modell elérésének több kísérletek végrehajtása. 

## <a name="am-i-charged-based-on-how-many-times-my-web-services-is-called"></a>Vagyok I felszámított alapján a webszolgáltatások nevezik hány alkalommal?

Nem. Webszolgáltatások hívhatók szükséges, a számlázási modell kezelése hatással nélkül gyakorisággal. Az üzemelő példányok méretezését teljes egészében Ön határozhatja meg az alkalmazások szükségleteihez igazodva.

## <a name="how-can-i-scale-the-units-i-purchase-in-the-azure-machine-learning-model-management"></a>Hogyan méretezheti a egység az Azure Machine Learning modell felügyeleti vásárlása?

Módosíthatja a egységek száma felfelé vagy lefelé, az Azure-portálon vagy a parancssori felület használatával. 

## <a name="what-does-a-bill-look-like"></a>Mi a számlázási megjelenését?

Váltók naponta előállítása. A számlázás tekintetében a nap kezdete az UTC szerinti éjfél. A számlázás havi rendszerességgel történik. Külön díjak sem merülnek fel a bármely Azure-szolgáltatások, az Azure Machine Learning együtt használni. Díjak lehetnek, azonban nem csak: 
- Számítási díjakat
- HDInsight
- Azure Container Service
- Azure Container Registry 
- Azure Blob Storage
- Application Insights
- Azure Key Vault
- Visual Studio Team Services
- Azure Event Hub
- Az Azure Stream Analytics a a további részleteket, vagy egy minta számlázási megtekintéséhez keresse fel a [Azure díjszabása](https://azure.microsoft.com/pricing/details/machine-learning-services/). 
