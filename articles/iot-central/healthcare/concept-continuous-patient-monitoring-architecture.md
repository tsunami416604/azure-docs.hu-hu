---
title: Folyamatos fekvőbeteg-figyelési architektúra az Azure IoT Centralban | Microsoft Docs
description: Ismerje meg a folyamatos beteg monitorozási megoldás architektúráját.
author: philmea
ms.author: philmea
ms.date: 10/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: cf8cfc5fe75ff5364f812af034d772c7aadd891c
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027477"
---
# <a name="continuous-patient-monitoring-architecture"></a>Folyamatos kórházi monitorozási architektúra

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

A folyamatos beteg-figyelési megoldások a megadott alkalmazás-sablon használatával és az alább ismertetett architektúrával is használhatók.

>[!div class="mx-imgBorder"] 
>![CPM-architektúra](media/cpm-architecture.png)

1. Bluetooth-kapcsolaton keresztül kommunikáló orvosi eszközök
1. A mobiltelefon-átjáró nem fogad adatokat és küld IoT Central
1. A beteg állapotadatok folyamatos exportálása a FHIR készült Azure API-ba&reg;
1. Gépi tanulás az interoperábilis adatkezelésen alapuló
1. FHIR-adatbázisra épülő Care Team-irányítópult

## <a name="details"></a>Részletek
Ez a szakasz részletesebben ismerteti az architektúra-diagram egyes részeit.

### <a name="ble-medical-devices"></a>Rendszeregészségügyi eszközök
Az egészségügyi IoT felhasznált számos orvosi eszköz a Bluetooth alacsony energiafogyasztású eszközök, ami azt jelenti, hogy nem tudnak közvetlenül kommunikálni a felhővel, és át kell adni egy átjárót. Ez az architektúra egy mobiltelefonos alkalmazás használatát javasolja az átjáróként.

### <a name="mobile-phone-gateway"></a>Mobile Phone-átjáró
A mobiltelefonos alkalmazás elsődleges funkciója az, hogy az orvostechnikai eszközökből származó adatok betöltését és az Azure-IoT Central való kommunikációját. Emellett az alkalmazás segítséget nyújthat a betegeknek egy eszköz beállításához és kiépítési folyamatához, és a személyes állapotadatok megtekintését is lehetővé teszik. Más megoldások is használhatnak táblaszámítógép-átjárót vagy statikus átjárót, ha egy kórházi helyiségben ugyanazt a kommunikációs folyamatot érik el.

### <a name="export-to-azure-api-for-fhirreg"></a>Exportálás az Azure API-ba a FHIR&reg;
Az Azure IoT Central a HIPAA-kompatibilis és a HITRUST&reg; minősítéssel rendelkezik, de előfordulhat, hogy a FHIR Azure API-hoz is el kell küldenie a betegeknek szóló adatait. A [FHIR készült Azure API](../../healthcare-apis/overview.md) egy teljes körűen felügyelt, szabványon alapuló, megfelelő API a klinikai egészségügyi adataihoz, amely lehetővé teszi, hogy új összevonási rendszereket hozzon létre az állapotadatok adataival. Lehetővé teszi a gyors adatcserét a FHIR API-kon keresztül, és a felhőben felügyelt, szolgáltatásként nyújtott szolgáltatásként (Pásti) kínál támogatást. A IoT Central folyamatos adatexportálási funkcióinak használatával az Azure API-ra küldhet FHIR.

### <a name="machine-learning"></a>Gépi tanulás
Az adatösszesítés és a FHIR formátumba való fordítása után olyan gépi tanulási modelleket építhet ki, amelyek gazdagítják az elemzéseket, és lehetővé teszik az intelligensebb döntéshozatalt az ápolási csapat számára. A gépi tanulási modellek létrehozásához, betanításához és üzembe helyezéséhez számos különböző szolgáltatás használható. Az Azure Machine learning-ajánlatokkal kapcsolatos további információkért tekintse meg a [Machine learning dokumentációját](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Szolgáltatói irányítópult
A FHIR készült Azure API-ban található, a páciensek elemzésére szolgáló irányítópultot felépítő, vagy közvetlenül integrálható EMR segít a csapatoknak a beteg állapotának megjelenítésében. Az adatkezelési csapatok ezt az irányítópultot használhatják arra, hogy a beavatkozást igénylő betegeknek segítséget nyújtsanak, és megvessék a korai előrejelző jeleiket. Ha szeretné megtudni, hogyan hozhat létre Power BI valós idejű szolgáltatói irányítópultot, kövesse a [útmutató útmutatását.](howto-health-data-triage.md)

## <a name="next-steps"></a>Következő lépések
* [Ismerje meg, hogyan helyezheti üzembe a folyamatos beteg monitorozási alkalmazás sablonját](tutorial-continuous-patient-monitoring.md)