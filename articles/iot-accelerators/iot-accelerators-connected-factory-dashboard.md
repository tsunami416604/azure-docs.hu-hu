---
title: Használja a csatlakoztatott gyári irányítópultot – Azure |} A Microsoft Docs
description: Megtudhatja, hogyan használja a csatlakoztatott gyári irányítópultot funkcióit.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: 4af93b5667181941585cc1ac89f5696379962026
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076100"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>A csatlakoztatott gyár megoldás gyorsító irányítópultján szolgáltatások használata

A [ipari IoT-eszközök kezeléséhez felhőalapú megoldás üzembe helyezése](quickstart-connected-factory-deploy.md) a gyors útmutató bemutatta, hogyan keresse meg az irányítópult és az azokra való riasztásokat. Ez az útmutató bemutatja, néhány további irányítópult-szolgáltatás figyelését és kezelését az ipari IoT-eszközeit használhatja.

## <a name="apply-filters"></a>Szűrők alkalmazása

Az irányítópulton megjelenő információk szűrheti a a **gyári telephelyek** panel vagy a **riasztások** panelen:

1. Kattintson a **tölcsérre** a gyári telephelyek vagy a riasztások panelén elérhető szűrők listájának megjelenítéséhez.

1. A szűrők panel jelenik meg:

    [![Csatlakoztatott gyár megoldás – gyorsító szűrők](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. Válassza ki a szűrőt, amely igényelnek, és kattintson a **alkalmaz**. Akkor is, ha szabad szöveget a szűrő mezőkbe.

1. A szűrő ezután alkalmazva lesz. Extra tölcsér ikon azt jelzi, hogy a szűrő alkalmazása:

    [![Csatlakoztatott gyár megoldás gyorsító alkalmazott szűrő](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > Az aktív szűrők nincsenek hatással a megjelenített OEE és KPI mutatókra, csak a lista tartalmát szűri.

1. A szűrő törléséhez kattintson a tölcsér ikonra, és kattintson a **törölje a jelet** a szűrő panelen.

## <a name="browse-an-opc-ua-server"></a>OPC UA-kiszolgáló tallózása

A megoldásgyorsító központi telepítésekor automatikusan megkeresheti az irányítópultról szimulált OPC UA-kiszolgálók egy csoportja építhet ki. Szimulált kiszolgálók megkönnyítik, hogy a megoldásgyorsító valós kiszolgálók üzembe helyezése nélkül kísérletezhet. Ha szeretné valós OPC UA-kiszolgálóknak a megoldáshoz történő csatlakoztatásáról, tekintse meg a [az OPC UA-eszköz csatlakoztatása az Okosgyár-megoldásgyorsító](iot-accelerators-connected-factory-gateway-deployment.md) oktatóanyag.

1. Kattintson a **browser ikonja** az irányítópult navigációs sávján:

    [![Csatlakoztatott gyár megoldás gyorsító Kiszolgálótallózó](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. A kiszolgálók közül választhat a listából, amely az Ön számára a megoldásgyorsító üzembe helyezett kiszolgálókat mutatja:

    [![Csatlakoztatott gyár megoldás gyorsító kiszolgálólista](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. Kattintson a **Connect** (Csatlakozás) gombra. Megjelenik egy biztonsági párbeszédablak. A szimuláció esetében biztonságosan kattinthat a **folytatása**.

1. Kattintson bármely csomópontra a kiszolgálófán a kibontásához. Telemetriaadatokat közzétevő csomópontokat egy pipa mellett van:

    [![Csatlakoztatott gyár megoldás gyorsító kiszolgálófa](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. Egy csomópont olvasásához, írásához, közzétételéhez vagy meghívásához kattintson jobb gombbal az adott elemre. Az elérhető műveletek a jogosultságaitól és a csomópont attribútumaitól függnek. Az olvasási lehetőség egy helyi panelt jelenít meg, amelyen az adott csomópont értéke látható. Az írási lehetőség egy olyan helyi panelt jelenít meg, amelyen új értéket adhat meg. A hívási lehetőség egy csomópontot jelenít meg, amelyen megadhatja a hívás paramétereit.

## <a name="publish-a-node"></a>Csomópont közzététele

Ha egy *szimulált OPC UA-kiszolgálóhoz* tallóz, új csomópontokat is közzétehet, ha szeretne. Ezeknek a csomópontoknak a telemetriáját is elemezheti a megoldásban. Ezek *szimulált OPC UA-kiszolgálók* megkönnyítik a megoldásgyorsító kísérletezhet a valós eszközökön telepítés nélkül:

1. Lépjen egy közzétenni kívánt csomópontra az OPC UA-kiszolgáló tallózási fáján.

1. Kattintson a jobb gombbal a csomópontra. Kattintson a **közzététele**:

    [![Csatlakoztatott Factory megoldásgyorsító – csomópont közzététele](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. Megjelenik egy helyi panel, amely tájékoztatja, hogy a közzététel sikeres volt. A csomópont megjelenik az állomásszinten, mellette egy pipa állomásszintű nézetig:

    [![Csatlakoztatott Factory megoldásgyorsító – sikeres közzététel](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>Parancs és vezérlés

A csatlakoztatott gyár segítségével közvetlenül a felhőből irányíthatja és felügyelheti ipari eszközeit. A szolgáltatással az eszközök által létrehozott riasztásokra is reagálhat, Például egy parancs sikerült küldése kiadás nyomáskiegyenlítő szelepet megnyitásához az eszközön. A rendelkezésre álló parancsokat az OPC UA-kiszolgáló tallózási fájának **StationCommands** csomópontján találja. Ebben a forgatókönyvben megnyit egy nyomáskiegyenlítő szelepet egy müncheni gyártósor összeszerelő állomásán. A parancs és vezérlés funkciók használatához meg kell lennie a **rendszergazda** szerepkör a megoldás megoldásgyorsító üzembe helyezése:

1. Keresse meg a **StationCommands** csomópontra az OPC UA böngésző kiszolgálófán a München, 0 gyártósor összeszerelő állomásból.

1. Válassza ki a használni kívánt parancsot. Kattintson a jobb gombbal a **OpenPressureReleaseValve** csomópont. Kattintson a **hívás**:

    [![Csatlakoztatott gyár megoldás gyorsító – hívás parancs](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. Megjelenik egy helyi panel tájékoztatja arról, melyik módszert végrehajtandó hívás- és bármely paraméterek részleteiről. Kattintson a **hívás**:

    [![Csatlakoztatott gyár megoldás gyorsító hívás paraméterek](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. A helyi panel frissül, és tájékoztatja róla, ha a metódus meghívása sikeres volt. A hívás sikerességét ellenőrizheti a hívás eredményeképpen frissített nyomásmérő csomópont értékének leolvasásával.

    [![Csatlakoztatott gyár megoldás gyorsító – sikeres hívás](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>A színfalak mögött

Megoldásgyorsító üzembe helyezésekor az üzembehelyezési folyamat több erőforrást hoz létre a kiválasztott Azure-előfizetésben. Az Azure-ban is megtekintheti ezeket az erőforrásokat [portál](https://portal.azure.com). Az üzembehelyezési folyamat létrehoz egy **erőforráscsoportot** a megoldásgyorsítóhoz kiválasztott néven alapuló névvel:

[![Csatlakoztatott gyár megoldás gyorsító erőforráscsoport](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

Az egyes erőforrások beállításainak megtekintéséhez válassza ki az erőforrást az erőforráscsoport erőforráslistájában.

A megoldásgyorsító a forráskódja is megtekintheti a [azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory) GitHub-adattárban.

Ha elkészült, törölheti a megoldásgyorsító az Azure-előfizetését az a [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) hely. Így könnyedén törölheti a megoldásgyorsító létrehozásakor megkapott összes erőforrást.

> [!NOTE]
> Ahhoz, hogy Ön törölje a megoldásgyorsító kapcsolódó összes elemet, törölje a [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) hely. Ne törölje az erőforráscsoportot a portálon.

## <a name="next-steps"></a>További lépések

Most, hogy üzembe helyezett egy működő megoldásgyorsítót, a következő cikkek elolvasásával folytathatja az ismerkedést az IoT-megoldásgyorsítók használatával:

* [Csatlakoztatott Factory megoldásgyorsító bemutatója](iot-accelerators-connected-factory-sample-walkthrough.md)
* [Az eszköz csatlakoztatása az Okosgyár-megoldásgyorsító](iot-accelerators-connected-factory-gateway-deployment.md)
* [Engedélyek az azureiotsolutions.com webhelyen](iot-accelerators-permissions.md)
