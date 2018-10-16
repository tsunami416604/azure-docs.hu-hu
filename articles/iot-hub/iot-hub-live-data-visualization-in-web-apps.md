---
title: Az Azure IoT hub – a Web Apps érzékelőktől kapott adatok valós idejű adatok vizualizációját |} A Microsoft Docs
description: A Microsoft Azure App Service Web Apps szolgáltatás használatával jelenítheti meg az érzékelő összegyűjtött és az Iot hubnak küldött hőmérséklettel és páratartalommal kapcsolatos adatokat.
author: rangv
manager: ''
keywords: valós idejű adatvizualizáció, élő adatvizualizáció, érzékelő adatmegjelenítés
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: d40bcc8e6fd47a00618b98972f92c1e6fa019612
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318520"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-by-using-the-web-apps-feature-of-azure-app-service"></a>Az Azure App Service Web Apps szolgáltatásának használatát az Azure IoT hub valós idejű érzékelői adatainak megjelenítése

![Végpontok közötti diagram](media/iot-hub-get-started-e2e-diagram/5.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Ismertetett témák

Ebben az oktatóanyagban elsajátíthatja, hogyan jelenítheti meg az IoT hub által fogadott által üzemeltetett webes alkalmazást futtat egy webalkalmazás, valós idejű érzékelőadatok. Ha azt szeretné, próbálja meg az IoT hub az adatok megjelenítése Power BI használatával kapcsolatban lásd: [Power BI segítségével az Azure IoT Hub valós idejű érzékelői adatainak megjelenítése](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>TEENDŐ

- Webalkalmazás létrehozása az Azure Portalon.
- Készüljön fel az IoT hub az adatok eléréséhez egy fogyasztói csoport hozzáadásával.
- Az IoT hub érzékelői adatokat olvasni a WebApp konfigurálását.
- Töltse fel egy webalkalmazást a webalkalmazás üzemeltetni.
- Nyissa meg a webalkalmazás az IoT hub valós idejű hőmérséklettel és páratartalommal kapcsolatos adatok megtekintéséhez.

## <a name="what-you-need"></a>Mi szükséges

- [Az eszköz beállítása](iot-hub-raspberry-pi-kit-node-get-started.md), mely kiterjed a következő követelményeknek:
  - Aktív Azure-előfizetéssel
  - Az Iot hub, az előfizetéséhez
  - Egy ügyfélalkalmazás, amely üzeneteket küld az Iot hubhoz
- [A Git letöltése](https://www.git-scm.com/downloads)

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

1. Az a [az Azure portal](https://portal.azure.com/), kattintson a **erőforrás létrehozása** > **Web + mobil** > **webalkalmazás**.
2. Adjon meg egy egyedi feladat nevet, ellenőrizze az előfizetés, adja meg egy erőforráscsoportot és a egy helyen, jelölje be **rögzítés az irányítópulton**, és kattintson a **létrehozás**.

   Azt javasoljuk, hogy ugyanazon a helyen, amely az erőforráscsoport, jelölje ki. Ezzel segítséget nyújt a feldolgozási sebességétől, és csökkenti a költségeket az adatátvitelt.

   ![Webalkalmazás létrehozása](media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>A webalkalmazás adatokat olvasni az IoT hub konfigurálása

1. Nyissa meg a webalkalmazás csak kiépített.
2. Kattintson a **Alkalmazásbeállítások**, majd **Alkalmazásbeállítások**, adja hozzá a következő kulcs-érték párok:

   | Kulcs                                   | Érték                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | Az Azure parancssori felületen kapott                                      |
   | Azure.IoT.IoTHub.ConsumerGroup        | A fogyasztói csoportot adja hozzá az az IoT hub nevét  |
   | WEBSITE_NODE_DEFAULT_VERSION          | 8.9.4                                                        |

   ![A webalkalmazás a kulcs/érték párok beállítások hozzáadása](media/iot-hub-live-data-visualization-in-web-apps/4_web-app-settings-key-value-azure.png)

3. Kattintson a **Alkalmazásbeállítások**alatt **általános beállítások**, be-vagy kikapcsolása a **Web sockets** lehetőséget, majd kattintson a **mentése**.

   ![A Web sockets lehetőség átváltása](media/iot-hub-live-data-visualization-in-web-apps/10_toggle_web_sockets.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>A webalkalmazás üzemeltethető webalkalmazás feltöltése

A Githubon végeztünk elérhető egy webalkalmazást, amely az IoT hub valós idejű érzékelői adatainak megjelenítése. Ehhez szüksége a webalkalmazást egy Git-tárház, töltse le a webalkalmazást a Githubról, és töltse fel azt az Azure-gazdagépre a webalkalmazás konfigurálása.

1. Kattintson a web app alkalmazásban **központi telepítési beállítások** > **forrás kiválasztása** > **helyi Git-tárház**, és kattintson a **OK**.

   ![A webalkalmazások üzembe helyezését a helyi Git-tárház használandó konfigurálása](media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

2. Kattintson a **üzembe helyezési hitelesítő adatok**, hozzon létre egy felhasználónév és jelszó használata az Azure-ban a Git-tárház csatlakozhat, és kattintson a **mentése**.

3. Kattintson a **áttekintése**, és jegyezze fel az értékét **Git clone URL-cím**.

   ![A webalkalmazás Git clone URL-cím beszerzése](media/iot-hub-live-data-visualization-in-web-apps/7_web-app-git-clone-url-azure.png)

4. Nyisson meg egy parancs vagy a terminálablakban a helyi számítógépen.

5. Töltse le a webalkalmazást a Githubról, és töltse fel az Azure-bA a webalkalmazás-gazdagépre. Ehhez futtassa a következő parancsokat:

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   cd web-apps-node-iot-hub-data-visualization
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!NOTE]
   > \<Git clone URL-cím\> található Git-adattár URL-címe a **áttekintése** a webalkalmazás oldalán.

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Nyissa meg a webes alkalmazást az IoT hub valós idejű hőmérséklettel és páratartalommal kapcsolatos adatok

Az a **áttekintése** lapon kattintson a webalkalmazásokat, nyissa meg a webalkalmazás URL-CÍMÉT.

![A webalkalmazás URL-címére](media/iot-hub-live-data-visualization-in-web-apps/8_web-app-url-azure.png)

Az IoT hub megtekintheti a valós idejű hőmérséklettel és páratartalommal kapcsolatos adatokat.

![Valós idejű hőmérséklettel és páratartalommal kapcsolatos megjelenítő alkalmazás weblap](media/iot-hub-live-data-visualization-in-web-apps/9_web-app-page-show-real-time-temperature-humidity-azure.png)

> [!NOTE]
> Győződjön meg arról, a mintaalkalmazás fut az eszközön. Ha nem, megjelenik egy üres diagram, olvassa el a oktatóanyagok alatt [beállítani eszközét](iot-hub-raspberry-pi-kit-node-get-started.md).

## <a name="next-steps"></a>További lépések
A webalkalmazás sikeresen felhasználta az IoT hub valós idejű érzékelői adatainak megjelenítése.

Egy alternatív mód az Azure IoT Hub adatainak megjelenítése, lásd: [Power BI segítségével az IoT hub valós idejű érzékelői adatainak megjelenítése](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
