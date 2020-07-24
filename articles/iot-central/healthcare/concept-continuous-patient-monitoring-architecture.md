---
title: Folyamatos fekvőbeteg-figyelési architektúra az Azure IoT Centralban | Microsoft Docs
description: Ismerje meg a folyamatos beteg monitorozási megoldás architektúráját.
author: philmea
ms.author: philmea
ms.date: 7/23/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 0032f341330ad394241806a4fe61add530253f09
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87116867"
---
# <a name="continuous-patient-monitoring-architecture"></a>A folyamatos betegfigyelés architektúrája



A folyamatos fekvőbeteg-figyelési megoldások a megadott alkalmazás-sablonnal, valamint az alább ismertetett architektúra használatával használhatók.

>[!div class="mx-imgBorder"] 
>![CPM-architektúra](media/cpm-architecture.png)

1. Bluetooth-kapcsolaton keresztül kommunikáló egészségügyi eszközök
1. A mobiltelefon-átjáró nem fogad adatokat és küld IoT Central
1. A beteg állapotának folyamatos exportálása a FHIR készült Azure API-ba&reg;
1. Gépi tanulás az interoperábilis adatkezelésen alapuló
1. FHIR-adatbázisra épülő Care Team-irányítópult

## <a name="details"></a>Részletek
Ez a szakasz részletesebben ismerteti az architektúra-diagram egyes részeit.

### <a name="ble-medical-devices"></a>Rendszeregészségügyi eszközök
Az egészségügyi IoT felhasznált számos orvosi eszköz a Bluetooth alacsony energiafogyasztású eszközök. Nem tudnak közvetlenül kommunikálni a felhővel, és át kell lépniük egy átjárón. Ez az architektúra egy mobiltelefonos alkalmazás használatát javasolja az átjáróként. 

### <a name="mobile-phone-gateway"></a>Mobile Phone-átjáró
A mobiltelefonos alkalmazás elsődleges funkciója az, hogy az orvostechnikai eszközökből származó adatok betöltését és az Azure-IoT Central való kommunikációját. Emellett az alkalmazás segítséget nyújthat a betegeknek egy eszköz beállításához és kiépítési folyamatához, és a személyes állapotadatok megtekintését is lehetővé teszik. Más megoldások is használhatnak táblaszámítógép-átjárót vagy statikus átjárót, ha egy kórházi helyiségben ugyanazt a kommunikációs folyamatot érik el. Létrehoztunk egy Android és iOS rendszerhez elérhető nyílt forráskódú minta Mobile-alkalmazást, amely kiindulási pontként használható az alkalmazás-fejlesztési erőfeszítések elindításához. További információ a IoT Central folyamatos beteg monitorozási Mobile App-mintáról: [Azure-minták](https://docs.microsoft.com/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/).

### <a name="export-to-azure-api-for-fhirreg"></a>Exportálás az Azure API-ba FHIR&reg;
Az Azure IoT Central HIPAA-kompatibilis és HITRUST &reg; minősítéssel rendelkezik, de előfordulhat, hogy a FHIR készült Azure API-ra is el szeretné küldeni a betegeknek szóló adatait. A [FHIR készült Azure API](../../healthcare-apis/overview.md) egy teljes körűen felügyelt, szabványon alapuló, megfelelő API a klinikai egészségügyi adataihoz, amely lehetővé teszi, hogy új összevonási rendszereket hozzon létre az állapotadatok adataival. Lehetővé teszi a gyors adatcserét a FHIR API-kon keresztül, és a felhőben felügyelt, szolgáltatásként nyújtott szolgáltatásként (Pásti) kínál támogatást. A IoT Central folyamatos adatexportálási funkcióinak használatával az Azure API for FHIR-hez az Azure [IoT-összekötőn](https://docs.microsoft.com/azure/healthcare-apis/iot-fhir-portal-quickstart)keresztül küldhet adatküldhető FHIR.

### <a name="machine-learning"></a>Gépi tanulás
Az adatösszesítés és a FHIR formátumba való fordítása után olyan gépi tanulási modelleket építhet ki, amelyek gazdagítják az elemzéseket, és lehetővé teszik az intelligensebb döntéshozatalt az ápolási csapat számára. A gépi tanulási modellek létrehozásához, betanításához és üzembe helyezéséhez különböző típusú szolgáltatások használhatók. Az Azure Machine learning-ajánlatokkal kapcsolatos további információkért tekintse meg a [Machine learning dokumentációját](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Szolgáltatói irányítópult
A FHIR készült Azure API-ban található, a páciensek elemzésére szolgáló irányítópultot felépítő, vagy közvetlenül integrálható EMR segít a csapatoknak a beteg állapotának megjelenítésében. A Care Teams ezen irányítópult használatával gondoskodhat a beavatkozást igénylő betegekről, és megfigyelheti a romlás korai előrejelző jeleit. Ha szeretné megtudni, hogyan hozhat létre Power BI valós idejű szolgáltatói irányítópultot, kövesse a [útmutató útmutatását.](howto-health-data-triage.md)

## <a name="next-steps"></a>További lépések
* [Ismerje meg, hogyan helyezheti üzembe a folyamatos beteg monitorozási alkalmazás sablonját](tutorial-continuous-patient-monitoring.md)