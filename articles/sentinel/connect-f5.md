---
title: F5-adatkapcsolatok az Azure Sentinel-be | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók az F5-es adatkapcsolatok az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 6f33cecca1c67f91d0f2be64ab156f45ee500521
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588263"
---
# <a name="connect-f5-to-azure-sentinel"></a>Az F5 és az Azure Sentinel összekötése

Ez a cikk azt ismerteti, hogyan csatlakoztatható az F5-es készülék az Azure Sentinelhez. Az F5 adatösszekötővel könnyedén csatlakoztathatja az F5-naplókat az Azure Sentinel segítségével, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. Az F5 használata az Azure Sentinelben részletesebb információkat biztosít a szervezet internetes használatáról, és fokozza a biztonsági üzemeltetési képességeit. 

## <a name="configure-your-f5-to-send-cef-messages"></a>Az F5 beállítása CEF üzenetek küldéséhez

1. Lépjen az [alkalmazás-biztonsági események naplózása beállításnál az F5 billentyűre](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html), és kövesse az utasításokat a távoli naplózás beállításához az alábbi irányelvek alapján:
   - Állítsa a **távoli tárterület típusát** **CEF**értékre.
   - Állítsa a **protokollt** a **TCP**értékre.
   - Állítsa be az **IP-címet** a syslog-kiszolgáló IP-címére.
   - Állítsa a **portszámot** **514**-re, vagy azt a portot, amelyet az ügynöknek használnia kell.
   - Beállíthatja a **lekérdezési karakterlánc maximális méretét** az ügynökben beállított méretre.

1. A CEF-események Log Analytics vonatkozó sémájának használatához keresse meg a következőt `CommonSecurityLog`:.

1. Folytassa a [3. lépéssel: a kapcsolat ellenőrzése](connect-cef-verify.md).


## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható az F5 az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.

