---
title: "Az előre konfigurált megoldások bemutatása | Microsoft Docs"
description: "Ebből az oktatóanyagból megtudhatja, hogyan telepíthet egy előre konfigurált Azure IoT Suite-megoldást."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 6ab38d1a-b564-469e-8a87-e597aa51d0f7
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f86a70a5207f19063e9992325c8f8d696ca7823e


---
# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>Oktatóprogram: Az előre konfigurált megoldások bemutatása
## <a name="introduction"></a>Bevezetés
Az Azure IoT Suite [előre konfigurált megoldások][lnk-preconfigured-solutions] több Azure IoT-szolgáltatást kombinálnak, hogy általános IoT üzleti forgatókönyveket megvalósító végpontok közötti megoldásokat nyújtsanak. Az előre konfigurált *távoli figyelő* megoldás csatlakozik az eszközökhöz, és megfigyeli azokat. Ez a megoldás az eszközökről származó adatstream elemzésére használható, valamint az ezen streamre automatikusan válaszoló folyamatok létrehozásával az üzleti eredmények is javíthatók.

Ez az oktatóprogram bemutatja, hogyan építheti ki az előre konfigurált távoli figyelő megoldást. Emellett a távoli figyelő megoldás alapszintű funkcióin is végigvezeti. Ezek közül számos szolgáltatáshoz a megoldás irányítópultján keresztül férhet hozzá, amelyet a rendszer az előre konfigurált megoldással együtt telepít:

![Az előre konfigurált távoli figyelési megoldás irányítópultja][img-dashboard]

Az oktatóanyag elvégzéséhez aktív Azure-előfizetésre lesz szüksége.

> [!NOTE]
> Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információ: [Ingyenes Azure-fiók létrehozása][lnk_free_trial].
> 
> 

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="view-the-solution-dashboard"></a>A megoldás irányítópultjának megtekintése
A megoldás irányítópultján kezelheti az üzembe helyezett megoldást. Megtekintheti például a telemetriát, eszközöket adhat hozzá és szabályokat konfigurálhat.

1. Amikor a kiépítés befejeződött, és az előre konfigurált megoldás csempéje **Kész** állapotot jelez, kattintson az **Indítás** gombra a távoli figyelési megoldás új lapon való megnyitásához.
   
   ![Az előre konfigurált megoldás indítása][img-launch-solution]
2. Alapértelmezés szerint a megoldás portálja a *megoldás irányítópultját* jeleníti meg. A bal oldali menüből választhat más nézeteket.
   
   ![Az előre konfigurált távoli figyelési megoldás irányítópultja][img-dashboard]

Az irányítópult az alábbi információkat jeleníti meg:

* A térkép a megoldáshoz csatlakoztatott egyes eszközök helyét jeleníti meg. A megoldás első futtatásakor négy szimulált eszköz van. A szimulált eszközök Azure WebJobs feladatokként vannak megvalósítva, és a megoldás a Bing Térképek API-val jeleníti meg az információkat a térképen.
* A **Telemetria előzményei** panel a páratartalommal és hőmérséklettel kapcsolatos telemetriát jelenít meg a kiválasztott eszközről közel valós időben, és összesített adatokat tartalmaz, például a maximális, minimális és átlagos páratartalmat.
* A **Riasztások előzményei** panel közelmúltbeli riasztási eseményeket jelenít meg, amikor egy telemetriaérték túllépett egy küszöbértéken. Saját riasztásokat is meghatározhat az előre konfigurált megoldás által létrehozott példák mellett.

## <a name="view-the-device-list"></a>Az eszközlista megtekintése
Az eszközlista a megoldásban regisztrált összes eszközt megjeleníti. Megtekintheti és szerkesztheti az eszközök metaadatait, eszközöket adhat hozzá vagy távolíthat el, illetve parancsokat küldhet az eszközöknek.

1. Kattintson a bal oldali menü **Eszközök** elemére a megoldás *eszközlistájának* megjelenítéséhez.
   
   ![Az eszközök listája az irányítópulton][img-devicelist]
2. Az eszközlistán látható, hogy a kiépítési folyamat négy szimulált eszközt hozott létre.
3. Kattintson egy eszközre az eszközlistában az eszköz részleteinek megtekintéséhez.
   
   ![Eszközadatok az irányítópulton][img-devicedetails]

Az **Eszköz részletei** panel három szakaszból áll:

* A **Műveletek** szakasz az eszközön elvégezhető műveleteket sorolja fel. Ha letiltja az eszközt, az többé nem küldhet telemetriát és nem fogadhat parancsokat. A letiltott eszközöket újra engedélyezheti. Az eszközhöz társított szabályt adhat hozzá, amely kivált egy riasztást, ha egy telemetriaérték átlép egy küszöbértéket. Parancsot is küldhet az eszközökre. Amikor egy eszköz először csatlakozik, közli azokat a parancsokat a megoldással, amelyekre válaszolhat.
* Az **Eszköztulajdonságok** szakasz az eszköz metaadatait listázza. Egyes metaadatok magáról az eszközről származnak (például a gyártó), másokat pedig megoldás hoz létre (például a létrehozás idejét). Itt szerkesztheti az eszköz metaadatait.
* A **Hitelesítési kulcsok** szakasz azon kulcsokat sorolja fel, amelyeket az eszköz használhat a megoldással való hitelesítésre.

## <a name="send-a-command-to-a-device"></a>Parancs elküldése egy eszközre
Az eszköz részletei panel megjeleníti az adott eszköz által támogatott összes parancsot, és lehetővé teszi, hogy parancsokat küldjön az eszközökre. Amikor egy eszköz először elindul, információkat küld a megoldásnak az általa támogatott parancsokról.

1. A kiválasztott eszköz részleteit tartalmazó panelen kattintson a **Parancsok** elemre.
   
   ![Eszközparancsok az irányítópulton][img-devicecommands]
2. Válassza a **PingDevice** parancsot a parancslistából.
3. Kattintson a **Parancs küldése** parancsra.
4. A parancs előzményeiben láthatja a parancs állapotát.
   
   ![Parancsállapot az irányítópulton][img-pingcommand]

A megoldás nyomon követi az általa küldött összes parancs állapotát. Az eredmény kezdetben **Függőben** állapotú. Amikor az eszköz jelenti, hogy végrehajtotta a parancsot, az eredmény **Sikeres** állapotra vált.

## <a name="add-a-new-simulated-device"></a>Új szimulált eszköz hozzáadása
Az előre konfigurált megoldás üzembe helyezésekor az eszközlistában látható négy mintaeszköz kiosztása automatikusan megtörténik. Ezek az eszközök Azure WebJobs-feladatban futó, *szimulált eszközök*. A szimulált eszközök megkönnyítik az előre konfigurált megoldással történő kísérletezést, anélkül, hogy valódi, fizikai eszközök üzembe helyezésére lenne szükség. Valós eszközöknek a megoldáshoz történő csatlakoztatásáról a következő oktatóanyagban olvashat: [Az eszköz csatlakoztatása az előre konfigurált távoli figyelési megoldáshoz][lnk-connect-rm].

Az alábbi lépések bemutatják, hogyan adhat szimulált eszközt a megoldáshoz:

1. Lépjen vissza az eszközlistára.
2. Kattintson a bal alsó sarokban lévő **+ Eszköz hozzáadása** gombra egy eszköz hozzáadásához.
   
   ![Eszköz hozzáadása az előre konfigurált megoldáshoz][img-adddevice]
3. Kattintson a **Szimulált eszköz** csempe **Új hozzáadása** elemére.
   
   ![Az új eszköz adatainak megadása az irányítópulton][img-addnew]
   
   Új szimulált eszköz létrehozása mellett fizikai eszközt is hozzáadhat egy **Egyéni eszköz** létrehozásával. További információ fizikai eszközöknek a megoldáshoz csatlakoztatásáról: [Az eszköz csatlakoztatása az IoT Suite előre konfigurált távoli figyelési megoldáshoz][lnk-connect-rm].
4. Válassza a **Meghatározom a saját eszközazonosítómat** elemet, és írjon be egy egyéni eszközazonosító nevet, például a **mydevice_01** nevet.
5. Kattintson a **Létrehozás** gombra.
   
   ![Új eszköz mentése][img-definedevice]
6. A **Szimulált eszköz hozzáadása** folyamat 3. lépésében kattintson a **Kész** gombra az eszközlistához való visszatéréshez.
7. Az eszközlistában láthatja a **futó** eszközét.
   
    ![Új eszköz megtekintése az eszközlistában][img-runningnew]
8. Az új eszköz szimulált telemetriáját is megtekintheti az irányítópulton:
   
    ![Az új eszköz telemetriájának megtekintése][img-runningnew-2]

## <a name="edit-the-device-metadata"></a>Az eszköz metaadatainak szerkesztése
Amikor az eszköz első alkalommal csatlakozik a megoldáshoz, elküldi metaadatait a megoldás számára. Ha az eszköz metaadatait a megoldás irányítópultján szerkeszti, az elküldi az új metaadatértékeket az eszköznek, és a megoldás DocumentDB-adatbázisában tárolja azokat. További információ: [Eszközidentitás-jegyzék és DocumentDB][lnk-devicemetadata].

1. Lépjen vissza az eszközlistára.
2. Válassza ki az új eszközt az **Eszközlistában**, majd kattintson a **Szerkesztés** gombra az **Eszköztulajdonságok** szerkesztéséhez:
   
   ![Az eszköz metaadatainak szerkesztése][img-editdevice]
3. Görgessen lefelé, és módosítsa a szélességi és a hosszúsági értékeket. Ezután kattintson az **Eszközjegyzék módosításainak mentése** elemre.
   
    ![Az eszköz metaadatainak szerkesztése][img-editdevice2]
4. Az irányítópultra visszatérve láthatja, hogy az eszköz helye változott a térképen:
   
    ![Az eszköz metaadatainak szerkesztése][img-editdevice3]

## <a name="add-a-rule-for-the-new-device"></a>Szabály hozzáadása az új eszközhöz
A most hozzáadott új eszköznek még nincsenek szabályai. Ebben a szakaszban olyan szabályt ad hozzá, amely riasztást küld, amikor az új eszköz által jelentett hőmérséklet meghaladja a 47 fokot. Mielőtt elkezdené, figyelje meg, hogy az irányítópulton az új eszköz telemetriaelőzményei szerint az eszköz hőmérséklete soha nem lép túl 45 fokon.

1. Lépjen vissza az eszközlistára.
2. Válassza ki az új eszközt az **Eszközlistában**, majd kattintson a **Szabály hozzáadása** gombra egy szabály hozzáadásához.
3. Hozzon létre egy szabályt, amely a **Hőmérsékletet** használja adatmezőként és az **AlarmTemp** elemet használja kimenetként, amikor a hőmérséklet meghaladja a 47 fokot:
   
    ![Eszközszabály hozzáadása][img-adddevicerule]
4. Kattintson a **Mentés és szabályok megtekintése** elemre a módosítások mentéséhez.
5. Az új eszköz részleteit tartalmazó panelen kattintson a **Parancsok** elemre.
   
   ![Eszközszabály hozzáadása][img-adddevicerule2]
6. Válassza a **ChangeSetPointTemp** parancsot a parancslistából, és a **SetPointTemp** értékét állítsa 45-re. Ezután kattintson a **Parancs küldése** parancsra:
   
   ![Eszközszabály hozzáadása][img-adddevicerule3]
7. Lépjen vissza a megoldás irányítópultjára. Rövid idő után új bejegyzést fog látni a **Riasztások előzményei** panelen, amikor az új eszköz hőmérséklete meghaladja a 47 fokos küszöbértéket:
   
   ![Eszközszabály hozzáadása][img-adddevicerule4]
8. Az irányítópult **Szabályok** oldalán tekintheti át és szerkesztheti az összes szabályt:
   
    ![Eszközszabályok listázása][img-rules]
9. Az irányítópult **Műveletek** oldalán tekintheti át és szerkesztheti a szabályra adott válaszként elvégezhető összes műveletet:
   
    ![Eszközműveletek listázása][img-actions]

> [!NOTE]
> Olyan műveletek határozhatók meg, amelyek e-mail-üzenetet vagy SMS-t küldenek egy szabályra válaszul, vagy üzletági rendszerrel integrálhatók egy [logikai alkalmazáson][lnk-logic-apps] keresztül. További információ: [Logikai alkalmazás csatlakoztatása az Azure IoT Suite előre konfigurált távoli figyelési megoldásához][lnk-logicapptutorial].
> 
> 

## <a name="other-features"></a>Egyéb jellemzők
A megoldás portáljával adott jellemzőkkel, például modellszámmal rendelkező eszközöket kereshet:

![Eszköz keresése][img-search]

Letilthatja az eszközöket, és a letiltásuk után eltávolíthatja őket:

![Eszköz letiltása és eltávolítása][img-disable]

## <a name="behind-the-scenes"></a>A színfalak mögött
Előre konfigurált megoldás üzembe helyezésekor az üzembehelyezési folyamat több erőforrást hoz létre a kiválasztott Azure-előfizetésben. Ezeket az erőforrásokat az Azure [Portalon][lnk-portal] tekintheti meg. Az üzembe helyezési folyamat létrehoz egy **erőforráscsoportot** az előre konfigurált megoldáshoz kiválasztott néven alapuló névvel:

![Előre konfigurált megoldás az Azure Portalon][img-portal]

Az egyes erőforrások beállításainak megtekintéséhez válassza ki az erőforrást az erőforráscsoport erőforráslistájában.

Megtekintheti az előre konfigurált megoldás forráskódját is. Az előre konfigurált távoli figyelési megoldás forráskódja az [azure-iot-remote-monitoring][lnk-rmgithub] GitHub-adattárban található:

* A **DeviceAdministration** mappa tartalmazza az irányítópult forráskódját.
* A **Simulator** mappa tartalmazza a szimulált eszköz forráskódját.
* Az **EventProcessor** mappa tartalmazza a bejövő telemetriát kezelő háttérfolyamat forráskódját.

Amikor elkészült, törölheti az előre konfigurált megoldást az Azure-előfizetésből az [azureiotsuite.com][lnk-azureiotsuite] webhelyen. Így könnyedén törölheti az előre konfigurált megoldás létrehozásakor megkapott összes erőforrást.

> [!NOTE]
> Ahhoz, hogy biztosan törölje az előre konfigurált megoldáshoz kapcsolódó összes elemet, törölje a megoldást az [azureiotsuite.com][lnk-azureiotsuite] webhelyről, és ne törölje az erőforráscsoportot a portálon.
> 
> 

## <a name="next-steps"></a>Következő lépések
Most, hogy üzembe helyezett egy működő előre konfigurált megoldást, a következő cikkek elolvasásával folytathatja az ismerkedést az IoT Suite használatával:

* [A távoli figyelési előre konfigurált megoldás bemutatója][lnk-rm-walkthrough]
* [Az eszköz csatlakoztatása az előre konfigurált távoli figyelési megoldáshoz][lnk-connect-rm]
* [Engedélyek az azureiotsuite.com webhelyen][lnk-permissions]

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
[img-search]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_07.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md



<!--HONumber=Dec16_HO1-->


