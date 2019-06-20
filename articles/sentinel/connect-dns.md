---
title: Az Azure-on Előzetesben Sentinel-DNS-adatok csatlakoztatása |} A Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat az Azure-Sentinel DNS-adatok.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 4e6ed18a49a77f8061c975bdf3ecb085ebf71317
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190763"
---
# <a name="connect-your-domain-name-server"></a>Connect your domain name server

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bármely tartomány neve Server (DNS) futó Windows Azure-Sentinel kapcsolódhat. Ez történik, a DNS-gépen egy ügynök telepítésével. Naplózza a DNS-sel, mélyebb bepillantást nyerhet biztonsági, teljesítménnyel és műveletek kapcsolatos elemzéseket kaphat a DNS-infrastruktúra, a szervezet által gyűjtött, elemzése, és naplókezelője elemzési és auditnaplók és egyéb kapcsolódó adatokat a DNS-kiszolgálókról.

Ha engedélyezi a DNS kapcsolat, a következőket teheti:
- Azok az ügyfelek, kártékony tartománynevek azonosítása
- Elavult erőforrásrekordok azonosítása
- Gyakran lekérdezett tartománynevek és beszédes DNS-ügyfelek azonosítása
- DNS-kiszolgálók terhelését nézet kérelem
- Dinamikus DNS regisztrációs hibák megtekintése

## <a name="connected-sources"></a>Összekapcsolt források

A következő táblázat ismerteti a megoldás által támogatott csatlakoztatott forrásokat:

| **Csatlakoztatott forrás** | **Támogatás** | **Leírás** |
| --- | --- | --- |
| [Windows-ügynökök](../azure-monitor/platform/agent-windows.md) | Igen | A megoldás a DNS-adatok Windows-ügynököktől gyűjti. |
| [Linux-ügynökök](../azure-monitor/learn/quick-collect-linux-computer.md) | Nem | A megoldás nem DNS-információkat gyűjtsön a közvetlen Linux-ügynökök. |
| [System Center Operations Manager felügyeleti csoport](../azure-monitor/platform/om-agents.md) | Igen | A megoldás a DNS-adatok egy csatlakoztatott az Operations Manager felügyeleti csoportban lévő ügynököktől gyűjti. Az Azure monitornak közvetlen kapcsolat legyen az Operations Manager-ügynök nem kötelező. Adatok lesznek továbbítva a felügyeleti csoportból a Log Analytics-munkaterületet. |
| [Azure Storage-fiók](../azure-monitor/platform/collect-azure-metrics-logs.md) | Nem | A megoldás az Azure storage nem használja. |

### <a name="data-collection-details"></a>Adatok gyűjtése részletei

A megoldás gyűjti DNS-leltár- és DNS-esemény kapcsolatos adatokat a DNS-kiszolgálók ahol egy Log Analytics-ügynök telepítve van. A DNS PowerShell-parancsmagok futtatása leltárhoz kapcsolódó adatok, például a DNS-kiszolgálók, zónák és rekordok, számát gyűjti. Az adatok két naponta egyszer frissül. Az esemény-kapcsolódó adatok gyűjtése történik, közel valós időben a [elemzési és auditnaplók](https://technet.microsoft.com/library/dn800669.aspx#enhanc) továbbfejlesztett DNS-naplózás és diagnosztika a Windows Server 2012 R2 által biztosított.


## <a name="connect-your-dns-appliance"></a>Csatlakozás a DNS-berendezés

1. Az Azure-Sentinel-portálon válassza **adatösszekötők** , és válassza a **DNS** csempére.
1. Ha a DNS-gép az Azure-ban:
    1. Kattintson a **ügynök telepítése az Azure Windows virtuális gép**.
    1. Az a **virtuális gépek** listájához, válassza ki a kívánt streamelése az Azure-Sentinel DNS gép. Ellenőrizze, hogy ez egy Windows virtuális Gépet.
    1. A virtuális gép megnyíló ablakban kattintson a **Connect**.  
    1. Kattintson a **engedélyezése** a a **DNS összekötő** ablak. 

2. Ha a DNS-számítógép nem egy Azure virtuális Gépen:
    1. Kattintson a **ügynök telepítése nem Azure-beli gépek**.
    1. Az a **közvetlen ügynök** ablakában válassza **töltse le Windows-ügynök (64 bites)** vagy **töltse le Windows-ügynök (32 bites)** .
    1. Telepítse az ügynököt a DNS-gépen. Másolás a **munkaterület-Azonosítót**, **elsődleges kulcs**, és **másodlagos kulcs** , és használja őket, amikor a rendszer kéri, a telepítés során.

3. A Log Analytics a megfelelő sémát használ a DNS-naplókat, keresse meg **DnsEvents**.

## <a name="validate"></a>Érvényesítés 

A Log Analyticsben, keresse meg a séma **DnsEvents** , és ellenőrizze, hogy nincsenek az eseményeket.

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan szeretne csatlakozni a helyi DNS-berendezéseit Azure Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).
