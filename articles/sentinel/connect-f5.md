---
title: F5-adatok csatlakoztatása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakoztathatja az F5-adatokat az Azure Sentinelhez.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588263"
---
# <a name="connect-f5-to-azure-sentinel"></a>Az F5 csatlakoztatása az Azure Sentinelhez

Ez a cikk bemutatja, hogyan csatlakoztathatja az F5-ös készüléket az Azure Sentinelhez. Az F5-ös adatösszekötő lehetővé teszi, hogy könnyedén csatlakoztassa az F5-naplókat az Azure Sentinelhez irányítópultok megtekintéséhez, egyéni riasztások létrehozásához és a vizsgálat javításához. Az F5 használata az Azure Sentinelen további betekintést nyújt a szervezet internethasználatába, és javítja a biztonsági üzemeltetési képességeit. 

## <a name="configure-your-f5-to-send-cef-messages"></a>Az F5 beállítása CEF-üzenetek küldésére

1. Nyissa meg [az F5 Alkalmazásbiztonsági eseménynaplózás konfigurálása](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)című témakört, és kövesse a távoli naplózás beállításához szükséges utasításokat az alábbi irányelvek szerint:
   - Állítsa a **távtároló típusát** **CEF-re.**
   - Állítsa a **protokollt** **TCP-re.**
   - Állítsa az **IP-címet** a Syslog-kiszolgáló IP-címére.
   - Állítsa a **portszámot** **514-re,** vagy az ügynök által használt portra.
   - A maximális **lekérdezési karakterlánc mérete** az ügynökben beállított méretre állítható be.

1. Ha a megfelelő sémát szeretné használni a Log `CommonSecurityLog`Analytics szolgáltatásban a CEF-eseményekhez, keresse meg a keresett főt.

1. Folytassa a [3.](connect-cef-verify.md)


## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja az F5-öt az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats.md)
- Az adatok figyeléséhez [használjon munkafüzeteket.](tutorial-monitor-your-data.md)

