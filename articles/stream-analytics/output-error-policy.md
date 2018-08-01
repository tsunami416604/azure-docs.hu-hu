---
title: Kimeneti hiba házirendek az Azure Stream Analytics szolgáltatásban
description: Ismerje meg a kimeneti hibakezelés az Azure Stream Analytics szolgáltatásban elérhető házirend.
services: stream-analytics
author: sidram
ms.author: sidram
manager: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/27/2018
ms.openlocfilehash: f854e8ce35ac9d0a99b4a7dc1cdc66724114a5c4
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391738"
---
# <a name="output-error-policy"></a>Kimeneti hibakezelési házirend
Ez a cikk ismerteti a kimeneti adatok hibakezelési házirendeket, amelyek konfigurálhatók az Azure Stream Analytics szolgáltatásban.

Kimeneti adathibák kezelése szabályzatok érvényesek az Adatátalakítási hibák, amelyek akkor jelentkeznek, amikor egy Stream Analytics-feladat által előállított kimeneti esemény, csak a nem felel meg a cél fogadó sémája. Ezt a házirendet konfigurálhatja kiválasztásával vagy **újra** vagy **Drop**. Az Azure Portalon alatt a Stream Analytics-feladat, miközben **konfigurálása**válassza **hibakezelési házirend** , hogy a kijelölt.

![Hiba történt a házirend - helye](./media/stream-analytics-error-policy/stream-analytics-error-policy-locate.PNG)


## <a name="retry"></a>Retry
Ha hiba történik, az Azure Stream Analytics próbálkozások határozatlan ideig, amíg nem fejeződik az írást az esemény írása. Nincs újrapróbálkozások nincs időkorlát. Végül minden ezt követő események megoldásokig által blokkolt az eseményt, amely újból próbálkozik. Ez a beállítás az alapértelmezett kimeneti hibakezelési házirend.

## <a name="drop"></a>Elvetés
Az Azure Stream Analytics csökken, minden olyan kimeneti esemény, amely egy Adatátalakítási hiba eredményez. Az eldobott események az újrafeldolgozás később nem lehet helyreállítani.


Az összes átmeneti hibák (például hálózati hibák) rendszer újra próbálkozik, függetlenül a kimeneti hibakezelési házirend-konfigurációt.


## <a name="next-steps"></a>További lépések
[Az Azure Stream Analytics – hibaelhárítási útmutató](stream-analytics-troubleshooting-guide.md)
