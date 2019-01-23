---
title: Ütemezett értesítések küldése |} A Microsoft Docs
description: Ez a témakör ismerteti az ütemezett értesítések az Azure Notification Hubs használatával.
services: notification-hubs
documentationcenter: .net
keywords: leküldéses értesítések, leküldéses értesítés, leküldéses értesítések ütemezése
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: af0de9e8c18644f4ae200f6546c0dd0a41320f9f
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54471837"
---
# <a name="how-to-send-scheduled-notifications"></a>kézikönyv: Ütemezett értesítések küldése

Ha rendelkezik egy olyan forgatókönyvet, amelyben szeretne értesítést küldeni a valamikor a jövőben, de nem rendelkezik egy egyszerű módja annak, hogy felébressze a háttér-kód, az értesítés kiküldéséhez. Standard szintű notification hubs használatával támogatja az olyan szolgáltatás, amely lehetővé teszi, hogy a hét nappal később fel az értesítések ütemezését.


## <a name="schedule-your-notifications"></a>Az értesítések ütemezése
Értesítést küld, ha egyszerűen használja a [ `ScheduledNotification` osztály](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) az a Notification Hubs SDK a következő példában látható módon:

```c#
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Ütemezett értesítések megszakítása
Ezenkívül lemondhatja a szolgáltatásokat egy korábban ütemezett értesítést a címzettjének értesítési azonosítója:

```c#
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Ütemezett értesítések küldése száma nincs korlátozva van.

## <a name="next-steps"></a>További lépések

Lásd az alábbi oktatóanyagokat:

 - [Leküldéses értesítések küldése az összes regisztrált eszközre](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Leküldéses értesítések küldése adott eszközökre](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Honosított leküldéses értesítések küldése](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [Leküldéses értesítések küldése adott felhasználóknak](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Helyalapú leküldéses értesítések küldése](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
