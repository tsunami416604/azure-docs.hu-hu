---
title: Egyszerű naplók a Azure Monitorban (előzetes verzió) | Microsoft Docs
description: Az egyszerű naplók felülete lehetővé teszi, hogy alapszintű lekérdezéseket hozzon létre Azure Monitor anélkül, hogy közvetlenül interakciót KQL.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: bwren
ms.openlocfilehash: 323267dd47735ca54b84e47e6a55d1f2d14a0b06
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262171"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Egyszerű naplók a Azure Monitorban (előzetes verzió)
A Azure Monitor a KQL nyelv használatával [gazdag felhasználói élményt](get-started-portal.md) nyújt a [naplók](log-query-overview.md) létrehozásához. Előfordulhat, hogy nincs szükség a KQL teljes teljesítményére, és az alapszintű lekérdezési követelményekhez inkább egyszerűsített felhasználói élményt szeretne. Az egyszerű naplók felülete lehetővé teszi, hogy alapszintű lekérdezéseket hozzon létre anélkül, hogy közvetlenül interakciót KQL. Az egyszerű naplókat KQL tanulási eszközként is használhatja, mivel kifinomultabb lekérdezésekre van szükség.

> [!NOTE]
> Az egyszerű naplók jelenleg csak Cosmos DB és kulcstartók tesztelésére lettek implementálva. Ossza meg velünk a kapcsolatot a Microsofttal a [felhasználói hangon](https://feedback.azure.com/forums/913690-azure-monitor) keresztül, hogy segítsen megállapítani, hogy kiterjesztjük és Kiszabadítjuk ezt a funkciót.


## <a name="scope"></a>Scope
Az egyszerű naplók a kiválasztott erőforráshoz tartozó *AzureDiagnostics*, *AzureMetrics*és *AzureActivity* táblából származó adatok lekérését végzik. 

## <a name="using-simple-logs"></a>Egyszerű naplók használata
Navigáljon az Azure-előfizetésében található bármely Cosmos DB vagy Key Vaulthoz a [naplók log Analytics munkaterületen való összegyűjtéséhez konfigurált diagnosztikai beállításokkal](../platform/resource-logs-collect-storage.md). Az egyszerű naplók felületének megnyitásához kattintson a **figyelés** menü **naplók** elemére.

![Menü](media/simple-logs/menu.png)

Válasszon ki egy **mezőt** és egy **operátort** , és adjon meg egy **értéket** az összehasonlításhoz. Kattintson **+** a lehetőségre, és adja meg **és/vagy** adjon hozzá további feltételeket.

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
