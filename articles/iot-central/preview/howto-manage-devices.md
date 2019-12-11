---
title: Az Azure IoT Central alkalmazásban lévő eszközök kezelése | Microsoft Docs
description: Operátorként megismerheti, hogyan kezelheti az eszközöket az Azure IoT Central alkalmazásban.
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/06/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: bab5411b63cb71c47656696bd8049855ca797a57
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974104"
---
# <a name="manage-devices-in-your-azure-iot-central-application-preview-features"></a>Eszközök kezelése az Azure IoT Central alkalmazásban (előzetes verziójú funkciók)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ez a cikk azt ismerteti, hogyan felügyelheti az eszközöket az Azure IoT Central alkalmazásban. Operátorként a következőket teheti:

- Az **eszközök** lapon megtekintheti, hozzáadhatja és törölheti az Azure IoT Central-alkalmazáshoz csatlakoztatott eszközöket.
- Naprakész leltárt készíthet az eszközökről.
- Tartsa naprakészen az eszköz metaadatait úgy, hogy megváltoztatja az eszköz tulajdonságai között tárolt értékeket a nézetekben.
- Az eszközök viselkedésének szabályozása egy adott eszközre vonatkozó beállítások frissítésével a nézetekben.

## <a name="view-your-devices"></a>Eszközök megtekintése

Egy adott eszköz megtekintése:

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán. Itt látható az összes eszköz és az eszköz sablonjainak listája.

1. Válassza ki az eszköz sablonját.

1. Az **eszközök** lap jobb oldali ablaktábláján megjelenik az adott eszköz sablonjában létrehozott eszközök listája. Válasszon ki egy egyéni eszközt az eszköz adatai lap megjelenítéséhez:

    ![Eszköz részletei lap](./media/howto-manage-devices/devicelist.png)


## <a name="add-a-device"></a>Eszköz hozzáadása

Eszköz hozzáadása az Azure IoT Central-alkalmazáshoz:

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán.

1. Válassza ki azt az eszközt, amelyből eszközt szeretne létrehozni.

1. Válassza az + **új**lehetőséget.

1. A **szimulált** váltógomb be- **vagy** **kikapcsolása**. Egy valós eszköz az Azure IoT Central-alkalmazáshoz csatlakoztatott fizikai eszközhöz. Egy szimulált eszközön az Azure IoT Central által létrehozott mintaadatok láthatók.

1. Kattintson a  **Create** (Létrehozás) gombra.

1. Ez az eszköz most megjelenik az eszköz listájában ehhez a sablonhoz. Válassza ki az eszközt az eszköz részletei lap megjelenítéséhez, amely az eszköz összes nézetét tartalmazza.

## <a name="import-devices"></a>Eszközök importálása

Ha nagy számú eszközt szeretne összekötni az alkalmazással, tömegesen importálhatja az eszközöket egy CSV-fájlból. A CSV-fájlnak a következő oszlopokkal és fejlécekkel kell rendelkeznie:

* **IOTC_DeviceID** – az eszköz azonosítójának minden kisbetűnek kell lennie.
* **IOTC_DeviceName** – ez az oszlop nem kötelező.

Eszközök tömeges regisztrálása az alkalmazásban:

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán.

1. A bal oldali panelen válassza ki azt az eszközt, amelyhez tömegesen szeretné létrehozni az eszközöket.

    > [!NOTE]
    > Ha még nincs telepítve az eszközön, akkor a **minden eszköz** területen importálhatja az eszközöket, és sablon nélkül regisztrálhatja őket. Az eszközök importálása után áttelepítheti azokat egy sablonba.

1. Kattintson az **Importálás** gombra.

    ![Importálási művelet](./media/howto-manage-devices/bulkimport1a.png)


1. Válassza ki az importálni kívánt eszköz-azonosítókat tartalmazó CSV-fájlt.

1. Az eszköz importálása a fájl feltöltése után elindul. Az importálási állapotot az eszköz operatív paneljén követheti nyomon. Ez a panel automatikusan megjelenik az importálás megkezdése után, vagy a jobb felső sarokban található harang ikonra kattintva érheti el.

1. Miután az importálás befejeződik, megjelenik egy sikeres üzenet az eszköz működési paneljén.

    ![Sikeres importálás](./media/howto-manage-devices/bulkimport3a.png)


Ha az eszköz importálási művelete meghiúsul, hibaüzenet jelenik meg az eszköz működési paneljén. Egy naplófájl rögzíti az összes hibát, amelyet letöltheti.

**Eszközök áttelepítése sablonba**

Ha az eszközök **minden**eszköz alatt történő importálásával regisztrálja az eszközöket, akkor a rendszer az eszközöket sablon társítása nélkül hozza létre. Az eszközöket hozzá kell rendelni egy sablonhoz az eszköz adatainak és egyéb adatainak megismeréséhez. Kövesse az alábbi lépéseket az eszközök sablonhoz való hozzárendeléséhez:

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán.

1. A bal oldali panelen válassza a **minden eszköz**lehetőséget:

    ![Nem társított eszközök](./media/howto-manage-devices/unassociateddevices1a.png)


1. A rácson lévő szűrő használatával állapítsa meg, hogy az **eszköz sablon** oszlopában szereplő érték "nem társított"-e az eszközökhöz.

1. Válassza ki a sablonnal társítandó eszközöket:

1. **Áttelepítés**kiválasztása:

    ![Eszközök hozzárendelése](./media/howto-manage-devices/unassociateddevices2a.png)


1. Válassza ki a sablont az elérhető sablonok listájából, majd válassza az **áttelepítés**lehetőséget.

1. A kiválasztott eszközök társítva vannak a választott eszköz sablonnal.


## <a name="export-devices"></a>Eszközök exportálása

Ha egy valós eszközt szeretne csatlakoztatni a IoT Centralhoz, szüksége lesz a kapcsolati karakterláncra. Az eszköz adatainak tömeges exportálásával lekérheti az eszköz kapcsolati karakterláncok létrehozásához szükséges információkat. Az exportálási folyamat létrehoz egy CSV-fájlt az eszköz identitásával, az eszköz nevével és kulcsaival az összes kiválasztott eszközhöz.

Eszközök tömeges exportálása az alkalmazásból:

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán.

1. A bal oldali panelen válassza ki azt az eszközt, amelyről exportálni szeretné az eszközöket.

1. Válassza ki az exportálni kívánt eszközöket, majd válassza ki az **Exportálás** műveletet.

    ![Exportálás](./media/howto-manage-devices/export1a.png)


1. Elindul az exportálási folyamat. Az állapotot az eszköz működési paneljén követheti nyomon.

1. Ha az Exportálás befejeződik, megjelenik egy üzenet, amely a generált fájl letöltésére szolgáló hivatkozással együtt jelenik meg.

1. A fájl **letöltése** hivatkozásra kattintva letöltheti a fájlt a lemez helyi mappájába.

    ![Sikeres exportálás](./media/howto-manage-devices/export2a.png)


1. Az exportált CSV-fájl a következő oszlopokat tartalmazza: eszköz azonosítója, eszköznév, eszköz kulcsa és X509 tanúsítvány ujjlenyomatai megfelelnek:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

További információ a kapcsolati karakterláncokról és a valódi eszközök IoT Central alkalmazáshoz való csatlakoztatásáról: [Device connectivity in Azure IoT Central](overview-iot-central-get-connected.md).

## <a name="delete-a-device"></a>Eszköz törlése

Valós vagy szimulált eszköz törlése az Azure IoT Central alkalmazásból:

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán.

1. Válassza ki a törölni kívánt eszköz eszközének sablonját.

1. Az eszközök szűréséhez és kereséséhez használja a szűrő eszközöket. Jelölje be a törölni kívánt eszközök melletti jelölőnégyzetet.

1. Válassza a **Törlés** elemet. A törlés állapotát az eszköz operatív paneljén követheti nyomon.

## <a name="change-a-property"></a>Tulajdonság módosítása

A felhő tulajdonságai az eszközhöz társított eszköz-metaadatok, például a város és a sorozatszám. Az írható tulajdonságok vezérlik az eszköz viselkedését. Más szóval lehetővé teszik a bemenetek megadását az eszközön.  Az eszköz tulajdonságait az eszköz állítja be, és a IoT Centralon belül csak olvasható. Az eszköz **részletes** nézetében megtekintheti és frissítheti a tulajdonságokat.

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán.

1. Válassza ki annak az eszköznek a sablonját, amelynek tulajdonságait módosítani kívánja, majd válassza ki a kívánt eszközt.

1. Válassza ki azt a nézetet, amely az eszköz tulajdonságait tartalmazza, ez a nézet lehetővé teszi az értékek bevitelét és a **Mentés** lehetőséget a lap tetején. Itt láthatja az eszköz tulajdonságait és aktuális értékeit. A felhő tulajdonságai és az írható tulajdonságok szerkeszthető mezőkkel rendelkeznek, az eszköz tulajdonságai pedig csak olvashatók. Az írható tulajdonságok esetében a mező alján láthatja a szinkronizálási állapotukat. 

1. Módosítsa a tulajdonságokat a szükséges értékekre. Egyszerre több tulajdonságot is módosíthat, és egyszerre frissítheti őket.

1. Válassza a **Mentés** elemet. Ha az írható tulajdonságokat mentette, az értékeket a rendszer elküldi az eszközre. Amikor az eszköz megerősíti az írható tulajdonság módosítását, az állapot visszakerül a **szinkronizált**értékre. Ha mentett egy Felhőbeli tulajdonságot, az érték frissül.


## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan felügyelheti az eszközöket az Azure IoT Central alkalmazásban, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Az erőforráscsoportok használata](tutorial-use-device-groups.md)

<!-- Next how-tos in the sequence -->
