---
title: Éles – Azure Machine Learning modellek telepítése |} Microsoft Docs
description: Ügyfélszoftverek központi telepítése a modellek üzemi környezetben, lehetővé téve a fontos szerep üzleti döntések meghozatalában.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: deguhath
ms.openlocfilehash: 0505d58261ec32015f8847b710791249f87f049b
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838292"
---
# <a name="deploy-models-in-production"></a>Modellek üzembe helyezése éles környezetben

Éles környezet lehetővé teszi, hogy a modell aktív szerepet játszanak üzleti. Egy telepített modell által használható üzleti döntéseket hozhat.

## <a name="production-platforms"></a>Éles platformok
Nincsenek különböző szempontok és platformokat a modellek üzembe helyezésre. Íme néhány lehetőség:


- [Az Azure Machine Learning modell telepítése](../desktop-workbench/model-management-overview.md)
- [Központi telepítési modell SQL-kiszolgálón](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)


>[!NOTE]
>Telepítés előtt egy rendelkezik modell pontozása késleltetése elég alacsony, éles környezetben használandó biztosítja.
>


>[!NOTE]
>A telepítési Azure Machine Learning Studio használatával, lásd: [központi telepítése az Azure Machine Learning webszolgáltatás](../studio/publish-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A / B tesztelés
Ha több modellek éles környezetben, ez hasznos lehet a végrehajtásához [A / B tesztelés](https://en.wikipedia.org/wiki/A/B_testing) teljesítmény modell összehasonlítására. 

 
## <a name="next-steps"></a>További lépések

Forgatókönyvek, amelyek azt mutatják, a folyamat lépései **meghatározott forgatókönyvek** is rendelkezésre állnak. Szerepel a listában, és kapcsolódik a miniatűr leírásokat a [példa forgatókönyvek](walkthroughs.md) cikk. Ezek bemutatják, hogyan lehet felhő, a helyszíni eszközök és szolgáltatások egyesítése munkafolyamat vagy csővezeték intelligens alkalmazás létrehozása. 
 


