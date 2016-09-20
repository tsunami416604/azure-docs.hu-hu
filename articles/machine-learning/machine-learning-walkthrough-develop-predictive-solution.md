<properties
    pageTitle="Prediktív megoldás a hitelkockázat kiszámításához a Machine Learning használatával | Microsoft Azure"
    description="Részletes útmutató, amely azt ismerteti, hogyan hozható létre a hitelkockázat értékelésére szolgáló prediktív elemzési megoldás az Azure Machine Learning Studio eszközben."
    keywords="hitelkockázat, prediktív elemzési megoldás,kockázatértékelés"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/10/2016"
    ms.author="garye"/>


# Részletes útmutató: A hitelkockázat értékelésére szolgáló prediktív elemzési megoldás fejlesztése az Azure Machine Learning Studio használatával

Tegyük fel, hogy előrejelzést kell készíteni egy személy hitelkockázatáról az általa kitöltött hitelkérelemben megadott adatok alapján.  

A hitelkockázat értékelése természetesen összetett probléma, de a példa kedvéért most egyszerűsítsük le a paramétereket egy kicsit. Így már jó példaként szolgál annak a bemutatásához, hogy hogyan hozhatók létre prediktív elemzési megoldások a Microsoft Azure Machine Learning segítségével a Machine Learning Studio eszközt és a Machine Learning webszolgáltatást felhasználva.  

Ebben a részletes leírásban végigvezetjük Önt egy prediktív elemzési modell Machine Learning Studio használatával történő kifejlesztésén, majd a modell Azure Machine Learning webszolgáltatásként történő üzembe helyezésén. Nyilvánosan elérhető hitelkockázati adatokkal fogunk dolgozni, amelyek alapján kifejlesztünk és betanítunk egy prediktív modellt, majd a modellt üzembe helyezzük webszolgáltatásként, hogy mások is használhassák a hitelkockázatok értékeléséhez.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

<!-- -->

>[AZURE.TIP] Ha szeretne egy letölthető és kinyomtatható diagramot a Machine Learning Studio funkcióiról, tekintse meg [Az Azure Machine Learning Studio funkcióit bemutató diagram](machine-learning-studio-overview-diagram.md) című cikket.

A hitelkockázat-értékelési megoldás létrehozásához az alábbi lépéseket fogjuk követni:  

1.  [Machine Learning-munkaterület létrehozása](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Meglévő adatok feltöltése](machine-learning-walkthrough-2-upload-data.md)
3.  [Új kísérlet létrehozása](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [A modellek betanítása és kiértékelése](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [A webszolgáltatás üzembe helyezése](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Hozzáférés a webszolgáltatáshoz](machine-learning-walkthrough-6-access-web-service.md)

Ez az útmutató a [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/) kínálatában szereplő [Binary Classfication: Credit risk prediction](http://go.microsoft.com/fwlink/?LinkID=525270) (Bináris osztályozás: a hitelkockázat előrejelzése) nevű mintakísérlet egyszerűsített verzióján alapul.



<!--HONumber=sep16_HO1-->


