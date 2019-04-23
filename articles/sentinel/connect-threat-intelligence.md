---
title: Csatlakozás Azure-on Előzetesben Sentinel-fenyegetésfelderítési adatok |} A Microsoft Docs
description: Ismerje meg a fenyegetésfelderítési adatok kapcsolódás Azure Sentinel.
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 56412543-5664-44c1-b026-2dbaf78a9a50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: a33b00e4e553824f1f719be2c8518487241f0a11
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786184"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Csatlakozás a data-fenyegetésfelderítési szolgáltatóktól vásárolja 

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Után az adatok eseménysorozatot Azure Sentinel, gazdagabbá teheti azt a fenyegetésfelderítés hírcsatorna a a szervezetben használt. 

Ahhoz, hogy közötti ellenőrizze a riasztások és a szabályok a valós fenyegetések felderítése, például ha egy adott IP-cím, riasztást kap a threat intelligence szolgáltató integrációs fogja tudni, hogy tudja, az IP-cím nemrégiben megtalálható-e a rosszindulatú is lehet , Az azure Sentinel segítségével az integráció [fenyegetésfelderítési szolgáltatóktól vásárolja fenyegetés](https://aka.ms/graphsecuritytips). 

Fenyegetésfelderítési szolgáltatóktól vásárolja származó naplók is streamelése az Azure-Sentinel egyetlen kattintással. Ez a kapcsolat lehetővé teszi a különböző típusú observables például IP-cím, tartomány, URL-CÍMÉT tartalmazó mutatók építhet és fájlkivonat és létrehozását az egyéni szabályok az Azure-Sentinel riasztást küld.  
> [!NOTE]
> Akkor is bemeneteként testre szabott threat mutatók Azure Sentinel-riasztási szabályok, irányítópultok és vadászat forgatókönyvekhez használható integrálásával a [Microsoft Graph biztonsági tiIndicator](https://aka.ms/graphsecuritytiindicators) entitás vagy egy [Microsoft Graph biztonsági Threat Intelligence Platform integrált](https://aka.ms/graphsecuritytips).

## <a name="prerequisites"></a>Előfeltételek  

- Globális rendszergazdai vagy biztonsági rendszergazdai jogosultsággal rendelkező felhasználó 

- Threat intelligence alkalmazás integrálva van a Microsoft Intelligent Security Graph 

## <a name="connect-to-threat-intelligence"></a>Csatlakozhat a fenyegetések felderítése 

1. Ha már használja egy threat intelligence szolgáltató, mindenképpen keresse meg a TIP-alkalmazást, és engedélyezi a mutatók küldeni a Microsoftnak, és adja meg a szolgáltatás az Azure-Sentinel.  

2. Az Azure-Sentinel, válassza **adatösszekötők** és kattintson a **Fenyegetésészlelési intelligencia** csempére.

3. Kattintson a **Connect** (Csatlakozás) gombra. 

4. A megfelelő sémát használ a Log Analytics fenyegetésészlelési intelligenciával foglalkozó hírcsatornákra, keresse meg **ThreatIntelligenceIndicator**. 

 
## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megismerkedett a Fenyegetésfelderítés szolgáltató csatlakozni az Azure-Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket.

- Ismerkedés az Azure Sentinel-, Microsoft Azure-előfizetéssel kell. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/).
- Ismerje meg, hogyan [előkészíteni az adatok Azure Sentinel-](quickstart-onboard.md), és [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
