---
title: A Connected Factory irányítópult használata - Azure | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan használhatja a csatlakoztatott gyári irányítópult funkcióit az ipari IoT-eszközök figyelésére és kezelésére.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: b53177d578768428665891704269e63bd8edb09e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820172"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>A Csatlakoztatott gyári megoldásgyorsító irányítópultjának szolgáltatásainak használata

Az [ipari IoT-eszközök kezeléséhez felhőalapú megoldás üzembe helyezése](quickstart-connected-factory-deploy.md) rövid útmutató bemutatja, hogyan navigálhat az irányítópulton, és hogyan reagálhat a riasztásokra. Ez az útmutató bemutatja néhány további irányítópult-funkciót, amelyekkel figyelheti és kezelheti az ipari IoT-eszközöket.

## <a name="apply-filters"></a>Szűrők alkalmazása

Az irányítópulton megjelenő információkat a **Gyári helyek** panelen vagy a **Riasztások** panelen szűrheti:

1. Kattintson a **tölcsérre** a gyári telephelyek vagy a riasztások panelén elérhető szűrők listájának megjelenítéséhez.

1. Megjelenik a szűrők panel:

    [![A csatlakoztatottgyár-alapú megoldásgyorsító szűrői](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. Válassza ki a kívánt szűrőt, és kattintson **az Alkalmaz gombra.** Szabad szöveg et is beírhat a szűrőmezőkbe.

1. A rendszer ezután alkalmazza a szűrőt. Az extra tölcsér ikon azt jelzi, hogy a rendszer szűrőt alkalmaz:

    [![Csatlakoztatott gyári megoldásgyorsító szűrő alkalmazva](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > Az aktív szűrő nincs hatással a megjelenített OEE és KPI értékekre, csak a lista tartalmát szűri.

1. Szűrő törléséhez kattintson a tölcsérre, majd a szűrőpanelen a **Törlés** gombra.

## <a name="browse-an-opc-ua-server"></a>OPC UA-kiszolgáló tallózása

A megoldásgyorsító telepítésekor automatikusan kiépíti a szimulált OPC UA-kiszolgálókat, amelyek az irányítópultról tallózhatnak. A szimulált kiszolgálók megkönnyítik a megoldásgyorsítóval való kísérletezést anélkül, hogy valódi kiszolgálókat kellene telepítenie.

1. Kattintson a **böngésző ikonjára** az irányítópult navigációs sávján:

    [![A csatlakoztatottgyár-alapú megoldásgyorsító kiszolgálótallózója](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. Válassza ki az egyik kiszolgálót a megoldásgyorsítóban telepített kiszolgálókat megjelenítő listából:

    [![Csatlakoztatott gyári megoldásgyorsító kiszolgálólistája](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. Kattintson a **Connect** (Csatlakozás) gombra. Megjelenik egy biztonsági párbeszédablak. A szimulációhoz a **Folytatás**gombra kattintva biztosan kattinthat.

1. Kattintson bármely csomópontra a kiszolgálófán a kibontásához. A telemetriai adatokat közzétenni tartalmazó csomópontok mellett pipa van:

    [![A csatlakoztatottgyár-alapú megoldásgyorsító kiszolgálófája](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. Egy csomópont olvasásához, írásához, közzétételéhez vagy meghívásához kattintson jobb gombbal az adott elemre. Az elérhető műveletek a jogosultságaitól és a csomópont attribútumaitól függnek. Az olvasási lehetőség egy helyi panelt jelenít meg, amelyen az adott csomópont értéke látható. Az írási lehetőség egy olyan helyi panelt jelenít meg, amelyen új értéket adhat meg. A hívási lehetőség egy csomópontot jelenít meg, amelyen megadhatja a hívás paramétereit.

## <a name="publish-a-node"></a>Csomópont közzététele

Ha egy *szimulált OPC UA-kiszolgálóhoz* tallóz, új csomópontokat is közzétehet, ha szeretne. Ezeknek a csomópontoknak a telemetriáját is elemezheti a megoldásban. Ezek *a szimulált OPC UA-kiszolgálók* megkönnyítik a kísérletezést a megoldásgyorsítóval valódi eszközök telepítése nélkül:

1. Lépjen egy közzétenni kívánt csomópontra az OPC UA-kiszolgáló tallózási fáján.

1. Kattintson a jobb gombbal a csomópontra. Kattintson **a Közzététel gombra:**

    [![Csatlakoztatott gyári megoldásgyorsító közzététele csomópont](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. Megjelenik egy helyi panel, amely tájékoztatja, hogy a közzététel sikeres volt. A csomópont az állomásszintű nézetben jelenik meg, mellette egy pipával:

    [![A Connected Factory megoldásgyorsító sikeres közzétételét](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>Parancs és vezérlés

A csatlakoztatott gyár segítségével közvetlenül a felhőből irányíthatja és felügyelheti ipari eszközeit. A szolgáltatással az eszközök által létrehozott riasztásokra is reagálhat, Például, akkor küldjön egy parancsot, hogy a készülék, hogy nyissa meg a nyomás kioldó szelep. A rendelkezésre álló parancsokat az OPC UA-kiszolgáló tallózási fájának **StationCommands** csomópontján találja. Ebben a forgatókönyvben megnyit egy nyomáskiegyenlítő szelepet egy müncheni gyártósor összeszerelő állomásán. A parancs- és vezérlőfunkció használatához a megoldásgyorsító telepítéséhez **rendszergazdai** szerepkörben kell lennie:

1. Keresse meg a **StationCommands** csomópontot a München OPC UA-kiszolgálóböngészőfájában, a 0 gyártósor, az összeszerelő állomás.

1. Válassza ki a használni kívánt parancsot. Kattintson a jobb gombbal az **OpenPressureReleaseValve** csomópontra. Kattintson **a Hívás gombra:**

    [![A csatlakoztatottgyár-alapú megoldásgyorsító hívás parancsa](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. Megjelenik egy környezetpanel, amely tájékoztatja, hogy melyik metódust hívja meg, és milyen paraméterekkel kapcsolatos részleteket. Kattintson **a Hívás gombra:**

    [![Csatlakoztatott gyári megoldásgyorsító hívási paraméterei](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. A helyi panel frissül, és tájékoztatja róla, ha a metódus meghívása sikeres volt. A hívás sikerességét ellenőrizheti a hívás eredményeképpen frissített nyomásmérő csomópont értékének leolvasásával.

    [![A csatlakoztatottgyár-alapú megoldásgyorsító sikeres hívása](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>A színfalak mögött

Megoldásgyorsító üzembe helyezésekor az üzembehelyezési folyamat több erőforrást hoz létre a kiválasztott Azure-előfizetésben. Ezeket az erőforrásokat az Azure [Portalon](https://portal.azure.com) tekintheti meg. Az üzembehelyezési folyamat létrehoz egy **erőforráscsoportot** a megoldásgyorsítóhoz kiválasztott néven alapuló névvel:

[![Csatlakoztatott gyári megoldásgyorsító erőforráscsoport](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

Az egyes erőforrások beállításainak megtekintéséhez válassza ki az erőforrást az erőforráscsoport erőforráslistájában.

Megtekintheti a megoldásgyorsító forráskódját is az [azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory) GitHub-tárházban.

Ha elkészült, törölheti a megoldásgyorsítót az Azure-előfizetésből a [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) webhelyen. Így könnyedén törölheti a megoldásgyorsító létrehozásakor megkapott összes erőforrást.

> [!NOTE]
> Annak érdekében, hogy töröljön mindent, ami a megoldásgyorsítóhoz kapcsolódik, törölje azt a [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) webhelyen. Ne törölje az erőforráscsoportot a portálon.

## <a name="next-steps"></a>További lépések

Most, hogy üzembe helyezett egy működő megoldásgyorsítót, a következő cikkek elolvasásával folytathatja az ismerkedést az IoT-megoldásgyorsítók használatával:

* [A csatlakoztatott gyári megoldásgyorsító konfigurálása](iot-accelerators-connected-factory-configure.md)
* [Engedélyek a azureiotsolutions.com webhelyen](iot-accelerators-permissions.md)
