---
title: Az eszközök kezelése az Azure IoT Central alkalmazásban | Microsoft dokumentumok
description: Operátorként megtudhatja, hogyan kezelheti az eszközöket az Azure IoT Central alkalmazásban.
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8725a822c575ce80b9810d56bfd072241ded4c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157942"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Eszközök kezelése az Azure IoT Central alkalmazásban



Ez a cikk ismerteti, hogyan, mint operátor, az Azure IoT Central alkalmazás eszközeinek kezelése. Operátorként a következőket teheti:

- Az **Eszközök** lapon megtekintheti, hozzáadhatja és törölheti az Azure IoT Central alkalmazáshoz csatlakoztatott eszközöket.
- Naprakész leltárt tarthat az eszközeiről.
- Tartsa naprakészen az eszköz metaadatait az eszköz tulajdonságaiban tárolt értékek nek a nézetekből történő módosításával.
- Az eszközök működését úgy szabályozhatja, hogy egy adott eszközön lévő beállítást frissít a nézetekből.

## <a name="view-your-devices"></a>Eszközök megtekintése

Egyedi eszköz megtekintése:

1. Válassza az **Eszközök lehetőséget** a bal oldali ablaktáblán. Itt láthatja az összes eszköz és az eszközsablonok listáját.

1. Válasszon egy eszközsablont.

1. Az **Eszközök** lap jobb oldali ablaktáblájában az adott eszközsablonból létrehozott eszközök listája látható. Válasszon egy adott eszközt az eszköz részletes oldalának megtekintéséhez:

    ![Eszköz részletei lap](./media/howto-manage-devices/devicelist.png)


## <a name="add-a-device"></a>Eszköz hozzáadása

Eszköz hozzáadása az Azure IoT Central alkalmazáshoz:

1. Válassza az **Eszközök lehetőséget** a bal oldali ablaktáblán.

1. Válassza ki azt az eszközsablont, amelyből létre szeretne hozni egy eszközt.

1. Válassza a + **Új**lehetőséget.

1. Kapcsolja be vagy **ki** **a** **Szimulált kapcsolót.** Egy valódi eszköz egy fizikai eszköz, amely csatlakozik az Azure IoT Central alkalmazáshoz. Egy szimulált eszköz az Azure IoT Central által létrehozott mintaadatokat hoz létre.

1. Kattintson **a Létrehozás gombra.**

1. Ez az eszköz most megjelenik a sablon eszközlistájában. Válassza ki az eszközt az eszköz részletes lapjának megtekintéséhez, amely az eszköz összes nézetét tartalmazza.

## <a name="import-devices"></a>Eszközök importálása

Ha nagy számú eszközt szeretne csatlakoztatni az alkalmazáshoz, tömegesen importálhat eszközöket egy CSV-fájlból. A CSV-fájlnak a következő oszlopokkal és fejlécekkel kell rendelkeznie:

* **IOTC_DeviceID** - az eszköz azonosítójának kisbetűsnek kell lennie.
* **IOTC_DeviceName** – ez az oszlop nem kötelező.

Eszközök tömeges regisztrálása az alkalmazásban:

1. Válassza az **Eszközök lehetőséget** a bal oldali ablaktáblán.

1. A bal oldali panelen válassza ki azt az eszközsablont, amelyhez tömegesen létre szeretné hozni az eszközöket.

    > [!NOTE]
    > Ha még nem rendelkezik eszközsablonnal, akkor importálhatja az eszközöket **az Összes eszköz** alatt, és sablon nélkül regisztrálhatja őket. Az eszközök importálása után áttelepítheti őket egy sablonba.

1. Kattintson az **Importálás** gombra.

    ![Importálási művelet](./media/howto-manage-devices/bulkimport1a.png)


1. Jelölje ki azt a CSV-fájlt, amely tartalmazza az importálandó eszközazonosítók listáját.

1. Az eszközimportálás a fájl feltöltése után kezdődik. Az importálási állapotot az Eszközműveletek panelen követheti nyomon. Ez a panel automatikusan megjelenik az importálás megkezdése után, vagy a jobb felső sarokban lévő csengő ikonon keresztül érhető el.

1. Az importálás befejezése után egy sikeres üzenet jelenik meg az Eszközműveletek panelen.

    ![Sikeres importálás](./media/howto-manage-devices/bulkimport3a.png)


Ha az eszköz importálási művelete sikertelen, hibaüzenet jelenik meg az Eszköz műveletek panelén. A naplófájl rögzíti az összes hibát lehet letölteni.

**Eszközök áttelepítése sablonba**

Ha az eszközök regisztrálásával indítja el az importálást a **Minden eszköz**területen, akkor az eszközök eszközsablon-társítás nélkül jönnek létre. Az eszközöket egy sablonhoz kell társozni az eszköz adatainak és egyéb részleteinek megismeréséhez. Az eszközök sablonhoz való társításához kövesse az alábbi lépéseket:

1. Válassza az **Eszközök lehetőséget** a bal oldali ablaktáblán.

1. A bal oldali panelen válassza a **Minden eszköz**lehetőséget:

    ![Nem társított eszközök](./media/howto-manage-devices/unassociateddevices1a.png)


1. A rácsszűrő segítségével megállapíthatja, hogy az **Eszközsablon** oszlopértéke "Nem társ"-e bármelyik eszközhöz.

1. Jelölje ki a sablonhoz társítani kívánt eszközöket:

1. Válassza **az Áttelepítés**lehetőséget:

    ![Társeszközök](./media/howto-manage-devices/unassociateddevices2a.png)


1. Válassza ki a sablont az elérhető sablonok listájából, és válassza az **Áttelepítés**lehetőséget.

1. A kiválasztott eszközök a kiválasztott eszközsablonhoz vannak társítva.


## <a name="export-devices"></a>Eszközök exportálása

Egy valódi eszköz csatlakoztatásához az IoT Central, szüksége van a kapcsolati karakterlánc. Az eszköz adatait ömlesztve exportálhatja, hogy megkapja az eszközkapcsolati karakterláncok létrehozásához szükséges információkat. Az exportálási folyamat létrehoz egy CSV-fájlt az eszköz identitásával, az eszköz nevével és a kulcsokkal az összes kiválasztott eszközhöz.

Eszközök tömeges exportálása az alkalmazásból:

1. Válassza az **Eszközök lehetőséget** a bal oldali ablaktáblán.

1. A bal oldali ablaktáblán válassza ki azt az eszközsablont, amelyből exportálni szeretné az eszközöket.

1. Jelölje ki az exportálni kívánt eszközöket, majd jelölje ki az **Exportálás** műveletet.

    ![Exportálás](./media/howto-manage-devices/export1a.png)


1. Az exportálási folyamat elindul. Az állapotot az Eszközműveletek panelen követheti nyomon.

1. Amikor az exportálás befejeződik, egy sikeres üzenet jelenik meg a létrehozott fájl letöltésére mutató hivatkozással együtt.

1. A **Fájl letöltése** hivatkozással töltse le a fájlt a lemez helyi mappájába.

    ![Sikeres exportálás](./media/howto-manage-devices/export2a.png)


1. Az exportált CSV-fájl a következő oszlopokat tartalmazza: eszközazonosító, eszköznév, eszközkulcsok és X509 tanúsítvány ujjlenyomatai:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

A kapcsolati karakterláncokról és a valódi eszközök IoT Central-alkalmazáshoz való csatlakoztatásáról az [Azure IoT Central eszközkapcsolat című témakörében](concepts-get-connected.md)talál további információt.

## <a name="delete-a-device"></a>Eszköz törlése

Valódi vagy szimulált eszköz törlése az Azure IoT Central alkalmazásból:

1. Válassza az **Eszközök lehetőséget** a bal oldali ablaktáblán.

1. Válassza ki a törölni kívánt eszköz eszközsablonját.

1. A szűrőeszközökkel szűrheti és keresheti meg eszközeit. Jelölje be a törölni kívánt eszközök melletti jelölőnégyzetet.

1. Válassza a **Törlés** elemet. A törlés állapotát az Eszközműveletek panelen követheti nyomon.

## <a name="change-a-property"></a>Tulajdonság módosítása

A felhőtulajdonságok az eszközhöz társított eszköz metaadatai, például a város- és sorozatszám. Az írható tulajdonságok szabályozzák az eszköz viselkedését. Más szóval lehetővé teszik, hogy adatokat biztosítson az eszközhöz.  Az eszköz tulajdonságait az eszköz állítja be, és csak olvasható az IoT Central on belül. A tulajdonságokat az **eszköz eszközadatai** nézetekben tekintheti meg és frissítheti.

1. Válassza az **Eszközök lehetőséget** a bal oldali ablaktáblán.

1. Válassza ki annak az eszköznek az eszközsablonját, amelynek tulajdonságait módosítani szeretné, és jelölje ki a céleszközt.

1. Válassza ki az eszköz tulajdonságait tartalmazó nézetet, ez a nézet lehetővé teszi az értékek bevitelét, és a lap tetején a **Mentés** lehetőséget választja. Itt láthatja az eszköz tulajdonságait és aktuális értékeit. A felhőbeli tulajdonságok és az írható tulajdonságok szerkeszthető mezőkkel rendelkeznek, míg az eszköz tulajdonságai írásvédettek. Írható tulajdonságok esetén a szinkronizálási állapotuk a mező alján látható. 

1. Módosítsa a tulajdonságokat a szükséges értékekre. Egyszerre több tulajdonságot is módosíthat, és egyszerre frissítheti azokat.

1. Válassza a **Mentés** elemet. Ha írható tulajdonságokat mentett, a rendszer elküldi az értékeket az eszközre. Amikor az eszköz megerősíti az írható tulajdonság módosítását, az állapot visszatér **a szinkronizált**állapotba. Ha mentett egy felhőtulajdonságot, az érték frissül.


## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan kezelheti az eszközöket az Azure IoT Central alkalmazásban, az alábbiakat javasolta:

> [!div class="nextstepaction"]
> [Eszközcsoportok használata](tutorial-use-device-groups.md)

<!-- Next how-tos in the sequence -->
