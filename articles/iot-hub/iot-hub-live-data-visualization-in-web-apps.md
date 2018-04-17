---
title: Az érzékelők adatstreamének az Azure IoT hub – Web Apps a valós idejű adatok vizuális |} Microsoft Docs
description: A Microsoft Azure App Service Web Apps szolgáltatása segítségével, amely az érzékelő gyűjtése történik, és az Iot hub küldött hőmérséklet és a páratartalom adatainak megjelenítése.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: valós idejű adatok vizuális, az élő adatok vizuális érzékelő adatábrázolási
ms.assetid: e42b07a8-ddd4-476e-9bfb-903d6b033e91
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 0edf72595f67dd9f6b33f9bfeaf4cd98405dc3ca
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-by-using-the-web-apps-feature-of-azure-app-service"></a>Az Azure IoT hub a valós idejű érzékelőadatok megjelenítése az Azure App Service Web Apps szolgáltatásának használatával

![Végpontok közötti diagramja](media/iot-hub-get-started-e2e-diagram/5.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Ismertetett témák

Ebben az oktatóanyagban elsajátíthatja, hogyan jelenítheti meg az IoT-központ által futtatott webalkalmazás fut a webes alkalmazás kap valós idejű érzékelőadatok. Ha azt szeretné, próbálja meg az IoT hub adatainak megjelenítése Power BI használatával kapcsolatos tudnivalókat lásd: [használjon Power BI segítségével ábrázolhatja valós idejű érzékelőadatok Azure IoT hubról](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Mit

- Webalkalmazás létrehozása az Azure portálon.
- Felkészülés az IoT hub adatelérés egy felhasználói csoport hozzáadásával.
- A webalkalmazás érzékelő adatokat olvasni az IoT hub konfigurálása.
- Töltse fel a webalkalmazások a web app működhetnek.
- Nyissa meg a webalkalmazás az IoT hub valós idejű hőmérséklet és a páratartalom adatai.

## <a name="what-you-need"></a>Mi szükséges

- [Konfigurálja az eszközt](iot-hub-raspberry-pi-kit-node-get-started.md), amely hozzá van rendelve az alábbi követelményeknek:
  - Aktív Azure-előfizetés
  - Az Iot-központ az előfizetéshez tartozó
  - Egy ügyfélalkalmazást, amely üzeneteket küld az Iot hub
- [Töltse le a Git](https://www.git-scm.com/downloads)

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

1. Az a [Azure-portálon](https://portal.azure.com/), kattintson a **hozzon létre egy erőforrást** > **Web + mobil** > **webalkalmazás**.
2. Adja meg egy egyedi feladat nevét, ellenőrizze az előfizetés, adjon meg egy erőforráscsoportot és helyet, jelölje be **rögzítés az irányítópulton**, és kattintson a **létrehozása**.

   Azt javasoljuk, hogy legyen, mint az erőforráscsoport válassza ki az ugyanazon a helyen. Ezzel segítséget nyújt a feldolgozási sebesség, és csökkenti az adatátvitelt.

   ![Webalkalmazás létrehozása](media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>A webalkalmazás adatokat olvasni az IoT hub konfigurálása

1. Nyissa meg a webes alkalmazás imént létesített.
2. Kattintson a **Alkalmazásbeállítások**, majd az **Alkalmazásbeállítások**, adja hozzá a következő kulcs/érték párok:

   | Kulcs                                   | Érték                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | Az IOT hubbal-explorer kapott                                |
   | Azure.IoT.IoTHub.ConsumerGroup        | A fogyasztói csoportot felvenni kívánt az IoT hub nevét  |

   ![A webalkalmazás a kulcs/érték párok beállítások hozzáadása](media/iot-hub-live-data-visualization-in-web-apps/4_web-app-settings-key-value-azure.png)

3. Kattintson a **Alkalmazásbeállítások**a **általános beállítások**, váltása a **webes szoftvercsatornák** lehetőséget, majd kattintson a **mentése**.

   ![A webes szoftvercsatornák beállítást váltása](media/iot-hub-live-data-visualization-in-web-apps/10_toggle_web_sockets.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>A webalkalmazás üzemeltetnie webalkalmazás feltöltése

A Githubon hajtottunk érhető el egy webes alkalmazás, amely az IoT hub valós idejű érzékelő adatait jeleníti meg. Ehhez szüksége a webalkalmazás Git-tárház, töltse le a webes alkalmazás a Githubból, és töltse fel azt Azure állomásnak a webalkalmazás konfigurálása.

1. Kattintson a webalkalmazás **központi telepítési beállítások** > **forrás választása** > **helyi Git-tárház**, és kattintson a **OK**.

   ![A webes alkalmazás telepítéséhez a helyi Git-tárházon konfigurálása](media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

2. Kattintson a **üzembe helyezési hitelesítő adatok**, hozzon létre egy felhasználónevet és jelszót ehhez a Git-tárházat az Azure-ban, és kattintson a **mentése**.

3. Kattintson a **áttekintése**, és jegyezze fel a értékének **Git-klón URL-címét**.

   ![A webalkalmazás Git klón URL-cím beszerzése](media/iot-hub-live-data-visualization-in-web-apps/7_web-app-git-clone-url-azure.png)

4. Nyissa meg a parancsot vagy terminálablakot a helyi számítógépen.

5. Töltse le a webalkalmazást a Githubból, és töltse fel az Azure a webalkalmazás a gazdagépre. Ehhez futtassa a következő parancsokat:

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   cd web-apps-node-iot-hub-data-visualization
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!NOTE]
   > \<Git-klón URL-cím\> található a Git-tárház URL-címe a **áttekintése** a webes alkalmazás lapján.

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Nyissa meg a webes alkalmazást az IoT hub valós idejű hőmérséklet és a páratartalom adatai

Az a **áttekintése** lap webalkalmazás, kattintson a nyissa meg a webes alkalmazás URL-CÍMÉT.

![A webalkalmazás URL-cím beszerzése](media/iot-hub-live-data-visualization-in-web-apps/8_web-app-url-azure.png)

Az IoT hub származó, meg kell jelennie a hőmérséklet és a páratartalom valós idejű adatok.

![Valós idejű hőmérséklet és a páratartalom bemutató alkalmazás weblap](media/iot-hub-live-data-visualization-in-web-apps/9_web-app-page-show-real-time-temperature-humidity-azure.png)

> [!NOTE]
> Ellenőrizze, hogy a mintaalkalmazás fut-e az eszközön. Ha nem, elérhetővé válik egy üres diagram, olvassa el az oktatóanyagok alatt [beállítani az eszközét](iot-hub-raspberry-pi-kit-node-get-started.md).

## <a name="next-steps"></a>További lépések
A webalkalmazás sikeresen használtuk az IoT hub a valós idejű érzékelőadatok megjelenítéséhez.

Egy Azure IoT Hub-adatok ábrázolása alternatív módja, lásd: [használjon Power BI segítségével ábrázolhatja az IoT hub a valós idejű érzékelőadatok](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
