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
ms.openlocfilehash: 6d915145e64a5f1a097f38cf79b19426c3acbaf2
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457422"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Az Azure Portalról az Azure IoT Edge-modulok telepítése

Miután létrehozott egy IoT Edge modulok az üzleti logikával rendelkező, érdemes őket az eszközökre telepíteni kívánt megfelelően működjenek a peremhálózaton. Ha több modulokat, amelyek együttműködve gyűjtenek és dolgoznak fel adatokat, és egyszerre telepítheti őket, és deklarálja az útválasztási szabályokat, amelyek csatlakoztathatja őket.

Ez a cikk bemutatja, hogyan az Azure Portalon végigvezeti egy manifest nasazení létrehozása és leküldése az üzembe helyezés az IoT Edge-eszköz. További információ a megosztott címkék alapján több eszközt célzó központi telepítés létrehozásáról: [IoT Edge modulok üzembe helyezése és figyelése nagy léptékben](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Előfeltételek

* Egy [IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésében.
* [IoT Edge-eszköz](how-to-register-device.md#register-in-the-azure-portal) , amelyen telepítve van a IoT Edge futtatókörnyezet.

## <a name="select-your-device"></a>Válassza ki az eszközt

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz.
1. A menüből válassza a **IoT Edge** lehetőséget.
1. Kattintson az eszközök a listából a célként megadott eszköz Azonosítóját.
1. Válassza a **Modulok beállítása** lehetőséget.

## <a name="configure-a-deployment-manifest"></a>A manifest nasazení konfigurálása

A manifest nasazení egy JSON-dokumentum, amely azt ismerteti, hogy mely modulok üzembe helyezéséhez a modulokat, és az ikermodulokkal tulajdonságaiként közti adatfolyamok. Az üzembe helyezési jegyzékek működésével és létrehozásával kapcsolatos további információkért lásd: [IoT Edge modulok használatának, konfigurálásának és](module-composition.md)újbóli használatának ismertetése.

Az Azure Portalon rendelkezik egy varázslót, amely manifest nasazení, a JSON-dokumentum manuálisan semmint létrehozásának módját ismerteti. Három lépésből áll: **modulok hozzáadása**, **útvonalak megadása**és az **üzembe helyezés áttekintése**.

### <a name="add-modules"></a>Modulok hozzáadása

1. A lap **Container Registry beállítások** szakaszában adja meg a modul lemezképeit tartalmazó privát tároló-nyilvántartók eléréséhez szükséges hitelesítő adatokat.

1. A lap **központi telepítési modulok** szakaszában válassza a **Hozzáadás**lehetőséget.

1. A modulok típusú tekintse meg a legördülő listából:

   * **IoT Edge modul** – az alapértelmezett beállítás.
   * **Azure stream Analytics modul** – csak Azure stream Analytics munkaterhelés által generált modulok.
   * **Azure Machine learning modul** – csak Azure Machine learning munkaterületről generált modell-lemezképek.

1. Válassza ki a **IoT Edge modult**.

1. Adja meg a modul nevét, majd adja meg a tároló rendszerképét. Például:

   * **Név** – SimulatedTemperatureSensor
   * **Rendszerkép URI-ja** – MCR.microsoft.com/azureiotedge-simulated-Temperature-Sensor:1.0

1. Töltse ki az opcionális mezőket, ha szükséges. További információ a tároló létrehozási lehetőségeiről, az újraindítási szabályzatról és a kívánt állapotról: [EdgeAgent kívánt tulajdonságai](module-edgeagent-edgehub.md#edgeagent-desired-properties). További információ a modul Twin-ről: a [kívánt tulajdonságok meghatározása vagy frissítése](module-composition.md#define-or-update-desired-properties).

1. Kattintson a **Mentés** gombra.

1. Ismételje meg a 2 – 6 további modulok hozzáadása a központi telepítés.

1. Az útvonalak szakasz folytatásához kattintson a **tovább** gombra.

### <a name="specify-routes"></a>Útvonalak megadása

Alapértelmezés szerint a varázsló egy **route (útvonal** ) nevű útvonalat ad meg, amelyet **a/* be $upstream * *-ban határoz meg, ami azt jelenti, hogy bármely modulból kimenetet küld az IoT hubhoz.  

Adja hozzá vagy frissítse az útvonalakat a [deklarált útvonalak](module-composition.md#declare-routes)információi alapján, majd kattintson a **tovább** gombra a felülvizsgálati szakasz folytatásához.

### <a name="review-deployment"></a>Tekintse át a központi telepítés

A tekintse át a szakasz azt mutatja be, a JSON-telepítésben manifest létrehozott az előző két szakaszokban alapján. Vegye figyelembe, hogy két modul deklarálta azt, hogy nem adta hozzá a következőket: **$edgeAgent** és **$edgeHub**. Ez a két modul hozza létre a [IoT Edge futtatókörnyezetet](iot-edge-runtime.md) , és minden központi telepítés esetében kötelező alapértelmezett érték.

Tekintse át az üzembe helyezési adatokat, majd válassza a **Küldés**lehetőséget.

## <a name="view-modules-on-your-device"></a>Modulok megjelenítése az eszközön

Miután telepítette a modulokat az eszközre, megtekintheti az összeset a portál **eszköz adatai** lapján. Ezen a lapon minden egyes telepített modul, valamint a hasznos információk, például a központi telepítési állapot és a kilépési kód nevét jeleníti meg.

## <a name="deploy-modules-from-azure-marketplace"></a>Modulok üzembe helyezése az Azure Marketplace-en

Az Azure Marketplace egy online alkalmazások és szolgáltatások piaca, ahol az Azure-on futó Nagyvállalati alkalmazások és megoldások széles körét böngészheti, beleértve a [IoT Edge modulokat](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)is. Az Azure Marketplace a Azure Portal **erőforrás létrehozása**lehetőség alatt is elérhető.

IoT Edge modult az Azure Marketplace-ről vagy a Azure Portalról is telepíthet:

1. Keresse meg a modult, és kezdje el a telepítési folyamatot.

   * Azure Portal: keresse meg a modult, és válassza a **Létrehozás**lehetőséget.

   * Azure Marketplace:

     1. Keresse meg a modult, és válassza a **Letöltés most**lehetőséget.
     1. A **Folytatás**gombra kattintva nyugtázza a szolgáltató használati feltételeit és adatvédelmi szabályzatát.

1. Válassza ki az előfizetését és azt a IoT Hub, amelyhez a céleszköz csatolva van.

1. Válassza **a telepítés eszközre**lehetőséget.

1. Adja meg az eszköz nevét, vagy válassza az **eszköz keresése** lehetőséget a hub-ban regisztrált eszközök között a tallózáshoz.

1. Válassza a **Létrehozás** lehetőséget a telepítési jegyzék konfigurálásának szabványos folyamatának folytatásához, beleértve a további modulok hozzáadását, ha szükséges. Az új modul adatai, például a rendszerkép URI-ja, a létrehozási beállítások és a kívánt tulajdonságok előre definiálva vannak, de módosíthatók.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [helyezhet üzembe és figyelheti IoT Edge modulokat a skálán](how-to-deploy-monitor.md)
