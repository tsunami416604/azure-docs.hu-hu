---
title: Kimeneti hiba házirendek az Azure Stream Analytics szolgáltatásban
description: Ismerje meg a kimeneti hibakezelés az Azure Stream Analytics szolgáltatásban elérhető házirend.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: b31530966d2c5ca9a3f82f3e74ba349e66053a83
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109787"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Az Azure Stream Analytics kimeneti hibakezelési házirend
Ez a cikk ismerteti a kimeneti adatok hibakezelési házirendeket, amelyek konfigurálhatók az Azure Stream Analytics szolgáltatásban.

Kimeneti adathibák kezelése szabályzatok érvényesek az Adatátalakítási hibák, amelyek akkor jelentkeznek, amikor egy Stream Analytics-feladat által előállított kimeneti esemény, csak a nem felel meg a cél fogadó sémája. Ezt a házirendet konfigurálhatja kiválasztásával vagy **újra** vagy **Drop**. Az Azure Portalon alatt a Stream Analytics-feladat, miközben **konfigurálása**válassza **hibakezelési házirend** , hogy a kijelölt.

![Az Azure Stream Analytics kimeneti hiba történt a házirend helye](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Retry
Ha hiba történik, az Azure Stream Analytics próbálkozások határozatlan ideig, amíg nem fejeződik az írást az esemény írása. Nincs újrapróbálkozások nincs időkorlát. Végül minden ezt követő események megoldásokig által blokkolt az eseményt, amely újból próbálkozik. Ez a beállítás az alapértelmezett kimeneti hibakezelési házirend.

## <a name="drop"></a>Elvetés
Az Azure Stream Analytics csökken, minden olyan kimeneti esemény, amely egy Adatátalakítási hiba eredményez. Az eldobott események az újrafeldolgozás később nem lehet helyreállítani.


Az összes átmeneti hibák (például hálózati hibák) rendszer újra próbálkozik, függetlenül a kimeneti hibakezelési házirend-konfigurációt.


## <a name="next-steps"></a>További lépések
[Az Azure Stream Analytics – hibaelhárítási útmutató](stream-analytics-troubleshooting-guide.md)
