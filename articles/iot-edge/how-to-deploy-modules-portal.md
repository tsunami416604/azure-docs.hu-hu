---
title: Modulok az Azure portal – Azure IoT Edge üzembe helyezése |} A Microsoft Docs
description: Modulok IoT Edge-eszköz üzembe helyezése az Azure portal használatával
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/19/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 9d7729dce5419c5813de3c4dfce55c40098f5988
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430114"
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

Az Azure Portalon rendelkezik egy varázslót, amely manifest nasazení, a JSON-dokumentum manuálisan semmint létrehozásának módját ismerteti. Ez a három lépésből áll: **Modulok hozzáadása**, **útvonalak megadása**, és **tekintse át a központi telepítési**.

### <a name="add-modules"></a>Modulok hozzáadása

1. Az a **beállításjegyzék-beállítások** szakasz az oldal bármely privát tárolójegyzékek, amelyek tartalmazzák a modul rendszerképek eléréséhez hitelesítő adatok megadása.

1. Az a **üzembe helyezési modulok** című oldalon válassza **Hozzáadás**.

1. A modulok típusú tekintse meg a legördülő listából:

   * **IoT Edge-modul** – az alapértelmezett beállítás.
   * **Az Azure Stream Analytics modul** – csak a modulok által létrehozott egy Azure Stream Analytics számítási feladatot.
   * **Az Azure Machine Learning modul** – csak modellezheti az Azure Machine Learning-munkaterület az a képeket.

1. Válassza ki a **IoT Edge-modul**.

1. Adja meg a modul nevét, majd adja meg a tároló rendszerképét. Példa:

   * **Név** -tempSensor
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

## <a name="deploy-modules-from-azure-marketplace"></a>Azure Marketplace-ről modulok telepítése

Az Azure Marketplace-en, tallózhat a vállalati alkalmazások és megoldások, hitelesített és optimalizált futtatásához az Azure-ban számos online alkalmazások és szolgáltatások piactérről köztük [IoT Edge-modulok](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Az Azure Marketplace-en is elérhető az Azure Portalon a **erőforrás létrehozása**.

Azure Marketplace-en vagy az Azure Portalon lehet telepíteni az IoT Edge-modul:

1. Keresse meg a modult, és a telepítési folyamat.

   * Az Azure Portalon: Egy modul megkeresése és kiválasztása **létrehozás**.

   * Az Azure Marketplace-en:

     1. Egy modul megkeresése és kiválasztása **Letöltés most**.
     1. Tudomásul veszi a szolgáltató használati feltételei és adatvédelmi szabályzat kiválasztásával **Folytatás**.

1. Válassza ki az előfizetés és az IoT Hub, az eszköznek, amelyhez csatlakozik.

1. Válasszon **üzembe helyezés az eszköz**.

1. Adja meg a nevét, válassza ki vagy az **található eszköz** , keresse meg a központon regisztrált eszközök között.

1. Válassza ki **létrehozás** konfigurálása a központi telepítési jegyzékfájl, beleértve a más modulok hozzáadása, ha szükséges, a standard szintű folyamat folytatásához. Lemezkép URI-ja, például az új modul részletei beállítások létrehozása, és kívánt tulajdonságok előre, de módosítható.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok](how-to-deploy-monitor.md)
