---
title: Modulok az Azure portal – Azure IoT Edge üzembe helyezése |} A Microsoft Docs
description: Modulok IoT Edge-eszköz üzembe helyezése az Azure portal használatával
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f27484fd1d47a2e29aa5083a7d440e5c7dba11c1
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839643"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Az Azure Portalról az Azure IoT Edge-modulok telepítése

Miután létrehozott egy IoT Edge modulok az üzleti logikával rendelkező, érdemes őket az eszközökre telepíteni kívánt megfelelően működjenek a peremhálózaton. Ha több modulokat, amelyek együttműködve gyűjtenek és dolgoznak fel adatokat, és egyszerre telepítheti őket, és deklarálja az útválasztási szabályokat, amelyek csatlakoztathatja őket.

Ez a cikk bemutatja, hogyan az Azure Portalon végigvezeti egy manifest nasazení létrehozása és leküldése az üzembe helyezés az IoT Edge-eszköz. A megosztott címkék alapján több eszköz célzó központi telepítés létrehozásával kapcsolatos információkért lásd: [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Előfeltételek

* Egy [az IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésében.
* Egy [IoT Edge-eszköz](how-to-register-device-portal.md) az telepítve van az IoT Edge-futtatókörnyezet.

## <a name="select-your-device"></a>Válassza ki az eszközt

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és keresse meg az IoT hubot.
1. Válassza ki **IoT Edge** a menüből.
1. Kattintson az eszközök a listából a célként megadott eszköz Azonosítóját.
1. Válassza a **Modulok beállítása** lehetőséget.

## <a name="configure-a-deployment-manifest"></a>A manifest nasazení konfigurálása

A manifest nasazení egy JSON-dokumentum, amely azt ismerteti, hogy mely modulok üzembe helyezéséhez a modulokat, és az ikermodulokkal tulajdonságaiként közti adatfolyamok. Hogyan alkalmazásjegyzékeket az üzembe helyezési a munkahelyi, és hogyan hozhat létre, azokat kapcsolatos további információkért lásd: [megismerheti, hogyan IoT Edge-modulok használják, konfigurálhatók, és újra felhasználható](module-composition.md).

Az Azure Portalon rendelkezik egy varázslót, amely manifest nasazení, a JSON-dokumentum manuálisan semmint létrehozásának módját ismerteti. Három lépésből áll: **Modulok hozzáadása**, **útvonalak megadása**és az **üzembe helyezés áttekintése**.

### <a name="add-modules"></a>Modulok hozzáadása

1. A lap **Container Registry beállítások** szakaszában adja meg a modul lemezképeit tartalmazó privát tároló-nyilvántartók eléréséhez szükséges hitelesítő adatokat.

1. A lap **központi telepítési modulok** szakaszában válassza a **Hozzáadás**lehetőséget.

1. A modulok típusú tekintse meg a legördülő listából:

   * **IoT Edge-modul** – az alapértelmezett beállítás.
   * **Az Azure Stream Analytics modul** – csak a modulok által létrehozott egy Azure Stream Analytics számítási feladatot.
   * **Azure Machine learning modul** – csak Azure Machine learning munkaterületről generált modell-lemezképek.

1. Válassza ki a **IoT Edge-modul**.

1. Adja meg a modul nevét, majd adja meg a tároló rendszerképét. Példa:

   * **Név** – SimulatedTemperatureSensor
   * **Lemezkép URI** -mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

1. Töltse ki az opcionális mezőket, ha szükséges. További információ a tároló-létrehozási beállítások, újraindítási szabályzata, és tekintse meg a kívánt állapot [EdgeAgent kívánt tulajdonságok](module-edgeagent-edgehub.md#edgeagent-desired-properties). További információ az ikermodul: [meghatározása vagy a frissítés kívánt tulajdonságok](module-composition.md#define-or-update-desired-properties).

1. Kattintson a **Mentés** gombra.

1. Ismételje meg a 2 – 6 további modulok hozzáadása a központi telepítés.

1. Válassza ki **tovább** továbbra is az útvonalak szakaszban.

### <a name="specify-routes"></a>Útvonalak megadása

A varázsló lehetővé teszi, hogy alapértelmezés szerint egy útvonal neve **útvonal** és meghatározott, **FROM /* be felső $**, ami azt jelenti, kimenetét kapcsolt modulok üzeneteket az IoT hubnak küldött.  

Hozzáadása vagy frissítése az útvonalak származó adatokkal [útvonalak deklarálja](module-composition.md#declare-routes), majd **tovább** továbbra is a felülvizsgálati szakaszban.

### <a name="review-deployment"></a>Tekintse át a központi telepítés

A tekintse át a szakasz azt mutatja be, a JSON-telepítésben manifest létrehozott az előző két szakaszokban alapján. Vegye figyelembe, hogy nincsenek a két modul deklarálva, hogy nem adta hozzá: **$edgeAgent** és **$edgeHub**. Ezeket a modulokat két alkotják a [IoT Edge-futtatókörnyezet](iot-edge-runtime.md) és minden üzembe helyezés a szükséges alapértelmezett értékek.

Tekintse át a telepítési adatokat, majd válassza a **küldés**.

## <a name="view-modules-on-your-device"></a>Modulok megjelenítése az eszközön

Miután telepítette a modulokat az eszközön, megtekintheti azokat a **eszközadatok** a portál. Ezen a lapon minden egyes telepített modul, valamint a hasznos információk, például a központi telepítési állapot és a kilépési kód nevét jeleníti meg.

## <a name="deploy-modules-from-azure-marketplace"></a>Modulok üzembe helyezése az Azure Marketplace-en

Az Azure Marketplace egy online alkalmazások és szolgáltatások piaca, ahol az Azure-on futó Nagyvállalati alkalmazások és megoldások széles körét böngészheti, beleértve a [IoT Edge modulokat](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)is. Az Azure Marketplace a Azure Portal **erőforrás létrehozása**lehetőség alatt is elérhető.

IoT Edge modult az Azure Marketplace-ről vagy a Azure Portalról is telepíthet:

1. Keresse meg a modult, és kezdje el a telepítési folyamatot.

   * Azure Portal: Keresse meg a modult, és válassza a **Létrehozás**lehetőséget.

   * Azure Marketplace:

     1. Keresse meg a modult, és válassza a **Letöltés most**lehetőséget.
     1. A **Folytatás**gombra kattintva nyugtázza a szolgáltató használati feltételeit és adatvédelmi szabályzatát.

1. Válassza ki az előfizetését és azt a IoT Hub, amelyhez a céleszköz csatolva van.

1. Válassza **a telepítés eszközre**lehetőséget.

1. Adja meg az eszköz nevét, vagy válassza az **eszköz keresése** lehetőséget a hub-ban regisztrált eszközök között a tallózáshoz.

1. Válassza a **Létrehozás** lehetőséget a telepítési jegyzék konfigurálásának szabványos folyamatának folytatásához, beleértve a további modulok hozzáadását, ha szükséges. Az új modul adatai, például a rendszerkép URI-ja, a létrehozási beállítások és a kívánt tulajdonságok előre definiálva vannak, de módosíthatók.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok](how-to-deploy-monitor.md)
