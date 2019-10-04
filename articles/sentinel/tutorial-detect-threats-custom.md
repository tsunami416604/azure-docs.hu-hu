---
title: Egyéni analitikus szabályok létrehozása a gyanús fenyegetések észleléséhez az Azure Sentinel használatával | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egyéni analitikai szabályokat a gyanús fenyegetések észleléséhez az Azure Sentinel használatával.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 611d3b3fdf2f76a9d5e79b8f3728dedae59074cd
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240451"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Oktatóanyag: Egyéni analitikus szabályok létrehozása a gyanús fenyegetések észleléséhez

Miután az [adatforrásokat](quickstart-onboard.md) az Azure sentinelhez csatlakoztatta, létrehozhat olyan egyéni szabályokat, amelyek meghatározott feltételeket kereshetnek a környezetében, és incidenseket hozhatnak létre, amikor a feltételek teljesülnek, így kivizsgálhatja azokat. Ez az oktatóanyag segít egyéni szabályok létrehozásában az Azure Sentinel használatával észlelt fenyegetések észleléséhez.

Ez az oktatóanyag segítséget nyújt az Azure Sentinel-fenyegetések észlelésében.
> [!div class="checklist"]
> * Analitikus szabályok létrehozása
> * Fenyegetési válaszok automatizálása

## <a name="create-custom-analytic-rules"></a>Egyéni analitikus szabályok létrehozása

Egyéni analitikai szabályokat hozhat létre, amelyek segítségével megkeresheti a környezetben gyanús fenyegetések és rendellenességek típusát. A szabály ellenőrzi, hogy azonnal értesítést kap-e, hogy a fenyegetések osztályozását, kivizsgálását és szervizelését el tudja végezni.

1. Az Azure Sentinel alatti Azure Portal válassza az **elemzés**lehetőséget.

1. A felső menüsorban válassza a **+ Létrehozás** lehetőséget, és válassza az **ütemezett lekérdezési szabályt**. Ekkor megnyílik az **Egyéni szabály létrehozása varázsló**.

    ![Ütemezett lekérdezés létrehozása](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Az **általános** lapon adjon meg egy leíró nevet és egy leírást. Szükség szerint állítsa be a **riasztás súlyosságát** . A szabály létrehozásakor engedélyezheti azt, ami azt eredményezi, hogy a létrehozása után azonnal le fog futni. Ha letiltottként hozza létre, a szabály hozzá lesz adva az **aktív szabályok** laphoz, és szükség esetén engedélyezheti azt.

    ![Egyéni analitikus szabály létrehozásának megkezdése](media/tutorial-detect-threats-custom/general-tab.png)

1. A **Beállítások** lapon közvetlenül is írhat egy lekérdezést, vagy létrehozhatja a lekérdezést log Analytics, majd beillesztheti azt a **keresési lekérdezés** mezőbe. A lekérdezés módosításakor és konfigurálásakor az Azure Sentinel a jobb oldalon az **eredmények előnézeti** ablakában szimulálja a lekérdezés eredményeit. Ez lehetővé teszi, hogy betekintést kapjon arról, hogy mekkora mennyiségű adat jöjjön létre a létrehozott riasztás adott időintervallumában. Az összeg attól függ, hogy mit állított le a **lekérdezés futtatásához** , és **az összes keresési adatait az elmúlt**. Ha úgy látja, hogy a riasztások általában túl gyakran indítják el a riasztásokat, beállíthatja, hogy az eredmények száma nagyobb legyen, hogy az átlagos alaptervnél magasabb legyen.

   ![Lekérdezés létrehozása az Azure Sentinelben](media/tutorial-detect-threats-custom/settings-tab.png)

   Íme egy példa a lekérdezésre, amely riasztást küld, ha rendellenes számú erőforrás jön létre az Azure-tevékenységben.

    `AzureActivity
    \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    \| where ActivityStatus == "Succeeded"
    \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

   > [!NOTE]
   > A lekérdezés hosszának 1 és 1, 0000 karakter közé kell esnie, és nem \*tartalmazhat "Search" \*vagy "Union" karaktert.

    1. A **lekérdezés ütemezése**területen állítsa be a következő paramétereket:

        1.  A lekérdezés **futtatásának**  **gyakorisága** beállításnál állítsa be, hogy a lekérdezés milyen gyakran fusson – akár 5 percenként, akár ritkábban, akár naponta egyszer.

        1.  **A legutóbbi keresési adatok** beállításával szabályozhatja a lekérdezés által futtatott adatok mennyiségét – például óránként, 60 percen belül is futhat.

        1. Az Azure Sentinel beállítható úgy, hogy **a riasztás létrehozása után leállítsa a lekérdezés futtatását** , ha csak egyszer szeretné lekérni a riasztást. Be kell állítania azt az ablakot, amely alatt a lekérdezésnek futnia kell, akár 24 óráig.

    1. Riasztási trigger feltételeinek meghatározása a **riasztási trigger**alatt. Az **entitás-hozzárendelés**területen a lekérdezés oszlopai az Azure Sentinel által felismert entitás mezőire képezhetők le. Az egyes mezők esetében rendelje hozzá a Log Analyticsban létrehozott lekérdezés megfelelő oszlopát a megfelelő entitás mezőhöz. Minden entitás több mezőt tartalmaz, például SID és GUID. Az entitást bármely mező szerint leképezheti, nem csak a felső szintű entitást.

1.  A **válaszok automatizálása** lapon válassza ki azokat a forgatókönyveket, amelyeket automatikusan szeretne futtatni, ha az egyéni szabály létrehoz egy riasztást. A forgatókönyvek létrehozásával és automatizálásával kapcsolatos további információkért lásd: [válaszadás a fenyegetésekre](tutorial-respond-threats-playbook.md).

    ![A fenyegetésekre adott válaszok automatizálása az Azure Sentinelben](media/tutorial-detect-threats-custom/response-automation-custom.png)

1. Válassza az **Áttekintés** lehetőséget az új riasztási szabály összes beállításának áttekintéséhez, majd válassza a létrehozás lehetőséget a **riasztási szabály inicializálásához**.

   ![Egyéni lekérdezés áttekintése](media/tutorial-detect-threats-custom/review-tab.png)

1.  A riasztás létrehozása után a rendszer egy egyéni szabályt ad hozzá a táblához az **aktív szabályok**területen. Ebből a listából engedélyezheti, letilthatja vagy törölheti az egyes szabályokat.

1.  A létrehozott riasztási szabályok eredményeinek megtekintéséhez nyissa meg az **incidensek** lapot, ahol osztályozást készíthet, [kivizsgálhatja az incidenseket](tutorial-investigate-cases.md), és elháríthatja a fenyegetéseket.


> [!NOTE]
> Az Azure Sentinelben létrehozott riasztások [Microsoft Graph biztonságon](https://aka.ms/securitygraphdocs)keresztül érhetők el. További információ: [Microsoft Graph Security riasztások dokumentációja](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="next-steps"></a>További lépések

Ebből az oktatóanyagból megtudhatta, hogyan kezdheti el a fenyegetések észlelését az Azure Sentinel használatával.

Ha szeretné megtudni, hogyan automatizálhatja a fenyegetésekre adott válaszokat, [állítson be automatizált fenyegetési válaszokat az Azure sentinelben](tutorial-respond-threats-playbook.md).

