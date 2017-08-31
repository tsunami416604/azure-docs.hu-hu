---
title: "Mire használható az Azure Machine Learning funkciója? | Microsoft Docs"
description: "Ismerteti a felhőalapú Machine Learning alapvető fogalmait, az alkalmazási területeket, valamint a Machine Learning kifejezéseit."
keywords: "mi az a machine learning,machine learning-kifejezések,prediktív,mi az a prediktív elemzés,machine learning-kifejezések"
services: machine-learning
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: eaee083e-eaa1-4408-838b-93e51423d159
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/12/2017
ms.author: cgronlun
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 179a0d3696c6044ffb5b9e377effa30dda54ba7f
ms.contentlocale: hu-hu
ms.lasthandoff: 08/23/2017

---
# <a name="introduction-to-machine-learning-in-the-azure-cloud"></a>Bevezetés az Azure-felhőalapú Machine Learning használatába

## <a name="what-is-machine-learning"></a>Mit jelent a gépi tanulás funkció?
A Machine Learning egy olyan adatelemzési módszer, amely lehetővé teszi, hogy a számítógépek a meglévő adatokból tanulva jövőbeni viselkedéseket, kimeneteket és trendeket jelezhessenek előre. A gépi tanulással a számítógépek konkrét programozás nélkül tanulhatnak. 

A gépi tanulás a mesterséges intelligencia (AI) egyik alkategóriája. A gépi tanulás által biztosított előrejelzéseket felhasználva intelligensebbé tehetők az alkalmazások és az eszközök. Ha például online vásárlói felületeknél alkalmazzák, a gépi tanulás képes a korábban megvásárolt cikkek alapján olyan termékeket ajánlani, amelyek esetleg szintén érdekelhetik Önt. A Machine Learning a bankkártya minden lehúzásakor összeveti az adott tranzakciót az adatbázisban található tranzakciókkal, így segít a csalások felismerésében. Ha robotporszívóra bízza a szoba kitakarítását, a Machine Learning segít eldönteni, hogy a feladat el lett-e végezve.

Az [Adatelemzés kezdőknek](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) videósorozat rövid áttekintést ad ezekről a témákról. Az Adatelemzés kezdőknek sorozat szakzsargon vagy matematikai kifejezések használata nélkül ismerteti meg a Machine Learning használatával, és vezeti végig egy egyszerű prediktív modellen.

## <a name="what-is-machine-learning-in-the-microsoft-azure-cloud"></a>Mi a Microsoft Azure-felhő Machine Learning funkciója?

![Mit jelent a gépi tanulás funkció? A prediktív elemzés működésbe léptetésének alapvető munkafolyamata az Azure Machine Learning szolgáltatásban.](./media/machine-learning-what-is-machine-learning/machine-learning-service-parts-and-workflow.png)

Az Azure Machine Learning egy felhőalapú prediktív elemzési szolgáltatás, amely lehetővé teszi elemzési megoldásként használható prediktív modellek gyors létrehozását és üzembe helyezését.

A használatra kész algoritmusok tárából dolgozhat, az algoritmusokat modellek létrehozására használhatja internetkapcsolattal rendelkező számítógépeken, és a prediktív elemzési megoldást gyorsan üzembe helyezheti. Kezdje a munkát a [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com/) használatra kész példáival és megoldásaival.

Az Azure Machine Learning nem csupán eszközöket biztosít a modellek prediktív elemzéséhez, de teljes körűen felügyelt szolgáltatást is nyújt, amelynek segítségével felhasználásra kész webszolgáltatásként üzembe helyezheti prediktív modelljeit.

## <a name="what-is-predictive-analytics"></a>Mi az a prediktív elemzés?
A prediktív elemzések különböző matematikai képleteket, úgynevezett algoritmusokat használnak a korábbi és a jelenlegi adatok elemzéséhez, amelyekben a jövőbeni események előrejelzését segítő mintázatokat vagy trendeket azonosítanak.

## <a name="tools-to-build-complete-machine-learning-solutions-in-the-cloud"></a>Eszközök teljes körű gépi tanulási megoldások létrehozásához a felhőben
Az Azure Machine Learning szolgáltatásban minden megtalálható, amire szüksége lehet a teljes körű prediktív elemzési megoldások létrehozásához a felhőben: nagy méretű algoritmustár, létrehozási modelleket tartalmazó stúdió, a modellek webszolgáltatásként való üzembe helyezését megkönnyítő funkciók és még számos hasznos lehetőség. Prediktív modelleket hozhat létre, amelyeket könnyedén tesztelhet, működésbe léptethet, és felügyelhet.

### <a name="machine-learning-studio-create-predictive-models"></a>Machine Learning Studio: Prediktív modellek létrehozása
Húzással és modulok csatlakoztatásával gyorsan létrehozhat prediktív modelleket a [Machine Learning Studióban](machine-learning-what-is-ml-studio.md). Kísérletezhet a különböző kombinációkkal, amelyeket [ingyenesen ki is próbálhat](https://studio.azureml.net/?selectAccess=true&o=2).

* A [Cortana Intelligence Galleryben](machine-learning-gallery-how-to-use-contribute-publish.md) mások által kifejlesztett elemzési megoldásokat próbálhat ki, vagy létrehozhatja a sajátját. A közösség szívesen segít választ találni kísérletekkel kapcsolatos kérdéseire vagy megjegyzéseire. A kísérletekre mutató hivatkozásait különféle közösségi hálózatokon (például LinkedInen vagy Twitteren) is megoszthatja.

  ![Kipróbálhatja mások prediktív kísérleteit, vagy feltöltheti sajátjait az Azure Cortana Intelligence Gallerybe.](./media/machine-learning-what-is-machine-learning/machine-learning-cortana-intelligence-gallery.png)
* A Machine Learning Studióban olyan [Machine Learning-algoritmusok és -modulok](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) gazdag tára áll rendelkezésre, amelyek jelentősen felgyorsítják a prediktív modell létrehozását. Mintakísérletek, R- és Python-csomagok, valamint olyan kategóriavezető Microsoft-algoritmusok közül választhat, mint az Xbox és a Bing. A Studio moduljait saját [R-](machine-learning-extend-your-experiment-with-r.md) és [Python-](machine-learning-execute-python-scripts.md)parancsprogramjaival bővítheti ki.

  ![A prediktív elemzés bemutatása: prediktív elemzési kísérlet bemutatása az Azure Machine Learning Studióban](./media/machine-learning-what-is-machine-learning/azure-machine-learning-studio-predictive-score-experiment.png)

### <a name="operationalize-predictive-analytics-solutions-by-publishing-your-own"></a>Prediktív elemzési megoldások működésbe léptetése saját megoldások közzétételével
Az alábbi oktatóanyagok ismertetik a prediktív elemzési modellek működtetését:

 * [Webszolgáltatások üzembe helyezése](machine-learning-publish-a-machine-learning-web-service.md)
 * [A modellek újratanítása API-k segítségével](machine-learning-retrain-models-programmatically.md)
 * [Webszolgáltatás-végpontok kezelése](machine-learning-create-endpoint.md)
 * [Webszolgáltatás méretezése](machine-learning-scaling-webservice.md)
 * [Webszolgáltatások felhasználása](machine-learning-consume-web-services.md)

## <a name="key-machine-learning-terms-and-concepts"></a>A Machine Learning legfontosabb kifejezései és fogalmai
A Machine Learning kifejezéseinek jelentése nem mindig egyértelmű. Segítségül az alábbiakban ismertetjük a legfontosabb kifejezések meghatározását. Az ezek alatt található megjegyzés rovatban kérdezhet rá olyan további kifejezésekre, amelyek meghatározására kíváncsi.

### <a name="data-exploration-descriptive-analytics-and-predictive-analytics"></a>Az adatok feltárása, deskriptív elemzés és prediktív elemzés

Az **adatok feltárásának** azt a folyamatot nevezzük, amikor egy nagy és általában nem strukturált adathalmaz adatait összegyűjtve megkeressük a részletesebb elemzéshez felhasználható általános jellemzőket.

Az **adatbányászat** kifejezés az adatfeltárás automatizált formájára utal.

**Deskriptív elemzésnek nevezzük**, amikor az adathalmaz elemzésével azt szeretnénk megkapni, hogy pontosan mi történt az adatokkal. Az üzleti elemzések nagy része (például az értékesítési jelentések, webes metrikák és közösségihálózat-elemzések) deskriptív elemzésnek számít.

**Prediktív elemzésnek** azt a folyamatot nevezzük, amikor előzményadatok vagy aktuális adatok alapján modelleket hozunk létre, amelyek segítségével előre lehet jelezni valamilyen jövőbeli eredményt.

### <a name="supervised-and-unsupervised-learning"></a>Felügyelt és nem felügyelt tanítás
 A **felügyelt tanítással** képzett algoritmusokat címkézett adatokkal, azaz a kívánt válaszokat példázó adatokkal tanítják. A rosszindulatú bankkártyahasználatot azonosító modellt például már ismert rosszindulatú és érvényes terhelések címkézett adatpontjait tartalmazó adatkészletből kell betanítani. A gépi tanulás általában felügyelt módon történik.

 A **nem felügyelt tanítást** címkéket nem tartalmazó adatokon alkalmazzák, és a folyamat célja, hogy megtalálja az adathalmazon belüli összefüggéseket. Ez akkor hasznos, ha például vásárlói körében hasonló vásárlási szokásokat követő csoportokat szeretne felállítani.

### <a name="model-training-and-evaluation"></a>A modellek betanítása és kiértékelése
A gépi tanulási modell a megválaszolandó kérdések vagy az előre jelezni kívánt eredmények absztrakciója. A modellek tanítását és kiértékelését meglévő adatok alapján lehet elvégezni.

#### <a name="training-data"></a>Betanítási adatok
A modellek adatokból történő betanításakor használhat egy már ismert adatkészletet, majd a legpontosabb válaszok érdekében az adatjellemzők alapján módosíthatja a modellt. Az Azure Machine Learning szolgáltatásban egy, a tanítási adatokat feldolgozó algoritmusmodul, illetve funkcionális modulok, például pontozási modulok szükségek a modell létrehozásához.

Felügyelt tanítás során, például a csalásészlelő modell tanítása esetén egy tranzakciókat tartalmazó halmazt fog használni, amelyben minden tranzakciónál meg van jelölve, hogy az csalási célú vagy érvényes. Az adathalmazt ilyenkor véletlenszerűen fel kell darabolni: az egyik rész a modell betanítására, a másik a modell kiértékelésére való.

#### <a name="evaluation-data"></a>Az adatok kiértékelése
Ha elvégezte a modell betanítását, a fennmaradó tesztadatok segítségével értékelje ki a modellt. Ilyenkor olyan adatokat kell használni, amelyeknél tisztában van az eredményekkel, mivel így állapítható meg, hogy milyen pontosságú előrejelzésre képes a modell.

## <a name="other-common-machine-learning-terms"></a>A gépi tanulás tárgykörében gyakran használt egyéb kifejezések
* **algoritmus**: A szabályok olyan önálló készlete, amely adatfeldolgozás, matematikai műveletek vagy automatikus indoklás alkalmazásával segít a problémák megoldásában.
* **anomáliaészlelés**: Olyan modell, amely képes a szokatlan események vagy értékek megjelölésére, és így segíti a problémák észlelését. A rosszindulatú bankkártyahasználat észlelése során például a rendszer szokatlan vásárlásokat keres.
* **kategorizált adatok**: különböző kategóriákba sorolt, és különféle csoportokra osztható adatok. Egy gépjárműveket tartalmazó kategorizált adathalmazban például meg van adva a gyártás éve, a márka, a típus és az ár.
* **besorolás**: az adatpontok kategóriákba sorolására használható modell, amely egy kategóriacsoportokkal már ellátott adathalmazt vesz alapul.
* **jellemzőkiemelés**: az a folyamat, amikor bizonyos jellemzőket kinyerünk vagy kiválasztunk egy adathalmazból az adathalmaz és az eredmények minőségének javítása érdekében. A repülőjegyárakra vonatkozó adatokat például a hét napjainak és a munkaszüneti napoknak a kiemelésével lehet javítani. További információk: [Feature selection and engineering in Azure Machine Learning](machine-learning-feature-selection-and-engineering.md) (Jellemzőkiválasztás és -kiemelés az Azure Machine Learngingben).
* **modul**: A Machine Learning Studio-modell funkcionális része, például az Enter Data (Adatbevitel) modul, amely kis adatkészletek bevitelét és szerkesztését teszi lehetővé. A Machine Learning Studióban az algoritmusok is a modulok egyik típusának számítanak.
* **modell**: Egy Machine Learning-kísérlet eredményeként létrejövő felügyelt tanítási modell, amely betanítási adatokból, algoritmusmodulból és különböző funkcionális modulokból (például a Score Model (Modell pontozása) modul) áll.
* **numerikus adatok**: mérésekként (folytonos adatok) vagy megszámlált értékekként (diszkrét adatok) értelmezhető adatok. A numerikus adatokat *kvantitatív adatoknak* is nevezzük.
* **particionálás**: az a folyamat, amikor az adatokat mintákra osztjuk. További információk: [Partition and Sample](https://msdn.microsoft.com/library/azure/dn905960.aspx) (Particionálás és minták létrehozása).
* **előrejelzés**: a gépi tanulási modellek által előrejelzett értékeket előrejelzéseknek nevezzük. Az „előrejelzett pontszám” kifejezés is előfordulhat. Az előrejelzett pontszámok azonban nem a modellek végső kimenetei. A modell értékelése a pontszám alapján történik.
* **regresszió**: Olyan modell, amely a különböző értékek előrejelzését független változók alapján végzi el, például egy autó árát annak gyártási éve és márkája alapján becsüli meg.
* **pontszám**: betanított besorolási vagy regressziós modell által, a Machine Learning Studio [Score Model](https://msdn.microsoft.com/library/azure/dn905995.aspx) (Modell pontozása) modulja segítségével generált előre jelzett érték. A besorolási modell az előre jelzett érték valószínűségét jelző pontszámot is visszaad. Ha a modell már alkalmas pontszámok generálására, ellenőrizze a modell pontosságát az [Evaluate Model](https://msdn.microsoft.com/library/azure/dn905915.aspx) (Modell kiértékelése) modul segítségével.
* **minta**: az adathalmaz olyan része, amely az adathalmaz egészére vonatkozóan is reprezentatívnak tekinthető. A mintát kiválaszthatja véletlenszerűen vagy az adathalmaz konkrét jellemzői alapján.

## <a name="next-steps"></a>Következő lépések
A prediktív elemzés és a gépi tanulás alapjainak megismeréséhez használja [részletes oktatóanyagainkat](machine-learning-create-experiment.md) vagy [bővíthető mintáinkat](machine-learning-sample-experiments.md).  

<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/

