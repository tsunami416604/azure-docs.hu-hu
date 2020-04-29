---
title: Biztonsági ügynök hitelesítési módszerei
description: Ismerje meg a IoT szolgáltatás Azure Security Centerjának használatakor elérhető különböző hitelesítési módszereket.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311516"
---
# <a name="security-agent-authentication-methods"></a>Biztonsági ügynök hitelesítési módszerei

Ez a cikk ismerteti a különböző hitelesítési módszereket, amelyekkel a AzureIoTSecurity-ügynökkel végezheti el a hitelesítést a IoT Hub.

A IoT Hub IoT Azure Security Centerbe bekészített minden eszközhöz biztonsági modulra van szükség. Az eszköz hitelesítéséhez Azure Security Center a IoT két módszer egyikét használhatja. Válassza ki a meglévő IoT-megoldáshoz legjobban illő módszert.

> [!div class="checklist"]
> * SecurityModule beállítás
> * Eszköz beállítás

## <a name="authentication-methods"></a>Hitelesítési módszerek

A AzureIoTSecurity-ügynök két módszere a hitelesítés elvégzésére:

- **SecurityModule** hitelesítési mód<br>
Az ügynököt a biztonsági modul identitása alapján hitelesíti a rendszer az eszköz identitástól függetlenül.
Akkor használja ezt a hitelesítési típust, ha azt szeretné, hogy a biztonsági ügynök egy dedikált hitelesítési módszert használjon a biztonsági modulon keresztül (csak szimmetrikus kulcs).

- **Eszköz** hitelesítési módja<br>
Ebben a metódusban a biztonsági ügynök először hitelesíti magát az eszköz identitásával. A kezdeti hitelesítés után a IoT-ügynök Azure Security Center **Rest** -hívást hajt végre a IoT hub az eszköz hitelesítési adataival REST API használatával. A IoT-ügynök Azure Security Center ezt követően a biztonsági modul hitelesítési módszerét és adatait kéri le a IoT Hub. Az utolsó lépésben a IoT-ügynök Azure Security Centerja a IoT modul Azure Security Center a hitelesítését végzi.

Akkor használja ezt a hitelesítési típust, ha azt szeretné, hogy a biztonsági ügynök felhasználja a meglévő eszköz-hitelesítési módszert (önaláírt tanúsítvány vagy szimmetrikus kulcs).

A konfigurálásának megismeréséhez tekintse meg a [biztonsági ügynök telepítési paramétereit](#security-agent-installation-parameters) .

## <a name="authentication-methods-known-limitations"></a>Ismert hitelesítési módszerek

- A **SecurityModule** hitelesítési mód csak a szimmetrikus kulcsos hitelesítést támogatja.
- Az **eszköz** hitelesítési módja nem támogatja a hitelesítésszolgáltató által aláírt tanúsítványt.

## <a name="security-agent-installation-parameters"></a>Biztonsági ügynök telepítési paraméterei

[Biztonsági ügynök telepítésekor a](how-to-deploy-agent.md)hitelesítési adatokat argumentumként kell megadni.
Ezek az argumentumok a következő táblázatban vannak dokumentálva.

|Linux-paraméter neve | Windows-paraméter neve | Gyorsírás paraméter |Leírás|Beállítások|
|---------------------|---------------|---------|---------------|---------------|
|hitelesítés – identitás|AuthenticationIdentity|AUI|Hitelesítési identitás| **SecurityModule** vagy- **eszköz**|
|hitelesítés – metódus|AuthenticationMethod|Aum|Hitelesítési módszer|**SymmetricKey** vagy **SelfSignedCertificate**|
|fájl elérési útja|FilePath|nő|A tanúsítványt vagy a szimmetrikus kulcsot tartalmazó fájl teljes elérési útja| |
|gazdagép neve|HostName|HN|A IoT Hub teljes tartományneve|Példa: ContosoIotHub.azure-devices.net|
|eszköz azonosítója|DeviceId|bú|Eszközazonosító|Példa: MyDevice1|
|tanúsítvány – hely típusú|CertificateLocationKind|CL|Tanúsítvány tárolási helye|**LocalFile** vagy- **tároló**|
|

A biztonsági ügynök telepítése parancsfájl használatakor a rendszer automatikusan elvégzi a következő konfigurációt. A biztonsági ügynök hitelesítésének manuális szerkesztéséhez szerkessze a konfigurációs fájlt.

## <a name="change-authentication-method-after-deployment"></a>Hitelesítési módszer módosítása az üzembe helyezés után

Amikor telepítési parancsfájllal telepít biztonsági ügynököt, a rendszer automatikusan létrehoz egy konfigurációs fájlt.

Az üzembe helyezést követően a hitelesítési módszerek módosításához szükség van a konfigurációs fájl manuális szerkesztésére.

### <a name="c-based-security-agent"></a>C#-alapú biztonsági ügynök

Szerkessze a _Authentication. config_ fájlt a következő paraméterekkel:

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

Szerkessze a _LocalConfiguration. JSON_ fájlt a következő paraméterekkel:

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

- [Biztonsági ügynökök áttekintése](security-agent-architecture.md)
- [Biztonsági ügynök telepítése](how-to-deploy-agent.md)
- [Hozzáférés a nyers biztonsági adatokhoz](how-to-security-data-access.md)
