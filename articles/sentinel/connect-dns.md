---
title: DNS-adatok csatlakoztatása az Azure Sentinelben| Microsoft dokumentumok
description: Ismerje meg, hogyan kapcsolhatja össze a DNS-adatokat az Azure Sentinelben.
services: sentinel
documentationcenter: na
author: yelevin
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
ms.author: yelevin
ms.openlocfilehash: 6d43b82ecd13ac5e082d270ee44ce61fef763d2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588314"
---
# <a name="connect-your-domain-name-server"></a>A tartománynév-kiszolgáló csatlakoztatása

> [!IMPORTANT]
> Az Azure Sentinel DNS-adatösszekötője jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

A Windows rendszeren futó bármely DNS-kiszolgálót csatlakoztathatja az Azure Sentinelhez. Ez úgy történik, hogy ügynököt telepít a DNS-gépre. A DNS-naplók használatával a DNS-kiszolgálókanalitikus és naplóinak és egyéb kapcsolódó adatainak gyűjtésével, elemzésével és társításával biztonsággal, teljesítménnyel és műveletekkel kapcsolatos elemzéseket szerezhet a szervezet DNS-infrastruktúrájába.

Ha engedélyezi a DNS-naplókapcsolatot, a következőket teheti:
- A kártékony tartományneveket megkísérlő ügyfelek azonosítása
- Elavult erőforrásrekordok azonosítása
- A gyakran lekérdezett tartománynevek és a beszédes DNS-ügyfelek azonosítása
- A DNS-kiszolgálókon lévő kérelmek terhelésének megtekintése
- Dinamikus DNS-regisztrációs hibák megtekintése

## <a name="connected-sources"></a>Összekapcsolt források

Az alábbi táblázat a megoldás által támogatott csatlakoztatott forrásokat ismerteti:

| **Csatlakoztatott forrás** | **Támogatás** | **Leírás** |
| --- | --- | --- |
| [Windows-ügynökök](../azure-monitor/platform/agent-windows.md) | Igen | A megoldás DNS-adatokat gyűjt a Windows-ügynököktől. |
| [Linux-ügynökök](../azure-monitor/learn/quick-collect-linux-computer.md) | Nem | A megoldás nem gyűjt DNS-adatokat a közvetlen Linux-ügynököktől. |
| [System Center Operations Manage felügyeleti csoport](../azure-monitor/platform/om-agents.md) | Igen | A megoldás dns-adatokat gyűjt egy csatlakoztatott Operations Manager felügyeleti csoport ügynökeitől. Nincs szükség közvetlen kapcsolatra az Operations Manager-ügynök és az Azure Monitor között. Az adatok továbbítása a felügyeleti csoportból a Log Analytics munkaterületre. |
| [Azure tárfiók](../azure-monitor/platform/collect-azure-metrics-logs.md) | Nem | Az Azure storage-t a megoldás nem használja. |

### <a name="data-collection-details"></a>Adatgyűjtésrészletei

A megoldás dns-leltárt és DNS-eseményekkel kapcsolatos adatokat gyűjt azoktól a DNS-kiszolgálóktól, amelyeken a Log Analytics-ügynök telepítve van. A készlettel kapcsolatos adatokat, például a DNS-kiszolgálók, zónák és erőforrásrekordok számát a DNS PowerShell-parancsmagok futtatásával gyűjti a rendszer. Az adatok kétnaponta egyszer frissülnek. Az eseményekkel kapcsolatos adatokat a rendszer közel valós időben gyűjti a Windows Server 2012 R2 továbbfejlesztett DNS-naplózása és diagnosztikája által biztosított [elemzési és naplóból.](https://technet.microsoft.com/library/dn800669.aspx#enhanc)


## <a name="connect-your-dns-appliance"></a>A DNS-készülék csatlakoztatása

1. Az Azure Sentinel portálon válassza **az Adatösszekötők,** és válassza ki a **DNS (Előzetes)** csempe.
1. Ha a DNS-gépek az Azure-ban:
    1. Kattintson **az Ügynök telepítése az Azure Windows virtuális gépen**elemre.
    1. A **virtuális gépek** listájában válassza ki az Azure Sentinelbe streamelni kívánt DNS-gépet. Győződjön meg arról, hogy ez egy Windows virtuális gép.
    1. A virtuális gép számára megnyíló ablakban kattintson a **Csatlakozás gombra.**  
    1. Kattintson az **Engedélyezés gombra** a **DNS-összekötő** ablakban. 

2. Ha a DNS-gép nem Azure-beli virtuális gép:
    1. Kattintson **az Ügynök telepítése nem Azure-alapú gépekre**elemre.
    1. A **Közvetlen ügynök** ablakban válassza a **Windows-ügynök letöltése (64 bites)** vagy **a Windows-ügynök letöltése (32 bites) lehetőséget.**
    1. Telepítse az ügynököt a DNS-gépére. Másolja a **munkaterület-azonosítót**, **az elsődleges kulcsot**és a Másodlagos **kulcsot,** és használja őket, amikor a telepítés során a rendszer kéri.

3. Ha a Dns-naplókhoz a Log Analytics megfelelő sémáját szeretné használni, keresse meg a **DnsEvents (DnsEvents)** kifejezést.

## <a name="validate"></a>Érvényesítés 

A Log Analytics, keresse meg a séma **DnsEvents,** és győződjön meg arról, hogy vannak események.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a lekérdezések nem jelennek meg az Azure Sentinelben, kövesse az alábbi lépéseket, hogy a lekérdezések megfelelően jelenjenek meg:
1. Kapcsolja be a [DNS Analytics naplók at a szerverek](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)).
2. Győződjön meg arról, hogy a DNSEvents megjelenik a Log Analytics-gyűjteménylistában.
3. Kapcsolja be az [Azure DNS Analytics](../azure-monitor/insights/dns-analytics.md)szolgáltatást.
4. Az Azure DNS Analytics csoportban a **Konfiguráció**csoportban módosítsa a beállításokat, mentse el, majd szükség esetén módosítsa vissza, majd mentse újra.
5. Ellenőrizze az Azure DNS-elemzés, hogy a lekérdezések most jelennek meg.

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a helyszíni DNS-készülékeket az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
