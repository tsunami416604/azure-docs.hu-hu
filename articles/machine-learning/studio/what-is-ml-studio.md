---
title: Mi az
titleSuffix: Azure Machine Learning Studio
description: Az Azure Machine Learning Studio egy olyan fogd és vidd eszköz gyorsan felépíthetők a modellek egy használatra kész algoritmusok és a modulok könyvtárból.
services: machine-learning
documentationcenter: ''
author: garyericson
ms.custom: seodec18
ms.author: garye
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/28/2018
ms.openlocfilehash: e454420c014460fbf44c01cb9f620d0f3273c602
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56586018"
---
# <a name="what-is-azure-machine-learning-studio"></a>Mi az Azure Machine Learning Studio?
A Microsoft Azure Machine Learning Studio olyan, az együttműködést támogató, egérrel kezelhető eszköz, amellyel az adataihoz prediktív elemzési megoldások hozhatók létre, tesztelhetők és helyezhetők üzembe. A Machine Learning Studio a modelleket webszolgáltatásként teszi közzé, amelyeket az egyéni alkalmazások vagy az Excel és más üzletiintelligencia-eszközök egyszerűen felhasználhatnak.

A Machine Learning Studio találkozási pontot biztosít az adatelemzés, a prediktív elemzés, a felhőerőforrások és az Ön adatai számára.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>A Machine Learning Studio interaktív munkaterülete
A prediktív elemzési modellek létrehozásához általában egy vagy több forrásból származó adatokat veszünk alapul, különböző adatkezelési és statisztikai függvények felhasználásával átalakítjuk őket, majd eredményhalmazt hozunk létre. Az ilyen modellek fejlesztése iteratív folyamat. A különböző függvényeket és paramétereket addig módosítjuk, az eredményeket addig közelítjük, amíg úgy nem véljük, hogy jól betanított, hatékony modellel rendelkezünk.

Az **Azure Machine Learning Studio** interaktív, grafikus munkaterületet nyújt, amelyen könnyedén létrehozhatók, tesztelhetők és fejleszthetők a prediktív elemzési modellek. Az ***adathalmazokat*** és elemzési ***modulokat*** egy interaktív ***vászonra*** húzhatja, és összekapcsolhatja őket a Machine Learning Studio eszközben futtatható kísérletekké. A modell felépítésének fejlesztéséhez szerkesztheti a kísérletet, szükség esetén mentheti egy másolatát, majd újrafuttathatja. Amikor készen áll, a ***tanítási kísérletet*** átalakíthatja ***prediktív kísérletté***, majd ***közzéteheti*** webszolgáltatásként, hogy mások is hozzáférjenek.

A prediktív elemzési modell összeállításához nincs szükség programozásra, csupán az adathalmazok és a modulok grafikus összekapcsolására.

![Az Azure Machine Learning studio diagramja: Kísérletek létrehozása, olvasása, adatok számos forrásból, pontozott adatok, modellek írása.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

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
Kattintson a **Katalógus** elemre, és megnyílik az **[Azure AI-katalógus](http://gallery.azure.ai/)**. A Gallery olyan hely, ahol az adatelemzők és fejlesztők közössége megoszthatja a Cortana Intelligence Suite összetevői használatával létrehozott megoldásokat.

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

Prediktív elemzési megoldások létrehozásának további részletes útmutatást lásd: [prediktív megoldás fejlesztése az Azure Machine Learning Studio](tutorial-part1-credit-risk.md).

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

Való eligazodást segíti gépi tanulási algoritmusok rendelkezésre álló, lásd: [algoritmusok kiválasztása a Microsoft Azure Machine Learning Studióban](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>A prediktív elemzési webszolgáltatások telepítése
Ha elkészült a prediktív elemzési modell, közvetlenül a Machine Learning Studio eszközből üzembe helyezheti webszolgáltatásként. A folyamattal kapcsolatos további információkért tekintse meg az [Azure Machine Learning webszolgáltatás üzembe helyezése](publish-a-machine-learning-web-service.md) című cikket.


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
A modellek adatokból történő betanításakor használhat egy már ismert adatkészletet, majd a legpontosabb válaszok érdekében az adatjellemzők alapján módosíthatja a modellt. Az Azure Machine Learning Studio a modell a tanítási adatokat feldolgozó algoritmusmodul, illetve funkcionális modulok, például pontozási modul épül fel.

Felügyelt tanítás során, például a csalásészlelő modell tanítása esetén egy tranzakciókat tartalmazó halmazt fog használni, amelyben minden tranzakciónál meg van jelölve, hogy az csalási célú vagy érvényes. Az adathalmazt ilyenkor véletlenszerűen fel kell darabolni: az egyik rész a modell betanítására, a másik a modell kiértékelésére való.

#### <a name="evaluation-data"></a>Az adatok kiértékelése
Ha elvégezte a modell betanítását, a fennmaradó tesztadatok segítségével értékelje ki a modellt. Ilyenkor olyan adatokat kell használni, amelyeknél tisztában van az eredményekkel, mivel így állapítható meg, hogy milyen pontosságú előrejelzésre képes a modell.

## <a name="other-common-machine-learning-terms"></a>A gépi tanulás tárgykörében gyakran használt egyéb kifejezések
* **algoritmus**: Az adatfeldolgozás, matematikai műveletek vagy automatikus indoklás alkalmazásával segít a problémák megoldásában használt szabályok olyan önálló készlete.
* **anomáliadetektálás**: Egy modellt, amely észleli, ha szokatlan események vagy értékek, és segít a problémák észlelését. A rosszindulatú bankkártyahasználat észlelése során például a rendszer szokatlan vásárlásokat keres.
* **kategorikus adatok**: Amely kategóriák szerint vannak rendszerezve, és, amely csoportokra osztható adatok. Egy gépjárműveket tartalmazó kategorizált adathalmazban például meg van adva a gyártás éve, a márka, a típus és az ár.
* **Besorolási**: Az adatpontok egy adatkészlet, amely csoportosításokat már ismert alapján kategóriákba sorolására használható modell.
* **jellemzőkiemelés**: A folyamat, vagy kiválasztunk annak érdekében, hogy növelje az adatkészlet, és javíthatja az eredményeket egy adatkészlet kapcsolódó szolgáltatások. A repülőjegyárakra vonatkozó adatokat például a hét napjainak és a munkaszüneti napoknak a kiemelésével lehet javítani. Lásd: [kiválasztása és az Azure Machine Learning Studio a mérnöki csapathoz](../team-data-science-process/create-features.md).
* **a modul**: A Machine Learning Studio modellben, például az adatbevitel modul, amely lehetővé teszi a bevitelét és szerkesztését a kis adatkészletekhez funkcionális része. A Machine Learning Studióban az algoritmusok is a modulok egyik típusának számítanak.
* **Modell**: Felügyelt tanítási modell, amely az a machine learning-kísérletből a betanítási adatok, algoritmusmodul és funkcionális modulok, például a Score Model-modul áll.
* **numerikus adatok**: Adatok, amelyek mérésekként (folytonos adatok), vagy megszámlálják (a diszkrét adatok). A numerikus adatokat *kvantitatív adatoknak* is nevezzük.
* **partíció**: A módszer osztjuk adatokat mintákra. További információk: [Partition and Sample](https://msdn.microsoft.com/library/azure/dn905960.aspx) (Particionálás és minták létrehozása).
* **előrejelzési**: Előrejelzett értékeket egy gépi tanulási modellt az nevezzük. Az „előrejelzett pontszám” kifejezés is előfordulhat. Az előrejelzett pontszámok azonban nem a modellek végső kimenetei. A modell értékelése a pontszám alapján történik.
* **Regresszió**: Egy érték előrejelzésére szolgáló modell független változók, például az egy autó árát annak év alapján előrejelzésére alapján, és győződjön meg arról.
* **pontszám**: Egy betanított besorolási vagy regressziós modell generált előre jelzett érték használatával a [Score Model-modul](https://msdn.microsoft.com/library/azure/dn905995.aspx) a Machine Learning Studióban. A besorolási modell az előre jelzett érték valószínűségét jelző pontszámot is visszaad. Ha a modell már alkalmas pontszámok generálására, ellenőrizze a modell pontosságát az [Evaluate Model](https://msdn.microsoft.com/library/azure/dn905915.aspx) (Modell kiértékelése) modul segítségével.
* **minta**: Egy adatkészlet is reprezentatívnak tekinthető, az egész részét. A mintát kiválaszthatja véletlenszerűen vagy az adathalmaz konkrét jellemzői alapján.

## <a name="next-steps"></a>További lépések
Alapismeretek a Mobilfunkciók prediktív elemzés, és a machine learning használatával egy [részletes rövid](create-experiment.md) , illetve [bővíthető mintáinkat](sample-experiments.md).


<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
