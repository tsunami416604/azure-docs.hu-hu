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
ms.date: 04/20/2019
ms.openlocfilehash: dd1eaa95a23deed0bf2098995be43402c605defc
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024231"
---
# <a name="what-is-azure-machine-learning-studio"></a>Mi az Azure Machine Learning Studio?
A Microsoft Azure Machine Learning Studio olyan, az együttműködést támogató, egérrel kezelhető eszköz, amellyel az adataihoz prediktív elemzési megoldások hozhatók létre, tesztelhetők és helyezhetők üzembe. A Machine Learning Studio a modelleket webszolgáltatásként teszi közzé, amelyeket az egyéni alkalmazások vagy az Excel és más üzletiintelligencia-eszközök egyszerűen felhasználhatnak.

A Machine Learning Studio találkozási pontot biztosít az adatelemzés, a prediktív elemzés, a felhőerőforrások és az Ön adatai számára.


## <a name="the-machine-learning-studio-interactive-workspace"></a>A Machine Learning Studio interaktív munkaterülete
A prediktív elemzési modellek fejlesztése, egy data általában használhatja, vagy további források, átalakíthatja és elemezheti az adatokat különböző adatkezelési és statisztikai függvények keresztül, és eredmények létrehozásához. Az ilyen modellek fejlesztése iteratív folyamat. A különböző függvényeket és paramétereket addig módosítjuk, az eredményeket addig közelítjük, amíg úgy nem véljük, hogy jól betanított, hatékony modellel rendelkezünk.

Az **Azure Machine Learning Studio** interaktív, grafikus munkaterületet nyújt, amelyen könnyedén létrehozhatók, tesztelhetők és fejleszthetők a prediktív elemzési modellek. Az ***adathalmazokat*** és elemzési ***modulokat*** egy interaktív ***vászonra*** húzhatja, és összekapcsolhatja őket a Machine Learning Studio eszközben futtatható kísérletekké. A modell felépítésének fejlesztéséhez szerkesztheti a kísérletet, szükség esetén mentheti egy másolatát, majd újrafuttathatja. Amikor készen áll, a ***tanítási kísérletet*** átalakíthatja ***prediktív kísérletté***, majd ***közzéteheti*** webszolgáltatásként, hogy mások is hozzáférjenek.

A prediktív elemzési modell összeállításához nincs szükség programozásra, csupán az adathalmazok és a modulok grafikus összekapcsolására.

![Az Azure Machine Learning studio diagramja: Kísérletek létrehozása, olvasása, adatok számos forrásból, pontozott adatok, modellek írása.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-overview-diagram"></a>Töltse le a Machine Learning Studio áttekintési diagramját
Töltse le a **Microsoft Azure Machine Learning Studio képességeinek áttekintése** diagramot, hogy egy átfogó képet kapjon a Machine Learning Studio képességeiről. Hogy mindig kéznél legyen, kinyomtathatja a diagramot tabloid méretben (11 x 17-es méret).

**Töltse le a diagramot innen: [Microsoft Azure Machine Learning Studio képességeinek áttekintése](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)**
![Microsoft Azure Machine Learning Studio képességeinek áttekintése](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)

## <a name="get-started-with-machine-learning-studio"></a>A Machine Learning Studio használatának első lépései
Amikor először belép a Machine Learning Studióban] (https://studio.azureml.net) láthatja a **kezdőlap** lapot. Innen kiindulva megtekintheti a dokumentációt, valamint videókat, webes előadások és más hasznos forrásokat érhet el.

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
Kattintson a **Katalógus** elemre, és megnyílik az **[Azure AI-katalógus](https://gallery.azure.ai/)**. A Gallery olyan hely, ahol az adatelemzők és fejlesztők közössége megoszthatja a Cortana Intelligence Suite összetevői használatával létrehozott megoldásokat.

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

A kísérlet létrehozása, mert közül választhat a rendelkezésre álló adatkészletek listája a vászon bal oldalán.

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

<a name="compare"></a>
## <a name="how-is-machine-learning-studio-different-from-azure-machine-learning-service"></a>Miben különbözik a Machine Learning Studio Azure Machine Learning szolgáltatás?

[Az Azure Machine Learning szolgáltatás](../service/overview-what-is-azure-ml.md) mindkét SDK-kat biztosít **- és -** egy vizuális interface(preview), hogy gyorsan előkészítheti az adatokat, betanítását és gépi tanulási modellek üzembe helyezése. A vizuális felhasználói felületet (előzetes verzió) Studio hasonló fogd és vidd élményt nyújt. Azonban az ellentétben a szellemi tulajdont képező számítási platform Studio, a vizuális felhasználói felületet a saját számítási erőforrást használ, és teljesen integrálva van az Azure Machine Learning szolgáltatás.

Íme egy gyors összehasonlítása.

|| Machine Learning Studio | Azure Machine Learning service:<br/>Vizuális felhasználói felületet|
|---| --- | --- |
|| Általánosan elérhető (GA) | Előzetes verzióban|
|Modulok interfész| Számos | A kezdeti beállítás népszerű-modulok|
|Képzési számítási célnak| Saját fejlesztésű számítási célnak, csak CPU-támogatás| Az Azure Machine Learning compute gpu-n és CPU támogatja.<br/>(Más számítási erőforrások támogatott SDK-ban)|
|Üzembe helyezés számítási célnak| Saját fejlesztésű webes szolgáltatás formátumú, nem testre szabható | Vállalati biztonsági beállítások és az Azure Kubernetes Service-ben. <br/>([Más számítási erőforrások](../service/how-to-deploy-and-where.md) támogatott SDK-ban) |
|Automatizált modellek tanítása és a hiperparaméter finomhangolása | Nem | Még nincs a vizuális felhasználói felületet. <br/> (Az SDK-t és az Azure Portalon támogatott.) | 

Próbálja ki a vizuális felhasználói felületet (előzetes verzió) az [a rövid útmutató: Készítse elő és kód írása nélkül az adatok megjelenítése](../service/ui-quickstart-run-experiment.md)

> [!NOTE]
> A Studióban létrehozott modelleket használva nem kell telepíteni, vagy az Azure Machine Learning szolgáltatás kezeli. Modellek létrehozása és telepítése a szolgáltatás vizuális felületen felügyelhető, az Azure Machine Learning szolgáltatás munkaterületén.

## <a name="free-trial"></a>Ingyenes próbaidőszak

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="next-steps"></a>További lépések
Alapismeretek a Mobilfunkciók prediktív elemzés, és a machine learning használatával egy [részletes rövid](create-experiment.md) , illetve [bővíthető mintáinkat](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
