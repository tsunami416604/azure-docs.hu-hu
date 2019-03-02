---
title: Az Azure-on Előzetesben Sentinel-DNS-adatok gyűjtése |} A Microsoft Docs
description: Ismerje meg, hogyan gyűjtheti az Azure-Sentinel a DNS-adatok.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 088a8f5034593b222a8a204feaa40b9cf5374059
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57241884"
---
# <a name="connect-your-domain-name-server"></a>Connect your domain name server

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bármely tartomány neve Server (DNS) futó Windows Azure-Sentinel kapcsolódhat. Ez történik, a DNS-gépen egy ügynök telepítésével. Naplózza a DNS-sel, mélyebb bepillantást nyerhet biztonsági, teljesítménnyel és műveletek kapcsolatos elemzéseket kaphat a DNS-infrastruktúra, a szervezet által gyűjtött, elemzése, és naplókezelője elemzési és auditnaplók és egyéb kapcsolódó adatokat a DNS-kiszolgálókról.

Ha engedélyezi a DNS-naplógyűjtés, a következőket teheti:
- Azok az ügyfelek, kártékony tartománynevek azonosítása
- Elavult erőforrásrekordok azonosítása
- Gyakran lekérdezett tartománynevek és beszédes DNS-ügyfelek azonosítása
- DNS-kiszolgálók terhelését nézet kérelem
- Dinamikus DNS regisztrációs hibák megtekintése

## <a name="how-it-works"></a>Működés

DNS-gyűjtemény egy ügynök telepítésével a DNS-gépen történik. Az ügynök lekéri az események a DNS szolgáltatásból, és továbbítja őket a Log Analytics.

## <a name="connect-your-dns-appliance"></a>Csatlakozás a DNS-berendezés

1. Az Azure-Sentinel-portálon válassza **adatgyűjtés** , és válassza a **DNS** csempére.
1. Ha a DNS-gép az Azure-ban:
    1. Kattintson a **letöltése és telepítése az ügynök a Windows virtuális gépek**.
    1. Az a **virtuális gépek** listájához, válassza ki a kívánt streamelése az Azure-Sentinel DNS gép. Ellenőrizze, hogy ez egy Windows virtuális Gépet.
    1. A virtuális gép megnyíló ablakban kattintson a **Connect**.  
    1. Kattintson a **engedélyezése** a a **DNS összekötő** ablak. 

2. Ha a DNS-számítógép nem egy Azure virtuális Gépen:
    1. Kattintson a **letöltése és telepítése a Windows Azure-beli gépek ügynök**.
    1. Az a **közvetlen ügynök** ablakában válassza **töltse le Windows-ügynök (64 bites)** vagy **töltse le Windows-ügynök (32 bites)**.
    1. Telepítse az ügynököt a DNS-gépen. Másolás a **munkaterület-Azonosítót**, **elsődleges kulcs**, és **másodlagos kulcs** , és használja őket, amikor a rendszer kéri, a telepítés során.

3. A Log Analytics a megfelelő sémát használ a DNS-naplókat, keresse meg **DnsEvents**.

## <a name="validate"></a>Érvényesítés 

A Log Analyticsben, keresse meg a séma **DnsEvents** , és ellenőrizze, hogy nincsenek az eseményeket.

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan szeretne csatlakozni a helyi DNS-berendezéseit Azure Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).
