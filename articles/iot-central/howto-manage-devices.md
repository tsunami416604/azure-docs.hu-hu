---
title: Az Azure IoT központi alkalmazás lévő mobileszközök felügyelete |} Microsoft Docs
description: Kezelőként megtudhatja, hogyan kezelheti az Azure IoT központi alkalmazás eszközöket.
services: iot-central
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 75472d701160e7cfd331d01efcdc1a19ae20fb2d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Az Azure IoT központi alkalmazásban eszközök kezelése

Ez a cikk ismerteti, hogyan kezelőként, a Microsoft Azure IoT központi alkalmazás lévő eszközök kezeléséhez. Kezelőként a következő műveletek végezhetők el:

- Használja a **Explorer** lapon lehet megtekinteni, adja hozzá, és az Azure IoT központi alkalmazáshoz kapcsolódó eszközök törlése.
- Az eszközök naprakészen készletnyilvántartás.
- Telepítheti az eszköz metaadatait a eszköztulajdonságok tárolt értékek módosításával.
- Felügyeli az eszközöket az azáltal, hogy egy adott eszközről beállítást frissíti a **beállítások** lap.

## <a name="view-your-devices"></a>Az eszközök megtekintése

Az egyes eszközök megtekintése:

1. Válasszon **Explorer** a bal oldali navigációs menü. Itt láthatja listáját a [eszköz sablonok](howto-set-up-template.md).

1. Válasszon egy **eszköz sablon** a bal oldali ablaktáblán.

1. A jobb oldali ablaktáblában tekintse meg az adott eszköz a sablonból létrehozott eszközök listáját. Válassza ki az egyes eszközt, és ellenőrizze a **eszközadatok** lap az eszköznek:

    [![Eszköz részleteit megjelenítő oldalra](./media/howto-manage-devices/image1.png)](./media/howto-manage-devices/image1.png#lightbox)

## <a name="add-a-device"></a>Eszköz hozzáadása

Eszköz hozzáadása az Azure IoT központi alkalmazáshoz:

1. Válasszon **Explorer** a bal oldali navigációs menü.

1. Válassza ki az eszköz sablon, amelyből el kívánja létrehozni egy eszközt.

1. Válasszon + **új**.

1. Válasszon **valós** vagy **szimulált**. Egy valódi eszköz van egy fizikai eszköz számára, hogy az Azure IoT központi alkalmazás kapcsolódni. A szimulált eszköz állítja elő az Ön Azure IoT központi minta adatokat tartalmaz. A példa egy valós eszközt. Válasszon **valós** lehetőségre, és navigáljon a **eszközadatok** lap az új eszközhöz.


## <a name="bulk-import-devices"></a>Eszközök tömeges importálással

Nagy számú eszköz kapcsolódni az alkalmazáshoz, Azure IoT központi ajánlatok tömeges importálását eszközök keresztüli CSV-fájl. 

CSV-fájl követelmények:
1. A CSV-fájl csak egy eszköz azonosítóit tartalmazó oszlop kell rendelkeznie.

1. A fájl nem rendelkezhet minden fejléc.


A tömeges-regisztráció eszközök az alkalmazásban:

1. Válasszon **Explorer** a bal oldali navigációs menü.

1. A bal oldali panelen válassza ki a tömeges kívánt eszköz sablon létrehozása az eszközök.

1. Válasszon **új** válassza **tömeges importálással**.

    [![Tömeges importálási művelet](./media/howto-manage-devices/BulkImport1.png)](./media/howto-manage-devices/BulkImport1.png#lightbox)

1. Válassza ki a CSV-fájlt, amely rendelkezik a eszközazonosítókat, importálandók listáját.

1. Eszköz importálási követően a fájl fel lett töltve indul. Követheti, hogy az importálás állapotát az eszköz rács tetején.

1. Az importálás után egy üzenetet az eszköz rács látható.

    [![Tömeges importálása sikeres](./media/howto-manage-devices/BulkImport3.png)](./media/howto-manage-devices/BulkImport3.png#lightbox)

Ha az eszköz importálja a művelet sikertelen, hiba történt az eszköz rács látható lesz. A naplófájl a hibákat a rögzítés jön létre, és a hibaüzenet a következő kattintva tölthető le.



## <a name="delete-a-device"></a>Eszköz törlése

Akár egy valós vagy szimulált eszköz törlése az Azure IoT központi alkalmazásból:

1. Válasszon **Explorer** a navigációs menü.

1. Válassza ki a eszköz sablont törli az eszköz.

1. Törli az eszköz melletti négyzetet.

1. Válasszon **törlése**.

## <a name="change-a-device-setting"></a>Egy eszköz beállításának módosítása

Beállítások egy eszköz felügyeli. Más szóval lehetővé teszik, hogy adja meg a bemeneti adatok az eszközön. Megtekintheti és az eszköz beállítások frissítése a **eszközadatok** lap.

1. Válasszon **Explorer** a navigációs menü.

1. Válassza ki az eszköz, amelynek beállításait módosítani szeretné az eszköz sablont.

1. Válassza ki a **beállítások** fülre. Itt láthatja az eszköz rendelkezik az összes beállítás és a jelenlegi értékeit. Az egyes beállítások, láthatja, ha az eszköz továbbra is szinkronizál.

1. Módosítsa a beállításokat a kívánt értékeket. Egyszerre több beállításainak módosítása, és frissítse azokat ugyanabban az időpontban.

1. Válasszon **frissítés**. Az eszköz az értékeket kell küldeni. Ha az eszköz elfogadja a beállítás módosítása, a beállítási állapot visszatér **szinkronizált**.

## <a name="change-a-property"></a>A tulajdonság módosítása

A rendszer az eszköz metaadatait az eszköz, például a város és sorozatszám társított tulajdonságokat. Megtekintheti és a tulajdonságok frissítése a **eszközadatok** lap.

1. Válasszon **Explorer** navigációs menü.

1. Válassza ki az eszköz, amelynek a tulajdonságait módosítani szeretné az eszköz sablont.

1. Válassza ki a **tulajdonságok** válaszoknál láthatja a Tulajdonságok lapon.

1. Módosítsa a tulajdonságait és a kívánt értékeket. Több tulajdonságra egyszerre módosíthatja és frissítheti az összes egyszerre.

1. Válasszon **frissítés**.

> [!NOTE]
> Az érték nem módosítható _eszköztulajdonságok_. Eszköztulajdonságok az eszköz-beállításokat, és az Azure IoT központi alkalmazásban csak olvashatók.

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik megismerte az Azure IoT központi alkalmazásban eszközkezelés, ez a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Eszköz készlet használata](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
