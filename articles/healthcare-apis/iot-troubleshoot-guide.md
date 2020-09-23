---
title: Azure IoT-összekötő a FHIR (előzetes verzió) – hibaelhárítási útmutató és útmutató
description: A FHIR (előzetes verzió) hibaüzenetei és feltételei, valamint a leképezési fájlok másolása a gyakori Azure IoT-összekötővel
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 09/16/2020
ms.author: jasteppe
ms.openlocfilehash: 64056ef2f63331686553c52040af9e10ee0ac468
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982980"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Azure IoT-összekötő a FHIR (előzetes verzió) – hibaelhárítási útmutató

Ez a cikk az Azure IoT-összekötő FHIR * hibaüzenetek és feltételek esetén történő hibaelhárításának lépéseit ismerteti.  

Azt is megtudhatja, hogyan hozhat létre másolatot az Azure IoT-összekötőről a FHIR átalakítási leképezések JSON-fájlhoz (például: eszköz-és FHIR).  

A Azure Portalon kívüli szerkesztéshez és archiváláshoz használhatja a konvertálási leképezés JSON-másolatait.  

> [!TIP]
> Ha a FHIR-hez készült Azure IoT-összekötőhöz [Azure technikai támogatási](https://azure.microsoft.com/support/create-ticket/) jegyet nyit meg, ügyeljen arra, hogy a hibaelhárítási folyamattal kapcsolatos segítséget nyújtson a konverziós leképezési JSON másolatának elkészítéséhez.

## <a name="device-and-fhir-conversion-mapping-json-template-validations-for-azure-iot-connector-for-fhir-preview"></a>Eszköz-és FHIR-konverzió leképezése JSON-sablon érvényessége az Azure IoT-összekötőhöz a FHIR (előzetes verzió)
Ebben a szakaszban megtudhatja, hogy a FHIR készült Azure IoT Connector milyen ellenőrzési folyamattal ellenőrzi, hogy az eszköz és a FHIR konvertálási leképezése JSON-sablonokat kell-e használni, mielőtt azok használatra elmenthetők.  Ezek az elemek szükségesek az eszköz és a FHIR átalakítási leképezés JSON-fájljában.

**Eszköz-hozzárendelés**

|Elem|Kötelező|
|:-------|:------|
|TypeName|Igaz|
|TypeMatchExpression|Igaz|
|DeviceIdExpression|Igaz|
|TimestampExpression|Igaz|
|Értékek []. ValueName|Igaz|
|Értékek []. ValueExpression|Igaz|

> [!NOTE]
> Értékek []. ValueName és értékek []. ValueExpression
>
> Ezek az elemek csak akkor szükségesek, ha a tömbben van egy érték bejegyzése – érvényes, hogy nincs hozzárendelve érték. Ez akkor használatos, ha az elküldött telemetria egy esemény. Például: Ha egy hordható IoMT-eszközt helyeznek be vagy távolítanak el. Az elem (ek) nem rendelkezik értékekkel, kivéve az Azure IoT-összekötőt a FHIR-egyezésekhez és a kibocsátó nevekhez. A FHIR-átalakításban a FHIR készült Azure IoT-összekötő a szemantikai típuson alapuló, kód alapján használható fogalomra képezi le, a tényleges értékek nincsenek kitöltve.

**FHIR leképezése**

|Elem|Kötelező|
|:------|:-------|
|TypeName|Igaz|

> [!NOTE]
> Ez az egyetlen szükséges FHIR-megfeleltetési elem jelenleg érvényesítve.

## <a name="error-messages-and-fixes-for-azure-iot-connector-for-fhir-preview"></a>Hibaüzenetek és javítások a FHIR készült Azure IoT Connectorhoz (előzetes verzió)

|Üzenet|Megjelenített|Condition (Állapot)|Javítás| 
|-------|---------|---------|---|
|Érvénytelen leképezési név, a hozzárendelés nevének eszköz-vagy FHIR kell lennie.|API|A megadott leképezési típus nem eszköz-vagy FHIR.|Használja a két támogatott leképezési típus valamelyikét (például: eszköz vagy FHIR).|
|Az érvényesítés nem sikerült. A szükséges adatok hiányoznak vagy érvénytelenek.|API és Azure Portal|Egy átalakítási leképezés hiányzó szükséges információt vagy elemet próbált menteni.|Adja hozzá a hiányzó konverziós megfeleltetési adatokat vagy elemeket, és próbálja meg újból menteni a konverziós leképezést.|
|Nincs definiálva a kulcs paramétereinek újbóli előállítása.|API|Kulcs kérésének újbóli előállítása.|Adja meg a paramétereket a újrageneráló kulcs kérelmében.|
|Elérte az előfizetésben üzembe helyezhető IoT-összekötők maximális számát.|API és Azure Portal|Elérte az Azure IoT Connectort a FHIR előfizetési kvótához (az alapértelmezett érték: (2)/előfizetés).|Törölje az Azure IoT Connector egyik meglévő példányát a FHIR.  Használjon másik előfizetést, amely nem érte el az előfizetési kvótát.  Az előfizetési kvóta növelését kéri.|
|Az erőforrás áthelyezése nem támogatott az IoT-összekötő Azure API FHIR-erőforráshoz való engedélyezése esetén.|API és Azure Portal|Kísérlet egy áthelyezési művelet végrehajtására egy olyan Azure API-FHIR erőforráshoz, amely az Azure IoT-összekötő egy vagy több példányával rendelkezik a FHIR-hez.|Az áthelyezési művelet elvégzéséhez törölje az Azure IoT-összekötő meglévő példányát (i) az FHIR-hez.|
|Az IoT-összekötő nincs kiépítve.|API|Az alárendelt szolgáltatások (&-hozzárendelések) használatának megkísérlése, ha a szülő (az Azure IoT-összekötő a FHIR esetében) még nincs kiépítve.|Azure IoT-összekötő kiépítése a FHIR.|
|A kérelem nem támogatott.|API|Az adott API-kérelem nem támogatott.|Használja a megfelelő API-kérést.|
|A fiók nem létezik.|API|Nem létezik olyan Azure IoT-összekötő hozzáadása a FHIR és az Azure API for FHIR-erőforráshoz, amely nem létezik.|Hozza létre az Azure API-t a FHIR-erőforráshoz, majd próbálkozzon újra a művelettel.|
|Az Azure API for FHIR erőforrás-FHIR verziója nem támogatott a IoT-összekötő esetében.|API|Kísérlet egy Azure IoT-összekötő használatára az FHIR-hez az Azure API FHIR-erőforrással való inkompatibilis verziójával.|Hozzon létre egy új Azure API-t a FHIR-erőforráshoz (R4-es verzió), vagy használjon egy meglévő Azure API-t a FHIR erőforráshoz (R4-es verzió

##  <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>Miért nem jelenik meg az Azure IoT-összekötő a FHIR (előzetes verzió) számára az Azure API for FHIR szolgáltatásban?

|Lehetséges problémák|Javítások|
|----------------|-----|
|Az adatfeldolgozás még folyamatban van.|Az egressed az Azure API-ra FHIR a kötegekben (minden ~ 15 percenként).  Lehetséges, hogy az adatfeldolgozás még folyamatban van, és további időre van szükség ahhoz, hogy az információk megmaradjanak a FHIR készült Azure API-ban.|
|Nincs konfigurálva az eszköz konvertálási leképezésének JSON-fájlja.|Konfigurálhatja és mentheti a megfelelő eszköz-konverzió leképezése JSON-t.|
|A FHIR átalakítási leképezése JSON nincs konfigurálva.|Konfigurálhatja és mentheti a FHIR konverziójának leképezése JSON-t.|
|Az eszköz üzenete nem tartalmaz az eszköz-hozzárendelésben definiált várt kifejezést.|Ellenőrizze az eszköz üzenetében meghatározott JsonPath-kifejezéseket.|
|Nem jött létre eszköz-erőforrás a FHIR készült Azure API-ban (feloldási típus: csak keresés) *.|Hozzon létre egy érvényes eszköz-erőforrást az Azure API FHIR. Győződjön meg arról, hogy az eszköz erőforrása tartalmaz egy azonosítót, amely megfelel a bejövő üzenetben megadott eszköz azonosítójának.|
|A FHIR készült Azure API-ban nem hoztak létre egy beteg-erőforrást (megoldás típusa: csak keresés) *.|Hozzon létre egy érvényes beteg-erőforrást a FHIR készült Azure API-ban.|
|Az eszköz. beteg hivatkozás nincs beállítva, vagy a hivatkozás érvénytelen (feloldási típus: csak keresés) *.|Győződjön meg arról, hogy az eszköz erőforrása érvényes [hivatkozást](https://www.hl7.org/fhir/device-definitions.html#Device.patient) tartalmaz a beteg erőforrására.| 

* Rövid útmutató: az Azure [IoT Connector (előzetes verzió) üzembe helyezése a Azure Portal használatával](iot-fhir-portal-quickstart.md#create-new-azure-iot-connector-for-fhir-preview) az Azure IoT-összekötő funkcionális leírását a FHIR-feloldási típusokhoz (például: keresés vagy létrehozás).

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>Az Azure IoT-összekötő FHIR (előzetes verzió) konvertálási leképezési JSON-példányának létrehozása
Az Azure IoT Connector FHIR-megfeleltetési fájlokra való másolása hasznos lehet a Azure Portal webhelyén kívüli szerkesztéshez és archiváláshoz.

Az Azure technikai támogatásához meg kell adni a leképezési fájlok másolatait, amikor egy támogatási jegyet nyitnak meg, amely segítséget nyújt a hibaelhárításhoz.

> [!NOTE]
> A JSON az eszköz-és FHIR-hozzárendelési fájlok egyetlen támogatott formátuma.

> [!TIP]
> További információ az Azure IoT-összekötőről a FHIR [-eszköz és a FHIR átalakítási leképezése JSON](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates) -hoz

1. Válassza az **"IoT-összekötő (előzetes verzió)"** lehetőséget az Azure API FHIR erőforrás-irányítópultjának bal alsó részén a **"beépülő modulok"** szakaszban.

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT Connector1" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Válassza ki a **"Connector" (összekötő** ) elemet, amelyről a konvertálási leképezés JSON-fájlját másolni szeretné.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT Connector2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Ezt a folyamatot a **"FHIR-leképezés konfigurálása"** JSON-fájl tartalmának másolásához és mentéséhez is használhatja.

3. Válassza az **"eszköz-hozzárendelés konfigurálása"** lehetőséget.

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT Connector3" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Válassza ki a JSON tartalmát, és végezze el a másolási műveletet (például: válassza a CTRL + c billentyűkombinációt). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT Connector4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Végezze el a beillesztési műveletet (például a CTRL + v billentyűkombinációt) egy új fájlba egy szerkesztőben (például: Visual Studio Code, notepad), és mentse a fájlt egy *. JSON kiterjesztéssel.

> [!TIP]
> Ha a FHIR-hez készült Azure IoT-összekötőhöz [Azure technikai támogatási](https://azure.microsoft.com/support/create-ticket/) jegyet nyit meg, ügyeljen arra, hogy a hibaelhárítási folyamattal kapcsolatos segítséget nyújtson a konverziós leképezési JSON másolatának elkészítéséhez.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a FHIR készült Azure IoT-összekötővel kapcsolatos gyakori kérdéseket.

>[!div class="nextstepaction"]
>[Azure IoT-összekötő FHIR – gyakori kérdések](fhir-faq.md#azure-iot-connector-for-fhir-preview)

* A Azure Portal a FHIR készült Azure IoT-összekötő a IoT-összekötő (előzetes verzió) néven ismert.

Az FHIR a HL7 bejegyzett védjegye, amelynek felhasználását a HL7 engedélyezte.
