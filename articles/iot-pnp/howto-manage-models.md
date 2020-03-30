---
title: IoT Plug and Play előzetes modellek kezelése a tárházban| Microsoft Dokumentumok"
description: Az Azure Certified for IoT portal, az Azure CLI és a Visual Studio-kód használatával a tárházban lévő eszközképesség-modellek kezelése.
author: Philmea
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: how-to
ms.date: 12/26/2019
ms.author: philmea
ms.openlocfilehash: 78406175090521af70381f61f4e33dfb6c35ac8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159149"
---
# <a name="manage-models-in-the-repository"></a>Modellek kezelése a tárházban

Az IoT Plug and Play Preview modelltártár eszközképességi modelleket és interfészeket tárol. A tárház a modelleket és a felületeket felderíthetővé és a megoldásfejlesztők által felderíthetővé teszi.

A tárház kezeléséhez három eszköz használható:

- Az Azure Certified for IoT portál
- Azure CLI
- Visual Studio Code

## <a name="model-repositories"></a>Modelltárolók

Kétféle modelltárház létezik az eszközképességi modellek és felületek tárolására:

- Az [Azure Certified for IoT eszközkatalógusban](https://aka.ms/iotdevcat)egyetlen _nyilvános tárház_ található, amely tárolja az eszközök eszközképességi modelljeit és felületeit. Ez a tárház a Microsoft Partners által közzétett [közös interfészeket,](./concepts-common-interfaces.md) [dcm-eket és interfészeket](./howto-onboard-portal.md)is tárolja. Ha meg szeretné tudni, hogyan hitelesítheti az eszközt, és hogyan adhat hozzá eszközképességi modelljét a nyilvános tárházhoz, olvassa el az [IoT Plug and Play eszköz hitelesítése című oktatóanyagcímű témakört.](./tutorial-certification-test.md)
- Több _vállalati adattárak_. A vállalati tárház automatikusan létrejön a szervezet számára, amikor [az Azure Certified for IoT portálra kerül.](./howto-onboard-portal.md) A vállalati tárház segítségével tárolhatja az eszköz képességi modelljeit és felületeit a fejlesztés és a tesztelés során.

## <a name="azure-certified-for-iot-portal"></a>Azure Certified for IoT portál

Az [Azure Certified for IoT portálon](https://preview.catalog.azureiotsolutions.com)a következő feladatokat végezheti el:

- [Az IoT-eszköz hitelesítési folyamatának befejezése.](./tutorial-certification-test.md)
- IoT Plug and Play eszközképesség-modellek keresése. Ezekkel a modellekkel [gyorsan hozhat létre IoT-kompatibilis eszközöket, és integrálhatja őket megoldásokkal.](./quickstart-connect-pnp-device-solution-node.md)

## <a name="azure-cli"></a>Azure CLI

Az Azure CLI parancsokat biztosít az iot Plug and Play nyilvános és vállalati modelladattárakban az eszközképességi modellek és felületek kezeléséhez. További információkért tekintse meg az [Azure IoT-bővítmény telepítése és használata az Azure CLI](./howto-install-pnp-cli.md) útmutató.

## <a name="visual-studio-code"></a>Visual Studio Code

A **Modelltárház** nézet megnyitása a Visual Studio-kódban.

1. Nyissa meg a Visual Studio-kódot, használja **a Ctrl+Shift+P**billentyűkombinációt, és válassza az **IoT Plug and Play: Open Model Repository parancsot.**

1. Választhat, hogy **megnyitja a nyilvános modell tárház** vagy **nyílt szervezeti modell tárház**. A vállalati modell tárház, meg kell adnia a modell tárház kapcsolati karakterlánc. Ez a kapcsolati karakterlánc az [Azure Certified for IoT portalon](https://preview.catalog.azureiotsolutions.com) található a **vállalati tárház** **Kapcsolatkarakterláncok** lapján.

1. Egy új lap megnyitja a **Modelltárház** nézetet.

    Ebben a nézetben eszközképességi modelleket és felületeket adhat hozzá, tölthet le és törölhet. Szűrő segítségével megkeresheti a lista bizonyos elemeit.

1. A vállalati modelltárház és a nyilvános modelltárház közötti váltáshoz használja a **Ctrl+Shift+P**billentyűkombinációt, és válassza az **IoT Plug and Play: Sign out Model Repository parancsot.** Ezután használja újra az **IoT Plug and Play: Open Model Repository** parancsot.

> [!NOTE]
> A VS-kódban a nyilvános modelltárház csak olvasható. A Microsoft Partners frissítheti a nyilvános adattárat az [Azure Certified for IoT portálon.](https://preview.catalog.azureiotsolutions.com)

## <a name="next-steps"></a>További lépések

A javasolt következő lépés az [IoT Plug and Play eszköz hitelesítésre való beküldése.](tutorial-certification-test.md)
