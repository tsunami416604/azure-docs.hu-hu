---
title: Felelős Machine Learning (ML)
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogy mi a felelős ML, és hogyan használható a Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/08/2020
ms.openlocfilehash: 09bb7710df19482e9625c1834d2646f70368fc1d
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85920367"
---
# <a name="responsible-machine-learning-ml"></a>Felelős Machine Learning (ML)

Ebből a cikkből megtudhatja, hogy a felelős ML milyen módon helyezheti üzembe a gyakorlatban a Azure Machine Learning használatával.

Az AI-rendszerek fejlesztése és használata során a megbízhatóságnak a legfontosabbnak kell lennie. Megbízhatóság a platformon, a folyamaton és a modelleken. A Microsoftnál a felelős ML a következő értékeket és alapelveket foglalja magában:

- A gépi tanulási modellek ismertetése
  - A modell viselkedésének értelmezése és magyarázata
  - A modell kiértékelése és enyhítése
- A személyek és az adataik védelme
  - A különbözeti adatokkal való kitettség megakadályozása  
- A végpontok közötti gépi tanulási folyamat szabályozása
  - A gépi tanulási életciklus dokumentálása az adatlapokkal

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="Felelős ML-oszlopok":::

Mivel a mesterséges intelligencia és az autonóm rendszerek egyre inkább integrálják a társadalmat a társadalomba, fontos, hogy proaktív módon végezze el az ilyen technológiák nem szándékolt következményeinek megelőzését és enyhítését.

## <a name="interpret-and-explain-model-behavior"></a>A modell viselkedésének értelmezése és magyarázata

A nehezen érthető vagy átlátszatlan rendszerek problémái lehetnek, mert az érintett felek, például a rendszerfejlesztők, a szabályozók, a felhasználók és az üzleti döntéshozók megtudhatják, hogy a rendszer miért tesz bizonyos döntéseket. Néhány AI-rendszer jobban megmagyarázható, mint a többinél, és néha egy kompromisszumos rendszer, amely nagyobb pontossággal rendelkezik, és az egyik még magyarázható.

A értelmezhető AI-rendszerek létrehozásához használja a [InterpretML](https://github.com/interpretml/interpret)-t, amely a Microsoft által készített nyílt forráskódú csomag. A InterpretML a gépi tanulási modellek, például az [automatizált gépi tanulási modellek](how-to-machine-learning-interpretability-automl.md) [értelmezése és magyarázata](how-to-machine-learning-interpretability-aml.md) [Azure Machine learning belül is használható](how-to-machine-learning-interpretability.md) .

## <a name="assess-and-mitigate-model-unfairness"></a>A modell kiértékelése és enyhítése

Mivel a mesterséges intelligencia-rendszerek egyre nagyobb szerepet játszanak a társadalom mindennapos döntéshozatalában, rendkívül fontos, hogy ezek a rendszerek jól működjenek a tisztességes eredmények mindenki számára való biztosítása terén.

A mesterséges intelligencia-rendszerekben a következő nem kívánt következményekkel járhat:

- Lehetőségek, erőforrások és információk visszatartása magánszemélyek számára.
- A torzítások és a sztereotípiák megerősítése.

A méltányosság számos aspektusa nem rögzíthető és nem jeleníthető meg mérőszámok alapján. Vannak olyan eszközök és gyakorlatok, amelyek az AI-rendszerek kialakításában és fejlesztésében is javítják a méltányosság hatékonyságát.

Az AI-rendszerekben a tisztességtelenség csökkentésének két fő lépése az értékelés és a mérséklés. Javasoljuk, hogy [FairLearn](https://github.com/fairlearn/fairlearn)egy nyílt forráskódú csomagot, amely képes felmérni és elhárítani a mesterséges intelligencia-rendszerek potenciális méltánytalan képességét. Ha többet szeretne megtudni a méltányosság és a FairLearn csomagról, tekintse meg a [méltányosság ml-ben című cikket](./concept-fairness-ml.md).

## <a name="prevent-data-exposure-with-differential-privacy"></a>A különbözeti adatokkal való kitettség megakadályozása

Az adatok elemzésre való használatakor fontos, hogy az adatok a használat során magánjellegűek és bizalmasak maradnak. A differenciált adatvédelem olyan rendszerek és eljárások összessége, amelyek segítenek megőrizni a magánszemélyek adatait.

A hagyományos helyzetekben a nyers adatok tárolása a fájlokban és az adatbázisokban történik. Amikor a felhasználók elemeznek az adatelemzést, általában a nyers adattípust használják. Ez aggodalomra ad okot, mert sértheti az egyén adatvédelmét. A differenciált adatvédelem a "zaj" vagy az adatok véletlenszerű törlésével próbálkozik a probléma megoldásával, így a felhasználók nem azonosíthatják az egyes adatpontokat.

A differentially privát rendszereinek megvalósítása nehéz feladat. A [WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-core) egy nyílt forráskódú projekt, amely különböző összetevőket tartalmaz a globális differentially-alapú privát rendszerek létrehozásához. Ha többet szeretne megtudni a differenciált adatvédelemről és a WhiteNoise projektről, tekintse meg az adatvédelem [megőrzése a különbözeti adatvédelem és a WhiteNoise című cikkben leírtakat](./concept-differential-privacy.md) .

> [!NOTE]
> Vegye figyelembe, hogy átnevezjük az eszközkészletet, és az új nevet az elkövetkező hetekben fogjuk bevezetni. 

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>A gépi tanulási életciklus dokumentálása az adatlapokkal

A megfelelő információk dokumentálása a gépi tanulási folyamat során kulcsfontosságú a felelős döntések meghozatalához az egyes fázisokban. Az adatlapokon dokumentálhatja a gépi tanulási életciklus részeként használt és létrehozott gépi tanulási eszközöket.

A modellek általában úgy gondolják, hogy "átlátszatlan dobozok", és gyakran kevés információ áll rendelkezésre róluk. Mivel a gépi tanulási rendszerek egyre átfogóbbak, és a döntéshozatalhoz használják őket, az adatlapok használata egy lépés a felelősségteljesebb gépi tanulási rendszerek fejlesztése felé.

Néhány modell-információ, amelyet egy adatlap részeként szeretne dokumentálni:

- Rendeltetésszerű használat
- Modell architektúrája
- Felhasznált betanítási adatgyűjtés
- Felhasznált kiértékelési adatszolgáltatások
- Képzési modell teljesítmény-mérőszámai
- A méltányosság adatai.

Tekintse meg az alábbi mintát, amelyből megtudhatja, hogyan hozhatja létre a [modellek adatlapjait](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb)a Azure Machine learning SDK használatával.

## <a name="additional-resources"></a>További források

- A homomorphic titkosítás használatával [helyezzen üzembe egy titkosított következtetési webszolgáltatás-szolgáltatást](how-to-homomorphic-encryption-seal.md).
- További információ a Machine learning-rendszer dokumentációjának a [ml](https://www.partnershiponai.org/about-ml/) -készletéről.
