---
title: A biztonsági megoldás konfigurálása a CEF-adatkapcsolatok Azure Sentinel Preview-hoz való összekapcsolásához | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a biztonsági megoldást a CEF-alapú adatkapcsolatok Azure Sentinelhez való összekapcsolásához.
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
ms.openlocfilehash: 1c25e48bd46f0d37330f693cb4d6538e7bc29c4b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367240"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>2. lépés: a biztonsági megoldás konfigurálása CEF-üzenetek küldésére

Ebben a lépésben a szükséges konfigurációs módosításokat fogja végrehajtani a biztonsági megoldásban, hogy a naplók elküldjék a CEF-ügynöknek.

## <a name="configure-a-solution-with-a-connector"></a>Megoldás konfigurálása összekötővel

Ha a biztonsági megoldás már rendelkezik egy meglévő összekötővel, használja az összekötő-specifikus utasításokat az alábbiak szerint:

- [AI Vectra Detect](connect-ai-vectra-detect.md)
- [Ellenőrzőpont](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 ASM](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>Bármely más megoldás konfigurálása

Ha nem létezik összekötő az adott biztonsági megoldáshoz, a következő általános útmutatást követve továbbíthatja a naplókat a CEF-ügynöknek.

1. A CEF-üzenetek küldésére vonatkozó megoldás konfigurálásának lépéseihez lépjen az adott konfigurációs cikkhez. Ha a megoldás nem szerepel a listáján, akkor a készüléken meg kell adnia ezeket az értékeket, hogy a berendezés a szükséges formátumú naplókat a szükséges formátumban küldje el az Azure Sentinel syslog-ügynöknek a Log Analytics ügynök alapján. Ezeket a paramétereket módosíthatja a berendezésben, ha az Azure Sentinel-ügynök syslog démonán is módosítja őket.
    - Protokoll = TCP
    - Port = 514
    - Format = CEF
    - IP-cím – ügyeljen arra, hogy a CEF üzeneteket az erre a célra kijelölt virtuális gép IP-címére küldje el.

   > [!NOTE]
   > Ez a megoldás a syslog RFC 3164 vagy az RFC 5424 szolgáltatást támogatja.

1. A CEF-események Log Analytics vonatkozó sémájának használatához keresse meg a következőt: `CommonSecurityLog` .

1. Folytassa a 3. LÉPÉSsel: a [kapcsolat ellenőrzése](connect-cef-verify.md).

## <a name="next-steps"></a>További lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók a CEF-készülékek az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).
