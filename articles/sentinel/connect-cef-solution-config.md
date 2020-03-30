---
title: A biztonsági megoldás konfigurálása a CEF-adatok azure Sentinel Preview-hoz való csatlakoztatásához| Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhatja a biztonsági megoldást a CEF-adatok Azure Sentinelhez való csatlakoztatásához.
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: bdb76954b1db8135d8a36d6658bb7fff274ac126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588450"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>2. LÉPÉS: A biztonsági megoldás konfigurálása CEF-üzenetek küldésére

Ebben a lépésben elvégzi a szükséges konfigurációs módosításokat a biztonsági megoldás maga a naplók küldése a CEF-ügynök.

## <a name="configure-a-solution-with-a-connector"></a>Megoldás konfigurálása összekötővel

Ha a biztonsági megoldás már rendelkezik egy meglévő összekötővel, az összekötő-specifikus utasításokat az alábbiak szerint használja:

- [Ellenőrzőpont](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>Bármely más megoldás konfigurálása
Ha az adott biztonsági megoldáshoz nem létezik összekötő, kövesse az alábbi általános utasításokat a naplók CEF-ügynöknek történő továbbításához.

1. A megoldás CEF-üzenetek küldésére történő konfigurálásával kapcsolatos tudnivalókat az adott konfigurációs cikkben találja. Ha a megoldás nem szerepel a listában, a készüléken be kell állítania ezeket az értékeket, hogy a készülék elküldi a szükséges naplókat a szükséges formátumban az Azure Sentinel Syslog ügynök, a Log Analytics-ügynök alapján. Ezeket a paramétereket a készülékben, mindaddig, amíg azokat is módosíthatja a Syslog démon az Azure Sentinel ügynök.
    - Protokoll = TCP
    - Port = 514
    - Formátum = CEF
    - IP-cím - győződjön meg róla, hogy a CEF üzeneteket az erre a célra kijelölt virtuális gép IP-címére küldje.

   > [!NOTE]
   > Ez a megoldás támogatja a Syslog RFC 3164 vagy RFC 5424-et.


1. Ha a megfelelő sémát szeretné használni a Log `CommonSecurityLog`Analytics szolgáltatásban a CEF-eseményekhez, keresse meg a keresett főt.

1. Folytassa a [3.](connect-cef-verify.md)

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a CEF-készülékeket az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats.md)

