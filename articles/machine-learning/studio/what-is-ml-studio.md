---
title: Mi az ML Studio (klasszikus)
titleSuffix: Azure
description: Az Azure Machine Learning Studio (klasszikus) egy drag-and-drop eszköz gyorsan modellek et készíteni egy használatra kész könyvtár algoritmusok és modulok.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371974"
---
# <a name="what-is-machine-learning-studio-classic"></a>Mi az a Machine Learning Studio (klasszikus)?

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

A Microsoft Azure Machine Learning Studio (klasszikus) egy együttműködésen alapuló, húzással használható eszköz, amellyel prediktív elemzési megoldásokat hozhat létre, tesztelhet és helyezhet üzembe az adatokon. Az Azure Machine Learning Studio (klasszikus) olyan webszolgáltatásként teszi közzé a modelleket, amelyeket az egyéni alkalmazások vagy az üzletiintelligencia-eszközök, például az Excel könnyen használhatnak fel.

A Machine Learning Studio (klasszikus) az adattudomány, a prediktív elemzés, a felhőalapú erőforrások és az adatok találkozása.

## <a name="the-machine-learning-studio-classic--interactive-workspace"></a>A Machine Learning Studio (klasszikus) interaktív munkaterület

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Prediktív elemzési modell kifejlesztéséhez általában egy vagy több forrásból származó adatokat használ, átalakítja és elemzi ezeket az adatokat különböző adatmanipulációk és statisztikai függvények segítségével, és eredmények halmazát hozza létre. Az ilyen modellek fejlesztése iteratív folyamat. A különböző függvényeket és paramétereket addig módosítjuk, az eredményeket addig közelítjük, amíg úgy nem véljük, hogy jól betanított, hatékony modellel rendelkezünk.

Az Azure Machine Learning Studio (klasszikus) interaktív, vizuális munkaterületet biztosít a prediktív elemzési modell egyszerű létrehozásához, teszteléséhez és itetetel. Az ***adatkészleteket*** és az ***elemzési modulokat*** egy interaktív vászonra húzza, és összekapcsolja őket egy ***kísérlethez,*** amelyet a Machine Learning Studio (klasszikus) programban futtat. A modell felépítésének fejlesztéséhez szerkesztheti a kísérletet, szükség esetén mentheti egy másolatát, majd újrafuttathatja. Amikor készen áll, a ***tanítási kísérletet*** átalakíthatja ***prediktív kísérletté***, majd közzéteheti***webszolgáltatásként***, hogy mások is hozzáférjenek.

Nincs szükség programozásra, vizuálisan csatlakoztassa az adatkészleteket és a modulokat a prediktív elemzési modell létrehozásához.

![Azure Machine Learning Studio (klasszikus) diagram: Kísérletek létrehozása, adatok olvasása számos forrásból, írási pontozott adatok, írási modellek.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-classic-overview-diagram"></a>A Machine Learning Studio (klasszikus) áttekintő diagramjának letöltése
Töltse le a **Microsoft Azure Machine Learning Studio (klasszikus) képességek áttekintése** diagramot, és tekintse meg a Machine Learning Studio (klasszikus) képességeit. Hogy mindig kéznél legyen, kinyomtathatja a diagramot tabloid méretben (11 x 17-es méret).

**Töltse le a diagramot itt: [Microsoft Azure Machine Learning Studio (klasszikus) képességek – áttekintés Microsoft](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)**
![Azure Machine Learning Studio (klasszikus) képességek – áttekintés](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Egy studio (klasszikus) kísérlet összetevői
A kísérletek adathalmazokból állnak, amelyek adatokat szolgáltatnak az elemzési modulok számára; az elemzési modulok pedig összekapcsolódva prediktív elemzési modellt alkotnak. Az érvényes kísérletek a következő jellemzőkkel rendelkeznek:

* A kísérlet tartalmaz legalább egy adathalmazt és egy modult
* Az adathalmazok csak a modulokhoz csatlakoztathatók
* A modulok csatlakoztathatók adathalmazokhoz és más modulokhoz is
* A modulok összes bemeneti portjának rendelkeznie kell valamilyen kapcsolattal az adatfolyamhoz
* Minden modul összes szükséges paraméterét meg kell adni

A kísérleteket létrehozhatja nulláról, vagy egy meglévő mintakísérletet sablonként használva További információkért lásd: [Copy example experiments to create new machine learning experiments](sample-experiments.md) (Új Machine Learning-kísérletek létrehozása példakísérletek másolásával).

Egy kísérlet létrehozásához például [lásd: Egyszerű kísérlet létrehozása az Azure Machine Learning Studio -ban (klasszikus).](create-experiment.md)

A prediktív elemzési megoldás létrehozásának teljesebb forgatókönyvét az [Azure Machine Learning Studio (klasszikus) prediktív megoldásának fejlesztése](tutorial-part1-credit-risk.md)című témakörben találja.

### <a name="datasets"></a>Adathalmazok
Az adatkészlet olyan adatok, amelyeket a Machine Learning Studio (klasszikus) feltöltésre került, hogy használható legyen a modellezési folyamatban. A Machine Learning Studio (klasszikus) számos mintaadatkészletet tartalmaz, amelyekkel kísérletezhet, és szükség esetén további adatkészleteket tölthet fel. Néhány példa a mintaadathalmazokra:

* **Különböző autók fogyasztási adatai** – Hengerszám, lóerő stb. alapján azonosított autók üzemanyag-fogyasztási adatai.
* **Mellrákkal kapcsolatos adatok** – Mellrák-diagnosztikai adatok.
* **Erdőtüzek adatai** – Az Északkelet-Portugáliában előfordult erdőtüzek kiterjedése.

A kísérlet létrehozásasorán a vászon bal oldalán elérhető adatkészletek listájából választhat.

A Machine Learning Studio (klasszikus) mintaadatkészleteinek listáját [a Mintaadatkészletek használata az Azure Machine Learning Studioban (klasszikus) című témakörben tartalmazza.](use-sample-datasets.md)

### <a name="modules"></a>Modulok
A modulok az adatokon végezhető algoritmusok. Az Azure Machine Learning Studio (klasszikus) számos modullal rendelkezik, az adatbe- és betanítási függvényektől a betanítási, pontozási és érvényesítési folyamatokig. Néhány példa a mellékelt modulokra:

* [Convert to ARFF][convert-to-arff] (Átalakítás ARFF-formátumba) – Szerializált .NET-adathalmazok átalakítása attribútum-relációs fájlformátumra (ARFF).
* [Compute Elementary Statistics][elementary-statistics] (Alapvető statisztikai számítások) – Alapvető statisztikai számítások, például átlag, szórás stb. kiszámítása.
* [Linear Regression][linear-regression] (Lineáris regresszió) – Online, grádiens módszeren alapuló lineáris regressziós modell létrehozása.
* [Score Model][score-model] (Pontszámmodell) – Betanított osztályozási vagy regressziós modell pontozása.

A kísérlet létrehozásakor a vászon bal oldalán elérhető modulok listájából választhat.

A modul rendelkezhet a belső algoritmusok konfigurálásához használható paraméterek készletével. Amikor kiválaszt egy modult a vásznon, a modul paraméterei megjelennek a **Tulajdonságok** panelen a vászontól jobbra. A modell hangolásához módosíthatja a paramétereket a panelen.

A rendelkezésre álló gépi tanulási algoritmusok nagy könyvtárában való navigálásról a [Microsoft Azure Machine Learning Studio (klasszikus) algoritmusainak kiválasztása](algorithm-choice.md)című témakörben talál segítséget.

## <a name="deploying-a-predictive-analytics-web-service"></a>A prediktív elemzési webszolgáltatások telepítése
Miután a prediktív elemzési modell készen áll, üzembe helyezheti azt webszolgáltatásként közvetlenül a Machine Learning Studio (klasszikus). A folyamatról további információt az [Azure Machine Learning webszolgáltatás telepítése című témakörben](deploy-a-machine-learning-web-service.md)talál.

## <a name="next-steps"></a>További lépések
A prediktív elemzés és a gépi tanulás alapjait [részletes enyésztával](create-experiment.md) és [mintákra építve](sample-experiments.md)ismerheti meg.

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
