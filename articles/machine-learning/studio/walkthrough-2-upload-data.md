---
title: '2. lépés: A gépi tanulási kísérlet az adatok feltöltése |} Microsoft Docs'
description: 'A Develop egy prediktív megoldás útmutató 2. lépés: feltöltés nyilvános adatok tárolása az Azure Machine Learning Studio.'
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 9f4bc52e-9919-4dea-90ea-5cf7cc506d85
ms.service: machine-learning
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.openlocfilehash: f482b1273f83f5ae5bb4f1e64609767ee0c5fe32
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-experiment"></a>Az útmutató 2. lépése: A meglévő adatok feltöltése egy Azure Machine Learning-kísérletbe
Ez a forgatókönyv, a második lépése az [az Azure Machine Learning a prediktív elemzési megoldás fejlesztése](walkthrough-develop-predictive-solution.md)

1. [Machine Learning-munkaterület létrehozása](walkthrough-1-create-ml-workspace.md)
2. **Meglévő adatok feltöltése**
3. [Új kísérlet létrehozása](walkthrough-3-create-new-experiment.md)
4. [A modellek betanítása és kiértékelése](walkthrough-4-train-and-evaluate-models.md)
5. [A webszolgáltatás üzembe helyezése](walkthrough-5-publish-web-service.md)
6. [Hozzáférés a webszolgáltatáshoz](walkthrough-6-access-web-service.md)

- - -
A hitelkockázat kiszámításához a prediktív modell elkészítéséhez kell képzése és majd a modell teszteléséhez használjuk adatokat. Ennél a bemutatónál az "UCI Statlog (német jóváírás adatok) adatkészlet" fogjuk használni a Egyediségi Irvine Machine Learning tárházból. Megtalálja itt:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Nevű fájlban található fogjuk használni **german.data**. Töltse le a fájlt a helyi merevlemez-meghajtóról.  

A **german.data** dataset 20 változók sorokat tartalmaz-jóváírási 1000 múltbeli kérelmező esetében. 20 változókhoz határoz meg az adatkészlet számos funkciót (a *szolgáltatás vektoros*), amely lehetővé teszi azonosító jellemzőinek minden jóváírás kérelmező. Minden egyes sorban egy további oszlopot a kérelmező számított hitelkockázat, a magas kockázatú alacsony hitelkockázat és 300 azonosítottak 700 kérelmezők jelöli.

A UCI webhely ezeket az adatokat a szolgáltatás vektor attribútumait ismerteti. Ez magában foglalja az információkat, jóváírás előzmények, állapota és a személyes adatokat. Az egyes kérelmező bináris minősítést volt adott, amely azt jelzi, hogy azok az alacsony vagy magas kockázatú követel. 

Ezeket az adatokat fogjuk használni egy prediktív elemzési modell betanításához. Ha azt végzett, a modell fogadja el a szolgáltatás vektor egy új egyedi, és hogy nem magas vagy alacsony hitelkockázat előrejelzése kell lennie.  

Íme egy érdekes formája. Leírja, hogy az adatkészlet a UCI webhelyen akkor említi, mi azt költségek, ha egy személy hitelkockázat misclassify azt.
A modell magas hitelkockázat előrejelzi a ténylegesen alacsony hitelkockázat rendszer, ha a modell tett egy téves besorolás.
A fordított téves besorolás ötször költségesebb, a pénzügyi intézménynél, de: Ha a modell egy alacsony hitelkockázat előrejelzi mások számára ténylegesen hitelkockázati kockázatot jelent.

Igen azt szeretnénk, a modell betanításához, úgy, hogy ez utóbbi típusú téves besorolás költségét ötször magasabb, mint a más módon misclassifying.
Egy egyszerű Ez a kísérletben modell betanításakor módja (ötször) azokra a bejegyzésekre, amelyek megfelelnek egy magas hitelkockázat rendelkező bármely személy másolásával. Ezt követően a modell misclassifies valaki alacsony hitelkockázat, ha azok ténylegesen nagy kockázatot jelent, ha a modell elvégzi, hogy ugyanazon téves besorolás ötször, egyszer minden ismétlődő. Ez növeli a hiba, a képzési eredmények költségét.


## <a name="convert-the-dataset-format"></a>Alakítsa át a dataset formátumban
Az eredeti adathalmazból egy üres elválasztott formátumot használja. A Machine Learning Studio jobban működik egy vesszővel tagolt (CSV) fájl, így a dataset nem fogja átalakítás azáltal, hogy szóközök vesszővel válassza el egymástól.  

Számos módon konvertálni az adatokat. Egyik módja a következő Windows PowerShell-parancs használatával:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Egy másik módja a Unix csökkentésének parancs használatával:  

    sed 's/ /,/g' german.data > german.csv  

Mindkét esetben létrehoztunk egy nevű fájlt egy vesszővel tagolt verziója **german.csv** , hogy a kísérletben is használhatók.

## <a name="upload-the-dataset-to-machine-learning-studio"></a>Töltse fel az adatkészletet a Machine Learning Studio
Miután az adatok CSV formátumban lett konvertálva, igazolnia kell a Machine Learning Studióhoz töltse fel azt. 

1. Nyissa meg a Machine Learning Studio kezdőlap ([https://studio.azureml.net](https://studio.azureml.net)). 

2. A menü ![menü][1] a az ablak bal felső sarkában kattintson **Azure Machine Learning**, jelölje be **Studio**, és jelentkezzen be.

3. Kattintson a **+ új** az ablak alján.

4. Válassza ki **DATASET**.

5. Válassza ki **helyi FÁJLBÓL**.

    ![A DataSet adatkészlet hozzáadása a helyi fájlból][2]

6. Az a **töltse fel az új adatkészlet** párbeszédpanel, kattintson a **Tallózás** , és keresse a **german.csv** létrehozott fájlt.

7. Adja meg az adatkészlet nevét. Ennél a bemutatónál neki "UCI német hitelkártya adatok".

8. Adattípus, válassza ki a **fejléc nélküli általános CSV-fájlt (. nh.csv)**.

9. Ha azt szeretné, adjon meg egy leírást.

10. Kattintson a **OK** pipára.  

    ![A dataset feltöltése][3]

Ez feltölti az adatokat is használhatók. a kísérlet a dataset modulba.

Studio kattintva feltöltött adathalmazok kezelheti a **ADATKÉSZLETEK** fülre, és a Studio ablak bal oldalán.

![Adatkészletek kezelése][4]

Más típusú adatok kísérlet történő importálásával kapcsolatos további információkért lásd: [a betanítási adatok importálása az Azure Machine Learning Studio](import-data.md).

**Következő: [új kísérlet létrehozása](walkthrough-3-create-new-experiment.md)**

[1]: media/walkthrough-2-upload-data/menu.png
[2]: media/walkthrough-2-upload-data/add-dataset.png
[3]: media/walkthrough-2-upload-data/upload-dataset.png
[4]: media/walkthrough-2-upload-data/dataset-list.png
