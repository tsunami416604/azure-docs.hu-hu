---
title: Az Azure IoT Connector megtekintése és konfigurálása a FHIR (előzetes verzió) Mérőszámokhoz
description: Ez a cikk az Azure IoT Connector FHIR (előzetes verzió) metrikáinak megjelenítését és konfigurálását ismerteti
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/29/2020
ms.author: jasteppe
ms.openlocfilehash: 76166fc5c525d36474a585179472e93b22dad647
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133624"
---
# <a name="view-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Az Azure IoT Connector megtekintése és konfigurálása a FHIR (előzetes verzió) Mérőszámokhoz 

Ebből a cikkből megtudhatja, hogyan tekintheti meg és konfigurálhatja az Azure IoT Connectort az FHIR * Mérőszámokhoz. 

> [!TIP]
> Kövesse az [Azure IoT CONNECTOR FHIR (előzetes verzió) metrikáinak exportálásával](https://docs.microsoft.com/azure/healthcare-apis/iot-metrics-diagnostics-export) kapcsolatos útmutatót a diagnosztikai beállítások segítségével, amelyből megtudhatja, hogyan állíthatja be a metrikai adatok exportálását.

## <a name="view-metrics-for-azure-iot-connector-for-fhir-preview"></a>Az Azure IoT-összekötő metrikáinak megtekintése a FHIR (előzetes verzió)
1. A IoT-összekötők metrikáinak megtekintéséhez válassza ki a Azure Portal Azure API-ját a FHIR szolgáltatáshoz. 

2. Navigáljon a **mérőszámokhoz** 

3. Válassza az **IoT-összekötő** fület.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. Válasszon ki egy IoT-összekötőt a metrikák megtekintéséhez (például: vannak (4) a FHIR szolgáltatáshoz tartozó Azure API-hoz társított IoT-összekötők).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

> [!NOTE]
> Az **Egyéni** lapon megadhatja a IoT-összekötő metrikáinak megtekintésére megadott idő-/időkombinációk létrehozását.

5. Válassza ki a megjelenítendő IoT-összekötő metrikáinak időtartamát (például: 1 óra, 24 óra, 7 nap vagy egyéni).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metrics-types-for-azure-iot-connector-for-fhir-preview"></a>Metrikai típusok az Azure IoT-összekötőhöz a FHIR (előzetes verzió) 
A megjelenített IoT-összekötő Metrikái a következők:

|Metrikák típusa|Mérőszámok célja| 
|-----------|--------------|
|Bejövő üzenetek száma|A fogadott nyers bejövő üzenetek száma (például az eszköz eseményei).|
|Normalizált üzenetek száma|A normalizált üzenetek száma.|
|Üzenetszöveg-csoportok száma|Azoknak a csoportoknak a száma, amelyekben a kijelölt időablakban összesítve vannak az üzenetek.|
|Átlagos normalizált szakasz késése|A normalizálás fázisának átlagos késése. A normalizálás a nyers bejövő üzenetek normalizálása.|
|Csoportosítási szakasz átlagos késése|A csoport fázisának átlagos késése. A csoportosítási fázis a normalizált üzenetek pufferelésének, összesítésének és csoportosításának elvégzése.| 
|Hibák száma összesen|Hibák száma összesen| 

## <a name="focusing-and-configuring-azure-iot-connector-for-fhir-preview-metrics"></a>Az Azure IoT-összekötő összpontosítása és konfigurálása a FHIR (előzetes verzió) Mérőszámokhoz
Ebben a példában a **Bejövő üzenetek metrikáinak számára** fogunk összpontosítani.

1. Válassza ki azt az időpontot, amelyre összpontosítani kíván.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. Ebből a képernyőből **metrika hozzáadásával** , **szűrő hozzáadásával** és **felosztással** végezheti el a további testreszabásokat. 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>Összegzés 
A figyeléshez és a hibaelhárításhoz elengedhetetlen, hogy az adatközpont metrikái hozzáférjenek.  A FHIR készült Azure IoT-összekötő segítséget nyújt ezeknek a műveleteknek a metrikák használatával történő elvégzésében. 

## <a name="next-steps"></a>Következő lépések

Tekintse meg a FHIR készült Azure IoT-összekötővel kapcsolatos gyakori kérdéseket.

>[!div class="nextstepaction"]
>[Azure IoT-összekötő FHIR – gyakori kérdések](fhir-faq.md)

* A Azure Portal a FHIR készült Azure IoT-összekötő a IoT-összekötő (előzetes verzió) néven ismert.

Az FHIR a HL7 bejegyzett védjegye, amelynek felhasználását a HL7 engedélyezte.