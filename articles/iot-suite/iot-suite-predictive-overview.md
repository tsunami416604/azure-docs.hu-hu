<properties
 pageTitle="Prediktív karbantartási előre konfigurált megoldás | Microsoft Azure"
 description="Az Azure IoT prediktív karbantartási előre konfigurált megoldás ismertetése."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="05/24/2016"
 ms.author="stevehob"/>

# A prediktív karbantartási előre konfigurált megoldás áttekintése

A *prediktív karbantartás* előre konfigurált megoldás a [Microsoft Azure IoT Suite][lnk_iot_suite] részeként kiadott [előre konfigurált megoldások][lnk_preconfigured_solutions] egyike. Ez a megoldás a valós idejű eszköztelemetria-gyűjtést az [Azure Machine Learning][lnk_machine_learning] használatával létrehozott prediktív modellel integrálja.


Az Azure IoT Suite segítségével a vállalatok gyorsan és könnyedén figyelhetik az objektumokat és csatlakozhatnak hozzájuk, valamint valós időben elemezhetik az adatokat. A prediktív karbantartási előre konfigurált megoldás részletes irányítópultokkal és látványtervekkel használja fel ezeket az adatokat, így a hatékonyságot és a bevételeket növelő új elemzéseket biztosít a vállalkozásoknak.

## A forgatókönyv

A Fabrikam egy regionális légitársaság, amely a nagyszerű ügyfélélményre összpontosít versenyképes árakon. A járatok késésének egyik okai a karbantartási problémák, és a repülőmotorok karbantartása különösen nagy kihívást jelent. A repülés közbeni motorhibákat minden áron el kell kerülni, így a Fabrikam rendszeresen megvizsgálja a motorokat, és ütemezett karbantartási programot követ. A repülők motorja azonban nem mindig ugyanolyan ütemben használódik el. Időnként feleslegesen végeznek karbantartást a motorokon. Még fontosabb, hogy olyan problémák merülnek fel, amelyek miatt a repülő nem szállhat fel a karbantartásig. Ez költséges késéseket okoz, különösen, ha egy repülő olyan helyen van, ahol nincsenek megfelelő szerelők vagy pótalkatrészek.

A Fabrikam repülőmotorjai olyan érzékelőkkel vannak felszerelve, amelyek megfigyelik a motor állapotát repülés közben. A Fabrikam az Azure IoT Suite használatával gyűjti be a repülés alatt begyűjtött érzékelőadatokat. A motor működési és meghibásodási adatainak többéves begyűjtése után a Fabrikam adatszakértői olyan módszert alkottak meg, amely előrejelzi a repülőmotor fennmaradó hasznos élettartamát (RUL). Összefüggést találtak a négy motorérzékelőből érkező adatok és a meghibásodáshoz vezető motorkopás között. A Fabrikam továbbra is rendszeres vizsgálatokat végez a biztonság érdekében, azonban már modellek használatával kiszámíthatja mindegyik motor RUL értékét minden egyes út után a repülés alatt a motorokból gyűjtött telemetriával. A Fabrikam így előre jelezheti a jövőbeli meghibásodási pontokat, és megtervezheti a karbantartást és a javítást.

> [AZURE.NOTE] A megoldásmodell tényleges motorkopási adatokat használ.

A karbantartás várható idejének előrejelzésével a Fabrikam optimalizálhatja a műveleteket a költségek csökkentése érdekében. A karbantartási koordinátorok a menetrendek készítőivel együtt tervezik meg a karbantartást, hogy egybeessen a repülő adott helyen való megállásával, és elegendő idő maradjon a repülő karbantartására a menetrend megzavarása nélkül. A Fabrikam ennek megfelelően oszthatja be a technikusokat, amivel biztosítható a repülőgépek hatékony, várakozás nélküli szervizelés. A készletgazdálkodási vezetők karbantartási terveket kapnak, hogy optimalizálhassák a rendelési folyamatokat és a pótalkatrészek készletét. A Fabrikam mindezekkel minimalizálhatja a repülő földön töltött idejét és csökkentheti a működési költségeket, miközben az utasok és a személyzet biztonságát is garantálja.

Annak megértéséhez, hogy az [Azure IoT Suite][lnk_iot_suite] hogyan nyújt az ügyfeleknek olyan képességeket, amelyekre a prediktív karbantartás lehetőségeinek kiaknázásához szükségük van, tekintse át ezt az [információs grafikát][lnk_infographic].

## A prediktív karbantartási megoldás felépítése

A megoldás egy sablonként elérhető meglévő Azure Machine Learning-modellel mutatja be ezeket a képességeket, amely az IoT Suite szolgáltatásokon keresztül gyűjtött eszköztelemetriából dolgozik. A Microsoft felépítette egy repülőmotor [regressziós modelljét][lnk_regression_model], és közzétette a modell használatának teljes sablonját, adatait<sup>\[1\]</sup> és lépésenkénti útmutatóját.

Az Azure IoT prediktív karbantartási előre konfigurált megoldás az ebből a sablonból létrehozott regressziós modellt használja. A rendszer az Azure-előfizetésben helyezi üzembe, és egy automatikusan létrehozott API-n keresztül teszi közzé. A megoldásban a tesztadatok alkészlete szerepel, amely (összesen 100-ból) 4 motort és (összesen 21-ből) 4 érzékelőstreamet ábrázol, és a Trained Model pontos eredményét biztosítja.

*\[1\] A. Saxena és K. Goebel (2008). „Turbofan Engine Degradation Simulation Data Set”, NASA Ames Prognostics Data Repository (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), NASA Ames Research Center, Moffett Field, CA*

## Következő lépések

További információ arról, hogyan teszi lehetővé az Azure IoT a prediktív karbantartási forgatókönyveket: [Capture value from the Internet of Things][lnk_capture_value] (Érték rögzítése az eszközök internetes hálózatáról).

Tekintse át a prediktív karbantartás előre konfigurált megoldás [bemutatóját][lnk-predictive-walkthrough].

[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_machine_learning]: https://azure.microsoft.com/en-us/services/machine-learning/
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF



<!--HONumber=jun16_HO2-->


