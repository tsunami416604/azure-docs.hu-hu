---
title: Hitelesítési módszerek az Azure Security Center az IoT-előzetes verzió |} A Microsoft Docs
description: Ismerje meg az elérhető különböző hitelesítési módszereit IoT-szolgáltatás az Azure Security Center használata esetén.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: d1f9d77d1f87e8d201e2cb034401bb3cae14f41b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58862913"
---
# <a name="security-agent-authentication-methods"></a>Biztonsági ügynök hitelesítési módszerek 

> [!IMPORTANT]
> Az Azure Security Center az IoT jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió nélkül egy szolgáltatói szerződést, és nem javasolt éles számítási feladatok esetében. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk ismerteti a különböző hitelesítési módszerek AzureIoTSecurity-ügynökkel rendelkező használhatja az IoT Hub-hitelesítésre.

Minden egyes eszköz előkészítve az Azure Security Center (ASC) az IoT hub IOT egy biztonsági modulra szükség. Az eszköz hitelesítéséhez, az IoT ASC két módszer egyikét használhatja. Válassza ki a meglévő IoT-megoldás optimális módját. 

> [!div class="checklist"]
> * A modul a biztonsági beállítás
> * Eszköz beállítás

## <a name="authentication-methods"></a>Hitelesítési módszerek

A hitelesítés végrehajtásához AzureIoTSecurity ügynök szolgáló két módszer:

 - **A modul** hitelesítési mód<br>
   A modul az ikereszköz függetlenül van hitelesítve.
   Akkor használja ezt a hitelesítési típust, ha szeretné, hogy a biztonsági ügynök egy dedikált hitelesítési módszer biztonsági modul (csak a szimmetrikus kulcs) keresztül.
        
 - **Eszköz** hitelesítési mód<br>
    Ezzel a módszerrel a biztonsági ügynök először hitelesíti az eszközidentitást. Az első hitelesítés után az IoT-ügynök ASC végez egy **REST** hívás a REST API-val, a hitelesítési adatok az eszköz IoT hubhoz. IoT-ügynök az ASC majd kéri a biztonsági modul hitelesítési módszert, és az adatokat az IoT hubról. Az utolsó lépésben IoT-ügynök az ASC-hitelesítésére az ASC IoT modul hajt végre.
    
    Akkor használja ezt a hitelesítési típust, ha szeretné, hogy a biztonsági ügynök újra felhasználhatja a meglévő eszköz hitelesítési módszert (önaláírt tanúsítvány és szimmetrikus kulcsot). 

Lásd: [biztonsági ügynök telepítési paramétereket](#security-agent-installation-parameters) megtudhatja, hogyan konfigurálhatja.
                                
## <a name="authentication-methods-known-limitations"></a>Hitelesítési módszerek ismert korlátozások

- **A modul** hitelesítési mód csak a szimmetrikus kulcsú hitelesítést támogatja.
- Nem támogatja a hitelesítésszolgáltató által aláírt tanúsítványt **eszköz** hitelesítési mód.  

## <a name="security-agent-installation-parameters"></a>Biztonsági ügynök telepítési paraméterek

Amikor [egy biztonsági ügynök üzembe helyezése](how-to-deploy-agent.md), hitelesítés részletei argumentumot meg kell adni.
Ezek az argumentumok az alábbi táblázatban szerepelnek.


|Paraméter|Leírás|Beállítások|
|---------|---------------|---------------|
|**Identitás**|Hitelesítési módszer| **A modul** vagy **eszköz**|
|**type**|Hitelesítés típusa|**SymmetricKey** vagy **SelfSignedCertificate**|
|**filePath**|A tanúsítvány és szimmetrikus kulcsot tartalmazó fájl abszolút teljes elérési útja| |
|**gatewayHostname**|Az IoT Hub teljes Tartományneve|Példa: ContosoIotHub.azure-devices.net|
|**deviceId**|Eszközazonosító|Példa: MyDevice1|
|**certificateLocationKind**|A tanúsítvány tárolási helye|**Helyi_fájl** vagy **Store**|


A biztonsági ügynök telepítési parancsfájl használata esetén a következő konfigurációt automatikusan történik.

Manuálisan szerkessze a biztonsági ügynök hitelesítés, módosítsa a konfigurációs fájlban. 

## <a name="change-authentication-method-after-deployment"></a>Üzembe helyezés után módosítsa a hitelesítési módszer

Egy telepítési parancsfájlt a biztonsági ügynök telepítésekor automatikusan létrejön egy konfigurációs fájlban.

Az üzembe helyezés után módosíthatja a hitelesítési módszereket, a konfigurációs fájl manuális szerkesztése szükség.


### <a name="c-based-security-agent"></a>C#-alapú biztonsági ügynök

Szerkesztés _Authentication.config_ a következő paraméterekkel:

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

Szerkesztés _LocalConfiguration.json_ a következő paraméterekkel:

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
- [Hozzáférés nyers biztonsági adatok](how-to-security-data-access.md)
