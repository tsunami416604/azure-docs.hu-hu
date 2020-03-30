---
title: Modulok üzembe helyezése az Azure Portalról – Azure IoT Edge
description: Az IoT Hub az Azure Portalon egy IoT Edge-modul leküldése az IoT Hub az IoT Edge-eszköz, egy központi telepítési jegyzékben konfigurálva.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5c44561895bc1905328ec0eb357bee1c68a8eb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271446"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Azure IoT Edge-modulok üzembe helyezése az Azure Portalról

Miután létrehozta az IoT Edge-modulokat az üzleti logikával, szeretné telepíteni őket az eszközökre, hogy a peremhálózaton működjenek. Ha több modullal rendelkezik, amelyek együtt dolgoznak az adatok gyűjtésében és feldolgozásában, egyszerre telepítheti őket, és deklarálhatja az őket összekötő útválasztási szabályokat.

Ez a cikk bemutatja, hogyan az Azure Portalon végigvezeti a központi telepítési jegyzékfájl létrehozása és a központi telepítés leválasztása egy IoT Edge-eszköz. Az [IoT Edge-modulok üzembe helyezése és figyelése nagy méretekben](how-to-deploy-monitor.md)című témakörben talál további információt egy olyan központi telepítés létrehozásáról, amely több eszközt céloz meg a megosztott címkék alapján.

## <a name="prerequisites"></a>Előfeltételek

* Egy [IoT-központ](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésben.
* IoT [Edge-eszköz](how-to-register-device.md#register-in-the-azure-portal) az IoT Edge futásidejű telepítve.

## <a name="configure-a-deployment-manifest"></a>Központi telepítési jegyzékfájl konfigurálása

A központi telepítési jegyzékfájl egy JSON-dokumentum, amely leírja, hogy mely modulokat kell telepíteni, hogyan adatfolyamok a modulok között, és a modul twins kívánt tulajdonságait. A központi telepítési jegyzékek működéséről és létrehozásáról további információt [az IoT Edge-modulok használatba és újrafelhasználtkezelésének ismertetése](module-composition.md)című témakörben talál.

Az Azure Portalon van egy varázsló, amely végigvezeti a központi telepítési jegyzékfájl létrehozásában, ahelyett, hogy a JSON-dokumentum manuális létrehozása. Három lépésből áll: **Modulok hozzáadása**, **Útvonalak megadása**és A **telepítés áttekintése**.

### <a name="select-device-and-add-modules"></a>Eszköz kiválasztása és modulok hozzáadása

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg az IoT-központot.
1. A bal oldali ablaktáblában válassza a menü **IoT Edge parancsát.**
1. Kattintson a céleszköz azonosítójára az eszközök listájából.
1. A felső sávon válassza a **Modulok beállítása**lehetőséget.
1. A **lap Tároló beállításjegyzék-beállításai** szakaszában adja meg a hitelesítő adatokat a modullemezképeket tartalmazó privát tárolónyilvántartások eléréséhez.
1. A lap **IoT Edge Modulok** szakaszában válassza a **Hozzáadás lehetőséget.**
1. Tekintse meg a modulok típusait a legördülő menüből:

   * **IoT Edge module –** Adja meg a modul nevét és a tároló rendszerkép URI. Például a minta SimulatedTemperatureSensor modul `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`képURI-ja a . Ha a modullemezkép egy privát tároló beállításjegyzékében van tárolva, adja hozzá a lapon található hitelesítő adatokat a lemezkép eléréséhez.
   * **Marketplace-modul** – Az Azure Piactéren üzemeltetett modulok. Egyes piactéri modulok további konfigurációt igényelnek, ezért tekintse át a modul részleteit az [Azure Marketplace IoT Edge-modulok](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) listájában.
   * **Azure Stream Analytics-modul** – Az Azure Stream Analytics-számítási feladatokból létrehozott modulok.

1. A modul hozzáadása után válassza ki a modul nevét a listából a modulbeállítások megnyitásához. Szükség esetén töltse ki a választható mezőket. A tárolók létrehozásának beállításairól, az újraindítási házirendről és a kívánt állapotról az [EdgeAgent kívánt tulajdonságai](module-edgeagent-edgehub.md#edgeagent-desired-properties)című témakörben talál további információt. Az ikermodulról a [Kívánt tulajdonságok definiálása vagy frissítése](module-composition.md#define-or-update-desired-properties)című témakörben talál további információt.
1. Ha szükséges, ismételje meg az 5–8.
1. Válassza a **Tovább: Útvonalak lehetőséget** az útvonalak szakaszhoz való folytatáshoz.

### <a name="specify-routes"></a>Útvonalak megadása

Az **Útvonalak** lapon megadhatja, hogy az üzenetek hogyan továbbítódnak a modulok és az IoT Hub között. Az üzenetek név-érték párok használatával készülnek. Alapértelmezés szerint egy útvonal neve **útvonal** és a defined as **FROM /messages/\* INTO $upstream,** ami azt jelenti, hogy bármely modul által küldött üzeneteket az IoT hub.  

Adja hozzá vagy frissítse az útvonalakat az [útvonalak deklarálása](module-composition.md#declare-routes)szolgáltatásból származó információkkal, majd válassza a **Tovább: Véleményezés + létrehozás** lehetőséget a varázsló következő lépéséhez való folytatáshoz.

### <a name="review-deployment"></a>Telepítés áttekintése

A véleményezési szakasz a JSON központi telepítési jegyzéket jeleníti meg, amely az előző két szakaszban megadott beállítások alapján jött létre. Ne feledje, hogy két modul deklarált, hogy nem adja hozzá: **$edgeAgent** és **$edgeHub**. Ez a két modul alkotja az [IoT Edge futásidejű,](iot-edge-runtime.md) és minden üzembe helyezéskor szükséges alapértelmezett.

Tekintse át a központi telepítési adatokat, majd válassza a **Létrehozás lehetőséget.**

## <a name="view-modules-on-your-device"></a>Modulok megtekintése az eszközön

Miután üzembe helyezte a modulokat az eszközre, megtekintheti az összeset az IoT Hub eszközadatok lapján. Ez a lap megjeleníti az egyes üzembe helyezett modulok nevét, valamint olyan hasznos információkat, mint a központi telepítés állapota és a kilépési kód.

## <a name="deploy-modules-from-azure-marketplace"></a>Modulok üzembe helyezése az Azure Piactérről

[Az Azure Marketplace](https://azuremarketplace.microsoft.com/) egy online alkalmazások és szolgáltatások piactere, ahol az Azure-ban hitelesített és optimalizált vállalati alkalmazások és megoldások széles körében böngészhet, beleértve az [IoT Edge modulokat is.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)

Az IoT Edge-modul az Azure Marketplace-en és az IoT Hub.

### <a name="deploy-from-azure-marketplace"></a>Üzembe helyezés az Azure Piactérről

Az IoT Edge-modulok at a piactéren, és ha megtalálja a kívánt, akkor üzembe helyezheti azt a Create vagy a Get it now **(Létrehozás** **most)** lehetőséget választva. Folytassa a központi telepítési varázsló lépéseit, amelyek a kiválasztott IoT Edge-modultól függően változhatnak:

1. Nyugtázza a szolgáltató használati feltételeit és adatvédelmi szabályzatát a **Folytatás**lehetőség kiválasztásával. Előfordulhat, hogy először meg kell adnia az elérhetőségi adatokat.
1. Válassza ki az előfizetést és az IoT Hubot, amelyhez a céleszköz csatlakozik.
1. **Telepítsen egy eszközre**lehetőséget.
1. Adja meg az eszköz nevét, vagy válassza az **Eszköz keresése lehetőséget** a hubon regisztrált eszközök közötti böngészéshez.
1. Válassza a **Létrehozás** lehetőséget a központi telepítési jegyzékfájl konfigurálásának szokásos folyamatának folytatásához, beleértve más modulok hozzáadását is, ha szükséges. Az új modul részletei, például a lemezkép URI-ja, a létrehozási beállítások és a kívánt tulajdonságok előre definiálva vannak, de módosíthatók.

Ellenőrizze, hogy a modul telepítve van-e az IoT Hub az Azure Portalon. Válassza ki az eszközt, válassza **a Modulok beállítása** lehetőséget, és a modulnak szerepelnie kell az **IoT Edge Modules** szakaszban.

### <a name="deploy-from-azure-iot-hub"></a>Üzembe helyezés az Azure IoT Hubról

Gyorsan üzembe helyezhet egy modult az Azure Marketplace-ről az eszközére az Azure Portalon az IoT Hubban.

1. Az Azure Portalon keresse meg az IoT Hub.
1. A bal oldali ablaktáblán az **Automatikus eszközkezelés**csoportban válassza az **IoT Edge**lehetőséget.
1. Válassza ki az IoT Edge-eszközt, amely a központi telepítés fogadásához.
1. A felső sávon válassza a **Modulok beállítása**lehetőséget.
1. Az **IoT Edge-modulok csoportban** kattintson a **Hozzáadás**gombra, és válassza a legördülő menü **Piactérmodul** parancsát.

![Modul hozzáadása az IoT Hubban](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Válasszon egy **modult az IoT Edge module piactér** oldalon. A kiválasztott modul automatikusan konfigurálva van az előfizetéshez, az erőforráscsoporthoz és az eszközhöz. Ezután megjelenik az IoT Edge-modulok listájában. Egyes modulok további konfigurációt igényelhetnek.

> [!TIP]
> Az Azure IoT Hub IoT Edge-moduljaival kapcsolatos információk korlátozottak. Először többet az [IoT Edge-modulok](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) az Azure Marketplace-en.

Válassza a **Tovább: Útvonalak és** folytassa a telepítést az [útvonalak megadása](#specify-routes) és a [Telepítés áttekintése](#review-deployment) korábbi cikkben leírtak szerint.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [telepítheti és figyelheti az IoT Edge-modulokat nagy méretekben](how-to-deploy-monitor.md)
