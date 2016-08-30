<properties
    pageTitle="Az előre konfigurált megoldások bemutatása | Microsoft Azure"
    description="Ebből az oktatóanyagból megtudhatja, hogyan telepíthet egy előre konfigurált Azure IoT Suite-megoldást."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="05/25/2016"
     ms.author="dobett"/>

# Oktatóprogram: Az előre konfigurált megoldások bemutatása

## Bevezetés

Az Azure IoT Suite [előre konfigurált megoldások][lnk-preconfigured-solutions] több Azure IoT-szolgáltatást kombinálnak, hogy általános IoT üzleti forgatókönyveket megvalósító végpontok közötti megoldásokat nyújtsanak.

Ez az oktatóprogram bemutatja, hogyan építheti ki az előre konfigurált *távoli figyelő* megoldást. A program az előre konfigurált távoli figyelő megoldás alapvető funkcióin is végigvezeti.

Az oktatóprogram elvégzéséhez aktív Azure-előfizetésre van szüksége.

> [AZURE.NOTE]  Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása][lnk_free_trial].

## Az előre konfigurált távoli figyelő megoldás kiépítése

1.  Jelentkezzen be az [azureiotsuite.com][lnk-azureiotsuite] címre az Azure-fiókja hitelesítő adataival, majd kattintson a **+** elemre egy új megoldás létrehozásához.

    > [AZURE.NOTE] Ha problémája van a megoldás kiépítéséhez szükséges engedélyekkel, útmutatásért lásd: [Engedélyek az azureiotsuite.com webhelyen][lnk-permissions].

2.  Kattintson a **Kiválasztás** elemre a **Távoli figyelés** csempén.

3.  Adja meg a **Megoldásnevet** az előre konfigurált távoli figyelési megoldáshoz.

4.  Válassza ki a megoldás kiépítéséhez használni kívánt **Régiót** és **Előfizetést**.

5.  Kattintson a **Megoldás létrehozása** gombra a kiépítés elkezdéséhez. Ennek futtatása általában több percig tart.

## Várjon, amíg a kiépítési folyamat befejeződik

1. Kattintson a megoldás **Kiépítési** állapotát jelző csempére.
 
2. Megtekintheti a **Kiépítési állapotokat**, miközben az Azure-szolgáltatások telepítése megtörténik az Azure-előfizetésben.

3. A kiépítés befejezése után az állapot **Kész** értékre változik.

4. Kattintson a csempére, és a jobb oldali panelen láthatja a megoldás részleteit.

> [AZURE.NOTE] Ha problémái vannak az előre konfigurált megoldás telepítésekor, tekintse meg az [Engedélyek az azureiotsuite.com webhelyen][lnk-permissions] és a [GYIK][lnk-faq] fejezetet. Ha a problémák továbbra is fennállnak, hozzon létre egy szolgáltatásjegyet a [portálon][lnk-portal].

Hiányol bizonyos részleteket a megoldásával kapcsolatban? A [felhasználói visszajelzési webhelyen](https://feedback.azure.com/forums/321918-azure-iot) elküldheti a szolgáltatásokkal kapcsolatos javaslatait.

## Távoli megfigyelő megoldás irányítópultjának megtekintése

A megoldás irányítópultján kezelheti az üzembe helyezett megoldást. Megtekintheti például a telemetriát, eszközöket adhat hozzá és szabályokat konfigurálhat.

1.  Amikor a kiépítés befejeződött, és az előre konfigurált megoldás csempéje **Kész** állapotot jelez, kattintson az **Indítás** gombra a távoli figyelési megoldás új lapon való megnyitásához.

    ![][img-launch-solution]

2.  Alapértelmezés szerint a megoldás portálja a *megoldás irányítópultját* jeleníti meg. A bal oldali menüből választhat más nézeteket.

    ![][img-dashboard]

Az irányítópult az alábbi információkat jeleníti meg:

- A térkép a megoldáshoz csatlakoztatott egyes eszközök helyét jeleníti meg. A megoldás első futtatásakor négy szimulált eszköz van. A szimulált eszközök Azure WebJobs feladatokként vannak megvalósítva, és a megoldás a Bing Térképek API-val jeleníti meg az információkat a térképen.
- A **Telemetria előzményei** panel a páratartalommal és hőmérséklettel kapcsolatos telemetriát jelenít meg a kiválasztott eszközről közel valós időben, és összesített adatokat tartalmaz, például a maximális, minimális és átlagos páratartalmat.
- A **Riasztások előzményei** panel közelmúltbeli riasztási eseményeket jelenít meg, amikor egy telemetriaérték túllépett egy küszöbértéken. Saját riasztásokat is meghatározhat az előre konfigurált megoldás által létrehozott példák mellett.

## A megoldás eszközlistájának megtekintése

Az eszközlista a megoldásban regisztrált összes eszközt megjeleníti. Megtekintheti és szerkesztheti az eszközök metaadatait, eszközöket adhat hozzá vagy távolíthat el, illetve parancsokat küldhet az eszközöknek.

1.  Kattintson a bal oldali menü **Eszközök** elemére a megoldás *eszközlistájának* megjelenítéséhez.

    ![][img-devicelist]

2.  Az eszközlistán látható, hogy a kiépítési folyamat négy szimulált eszközt hozott létre.

3.  Kattintson egy eszközre az eszközlistában az eszköz részleteinek megtekintéséhez.

    ![][img-devicedetails]

Az **Eszköz részletei** panel három szakaszból áll:

- A **Műveletek** szakasz az eszközön elvégezhető műveleteket sorolja fel. Ha letiltja az eszközt, az többé nem küldhet telemetriát és nem fogadhat parancsokat. A letiltott eszközöket újra engedélyezheti. Az eszközhöz társított szabályt adhat hozzá, amely kivált egy riasztást, ha egy telemetriaérték átlép egy küszöbértéket. Parancsot is küldhet az eszközökre. Amikor egy eszköz először csatlakozik, közli azokat a parancsokat a megoldással, amelyekre válaszolhat.
- Az **Eszköztulajdonságok** szakasz az eszköz metaadatait listázza. Egyes metaadatok magáról az eszközről származnak (például a gyártó), másokat pedig megoldás hoz létre (például a létrehozás idejét). Itt szerkesztheti az eszköz metaadatait.
- A **Hitelesítési kulcsok** szakasz azon kulcsokat sorolja fel, amelyeket az eszköz használhat a megoldással való hitelesítésre.

## Parancs elküldése egy eszközre

Az eszköz részletei panel megjeleníti az adott eszköz által támogatott összes parancsot, és lehetővé teszi, hogy parancsokat küldjön az eszközökre. Amikor egy eszköz először elindul, információkat küld a megoldásnak az általa támogatott parancsokról.

1.  A kiválasztott eszköz részleteit tartalmazó panelen kattintson a **Parancsok** elemre.

    ![][img-devicecommands]

2.  Válassza a **PingDevice** parancsot a parancslistából.

3.  Kattintson a **Parancs küldése** parancsra.

4.  A parancs előzményeiben láthatja a parancs állapotát.

    ![][img-pingcommand]

A megoldás nyomon követi az általa küldött összes parancs állapotát. Az eredmény kezdetben **Függőben** állapotú. Amikor az eszköz jelenti, hogy végrehajtotta a parancsot, az eredmény **Sikeres** állapotra vált.

## Új szimulált eszköz hozzáadása

1.  Lépjen vissza az eszközlistára.

2.  Kattintson a bal alsó sarokban lévő **+ Eszköz hozzáadása** gombra új eszköz hozzáadásához.

    ![][img-adddevice]

3.  Kattintson a **Szimulált eszköz** csempe **Új hozzáadása** elemére.

    ![][img-addnew]
    
    Új szimulált eszköz létrehozása mellett fizikai eszközt is hozzáadhat egy **Egyéni eszköz** létrehozásával. További információért lásd: [Az eszköz csatlakoztatása az IoT Suite előre konfigurált távoli figyelési megoldáshoz][lnk-connecting-devices].

4.  Válassza a **Meghatározom a saját eszközazonosítómat** elemet, és írjon be egy egyéni eszközazonosító nevet, például a **mydevice_01** nevet.

5.  Kattintson a ** Create** (Létrehozás) gombra.

    ![][img-definedevice]

6. A **Szimulált eszköz hozzáadása** folyamat 3. lépésében kattintson a **Kész** gombra, az eszközlistához való visszatéréshez.

7. Az eszközlistában láthatja a **futó** eszközét.

    ![][img-runningnew]

8. Az új eszköz szimulált telemetriáját is megtekintheti az irányítópulton:

    ![][img-runningnew-2]

## Az eszköz metaadatainak szerkesztése

1.  Lépjen vissza az eszközlistára.

2.  Válassza ki az új eszközt az **Eszközlistában**, majd kattintson a **Szerkesztés** gombra az **Eszköztulajdonságok** szerkesztéséhez:

    ![][img-editdevice]

3. Görgessen lefelé, és módosítsa a szélességi és a hosszúsági értékeket. Ezután kattintson az **Eszközjegyzék módosításainak mentése** elemre.

    ![][img-editdevice2]

4. Az irányítópultra visszatérve láthatja, hogy az eszköz helye változott a térképen:

    ![][img-editdevice3]

## Szabály hozzáadása az új eszközhöz

A most hozzáadott új eszköznek még nincsenek szabályai. Ebben a szakaszban olyan szabályt ad hozzá, amely riasztást küld, amikor az új eszköz által jelentett hőmérséklet meghaladja a 47 fokot. Mielőtt elkezdené, figyelje meg, hogy az irányítópulton az új eszköz telemetriaelőzményei szerint az eszköz hőmérséklete soha nem lép túl 45 fokon.

1.  Lépjen vissza az eszközlistára.

2.  Válassza ki az új eszközt az **Eszközlistában**, majd kattintson a **Szabály hozzáadása** gombra, egy új szabály hozzáadásához.

3. Hozzon létre egy szabályt, amely a **Hőmérsékletet** használja adatmezőként és az **AlarmTemp** elemet használja kimenetként, amikor a hőmérséklet meghaladja a 47 fokot:

    ![][img-adddevicerule]

4. Kattintson a **Mentés és szabályok megtekintése** elemre a módosítások mentéséhez.

5.  Az új eszköz részleteit tartalmazó panelen kattintson a **Parancsok** elemre.

    ![][img-adddevicerule2]

6.  Válassza a **ChangeSetPointTemp** parancsot a parancslistából, és a **SetPointTemp** értékét állítsa 45-re. Ezután kattintson a **Parancs küldése** parancsra:

    ![][img-adddevicerule3]

7.  Lépjen vissza a megoldás irányítópultjára. Rövid idő után új bejegyzést fog látni a **Riasztások előzményei** panelen, amikor az új eszköz hőmérséklete meghaladja a 47 fokos küszöbértéket:

    ![][img-adddevicerule4]

8. Az irányítópult **Szabályok** oldalán tekintheti át és szerkesztheti az összes szabályt:

    ![][img-rules]

9. Az irányítópult **Műveletek** oldalán tekintheti át és szerkesztheti a szabályra adott válaszként elvégezhető összes műveletet:

    ![][img-actions]

> [AZURE.NOTE] Olyan műveletek határozhatók meg, amelyek e-mail-üzenetet vagy SMS-t küldenek egy szabályra válaszul, vagy üzletági rendszerrel integrálhatók egy [logikai alkalmazáson][lnk-logic-apps] keresztül.

## A színfalak mögött

Előre konfigurált megoldás üzembe helyezésekor az üzembehelyezési folyamat több erőforrást hoz létre a kiválasztott Azure-előfizetésben. Ezeket az erőforrásokat az Azure [Portalon][lnk-portal] tekintheti meg. Az üzembehelyezési folyamat létrehoz egy **erőforráscsoportot** az előre konfigurált megoldáshoz kiválasztott néven alapuló névvel:

![][img-portal]

Az egyes erőforrások beállításainak megtekintéséhez válassza ki az erőforrást az erőforráscsoport erőforráslistájában. A fenti képernyőképen az előre konfigurált megoldásban használt IoT Hub beállításai láthatók.

Megtekintheti az előre konfigurált megoldás forráskódját is. Az előre konfigurált távoli figyelési megoldás forráskódja az [azure-iot-remote-monitoring][lnk-rmgithub] mappában található:

- A **DeviceAdministration** mappa tartalmazza az irányítópult forráskódját.
- A **Simulator** mappa tartalmazza a szimulált eszköz forráskódját.
- Az **EventProcessor** mappa tartalmazza a bejövő telemetriát kezelő háttérfolyamat forráskódját.

Amikor elkészült, törölheti az előre konfigurált megoldást az Azure-előfizetésből az [azureiotsuite.com][lnk-azureiotsuite] webhelyen – így könnyedén törölheti az előre konfigurált megoldás létrehozásakor megkapott összes erőforrást.

> [AZURE.NOTE] Ahhoz, hogy biztosan törölje az előre konfigurált megoldáshoz kapcsolódó összes elemet, törölje azt az [azureiotsuite.com][lnk-azureiotsuite] webhelyről, és ne csak az erőforráscsoportot törölje a portálon.

## Következő lépések

Most, hogy működő előre konfigurált megoldást épített, a következő bemutatókkal folytathatja:

-   [Útmutatás az előre konfigurált megoldások testreszabásához][lnk-customize]
-   [Előre konfigurált prediktív karbantartási megoldás áttekintése][lnk-predictive]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md
[lnk-connecting-devices]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-faq]: iot-suite-faq.md



<!--HONumber=jun16_HO2-->


