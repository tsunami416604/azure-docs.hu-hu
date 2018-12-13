---
title: Modulok az Azure portal – Azure IoT Edge üzembe helyezése |} A Microsoft Docs
description: Modulok IoT Edge-eszköz üzembe helyezése az Azure portal használatával
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: fab85b74fb53bec07843abf31aac1a8495d0fcfb
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093981"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Az Azure Portalról az Azure IoT Edge-modulok telepítése

Miután létrehozott egy IoT Edge modulok az üzleti logikával rendelkező, érdemes őket az eszközökre telepíteni kívánt megfelelően működjenek a peremhálózaton. Ha több modulokat, amelyek együttműködve gyűjtenek és dolgoznak fel adatokat, és egyszerre telepítheti őket, és deklarálja az útválasztási szabályokat, amelyek csatlakoztathatja őket. 

Ez a cikk bemutatja, hogyan az Azure Portalon végigvezeti egy manifest nasazení létrehozása és leküldése az üzembe helyezés az IoT Edge-eszköz. A megosztott címkék alapján több eszköz célzó központi telepítés létrehozásával kapcsolatos információkért lásd: [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Előfeltételek

* Egy [az IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésében. 
* Egy [IoT Edge-eszköz](how-to-register-device-portal.md) az telepítve van az IoT Edge-futtatókörnyezet. 

## <a name="select-your-device"></a>Válassza ki az eszközt

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és keresse meg az IoT hubot.
2. Válassza ki **IoT Edge** a menüből.
3. Kattintson az eszközök a listából a célként megadott eszköz Azonosítóját. 
4. Válassza a **Modulok beállítása** lehetőséget.

## <a name="configure-a-deployment-manifest"></a>A manifest nasazení konfigurálása

A manifest nasazení egy JSON-dokumentum, amely azt ismerteti, hogy mely modulok üzembe helyezéséhez a modulokat, és az ikermodulokkal tulajdonságaiként közti adatfolyamok. Hogyan alkalmazásjegyzékeket az üzembe helyezési a munkahelyi, és hogyan hozhat létre, azokat kapcsolatos további információkért lásd: [megismerheti, hogyan IoT Edge-modulok használják, konfigurálhatók, és újra felhasználható](module-composition.md).

Az Azure Portalon rendelkezik egy varázslót, amely manifest nasazení, a JSON-dokumentum manuálisan semmint létrehozásának módját ismerteti. Azt a három lépésből áll: **modulok hozzáadása**, **útvonalak megadása**, és **tekintse át a központi telepítési**. 

### <a name="add-modules"></a>Modulok hozzáadása

1. Az a **beállításjegyzék-beállítások** szakasz az oldal bármely privát tárolójegyzékek, amelyek tartalmazzák a modul rendszerképek eléréséhez hitelesítő adatok megadása. 
2. Az a **üzembe helyezési modulok** című oldalon válassza **Hozzáadás**. 
3. A modulok típusú tekintse meg a legördülő listából: 
   * **IoT Edge-modul** – az alapértelmezett beállítás.
   * **Az Azure Stream Analytics modul** – csak a modulok által létrehozott egy Azure Stream Analytics számítási feladatot. 
4. Válassza ki a **IoT Edge-modul**.
5. Adja meg a modul nevét, majd adja meg a tároló rendszerképét. Példa: 
   * **Név** -tempSensor
   * **Lemezkép URI** -mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
6. Töltse ki az opcionális mezőket, ha szükséges. További információ a tároló-létrehozási beállítások, újraindítási szabályzata, és tekintse meg a kívánt állapot [EdgeAgent kívánt tulajdonságok](module-edgeagent-edgehub.md#edgeagent-desired-properties). További információ az ikermodul: [meghatározása vagy a frissítés kívánt tulajdonságok](module-composition.md#define-or-update-desired-properties).
7. Kattintson a **Mentés** gombra.
8. Ismételje meg a 2 – 6 további modulok hozzáadása a központi telepítés. 
9. Válassza ki **tovább** továbbra is az útvonalak szakaszban.

### <a name="specify-routes"></a>Útvonalak megadása

A varázsló lehetővé teszi, hogy alapértelmezés szerint egy útvonal neve **útvonal** és meghatározott, **FROM /* be felső $**, ami azt jelenti, kimenetét kapcsolt modulok üzeneteket az IoT hubnak küldött.  

Hozzáadása vagy frissítése az útvonalak származó adatokkal [útvonalak deklarálja](module-composition.md#declare-routes), majd **tovább** továbbra is a felülvizsgálati szakaszban.

### <a name="review-deployment"></a>Tekintse át a központi telepítés

A tekintse át a szakasz azt mutatja be, a JSON-telepítésben manifest létrehozott az előző két szakaszokban alapján. Vegye figyelembe, hogy nincsenek a két modul deklarálva, hogy nem adta hozzá: **$edgeAgent** és **$edgeHub**. Ezeket a modulokat két alkotják a [IoT Edge-futtatókörnyezet](iot-edge-runtime.md) és minden üzembe helyezés a szükséges alapértelmezett értékek. 

Tekintse át a telepítési adatokat, majd válassza a **küldés**. 

## <a name="view-modules-on-your-device"></a>Modulok megjelenítése az eszközön

Miután telepítette a modulokat az eszközön, megtekintheti azokat a **eszközadatok** a portál. Ezen a lapon minden egyes telepített modul, valamint a hasznos információk, például a központi telepítési állapot és a kilépési kód nevét jeleníti meg. 

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok](how-to-deploy-monitor.md)
