---
title: Kimeneti hibaházirendek az Azure Stream Analytics szolgáltatásban
description: Ismerje meg az Azure Stream Analytics-ben elérhető kimeneti hibakezelési szabályzatokat.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 22112272bb302769e5969cf6995d486438deb41f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431615"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Az Azure Stream Analytics kimeneti hibaházirendje
Ez a cikk ismerteti a kimeneti adatok hibakezelési szabályzatok, amelyek konfigurálhatók az Azure Stream Analytics.

A kimeneti adatok hibakezelési szabályzatai csak azokra az adatkonverziós hibákra vonatkoznak, amelyek akkor fordulnak elő, ha a Stream Analytics-feladat által létrehozott kimeneti esemény nem felel meg a célfogadó sémájának. Ezt a házirendet az Újra vagy **a** **Ledobás**lehetőséget választva állíthatja be. Az Azure Portalon, míg a Stream Analytics-feladat, **a Konfigurálás**csoportban válassza **a Hibaszabályzat** a kiválasztáshoz.

![Az Azure Stream Analytics kimeneti hibaházirendjének helye](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Retry
Hiba esetén az Azure Stream Analytics újra megpróbálja az esemény határozatlan ideig történő írását, amíg az írás sikeres nem lesz. Az újrapróbálkozások nem időtúlodott. Végül az összes további esemény feldolgozása az újrapróbálkozó esemény blokkolja. Ez a beállítás az alapértelmezett kimeneti hibakezelési házirend.

## <a name="drop"></a>Csepp
Az Azure Stream Analytics elvet minden kimeneti eseményt, amely adatkonvertálási hibát eredményez. Az elvetett események később nem állíthatók helyre a feldolgozás újbóli megkísérléséhez.


Az összes átmeneti hibát (például a hálózati hibákat) a kimeneti hibakezelési házirend konfigurációjától függetlenül újra próbálkozik.


## <a name="next-steps"></a>További lépések
[Hibaelhárítási útmutató az Azure Stream Analytics szolgáltatáshoz](stream-analytics-troubleshooting-guide.md)
