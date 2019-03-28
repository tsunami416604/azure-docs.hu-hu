---
title: Hitelesítési módszerek IoT villámnézethez ASC |} A Microsoft Docs
description: Ismerje meg az elérhető különböző hitelesítési módszereit IoT-szolgáltatást az ASC használatakor.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 23bc4d0df1c8124ec225ac31239c7acb3f1ab546
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541812"
---
# <a name="security-agent-authentication-methods"></a>Biztonsági ügynök hitelesítési módszerek 

> [!IMPORTANT]
> Az IoT ASC jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió nélkül egy szolgáltatói szerződést, és nem javasolt éles számítási feladatok esetében. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk ismerteti a különböző hitelesítési módszerek AzureIoTSecurity-ügynökkel rendelkező használhatja az IoT Hub-hitelesítésre.

Minden egyes eszköz előkészítve az ASC-hez, az IoT hub IOT egy biztonsági modulra szükség. Az eszköz hitelesítéséhez, az IoT ASC két módszer egyikét használhatja. Válassza ki a meglévő IoT-megoldás optimális módját. 

> [!div class="checklist"]
> * A modul a biztonsági beállítás
> * Eszköz beállítás

## <a name="authentication-methods"></a>Hitelesítési módszerek

A hitelesítés végrehajtásához AzureIoTSecurity ügynök szolgáló két módszer:

 - **A modul** hitelesítési mód<br>
   A modul az ikereszköz függetlenül van hitelesítve.
   Az ilyen típusú hitelesítés van definiálva a a Authentication.config fájlban a szükséges adatokat C# és a c-hez tartozó LocalConfiguration.json
        
 - **Eszköz** hitelesítési mód<br>
    Ezzel a módszerrel a biztonsági ügynök először hitelesíti az eszközt. Az első hitelesítés után az IoT-ügynök ASC végez **Rest** hívás a Rest API-val, a hitelesítési adatok az eszköz IoT hubhoz. IoT-ügynök az ASC majd kéri a biztonsági modul hitelesítési módszert, és az adatokat az IoT hubról. Az utolsó lépésben IoT-ügynök az ASC-hitelesítésére az ASC IoT modul hajt végre.    

Lásd: [biztonsági ügynök telepítési paramétereket](#security-agent-installation-parameters) megtudhatja, hogyan konfigurálhatja.
                                
## <a name="authentication-methods-known-limitations"></a>Hitelesítési módszerek ismert korlátozások

- **A modul** hitelesítési mód csak a szimmetrikus kulcsú hitelesítést támogatja.
- Nem támogatja a hitelesítésszolgáltató által aláírt tanúsítványt **eszköz** hitelesítési mód.  

## <a name="security-agent-installation-parameters"></a>Biztonsági ügynök telepítési paraméterek

Amikor [egy biztonsági ügynök üzembe helyezése](select-deploy-agent.md), hitelesítés részletei argumentumot meg kell adni.
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
- [Biztonsági ügynök telepítése](select-deploy-agent.md)
- [Hozzáférés nyers biztonsági adatok](how-to-security-data-access.md)
