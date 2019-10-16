---
title: A fenyegetési intelligencia-adatok összekapcsolásának az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan kapcsolódhat a veszélyforrások felderítéséhez az Azure Sentinel szolgáltatáshoz.
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: 44b3830465bf2b5aa06612aa868b086b120f1ece
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372277"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Adatok összekötése a veszélyforrások felderítését biztosító szolgáltatóktól

> [!IMPORTANT]
> A Threat Intelligence Platforms adatösszekötő az Azure Sentinel szolgáltatásban jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure Sentinel lehetővé teszi a szervezet által használt veszélyforrás-mutatók importálását, ami növelheti a biztonsági elemzők számára az ismert fenyegetések észlelésének és rangsorolásának képességét. Az Azure Sentinel számos funkciója elérhetővé válik vagy bővül:

- Az **elemzések** olyan ütemezett sablonok készletét foglalják magukban, amelyek lehetővé teszik, hogy riasztásokat és incidenseket hozzon létre, amelyek a fenyegetési mutatók naplófájljainak egyezésén alapulnak.

- A **munkafüzetek** összesített információkat biztosítanak az Azure sentinelbe importált veszélyforrási mutatókról, valamint az elemzési szabályok alapján létrehozott összes riasztásról, amelyek megfelelnek a fenyegetési mutatóknak.

- A **vadászati** lekérdezések lehetővé teszik a biztonsági nyomozók számára, hogy a gyakori vadászati forgatókönyvek kontextusában használják a veszélyforrások mutatóit.

- A **jegyzetfüzetek** a veszélyforrások kivizsgálásakor és a rosszindulatú viselkedések kivizsgálása során is használhatják a fenyegetéseket.

A veszélyforrások indikátorait továbbíthatja az Azure Sentinelnek a következő szakaszban felsorolt integrált veszélyforrások elleni hírszerzési platform (TIP) termékeinek egyikével, illetve a [Microsoft Graph Security TIINDICATORS API](https://aka.ms/graphsecuritytiindicators)-val való közvetlen integráció használatával.

## <a name="integrated-threat-intelligence-platform-products"></a>Integrált veszélyforrások elleni hírszerzési platform termékei

- [MICSKÓ nyílt forráskódú veszélyforrások elleni intelligencia platform](https://www.misp-project.org/)
    
    A MICSKÓ-példányokkal rendelkező ügyfelek számára a veszélyforrások Microsoft Graph biztonsági API-ra történő áttelepítéséhez a következő témakörben talál további információt: [micskó Microsoft Graph biztonsági parancsfájl](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Palo Alto hálózatok MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Útmutatásért lásd: [IOCs küldése a Microsoft Graph Security API-nak a MineMeld használatával](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [ThreatConnect platform](https://threatconnect.com/solution/)


## <a name="prerequisites"></a>Előfeltételek  

- A globális rendszergazda vagy a biztonsági rendszergazda Azure AD szerepköre, hogy engedélyeket adjon a tipp termékének vagy egyéni alkalmazásának, amely közvetlen integrációt használ a Microsoft Graph Security tiIndicators API-val.

- Olvasási és írási engedélyek az Azure Sentinel-munkaterülethez a veszélyforrások mutatóinak tárolásához.

## <a name="connect-azure-sentinel-to-your-threat-intelligence-provider"></a>Az Azure Sentinel összekötése a veszélyforrások elleni hírszerző szolgáltatóval

1. Azure Active Directory [alkalmazás regisztrálása](/graph/auth-v2-service#1-register-your-app) az alkalmazás-azonosító, az alkalmazás titkos kódjának és a Azure Active Directory bérlői azonosító beszerzéséhez. Ezekre az értékekre akkor van szükség, amikor az integrált tipp termékét vagy alkalmazását a Microsoft Graph Security tiIndicators API-val való közvetlen integrációt használó alkalmazáshoz konfigurálja.

2. [API-engedélyek konfigurálása](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) a regisztrált alkalmazáshoz: adja hozzá a Microsoft Graph alkalmazás engedély **ThreatIndicators. ReadWrite. OwnedBy** a regisztrált alkalmazáshoz.

3. Kérje meg a Azure Active Directory bérlői rendszergazdát, hogy rendszergazdai jóváhagyást adjon a szervezete regisztrált alkalmazásának. A Azure Portal: **Azure Active Directory** > **Alkalmazásregisztrációk** >  **\<_alkalmazás neve_>**  > **View API-engedélyek**0**engedélyezési rendszergazdai jóváhagyás 2_bérlő neve_ @no__t – 14**.

4. Konfigurálja a tipp termékét vagy alkalmazását, amely közvetlen integrációt használ Microsoft Graph Security tiIndicators API-val a mutatók Azure Sentinelbe való küldéséhez az alábbiak megadásával:
    
    a. A regisztrált alkalmazás AZONOSÍTÓjának, titkos kódjának és bérlői AZONOSÍTÓjának értékei.
    
    b. A megcélzott termék esetében az Azure Sentinelt kell megadnia.
    
    c. A művelethez válassza a riasztást.

5. A Azure Portal navigáljon az **Azure Sentinel** > **adatösszekötők** elemre, majd válassza ki a **Threat Intelligence Platforms (előzetes verzió)** összekötőt.

6. Válassza az **összekötő megnyitása lapot**, majd a **Csatlakozás**lehetőséget.

7. Az Azure Sentinelbe importált veszélyforrások mutatóinak megtekintéséhez navigáljon az **Azure Sentinel-Logs** > **SecurityInsights**, majd bontsa ki a **ThreatIntelligenceIndicator**csomópontot.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a veszélyforrások elleni intelligencia szolgáltatója az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket.

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).
