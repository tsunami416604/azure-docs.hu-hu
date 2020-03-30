---
title: Egyszerű naplók élményaz Azure Monitor (előzetes verzió) | Microsoft dokumentumok
description: Az egyszerű naplók felület lehetővé teszi, hogy az Azure Monitorban alapvető lekérdezéseket hozzon létre a KQL-lel való közvetlen interakció nélkül.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 59bcb42edaf7d46498a3514b4f1c919c6e8cc0c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660257"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Egyszerű naplók élménye az Azure Monitorban (előzetes verzió)
Az Azure Monitor [gazdag élményt](get-started-portal.md) nyújt a KQL-nyelven létrehozott [naplólekérdezések](log-query-overview.md) létrehozásához. Előfordulhat, hogy nem igényel teljes erejét KQL mégis, és inkább egy egyszerűsített élményt az alapvető lekérdezési követelmények. Az Egyszerű naplók felület lehetővé teszi, hogy alapvető lekérdezéseket hozzon létre anélkül, hogy közvetlenül kommunikálna a KQL-lel. Az egyszerű naplókat a KQL tanulási eszközeként is használhatja, mivel kifinomultabb lekérdezésekre van szüksége.

> [!NOTE]
> Egyszerű naplók jelenleg csak a Cosmos DB és a Key Vaults tesztként van megvalósítva. Kérjük, ossza meg tapasztalatait a Microsofttal a [User Voice](https://feedback.azure.com/forums/913690-azure-monitor) segítségével, hogy segítsen nekünk eldönteni, hogy bővítjük-e és feladjuk-e ezt a funkciót.


## <a name="scope"></a>Hatókör
Az egyszerű naplók felület lekéri az adatokat az *AzureDiagnostics*, *AzureMetrics*és *AzureActivity* tábla a kiválasztott erőforrás. 

## <a name="using-simple-logs"></a>Egyszerű naplók használata
Keresse meg az Azure-előfizetésbármely Cosmos DB-jét vagy Key Vaultját a [Log Analytics-munkaterületen naplók gyűjtésére beállított diagnosztikai beállításokkal.](../platform/resource-logs-collect-storage.md) Kattintson a **Figyelés** menü **Naplók parancsára** az Egyszerű naplók felület megnyitásához.

![Menü](media/simple-logs/menu.png)

Jelöljön ki egy **mezőt** és **operátort,** és adjon meg **egy összehasonlítási értéket.** Kattintson ide, **+** és adja meg **az És/Vagy értéket** további feltételek hozzáadásához.

![Feltételek](media/simple-logs/criteria.png)

A lekérdezés eredményének megtekintéséhez kattintson a **Futtatás** gombra.

## <a name="view-and-edit-kql"></a>KQL megtekintése és szerkesztése
Válassza **a Lekérdezésszerkesztőt** az Egyszerű naplók lekérdezés által létrehozott KQL megnyitásához a teljes Log Analytics-élményben. 

![Lekérdezésszerkesztő](media/simple-logs/query-editor.png)

Közvetlenül szerkesztheti a KQL-t, és a Log Analytics egyéb funkcióit, például a szűrőket is használhatja az eredmények további finomításához.

![KQL szerkesztése](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>További lépések

- Töltse ki az [Azure Portalon a Log Analytics használatával](get-started-portal.md)kapcsolatos oktatóanyagot.
- A [naplólekérdezések írásával](get-started-portal.md)kapcsolatos oktatóanyag befejezése .
