---
title: "Multi-földrajzi súgó dokumentumai |} Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre munkaterületet, és egy webszolgáltatás-bővítmény közzététele az Azure-régióban a Dél központi az Amerikai Egyesült Államok (SCUS) a különböző Azure-régiót."
services: machine-learning
documentationcenter: 
author: tedway
manager: jhubbard
editor: rmca14
tags: 
ms.assetid: ed0ca8a8-fa53-4e56-b824-2d7e44641967
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/6/2017
ms.author: tedway; neerajkh
ms.openlocfilehash: e4941ccf8c6d7a0c77527e9c1d722bc3a770114a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="multi-geo-help-documentation"></a>Több régióra kiterjedő súgódokumentáció
Ez a cikk ismerteti, hogyan hozhat létre munkaterületet, és egy webszolgáltatás-bővítmény közzététele a különböző Azure-régiók.  A [Azure termékek régió lap](https://azure.microsoft.com/en-us/regions/services/) sorolja fel a régiókban, ahol az Azure Machine Learning áll rendelkezésre.

## <a name="create-a-workspace"></a>Munkaterületek létrehozása
1. Jelentkezzen be a klasszikus Azure portálon.
2. Kattintson a **+ új** > **ADATSZOLGÁLTATÁSOK** > **gépi tanulás** > **gyors létrehozás**.  A **hely** válassza ki, mint egy másik régióban **Délkelet-Ázsia**.
   ![1 kép multi-földrajzi Súgó][1]
3. Válassza ki a munkaterületet, és kattintson a **bejelentkezés az ML Studio**.
   ![2 kép multi-földrajzi Súgó][2]
4. Most már rendelkezik egy munkaterület egy másik régióban, csakúgy, mint bármely más munkaterület használhat. A munkaterületek között válthat, keresse meg a képernyő jobb felső sarkában. Kattintson a legördülő listában, válassza ki azt a régiót, és válassza ki a munkaterületen. Helyi a munkaterület-régióban.  Például a létrehozott egy olyan munkaterületről, webszolgáltatások mindegyikét lesz ugyanabban a régióban található, a munkaterületen.
   ![3 kép multi-földrajzi Súgó][3]

## <a name="open-an-experiment-from-gallery"></a>Egy kísérlet megnyitásához a gyűjteményből
Egy kísérlet megnyitásához a katalógusból, ha át kívánja másolni a kísérletet, és melyik régióban is kiválaszthatja.

![4 kép multi-földrajzi Súgó][4a]

## <a name="web-service-management"></a>Rendszerfelügyeleti webszolgáltatás
Programozott módon webes szolgáltatások kezelésére, többek között az átképezési, a régióspecifikus cím használata:

* https://asiasoutheast.Management.azureml.NET
* https://europewest.Management.azureml.NET

### <a name="things-to-note"></a>Ügyeljen a következőkre
1. Kísérletek között munkaterületek, így ugyanahhoz a régióhoz tartozó csak másolhatja. Ha másolja kísérlet között különböző régiókban munkaterületek, használhatja a [PowerShell](http://aka.ms/amlps) parancsmag [ *másolási-AmlExperiment* ](https://github.com/hning86/azuremlps/blob/master/README.md#copy-amlexperiment) hajthatja végre, hogy. Egy másik megoldás, hogy a kísérletben a gyűjteményelem közzététele fel nem sorolt módban, majd nyissa meg a munkaterület más régióban.
2. A régió választó csak egy régió munkaterületek jelennek egyszerre.  
3. Egy szabad munkaterület vagy Vendég Access (névtelen) munkaterületen létrejön és Dél központi USA tárolva  
4. Egy olyan munkaterületről, Délkelet-Ázsiában található telepített webszolgáltatások is lehet üzemeltetni Délkelet-Ázsia.  

## <a name="more-information"></a>További információ
Tegyen fel kérdést az a [Azure Machine Learning fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning).

<!--Image references-->
[1]: ./media/multi-geo/multi-geo_1.png
[2]: ./media/multi-geo/multi-geo_2.png
[3]: ./media/multi-geo/multi-geo_3.png
[4a]: ./media/multi-geo/multi-geo_4a.png
