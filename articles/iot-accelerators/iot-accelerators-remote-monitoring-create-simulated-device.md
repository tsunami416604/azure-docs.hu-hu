---
title: Eszköz szimulálása az IoT távoli figyelés – Azure |} A Microsoft Docs
description: Ez az útmutató bemutatja, hogyan a készülékszimulátort a távoli figyelési megoldásgyorsító használandó.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 81efd9dc5d33ed23574b1cb66f26ccb444d5a3ff
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180534"
---
# <a name="create-and-test-a-new-simulated-device"></a>Hozhat létre és tesztelhet egy új szimulált eszköz

A távoli figyelési megoldásgyorsító meghatározhatja a saját szimulált eszközökhöz. Ez a cikk bemutatja, hogyan határozza meg az új eszköz szimulált villanykörte, és ezután helyben tesztelheti. A megoldásgyorsító például hőmérsékletű és tehergépkocsik szimulált eszközt magában foglal. Azonban megadhat saját szimulált eszközök az IoT-megoldások teszteléséhez, valódi eszközök üzembe helyezése előtt.

> [!NOTE]
> Ez a cikk ismerteti, hogyan használható a szimulált eszközök az eszköz szimulálása szolgáltatásban üzemeltetett. Ha szeretne létrehozni egy igazi eszközön, tekintse meg [az eszköz csatlakoztatása a távoli figyelési megoldásgyorsító](iot-accelerators-connecting-devices.md).

Ez az útmutató bemutatja, hogyan szabhatja testre az eszköz szimulálása mikroszolgáltatás. A mikroszolgáltatások a távoli figyelési megoldásgyorsító részét képezi. Mutatja be az eszköz szimulálása képességeket, ez az útmutató két forgatókönyvet használja fel a Contoso IoT-alkalmazást:

[!INCLUDE [iot-solution-accelerators-create-device](../../includes/iot-solution-accelerators-create-device.md)]

## <a name="next-steps"></a>További lépések

Ez az útmutató láthatta, hogyan hozhat létre egyéni szimulált eszköz típusok, és tesztelje le azokat az eszköz szimulálása mikroszolgáltatások helyi futtatásával.

Megtudhatja, hogyan helyezhet üzembe az egyéni szimulált eszköz típusa, akkor a javasolt következő lépésre az [távoli figyelési megoldásgyorsító](iot-accelerators-remote-monitoring-deploy-simulated-device.md).
