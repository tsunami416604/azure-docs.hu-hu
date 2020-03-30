---
title: Architekturális fogalmak az Azure IoT Central - Energy - Microsoft dokumentumok
description: Ez a cikk az Azure IoT Central architektúrájával kapcsolatos legfontosabb fogalmakat ismerteti
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 44171a08e69cfa058e0a9e75e3220fb996b7789d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77018006"
---
# <a name="azure-iot-central---solar-panel-app-architecture"></a>Azure IoT Central - napelemalkalmazás-architektúra




Ez a cikk áttekintést nyújt a napelem felügyeleti alkalmazás sablon architektúráról. Az alábbi ábrán egy általánosan használt architektúra napelem alkalmazás az Azure-ban az IoT Central platform használatával.

> [!div class="mx-imgBorder"]
> ![intelligens mérő architektúra](media/concept-iot-central-solar-panel/solar-panel-app-architecture.png)

Az architektúra az alábbi összetevőkből áll. Nem minden alkalmazáshoz szükséges az itt felsorolt összes összetevő.

## <a name="solar-panels-and-connectivity"></a>Napelemek és csatlakozás 

Napelemek az egyik jelentős megújuló energiaforrások. Attól függően, hogy a napelem típusát és beállítása, akkor csatlakoztassa akár átjárók vagy más köztes eszközök és saját rendszerek. Előfordulhat, hogy létre kell hoznia az IoT Central eszközhidat az eszközök csatlakoztatásához, amelyek nem csatlakoztathatók közvetlenül. Az IoT Central eszközhíd egy nyílt forráskódú megoldás, és a teljes részleteket [itt](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge)találja. 



## <a name="iot-central-platform"></a>IoT Central platform
Az Azure IoT Central egy olyan platform, amely leegyszerűsíti az IoT-megoldás fejlesztését, és segít csökkenteni az IoT-kezelés, a műveletek és a fejlesztés terheit és költségeit. Az IoT Central segítségével könnyedén csatlakozhat, figyelheti és kezelheti az eszközök internetes hálózatát (IoT) eszközeit. Miután csatlakoztatta a napelemeket az IoT Centralhoz, az alkalmazássablon beépített funkciókat használ, például eszközmodelleket, parancsokat és irányítópultokat. Az alkalmazássablon az IoT Central storage-ot is használja a meleg útvonal-forgatókönyvekhez, például a közel valós idejű mérőadatok figyeléséhez, elemzéséhez, szabályaihoz és vizualizációhoz.


## <a name="extensibility-options-to-build-with-iot-central"></a>Az IoT Central segítségével létrehozandó bővíthetőségi lehetőségek
Az IoT Central platform két bővíthetőségi lehetőséget biztosít: folyamatos adatexportálás (CDE) és API-k. Az ügyfelek és a partnerek választhatnak a lehetőségek közül, hogy egyedi igényeknek megfelelően szabják testre megoldásaikat. Például az egyik partnerünk konfigurálta a CDE-t az Azure Data Lake Storage (ADLS) segítségével. Az ADLS-t hosszú távú adatmegőrzési és más, a hideg út tárolási forgatókönyveihez használják, például kötegelt feldolgozáshoz, naplózáshoz és jelentéskészítési célokra. 

## <a name="next-steps"></a>További lépések

* Most, hogy már megtanulta az architektúrát, [hozzon létre napelem alkalmazást ingyen](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Ha többet szeretne megtudni az IoT Centralról, olvassa el az [IoT Central áttekintése című témakört.](https://docs.microsoft.com/azure/iot-central/)