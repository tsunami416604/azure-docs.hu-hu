---
title: Egészségügyi megoldások létrehozása az Azure IoT Central-vel | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egészségügyi megoldásokat az Azure IoT Central alkalmazás-sablonok használatával.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: a5a8c8e52c4bebda4e22e592fefa3801449504e3
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027459"
---
# <a name="building-healthcare-solutions-with-azure-iot-central"></a>Egészségügyi megoldások kiépítése az Azure IoT Central 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ismerje meg, hogyan hozhat létre egészségügyi megoldásokat az Azure IoT Central alkalmazás-sablonok használatával.

## <a name="what-is-continuous-patient-monitoring-template"></a>Mi az a folyamatos beteg-figyelési sablon?

Az egészségügyi IoT terén a folyamatos beteg monitorozás az egyik legfontosabb segítő, amely csökkenti a visszafogadások kockázatát, hatékonyabban kezeli a krónikus betegségeket, és javítja a betegek eredményeit. A folyamatos beteg monitorozás két fő kategóriába osztható:

1. **Fekvőbeteg-figyelés**: az orvosi wearables és más kórházi eszközök használata esetén a Care Teams a beteg létfontosságú jeleinek és orvosi feltételeinek elküldését anélkül, hogy egy ápolót naponta többször is fel kellene vennie. Az ápolási csapatok tudomásul veszik, hogy a pácienseknek kritikus figyelmet kell fordítaniuk az értesítéseken keresztül, és hatékonyan rangsorolják az idejüket.
1. **Távoli beteg figyelése**: az orvosi wearables és a beteg által jelentett eredmények (profik) a kórházon kívüli betegek figyelésére, az ismételt beléptetés kockázata jelentősen csökkenthető. A krónikus betegeknek és a rehabilitációs betegeknek származó adatokat össze lehet gyűjteni annak biztosítására, hogy a betegek betartsák a gondozási terveket, és hogy a páciensek károsodásával kapcsolatos riasztások a kritikus fontosságúak legyenek.

Ez az alkalmazás-sablon a folyamatos beteg-figyelés mindkét kategóriájának megoldására használható. Az előnyök a következők:

* Zökkenőmentesen kapcsolódhat számos orvosi hordható IoT Central-példányhoz.
* Az eszközök monitorozása és kezelése, hogy azok egészségesek maradjanak.
* Az eszközökre vonatkozó egyéni szabályok létrehozása a megfelelő riasztások elindításához.
* Exportálja a beteg Health-adatait a FHIR készült Azure API-ra, amely egy megfelelő adattár.
* Exportálja az összesített betekintést meglévő vagy új üzleti alkalmazásokba, amelyek felhatalmazzák az ápoló csapatokat.

>[!div class="mx-imgBorder"] 
>![CPM-irányítópult](media/in-patient-dashboard.png)

## <a name="next-steps"></a>Következő lépések

A folyamatos beteg monitorozási megoldás létrehozásának megkezdéséhez:

* [Az alkalmazás sablonjának üzembe helyezése](tutorial-continuous-patient-monitoring.md)
* [Példa architektúrára](concept-continuous-patient-monitoring-architecture.md)