---
title: Fenyegetésfelderítési adatok összekapcsolása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan kapcsolhatja össze a fenyegetésfelderítési adatokat az Azure Sentinelhez.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 5c79642d287224cd15531701d7cc87ebfd72eb69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588042"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Fenyegetésfelderítési szolgáltatóktól származó adatok összekapcsolása

> [!IMPORTANT]
> A Fenyegetésintelligencia-adatösszekötők az Azure Sentinel ben jelenleg nyilvános előzetes verzióban.
> Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Az Azure Sentinel lehetővé teszi a szervezet által használt fenyegetésjelzők importálását, amelyek növelhetik a biztonsági elemzők képességét az ismert fenyegetések észlelésére és rangsorolására. Az Azure Sentinel számos funkciója elérhetővé válik, vagy továbbfejlesztett:

- **Az Analytics** ütemezett szabálysablonokat tartalmaz, amelyek segítségével riasztásokat és incidenseket hozhat létre a fenyegetésjelzők naplóeseményeinek egyezése alapján.

- **A munkafüzetek** összesített információkat nyújtanak az Azure Sentinelbe importált fenyegetésjelzőkről és a fenyegetésjelzőknek megfelelő elemzési szabályokból származó riasztásokról.

- **A vadászati** lekérdezések lehetővé teszik a biztonsági vizsgálók számára, hogy a gyakori vadászati forgatókönyvek összefüggésében fenyegetésjelzőket használjanak.

- **A jegyzetfüzetek fenyegetésjelzőket** használhatnak a nomádok vizsgálatakor és a rosszindulatú viselkedések keresésében.

A fenyegetésjelzőket az Azure Sentinel beviheti a következő szakaszban felsorolt integrált fenyegetésfelderítési platform (TIP) egyik termékével, a TAXII-kiszolgálókhoz való csatlakozással vagy a [Microsoft Graph Security tiIndicators API-val](https://aka.ms/graphsecuritytiindicators)való közvetlen integrációval.

## <a name="integrated-threat-intelligence-platform-products"></a>Integrált fenyegetésfelderítési platform termékek

- [MISP nyílt forráskódú fenyegetésintelligencia platform](https://www.misp-project.org/)
    
    A misp-tárolóba való átáttelepítéshez misp-példányokat tartalmazó mintaparancsfájlt a [MISP-hez a Microsoft Graph Security Script](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP)című témakörben tanévenként láthatja el.

- [Palo Alto Hálózatok MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Az útmutató utasításokat az [IRC-k küldése a Microsoft Graph Security API-ba a MineMeld használatával című témakörben találja.](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540)

- [ThreatConnect Platform](https://threatconnect.com/solution/)

    További információt a [ThreatConnect-integrációk](https://threatconnect.com/integrations/) című témakörben talál, és keresse meg a Microsoft Graph Security API-t a lapon.


## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Csatlakoztassa az Azure Sentinelt a fenyegetésfelderítési platformhoz

## <a name="prerequisites"></a>Előfeltételek  

- A Globális rendszergazda vagy a Biztonsági rendszergazda Azure AD szerepköre engedélyeket ad a TIP-terméknek vagy a Microsoft Graph Security tiIndicators API-val közvetlenül integráló egyéni alkalmazásnak.

- Olvasási és írási engedélyeket az Azure Sentinel munkaterületa fenyegetésjelzők tárolásához.

## <a name="instructions"></a>Utasítások

1. [Regisztráljon egy alkalmazást](/graph/auth-v2-service#1-register-your-app) az Azure Active Directoryban egy alkalmazásazonosító, az alkalmazástitkos adat és az Azure Active Directory-bérlői azonosító levételéhez. Ezekre az értékekre akkor van szükség, ha a Microsoft Graph Security tiIndicators API-val közvetlenül integráló integrált TIP-terméket vagy alkalmazást konfigurál.

2. [Konfigurálja az API-engedélyeket](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) a regisztrált alkalmazáshoz: Adja hozzá a Microsoft Graph alkalmazás engedélyét **ThreatIndicators.ReadWrite.OwnedBy** a regisztrált alkalmazáshoz.

3. Kérje meg az Azure Active Directory bérlői rendszergazdáját, hogy adja meg a rendszergazdai jóváhagyást a szervezet regisztrált alkalmazásához. Az Azure Portalon: **Az Azure Active Directory** > **alkalmazás regisztrációk** > **\<_alkalmazás neve_>** > **Megtekintése API-engedélyek** > **Megadása rendszergazdai hozzájárulás \< _a bérlő neve._>**

4. Konfigurálja a TIP-terméket vagy alkalmazást, amely közvetlen integrációt használ a Microsoft Graph Security tiIndicators API-val, hogy kijelzőket küldjön az Azure Sentinelnek a következők megadásával:
    
    a. A regisztrált alkalmazás azonosítójának, titkos és bérlői azonosítójának értékei.
    
    b. A céltermékhez adja meg az Azure Sentinel.
    
    c. A művelethez adja meg a riasztást.

5. Az Azure Portalon keresse meg az Azure > **Sentinel-adatösszekötők,** és válassza ki a **fenyegetés-intelligencia platformok (előzetes verzió)** összekötőt. **Azure Sentinel**

6. Válassza **az Összekötő lap megnyitása**lehetőséget, majd a **Csatlakozás**lehetőséget.

7. Az Azure Sentinelbe importált fenyegetésjelzők megtekintéséhez keresse meg az Azure Sentinel – Logs**SecurityInsights**elemet, és **bontsa** > ki a **ThreatIntelligenceIndicator csomópontot.**

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Az Azure Sentinel csatlakoztatása TAXII-kiszolgálókhoz

## <a name="prerequisites"></a>Előfeltételek  

- Olvasási és írási engedélyeket az Azure Sentinel munkaterületa fenyegetésjelzők tárolásához.

- TAXII 2.0 szerver URI és gyűjtemény azonosító.

## <a name="instructions"></a>Utasítások

1. Az Azure Portalon keresse meg az Azure > **Sentinel-adatösszekötők,** és válassza ki a **fenyegetés-intelligencia - TAXII (Előzetes)** összekötő. **Azure Sentinel**

2. Válassza **az Összekötő lap megnyitása lehetőséget**.

3. Adja meg a szükséges és a választható információkat a szövegmezőkben.

4. A TAXII 2.0 kiszolgálóhoz való csatlakozás engedélyezéséhez válassza a **Hozzáadás** lehetőséget.

5. Ha további TAXII 2.0 szerverei vannak: Ismételje meg a 3.

6. Az Azure Sentinelbe importált fenyegetésjelzők megtekintéséhez keresse meg az Azure Sentinel – Logs**SecurityInsights**elemet, és **bontsa** > ki a **ThreatIntelligenceIndicator csomópontot.**

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a fenyegetésfelderítési szolgáltatót az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, tekintse meg az alábbi cikkeket.

- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats.md)
