---
title: Kimeneti hibák házirendjei a Azure Stream Analyticsban
description: További információ a Azure Stream Analyticsban elérhető kimeneti hibákkal kapcsolatos szabályzatokról.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 923e8d1ffc3e606c16226b4b2ccbdcfaf4d3e9f1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123421"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Azure Stream Analytics kimeneti hiba házirendje
Ez a cikk a Azure Stream Analytics konfigurálható kimeneti adathibákat ismerteti.

A kimeneti adatok hibáinak kezelése házirend csak olyan Adatátalakítási hibákra vonatkozik, amelyek akkor jelentkeznek, amikor egy Stream Analytics feladatokból származó kimeneti esemény nem felel meg a cél fogadó sémájának. Ezt a házirendet az **Újrapróbálkozás** vagy az **eldobás** lehetőség kiválasztásával konfigurálhatja. A Azure Portal, míg a Stream Analytics feladatban a **Konfigurálás** területen válassza a **hiba házirend** lehetőséget a kijelöléshez.

![Azure Stream Analytics kimeneti hiba házirend helye](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Retry
Ha hiba lép fel, Azure Stream Analytics újrapróbálkozik az esemény határozatlan idejű írásával, amíg az írási művelet nem sikerül. Az újrapróbálkozások esetében nincs időkorlát. Végül az összes további eseményt le kell tiltani az újrapróbálkozást követően. Ez a beállítás az alapértelmezett kimeneti hiba kezelési szabályzata.

## <a name="drop"></a>Legördülő
Az Azure Stream Analytics elvet minden kimeneti eseményt, amely adatkonvertálási hibát eredményez. Az elvetett események később nem állíthatók helyre a feldolgozás újbóli megkísérléséhez.


A rendszer az összes átmeneti hibát (például hálózati hibákat) újrapróbálkozik, függetlenül attól, hogy milyen kimeneti hiba történik a házirend-konfigurációban.


## <a name="next-steps"></a>Következő lépések
[A Azure Stream Analytics hibaelhárítási útmutatója](./stream-analytics-troubleshoot-query.md)