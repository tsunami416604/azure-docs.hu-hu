---
title: Retrain Machine Learning Studio-modell – Azure |} A Microsoft Docs
description: Ismerje meg, hogyan modellek szoftveres átképezése és frissíteni a webszolgáltatást a újonnan betanított modell használata az Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: d1cb6088-4f7c-4c32-94f2-f7523dad9059
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 57e3a444c9de951fdd974d98679908eb5a744ad1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099816"
---
# <a name="retrain-an-azure-machine-learning-studio-model"></a>Az Azure Machine Learning Studio-modell újratanítása
A machine learning-modellek az Azure Machine Learning operacionalizálás folyamat részeként a modell betanított és mentve. Ezt követően használhatja azt egy prediktív webszolgáltatás létrehozására. A Web service a webhelyek, az irányítópultok és a mobile apps majd használhatók fel. 

Machine Learning segítségével létrehozott modellek ben általában nem statikusak. Az új adatok válnak elérhetővé, vagy ha a fogyasztói API-rendelkezik a saját adatait a modell kell lennie retrained. 

Átképezési fordulhat elő, gyakran. Az Átképezési API programozás alapú funkció is programozott újratanítás a modell az Átképezési API-k használatával és a Web service frissítése az újonnan betanított modell. 

Ez a dokumentum azt ismerteti, hogyan megőrzési, és bemutatja, hogyan használhatja az Átképezési API-kat.

## <a name="why-retrain-defining-the-problem"></a>Miért újratanítás: határozza meg a problémát
A machine learning-betanítási folyamat részeként a modell tanítása az adatok egy készletét. Machine Learning segítségével létrehozott modellek ben általában nem statikusak. Az új adatok válnak elérhetővé, vagy ha a fogyasztói API-rendelkezik a saját adatait a modell kell lennie retrained.

Ezekben az esetekben egy programozható API-t, hogy Ön közzéteszi vagy az API-k létrehozni az ügyfelet, amely egy egyszeri vagy rendszeres időközönként a ténytábláknál a modell használatával saját adataik kényelmes módot biztosít. Ezután az átképezési eredmények értékelése, és a Web service API használata az újonnan betanított modell frissítése.

> [!NOTE]
> Ha rendelkezik egy meglévő betanítási kísérlet és új Web service, érdemes a bemutató a következő szakaszban említett helyett meglévő prediktív webszolgáltatás teljesített kapcsolat-újraépítési kivétele.
> 
> 

## <a name="end-to-end-workflow"></a>Teljes körű munkafolyamat
A folyamat magában foglalja az alábbi összetevőket: A tanítási kísérletet, és a egy prediktív kísérletet közzététele webszolgáltatásként. A betanított modell átképezési engedélyezéséhez a tanítási kísérlet közzé kell tenni a betanított modell kimenete webszolgáltatásként. Ez lehetővé teszi az átképezési API a modell hozzáférést. 

Új és a klasszikus Web services vonatkoznak az alábbi lépéseket:

A kezdeti prediktív webszolgáltatás létrehozására:

* Betanítási kísérlet létrehozása
* Prediktív webszolgáltatásokat kísérlet létrehozása
* Egy prediktív webszolgáltatás üzembe helyezése

A webszolgáltatás újratanítása:

* Betanítási kísérlet átképezési lehetővé frissítése
* A megőrzési webszolgáltatás üzembe helyezése
* A kötegelt végrehajtási szolgáltatás kód használatával a modell újratanítása

Az előző bemutató lépéseit, lásd: [Retrain Machine Learning-modellek](retrain-models-programmatically.md).

> [!NOTE] 
> Egy új webszolgáltatás üzembe helyezéséhez rendelkeznie megfelelő engedélyekkel, amelyhez az előfizetésben, a web Service szolgáltatásának telepítése. További információ: [egy webszolgáltatás, az Azure Machine Learning Web Services portál használata kezelheti](manage-new-webservice.md). 

Ha telepítette a klasszikus webszolgáltatások:

* A prediktív webszolgáltatás az új végpont létrehozása
* A JAVÍTÁSI URL-cím és a kód lekérése
* Az új végpont mutassanak a retrained modell a JAVÍTÁSI URL-cím használatával 

Az előző bemutató lépéseit, lásd: [klasszikus webszolgáltatás újratanítása](retrain-a-classic-web-service.md).

Ha a klasszikus webszolgáltatások átképezési nehézségek tapasztal, tekintse meg a [hibaelhárítása az Azure Machine Learning klasszikus Web Service átképezési](troubleshooting-retraining-models.md).

Ha üzembe helyezte az új webszolgáltatások:

* Jelentkezzen be az Azure Resource Manager-fiók
* A webszolgáltatás-definíciójának beolvasása
* A webszolgáltatás-definíciójának exportálása JSON-fájlként
* Frissítse a hivatkozást a `ilearner` a JSON-blob
* A JSON importálhat egy webszolgáltatás-definíciójának
* A Web service frissítése az új webszolgáltatás-definíciójának

Az előző bemutató lépéseit, lásd: [a Machine Learning Management PowerShell-parancsmagok használatával új webszolgáltatás újratanítása](retrain-new-web-service-using-powershell.md).

A folyamat a klasszikus webszolgáltatások átképezési beállításához az alábbi lépésekből áll:

![Megőrzési folyamat áttekintése][1]

A folyamat egy új webszolgáltatás átképezési beállításához az alábbi lépésekből áll:

![Megőrzési folyamat áttekintése][7]

## <a name="other-resources"></a>Egyéb források
* [Az Azure Data Factoryvel átképezési és frissítése az Azure Machine Learning modellek](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
* [Számos Machine Learning-modellek és webszolgáltatás service végpontok létrehozása egy kísérletből PowerShell-lel](create-models-and-endpoints-with-powershell.md)
* A [AML Átképezési modellek használata API-k](https://www.youtube.com/watch?v=wwjglA8xllg) a videó bemutatja, hogyan hozhat létre az Azure Machine Learning, Machine Learning modellek szoftveres átképezése az Átképezési API-kat és a PowerShell használatával.

<!--image links-->
[1]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

