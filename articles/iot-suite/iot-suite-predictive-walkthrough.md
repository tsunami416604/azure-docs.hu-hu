<properties
 pageTitle="Prediktív karbantartás bemutatója | Microsoft Azure"
 description="Az Azure IoT prediktív karbantartási előre konfigurált megoldás bemutatója."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="aguilaaj"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/17/2016"
 ms.author="araguila"/>

# A prediktív karbantartási előre konfigurált megoldás bemutatója

## Bevezetés

Az IoT Suite prediktív karbantartási előre konfigurált megoldás olyan teljes körű megoldást nyújt az üzleti forgatókönyvekben, amely előrejelzi a meghibásodás várható idejét. Ezt az előre konfigurált megoldást proaktív módon használhatja olyan tevékenységekhez, mint a karbantartás optimalizálása. A megoldás kombinálja a fő Azure IoT Suite-szolgáltatásokat, beleértve egy [Azure Machine Learning][lnk_machine_learning] munkaterületet. Ezen a munkaterületen kísérletek találhatók a repülőmotorok maradék hasznos élettartamának (RUL-jének) előrejelzéséhez egy nyilvános minta adatkészlet alapján. A megoldás az IoT üzleti forgatókönyv teljes megvalósítását biztosítja kiindulópontként, amellyel megtervezheti és megvalósíthatja ezt a megoldást a saját üzleti követelményeinek megfelelően.

## Logikai architektúra

A következő diagram az előre konfigurált megoldás logikai összetevőit vázolja fel:

![][img-architecture]

A kék elemek Azure-szolgáltatások, amelyek az előre konfigurált megoldás kiépítésekor kiválasztott helyen vannak kiépítve. Az USA keleti régiójában, Észak-Európában vagy Kelet-Ázsiában építheti ki az előre konfigurált megoldást.

Egyes erőforrások nem érhetők azokban a régiókban, ahol kiépíti az előre konfigurált megoldást. A diagram narancssárga elemei a legközelebbi elérhető régióban (az USA déli középső régiójában, Nyugat-Európában vagy Délkelet-Ázsiában) kiépített Azure-szolgáltatásokat jelzik a kiválasztott régió alapján.

A zöld elem egy repülőmotort jelképező szimulált eszköz. Az alábbi szakaszban tudhat meg többet ezekről a szimulált eszközökről.

A szürke elemek az *eszközfelügyeleti* képességeket megvalósító összetevőket jelölik. A prediktív karbantartási előre konfigurált megoldás jelenlegi kiadása nem biztosítja ezeket az erőforrásokat. Az eszközfelügyeletről a [távoli figyelési előre konfigurált megoldás][lnk-remote-monitoring] szakaszban talál további információt.

## Szimulált eszközök

Az előre konfigurált megoldásban a szimulált eszközök repülőmotorokat jelképeznek. A megoldás egyetlen repülőhöz tartozó két motorral van kiépítve. Mindegyik motor négy típusú telemetriát bocsát ki: a 9. érzékelő, a 11. érzékelő, a 14. érzékelő és a 15. érzékelő telemetriáját, amelyek a motor fennmaradó hasznos élettartama (RUL) kiszámítására szolgáló Machine Learning-modellhez szükséges adatokat nyújtják. Mindegyik szimulált eszköz a következő telemetriai üzeneteket küldi el az IoT Hubnak:

*Ciklusszám*. A ciklusok különböző hosszúságú (2 és 10 óra közötti) befejezett repülőutakat jeleznek, amelyek során a telemetriai adatokat a rendszer félóránként rögzíti.

*Telemetria*. Négy érzékelő jelzi a motorattribútumokat. Az érzékelők általában a 9. érzékelő, 11. érzékelő, 14. érzékelő és 15. érzékelő felirattal rendelkeznek. Ez a 4 érzékelő elegendő telemetriát jelez ahhoz, hogy hasznos eredmények érkezzenek a Machine Learning-modellről az RUL számításához. Ez a modell nyilvános adatkészletből jön létre, amely valódi motorérzékelők adatait tartalmazza. További információ a modell eredeti adatkészletből való létrehozásáról: [Cortana Intelligence Gallery Predictive Maintenance Template][lnk-cortana-analytics] (A Cortana Intelligence Gallery prediktív karbantartási sablonja).

A szimulált eszközök az IoT Hubról küldött következő parancsokat tudják kezelni:

| Parancs | Leírás |
|---------|-------------|
| StartTelemetry | A szimuláció állapotát vezérli.<br/>Elindítja a telemetriát küldő eszközt     |
| StopTelemetry  | A szimuláció állapotát vezérli.<br/>Leállítja a telemetriát küldő eszközt |

Az IoT Hub nyugtázza az eszközparancsokat.

## Azure Stream Analytics-feladat

**Feladat: Telemetria** – a bejövő eszköz telemetriastreamjén működik két utasítással. Az első kiválasztja az összes telemetriát az eszközökről, és ezeket az adatokat Blob Storage-ba küldi, ahol a webalkalmazásban jelennek meg. A második utasítás kiszámítja az átlagos érzékelőértékeket egy kétperces csúszóablakban és ezeket az adatokat az eseményközponton keresztül egy **eseményfeldolgozóba** küldi.

## Eseményfeldolgozó

Az **eseményfeldolgozó** a befejezett ciklusok átlagos érzékelőértékeit kezeli. Ezeket az értékeket olyan API-ra küldi, amely közzéteszi a Machine Learning betanított modellt egy motor RUL értékének kiszámításához.

## Azure Machine Learning

További információ a modell eredeti adatkészletből való létrehozásáról: [Cortana Intelligence Gallery Predictive Maintenance Template][lnk-cortana-analytics] (A Cortana Intelligence Gallery prediktív karbantartási sablonja).

## Kezdjük a bemutatót

Ez a szakasz végigvezeti a megoldás összetevőin, leírja a kívánt használati esetet, és példákat is tartalmaz.

### Prediktív karbantartási irányítópult

A webalkalmazás ezen oldala PowerBI JavaScript-vezérlőket használ (lásd a [PowerBI-vizualizációk tárát][lnk-powerbi]) a következők megjelenítéséhez:

- A Blob Storage-ban lévő Stream Analytics-feladatok kimeneti adatai.
- Az RUL és a ciklusszám repülőmotoronként.

### A felhőmegoldás viselkedésének megfigyelése

A kiépített erőforrások megtekintéséhez az Azure Portalon keresse meg a kiválasztott megoldásnévvel rendelkező erőforráscsoportot.

![][img-resource-group]

Az előre konfigurált megoldás kiépítésekor egy e-mailt kap, amely tartalmazza a Machine Learning-munkaterületre mutató hivatkozást. Az [azureiotsuite.com][lnk-azureiotsuite] oldalon is elérheti ezt a Machine Learning-munkaterületet a kiépített megoldáshoz, amikor az **Kész** állapotban van.

![][img-machine-learning]

A megoldás portálján láthatja, hogy a minta négy szimulált eszközzel van kiépítve, amelyek két repülőt jeleznek, repülőnként két motorral és motoronként négy érzékelővel. Amikor először keresi fel a megoldás portálját, a szimuláció leáll.

![][img-simulation-stopped]

Kattintson a **Start simulation** (Szimuláció indítása) elemre a szimuláció elindításához, amelyben az érzékelő előzményei, az RUL, a ciklusok és az RUL előzményei töltik ki az irányítópultot.

![][img-simulation-running]

Ha az RUL 160-nál kisebb (amely egy bemutatási célra véletlenszerűen kiválasztott küszöbérték), a megoldás portálja egy figyelmeztető szimbólumot jelenít meg az RUL kijelzése mellett, és a repülő motorját sárga színnel emeli ki. Látható, hogy az RUL értékek összesítve jellemzően csökkennek, de felfelé és lefelé is változnak. Ez a viselkedés a változó ciklushosszokból és modellpontosságból következik.

![][img-simulation-warning]

A teljes szimuláció 148 ciklusának elvégzése körülbelül 35 percet vesz igénybe. Először körülbelül 5 perc után éri el a 160 RUL-es küszöbértéket, és körülbelül 8 perc után éri el mindkét motor a küszöbértéket.

A szimuláció 148 cikluson halad végig a teljes adatkészleten, és megállapítja a RUL és a ciklus végső értékeit.

Bármikor leállíthatja a szimulációt, de a **Start Simulation** (Szimuláció indítása) gombra kattintva az adatkészlet elejétől indul újra a szimuláció.

## Következő lépések

A prediktív karbantartási előre konfigurált megoldás futtatása után módosíthatja is azt, ezzel kapcsolatban lásd az [útmutatást az előre konfigurált megoldások testreszabásához][lnk-customize].

Az [IoT Suite - Under The Hood - Predictive Maintenance](http://social.technet.microsoft.com/wiki/contents/articles/33527.iot-suite-under-the-hood-predictive-maintenance.aspx) (IoT Suite– Technikai részletek – Prediktív karbantartás) TechNet-blogbejegzés további részleteket tartalmaz a prediktív karbantartási előre konfigurált megoldásról.

Megismerheti az IoT Suite előre konfigurált megoldásának egyéb szolgáltatásait és funkcióit is:

- [Gyakran ismételt kérdések az IoT Suite-ról][lnk-faq]
- [IoT-biztonság létrehozása az alapoktól][lnk-security-groundup]


[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md



<!--HONumber=sep16_HO1-->


