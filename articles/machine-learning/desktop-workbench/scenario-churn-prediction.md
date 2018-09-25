---
title: Azure Machine Learning segítségével vásárlók Lemorzsolódásának Prognosztizálása |} A Microsoft Docs
description: Hogyan végezheti el a lemorzsolódás analytics az Azure Machine Learning Workbench használatával.
services: machine-learning
documentationcenter: ''
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: miprasad
ROBOTS: NOINDEX
ms.openlocfilehash: 0210e65c0859b00caac0fe66baa1c73063f644c8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947941"
---
# <a name="customer-churn-prediction-using-azure-machine-learning"></a>Vásárlók lemorzsolódásának prognosztizálása az Azure Machine Learning segítségével

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Átlagosan meglévő ügyfelek az ötször olcsóbb, mint az új felvételi költsége. Ennek eredményeképpen marketingvezetőket gyakran tapasztalhatja maguk próbálja meg a lemorzsolódás valószínűségét, és megtalálni a szükséges lépéseket a lemorzsolódási ráta minimalizálásához.

Ez a megoldás célja az Azure Machine Learning Workbench használatával prediktív lemorzsolódási analytics bemutatása. A megoldás egy egyszerűen használható sablon lemorzsolódási prediktív adatcsatornák fejlesztését kiskereskedők számára biztosít. A sablon használható különböző adatkészleteket és -adatforgalom külön definíciót. A gyakorlati példa célja, hogy:

1. Ismerje meg az Azure Machine Learning Workbench adat-előkészítési eszközök megtisztítják és adatváltozás analytics kapcsolat ügyféladatok feldolgozására.

2. Hajtsa végre az funkció átalakítási zajos heterogén adatok kezeléséhez.

3. Integrálhatja a külső gyártótól származó kódtárakat (például `scikit-learn` és `azureml`) Bayes és a forgalom fa-alapú osztályozó fejlesztéséhez.

4. Helyezze üzembe.

## <a name="link-of-the-gallery-github-repository"></a>Hivatkozás katalógus GitHub-adattár
Következő a összes kódot üzemeltető nyilvános GitHub-adattárra mutató hivatkozásra:

[https://github.com/Azure/MachineLearningSamples-ChurnPrediction](https://github.com/Azure/MachineLearningSamples-ChurnPrediction)

## <a name="use-case-overview"></a>Használati eset – áttekintés
Vállalatok egy hatékony stratégiával kell kezelnie az ügyfelek lemorzsolódását. A lemorzsolódás körébe ügyfelek leállítása folyamatban van a szolgáltatás használatát, váltson egy versenytárs szolgáltatás, egy alacsonyabb szintű felhasználói élményt a szolgáltatásban való váltás vagy hűségidőre a szolgáltatás.

A ezt a használati esetet áttekintjük adatok francia telekommunikációs cég narancssárga azonosításához az ügyfelek, akik várhatóan annak érdekében, hogy a szolgáltatás továbbfejlesztéséhez, és hozzon létre egyéni hasznosság kampányokat, amelyek segítségével a vevők megőrzése időn belül.

Távközlési cégek élesebb piaci között. Számos szolgáltatóknál bevétel postpaid ügyfeleink miatt a vásárlói lemorzsolódás elvesznek. Ezért pontosan meghatározhatja a vásárlók lemorzsolódásának lehetővé teszi egy felismerése tehát óriási előnyt is lehet.

A távközlési lemorzsolódáshoz hozzájáruló tényezők többek között:

* Észlelt gyakori szolgáltatáskimaradás
* Gyenge szolgáltatás ügyfélélményt online/kiskereskedelmi üzletekben
* Ajánlatai (jobb családi csomag, adatforgalmi díjcsomagot, stb.) más lemorzsolódásához.

Ebben a megoldásban használjuk egy konkrét példa telekommunikációs cég prediktív ügyfelek lemorzsolódásának modell létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

* Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverziók érhető el)

* Egy telepített példánya [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) következő a [rövid telepítési útmutatójában](quickstart-installation.md) telepítse a programot, és a egy munkaterület létrehozása

* Az operacionalizálás célszerű Ha helyben telepített és futtatott Docker-motor. Ha nem, akkor a fürt lehetőséget használja, de vegye figyelembe, hogy egy Azure Container Service (ACS) futó költséges lehet.

* Ez a megoldás feltételezi, hogy futtat az Azure Machine Learning Workbench Windows 10-es a helyileg telepített Docker-motor. Ha macOS használ utasításait követve a nagymértékben megegyezik.

## <a name="create-a-new-workbench-project"></a>Egy új Workbench-projekt létrehozása

Hozzon létre egy új projektet, ebben a példában egy sablon használatával:
1.  Nyissa meg az Azure Machine Learning Workbench alkalmazásban
2.  Az a **projektek** lap, kattintson a **+** aláírásához, és válassza ki **új projekt**
3.  Az a **új projekt létrehozása** panelen adja meg az információkat az új projekt
4.  Az a **projektsablonok keresése** keresőmezőbe, írja be a "Vásárlók Lemorzsolódásának Prognosztizálása", és válassza ki a sablont
5.  Kattintson a **Create** (Létrehozás) gombra

## <a name="data-description"></a>Adatok leírása

A megoldásban használt adatkészlet van SIDKDD 2009 versenytársaik közül. Azt nevezzük `CATelcoCustomerChurnTrainingSample.csv` , és a található a [ `data` ](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/tree/master/data) mappát. Az adatkészlet névtelenek, és a narancssárga francia telekommunikációs cég a heterogén zajos adatok (numerikus/kategorikus változók) áll.

A változók rögzítése demográfiai ügyféladatokat, a hívás statisztika (például átlagos hívás időtartama, hívások hibáinak sebessége, stb.), szerződések panasz statisztikai adatait. Forgalom változó bináris (0 – nem tudta churn, 1 – churn volt).

## <a name="scenario-structure"></a>A forgatókönyv-struktúra

A gyökérmappa-szerkezetében a következőképpen van rendezve:

__adatok__: tartalmazza az adatkészletet a megoldásban használt  

__Docs__: tartalmazza az összes laborgyakorlat

Gyakorlati Laborgyakorlatok sorrendje számára, a megoldás a következőképpen történik:
1. Adat-előkészítési: A fő fájl kapcsolatos adat-előkészítés az adatok mappában van `CATelcoCustomerChurnTrainingSample.csv`
2. Modellezési és értékelés céljából használják: A modellezésre és a gyökérmappában található értékelés kapcsolatos fő fájl `CATelcoCustomerChurnModeling.py`
3. Modellezési és értékelési .dprep nélkül: A fő fájl a gyökérmappában található a feladathoz `CATelcoCustomerChurnModelingWithoutDprep.py`
4. Operacionalizálás: A fő deloyment fájlok a modell (`model.pkl`) és a `churn_schema_gen.py`

| Rendelés| Fájlnév | Biztonságinapló-fájlok |
|--|-----------|------|
| 1 | [`DataPreparation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/DataPreparation.md) | "data/CATelcoCustomerChurnTrainingSample.csv" |
| 2 | [`ModelingAndEvaluation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluation.md) | "CATelcoCustomerChurnModeling.py" |
| 3 | [`ModelingAndEvaluationWithoutDprep.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluationWithoutDprep.md) | "CATelcoCustomerChurnModelingWithoutDprep.py" |
| 4 | [`Operationalization.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/Operationalization.md) | "model.pkl"<br>"churn_schema_gen.py" |

Kövesse a Labs a szekvenciális a fent leírt módon.

## <a name="conclusion"></a>Összegzés
Ez átadja forgatókönyv végrehajtása lemorzsolódásának prognosztizálása az Azure Machine Learning Workbench használatával mutatja be. Hogy először funkciófejlesztési adat-előkészítési eszközökkel kiegészítve zajos vagy heterogén adatok kezelésének adattisztító történik. Hogy majd használt nyílt forráskódú gépi tanulási eszközökkel hozhat létre, és a egy osztályozási modell kiértékelése, majd a helyi docker-tároló üzembe a modellt, így készen áll az éles környezetben használt.
