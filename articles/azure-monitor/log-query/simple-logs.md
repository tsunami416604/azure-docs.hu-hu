---
title: Egyszerű naplók a Azure Monitorban (előzetes verzió) | Microsoft Docs
description: Az egyszerű naplók felülete lehetővé teszi, hogy alapszintű lekérdezéseket hozzon létre Azure Monitor anélkül, hogy közvetlenül interakciót KQL.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 59bcb42edaf7d46498a3514b4f1c919c6e8cc0c4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77660257"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Egyszerű naplók a Azure Monitorban (előzetes verzió)
A Azure Monitor a KQL nyelv használatával [gazdag felhasználói élményt](get-started-portal.md) nyújt a [naplók](log-query-overview.md) létrehozásához. Előfordulhat, hogy nincs szükség a KQL teljes teljesítményére, és az alapszintű lekérdezési követelményekhez inkább egyszerűsített felhasználói élményt szeretne. Az egyszerű naplók felülete lehetővé teszi, hogy alapszintű lekérdezéseket hozzon létre anélkül, hogy közvetlenül interakciót KQL. Az egyszerű naplókat KQL tanulási eszközként is használhatja, mivel kifinomultabb lekérdezésekre van szükség.

> [!NOTE]
> Az egyszerű naplók jelenleg csak Cosmos DB és kulcstartók tesztelésére lettek implementálva. Ossza meg velünk a kapcsolatot a Microsofttal a [felhasználói hangon](https://feedback.azure.com/forums/913690-azure-monitor) keresztül, hogy segítsen megállapítani, hogy kiterjesztjük és Kiszabadítjuk ezt a funkciót.


## <a name="scope"></a>Hatókör
Az egyszerű naplók a kiválasztott erőforráshoz tartozó *AzureDiagnostics*, *AzureMetrics*és *AzureActivity* táblából származó adatok lekérését végzik. 

## <a name="using-simple-logs"></a>Egyszerű naplók használata
Navigáljon az Azure-előfizetésében található bármely Cosmos DB vagy Key Vaulthoz a [naplók log Analytics munkaterületen való összegyűjtéséhez konfigurált diagnosztikai beállításokkal](../platform/resource-logs-collect-storage.md). Az egyszerű naplók felületének megnyitásához kattintson a **figyelés** menü **naplók** elemére.

![Menü](media/simple-logs/menu.png)

Válasszon ki egy **mezőt** és egy **operátort** , és adjon meg egy **értéket** az összehasonlításhoz. Kattintson a lehetőségre, és **+** adja meg **és/vagy** adjon hozzá további feltételeket.

![Feltételek](media/simple-logs/criteria.png)

A lekérdezés eredményeinek megtekintéséhez kattintson a **Futtatás** gombra.

## <a name="view-and-edit-kql"></a>KQL megtekintése és szerkesztése
Válassza a **lekérdezéstervező** lehetőséget az egyszerű naplók lekérdezés által generált KQL megnyitásához a teljes log Analytics felületén. 

![Lekérdezésszerkesztő](media/simple-logs/query-editor.png)

Közvetlenül szerkesztheti a KQL, és más funkciókat is használhat Log Analytics például szűrőket az eredmények pontosításához.

![KQL szerkesztése](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>További lépések

- Végezzen el egy oktatóanyagot a [Azure Portal log Analytics használatáról](get-started-portal.md).
- Végezzen el egy oktatóanyagot a [naplók írásához](get-started-portal.md).
