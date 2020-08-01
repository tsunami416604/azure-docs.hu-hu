---
title: Azure IoT-összekötő a FHIR (előzetes verzió) – hibaelhárítási útmutató és útmutató
description: A FHIR (előzetes verzió) hibaüzenetei és feltételei, valamint a leképezési fájlok másolása a közös Azure IoT-összekötővel
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: jasteppe
ms.openlocfilehash: 8c372a865e34b2cbd1b5b3e6d8619c3ef0f438e0
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460422"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Azure IoT-összekötő a FHIR (előzetes verzió) – hibaelhárítási útmutató

Ez a cikk az Azure IoT-összekötő gyakori hibaelhárítási lépéseit ismerteti FHIR hibaüzenetek és feltételek esetén.  

Azt is megtudhatja, hogyan hozhat létre másolatot az Azure IoT-összekötőről a Azure Portalon kívüli JSON-leképezési fájlok szerkesztésére és archiválására, illetve a támogatási jegy megnyitásakor az Azure technikai támogatás biztosítására. 

## <a name="error-messages-and-fixes"></a>Hibaüzenetek és javítások

|Üzenet   |Feltétel  |Javítás         |
|----------|-----------|------------|
|Érvénytelen leképezési név, a hozzárendelés nevének eszköz-vagy FHIR kell lennie.|A megadott leképezési típus nem eszköz-vagy FHIR|Használja a két támogatott leképezési típus valamelyikét (például: eszköz vagy FHIR)|
|Nincs definiálva a kulcs paramétereinek újrakészítése|Kulcs kérésének újrakészítése|Paraméterek belefoglalása a Regeneration Key kérelembe|
|Elérte az előfizetésben üzembe helyezhető IoT-összekötők maximális számát|Elérte az Azure IoT Connectort a FHIR előfizetési kvótához (az alapértelmezett érték: (2)/előfizetés)|Törölje az Azure IoT Connector egyik meglévő példányát a FHIR-hez, használjon másik előfizetést, amely nem érte el az előfizetési kvótát, vagy igényeljen egy előfizetési kvótát.|
|Az erőforrás áthelyezése nem támogatott az IoT-összekötő Azure API FHIR-erőforráshoz való engedélyezése esetén|Kísérlet egy áthelyezési művelet végrehajtására egy Azure API-FHIR erőforráshoz, amely az Azure IoT-összekötő egy vagy több példányával rendelkezik a FHIR|Az Azure IoT-összekötő meglévő példányának törlése az FHIR-hez, és az áthelyezési művelet végrehajtása|
|Az IoT-összekötő nincs kiépítve|A Child Services (kapcsolatok &-hozzárendelések) használatának megkísérlése, ha a szülő (az Azure IoT-összekötő a FHIR esetében) még nincs kiépítve|Azure IoT-összekötő kiépítése a FHIR|
|A kérelem nem támogatott|Az adott API-kérelem nem támogatott|A megfelelő API-kérelem használata|
|A fiók nem létezik|Nem létezik Azure IoT-összekötő hozzáadása a FHIR-hez és az Azure API for FHIR-erőforráshoz|Hozza létre az Azure API-t a FHIR-erőforráshoz, majd próbálja megismételni a műveletet.|
|Az Azure API for FHIR erőforrás-FHIR verziója nem támogatott a IoT-összekötő esetében|Kísérlet egy Azure IoT-összekötő használatára a FHIR-hez az Azure API FHIR-erőforrással való inkompatibilis verziójával|Hozzon létre egy új Azure API-t a FHIR-erőforráshoz (R4-es verzió), vagy használjon egy meglévő Azure API-t a FHIR-erőforráshoz

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-mapping-files"></a>Az Azure IoT-összekötő FHIR (előzetes verzió) leképezési fájljainak másolatának létrehozása
Az Azure IoT Connector FHIR-megfeleltetési fájlokra való másolása hasznos lehet a Azure Portal webhelyen kívüli szerkesztéshez és archiváláshoz, illetve a támogatási jegy megnyitásakor az Azure technikai támogatásának biztosításához.

> [!NOTE]
> A JSON az eszköz-és FHIR-hozzárendelési fájlok egyetlen támogatott formátuma.

> [!TIP]
> További információ az Azure IoT-összekötőről a FHIR [-eszközök és a FHIR-leképezés JSON-fájljairól](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. Válassza az **"IoT-összekötő (előzetes verzió)"** lehetőséget az Azure API FHIR erőforrás-irányítópultjának bal alsó részén a **"beépülő modulok"** szakaszban.

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT-összekötő" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Válassza ki azt az **"összekötőt"** , amelyről a leképezési fájlokat másolni kívánja.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT-összekötő" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Ezt a folyamatot a **"FHIR-leképezés konfigurálása"** JSON-fájl tartalmának másolásához és mentéséhez is használhatja.

3. Válassza az **"eszköz-hozzárendelés konfigurálása"** lehetőséget.

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT-összekötő" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Válassza ki a JSON tartalmát, és végezze el a másolási műveletet (például: válassza a CTRL + c billentyűkombinációt). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT-összekötő" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Végezze el a beillesztési műveletet (például a CTRL + v billentyűkombinációt) egy új fájlba egy szerkesztőben (például: Visual Studio Code, notepad), és mentse a fájlt egy *. JSON kiterjesztéssel.

> [!TIP]
> Ha a FHIR készült Azure IoT-összekötőhöz [Azure technikai támogatási](https://azure.microsoft.com/support/create-ticket/) jegyet nyit meg, ügyeljen arra, hogy a leképezési fájlok másolatait is tartalmazza a hibaelhárítási folyamat elősegítése érdekében.

## <a name="next-steps"></a>További lépések

Tekintse meg a FHIR készült Azure IoT-összekötővel kapcsolatos gyakori kérdéseket.

>[!div class="nextstepaction"]
>[Azure IoT-összekötő a FHIR (előzetes verzió) – gyakori kérdések](fhir-faq.md#iot-connector-preview)

* A Azure Portal a FHIR készült Azure IoT-összekötő a IoT-összekötő (előzetes verzió) néven ismert.

Az FHIR a HL7 bejegyzett védjegye, amelynek felhasználását a HL7 engedélyezte.