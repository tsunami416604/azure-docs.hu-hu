---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/10/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b453a04a170764a037eed7415eaf71e5a4d37526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844588"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Event Grid IoT Edge modul telepítése

A modulok at ioT Edge-eszközökre többféleképpen is üzembe helyezheti, és mindegyik az Azure Event Grid ben működik az IoT Edge-en. Ez a cikk ismerteti az Event Grid az Azure Portalon az IoT Edge üzembe helyezésének lépéseit.

>[!NOTE]
> Ebben az oktatóanyagban az Event Grid modult adatmegőrzés nélkül telepíti. Ez azt jelenti, hogy az ebben az oktatóanyagban létrehozott témakörök és előfizetések törlődnek a modul újratelepítésekor. Az adatmegőrzés beállításáról a következő cikkekben talál további információt: [Állapot megőrzése Linux alatt](../articles/event-grid/edge/persist-state-linux.md) vagy [Perzisztencia állapot a Windows rendszerben.](../articles/event-grid/edge/persist-state-windows.md) Éles számítási feladatok esetén azt javasoljuk, hogy az Event Grid modult adatmegőrzéssel telepítse.

>[!IMPORTANT]
> Ebben az oktatóanyagban az Event Grid modul az ügyfélhitelesítés kikapcsolt verziójával lesz telepítve, és engedélyezi a HTTP-előfizetők számára. Éles számítási feladatok esetén azt javasoljuk, hogy csak HTTPS-kérelmeket és az ügyfelek hitelesítése engedélyezve van előfizetők engedélyezése. Az Event Grid modul biztonságos konfigurálásáról a [Biztonság és hitelesítés](../articles/event-grid/edge/security-authentication.md)című témakörben talál további információt.
 
### <a name="select-your-iot-edge-device"></a>Válassza ki az IoT Edge-eszközt

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com)
1. Keresse meg az IoT Hubot.
1. Válassza az **IoT Edge elemet** az **Automatikus eszközkezelés** szakasz menüjéből. 
1. Kattintson a céleszköz azonosítójára az eszközök listájából
1. Válassza **a Modulok beállítása**lehetőséget. Tartsa nyitva az oldalt. A következő szakaszban ismertetett lépésekkel folytatja.

### <a name="configure-a-deployment-manifest"></a>Központi telepítési jegyzékfájl konfigurálása

A központi telepítési jegyzékfájl egy JSON-dokumentum, amely leírja, hogy mely modulokat kell telepíteni, hogyan adatfolyamok a modulok között, és a modul twins kívánt tulajdonságait. Az Azure Portalon van egy varázsló, amely végigvezeti a központi telepítési jegyzékfájl létrehozásán, ahelyett, hogy a JSON-dokumentum manuálislétrehozása.  Három lépésből áll: **Modulok hozzáadása**, **Útvonalak megadása**és A **telepítés áttekintése**.

### <a name="add-modules"></a>Modulok hozzáadása

1. A **Központi telepítési modulok csoportban** válassza **a Hozzáadás**
1. A legördülő listában szereplő modulok típusai közül válassza az **IoT Edge Module**
1. Adja meg a tároló nevét, lemezképét és tárolójának létrehozási beállításait:

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **Név**: eventgridmodule
   * **Kép URI**-`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Tároló létrehozási beállításai:**

    ```json
        {
          "Env": [
            "inbound__clientAuth:clientCert__enabled=false",
            "outbound__webhook__httpsOnly=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                  "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```

 1. Kattintson a **Mentés gombra**
 1. Kattintson a **Tovább** gombra az útvonalak szakasz folytatásához

    > [!NOTE]
    > Ha egy Azure virtuális gép peremhálózati eszközként, adjon hozzá egy bejövő port szabály, amely lehetővé teszi a bejövő forgalmat a 4438-as porton. A szabály hozzáadásáról a [Portok megnyitása virtuális géphez](../articles/virtual-machines/windows/nsg-quickstart-portal.md)című témakörben talál útmutatást.


### <a name="setup-routes"></a>Útvonalak beállítása

 Az alapértelmezett útvonalak megtartása, majd a Tovább gombra a véleményezési szakasz folytatásához válassza a **Tovább** gombot.

### <a name="review-deployment"></a>Telepítés áttekintése

1. A véleményezési szakasz a JSON központi telepítési jegyzéket jeleníti meg, amely az előző két szakaszban megadott beállítások alapján jött létre. Ellenőrizze, hogy a listában két modul **látható-e: $edgeAgent** és **$edgeHub**. Ez a két modul alkotja az IoT Edge futásidejű, és minden üzembe helyezéskor szükséges alapértelmezett.
1. Tekintse át a központi telepítési adatokat, majd válassza a **Küldés lehetőséget.**

### <a name="verify-your-deployment"></a>A telepítés ellenőrzése

1. Miután elküldte a központi telepítést, visszatér az IoT-központ IoT Peremhálózati lapjára.
1. Válassza ki az **IoT Edge-eszközt,** amelyet a központi telepítéssel célzott meg a részletek megnyitásához.
1. Az eszköz részleteiben ellenőrizze, hogy az Event Grid modul a **központi telepítésben megadott** és az eszköz által jelentett ként is szerepel-e. **Reported by device**

Eltarthat néhány percig, amíg a modul elindul az eszközön, és majd jelenteni kell az IoT Hubnak. Frissítse a lapot a frissített állapot megtekintéséhez.