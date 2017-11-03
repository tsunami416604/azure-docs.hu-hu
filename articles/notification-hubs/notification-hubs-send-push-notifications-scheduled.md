---
title: "Hogyan küldhetők értesítések ütemezett |} Microsoft Docs"
description: "Ez a témakör ismerteti az ütemezett értesítések az Azure Notification Hubs használatával."
services: notification-hubs
documentationcenter: .net
keywords: "leküldéses értesítések, leküldéses értesítési leküldéses értesítések ütemezése"
author: ysxu
manager: erikre
editor: 
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: efac6e1ecc00359f1622d380333140bc055c83e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-send-scheduled-notifications"></a>Útmutató: Az ütemezett értesítések küldése
## <a name="overview"></a>Áttekintés
Ha egy olyan forgatókönyvet, amelyben szeretné, hogy a jövőben bármikor elküldeni egy értesítést, de nem rendelkeznek egyszerűen felébressze a értesítést küldeni a háttér-kód. Standard szint a Notification Hubs lehetővé teszi, hogy a jövőben 7 nap be az értesítések ütemezését támogatja.

Értesítést küld, ha egyszerűen használja a [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) a Notification Hubs SDK osztályt a következő példában látható módon:

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Emellett megszakíthatja a notificationId használatával korábban ütemezett értesítést:

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Nincsenek nem korlátozza a küldhet ütemezett értesítések száma.

