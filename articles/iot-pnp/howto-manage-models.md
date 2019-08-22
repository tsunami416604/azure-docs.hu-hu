---
title: IoT kezelése Plug and Play előnézeti modellek az adattárban | Microsoft Docs "
description: Az eszköz képesség modelljeinek kezelése az adattárban az Azure Certified for IoT portál, az Azure CLI és a Visual Studio Code használatával.
author: YasinMSFT
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: yahajiza
ms.openlocfilehash: ec36bef9c2b65a5c88a52621bbf3a1db708c235c
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880500"
---
# <a name="manage-models-in-the-repository"></a>Modellek kezelése a tárházban

A IoT Plug and Play előzetes modell tárháza az eszköz képességeinek modelljeit és felületeit tárolja. A tárház lehetővé teszi, hogy a modellek és felületek felderíthetők legyenek a megoldások fejlesztői számára.

Három eszköz használható az adattár kezeléséhez:

- Az Azure Certified for IoT portál
- Azure CLI
- Visual Studio Code

## <a name="model-repositories"></a>Modell adattárai

Az eszköz-képesség modellek és felületek tárolásához kétféle modell-tárház létezik:

- Létezik egy _nyilvános tárház_ , amely az eszköz képességeinek modelljeit és felületeit tárolja az [Azure Certified for IoT Device Catalog](https://aka.ms/iotdevcat)eszközön. Ez a tárház a [Microsoft-partnerek által közzétett](./howto-onboard-portal.md) [általános interfészeket](./concepts-common-interfaces.md) és DCMs és illesztőket is tárolja. Ha szeretné megtudni, hogyan hitelesítheti az eszközt, és hogyan adhatja hozzá az eszköz képességeinek modelljét a nyilvános tárházhoz, tekintse meg a [IoT Plug and Play eszköz tanúsítását](./tutorial-certification-test.md)ismertető oktatóanyagot.
- Több _vállalati tárház_is rendelkezésre áll. A céges tárház automatikusan létrejön a szervezet számára, amikor bevezeti [Az Azure Certified for IoT portált](./howto-onboard-portal.md). A vállalati adattár segítségével az eszköz képességeinek modelljeit és felületeit a fejlesztés és a tesztelés során is tárolhatja.

## <a name="azure-certified-for-iot-portal"></a>Az Azure Certified for IoT portál

Az [Azure Certified for IoT portálon](https://preview.catalog.azureiotsolutions.com)a következő feladatokat végezheti el:

- [Fejezze be a IoT-eszköz minősítési folyamatát](./tutorial-certification-test.md).
- Keresse meg a IoT Plug and Play eszköz képességeinek modelljeit. Ezekkel a modellekkel [gyorsan hozhat létre IoT-kompatibilis eszközöket, és integrálhatja azokat megoldásokkal](./quickstart-connect-pnp-device-solution.md).

## <a name="azure-cli"></a>Azure CLI

Az Azure CLI lehetővé teszi az eszköz-képességi modellek és felületek felügyeletét a IoT Plug and Play a nyilvános és a vállalati modell adattárakban.

## <a name="visual-studio-code"></a>Visual Studio Code

A **modell tárház** nézetének megnyitása a Visual Studio Code-ban.

1. Nyissa meg a Visual Studio Code-ot, használja a **CTRL + SHIFT + P billentyűkombinációt**, írja be és válassza **a IoT Plug and Play: Nyissa meg**a modell adattárát.

1. Dönthet úgy, hogy megnyithatja a **nyilvános modell tárházát** vagy megnyithatja a **szervezeti modell adattárát**. A vállalati modell tárháza esetében meg kell adnia a modell adattárához tartozó kapcsolódási karakterláncot. Ez a [IoT](https://preview.catalog.azureiotsolutions.com) -portálon található, a **vállalati tárházhoz**tartozó **kapcsolatok karakterláncok** lapján megtalálhatja ezt a kapcsolatokat megadó karakterláncot.

1. Egy új lapon megnyílik a **modell tárházának** nézete.

    Ez a nézet eszköz-képességi modellek és felületek hozzáadására, letöltésére és törlésére használható. Egy szűrő használatával megkeresheti a listában szereplő egyes elemeket.

1. A vállalati modell tárháza és a nyilvános modell tárháza közötti váltáshoz használja a **CTRL + SHIFT + P**billentyűkombinációt **, majd írja be a IoT Plug and Play: Kijelentkezési modell**tárháza. Ezután használja a **IoT Plug and Play: Nyissa meg** újra a Model adattár parancsát.

> [!NOTE]
> A VS Code-ban a nyilvános modell tárháza csak olvasható. A Microsoft partnerei frissíthetik a nyilvános tárházat az [Azure Certified for IoT portálon](https://preview.catalog.azureiotsolutions.com).

## <a name="next-steps"></a>További lépések

A következő lépés azt ismerteti, hogyan lehet beküldeni [egy IoT Plug and Play eszközét minősítésre](tutorial-certification-test.md).
