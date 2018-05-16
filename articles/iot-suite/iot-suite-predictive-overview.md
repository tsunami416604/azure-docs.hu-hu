---
title: A prediktív karbantartási megoldásgyorsító áttekintése – Azure | Microsoft Docs
description: Az Azure prediktív karbantartási megoldásgyorsító ismertetése.
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: b370b3d7-2ce5-4906-9818-3aeedd471ee3
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 1a2750232f9396d8bc27e4fbb72eacf8790a1c52
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>A prediktív karbantartási megoldásgyorsító áttekintése

A *prediktív karbantartási* [megoldásgyorsító][lnk_preconfigured_solutions] a [Microsoft Azure IoT-megoldásgyorsítók][lnk_iot_suite] egyike. Ez a megoldás a valós idejű eszköztelemetria-gyűjtést az [Azure Machine Learning][lnk-machine-learning] használatával létrehozott prediktív modellel integrálja.

Az Azure IoT-megoldásgyorsítók segítségével gyorsan és könnyedén monitorozhatja az objektumokat és csatlakozhat hozzájuk, valamint valós időben elemezheti a telemetriát irányítópultok és látványtervek segítségével. A prediktív karbantartási megoldásgyorsító irányítópultjai és látványtervei hatékonyságot és a bevételeket növelő új elemzéseket biztosítanak.

## <a name="the-scenario"></a>A forgatókönyv

A Fabrikam egy regionális légitársaság, amely a nagyszerű ügyfélélményre összpontosít versenyképes árakon. A járatok késésének egyik okai a karbantartási problémák, és a repülőmotorok karbantartása különösen nagy kihívást jelent. A Fabrikamnak minden áron el kell kerülnie a repülés közbeni motorhibákat, így rendszeresen megvizsgálja a motorokat, és tervszerűen ütemezi a karbantartást. A repülők motorja azonban nem mindig ugyanolyan ütemben használódik el. Időnként feleslegesen végeznek karbantartást a motorokon. Még fontosabb, hogy olyan problémák merülnek fel, amelyek miatt a repülő nem szállhat fel a karbantartásig. Ezek a problémák különösen költségesek lehetnek, ha egy repülő olyan helyen van, ahol nincsenek megfelelő szerelők vagy pótalkatrészek.

A Fabrikam repülőmotorjai olyan érzékelőkkel vannak felszerelve, amelyek megfigyelik a motor állapotát repülés közben. A Fabrikam a prediktív karbantartási megoldásgyorsító használatával gyűjti be a repülés alatt begyűjtött érzékelőadatokat. A motor működési és meghibásodási adatainak többéves begyűjtése után a Fabrikam adatszakértői olyan módszert alkottak meg, amely előrejelzi a repülőmotor fennmaradó hasznos élettartamát (RUL). A modell a négy motorérzékelőből érkező adatok és a meghibásodáshoz vezető motorkopás közötti összefüggést alkalmazza. A Fabrikam továbbra is rendszeres vizsgálatokat végez a biztonság érdekében, azonban mostantól a modellek használatával kiszámíthatja mindegyik motor RUL értékét minden egyes repülőút után. A modell a repülőút alatt a motorokból gyűjtött telemetriát használja. A Fabrikam így előre jelezheti a jövőbeli meghibásodási pontokat, és megtervezheti a karbantartást és a javítást.

> [!NOTE]
> A megoldásmodell tényleges motorkopási adatokat használ.

A karbantartás várható idejének előrejelzésével a Fabrikam optimalizálhatja a műveleteket a költségek csökkentése érdekében.

A karbantartási koordinátorok és a menetrendek készítői együttműködve elvégzik a következőket:

- Megtervezik a karbantartást, hogy az egybeessen a repülő adott helyen való megállásával.
- Biztosítják, hogy elegendő idő maradjon a repülő karbantartására a menetrend megzavarása nélkül.
- Beosztják a technikusokat, hogy biztosítsák a repülőgépek hatékony, várakozás nélküli szervizelését.

A készletgazdálkodási vezetők karbantartási terveket kapnak, hogy optimalizálhassák a rendelési folyamatokat és a pótalkatrészek készletét.

A Fabrikam mindezeknek köszönhetően képes minimálisra csökkenteni a repülő földön töltött idejét és csökkenteni a működési költségeket, miközben az utasok és a személyzet biztonságát is garantálja.

Annak megértéséhez, hogy az [Azure IoT megoldásgyorsítók][lnk_iot_suite] hogyan nyújtanak az ügyfeleknek olyan képességeket, amelyekre a prediktív karbantartás lehetőségeinek kiaknázásához szükségük van, tekintse át ezt az [információs grafikát][lnk_infographic].

## <a name="how-the-predictive-maintenance-solution-accelerator-is-built"></a>A prediktív karbantartási megoldásgyorsító felépítése

A megoldás egy sablonként elérhető meglévő Azure Machine Learning-modellel mutatja be ezeket a képességeket, amely az IoT-megoldásgyorsítók szolgáltatásain keresztül gyűjtött eszköztelemetriából dolgozik. A Microsoft nyilvánosan elérhető adatok alapján<sup>\[1\]</sup> felépítette egy repülőmotor [regressziós modelljét][lnk_regression_model] és a modell használatának lépésenkénti útmutatóját.

Az Azure IoT prediktív karbantartási megoldásgyorsító az ebből a sablonból létrehozott regressziós modellt használja. A rendszer az Azure-előfizetésben helyezi üzembe a modellt, és egy automatikusan létrehozott API-n keresztül teszi közzé. A megoldásban a tesztadatok egy része szerepel, amely (összesen 100-ból) 4 motort és (összesen 21-ből) 4 érzékelőadat-streamet ábrázol. Ezek az adatok elegendők ahhoz, hogy pontos eredményt biztosítsanak a betanított modellből.

*\[1\] A. Saxena és K. Goebel (2008). „Turbofan Engine Degradation Simulation Data Set”, NASA Ames Prognostics Data Repository (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), NASA Ames Research Center, Moffett Field, CA*

## <a name="get-started-with-predictive-maintenance"></a>Ismerkedés a prediktív karbantartással

Ez az oktatóanyag bemutatja, hogyan építheti ki a prediktív karbantartási megoldásgyorsítót. Emellett a prediktív karbantartási megoldásgyorsító alapszintű funkcióin is végigvezeti. Ezek közül számos szolgáltatáshoz a megoldás irányítópultján keresztül férhet hozzá, amelyet a rendszer a megoldásgyorsítóval együtt telepít.

Az oktatóanyag elvégzéséhez aktív Azure-előfizetésre lesz szüksége.

> [!NOTE]
> Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információ: [Ingyenes Azure-fiók létrehozása][lnk_free_trial].

1. Jelentkezzen be az [azureiotsuite.com][lnk-azureiotsuite] címre az Azure-fiókja hitelesítő adataival, majd kattintson a **+** elemre egy megoldás létrehozásához.
1. Kattintson a **Kiválasztás** elemre, majd a **Prediktív karbantartás** csempére.
1. Adjon meg **Megoldásnevet** a prediktív karbantartási megoldásgyorsítóhoz.
1. Válassza ki a megoldás kiépítéséhez használni kívánt **Régiót** és **Előfizetést**.
1. Kattintson a **Megoldás létrehozása** gombra a kiépítés elkezdéséhez. Ez a folyamat általában több percig tart.

### <a name="wait-for-the-provisioning-process-to-complete"></a>Várjon, amíg a kiépítési folyamat befejeződik

1. Kattintson a megoldás **Kiépítési** állapotát jelző csempére.
1. Megtekintheti a **Kiépítési állapotokat**, miközben az Azure-szolgáltatások telepítése megtörténik az Azure-előfizetésben.
1. A kiépítés befejezése után az állapot **Kész** értékre változik.
1. Kattintson a csempére, és a jobb oldali panelen láthatja a megoldás részleteit. Erről a panelről indíthatja el a megoldás irányítópultját, és innen férhet hozzá a Machine Learning-munkaterülethez.

> [!NOTE]
> Ha problémái vannak a megoldásgyorsító telepítésekor, tekintse meg az [Engedélyek az azureiotsuite.com webhelyen][lnk-permissions] és a [GYIK][lnk-faq] fejezetet. Ha a problémák továbbra is fennállnak, hozzon létre egy szolgáltatásjegyet a [portálon][lnk-portal].

Hiányol bizonyos részleteket a megoldásával kapcsolatban? A [felhasználói visszajelzési webhelyen](https://feedback.azure.com/forums/321918-azure-iot) elküldheti a szolgáltatásokkal kapcsolatos javaslatait.

## <a name="view-the-solution"></a>A megoldás megtekintése

Ez a szakasz végigvezeti a megoldás felhasználói felületén.

### <a name="predictive-maintenance-dashboard"></a>Prediktív karbantartási irányítópult

A webalkalmazás ezen oldala PowerBI JavaScript-vezérlőket használ (lásd a [PowerBI-vizualizációk tárát][lnk-powerbi]) a következők megjelenítéséhez:

* A Blob Storage-ban lévő Stream Analytics-feladatok kimeneti adatai.
* Az RUL és a ciklusszám repülőmotoronként.

### <a name="observing-the-behavior-of-the-cloud-solution"></a>A felhőmegoldás viselkedésének megfigyelése

A kiépített erőforrások megtekintéséhez az Azure Portalon keresse meg a kiválasztott megoldásnévvel rendelkező erőforráscsoportot.

![][img-resource-group]

A megoldásgyorsító kiépítésekor egy e-mailt kap, amely tartalmazza a Machine Learning-munkaterületre mutató hivatkozást. Az [azureiotsuite.com][lnk-azureiotsuite] oldalon is elérheti ezt a Machine Learning-munkaterületet a kiépített megoldáshoz. Amikor a megoldás **Kész** állapotban van, elérhetővé válik egy csempe ezen az oldalon.

![][img-machine-learning]

A megoldás portálján láthatja, hogy a minta négy szimulált eszközzel van kiépítve, amelyek két repülőt jeleznek, repülőnként két motorral és motoronként négy érzékelővel. Amikor először keresi fel a megoldás portálját, a szimuláció leáll.

![][img-simulation-stopped]

Kattintson a **Start simulation** (Szimuláció indítása) elemre a szimuláció elindításához. Az irányítópultot az érzékelő előzményei, az RUL, a ciklusok és az RUL előzményei töltik ki.

![][img-simulation-running]

Ha az RUL 160-nál kisebb (amely egy bemutatási célra véletlenszerűen kiválasztott küszöbérték), a megoldás portálja egy figyelmeztető szimbólumot jelenít meg az RUL kijelzése mellett. A megoldásportál emellett a repülő motorját sárga színnel emeli ki. Látható, hogy az RUL értékek összesítve jellemzően csökkennek, de felfelé és lefelé is változnak. Ez a viselkedés a változó ciklushosszokból és modellpontosságból következik.

![][img-simulation-warning]

A teljes szimuláció 148 ciklusának elvégzése körülbelül 35 percet vesz igénybe. Először körülbelül 5 perc után éri el a 160 RUL-es küszöbértéket, és körülbelül 8 perc után éri el mindkét motor a küszöbértéket.

A szimuláció 148 cikluson halad végig a teljes adatkészleten, és megállapítja a RUL és a ciklus végső értékeit.

Bármikor leállíthatja a szimulációt, de a **Start Simulation** (Szimuláció indítása) gombra kattintva az adatkészlet elejétől indul újra a szimuláció.

## <a name="next-steps"></a>További lépések

További információ arról, hogyan teszi lehetővé az Azure IoT a prediktív karbantartási forgatókönyveket: [Capture value from the Internet of Things][lnk_capture_value] (Érték rögzítése az eszközök internetes hálózatáról).

Tekintse át a prediktív karbantartási megoldásgyorsító [bemutatóját][lnk-predictive-walkthrough].

Megismerheti az IoT-megoldásgyorsítók egyéb szolgáltatásait és funkcióit is:

* [Gyakran ismételt kérdések az IoT-megoldásgyorsítókról][lnk-faq]
* [IoT-biztonság létrehozása az alapoktól][lnk-security-groundup]

[img-resource-group]: media/iot-suite-predictive-overview/resource-group.png
[img-simulation-stopped]: media/iot-suite-predictive-overview/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-overview/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-overview/simulation-warning.png
[img-machine-learning]: media/iot-suite-predictive-overview/machine-learning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-solution-accelerators.md
[lnk_iot_suite]: iot-suite-options.md
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3

[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF
[lnk-faq]: iot-suite-v1-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-portal]: http://portal.azure.com/
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/