---
title: Azure IoT biztonsági modulok (portál) telepítése |} Microsoft Docs
description: Az Azure portál segítségével IoT peremhálózati eszköz modulok telepítése
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4082189d451f670c1ae3f76b8ec785d8bd0518b3
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036107"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Azure-portálról Azure IoT Edge-modulok telepítése

Miután létrehozott egy IoT peremhálózati modulok az üzleti logikát, érdemes őket az eszközökre telepíteni kívánt működéséhez a peremhálózaton. Ha több modulokat, amelyek együttműködése gyűjti és feldolgozza, egyszerre telepítheti őket, és csatlakoztassa őket az útválasztási szabályokat deklarálható. 

Ez a cikk bemutatja, hogyan az Azure-portálon végigvezeti egy üzembe helyezési jegyzék létrehozása és IoT peremhálózati eszköz a központi telepítést végez leküldést. A központi telepítés, amelynek célpontja a megosztott címkék alapján több eszközre létrehozásával kapcsolatos további információkért lásd: [telepítés és az IoT peremhálózati modulok léptékű figyelése](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Előfeltételek

* Egy [IoT-központ](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésben. 
* Egy [IoT peremhálózati eszköz](how-to-register-device-portal.md) telepített IoT peremhálózati futtatási idő mellett. 

## <a name="select-your-device"></a>Az eszköz kiválasztásához

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) , és keresse meg az IoT hub.
2. Válassza ki **IoT peremhálózati** a menüből.
3. Kattintson az eszközök a listából a célként megadott eszköz azonosítója. 
4. Válassza a **Modulok beállítása** lehetőséget.

## <a name="configure-a-deployment-manifest"></a>Egy üzembe helyezési jegyzék konfigurálása

Egy üzembe helyezési jegyzék egy JSON-dokumentumában, hogy melyik modulokat telepíteni, hogyan közötti adatáramlás a modulok, és a modul twins kívánt tulajdonságait ismerteti. Hogyan telepítési módjától munkahelyi és hogyan hozza létre a címzetteket kapcsolatos további információkért lásd: [megérteni, hogyan IoT peremhálózati modulok használják, konfigurálhatók, és használja fel újra](module-composition.md).

Az Azure portálon találhat egy varázslót, amely végigvezeti Önt az üzembe helyezési jegyzék helyett manuálisan összeállítása a JSON-dokumentum létrehozása. Három lépést tartalmaz: **modulok hozzáadása**, **adja meg az útvonalakat**, és **tekintse át a központi telepítési**. 

### <a name="add-modules"></a>Modulok hozzáadása

1. Az a **beállításjegyzék-beállítások** szakasz az oldal adjon meg semmilyen személyes tárolót nyilvántartó modul képeit tartalmazó eléréséhez szükséges hitelesítő adatokat. 
2. Az a **telepítési modulok** lapon jelölje be szakasza **hozzáadása**. 
3. Válassza ki a modul típusát a legördülő listából: 
   * **Az IoT peremhálózati modul** -az alapértelmezett beállítás.
   * **Az Azure Stream Analytics modul** -csak a modulok által létrehozott egy Azure Stream Analytics munkaterhelés. 

4. Adja meg a modul nevét, majd adja meg a tároló lemezképet. Példa: 
   * **Név** -tempSensor
   * **Kép URI** -mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
5. Töltse ki a választható mezőket, ha szükséges. További információ a tároló létrehozása újraindítási házirend, beállítások, és tekintse meg a kívánt állapot [EdgeAgent szükséges tulajdonságok](module-edgeagent-edgehub.md#edgeagent-desired-properties). A modul a két további információ: [megadása vagy frissítés szükséges tulajdonságok](module-composition.md#define-or-update-desired-properties).
6. Kattintson a **Mentés** gombra.
7. Ismételje meg a 2 – 6 további modulok hozzá szeretne adni a telepítéshez. 
8. Válassza ki **következő** továbbra is az útvonalak szakaszban.

### <a name="specify-routes"></a>Adja meg az útvonalakat

A varázsló lehetővé teszi az alapértelmezés szerint egy útvonal neve **útvonal** és definiált, **FROM /* $előtt be **, ami azt jelenti, az IoT hub küldött kimenetét modul az üzeneteket.  

Hozzáadása vagy az útvonalak frissítése adataival [útvonalak deklarálható](module-composition.md#declare-routes), majd jelölje be **következő** továbbra is a tekintse át a szakasz.

### <a name="review-deployment"></a>Tekintse át a központi telepítés

A tekintse át a szakasz azt mutatja be a JSON-telepítésben, manifest létrehozott az előző két szakaszban megadott beállítások szerint. Vegye figyelembe, hogy van két modulok deklarálva, hogy nem vett fel: **$edgeAgent** és **$edgeHub**. Ezek a két modulok jött létre a [IoT peremhálózati futásidejű](iot-edge-runtime.md) és a szükséges alapértelmezett minden környezetben. 

Tekintse át a központi telepítési adatait, majd válasszon **Submit**. 

## <a name="view-modules-on-your-device"></a>Modulok megjelenítése az eszközön

Miután modulok telepítése után az eszközön, megtekintheti azokat a **eszközadatok** a portál lapján. Ezen a lapon minden egyes telepített modul, valamint a hasznos információkat, például a központi telepítési állapot- és kilépési kód nevét jeleníti meg. 

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [telepítés és az IoT peremhálózati modulok léptékű figyelése](how-to-deploy-monitor.md)
