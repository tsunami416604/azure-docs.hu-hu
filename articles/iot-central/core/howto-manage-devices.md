---
title: Az Azure IoT Central alkalmazásban lévő eszközök kezelése | Microsoft Docs
description: Operátorként megismerheti, hogyan kezelheti az eszközöket az Azure IoT Central alkalmazásban.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: adcaa29ed894f2d61871f467369bcdd05f8cc593
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73601591"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Az Azure IoT Central-alkalmazásban lévő eszközök kezelése

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Ez a cikk azt ismerteti, hogyan felügyelheti az eszközöket az Azure IoT Central alkalmazásban. Operátorként a következőket teheti:

- Az **eszközök** lapon megtekintheti, hozzáadhatja és törölheti az Azure IoT Central-alkalmazáshoz csatlakoztatott eszközöket.
- Naprakész leltárt készíthet az eszközökről.
- Tartsa naprakészen az eszköz metaadatait az eszköz tulajdonságaiban tárolt értékek módosításával.
- Az eszközök viselkedésének szabályozása egy adott eszköz **beállításainak a beállítások** lapról történő frissítésével.

## <a name="view-your-devices"></a>Eszközök megtekintése

Egy adott eszköz megtekintése:

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán. Itt láthatja az [eszköz sablonjainak](howto-set-up-template.md)listáját.

1. Válassza ki az eszköz sablonját a **sablonok** listájában.

1. Az **eszközök** lap jobb oldali ablaktábláján megjelenik az adott eszköz sablonjában létrehozott eszközök listája. Válasszon ki egy egyéni eszközt az eszköz adatai lap megjelenítéséhez:

    ![Eszköz részletei lap](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Eszköz hozzáadása

Eszköz hozzáadása az Azure IoT Central-alkalmazáshoz:

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán.

1. Válassza ki azt az eszközt, amelyből eszközt szeretne létrehozni.

1. Válassza az + **új**lehetőséget.

1. Válassza a **Real** vagy a **szimulált**elemet. Egy valós eszköz az Azure IoT Central-alkalmazáshoz csatlakoztatott fizikai eszközhöz. Egy szimulált eszközön az Azure IoT Central által létrehozott mintaadatok láthatók.

## <a name="import-devices"></a>Eszközök importálása

Ha nagy számú eszközt szeretne összekötni az alkalmazással, tömegesen importálhatja az eszközöket egy CSV-fájlból. A CSV-fájlnak a következő oszlopokkal és fejlécekkel kell rendelkeznie:

* **IOTC_DeviceID** – az eszköz azonosítójának minden kisbetűsnek kell lennie.
* **IOTC_DeviceName** – ez az oszlop nem kötelező.

Eszközök tömeges regisztrálása az alkalmazásban:

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán.

1. A bal oldali panelen válassza ki azt az eszközt, amelyhez tömegesen szeretné létrehozni az eszközöket.

    > [!NOTE]
    > Ha még nem rendelkezik sablonnal, akkor a nem **társított eszközök** területen importálhatja az eszközöket, és sablon nélkül regisztrálhatja őket. Az eszközök importálása után hozzárendelheti őket egy sablonhoz.

1. Válassza az **Importálás**lehetőséget.

    ![Importálási művelet](./media/howto-manage-devices/bulkimport1a.png)

1. Válassza ki az importálni kívánt eszköz-azonosítókat tartalmazó CSV-fájlt.

1. Az eszköz importálása a fájl feltöltése után elindul. Az importálási állapotot az eszköz rácsának tetején követheti nyomon.

1. Miután az importálás befejeződik, megjelenik egy sikeres üzenet az eszköz rácsán.

    ![Sikeres importálás](./media/howto-manage-devices/bulkimport3a.png)

Ha az eszköz importálási művelete meghiúsul, hibaüzenet jelenik meg az eszköz rácsán. Egy naplófájl rögzíti az összes hibát, amelyet letöltheti.

**Eszközök társítása sablonnal**

Ha regisztrálja az eszközöket az importálás elindításával a nem **társított eszközök**területen, az eszközök létrehozása nem minden sablon társítása nélkül történik. Az eszközöket hozzá kell rendelni egy sablonhoz az eszköz adatainak és egyéb adatainak megismeréséhez. Kövesse az alábbi lépéseket az eszközök sablonhoz való hozzárendeléséhez:

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán.

1. A bal oldali panelen válassza a nem **társított eszközök**elemet:

    ![Nem társított eszközök](./media/howto-manage-devices/unassociateddevices1a.png)

1. Válassza ki a sablonnal társítandó eszközöket:

1. **Hozzárendelés**kiválasztása:

    ![Eszközök hozzárendelése](./media/howto-manage-devices/unassociateddevices2a.png)

1. Válassza ki a sablont az elérhető sablonok listájából, majd válassza a **hozzárendelés**lehetőséget.

1. A kiválasztott eszközök társítva vannak a választott eszköz sablonnal.

> [!NOTE]
> Miután egy eszköz társítva lett egy sablonhoz, nem társítható vagy nem társítható másik sablonhoz.

## <a name="export-devices"></a>Eszközök exportálása

Ha egy valós eszközt szeretne csatlakoztatni a IoT Centralhoz, szüksége lesz a kapcsolati karakterláncra. Az eszköz adatainak tömeges exportálásával lekérheti az eszköz kapcsolati karakterláncok létrehozásához szükséges információkat. Az exportálási folyamat létrehoz egy CSV-fájlt az eszköz identitásával, az eszköz nevével és kulcsaival az összes kiválasztott eszközhöz.

Eszközök tömeges exportálása az alkalmazásból:

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán.

1. A bal oldali panelen válassza ki azt az eszközt, amelyről exportálni szeretné az eszközöket.

1. Válassza ki az exportálni kívánt eszközöket, majd válassza ki az **Exportálás** műveletet.

    ![Exportálás](./media/howto-manage-devices/export1a.png)

1. Elindul az exportálási folyamat. A rács felső részén nyomon követheti az állapotot.

1. Ha az Exportálás befejeződik, megjelenik egy üzenet, amely a generált fájl letöltésére szolgáló hivatkozással együtt jelenik meg.

1. Válassza ki a **sikert jelző üzenetet** , és töltse le a fájlt a lemez helyi mappájába.

    ![Sikeres exportálás](./media/howto-manage-devices/export2a.png)

1. Az exportált CSV-fájl a következő oszlopokat tartalmazza: eszköz azonosítója, eszköznév, eszköz kulcsa és X509 tanúsítvány ujjlenyomatai megfelelnek:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Lásd: az eszköz csatlakoztatása [Az Azure IoT Centralban](concepts-connectivity.md), további információ a kapcsolati karakterláncokról és a valós eszközök csatlakoztatásáról a IoT Central alkalmazáshoz.

## <a name="delete-a-device"></a>Eszköz törlése

Valós vagy szimulált eszköz törlése az Azure IoT Central alkalmazásból:

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán.

1. Válassza ki a törölni kívánt eszköz eszközének sablonját.

1. Jelölje be a törölni kívánt eszköz melletti jelölőnégyzetet.

1. Válassza a **Törlés**lehetőséget.

## <a name="change-a-device-setting"></a>Eszközbeállítások módosítása

A beállítások vezérlik az eszköz viselkedését. Más szóval lehetővé teszik a bemenetek megadását az eszközön. Az eszközbeállítások lapon megtekintheti és frissítheti **az eszköz beállításait** .

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán.

1. Válassza ki annak az eszköznek a sablonját, amelynek a beállításait módosítani szeretné.

1. Válassza a **Beállítások** lapot. Itt láthatja az eszköz összes beállítását és a jelenlegi értékeit. Minden beállításnál láthatja, hogy az eszköz még mindig szinkronizálva van-e.

1. Módosítsa a beállításokat a szükséges értékekre. Egyszerre több beállítást is módosíthat, és egyszerre frissítheti őket.

1. Válassza a **frissítés**lehetőséget. Az értékeket a rendszer elküldi az eszközre. Amikor az eszköz megerősíti a beállítás módosítását, a beállítás állapota **szinkronizálva**állapotba kerül.

## <a name="change-a-property"></a>Tulajdonság módosítása

A tulajdonságok az eszközhöz társított eszköz-metaadatok, például a város és a sorozatszám. Az **eszköz részletei** lapon megtekintheti és frissítheti a tulajdonságokat.

1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán.

1. Válassza ki annak az eszköznek a sablonját, amelynek a tulajdonságait módosítani szeretné.

1. Válassza a **Tulajdonságok** fület, ahol az összes tulajdonság megjelenik.

1. Módosítsa az alkalmazás tulajdonságait a szükséges értékekre. Egyszerre több tulajdonságot is módosíthat, és egyszerre frissítheti őket. Válassza a **frissítés**lehetőséget.

> [!NOTE]
> Az _eszköz tulajdonságainak_értéke nem módosítható. Az eszköz tulajdonságait az eszköz állítja be, és csak olvasható az Azure IoT Central alkalmazásban.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan felügyelheti az eszközöket az Azure IoT Central alkalmazásban, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Az eszközök készletének használata](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
