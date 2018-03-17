---
title: "A gépi tanulási modell újratanítása |} Microsoft Docs"
description: "Megtudhatja, hogyan működik a modell, és frissíti a az újonnan betanított modell használatára az Azure Machine Learning webszolgáltatás."
services: machine-learning
documentationcenter: 
author: serinakaye
ms.author: serinak
manager: mwinkle
editor: 
ms.assetid: d1cb6088-4f7c-4c32-94f2-f7523dad9059
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 65c5e86e47528aab36972507b7ee8324040b9beb
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="retrain-a-machine-learning-model"></a>A gépi tanulási modell újratanítása
A machine learning modellek Azure Machine Learning operationalization folyamat részeként a modell betanítása és mentve. Majd a használatával hozzon létre egy predicative webszolgáltatás-bővítmény. A webszolgáltatás majd használni képes a webhelyek, az irányítópultok és a mobile apps szolgáltatásban. 

Használata a Machine Learning modellek jellemzően nem statikus. Amint az elérhetővé válik az új adatok, vagy ha a fogyasztó API rendelkezik saját adataikat a modellnek támogatnia kell a retrained lehet. 

Átképezési fordulhat elő, gyakran. Programozott Átképezési API szolgáltatás programozott módon is működik a modellt az Átképezési API-kkal és a webszolgáltatás frissítse az újonnan betanított modell. 

Ez a dokumentum megőrzési folyamatát mutatja be, és bemutatja, hogyan használhatja az Átképezési API-kat.

## <a name="why-retrain-defining-the-problem"></a>Miért újratanítása: a probléma meghatározása
Gépi tanulási képzési folyamat részeként a modell betanítása adatok használatával. Használata a Machine Learning modellek jellemzően nem statikus. Amint az elérhetővé válik az új adatok, vagy ha a fogyasztó API rendelkezik saját adataikat a modellnek támogatnia kell a retrained lehet.

Ezekben az esetekben a programozott API engedélyezéséhez, vagy az ügyfél az API-ügyfél, amely egy egyszeri vagy rendszeres időközönként újratanítása is, a saját adatok modell létrehozásához kényelmes megoldást kínál. Azok majd értékelje ki a átképezési eredményeit, és frissítse a webszolgáltatási API az újonnan betanított modell használatára.

> [!NOTE]
> Ha egy meglévő tanítási kísérletet, és az új webszolgáltatás-bővítmény, érdemes lehet egy meglévő prediktív webszolgáltatás helyett a következő részben leírt forgatókönyv a következő teljesített kapcsolat-újraépítési kivétele.
> 
> 

## <a name="end-to-end-workflow"></a>Teljes körű munkafolyamat
A folyamat a következő összetevőket foglalja magában: A tanítási kísérletet, és egy prediktív Kísérletté közzétett webszolgáltatásként. A modell betanítását átképezési engedélyezéséhez a tanítási kísérletet közzé kell tenni a kimenet a betanított modell webszolgáltatásként. Ez lehetővé teszi az átképezési API a modell hozzáférést. 

Új és a klasszikus Web services alkalmazni az alábbi lépéseket:

Hozza létre a kezdeti prediktív webszolgáltatás:

* Hozzon létre egy tanítási kísérletet
* A prediktív webes kísérlet létrehozása
* Egy prediktív webszolgáltatás-bővítmény telepítése

A webszolgáltatás működik:

* Frissítés tanítási kísérletet, amelyek engedélyezik az átképezési
* A megőrzési webszolgáltatás üzembe helyezése
* A kötegelt végrehajtási szolgáltatás kód segítségével a modell újratanítása

Részletes útmutatás a fenti lépéseket, a következő témakörben: [Machine Learning-modellek szoftveres](retrain-models-programmatically.md).

> [!NOTE] 
> Egy új webszolgáltatás-bővítmény telepítése, megfelelő engedélyekkel kell rendelkeznie, amelyhez az előfizetést, a webszolgáltatás telepítése. További információ: [kezelése az Azure Machine Learning webszolgáltatások portál használatával egy webszolgáltatás-bővítmény](manage-new-webservice.md). 

Ha telepítette a klasszikus webszolgáltatás:

* A prediktív webszolgáltatáson új végpont létrehozásához
* A javítás URL-cím és a kód
* Az új végpont helyen a retrained modell a javítás URL-cím használatával 

Részletes útmutatás a fenti lépéseket, a következő témakörben: [klasszikus webszolgáltatás újratanítása](retrain-a-classic-web-service.md).

Ha a klasszikus webszolgáltatás átképezési nehézségek futtatja, lásd: [hibaelhárítása az Azure Machine Learning klasszikus webszolgáltatás átképezési](troubleshooting-retraining-models.md).

Ha egy új webszolgáltatás-bővítmény:

* Jelentkezzen be az Azure Resource Manager-fiókba
* A webszolgáltatás-definíciójának beolvasása
* A webszolgáltatás-definíciójának JSON exportálása
* Frissítse a hivatkozást a `ilearner` blob a JSON-ban
* A JSON importálnia kell a webszolgáltatás-definíciójának
* A webszolgáltatás új webszolgáltatás-definíciójának frissítése

Részletes útmutatás a fenti lépéseket, a következő témakörben: [egy új webszolgáltatás-bővítmény a Machine Learning Management PowerShell-parancsmagok használatával újratanítása](retrain-new-web-service-using-powershell.md).

A folyamat az adott klasszikus webszolgáltatás átképezési beállításának a következő lépésekből áll:

![Megőrzési folyamat áttekintése][1]

A folyamat az új webszolgáltatás átképezési beállításához a következő lépésekből áll:

![Megőrzési folyamat áttekintése][7]

## <a name="other-resources"></a>Egyéb források
* [Azure Data Factory átképezési és frissítési Azure Machine Learning modellek](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
* [Hozzon létre az számos Machine Learning modellek és webes szolgáltatás végpontok egy kísérlet PowerShell használatával](create-models-and-endpoints-with-powershell.md)
* A [AML Átképezési modellek használata API-k](https://www.youtube.com/watch?v=wwjglA8xllg) videó bemutatja, hogyan működik a Machine Learning modellek létrehozása az Azure Machine Learning Átképezési API-kat és a PowerShell használatával.

<!--image links-->
[1]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

