---
title: Egyszerű naplók a Azure Monitorban (előzetes verzió) | Microsoft Docs
description: Az egyszerű naplók felülete lehetővé teszi, hogy alapszintű lekérdezéseket hozzon létre Azure Monitor anélkül, hogy közvetlenül interakciót KQL.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 4926e18aa6b00fe36608843ea5253903ace774e2
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90089108"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Egyszerű naplók a Azure Monitorban (előzetes verzió)
A Azure Monitor a KQL nyelv használatával [gazdag felhasználói élményt](get-started-portal.md) nyújt a [naplók](log-query-overview.md) létrehozásához. Előfordulhat, hogy nincs szükség a KQL teljes teljesítményére, és az alapszintű lekérdezési követelményekhez inkább egyszerűsített felhasználói élményt szeretne. Az egyszerű naplók felülete lehetővé teszi, hogy alapszintű lekérdezéseket hozzon létre anélkül, hogy közvetlenül interakciót KQL. Az egyszerű naplókat KQL tanulási eszközként is használhatja, mivel kifinomultabb lekérdezésekre van szükség.

> [!NOTE]
> Az egyszerű naplók jelenleg csak Cosmos DB és kulcstartók tesztelésére lettek implementálva. Ossza meg velünk a kapcsolatot a Microsofttal a [felhasználói hangon](https://feedback.azure.com/forums/913690-azure-monitor) keresztül, hogy segítsen megállapítani, hogy kiterjesztjük és Kiszabadítjuk ezt a funkciót.


## <a name="scope"></a>Hatókör
Az egyszerű naplók a kiválasztott erőforráshoz tartozó *AzureDiagnostics*, *AzureMetrics*és *AzureActivity* táblából származó adatok lekérését végzik. 

## <a name="using-simple-logs"></a>Egyszerű naplók használata
Navigáljon az Azure-előfizetésében található bármely Cosmos DB vagy Key Vaulthoz a [naplók log Analytics munkaterületen való összegyűjtéséhez konfigurált diagnosztikai beállításokkal](../platform/resource-logs.md#send-to-azure-storage). Az egyszerű naplók felületének megnyitásához kattintson a **figyelés** menü **naplók** elemére.

![Képernyőfelvétel: a figyelés menü, amelyen a naplók vannak kiválasztva.](media/simple-logs/menu.png)

Válasszon ki egy **mezőt** és egy **operátort** , és adjon meg egy **értéket** az összehasonlításhoz. Kattintson a lehetőségre, és **+** adja meg **és/vagy** adjon hozzá további feltételeket.

![A képernyőképen a naplók ablaktáblán a keresés látható, ahol az egyszerű naplók ki vannak választva.](media/simple-logs/criteria.png)

A lekérdezés eredményeinek megtekintéséhez kattintson a **Futtatás** gombra.

## <a name="view-and-edit-kql"></a>KQL megtekintése és szerkesztése
Válassza a **lekérdezéstervező** lehetőséget az egyszerű naplók lekérdezés által generált KQL megnyitásához a teljes log Analytics felületén. 

![Lekérdezésszerkesztő](media/simple-logs/query-editor.png)

Közvetlenül szerkesztheti a KQL, és más funkciókat is használhat Log Analytics például szűrőket az eredmények pontosításához.

![KQL szerkesztése](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Következő lépések

- Végezzen el egy oktatóanyagot a [Azure Portal log Analytics használatáról](get-started-portal.md).
- Végezzen el egy oktatóanyagot a [naplók írásához](get-started-portal.md).
