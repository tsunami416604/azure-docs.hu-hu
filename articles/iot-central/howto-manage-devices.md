---
title: Az Azure IoT Central-alkalmazást az eszközök felügyeletére |} A Microsoft Docs
description: Kezelőként megtudhatja, hogyan kezelheti eszközeit az Azure IoT Central alkalmazáshoz.
author: ellenfosborne
ms.author: elfarber
ms.date: 11/02/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 22158d6a5a8c2768ee343f86160f79fc0dbd241b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456256"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Eszközök kezelése az Azure IoT Central alkalmazáshoz a

Ez a cikk azt ismerteti, hogyan kezelőként Eszközkezelés az Azure IoT Central alkalmazáshoz. Kezelőként a következőket teheti:

- Használja a **Device Explorer** megtekintése, hozzáadása és törlése az Azure IoT Central alkalmazásnak csatlakoztatott eszközök lap.
- Az eszköz naprakész készletnyilvántartás.
- Az eszköz metaadatait az eszköztulajdonságok tárolt értékek módosításával tartsa naprakészen a.
- Az eszközök viselkedését vezérlő egy beállítást egy adott eszköz frissítésével a **beállítások** lapot.

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="view-your-devices"></a>Eszközök megtekintése

Egy adott eszköz megtekintése:

1. Válasszon **Device Explorer** a bal oldali navigációs menüben. Itt láthatja az listáját a [eszközsablonok](howto-set-up-template.md).

1. Válasszon ki egy eszközt sablont, a sablonok a bal oldali ablaktábla.

1. A jobb oldali ablaktáblán a Device Explorer lap tekintse meg az alább látható módon, hogy eszköz sablon alapján létrehozott eszköz listáját. Válasszon egy-egy eszköz tekintse meg az eszköz részleteit tartalmazó oldalra az eszközön:

    [![Eszköz részleteit tartalmazó oldalra](./media/howto-manage-devices/image1.png)](./media/howto-manage-devices/image1.png#lightbox)

## <a name="add-a-device"></a>Eszköz hozzáadása

Eszköz hozzáadása az Azure IoT Central alkalmazáshoz:

1. Válasszon **Device Explorer** a bal oldali navigációs menüben.

1. Válassza ki, amelyről szeretne létrehozni egy eszközt a device sablont.

1. Válassza a + **új**.

1. Válasszon **valós** vagy **szimulált**. Egy igazi eszközön van egy fizikai eszköz, az Azure IoT Central alkalmazáshoz csatlakozik. Szimulált eszköz által az Azure IoT Central az Ön számára létrehozott mintaadatokat tartalmazza. Ez a példa egy igazi eszközön használja. Válassza a **valós** navigálhat a **eszközadatok** lap az új eszköz.


## <a name="import-devices"></a>Eszközök importálása

A nagy mennyiségű eszköz csatlakoztatása az Azure IoT Central alkalmazásnak ajánlatok tömeges importáló eszközöket egy CSV-fájlt. A CSV-fájlt a következő oszlopokat (és a fejlécek) kell rendelkeznie.
1.  IOTC_DeviceID  **<span style="color:Red">(a kis kell lennie)</span>**
1.  IOTC_DeviceName (nem kötelező)


Az alkalmazás eszközök tömeges-regisztrálni:

1. Válasszon **Device Explorer** a bal oldali navigációs menüben.

1. A bal oldali panelen válassza az eszköz sablon, amely a tömeges szeretné létrehozni az eszközök.

 >   [!NOTE] 
    Ha egy eszköz sablon nem rendelkezik még importálhatja az eszközök területen **nincs társítva eszközök** , és regisztrálja őket minden olyan sablon nélkül. Eszközök importálása után majd társíthatja őket egy sablont egy későbbi lépésben.

1. Válassza ki **importálás**.

    [![Importálási művelet](./media/howto-manage-devices/BulkImport1.png)](./media/howto-manage-devices/BulkImport1.png#lightbox)

1. Válassza ki a CSV-fájl, amely az importálni kívánt eszközazonosítókat listája szerepel.

1. Eszközök importálása akkor indul, amikor a fájl fel lett töltve. Az importálás állapotát az eszköz rács tetején követheti nyomon.

1. Az importálás befejezése után az eszköz rács sikert jelző üzenet jelenik meg.

    [![Sikeres importálás](./media/howto-manage-devices/BulkImport3.png)](./media/howto-manage-devices/BulkImport3.png#lightbox)

Ha az eszköz importálja a művelet sikertelen lesz, látni fogja hibaüzenet jelenik meg az eszköz rács. A naplófájl a hibákat a rögzítés jön létre, és a hibaüzenet kiválasztásával tölthető le.


**A sablon eszközök társítása**

Eszközök regisztrálása az importálás alatt elindításával **nincs társítva eszközök**, akkor az eszközök bármilyen eszköztársítás sablon nélkül jön létre. Eszköz egy sablont az adatokat és egyéb adatait az eszköz társítva kell lennie. Kövesse az alábbi lépéseket eszközök társítása egy sablont:
1. Válasszon **Device Explorer** a bal oldali navigációs menüben.
1. Válassza a bal oldali panelen, **nincs társítva eszközök**.
    [![Társítatlan eszközök](./media/howto-manage-devices/UnassociatedDevices1.png)](./media/howto-manage-devices/UnassociatedDevices1.png#lightbox)
1. Válassza ki a sablon társítani kívánt eszközöket.
1. Válassza ki **társítása** lehetőséget.
    [![Eszközök hozzárendelése](./media/howto-manage-devices/UnassociatedDevices2.png)](./media/howto-manage-devices/UnassociatedDevices2.png#lightbox)
1. A sablont a listából a rendelkezésre álló sablonok, és válasszon **társítása**.
1. A kiválasztott eszközök átkerül a megfelelő eszköz sablon alapján.

 >   [!NOTE] 
    Ha egy eszköz társítva egy sablont nem lehet nincs társítva vagy egy másik sablonnal társított.

## <a name="export-devices"></a>Eszközök exportálása

Üzembe helyezi az eszközök csatlakoztatása IoT-központ, szüksége lesz az eszköz IoT-központ által létrehozott a kapcsolati karakterláncot. Az exportálási szolgáltatás segítségével a kapcsolati karakterláncokkal és egyéb tulajdonságok, az eszközök tömeges lekérése az alkalmazásból. Exportálás hoz létre egy CSV-fájlt az Eszközidentitás, az eszköz neve és az elsődleges kapcsolati karakterlánc a kiválasztott eszközökhöz.

Tömeges exportálása eszközök az alkalmazásból:
1. Válasszon **Device Explorer** a bal oldali navigációs menüben.

1. A bal oldali panelen válassza ki, amelyhez az eszköz exportálása kívánt eszköz sablont.

1. Jelölje ki az eszközöket, amelyet szeretne exportálni, és válassza ki a **exportálása** művelet.

    [![Exportálás](./media/howto-manage-devices/Export1.png)](./media/howto-manage-devices/Export1.png#lightbox)

1. Exportálási folyamat elindul, és nyomon követheti a rács tetején állapotát. 

1. Az exportálás befejezése után a sikert jelző üzenet jelenik meg együtt egy hivatkozás a létrehozott fájl letöltéséhez.

1. Válassza ki a **sikert jelző üzenettel** letölteni a fájlt egy helyi mappába a lemezen.

    [![Sikeres exportálása](./media/howto-manage-devices/Export2.png)](./media/howto-manage-devices/Export2.png#lightbox)

1. Az exportált CSV-fájlt a következő oszlopok adatokat fog rendelkezni: **Eszköz azonosítója, eszköz, eszköz Priamry és másodlagos kulcsok és az elsődleges és másodlagos tanúsítvány-ujjlenyomatai**
    *   IOTC_DEVICEID
    *   IOTC_DEVICENAME
    *   IOTC_SASKEY_PRIMARY
    *   IOTC_SASKEY_SECONDARY
    *   IOTC_X509THUMBPRINT_PRIMARY 
    *   IOTC_X509THUMBPRINT_SECONDARY

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

1. Módosítsa a beállításait, és a kívánt értékeket. Egyszerre több beállításainak módosítása, és frissítse azokat az összes egyszerre.

1. Válasszon **frissítés**. Az eszköz az értékeket érkeznek. Az eszközön beállítás változása tudomásul veszi, ha a beállítás állapota visszatér arra **szinkronizált**.

## <a name="change-a-property"></a>Egy tulajdonság módosítása

A tulajdonságok akkor, az eszköz metaadatait, például az városa és sorozatszám az eszközhöz társított. Megtekintheti és tulajdonságainak frissítése az a **eszközadatok** lapot.

1. Válasszon **Device Explorer** navigációs menüben.

1. Válassza ki az eszközt, amelynek tulajdonságait meg szeretné változtatni az eszköz sablonját.

1. Válassza ki a **tulajdonságok** lapra, ahol láthatja az összes tulajdonság.

1. A kívánt értékeket az alkalmazás tulajdonságainak módosítása Egyszerre több tulajdonságainak módosítása, és frissítse azokat az összes egyszerre. Válasszon **frissítés**.

> [!NOTE]
> Az érték nem módosítható _eszköztulajdonságok_. Eszköztulajdonságok által az eszközön, és az Azure IoT Central alkalmazáshoz csak olvashatók.

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik megtanulta, hogyan kezelheti az Azure IoT Central alkalmazáshoz eszközeit, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Eszköz-csoportok használata](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
