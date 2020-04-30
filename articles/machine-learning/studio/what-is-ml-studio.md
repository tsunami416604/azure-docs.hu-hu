---
title: Mi az ML Studio (klasszikus)
titleSuffix: Azure
description: A Azure Machine Learning Studio (klasszikus) egy fogd és vidd eszköz, amellyel gyorsan készíthet modelleket a használatra kész algoritmusok és modulok használatával.
services: machine-learning
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.topic: overview
ms.date: 03/24/2020
ms.openlocfilehash: c1772ceb514e46542129759711f2d45db39abf82
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80371974"
---
# <a name="what-is-machine-learning-studio-classic"></a>Mi az Machine Learning Studio (klasszikus)?

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

A Microsoft Azure Machine Learning Studio (klasszikus) egy együttműködésen alapuló, húzással használható eszköz, amellyel prediktív elemzési megoldásokat hozhat létre, tesztelheti és helyezhet üzembe az adatain. A Azure Machine Learning Studio (klasszikus) webszolgáltatásként teszi közzé a modelleket, amelyeket egyszerűen felhasználhat egyéni alkalmazások vagy BI-eszközök, például az Excel használatával.

Machine Learning Studio (klasszikus) az adatelemzés, a prediktív elemzés, a Felhőbeli erőforrások és az adatai.

## <a name="the-machine-learning-studio-classic--interactive-workspace"></a>Az Machine Learning Studio (klasszikus) interaktív munkaterület

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

A prediktív elemzési modell kialakításához jellemzően egy vagy több forrásból származó adatok felhasználásával, az adatok átalakításával és elemzésével különböző adatkezelési és statisztikai függvények, valamint az eredmények összessége hozható létre. Az ilyen modellek fejlesztése iteratív folyamat. A különböző függvényeket és paramétereket addig módosítjuk, az eredményeket addig közelítjük, amíg úgy nem véljük, hogy jól betanított, hatékony modellel rendelkezünk.

A Azure Machine Learning Studio (klasszikus) interaktív, vizualizációs munkaterületet biztosít a prediktív elemzési modell egyszerű létrehozásához, teszteléséhez és megismétléséhez. Az ***adatkészleteket*** és az elemzési ***modulokat*** egy interaktív vászonra húzhatja, és összekapcsolhatja őket egy ***kísérlet***létrehozásához, amelyet Machine learning Studio (klasszikus) futtat. A modell felépítésének fejlesztéséhez szerkesztheti a kísérletet, szükség esetén mentheti egy másolatát, majd újrafuttathatja. Amikor készen áll, a ***tanítási kísérletet*** átalakíthatja ***prediktív kísérletté***, majd közzéteheti***webszolgáltatásként***, hogy mások is hozzáférjenek.

A prediktív elemzési modell létrehozásához nincs szükség programozásra, valamint az adatkészletek és modulok vizuális összekapcsolására.

![Azure Machine Learning Studio (klasszikus) diagram: kísérletek létrehozása, számos forráshoz tartozó adatolvasás, pontszámmal ellátható adatmennyiség, írási modellek.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-classic-overview-diagram"></a>Az Machine Learning Studio (klasszikus) áttekintési diagram letöltése
Töltse le a **Microsoft Azure Machine learning Studio (klasszikus) képességek áttekintése** diagramot, és tekintse át a Machine learning Studio (klasszikus) képességeinek magas szintű nézetét. Hogy mindig kéznél legyen, kinyomtathatja a diagramot tabloid méretben (11 x 17-es méret).

**A diagram letöltése: [Microsoft Azure Machine learning Studio (klasszikus) képességek áttekintése](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)**
![Microsoft Azure Machine learning Studio (klasszikus) képességek áttekintése](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>A Studio (klasszikus) kísérlet összetevői
A kísérletek adathalmazokból állnak, amelyek adatokat szolgáltatnak az elemzési modulok számára; az elemzési modulok pedig összekapcsolódva prediktív elemzési modellt alkotnak. Az érvényes kísérletek a következő jellemzőkkel rendelkeznek:

* A kísérlet tartalmaz legalább egy adathalmazt és egy modult
* Az adathalmazok csak a modulokhoz csatlakoztathatók
* A modulok csatlakoztathatók adathalmazokhoz és más modulokhoz is
* A modulok összes bemeneti portjának rendelkeznie kell valamilyen kapcsolattal az adatfolyamhoz
* Minden modul összes szükséges paraméterét meg kell adni

A kísérleteket létrehozhatja nulláról, vagy egy meglévő mintakísérletet sablonként használva További információkért lásd: [Copy example experiments to create new machine learning experiments](sample-experiments.md) (Új Machine Learning-kísérletek létrehozása példakísérletek másolásával).

A kísérlet létrehozásával kapcsolatos példát a következő témakörben talál: [egyszerű kísérlet létrehozása Azure Machine learning Studio (klasszikus)](create-experiment.md).

A prediktív elemzési megoldások létrehozásának részletes ismertetését lásd: [prediktív megoldás fejlesztése Azure Machine learning Studio (klasszikus)](tutorial-part1-credit-risk.md)használatával.

### <a name="datasets"></a>Adathalmazok
Az adatkészlet olyan adatokat tartalmaz, amelyeket Machine Learning Studioba (klasszikus) töltöttek fel, hogy használhatók legyenek a modellezési folyamatban. A Machine Learning Studio (klasszikus) számos minta adatkészletet tartalmaz, amelyekkel kísérletezni tud, és szükség esetén több adatkészletet is feltölthet. Néhány példa a mintaadathalmazokra:

* **Különböző autók fogyasztási adatai** – Hengerszám, lóerő stb. alapján azonosított autók üzemanyag-fogyasztási adatai.
* **Mellrákkal kapcsolatos adatok** – Mellrák-diagnosztikai adatok.
* **Erdőtüzek adatai** – Az Északkelet-Portugáliában előfordult erdőtüzek kiterjedése.

Kísérlet létrehozásakor a vászon bal oldalán elérhető adatkészletek listájából választhat.

A Machine Learning Studio (klasszikus) részét képező minta adatkészletek listáját a következő témakörben tekintheti meg: [a mintaadatok-készletek használata Azure Machine learning Studio (klasszikus)](use-sample-datasets.md).

### <a name="modules"></a>Modulok
A modulok az adatokon végezhető algoritmusok. A Azure Machine Learning Studio (klasszikus) számos modult tartalmaz, amelyek az adatok beáramlása funkciótól kezdve a képzés, a pontozás és az érvényesítési folyamatokig terjednek. Néhány példa a mellékelt modulokra:

* [Convert to ARFF][convert-to-arff] (Átalakítás ARFF-formátumba) – Szerializált .NET-adathalmazok átalakítása attribútum-relációs fájlformátumra (ARFF).
* [Compute Elementary Statistics][elementary-statistics] (Alapvető statisztikai számítások) – Alapvető statisztikai számítások, például átlag, szórás stb. kiszámítása.
* [Linear Regression][linear-regression] (Lineáris regresszió) – Online, grádiens módszeren alapuló lineáris regressziós modell létrehozása.
* [Score Model][score-model] (Pontszámmodell) – Betanított osztályozási vagy regressziós modell pontozása.

Kísérlet létrehozásakor a vászon bal oldalán elérhető modulok listájából választhat.

A modul rendelkezhet a belső algoritmusok konfigurálásához használható paraméterek készletével. Amikor kiválaszt egy modult a vásznon, a modul paraméterei megjelennek a **Tulajdonságok** panelen a vászontól jobbra. A modell hangolásához módosíthatja a paramétereket a panelen.

Ha segítségre van lehetősége az elérhető gépi tanulási algoritmusok nagy könyvtára között, tekintse meg a következőt: [algoritmusok kiválasztása Microsoft Azure Machine learning studiohoz (klasszikus)](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>A prediktív elemzési webszolgáltatások telepítése
Miután a prediktív elemzési modell elkészült, üzembe helyezheti webszolgáltatásként közvetlenül Machine Learning Studio (klasszikus) webhelyről. További információ erről a folyamatról: [Azure Machine learning webszolgáltatások üzembe helyezése](deploy-a-machine-learning-web-service.md).

## <a name="next-steps"></a>További lépések
A prediktív elemzés és a gépi tanulás alapjaival megismerheti a [lépésenkénti](create-experiment.md) útmutatót, valamint [a minták kialakítását](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
