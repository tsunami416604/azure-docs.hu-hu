---
title: Mi az Azure Machine Learning Studio? | Microsoft Docs
description: Az Azure ML Studio áttekintése. Az Azure ML Studio olyan, egérrel kezelhető eszköz, amellyel egy használatra kész algoritmus- és modultárból gyorsan felépíthetők a modellek.
keywords: azure machine learning,azure ml, ml studio
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/28/2018
ms.openlocfilehash: cfe7f1dd932da45ae057b73817acd6bc4f859edd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989838"
---
# <a name="what-is-azure-machine-learning-studio"></a>Mi az Azure Machine Learning Studio?
A Microsoft Azure Machine Learning Studio olyan, az együttműködést támogató, egérrel kezelhető eszköz, amellyel az adataihoz prediktív elemzési megoldások hozhatók létre, tesztelhetők és helyezhetők üzembe. A Machine Learning Studio a modelleket webszolgáltatásként teszi közzé, amelyeket az egyéni alkalmazások vagy az Excel és más üzletiintelligencia-eszközök egyszerűen felhasználhatnak.

A Machine Learning Studio találkozási pontot biztosít az adatelemzés, a prediktív elemzés, a felhőerőforrások és az Ön adatai számára.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>A Machine Learning Studio interaktív munkaterülete
A prediktív elemzési modellek létrehozásához általában egy vagy több forrásból származó adatokat veszünk alapul, különböző adatkezelési és statisztikai függvények felhasználásával átalakítjuk őket, majd eredményhalmazt hozunk létre. Az ilyen modellek fejlesztése iteratív folyamat. A különböző függvényeket és paramétereket addig módosítjuk, az eredményeket addig közelítjük, amíg úgy nem véljük, hogy jól betanított, hatékony modellel rendelkezünk.

Az **Azure Machine Learning Studio** interaktív, grafikus munkaterületet nyújt, amelyen könnyedén létrehozhatók, tesztelhetők és fejleszthetők a prediktív elemzési modellek. Az ***adathalmazokat*** és elemzési ***modulokat*** egy interaktív ***vászonra*** húzhatja, és összekapcsolhatja őket a Machine Learning Studio eszközben futtatható kísérletekké. A modell felépítésének fejlesztéséhez szerkesztheti a kísérletet, szükség esetén mentheti egy másolatát, majd újrafuttathatja. Amikor készen áll, a ***tanítási kísérletet*** átalakíthatja ***prediktív kísérletté***, majd ***közzéteheti*** webszolgáltatásként, hogy mások is hozzáférjenek.

A prediktív elemzési modell összeállításához nincs szükség programozásra, csupán az adathalmazok és a modulok grafikus összekapcsolására.

> [!TIP]
> Ha szeretne egy letölthető és kinyomtatható diagramot a Machine Learning Studio funkcióiról, tekintse meg [Az Azure Machine Learning Studio funkcióit bemutató diagram](studio-overview-diagram.md) című cikket.
> 
> 

![Az Azure ML Studio diagramja: kísérletek létrehozása, adatok beolvasása számos forrásból, pontozott adatok és modellek írása.][ml-studio-overview]

## <a name="get-started-with-machine-learning-studio"></a>A Machine Learning Studio használatának első lépései
Amikor először belép a [Machine Learning Studio](https://studio.azureml.net) felületére, a **Kezdőlap** oldal jelenik meg. Innen kiindulva megtekintheti a dokumentációt, valamint videókat, webes előadások és más hasznos forrásokat érhet el.

Kattintson a bal felső menüre, ![Menü](./media/what-is-ml-studio/menu.png) amelyben számos lehetőséget fog látni.

### <a name="cortana-intelligence"></a>Cortana Intelligence
Kattintson a **Cortana Intelligence** elemre, és megnyílik a [Cortana Intelligence Suite](https://www.microsoft.com/cloud-platform/cortana-intelligence-suite) kezdőlapja. A Cortana Intelligence Suite egy teljes mértékben felügyelt big data-feldolgozó és összetett elemzéseket végző csomag, amelynek segítségével az adatait intelligens műveletekké alakíthatja. A teljes dokumentációt, valamint az ügyfelek beszámolóit a csomag honlapján találja.

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
Két lehetőség érhető el: a **Kezdőlap**, vagyis a kiindulási oldal, valamint a **Studio**.

Kattintson a **Studio** elemre, és megnyílik az **Azure Machine Learning Studio**. A rendszer felkéri, hogy jelentkezzen be a Microsoft-fiókjával, illetve a munkahelyi vagy iskolai fiókjával. Miután bejelentkezett, bal oldalon az alábbi lapokat fogja látni:

* **PROJEKTEK** – Az egyes projekteket alkotó kísérletek, adatkészletek, jegyzetek és egyéb erőforrások gyűjteményei
* **KÍSÉRLETEK** – A létrehozott és futtatott vagy vázlatként mentett kísérletek
* **WEBSZOLGÁLTATÁSOK** – A kísérletekből üzembe helyezett webszolgáltatások
* **NOTEBOOKOK** – A létrehozott Jupyter notebookok
* **ADATHALMAZOK** – A Studióba feltöltött adathalmazok
* **BETANÍTOTT MODELLEK** – A kísérletek során betanított és a Studio eszközbe mentett kísérletek
* **BEÁLLÍTÁSOK** – A fiók és az erőforrások konfigurálására szolgáló beállítások gyűjteménye

### <a name="gallery"></a>Katalógus
Kattintson a **Katalógus** elemre, és megnyílik az **[Azure AI-katalógus](http://gallery.cortanaintelligence.com/)**. A Gallery olyan hely, ahol az adatelemzők és fejlesztők közössége megoszthatja a Cortana Intelligence Suite összetevői használatával létrehozott megoldásokat.

Erről további információkat a [Megoldások megosztása és felfedezése az Azure AI-katalógusban](gallery-how-to-use-contribute-publish.md) című cikkben talál.

## <a name="components-of-an-experiment"></a>A kísérletek összetevői
A kísérletek adathalmazokból állnak, amelyek adatokat szolgáltatnak az elemzési modulok számára; az elemzési modulok pedig összekapcsolódva prediktív elemzési modellt alkotnak. Az érvényes kísérletek a következő jellemzőkkel rendelkeznek:

* A kísérlet tartalmaz legalább egy adathalmazt és egy modult
* Az adathalmazok csak a modulokhoz csatlakoztathatók
* A modulok csatlakoztathatók adathalmazokhoz és más modulokhoz is
* A modulok összes bemeneti portjának rendelkeznie kell valamilyen kapcsolattal az adatfolyamhoz
* Minden modul összes szükséges paraméterét meg kell adni

A kísérleteket létrehozhatja nulláról, vagy egy meglévő mintakísérletet sablonként használva További információkért lásd: [Copy example experiments to create new machine learning experiments](sample-experiments.md) (Új Machine Learning-kísérletek létrehozása példakísérletek másolásával).

Egy egyszerű kísérlet létrehozására láthat példát az [Egyszerű kísérlet létrehozása az Azure Machine Learning Studio eszközben](create-experiment.md) című cikkben.

A prediktív elemzési megoldások létrehozásának részletesebb leírásáért tekintse meg a [Prediktív megoldás kifejlesztése az Azure Machine Learning segítségével](walkthrough-develop-predictive-solution.md) című cikket.

### <a name="datasets"></a>Adathalmazok
Az adathalmaz olyan adatokból áll, amelyeket feltöltöttek a Machine Learning Studio eszközben, hogy fel lehessen őket használni a modellezési folyamatban. A Machine Learning Studio tartalmaz néhány mintaadathalmazt, amelyekkel kísérletezhet, és szükség esetén további adathalmazokat is feltölthet. Néhány példa a mintaadathalmazokra:

* **Különböző autók fogyasztási adatai** – Hengerszám, lóerő stb. alapján azonosított autók üzemanyag-fogyasztási adatai.
* **Mellrákkal kapcsolatos adatok** – Mellrák-diagnosztikai adatok.
* **Erdőtüzek adatai** – Az Északkelet-Portugáliában előfordult erdőtüzek kiterjedése.

A kísérlet létrehozása során a vászon bal oldalán megjelenő adathalmazlistából választhat.

A Machine Learning Studio mintaadathalmazainak listájáért tekintse meg a [Mintaadatkészletek használata az Azure Machine Learning Studio eszközben](use-sample-datasets.md) című cikket.

### <a name="modules"></a>Modulok
A modulok az adatokon végezhető algoritmusok. A Machine Learning Studio számos modult tartalmaz, amelyek az adatbeviteli függvényektől a betanítási, pontozási és ellenőrzési folyamatokig terjednek. Néhány példa a mellékelt modulokra:

* [Convert to ARFF][convert-to-arff] (Átalakítás ARFF-formátumba) – Szerializált .NET-adathalmazok átalakítása attribútum-relációs fájlformátumra (ARFF).
* [Compute Elementary Statistics][elementary-statistics] (Alapvető statisztikai számítások) – Alapvető statisztikai számítások, például átlag, szórás stb. kiszámítása.
* [Linear Regression][linear-regression] (Lineáris regresszió) – Online, grádiens módszeren alapuló lineáris regressziós modell létrehozása.
* [Score Model][score-model] (Pontszámmodell) – Betanított osztályozási vagy regressziós modell pontozása.

A kísérlet létrehozása során a vászon bal oldalán megjelenő modullistából választhat.  

A modul rendelkezhet a belső algoritmusok konfigurálásához használható paraméterek készletével. Amikor kiválaszt egy modult a vásznon, a modul paraméterei megjelennek a **Tulajdonságok** panelen a vászontól jobbra. A modell hangolásához módosíthatja a paramétereket a panelen.

A rendelkezésre álló gépi tanulási algoritmusok hatalmas katalógusában való eligazodást segíti az [Algoritmusok kiválasztása a Microsoft Azure Machine Learning szolgáltatáshoz](algorithm-choice.md) című fejezet.

## <a name="deploying-a-predictive-analytics-web-service"></a>A prediktív elemzési webszolgáltatások telepítése
Ha elkészült a prediktív elemzési modell, közvetlenül a Machine Learning Studio eszközből üzembe helyezheti webszolgáltatásként. A folyamattal kapcsolatos további információkért tekintse meg az [Azure Machine Learning webszolgáltatás üzembe helyezése](publish-a-machine-learning-web-service.md) című cikket.

[ml-studio-overview]:./media/what-is-ml-studio/azure-ml-studio-diagram.jpg



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
* **jellemzőkiemelés**: az a folyamat, amikor bizonyos jellemzőket kinyerünk vagy kiválasztunk egy adathalmazból az adathalmaz és az eredmények minőségének javítása érdekében. A repülőjegyárakra vonatkozó adatokat például a hét napjainak és a munkaszüneti napoknak a kiemelésével lehet javítani. További információk: [Feature selection and engineering in Azure Machine Learning](../team-data-science-process/create-features.md) (Jellemzőkiválasztás és -kiemelés az Azure Machine Learngingben).
* **modul**: A Machine Learning Studio-modell funkcionális része, például az Enter Data (Adatbevitel) modul, amely kis adatkészletek bevitelét és szerkesztését teszi lehetővé. A Machine Learning Studióban az algoritmusok is a modulok egyik típusának számítanak.
* **modell**: Egy Machine Learning-kísérlet eredményeként létrejövő felügyelt tanítási modell, amely betanítási adatokból, algoritmusmodulból és különböző funkcionális modulokból (például a Score Model (Modell pontozása) modul) áll.
* **numerikus adatok**: mérésekként (folytonos adatok) vagy megszámlált értékekként (diszkrét adatok) értelmezhető adatok. A numerikus adatokat *kvantitatív adatoknak* is nevezzük.
* **particionálás**: az a folyamat, amikor az adatokat mintákra osztjuk. További információk: [Partition and Sample](https://msdn.microsoft.com/library/azure/dn905960.aspx) (Particionálás és minták létrehozása).
* **előrejelzés**: a gépi tanulási modellek által előrejelzett értékeket előrejelzéseknek nevezzük. Az „előrejelzett pontszám” kifejezés is előfordulhat. Az előrejelzett pontszámok azonban nem a modellek végső kimenetei. A modell értékelése a pontszám alapján történik.
* **regresszió**: Olyan modell, amely a különböző értékek előrejelzését független változók alapján végzi el, például egy autó árát annak gyártási éve és márkája alapján becsüli meg.
* **pontszám**: betanított besorolási vagy regressziós modell által, a Machine Learning Studio [Score Model](https://msdn.microsoft.com/library/azure/dn905995.aspx) (Modell pontozása) modulja segítségével generált előre jelzett érték. A besorolási modell az előre jelzett érték valószínűségét jelző pontszámot is visszaad. Ha a modell már alkalmas pontszámok generálására, ellenőrizze a modell pontosságát az [Evaluate Model](https://msdn.microsoft.com/library/azure/dn905915.aspx) (Modell kiértékelése) modul segítségével.
* **minta**: az adathalmaz olyan része, amely az adathalmaz egészére vonatkozóan is reprezentatívnak tekinthető. A mintát kiválaszthatja véletlenszerűen vagy az adathalmaz konkrét jellemzői alapján.

## <a name="next-steps"></a>További lépések
A prediktív elemzés és a gépi tanulás alapjainak megismeréséhez használja [részletes oktatóanyagainkat](create-experiment.md) vagy [bővíthető mintáinkat](sample-experiments.md).  


<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
