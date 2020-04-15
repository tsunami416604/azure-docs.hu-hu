---
title: Biztonsági ügynök hitelesítési módszerei
description: Ismerje meg a különböző hitelesítési módszerek elérhető az Azure Security Center for IoT szolgáltatás használatakor.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0d9d51292c3cae9634af917819b558cdfd2fa04b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311516"
---
# <a name="security-agent-authentication-methods"></a>Biztonsági ügynök hitelesítési módszerei

Ez a cikk ismerteti a különböző hitelesítési módszereket az AzureIoTSecurity ügynökkel az IoT Hubhitelesítéshez használható.

Az IoT-központban az Azure Security Center for IoT-be bedeszkázott minden egyes eszközhöz szükség van egy biztonsági modulra. Az eszköz hitelesítéséhez az Azure Security Center for IoT két módszer egyikét használhatja. Válassza ki azt a módszert, amely a legjobban megfelel a meglévő IoT-megoldásnak.

> [!div class="checklist"]
> * SecurityModule beállítás
> * Eszközbeállítás

## <a name="authentication-methods"></a>Hitelesítési módszerek

Az AzureIoTSecurity ügynök két módszere a hitelesítés végrehajtására:

- **SecurityModule** hitelesítési mód<br>
Az ügynök hitelesítése a biztonsági modul identitása az eszköz identitásátől függetlenül.
Akkor használja ezt a hitelesítési típust, ha azt szeretné, hogy a biztonsági ügynök egy dedikált hitelesítési módszert használjon a biztonsági modulon keresztül (csak szimmetrikus kulcs).

- **Eszközhitelesítési** mód<br>
Ebben a módszerben a biztonsági ügynök először hitelesíti magát az eszköz identitásával. A kezdeti hitelesítés után az Azure Security Center for IoT-ügynök **rest-hívást** hajt végre az IoT Huba a REST API használatával az eszköz hitelesítési adataival. Az Azure Security Center for IoT-ügynök majd kéri a biztonsági modul hitelesítési módszer és az adatok az IoT Hub. Az utolsó lépésben az Azure Security Center for IoT-ügynök hitelesítést hajt végre az Azure Security Center for IoT modul.

Akkor használja ezt a hitelesítési típust, ha azt szeretné, hogy a biztonsági ügynök újra felhasználjon egy meglévő eszközhitelesítési módszert (önaláírt tanúsítványt vagy szimmetrikus kulcsot).

A konfigurálásról a [Biztonsági ügynök telepítési paraméterei](#security-agent-installation-parameters) című témakörben olvashat.

## <a name="authentication-methods-known-limitations"></a>Ismert hitelesítési módszerek korlátai

- **A SecurityModule** hitelesítési mód csak a szimmetrikus kulcshitelesítést támogatja.
- A hitelesítésszolgáltató által aláírt tanúsítványt **az eszközhitelesítési** mód nem támogatja.

## <a name="security-agent-installation-parameters"></a>Biztonsági ügynök telepítési paraméterei

[Biztonsági ügynök telepítésekor](how-to-deploy-agent.md)a hitelesítési adatokat argumentumként kell megadni.
Ezeket az argumentumokat az alábbi táblázat dokumentálja.

|Linux paraméter neve | Windows-paraméter neve | Gyorsírás i. paramétere |Leírás|Beállítások|
|---------------------|---------------|---------|---------------|---------------|
|hitelesítés-identitás|AuthenticationIdentity (Hitelesítési identitás)|aui|Hitelesítési identitás| **SecurityModule** vagy **eszköz**|
|hitelesítési módszer|AuthenticationMethod (Hitelesítési módszer)|Aum|Hitelesítési módszer|**SymmetricKey** vagy **SelfSignedCertificate**|
|fájlelérési út|FilePath (Fájlelérési út)|nő|A tanúsítványt vagy a szimmetrikus kulcsot tartalmazó fájl teljes elérési útja| |
|állomásnév|HostName|Hn|Az IoT-központ teljes tartományszáma|Példa: ContosoIotHub.azure-devices.net|
|eszköz-azonosító|DeviceId|Di|Eszközazonosító|Példa: MyDevice1|
|tanúsítvány helye-fajta|CertificateLocationKind|Cl|Tanúsítvány tárolási helye|**LocalFile** vagy **Áruház**|
|

A telepítési biztonsági ügynök parancsfájl használatakor a következő konfiguráció automatikusan végrehajtásra kerül. A biztonsági ügynök hitelesítésének manuális szerkesztéséhez szerkessze a konfigurációs fájlt.

## <a name="change-authentication-method-after-deployment"></a>Hitelesítési módszer módosítása telepítés után

Ha egy biztonsági ügynököt telepítési parancsfájllal telepít, a rendszer automatikusan létrehoz egy konfigurációs fájlt.

A hitelesítési módszerek központi telepítés utáni módosításához a konfigurációs fájl manuális szerkesztése szükséges.

### <a name="c-based-security-agent"></a>C#-alapú biztonsági ügynök

A _Authentication.config_ fájl szerkesztése a következő paraméterekkel:

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>C-alapú biztonsági ügynök

A _LocalConfiguration.json szerkesztése_ a következő paraméterekkel:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Lásd még

- [Biztonsági ügynökök – áttekintés](security-agent-architecture.md)
- [Biztonsági ügynök telepítése](how-to-deploy-agent.md)
- [Hozzáférés a nyers biztonsági adatokhoz](how-to-security-data-access.md)
