---
title: Használja az Azure Machine Learning webszolgáltatás-paramétereket |} Microsoft Docs
description: Hogyan használható az Azure Machine Learning webszolgáltatás-paramétereket a webszolgáltatás elérésekor a modell működésének módosítása céljából.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.openlocfilehash: 91b3c9df8a7fd0e1abb79c21b1e1d833e57c24d5
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835926"
---
# <a name="use-azure-machine-learning-web-service-parameters"></a>Az Azure Machine Learning webszolgáltatás paramétereinek használata
Az Azure Machine Learning webszolgáltatás közzététele, amely konfigurálható paraméterekkel lehetővé tevő modulokat tartalmaz a kísérlet hozta létre. Bizonyos esetekben érdemes lehet a modul megváltozzon, miközben fut a webes szolgáltatás. *Webszolgáltatási paramétereket* engedélyezi, hogy a feladat végrehajtásához. 

Ilyenek például beállítását a [és adatokat importálhat] [ reader] modul, hogy a felhasználó a közzétett webes szolgáltatás adhat meg egy másik adatforráshoz, a webszolgáltatás elérésekor. Vagy konfigurálása a [adatok exportálása] [ writer] modul, hogy egy másik cél adható meg. Más például bitjei számának módosítása a [Szolgáltatáskivonatolás] [ feature-hashing] modul vagy száma szükséges szolgáltatásokat a [szűrő-alapú szolgáltatás kiválasztása] [ filter-based-feature-selection] modul. 

Állítsa be a webszolgáltatás-paramétereket, és rendelje hozzá őket egy vagy több modulja paraméter a kísérletben, és megadhatja, hogy azok kötelező vagy választható. A felhasználó a webszolgáltatás lehet majd adja meg ezeket a paramétereket, amikor azok a webszolgáltatás hívására. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="how-to-set-and-use-web-service-parameters"></a>Állítsa be, és webszolgáltatás-paramétereket használata
A webszolgáltatási paraméter a paraméter egy modul melletti ikonra kattint, majd válassza a "Web service paraméter beállítása" határozza meg. Ez létrehoz egy új webszolgáltatási paraméter, és kapcsolódik az adott modul paraméter. Majd amikor a webes szolgáltatás, a felhasználó érték adható meg a webszolgáltatási paraméter, illetve az alkalmazásának a modul paraméterhez.

A webszolgáltatási paraméter határozza meg, miután a rendelkezésére áll a kísérletben más modul paraméter. Egy webszolgáltatási egy modul egy paraméterének társított paraméter megadása esetén használható, hogy ugyanazon webszolgáltatási paraméter bármely más modul mindaddig, amíg a paraméter a ugyanolyan típusú értéket vár. Például ha a webszolgáltatási paraméter egy numerikus értéket, majd csak használat modul paraméter, amely egy numerikus értéket várt. A felhasználó a webes szolgáltatás paraméter beállít egy értéket, akkor lépnek érvénybe az összes társított modul paraméterei.

Eldöntheti, hogy a webes paraméter alapértelmezett értéket adjon meg. Ha így tesz, a paraméter megadása esetén a felhasználó a webszolgáltatás nem kötelező. Ha nem ad meg alapértelmezett értéket, majd a felhasználó szükséges adjon meg egy értéket, ha hozzáfér a webes szolgáltatás.

A webszolgáltatás API dokumentációja tartalmaz adatokat a szolgáltatás felhasználó programozott módon megadása a webszolgáltatási paraméter, a webszolgáltatás elérésekor.

> [!NOTE]
> Az API dokumentációjának klasszikus webszolgáltatáshoz keresztül valósul meg a **API súgólap** hivatkozásra a webszolgáltatás **IRÁNYÍTÓPULT** a Machine Learning Studióban. Egy új webszolgáltatás-bővítmény API dokumentációjában keresztül valósul meg a [Azure Machine Learning webszolgáltatások](https://services.azureml.net/Quickstart) portált a **felhasználás** és **Swagger API** a Web pages a szolgáltatás.
> 
> 

## <a name="example"></a>Példa
Tegyük fel, tételezzük fel, hogy az a kísérlet van egy [adatok exportálása] [ writer] modul, amely adatokat küld az Azure blob Storage tárolóban. A webszolgáltatási nevű, "Blob elérési út" paraméter fogunk meghatározni, amely lehetővé teszi, hogy a szolgáltatás felhasználó elérési útjának módosítása a blob-tároló, a szolgáltatás elérésekor.

1. A Machine Learning Studióban, kattintson a [adatok exportálása] [ writer] modul rá kattintva jelölje ki. A Tulajdonságok panelen jobb oldalán a kísérletvászonra tulajdonságai jelennek meg.
2. Adja meg a tárolási típusát:
   
   * A **adja meg az adatok cél**, jelölje be az "Azure Blob Storage".
   * A **adja meg a hitelesítési típus**, jelölje be az "Account".
   * Írja be az Azure blob storage fiók adatait. 

3. Kattintson a jobb oldalán ikonra a **elérési útját a blob-tároló paraméter-tól kezdődően**. Néz ki:
   
   ![Webes szolgáltatás paraméter ikon][icon]
   
   Válassza ki a "Web service paraméter beállítása".
   
   Bejegyzés hozzáadása az **webszolgáltatás-paramétereket** "Blob-tároló kezdetű elérési" nevű Tulajdonságok ablaktábla alján. Ez az a webszolgáltatási paraméter, amelyik most már a társított [adatok exportálása] [ writer] modul paraméter.
4. Nevezze át a webes paraméter, kattintson a nevére, írja be a "Blob path", és nyomja le a **Enter** kulcs. 
5. A webszolgáltatási paraméter alapértelmezett értéke megadásához kattintson az ikonra a nevétől jobbra, válassza a "Megadása az alapértelmezett érték", adjon meg egy értéket (például "container1/output1.csv"), és nyomja le az ENTER a **Enter** kulcs.
   
   ![Webszolgáltatási paraméter][parameter]
6. Kattintson a **Run** (Futtatás) parancsra. 
7. Kattintson a **webes szolgáltatás telepítése** válassza **webes szolgáltatás telepítése [klasszikus]** vagy **[Új] webes szolgáltatás telepítése** a webszolgáltatás telepítése.

> [!NOTE] 
> Egy új webszolgáltatás-bővítmény telepítése, megfelelő engedélyekkel kell rendelkeznie, amelyhez az előfizetést, a webszolgáltatás telepítése. További információ: [kezelése az Azure Machine Learning webszolgáltatások portál használatával egy webszolgáltatás-bővítmény](manage-new-webservice.md). 

A felhasználó a webszolgáltatás most adhatja meg az új cél a [adatok exportálása] [ writer] modul a webszolgáltatáshoz való hozzáféréskor.

## <a name="more-information"></a>További információ
Részletes példa, tekintse meg a [webszolgáltatás-paramétereket](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) bejegyzést a [Machine Learning Blog](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

A Machine Learning webszolgáltatás elérése további információkért lásd: [hogyan kell használni az Azure Machine Learning Web service](consume-web-services.md).

<!-- Images -->
[icon]: ./media/web-service-parameters/icon.png
[parameter]: ./media/web-service-parameters/parameter.png


<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

