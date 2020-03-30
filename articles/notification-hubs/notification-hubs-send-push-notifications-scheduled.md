---
title: Ütemezett értesítések küldése | Microsoft dokumentumok
description: Ez a témakör az Ütemezett értesítések és az Azure Értesítési központok használatát ismerteti.
services: notification-hubs
documentationcenter: .net
keywords: leküldéses értesítések,leküldéses értesítések,leküldéses értesítések ütemezése
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: ef5eedaa903480ae670f9bc48d0af89744a99d22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212997"
---
# <a name="how-to-send-scheduled-notifications"></a>Útmutató: Ütemezett értesítések küldése

Ha van egy forgatókönyv, amelyben szeretne értesítést küldeni egy bizonyos ponton a jövőben, de nem egy egyszerű módja annak, hogy felébredjen a háttérkódot, hogy küldje el az értesítést. A standard szintű értesítési központok olyan funkciót támogatnak, amely lehetővé teszi az értesítések ütemezését legfeljebb hét nappal a jövőben.


## <a name="schedule-your-notifications"></a>Az értesítések ütemezése
Értesítés küldésekor egyszerűen használja az [ `ScheduledNotification` osztályt](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) az Értesítési központok SDK-ban, ahogy az a következő példában látható:

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Ütemezett értesítések visszavonása
A korábban ütemezett értesítéseket az értesítésazonosító használatával is visszavonhatja:

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Nincs korlátozva az elküldhető ütemezett értesítések száma.

## <a name="next-steps"></a>További lépések

Lásd az alábbi oktatóanyagokat:

 - [Leküldéses értesítések az összes regisztrált eszközre](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Leküldéses értesítések küldése adott eszközökre](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Honosított leküldéses értesítések küldése](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [Leküldéses értesítések küldése adott felhasználóknak](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Helyalapú leküldéses értesítések küldése](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
