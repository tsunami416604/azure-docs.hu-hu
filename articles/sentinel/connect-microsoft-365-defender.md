---
title: Microsoft 365 Defender nyers adatvédelmeinak összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan tudja bevenni a Microsoft 365 Defenderből származó nyers események adatait az Azure Sentinelbe.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: e9e892ae08c65ee5c5008bbdc1b419d8dec30183
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935663"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Adatok összekötése Microsoft 365 Defender-ből az Azure Sentinelbe

> [!IMPORTANT]
>
> **Microsoft 365 Defender** korábbi nevén **Microsoft Threat Protection** vagy **MTP**.
>
> **A Microsoft Defender for Endpoint** korábban a **Microsoft Defender komplex veszélyforrások elleni védelmi** vagy **MDATP**néven ismert.
>
> Előfordulhat, hogy a régi nevek még mindig használatban vannak egy adott időszakban.

## <a name="background"></a>Háttér

Az új [Microsoft 365 Defender](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-threat-protection) -összekötő lehetővé teszi a **speciális vadászati** naplók továbbítását – a nyers események adattípusait – Microsoft 365 Defenderből az Azure sentinelbe. 

A [Microsoft Defender for Endpoint (MDATP)](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) a Microsoft 365 Defender biztonsági esernyőbe való integrálásával mostantól összegyűjtheti a Microsoft Defender a végpontok [speciális vadászati](https://aka.ms/mdatpAH) eseményeit az Microsoft 365 Defender-összekötő használatával, és közvetlenül az Azure Sentinel munkaterületen található új, célra épülő táblákba továbbíthatja őket. Ezek a táblák a Microsoft 365 Defender portálon használt sémára épülnek, így teljes körű hozzáférést biztosít a speciális vadászati naplók teljes készletéhez, és lehetővé teszi a következőket:

- A meglévő Microsoft Defender ATP Advanced Hunting-lekérdezéseket egyszerűen átmásolhatja az Azure Sentinelbe.

- A nyers eseménynaplók segítségével további elemzéseket biztosíthat a riasztásokról, a vadászatokról és a vizsgálatokról, valamint az eseményeket az Azure Sentinel további adatforrásaiból származó adatokkal társíthatja.

- A naplókat a Microsoft Defender for Endpoint vagy a Microsoft 365 Defender alapértelmezett megőrzési idejének megtartásával tárolja. Ezt úgy teheti meg, hogy beállítja a munkaterület megőrzését, vagy a Log Analytics.

> [!IMPORTANT]
>
> A Microsoft 365 Defender-összekötő jelenleg nyilvános előzetes verzióban érhető el. Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

- Érvényes licenccel kell rendelkeznie a Microsoft Defender for Endpoint szolgáltatáshoz, a [Microsoft Defender beállítása végpont üzembe helyezése](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)című témakörben leírtak szerint. 

- A felhasználónak hozzá kell rendelnie a globális rendszergazdai szerepkört a bérlőn (Azure Active Directory).

## <a name="connect-to-microsoft-365-defender"></a>Kapcsolódás Microsoft 365 Defenderhez

Ha a Microsoft Defender for Endpoint üzembe helyezését és az adataik betöltését végzi, az eseménynaplók könnyen továbbíthatók az Azure Sentinel szolgáltatásba.

1. Az Azure Sentinelben válassza az **adatösszekötők**lehetőséget, válassza **Microsoft 365 Defender (előzetes verzió)** elemet a katalógusból, és válassza az **összekötő lap megnyitása**lehetőséget.

1. A következő típusú események gyűjthetők a kapcsolódó speciális vadászati táblákból. A gyűjteni kívánt eseménytípus jelölőnégyzetének megjelölése:

    | Események típusa | Tábla neve |
    |-|-|
    | Számítógép adatai (beleértve az operációs rendszer adatait) | DeviceInfo |
    | Gépek hálózati tulajdonságai | DeviceNetworkInfo |
    | Folyamat létrehozása és kapcsolódó események | DeviceProcessEvents |
    | Hálózati kapcsolat és kapcsolódó események | DeviceNetworkEvents |
    | Fájlok létrehozása, módosítása és más fájlrendszerbeli események | DeviceFileEvents |
    | Beállításjegyzékbeli bejegyzések létrehozása és módosítása | DeviceRegistryEvents |
    | Bejelentkezések és egyéb hitelesítési események | DeviceLogonEvents |
    | DLL-betöltési események | DeviceImageLoadEvents |
    | További események típusai | DeviceEvents |
    |

1. Kattintson a **módosítások alkalmazása**lehetőségre. 

1. A Log Analytics speciális vadászati tábláinak lekérdezéséhez írja be a tábla nevét a fenti listából a lekérdezési ablakban.

## <a name="verify-data-ingestion"></a>Adatfeldolgozás ellenőrzése

Az összekötő oldal adatdiagramja azt jelzi, hogy adatfeldolgozást végez. Láthatja, hogy egyetlen sort mutat be, amely összesíti az események mennyiségét az összes engedélyezett táblán. Miután engedélyezte az összekötőt, a következő KQL-lekérdezéssel hozhatja elő az esemény-kötetek hasonló gráfját egyetlen táblához (módosítsa a *DeviceEvents* táblát a választott táblázatra):

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

A **következő lépések** lapon talál néhány, a tartalmazott példákat tartalmazó lekérdezést is. Ezeket a helyszínen futtathatja, vagy módosíthatja és mentheti.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan szerezheti be a Microsoft Defenderből származó nyers események adatait az Azure Sentinel-be a Microsoft 365 Defender Connector használatával. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).
