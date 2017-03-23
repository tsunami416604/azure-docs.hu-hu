---
title: Mi az Azure Machine Learning Studio? | Microsoft Docs
description: "Az Azure ML Studio áttekintése. Az Azure ML Studio olyan, egérrel kezelhető eszköz, amellyel egy használatra kész algoritmus- és modultárból gyorsan felépíthetők a modellek."
keywords: azure machine learning,azure ml, ml studio
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/20/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 9e738c4e5f43ae6c939f7c6da90c258498943e73
ms.openlocfilehash: b8115f1fb72b0ba89fd0c8afa3358878a0fab92b
ms.lasthandoff: 12/14/2016


---
# <a name="what-is-azure-machine-learning-studio"></a>Mi az Azure Machine Learning Studio?
A Microsoft Azure Machine Learning Studio olyan, az együttműködést támogató, egérrel kezelhető eszköz, amellyel az adataihoz prediktív elemzési megoldások hozhatók létre, tesztelhetők és helyezhetők üzembe. A Machine Learning Studio a modelleket webszolgáltatásként teszi közzé, amelyeket az egyéni alkalmazások vagy az Excel és más üzletiintelligencia-eszközök egyszerűen felhasználhatnak.

A Machine Learning Studio találkozási pontot biztosít az adatelemzés, a prediktív elemzés, a felhőerőforrások és az Ön adatai számára.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>A Machine Learning Studio interaktív munkaterülete
A prediktív elemzési modellek létrehozásához általában egy vagy több forrásból származó adatokat veszünk alapul, különböző adatkezelési és statisztikai függvények felhasználásával átalakítjuk őket, majd eredményhalmazt hozunk létre. Az ilyen modellek fejlesztése iteratív folyamat. A különböző függvényeket és paramétereket addig módosítjuk, az eredményeket addig közelítjük, amíg úgy nem véljük, hogy jól betanított, hatékony modellel rendelkezünk.

Az **Azure Machine Learning Studio** interaktív, grafikus munkaterületet nyújt, amelyen könnyedén létrehozhatók, tesztelhetők és fejleszthetők a prediktív elemzési modellek. Az ***adathalmazokat*** és elemzési ***modulokat*** egy interaktív ***vászonra*** húzhatja, és összekapcsolhatja őket a Machine Learning Studio eszközben futtatható kísérletekké. A modell felépítésének fejlesztéséhez szerkesztheti a kísérletet, szükség esetén mentheti egy másolatát, majd újrafuttathatja. Amikor készen áll, a ***tanítási kísérletet*** átalakíthatja ***prediktív kísérletté***, majd ***közzéteheti*** webszolgáltatásként, hogy mások is hozzáférjenek.

A prediktív elemzési modell összeállításához nincs szükség programozásra, csupán az adathalmazok és a modulok grafikus összekapcsolására.

> [!TIP]
> Ha szeretne egy letölthető és kinyomtatható diagramot a Machine Learning Studio funkcióiról, tekintse meg [Az Azure Machine Learning Studio funkcióit bemutató diagram](machine-learning-studio-overview-diagram.md) című cikket.
> 
> 

![Az Azure ML Studio diagramja: kísérletek létrehozása, adatok beolvasása számos forrásból, pontozott adatok és modellek írása.][ml-studio-overview]

## <a name="get-started-with-machine-learning-studio"></a>A Machine Learning Studio használatának első lépései
Amikor először belép a [Machine Learning Studio](https://studio.azureml.net) felületére, a **Kezdőlap** oldal jelenik meg. Innen kiindulva megtekintheti a dokumentációt, valamint videókat, webes előadások és más hasznos forrásokat érhet el.

Kattintson a bal felső menüre, ![Menü](media/machine-learning-what-is-ml-studio/menu.png) amelyben számos lehetőséget fog látni.

### <a name="cortana-intelligence"></a>Cortana Intelligence
Kattintson a **Cortana Intelligence** elemre, és megnyílik a [Cortana Intelligence Suite](https://www.microsoft.com/cloud-platform/cortana-intelligence-suite) kezdőlapja. A Cortana Intelligence Suite egy teljes mértékben felügyelt big data-feldolgozó és összetett elemzéseket végző csomag, amelynek segítségével az adatait intelligens műveletekké alakíthatja. A teljes dokumentációt, valamint az ügyfelek beszámolóit a csomag honlapján találja.

### <a name="azure-machine-learning"></a>Azure Machine Learning
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
Kattintson a **Katalógus** lapra, és megnyílik a **[Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/)**. A Gallery olyan hely, ahol az adatelemzők és fejlesztők közössége megoszthatja a Cortana Intelligence Suite összetevői használatával létrehozott megoldásokat.

Erről további információkat a [Megoldások megosztása és felfedezése a Cortana Intelligence Galleryben](machine-learning-gallery-how-to-use-contribute-publish.md) című cikkben talál.

## <a name="components-of-an-experiment"></a>A kísérletek összetevői
A kísérletek adathalmazokból állnak, amelyek adatokat szolgáltatnak az elemzési modulok számára; az elemzési modulok pedig összekapcsolódva prediktív elemzési modellt alkotnak. Az érvényes kísérletek a következő jellemzőkkel rendelkeznek:

* A kísérlet tartalmaz legalább egy adathalmazt és egy modult
* Az adathalmazok csak a modulokhoz csatlakoztathatók
* A modulok csatlakoztathatók adathalmazokhoz és más modulokhoz is
* A modulok összes bemeneti portjának rendelkeznie kell valamilyen kapcsolattal az adatfolyamhoz
* Minden modul összes szükséges paraméterét meg kell adni

A kísérleteket létrehozhatja nulláról, vagy egy meglévő mintakísérletet sablonként használva További információért tekintse meg az [Új kísérletek létrehozása a mintakísérletek használatával](machine-learning-sample-experiments.md) című cikket.

Egy egyszerű kísérlet létrehozására láthat példát az [Egyszerű kísérlet létrehozása az Azure Machine Learning Studio eszközben](machine-learning-create-experiment.md) című cikkben.

A prediktív elemzési megoldások létrehozásának részletesebb leírásáért tekintse meg a [Prediktív megoldás kifejlesztése az Azure Machine Learning segítségével](machine-learning-walkthrough-develop-predictive-solution.md) című cikket.

### <a name="datasets"></a>Adathalmazok
Az adathalmaz olyan adatokból áll, amelyeket feltöltöttek a Machine Learning Studio eszközben, hogy fel lehessen őket használni a modellezési folyamatban. A Machine Learning Studio tartalmaz néhány mintaadathalmazt, amelyekkel kísérletezhet, és szükség esetén további adathalmazokat is feltölthet. Néhány példa a mintaadathalmazokra:

* **Különböző autók fogyasztási adatai** – Hengerszám, lóerő stb. alapján azonosított autók üzemanyag-fogyasztási adatai.
* **Mellrákkal kapcsolatos adatok** – Mellrák-diagnosztikai adatok.
* **Erdőtüzek adatai** – Az Északkelet-Portugáliában előfordult erdőtüzek kiterjedése.

A kísérlet létrehozása során a vászon bal oldalán megjelenő adathalmazlistából választhat.

A Machine Learning Studio mintaadathalmazainak listájáért tekintse meg a [Mintaadatkészletek használata az Azure Machine Learning Studio eszközben](machine-learning-use-sample-datasets.md) című cikket.

### <a name="modules"></a>Modulok
A modulok az adatokon végezhető algoritmusok. A Machine Learning Studio számos modult tartalmaz, amelyek az adatbeviteli függvényektől a betanítási, pontozási és ellenőrzési folyamatokig terjednek. Néhány példa a mellékelt modulokra:

* [Convert to ARFF][convert-to-arff] (Átalakítás ARFF-formátumba) – Szerializált .NET-adathalmazok átalakítása attribútum-relációs fájlformátumra (ARFF).
* [Compute Elementary Statistics][elementary-statistics] (Alapvető statisztikai számítások) – Alapvető statisztikai számítások, például átlag, szórás stb. kiszámítása.
* [Linear Regression][linear-regression] (Lineáris regresszió) – Online, grádiens módszeren alapuló lineáris regressziós modell létrehozása.
* [Score Model][score-model] (Pontszámmodell) – Betanított osztályozási vagy regressziós modell pontozása.

A kísérlet létrehozása során a vászon bal oldalán megjelenő modullistából választhat.  

A modul rendelkezhet a belső algoritmusok konfigurálásához használható paraméterek készletével. Amikor kiválaszt egy modult a vásznon, a modul paraméterei megjelennek a **Tulajdonságok** panelen a vászontól jobbra. A modell hangolásához módosíthatja a paramétereket a panelen.

A rendelkezésre álló gépi tanulási algoritmusok hatalmas katalógusában való eligazodást segíti az [Algoritmusok kiválasztása a Microsoft Azure Machine Learning szolgáltatáshoz](machine-learning-algorithm-choice.md) című fejezet.

## <a name="deploying-a-predictive-analytics-web-service"></a>A prediktív elemzési webszolgáltatások telepítése
Ha elkészült a prediktív elemzési modell, közvetlenül a Machine Learning Studio eszközből üzembe helyezheti webszolgáltatásként. A folyamattal kapcsolatos további információkért tekintse meg az [Azure Machine Learning webszolgáltatás üzembe helyezése](machine-learning-publish-a-machine-learning-web-service.md) című cikket.

[ml-studio-overview]:./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/

