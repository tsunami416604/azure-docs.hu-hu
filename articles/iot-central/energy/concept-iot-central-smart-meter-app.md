---
title: Építészeti fogalmak az Azure IoT Centralban – energia | Microsoft Docs
description: Ez a cikk az Azure IoT Central Energy app-sablon architektúrájának főbb fogalmait ismerteti
author: op-ravi
ms.author: omravi
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 8272764e967fccfbfa88d54e688f9d7aaf4e0917
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027684"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Azure IoT Central – intelligens fogyasztásmérő alkalmazás architektúrája

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ez a cikk áttekintést nyújt az intelligens fogyasztásmérő monitorozási alkalmazás sablonjának architektúráról. Az alábbi ábra egy általánosan használt architektúrát mutat be az Azure-beli Smart Meter-alkalmazáshoz IoT Central platform használatával.

[!div class="mx-imgBorder"]
![intelligens fogyasztásmérő architektúrája](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

Az architektúra az alábbi összetevőkből áll. Előfordulhat, hogy egyes megoldások nem igénylik az itt felsorolt összes összetevőt.

## <a name="smart-meters-and-connectivity"></a>Intelligens mérőórák és kapcsolat 

Egy intelligens fogyasztásmérő az összes energiaellátási eszköz egyik legfontosabb eszköze. Az energiafogyasztással kapcsolatos adatokat rögzíti és közli a segédprogramokkal a figyelési és más használati esetekre, például a számlázásra és az igényre adott válaszra vonatkozóan. A mérési típus alapján a IoT Central átjárón keresztül, vagy más köztes eszközökön vagy rendszereken, például az Edge-eszközökön és a fő rendszereken keresztül is csatlakozhat. Hozzon létre IoT Central Device Bridge-et az eszközök csatlakoztatásához, amelyek nem csatlakoztathatók közvetlenül. A IoT Central Device Bridge egy nyílt forráskódú megoldás, és [itt](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge)találja a teljes részleteket. 


## <a name="iot-central-platform"></a>IoT Central platform

Az Azure IoT Central egy olyan platform, amely leegyszerűsíti a IoT-megoldás felépítését, és segít csökkenteni a IoT-kezelés, a műveletek és a fejlesztés terheit és költségeit. A IoT Central használatával könnyedén csatlakoztathatók, figyelhetők és kezelhetők a eszközök internetes hálózata (IoT) eszközei a skálán. Az intelligens mérőszámok IoT Centralhoz csatlakoztatása után az alkalmazás-sablon beépített funkciókat használ, mint például az eszközök modelljei, parancsai és irányítópultok. Az alkalmazás sablonja a IoT Central tárolót is használja a meleg elérési utakhoz, például a közel valós idejű mérési adatok figyeléséhez, elemzéséhez, szabályaihoz és vizualizációhoz. 


## <a name="extensibility-options-to-build-with-iot-central"></a>Bővíthetőségi lehetőségek a IoT Central létrehozásához
A IoT Central platform két bővíthetőségi lehetőséget kínál: folyamatos adatexportálást (CDE) és API-kat. Az ügyfelek és a partnerek választhatnak ezek között a lehetőségek közül, hogy testre szabják az adott igényeknek megfelelő megoldásokat. Például az egyik partner konfigurálta az Azure Data Lake Storage (ADLS) CDE. A ADLS használják a hosszú távú adatmegőrzésre és más, a lassú elérésű útvonalak tárolási helyzetére, az ilyen kötegelt feldolgozásra, a naplózásra és a jelentéskészítésre. 

## <a name="next-steps"></a>Következő lépések

* Most, hogy megismerte az architektúrát, [ingyenes Smart Meter-alkalmazást készíthet](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* További információ a IoT Centralről: [IoT Central áttekintése](https://docs.microsoft.com/azure/iot-central/)
