---
title: A biztonsági ügynök helyi konfigurációja (C#)
description: További információ az Azure Security Center for IoT biztonsági szolgáltatás, a biztonsági ügynök helyi konfigurációs fájl C#.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: adf0d72763e0cb1892d64c68a6dce05abbf6f582
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311661"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>A helyi konfigurációs fájl (C# ügynök) ismertetése

Az Azure Security Center for IoT biztonsági ügynök konfigurációkat használ egy helyi konfigurációs fájlból.

A biztonsági ügynök egyszer olvassa be a konfigurációs fájlt, amikor az ügynök elindul. A helyi konfigurációs fájlban található konfigurációk a hitelesítési konfigurációt és az ügynökkel kapcsolatos egyéb konfigurációkat is tartalmazzák.

A C# biztonsági ügynök több konfigurációs fájlt használ:

- **General.config** - Ügynökkel kapcsolatos konfigurációk.
- **Authentication.config** - Hitelesítéssel kapcsolatos konfiguráció (beleértve a hitelesítés részleteit).
- **SecurityIotInterface.config** - IoT-vel kapcsolatos konfigurációk.

A konfigurációs fájlok tartalmazzák az alapértelmezett konfigurációt. A hitelesítési konfiguráció az ügynök telepítése során történik, és az ügynök újraindításakor a konfigurációs fájl módosítása történik.

## <a name="configuration-file-location"></a>Konfigurációs fájl helye

Linux esetén:

- Az operációs rendszer konfigurációs fájljai a rendszerben `/var/ASCIoTAgent`találhatók.

Windows esetén:

- Az operációs rendszer konfigurációs fájljai a biztonsági ügynök könyvtárában találhatók.

### <a name="generalconfig-configurations"></a>General.config konfigurációk

| Konfiguráció neve | Lehetséges értékek | Részletek |
|:-----------|:---------------|:--------|
| ügynökazonosító | GUID | Ügynök egyedi azonosítója |
| readRemoteConfigurationTimeout | időtartam | A távoli konfiguráció nak az IoT Hubról való lehívásának időszaka. Ha az ügynök nem tudja beolvasni a konfigurációt a megadott időn belül, a művelet időtúlmarad.|
| schedulerInterval | időtartam | Belső ütemező intervallum. |
| gyártóIntervallum | időtartam | Eseményproduceri dolgozó időköze. |
| consumerInterval | időtartam | Esemény fogyasztói dolgozói intervallum. |
| highPriorityQueueSizePercentage | 0 < szám < 1 | A teljes gyorsítótár nak a magas prioritású üzenetekhez való része. |
| Loglevel | "Ki", "Végzetes", "Hiba", "Figyelmeztetés", "Információ", "Debug"  | Naplóüzenetek azonos és annál magasabb súlyosságú a debug konzol (Syslog Linux). |
| fileLogLevel |  "Ki", "Végzetes", "Hiba", "Figyelmeztetés", "Információ", "Debug"| Naplóüzenetek azonos és annál magasabb súlyosságú naplózva a fájlba (Syslog Linux). |
| diagnosticVerbosityLevel | "Nincs", "néhány", "Minden", | A diagnosztikai események részletességi szintje. Nincs – a diagnosztikai események nem kerülnek elküldésre, Néhány - Csak nagy fontosságú diagnosztikai események et küld, Minden - az összes napló diagnosztikai eseményként is elküldésre kerül. |
| logFilePath | Fájl elérési útja | Ha a fileLogLevel > Ki, a program ebbe a fájlba írja a naplókat. |
| defaultEventPriority | "Magas", "Alacsony", "Ki" | Alapértelmezett eseményprioritás. |

### <a name="generalconfig-example"></a>Példa a General.config

```XML
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>Hitelesítés.config

| Konfiguráció neve | Lehetséges értékek | Részletek |
|:-----------|:---------------|:--------|
| modulNeve | sztring | A biztonsági modul identitásának neve. Ennek a névnek meg kell felelnie az eszközben lévő modul identitásnevének. |
| deviceId | sztring | Az eszköz azonosítója (az Azure IoT Hubban regisztrált). || schedulerInterval | TimeSpan karakterlánc | Belső ütemező intervallum. |
| átjáróÁllomásnév | sztring | Az Azure Iot Hub gazdagépneve. Általában <a my-hub>.azure-devices.net |
| filePath | karakterlánc - fájl elérési útja | A hitelesítési titkot tartalmazó fájl elérési útja.|
| type | "SymmetricKey", "SelfSignedCertificate" | A hitelesítés felhasználói titkos kulcsa. Válassza *a SymmetricKey* lehetőséget, ha a felhasználói titkos kulcs szimmetrikus kulcs, válassza az *önaláírt tanúsítványt,* ha a titkos kulcs önaláírt tanúsítvány. |
| identity | "DPS", "Modul", "Eszköz" | Hitelesítési identitás – DPS, ha a hitelesítés DPS-en keresztül történik, a modul, ha a hitelesítés modulhitelesítő adatokkal történik, vagy eszköz, ha a hitelesítés eszközhitelesítő adatokkal történik.
| certificateLocationKind |  "LocalFile", "Store" | LocalFile ha a tanúsítvány fájlban van tárolva, tárolja, ha a tanúsítvány egy tanúsítványtárolóban található. |
| idScope | sztring | A DPS azonosító hatóköre |
| registrationId | sztring  | DPS-eszköz regisztrációs azonosítója. |
|

### <a name="authenticationconfig-example"></a>Például Authentication.config példa

```XML
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```

### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config

| Konfiguráció neve | Lehetséges értékek | Részletek |
|:-----------|:---------------|:--------|
| transportType (transportType) | "Ampq" "Mqtt" | IoT Hub átviteli típusa. |
|

### <a name="securityiotinterfaceconfig-example"></a>Példa a SecurityIotInterface.config

```XML
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>További lépések

- Olvassa el az Azure Security Center for IoT szolgáltatás [áttekintését](overview.md)
- További információ az Azure Security Center for IoT [Architecture szolgáltatásról](architecture.md)
- Az Azure Security Center [ioT-szolgáltatás](quickstart-onboard-iot-hub.md) engedélyezése
- Olvassa el az Azure Security Center for IoT szolgáltatás [gyakori kérdések](resources-frequently-asked-questions.md)
- További információ a [nyers biztonsági adatok](how-to-security-data-access.md) eléréséről
- Az [ajánlások megismerése](concept-recommendations.md)
- A biztonsági [riasztások ismertetése](concept-security-alerts.md)
