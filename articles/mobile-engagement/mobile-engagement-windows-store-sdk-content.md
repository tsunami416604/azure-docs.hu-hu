---
title: "Univerzális Windows-alkalmazások SDK tartalma"
description: "Az Azure Mobile Engagement univerzális alkalmazások Windows SDK tartalmát megismerése"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8fa1b701-1c2b-4aec-940c-06c974ef5405
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: b28d525ab16487b963772e23fdecd11f94dcabd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-apps-sdk-content"></a>Univerzális Windows-alkalmazások SDK tartalma
Ez a dokumentum, és ismerteti az SDK-t az alkalmazás által központilag telepített tartalom.

## <a name="the-resources-folder"></a>A `/Resources` mappa
Ez a mappa tartalmaz, amelyet a Mobile Engagement összes erőforrást. Testre is szabhatja azokat az alkalmazás megfelelően.

* `EngagementConfiguration.xml`: A Mobile Engagement konfigurációs fájlt, ahol testre szabhatja a Mobile Engagement-beállításokat (a Mobile Engagement kapcsolati karakterláncot, a jelentés összeomlási...) azt.

### <a name="html-folder"></a>/ HTML mappa
* `EngagementNotification.html`: A `Notification` html Nézetterv alkalmazásbeli transzparensek a webalkalmazás.
* `EngagementAnnouncement.html`: A `Announcement` webalkalmazás-Nézetterv html az alkalmazáson belüli közbeszúrt nézeteket.

### <a name="images-folder"></a>/Images mappa
* `EngagementIconNotification.png`: A márka ikonra a bal oldali, egy értesítés jelenik meg helyette a márka ikon jelzi.
* `EngagementIconOk.png`: A `Ok` ikon a reach tartalom az egyesített a műveletet, vagy az Érvényesítés gombra.
* `EngagementIconNOK.png`: A `NOK` ikon használható, ha az Érvényesítés gombra reach tartalom oldalak le van tiltva.
* `EngagementIconClose.png`: A `Close` ikon a reach-értesítések és a leállítási gomb tartalmát.

### <a name="overlay-folder"></a>/overlay mappa
* `EngagementPageOverlay.cs`: Az átmeneti területre lap hozzáadása az Engagement felelős elérni alárendelt alkalmazáson belüli felhasználói Felületet.

