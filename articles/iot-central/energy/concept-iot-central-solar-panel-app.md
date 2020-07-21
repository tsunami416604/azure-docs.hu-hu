---
title: Építészeti fogalmak az Azure IoT Centralban – napelemes panel | Microsoft Docs
description: Ez a cikk bemutatja az Azure architektúrával kapcsolatos főbb fogalmakat IoT Central
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: f09a8cfae9b0f12df2ea76ecc216b1b2eb66e9ff
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86511857"
---
# <a name="azure-iot-central---solar-panel-app-architecture"></a>Azure IoT Central – napelemes alkalmazások architektúrája




Ez a cikk áttekintést nyújt a napelemes figyelési alkalmazás sablonjának architektúráról. Az alábbi ábra az Azure napelemes alkalmazásának általánosan használt architektúráját mutatja be IoT Central platform használatával.

> [!div class="mx-imgBorder"]
> ![intelligens fogyasztásmérő architektúrája](media/concept-iot-central-solar-panel/solar-panel-app-architecture.png)

Az architektúra az alábbi összetevőkből áll. Nem minden alkalmazáshoz szükséges az itt felsorolt összes összetevő.

## <a name="solar-panels-and-connectivity"></a>Napelemek és kapcsolatok 

A napelemes panelek a megújuló energiaforrások egyik jelentős forrása. A napelemek típusától és beállítástól függően az átjárók vagy más köztes eszközök és a szabadalmaztatott rendszerek használatával is csatlakoztatható. Előfordulhat, hogy az eszközök csatlakoztatásához létre kell hoznia IoT Central Device Bridge eszközt, amely nem csatlakoztatható közvetlenül. A IoT Central Device Bridge egy nyílt forráskódú megoldás, és [itt](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge)találja a teljes részleteket. 



## <a name="iot-central-platform"></a>IoT Central platform
Az Azure IoT Central egy olyan platform, amely leegyszerűsíti a IoT-megoldás felépítését, és segít csökkenteni a IoT-kezelés, a műveletek és a fejlesztés terheit és költségeit. A IoT Central használatával könnyedén csatlakoztathatók, figyelhetők és kezelhetők a eszközök internetes hálózata (IoT) eszközei a skálán. A napelemek IoT Centralhoz való csatlakoztatása után az alkalmazás-sablon beépített funkciókat használ, mint például az eszközök modelljei, parancsai és irányítópultok. Az alkalmazás sablonja a IoT Central tárolót is használja a meleg elérési utakhoz, például a közel valós idejű mérési adatok figyeléséhez, elemzéséhez, szabályaihoz és vizualizációhoz.


## <a name="extensibility-options-to-build-with-iot-central"></a>Bővíthetőségi lehetőségek a IoT Central létrehozásához
A IoT Central platform két bővíthetőségi lehetőséget kínál: folyamatos adatexportálást (CDE) és API-kat. Az ügyfelek és a partnerek választhatnak ezek között a lehetőségek közül, hogy testre szabják az adott igényeknek megfelelő megoldásokat. Például az egyik partner konfigurálta az Azure Data Lake Storage (ADLS) CDE. A ADLS használják a hosszú távú adatmegőrzésre és más, a lassú elérésű útvonalak tárolási helyzetére, például a kötegelt feldolgozásra, a naplózásra és a jelentéskészítésre. 

## <a name="next-steps"></a>Következő lépések

* Most, hogy megismerte az architektúrát, [hozzon létre ingyen a Solar panel alkalmazást](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* További információ a IoT Centralről: [IoT Central áttekintése](https://docs.microsoft.com/azure/iot-central/)