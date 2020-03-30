---
title: Az Azure IoT-eszközkezelés a Visual Studio Cloud Explorer rel
description: Használja a Cloud Explorer for Visual Studio for Azure IoT Hub eszközfelügyeleti, amely a közvetlen módszerek és a Twin kívánt tulajdonságok kezelési lehetőségek.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 6fe5a45dda6632c56b3c6714827950e25e7d26af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953187"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>A Cloud Explorer for Visual Studio for Azure IoT Hub eszközkezelésének használata

![Végpontok között diagram](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[A Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) egy hasznos Visual Studio-bővítmény, amely lehetővé teszi az Azure-erőforrások megtekintését, tulajdonságaik vizsgálatát és a Visual Studio kulcsfontosságú fejlesztői műveletek elvégzését. Jön a felügyeleti lehetőségek, amelyek segítségével különböző feladatok at.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Felügyeleti beállítás          | Tevékenység                    |
|----------------------------|--------------------------------|
| Közvetlen metódusok             | Az eszköz megfelelően működjön, például indítsa el vagy állítsa le az üzenetek küldését, illetve indítsa újra az eszközt.                                        |
| Ikereszköz olvasása           | Az eszköz jelentett állapotának lerendőrsége. Például a készülék jelenti, hogy a LED villog.                                    |
| Ikereszköz frissítése         | Helyezzen egy eszközt bizonyos állapotokba, például egy LED-et zöldre, vagy a telemetriai küldési időközt 30 percre.         |
| Felhőből eszközre irányuló üzenetek   | Értesítések küldése egy eszközre. Például: "Nagyon valószínű, hogy ma esni fog. Ne felejts el esernyőt hozni."              |

A különbségek részletesebb magyarázatát és a beállítások használatával kapcsolatos útmutatást az [Eszközről a felhőbe irányuló kommunikációra vonatkozó útmutatásban](iot-hub-devguide-d2c-guidance.md) és [a felhőből az eszközre irányuló kommunikációra vonatkozó útmutatásban talál.](iot-hub-devguide-c2d-guidance.md)

Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit, például a metaadatokat, konfigurációkat és állapotokat tárolják. Az IoT Hub minden olyan eszközhöz, amely csatlakozik, egy ikereszköz marad. Az ikereszközökről az Első lépések az [ikereszközökkel című témakörben](iot-hub-node-node-twin-getstarted.md)talál további információt.

## <a name="what-you-learn"></a>Ismertetett témák

Ebből a cikkből megtudhatja, hogyan használhatja a Visual Studio Felhőkezelőjét különböző felügyeleti lehetőségekkel a fejlesztői számítógépen.

## <a name="what-you-do"></a>Mit csinálsz

Ebben a cikkben futtassa a Cloud Explorer for Visual Studio alkalmazást különböző felügyeleti beállításokkal.

## <a name="what-you-need"></a>Mi szükséges

A következő előfeltételekre van szüksége:

- Aktív Azure-előfizetés.

- Egy Azure IoT Hub az előfizetés alatt.

- Microsoft Visual Studio 2017 Update 9 vagy újabb verzió. Ez a cikk a [Visual Studio 2017-et vagy a Visual Studio 2019-et](https://www.visualstudio.com/vs/)használja.

- A Visual Studio Installer Felhőkezelő-összetevője, amely alapértelmezés szerint az Azure Workload szolgáltatással van kiválasztva.

## <a name="update-cloud-explorer-to-latest-version"></a>A Cloud Explorer frissítése a legújabb verzióra

A Visual Studio Installer for Visual Studio 2017 Cloud Explorer összetevője csak az eszközről a felhőbe és a felhőből az eszközre irányuló üzenetek figyelését támogatja. A Visual Studio 2017 használatához töltse le és telepítse a legújabb [Cloud Explorert.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)

## <a name="sign-in-to-access-your-hub"></a>Bejelentkezés a hub eléréséhez

1. A Visual Studio alkalmazásban válassza a**Felhőkezelő** **megtekintése** > lehetőséget a Cloud Explorer megnyitásához.

1. Az előfizetések megjelenítéséhez válassza a Fiókkezelés ikont.

    ![Fiókkezelés ikon](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Ha be van jelentkezve az Azure-ba, a fiókok jelennek meg. Ha először szeretne bejelentkezni az Azure-ba, válassza a **Fiók hozzáadása lehetőséget.**

1. Válassza ki a használni kívánt Azure-előfizetéseket, és válassza **az Alkalmaz**lehetőséget.

1. Bontsa ki az előfizetést, majd **bontsa ki az IoT Hubs csomópontot.**  Az egyes hubok alatt láthatja az adott hubeszközeit. A kezelési beállítások eléréséhez kattintson a jobb gombbal egy eszközre.

    ![Kezelési beállítások](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Közvetlen metódusok

Közvetlen módszerek használatához tegye a következő lépéseket:

1. Kattintson a jobb gombbal az eszközre, és válassza **az Eszközközvetlen metódus meghívása parancsot.**

1. Írja be a metódus nevét és hasznos adatát a **Közvetlen metódus meghívása mezőbe,** majd kattintson **az OK gombra.**

    Az eredmények a **Kimenet**ben jelennek meg.

## <a name="update-device-twin"></a>Ikereszköz frissítése

Az ikereszköz szerkesztéséhez tegye a következő lépéseket:

1. Kattintson a jobb gombbal az eszközre, és válassza **az Ikereszköz szerkesztése**parancsot.

   Megnyílik egy **azure-iot-device-twin.json** fájl az ikereszköz tartalmával.

1. Az **azure-iot-device-twin.json** fájlban módosításokat végzett **címkékvagy** **properties.desired** mezők között.

1. Nyomja le a **Ctrl+S billentyűkombinációt** az ikereszköz frissítéséhez.

   Az eredmények a **Kimenet**ben jelennek meg.

## <a name="send-cloud-to-device-messages"></a>Üzenetküldés a felhőből az eszközökre

Ha üzenetet szeretne küldeni az IoT Hubról az eszközére, kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal az eszközre, és válassza **a C2D-üzenet küldése parancsot.**

1. Írja be az üzenetet a **C2D küldése üzenetbe,** és válassza **az OK gombot.**

   Az eredmények a **Kimenet**ben jelennek meg.

## <a name="next-steps"></a>További lépések

Megtanulta, hogyan használhatja a Cloud Explorer t a Visual Studio számára különböző felügyeleti beállításokkal.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
