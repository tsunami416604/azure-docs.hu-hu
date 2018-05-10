---
title: Hogyan küldhetők értesítések ütemezett |} Microsoft Docs
description: Ez a témakör ismerteti az ütemezett értesítések az Azure Notification Hubs használatával.
services: notification-hubs
documentationcenter: .net
keywords: leküldéses értesítések, leküldéses értesítési leküldéses értesítések ütemezése
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 0f4055a11d22604c0936685a7a2be3d56b259a5b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-send-scheduled-notifications"></a>Útmutató: Az ütemezett értesítések küldése
## <a name="overview"></a>Áttekintés
Ha egy olyan forgatókönyvet, amelyben szeretné, hogy a jövőben bármikor elküldeni egy értesítést, de nem rendelkeznek egyszerűen felébressze a értesítést küldeni a háttér-kód. Standard szint a notification hubs lehetővé teszi, hogy a jövőben a hét napja fel az értesítések ütemezését támogatja.

Értesítést küld, ha egyszerűen használja a [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) a Notification Hubs SDK osztályt a következő példában látható módon:

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Emellett megszakíthatja a notificationId használatával korábban ütemezett értesítést:

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Nincsenek nem korlátozza a küldhet ütemezett értesítések száma.

