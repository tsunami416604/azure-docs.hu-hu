---
title: DNS-adatkapcsolatok összekötése az Azure Sentinelben | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók a DNS-adatkészletek az Azure Sentinelben.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: c5e58f496176ec0f1b8317c8b862a8ef2ffa434d
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262733"
---
# <a name="connect-your-domain-name-server"></a>Connect your domain name server

> [!IMPORTANT]
> A DNS-adatösszekötő az Azure Sentinel szolgáltatásban jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Windows rendszeren futó összes DNS-kiszolgálót összekapcsolhatja az Azure Sentinel szolgáltatással. Ezt úgy teheti meg, hogy egy ügynököt telepít a DNS-gépre. A DNS-naplók használatával biztonsági, teljesítménybeli és működés közbeni elemzéseket nyerhet a szervezet DNS-infrastruktúrájában azáltal, hogy összegyűjti, elemzi és korrelálja a DNS-kiszolgálókkal kapcsolatos elemzési és naplózási naplókat és egyéb kapcsolódó adatait.

A DNS-naplózási kapcsolatok engedélyezésekor a következőket teheti:
- Azonosítsa azokat az ügyfeleket, akik kártékony tartományneveket próbálnak megoldani
- Elavult erőforrásrekordok azonosítása
- A gyakran lekérdezett tartománynevek és a beszédes DNS-ügyfelek azonosítása
- Kérések terhelésének megtekintése DNS-kiszolgálókon
- Dinamikus DNS-regisztrálási hibák megtekintése

## <a name="connected-sources"></a>Összekapcsolt források

A következő táblázat ismerteti a megoldás által támogatott csatlakoztatott forrásokat:

| **Csatlakoztatott forrás** | **Támogatás** | **Leírás** |
| --- | --- | --- |
| [Windows-ügynökök](../azure-monitor/platform/agent-windows.md) | Igen | A megoldás a DNS-adatok Windows-ügynököktől gyűjti. |
| [Linux-ügynökök](../azure-monitor/learn/quick-collect-linux-computer.md) | Nem | A megoldás nem DNS-információkat gyűjtsön a közvetlen Linux-ügynökök. |
| [System Center Operations Manager felügyeleti csoport](../azure-monitor/platform/om-agents.md) | Igen | A megoldás a DNS-adatok egy csatlakoztatott az Operations Manager felügyeleti csoportban lévő ügynököktől gyűjti. Nem szükséges közvetlen kapcsolódás a Operations Manager ügynöktől a Azure Monitorhoz. Adatok lesznek továbbítva a felügyeleti csoportból a Log Analytics-munkaterületet. |
| [Azure Storage-fiók](../azure-monitor/platform/collect-azure-metrics-logs.md) | Nem | A megoldás az Azure storage nem használja. |

### <a name="data-collection-details"></a>Adatok gyűjtése részletei

A megoldás gyűjti DNS-leltár- és DNS-esemény kapcsolatos adatokat a DNS-kiszolgálók ahol egy Log Analytics-ügynök telepítve van. A DNS PowerShell-parancsmagok futtatása leltárhoz kapcsolódó adatok, például a DNS-kiszolgálók, zónák és rekordok, számát gyűjti. Az adatok két naponta egyszer frissül. Az esemény-kapcsolódó adatok gyűjtése történik, közel valós időben a [elemzési és auditnaplók](https://technet.microsoft.com/library/dn800669.aspx#enhanc) továbbfejlesztett DNS-naplózás és diagnosztika a Windows Server 2012 R2 által biztosított.


## <a name="connect-your-dns-appliance"></a>A DNS-berendezés összekötése

1. Az Azure Sentinel portálon válassza az **adatösszekötők** lehetőséget, és válassza a **DNS (előzetes verzió)** csempét.
1. Ha a DNS-gépek az Azure-ban vannak:
    1. Kattintson **az ügynök telepítése az Azure Windows rendszerű virtuális gépen**elemre.
    1. A **virtuális gépek** listájában válassza ki azt a DNS-gépet, amelyet az Azure sentinelbe szeretne továbbítani. Győződjön meg arról, hogy ez egy Windows rendszerű virtuális gép.
    1. A virtuális gép megnyíló ablakában kattintson a **kapcsolat**elemre.  
    1. A **DNS-összekötő** ablakban kattintson az **Engedélyezés** elemre. 

2. Ha a DNS-számítógép nem Azure-beli virtuális gép:
    1. Kattintson **az ügynök telepítése nem Azure-beli gépekre**elemre.
    1. A **közvetlen ügynök** ablakban válassza a Windows- **ügynök letöltése (64 bites)** vagy a **windows-ügynök letöltése (32 bit)** lehetőséget.
    1. Telepítse az ügynököt a DNS-gépre. Másolja a **munkaterület-azonosítót**, az **elsődleges kulcsot**és a **másodlagos kulcsot** , és használja őket, ha a telepítés során a rendszer kéri.

3. Ha a DNS-naplókhoz a Log Analytics vonatkozó sémát szeretné használni, keresse meg a **DnsEvents**.

## <a name="validate"></a>Érvényesítés 

A Log Analyticsban keresse meg a séma **DnsEvents** , és győződjön meg arról, hogy vannak események.

## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók a helyi DNS-készülékek az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
