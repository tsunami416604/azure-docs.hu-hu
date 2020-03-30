---
title: Architekturális fogalmak az Azure IoT Central - Energy - Microsoft dokumentumok
description: Ez a cikk az Azure IoT Central energy alkalmazássablon architektúrájával kapcsolatos legfontosabb fogalmakat ismerteti
author: op-ravi
ms.author: omravi
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 8f3772c1d65780337c421cfaaa7b70d7ac7186cf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77024313"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Azure IoT Central – intelligens mérőalkalmazás architektúrája



Ez a cikk áttekintést nyújt az intelligens mérőműszer figyelési alkalmazássablon architektúrájának áttekintéséről. Az alábbi ábrán egy általánosan használt architektúra intelligens mérő alkalmazás az Azure-ban az IoT Central platform használatával.

> [!div class="mx-imgBorder"]
> ![intelligens mérő architektúra](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

Az architektúra az alábbi összetevőkből áll. Előfordulhat, hogy egyes megoldásokhoz nem szükséges az összes itt felsorolt összetevő.

## <a name="smart-meters-and-connectivity"></a>Intelligens fogyasztásmérők és csatlakoztathatóság 

Az intelligens fogyasztásmérő az egyik legfontosabb eszköz az összes energiaeszköz között. Rögzíti és közli az energiafogyasztással kapcsolatban a közműveket a figyelésése és egyéb használati esetek, például a számlázás és a keresleti válasz érdekében. A mérő típusa alapján csatlakozhat az IoT Centralhoz átjárók vagy más köztes eszközök vagy rendszerek, például peremhálózati eszközök és fejvégrendszerek használatával. IoT Central eszközhidat hozhat létre az eszközök csatlakoztatásához, amelyek nem csatlakoztathatók közvetlenül. Az IoT Central eszközhíd egy nyílt forráskódú megoldás, és a teljes részleteket [itt](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge)találja. 


## <a name="iot-central-platform"></a>IoT Central platform

Az Azure IoT Central egy olyan platform, amely leegyszerűsíti az IoT-megoldás fejlesztését, és segít csökkenteni az IoT-kezelés, a műveletek és a fejlesztés terheit és költségeit. Az IoT Central segítségével könnyedén csatlakozhat, figyelheti és kezelheti az eszközök internetes hálózatát (IoT) eszközeit. Miután csatlakoztatta az intelligens mérőórákat az IoT Centralhoz, az alkalmazássablon beépített funkciókat, például eszközmodelleket, parancsokat és irányítópultokat használ. Az alkalmazássablon az IoT Central storage-ot is használja a meleg útvonal-forgatókönyvekhez, például a közel valós idejű mérőadatok figyeléséhez, elemzéséhez, szabályaihoz és vizualizációhoz. 


## <a name="extensibility-options-to-build-with-iot-central"></a>Az IoT Central segítségével létrehozandó bővíthetőségi lehetőségek
Az IoT Central platform két bővíthetőségi lehetőséget biztosít: folyamatos adatexportálás (CDE) és API-k. Az ügyfelek és a partnerek választhatnak a lehetőségek közül, hogy egyedi igényeknek megfelelően szabják testre megoldásaikat. Például az egyik partnerünk konfigurálta a CDE-t az Azure Data Lake Storage (ADLS) segítségével. Az ADLS-t hosszú távú adatmegőrzési és más hidegút-tárolási forgatókönyvekhez használják, például kötegelt feldolgozási, naplózási és jelentéskészítési célokra. 

## <a name="next-steps"></a>További lépések

* Most, hogy már megismerkedett az architektúrával, [hozzon létre ingyenes intelligens mérőalkalmazást](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Ha többet szeretne megtudni az IoT Centralról, olvassa el az [IoT Central áttekintése című témakört.](https://docs.microsoft.com/azure/iot-central/)
