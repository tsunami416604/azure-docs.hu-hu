---
title: Univerzális Windows-alkalmazások SDK tartalma
description: Az Azure Mobile Engagement univerzális alkalmazások Windows SDK tartalmát megismerése
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 8fa1b701-1c2b-4aec-940c-06c974ef5405
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 7b520adcc6af24f7b092580ea82a787a120668bf
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-sdk-content"></a>Univerzális Windows-alkalmazások SDK tartalma
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

Ez a dokumentum, és ismerteti az SDK-t az alkalmazás által központilag telepített tartalom.

## <a name="the-resources-folder"></a>A `/Resources` mappa
Ez a mappa tartalmaz, amelyet a Mobile Engagement összes erőforrást. Testre is szabhatja azokat az alkalmazás megfelelően.

* `EngagementConfiguration.xml` : A Mobile Engagement konfigurációs fájlt, ahol testre szabhatja a Mobile Engagement-beállításokat (a Mobile Engagement kapcsolati karakterláncot, a jelentés összeomlási...) azt.

### <a name="html-folder"></a>/ HTML mappa
* `EngagementNotification.html` : A `Notification` html Nézetterv alkalmazásbeli transzparensek a webalkalmazás.
* `EngagementAnnouncement.html` : A `Announcement` webalkalmazás-Nézetterv html az alkalmazáson belüli közbeszúrt nézeteket.

### <a name="images-folder"></a>/Images mappa
* `EngagementIconNotification.png` : A márka ikonra a bal oldali, egy értesítés jelenik meg helyette a márka ikon jelzi.
* `EngagementIconOk.png` : A `Ok` ikon a reach tartalom az egyesített a műveletet, vagy az Érvényesítés gombra.
* `EngagementIconNOK.png` : A `NOK` ikon használható, ha az Érvényesítés gombra reach tartalom oldalak le van tiltva.
* `EngagementIconClose.png` : A `Close` ikon a reach-értesítések és a leállítási gomb tartalmát.

### <a name="overlay-folder"></a>/overlay mappa
* `EngagementPageOverlay.cs` : Az átmeneti területre lap hozzáadása az Engagement felelős elérni alárendelt alkalmazáson belüli felhasználói Felületet.

