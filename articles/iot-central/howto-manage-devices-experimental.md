---
title: Az Azure IoT Central-alkalmazást az eszközök felügyeletére |} A Microsoft Docs
description: Kezelőként megtudhatja, hogyan kezelheti eszközeit az Azure IoT Central alkalmazáshoz.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 5e423478e894487795b376340b13155950b4ef8d
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667044"
---
# <a name="manage-devices-in-your-azure-iot-central-application-new-ui-design"></a>Az Azure IoT Central application (új felhasználói felület tervezése) lévő mobileszközök felügyelete

Ez a cikk azt ismerteti, hogyan kezelőként Eszközkezelés az Azure IoT Central alkalmazáshoz. Kezelőként a következőket teheti:

- Használja a **Device Explorer** megtekintése, hozzáadása és törlése az Azure IoT Central alkalmazásnak csatlakoztatott eszközök lap.
- Az eszköz naprakész készletnyilvántartás.
- Az eszköz metaadatait az eszköztulajdonságok tárolt értékek módosításával tartsa naprakészen a.
- Az eszközök viselkedését vezérlő egy beállítást egy adott eszköz frissítésével a **beállítások** lapot.

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="view-your-devices"></a>Eszközök megtekintése

Egy adott eszköz megtekintése:

1. Válasszon **Device Explorer** a bal oldali navigációs menüben. Itt láthatja az listáját a [eszközsablonok](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

1. Az eszköz sablon kiválasztása a **sablonok** listája.

1. A jobb oldali ablaktáblán, a **Device Explorer** lapon láthatja az adott eszköz a sablonból létrehozott eszköz listáját. Válasszon egy-egy eszköz tekintse meg az eszköz részleteit tartalmazó oldalra az eszközön:

    ![Eszköz részleteit tartalmazó oldalra](./media/howto-manage-devices-experimental/devicelist.png)

## <a name="add-a-device"></a>Eszköz hozzáadása

Eszköz hozzáadása az Azure IoT Central alkalmazáshoz:

1. Válasszon **Device Explorer** a bal oldali navigációs menüben.

1. Válassza ki, amelyről szeretne létrehozni egy eszközt a device sablont.

1. Válassza a + **új**.

1. Válasszon **valós** vagy **szimulált**. Egy igazi eszközön van egy fizikai eszköz, az Azure IoT Central alkalmazáshoz csatlakozik. Szimulált eszköz által az Azure IoT Central az Ön számára létrehozott mintaadatokat tartalmazza.

## <a name="import-devices"></a>Eszközök importálása

Nagy mennyiségű eszköz csatlakozhat az alkalmazáshoz, tömegesen eszközök importálása CSV-fájlból. A CSV-fájlt a következő oszlopok és a fejlécek kell rendelkeznie:

* **IOTC_DeviceID** – az eszköz azonosítója kisbetűkből kell lennie.
* **IOTC_DeviceName** – Ez az oszlop nem kötelező.

Az alkalmazás eszközök tömeges-regisztrálni:

1. Válasszon **Device Explorer** a bal oldali navigációs menüben.

1. A bal oldali panelen válassza az eszköz sablon, amely a tömeges szeretné létrehozni az eszközök.

    > [!NOTE]
    > Ha egy eszköz sablon nem rendelkezik még importálhatja az eszközök területen **nincs társítva eszközök** , és regisztrálja őket a sablon nélkül. Miután eszközök lettek importálva, majd társíthatja őket egy sablont.

1. Kattintson az **Importálás** gombra.

    ![Importálási művelet](./media/howto-manage-devices-experimental/BulkImport1.png)

1. Válassza ki a CSV-fájl, amely az importálni kívánt eszközazonosítókat listája szerepel.

1. Eszközök importálása akkor indul, amikor a fájl fel lett töltve. Az importálás állapotát az eszköz rács tetején követheti nyomon.

1. Az importálás befejezése után az eszköz rács sikert jelző üzenet jelenik meg.

    ![Sikeres importálás](./media/howto-manage-devices-experimental/BulkImport3.png)

Ha az eszköz importálja a művelet sikertelen, egy hibaüzenet jelenik meg az eszköz rács. A hibákat a rögzítés log fájl jön létre, hogy letöltheti.

**A sablon eszközök társítása**

Eszközök regisztrálása az importálás alatt elindításával **nincs társítva eszközök**, akkor az eszközök bármilyen eszköztársítás sablon nélkül jön létre. Eszközök fedezheti fel az adatokat és egyéb adatait az eszköz egy sablonhoz társított kell lennie. Kövesse az alábbi lépéseket eszközök társítása egy sablont:

1. Válasszon **Device Explorer** a bal oldali navigációs menüben.

1. Válassza a bal oldali panelen, **nincs társítva eszközök**:

    ![Társítatlan eszközök](./media/howto-manage-devices-experimental/UnassociatedDevices1.png)

1. A sablon társítani kívánt eszközök kiválasztása:

1. Kattintson a **társítása**:

    ![Eszközök társítása](./media/howto-manage-devices-experimental/UnassociatedDevices2.png)

1. Válassza ki a sablont a listából a rendelkezésre álló sablonok, és kattintson a **társítása** gombra.

1. A kiválasztott eszközök társítva a kiválasztott eszköz sablont.

> [!NOTE]
> Ha egy eszköz már egy sablonhoz társított nem lehet nincs társítva és hozzárendelt egy másik sablonnal.

## <a name="export-devices"></a>Eszközök exportálása

Egy valós eszköz csatlakoztatása az IoT-központ, a kapcsolati karakterláncot kell. Exportálhatja az eszközadatok tömeges kapcsolati karakterláncok és egyéb tulajdonságokat. Az exportálási folyamat létrehoz egy CSV-fájlt az eszközidentitás, eszköz nevét és minden kiválasztott eszköz elsődleges kapcsolati karakterláncára.

Tömeges exportálása eszközök az alkalmazásból:

1. Válasszon **Device Explorer** a bal oldali navigációs menüben.

1. A bal oldali panelen válassza ki, amelyről szeretné az eszköz exportálása eszköz sablont.

1. Jelölje ki az eszközöket, amelyeket szeretne exportálni, majd kattintson a **exportálása** művelet.

    ![Exportálás](./media/howto-manage-devices-experimental/Export1.png)

1. Az exportálási folyamat elindul. A rács tetején állapotát nyomon követéséhez.

1. Az Exportálás befejeződése után a sikert jelző üzenet jelenik meg és a egy hivatkozás a létrehozott fájl letöltéséhez.

1. Kattintson a **sikert jelző üzenettel** letölteni a fájlt egy helyi mappába a lemezen.

    ![Sikeres exportálása](./media/howto-manage-devices-experimental/Export2.png)

1. Az exportált CSV-fájlt a következő oszlopokat tartalmazza: eszköz azonosítója, az eszköz neve, a eszközkulcsok és a X509 tanúsítvány-ujjlenyomatok:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY   
    * IOTC_X509THUMBPRINT_SECONDARY

## <a name="delete-a-device"></a>Eszköz törlése

Akár egy valós vagy szimulált eszköz törlése az Azure IoT Central alkalmazásból:

1. Válasszon **Device Explorer** a navigációs menüben.

1. Válassza ki a törölni kívánt eszköz eszköz sablont.

1. Törli az eszköz melletti jelölőnégyzetet.

1. Válasszon **törlése**.

## <a name="change-a-device-setting"></a>Egy eszköz beállításának módosítása

Beállítások az eszközök viselkedését vezérli. Más szóval lehetővé teszik, hogy az eszköz a forráson. Megtekintheti, és az eszköz beállításait frissíteni a **eszközadatok** lapot.

1. Válasszon **Device Explorer** a navigációs menüben.

1. Válassza ki az eszközt, amelynek beállításait módosítani szeretné az eszköz sablonját.

1. Válassza ki a **beállítások** fülre. Itt láthatja az eszköz rendelkezik az összes beállítás és azok aktuális értékével. Mindegyik beállításnál láthatja, ha az eszköz továbbra is szinkronizálása folyamatban van.

1. Módosítsa a beállításait, és a szükséges értékeket. Egyszerre több beállítást módosíthatja, és frissíteni őket az összes egyszerre.

1. Válasszon **frissítés**. Az eszköz az értékeket érkeznek. Ha az eszköz ellenőrzi beállítás változása, a beállítás állapota visszatér arra **szinkronizált**.

## <a name="change-a-property"></a>Egy tulajdonság módosítása

A tulajdonságok akkor, az eszköz metaadatait, például az városa és sorozatszám az eszközhöz társított. Megtekintheti és tulajdonságainak frissítése az a **eszközadatok** lapot.

1. Válasszon **Device Explorer** navigációs menüben.

1. Válassza ki az eszközt, amelynek tulajdonságait meg szeretné változtatni az eszköz sablonját.

1. Válassza ki a **tulajdonságok** lapra, ahol láthatja az összes tulajdonság.

1. Az alkalmazás az értékekre szüksége vonatkozó tulajdonságok módosítása Egyszerre több tulajdonságainak módosítása, és frissítse azokat az összes egyszerre. Válasszon **frissítés**.

> [!NOTE]
> Az érték nem módosítható _eszköztulajdonságok_. Eszköztulajdonságok által az eszközön, és az Azure IoT Central alkalmazáshoz csak olvashatók.

## <a name="next-steps"></a>További lépések

Most, hogy bemutattuk, hogyan lehet az Azure IoT Central alkalmazáshoz lévő eszközök kezeléséhez, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Eszköz-csoportok használata](howto-use-device-sets.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)

<!-- Next how-tos in the sequence -->
