---
title: Mi a
titleSuffix: Azure Machine Learning Studio
description: A Azure Machine Learning Studio egy drag-drop eszköz, amellyel gyorsan felépíthető modellek a használatra kész algoritmusok és modulok használatával.
services: machine-learning
documentationcenter: ''
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/20/2019
ms.openlocfilehash: ce1e5f349f55074b53cf447126c411a7a1cd3394
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516905"
---
# <a name="what-is-azure-machine-learning-studio"></a>Mi az Azure Machine Learning Studio?
A Microsoft Azure Machine Learning Studio olyan, az együttműködést támogató, egérrel kezelhető eszköz, amellyel az adataihoz prediktív elemzési megoldások hozhatók létre, tesztelhetők és helyezhetők üzembe. A Machine Learning Studio a modelleket webszolgáltatásként teszi közzé, amelyeket az egyéni alkalmazások vagy az Excel és más üzletiintelligencia-eszközök egyszerűen felhasználhatnak.

A Machine Learning Studio találkozási pontot biztosít az adatelemzés, a prediktív elemzés, a felhőerőforrások és az Ön adatai számára.


## <a name="the-machine-learning-studio-interactive-workspace"></a>A Machine Learning Studio interaktív munkaterülete
A prediktív elemzési modell kialakításához jellemzően egy vagy több forrásból származó adatok felhasználásával, az adatok átalakításával és elemzésével különböző adatkezelési és statisztikai függvények, valamint az eredmények összessége hozható létre. Az ilyen modellek fejlesztése iteratív folyamat. A különböző függvényeket és paramétereket addig módosítjuk, az eredményeket addig közelítjük, amíg úgy nem véljük, hogy jól betanított, hatékony modellel rendelkezünk.

Az **Azure Machine Learning Studio** interaktív, grafikus munkaterületet nyújt, amelyen könnyedén létrehozhatók, tesztelhetők és fejleszthetők a prediktív elemzési modellek. Az ***adathalmazokat*** és elemzési ***modulokat*** egy interaktív ***vászonra*** húzhatja, és összekapcsolhatja őket a Machine Learning Studio eszközben futtatható kísérletekké. A modell felépítésének fejlesztéséhez szerkesztheti a kísérletet, szükség esetén mentheti egy másolatát, majd újrafuttathatja. Amikor készen áll, a ***tanítási kísérletet*** átalakíthatja ***prediktív kísérletté***, majd ***közzéteheti*** webszolgáltatásként, hogy mások is hozzáférjenek.

A prediktív elemzési modell összeállításához nincs szükség programozásra, csupán az adathalmazok és a modulok grafikus összekapcsolására.

![Azure Machine Learning Studio-diagram: Hozzon létre kísérleteket, olvasson be több forrást, írjon be egy gólt, és írjon modelleket.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-overview-diagram"></a>Töltse le a Machine Learning Studio áttekintési diagramját
Töltse le a **Microsoft Azure Machine Learning Studio képességeinek áttekintése** diagramot, hogy egy átfogó képet kapjon a Machine Learning Studio képességeiről. Hogy mindig kéznél legyen, kinyomtathatja a diagramot tabloid méretben (11 x 17-es méret).

**A diagram letöltése: [A Microsoft Azure Machine learning Studio képességek áttekintése](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)** 
MicrosoftAzureMachinelearningStudioképességek![áttekintése](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)

## <a name="get-started-with-machine-learning-studio"></a>A Machine Learning Studio használatának első lépései
Amikor először írja be Machine learning Studio,] (https://studio.azureml.net) a kezdőlapot  látja. Innen kiindulva megtekintheti a dokumentációt, valamint videókat, webes előadások és más hasznos forrásokat érhet el.

Kattintson a bal felső menüre, ![Menü](./media/what-is-ml-studio/menu.png) amelyben számos lehetőséget fog látni.
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
Kattintson a **Katalógus** elemre, és megnyílik az **[Azure AI-katalógus](https://gallery.azure.ai/)** . A Gallery olyan hely, ahol az adatelemzők és fejlesztők közössége megoszthatja a Cortana Intelligence Suite összetevői használatával létrehozott megoldásokat.

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

A prediktív elemzési megoldások létrehozásával kapcsolatos további útmutatásért tekintse meg a [prediktív megoldás fejlesztése Azure Machine learning Studio](tutorial-part1-credit-risk.md)használatával című témakört.

### <a name="datasets"></a>Adathalmazok
Az adathalmaz olyan adatokból áll, amelyeket feltöltöttek a Machine Learning Studio eszközben, hogy fel lehessen őket használni a modellezési folyamatban. A Machine Learning Studio tartalmaz néhány mintaadathalmazt, amelyekkel kísérletezhet, és szükség esetén további adathalmazokat is feltölthet. Néhány példa a mintaadathalmazokra:

* **Különböző autók fogyasztási adatai** – Hengerszám, lóerő stb. alapján azonosított autók üzemanyag-fogyasztási adatai.
* **Mellrákkal kapcsolatos adatok** – Mellrák-diagnosztikai adatok.
* **Erdőtüzek adatai** – Az Északkelet-Portugáliában előfordult erdőtüzek kiterjedése.

Kísérlet létrehozásakor a vászon bal oldalán elérhető adatkészletek listájából választhat.

A Machine Learning Studio mintaadathalmazainak listájáért tekintse meg a [Mintaadatkészletek használata az Azure Machine Learning Studio eszközben](use-sample-datasets.md) című cikket.

### <a name="modules"></a>Modulok
A modulok az adatokon végezhető algoritmusok. A Machine Learning Studio számos modult tartalmaz, amelyek az adatbeviteli függvényektől a betanítási, pontozási és ellenőrzési folyamatokig terjednek. Néhány példa a mellékelt modulokra:

* [Konvertálás ARFF][convert-to-arff] – a .net szerializált adatkészletet attribútum-kapcsolati fájlformátumra (ARFF) alakítja.
* [Számítási elemi statisztikák][elementary-statistics] – kiszámítja az általános statisztikai adatokat, például a középértéket, a szórást stb.
* [Lineáris regresszió][linear-regression] – egy online színátmenet-alapú lineáris regressziós modellt hoz létre.
* [Pontszám modell][score-model] – a betanított besorolási vagy regressziós modell pontszáma.

A kísérlet létrehozása során a vászon bal oldalán megjelenő modullistából választhat.

A modul rendelkezhet a belső algoritmusok konfigurálásához használható paraméterek készletével. Amikor kiválaszt egy modult a vásznon, a modul paraméterei megjelennek a **Tulajdonságok** panelen a vászontól jobbra. A modell hangolásához módosíthatja a paramétereket a panelen.

Ha segítségre van lehetősége az elérhető gépi tanulási algoritmusok nagy könyvtára között, tekintse meg az [algoritmusok kiválasztása a Microsoft Azure Machine learning studiohoz](algorithm-choice.md)című témakört.

## <a name="deploying-a-predictive-analytics-web-service"></a>A prediktív elemzési webszolgáltatások telepítése
Ha elkészült a prediktív elemzési modell, közvetlenül a Machine Learning Studio eszközből üzembe helyezheti webszolgáltatásként. A folyamattal kapcsolatos további információkért tekintse meg az [Azure Machine Learning webszolgáltatás üzembe helyezése](publish-a-machine-learning-web-service.md) című cikket.

<a name="compare"></a>
## <a name="how-is-machine-learning-studio-different-from-azure-machine-learning-service"></a>Miben különbözik Machine Learning Studio Azure Machine Learning szolgáltatástól?

A [Azure Machine learning szolgáltatás](../service/overview-what-is-azure-ml.md) az SDK **-k és a-** a vizualizációs felületet (előzetes verzió) is lehetővé teszi az adatelőkészítés gyors előállításához, a gépi tanulási modellek betanításához és telepítéséhez. Ez a vizuális felület (előzetes verzió) hasonló húzási felületet biztosít a studióhoz. A Studio tulajdonosi számítási platformjának eltérően azonban a vizualizációs felület saját számítási erőforrásokat használ, és teljes mértékben integrálva van Azure Machine Learning szolgáltatásba.

Íme egy gyors összehasonlítás.

|| Machine Learning Studio | Azure Machine Learning szolgáltatás:<br/>Vizuális felület|
|---| --- | --- |
|| Általánosan elérhető (GA) | Előzetes verzióban|
|Illesztőfelületi modulok| Számos | Népszerű modulok kezdeti készlete|
|Számítási célok betanítása| Tulajdonosi számítási cél, csak CPU-támogatás| A Azure Machine Learning számítást, GPU-t vagy CPU-t támogatja.<br/>(Egyéb, az SDK-ban támogatott számítások)|
|Üzembe helyezési számítási célok| Tulajdonosi webszolgáltatás formátuma, nem testreszabható | Vállalati biztonsági beállítások & Azure Kubernetes szolgáltatás. <br/>([Egyéb](../service/how-to-deploy-and-where.md) , az SDK-ban támogatott számítások) |
|Automatizált modellek betanítása és hiperparaméter finomhangolása | Nem | Még nem a Visual Interface-ben. <br/> (Támogatott az SDK-ban és a Azure Portal.) | 

Próbálja ki a vizualizációs felületet (előzetes verzió [) a gyors üzembe helyezéssel: Az adatelőkészítés és a kód írása nélküli megjelenítése](../service/ui-quickstart-run-experiment.md)

> [!NOTE]
> A Studióban létrehozott modellek nem helyezhetők üzembe vagy nem kezelhetők Azure Machine Learning szolgáltatással. A szolgáltatás vizualizációs felületén létrehozott és üzembe helyezett modellek azonban a Azure Machine Learning szolgáltatás munkaterületén kezelhetők.

## <a name="free-trial"></a>Ingyenes próbaidőszak

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="next-steps"></a>További lépések
A prediktív elemzés és a gépi tanulás alapjaival megismerheti a [lépésenkénti](create-experiment.md) útmutatót, valamint [a minták kialakítását](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
