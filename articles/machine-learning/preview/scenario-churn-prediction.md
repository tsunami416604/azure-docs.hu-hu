---
title: "Ügyfél Kavarog előrejelzés Azure Machine Learning használatával |} Microsoft Docs"
description: "Forgalom analytics Azure ML munkaterület használatának módjáról."
services: machine-learning
documentationcenter: 
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: miprasad
ms.openlocfilehash: 7db93786b71fb7876ae02fd8fd006a1e8e0c2271
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="customer-churn-prediction-using-azure-machine-learning"></a>Felhasználói forgalom előrejelzés Azure Machine Learning segítségével

Átlagosan tartani a meglévő ügyfeleknek ötször olcsóbb, mint a felvételi újakat költségét. Ennek eredményeképpen marketing vezetők gyakran található maguk próbál becsléséhez a felhasználói forgalom valószínűségét, és a szükséges lépéseket a minimalizálása érdekében a forgalom sebessége keresése.

Ez a megoldás célja használata az Azure Machine Learning-munkaterület forgalom prediktív elemzés bemutatása. A megoldás egy könnyen használható sablont forgalom prediktív adatok folyamatok kialakított kiskereskedőktől biztosít. Különböző datasets adatkészletekben és a forgalom különböző meghatározása használhatja a sablont. A gyakorlati például az a célja, hogy:

1. Ismerje meg, törlése és a forgalom analytics ügyfélkapcsolati adatot betöltési Azure Machine Learning munkaterület adatok előkészítése eszközöket.

2. Hajtsa végre a szolgáltatás átalakítása zajos heterogén adatok kezeléséhez.

3. Külső szalagtárak integrálni (például `scikit-learn` és `azureml`) bayes-féle és fa-alapú osztályozó előrejelzésére forgalmának kezeléséhez.

4. Telepítsen.

## <a name="link-of-the-gallery-github-repository"></a>A gyűjtemény GitHub-tárház hivatkozás
Az alábbiakban látható a nyilvános GitHub-tárházban, ahol a kód tárolása a hivatkozásra:

[https://github.com/Azure/MachineLearningSamples-ChurnPrediction](https://github.com/Azure/MachineLearningSamples-ChurnPrediction)

## <a name="use-case-overview"></a>Használja az eset áttekintése
A vállalatok egy hatékony stratégia kell ügyfél-forgalom kezelésére. Felhasználói forgalom ügyfelek használatát a szolgáltatás leállítása, átváltás egy versenytársnak szolgáltatás, a szolgáltatás egy alacsonyabb szintű felhasználói élménye átváltás vagy csökkenti a szolgáltatással engagement tartalmazza.

A használati eset a úgy tekintünk adatok francia telecom vállalat narancssárga valószínűleg kavarog egyidejűleg a közeljövőben ahhoz, hogy a szolgáltatás javítására, és hozzon létre egyéni szolgálat kampányok megőrzése érdekében a felhasználók azonosítására.

Telecom vállalatok szembesülhetnek a piaci. Sok üzemeltető szolgáltatók miatt postpaid ügyfelektől származó bevétel elveszti kavarog egyidejűleg. Ezért pontosan meghatározhatja a felhasználói forgalom képes hatalmas versenyelőnyt is lehet.

A felhasználói forgalom telecom tényezők közé:

* Észlelt gyakori service szolgáltatások
* Gyenge szolgáltatás ügyfélélményt online/kiskereskedelmi üzletekben
* Más versengő szállítók (jobb termékcsalád terv, adatforgalmi díjcsomagra, stb.) ajánlatait.

Ebben a megoldásban hozhat létre prediktív ügyfél forgalom modell az telecom vállalatok konkrét példája használjuk.

## <a name="prerequisites"></a>Előfeltételek

* Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverzió érhetők el)

* Egy telepített példánya [Azure Machine Learning-munkaterület](./overview-what-is-azure-ml.md) következő a [– első lépések a telepítési útmutató](./quickstart-installation.md) telepíteni a programot, és egy munkaterület létrehozása

* Operationalization célszerű, ha van telepítve, és helyileg futó Docker-motorhoz. Ha nem, akkor a fürt beállítást használja, de vegye figyelembe, hogy a fut egy Azure-tároló szolgáltatás (ACS) költséges lehet.

* A megoldás feltételezi, hogy futtatja Azure Machine Learning-munkaterület Windows 10 helyi telepítése Docker-motorhoz. MacOS használata található utasítás nagyjából azonos.

## <a name="create-a-new-workbench-project"></a>Új munkaterület-projekt létrehozása

Hozzon létre egy új projektet, ebben a példában egy sablon használatával:
1.  Nyissa meg az Azure Machine Learning-munkaterület
2.  Az a **projektek** lapján kattintson a  **+**  aláírásához, és válassza ki **új projekt**
3.  Az a **új projekt létrehozása** ablaktáblán, töltse ki az adatokat az új projekt
4.  Az a **keresési Projektsablonjai** keresési mezőbe, írja be a "Felhasználói Kavarog előrejelzés", és válassza ki a sablont
5.  Kattintson a **Create** (Létrehozás) gombra

## <a name="data-description"></a>Adatok leírása

A megoldásban használt adatkészlet a SIDKDD 2009 verseny származik. Azt nevezzük `CATelcoCustomerChurnTrainingSample.csv` található, és a [ `data` ](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/tree/master/data) mappa. A dataset francia Telecom vállalat narancssárga heterogén zajos adatok (numerikus/kategorikus változók) áll, és van anonimizált adatokon alapul.

A változók rögzítése ügyfél demográfiai adatait, (például átlagos időtartama, hívás hibaaránya stb.), a hívási statisztika szerződések csatlakoztatásra statisztikai adatait. Forgalom változó bináris (0 - nem volt kavarog, 1 – kavarog volt).

## <a name="scenario-structure"></a>A forgatókönyv struktúra

A mappastruktúra az alábbiak szerint van rendezve:

__adatok__: tartalmazza az adatkészletet a megoldásban használt  

__dokumentumok__: a gyakorlati labs tartalmazza

A megoldás elvégzésére Hands-on Labs sorrendjét a következőképpen történik:
1. Adatok előkészítése: A fő kapcsolatos adatok előkészítése az adatok mappában található fájl`CATelcoCustomerChurnTrainingSample.csv`
2. Modellezési és értékelés: A fő fájl modellezési és a gyökérmappában kiértékelése`CATelcoCustomerChurnModeling.py`
3. Modellezési és értékelési .dprep nélkül: A fő fájl a gyökérmappában található a feladathoz`CATelcoCustomerChurnModelingWithoutDprep.py`
4. Operationalization: A fő deloyment fájljai a modell (`model.pkl`) és`churn_schema_gen.py`

| Sorrend| Fájlnév | Biztonságinapló-fájlok |
|--|-----------|------|
| 1 | [`DataPreparation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/DataPreparation.md) | "data/CATelcoCustomerChurnTrainingSample.csv" |
| 2 | [`ModelingAndEvaluation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluation.md) | "CATelcoCustomerChurnModeling.py" |
| 3 | [`ModelingAndEvaluationWithoutDprep.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluationWithoutDprep.md) | "CATelcoCustomerChurnModelingWithoutDprep.py" |
| 4 | [`Operationalization.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/Operationalization.md) | "model.pkl"<br>"churn_schema_gen.py" |

A tesztkörnyezetek kövesse a szekvenciális a fent leírt módon.

## <a name="conclusion"></a>Összegzés
Ez átadja a forgatókönyvben bemutatott hogyan hajthat végre az Azure Machine Learning-munkaterület használatával forgalom előrejelzés. Azt először adatok előkészítése eszközökkel szolgáltatás mérnöki követ zajos és heterogén adatok kezelésének Adattisztítás végre. A Microsoft majd nyílt forráskódú gépi tanulási a eszközök segítségével hozhatók létre és a besorolási modell kiértékelése, majd állítsa rendszerbe a modellt, és készen áll a termelési környezetben használt helyi docker-tároló.
