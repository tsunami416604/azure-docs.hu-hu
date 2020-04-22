---
title: Azure IoT-eszközkezelés az Azure IoT-eszközök vscode-hoz
description: Használja az Azure IoT Tools for Azure IoT Hub eszközfelügyeleti szabályzatát, amely a közvetlen módszereket és a Twin kívánt tulajdonságkezelési beállításait tartalmazza.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: d85e0e967dd802a77ccbc11b884d7a9f2891524d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81688094"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Az Azure IoT-eszközök használata a Visual Studio-kódhoz az Azure IoT Hub eszközkezeléséhez

![Végpontok között diagram](media/iot-hub-get-started-e2e-diagram/2.png)

[Az Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) egy hasznos Visual Studio-kódbővítmény, amely megkönnyíti az IoT Hub-kezelés és az IoT-alkalmazások fejlesztését. Jön a felügyeleti lehetőségek, amelyek segítségével különböző feladatok at.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Felügyeleti beállítás          | Tevékenység                    |
|----------------------------|--------------------------------|
| Közvetlen metódusok             | Az eszköz megfelelően működjön, például indítsa el vagy állítsa le az üzenetek küldését, illetve indítsa újra az eszközt.                                        |
| Ikereszköz olvasása           | Az eszköz jelentett állapotának lerendőrsége. Például a készülék jelenti, hogy a LED villog.                                    |
| Ikereszköz frissítése         | Helyezzen egy eszközt bizonyos állapotokba, például egy LED-et zöldre, vagy a telemetriai küldési időközt 30 percre.         |
| Felhőből eszközre irányuló üzenetek   | Értesítések küldése egy eszközre. Például: "Nagyon valószínű, hogy ma esni fog. Ne felejts el esernyőt hozni."              |

A különbségek részletesebb magyarázatát és a beállítások használatával kapcsolatos útmutatást az [Eszközről a felhőbe irányuló kommunikációra vonatkozó útmutatásban](iot-hub-devguide-d2c-guidance.md) és [a felhőből az eszközre irányuló kommunikációra vonatkozó útmutatásban talál.](iot-hub-devguide-c2d-guidance.md)

Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit (metaadatokat, konfigurációkat és állapotokat) tárolják. Az IoT Hub minden olyan eszközhöz, amely csatlakozik, egy ikereszköz marad. Az ikereszközökről az Első lépések az [ikereszközökkel című témakörben](iot-hub-node-node-twin-getstarted.md)talál további információt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>Ismertetett témák

Az Azure IoT Tools for Visual Studio Code segítségével különböző felügyeleti lehetőségeket kínál a fejlesztői gépen.

## <a name="what-you-do"></a>Mit csinálsz

Futtassa az Azure IoT-eszközöket a Visual Studio-kódhoz különböző felügyeleti lehetőségekkel.

## <a name="what-you-need"></a>Mi szükséges

* Aktív Azure-előfizetés.
* Egy Azure IoT hub az előfizetés alatt.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT-eszközök a VS-kódhoz,](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) vagy másolja ezt`vscode:extension/vsciot-vscode.azure-iot-tools`az URL-címet, és illessze be egy böngészőablakba: .

## <a name="sign-in-to-access-your-iot-hub"></a>Bejelentkezés az IoT-központ eléréséhez

1. A VS-kód **Explorer** nézetében bontsa ki az **Azure IoT Hub-eszközök** szakaszt a bal alsó sarokban.

2. Kattintson **az IoT-központ kijelölése** parancsra a helyi menüben.

3. Egy előugró ablak jelenik meg a jobb alsó sarokban, amely lehetővé teszi, hogy először jelentkezzen be az Azure-ba.

4. Miután bejelentkezik, megjelenik az Azure-előfizetési lista, majd válassza az Azure Subscription és az IoT Hub lehetőséget.

5. Az eszközlista néhány másodpercen belül megjelenik az **Azure IoT Hub Devices** lapon.

   > [!Note]
   > A telepítést elvégezheti a **Set IoT Hub Connection String** (IoT Hub kapcsolati sztring beállítása) elemre kattintva is. Adja meg az IoT-központ **iothubowner** házirend-kapcsolati karakterláncát, amelyhez az IoT-eszköz csatlakozik az előugró ablakban.

## <a name="direct-methods"></a>Közvetlen metódusok

1. Kattintson a jobb gombbal az eszközre, és válassza **a Közvetlen metódus meghívása parancsot.** 

2. Írja be a metódus nevét és hasznos adatát a beviteli mezőbe.

3. Az eredmények az **AZURE** > **Azure IoT Hub** nézetben jelennek meg.

## <a name="read-device-twin"></a>Ikereszköz olvasása

1. Kattintson a jobb gombbal az eszközre, és válassza **az Ikereszköz szerkesztése**parancsot. 

2. Egy **azure-iot-device-twin.json** fájl nyílik meg az ikereszköz tartalmával.

## <a name="update-device-twin"></a>Ikereszköz frissítése

1. A **címkék** vagy a **properties.desired** mező szerkesztése.

2. Kattintson a jobb gombbal az **azure-iot-device-twin.json** fájlra.

3. Válassza **az Ikereszköz frissítése** lehetőséget az ikereszköz frissítéséhez.

## <a name="send-cloud-to-device-messages"></a>Üzenetküldés a felhőből az eszközökre

Ha üzenetet szeretne küldeni az IoT hubról az eszközére, kövesse az alábbi lépéseket:
 
1. Kattintson a jobb gombbal az eszközre, és válassza **a C2D-üzenet küldése az eszközre parancsot.** 

2. Írja be az üzenetet a beviteli mezőbe.

3. Az eredmények az **AZURE** > **Azure IoT Hub** nézetben jelennek meg.

## <a name="next-steps"></a>További lépések

Megtanulta, hogyan használhatja az Azure IoT Tools bővítményt a Visual Studio-kódhoz különböző felügyeleti beállításokkal.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
