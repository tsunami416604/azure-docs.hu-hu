---
title: Ütemezett értesítések küldése | Microsoft Docs
description: Ez a témakör az ütemezett értesítések Azure Notification Hubs használatával történő használatát ismerteti.
services: notification-hubs
documentationcenter: .net
keywords: leküldéses értesítések, leküldéses értesítés, leküldéses értesítések ütemezése
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
ms.openlocfilehash: d91b7816ee05d4b5ef5266b4c2556abdc1175b87
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87084262"
---
# <a name="how-to-send-scheduled-notifications"></a>Útmutató: ütemezett értesítések küldése

Ha van olyan forgatókönyv, amelyben egy későbbi időpontban szeretne értesítést küldeni, de nem kell egyszerűen felébredni a háttér-kóddal az értesítés elküldéséhez. A standard szintű értesítési központok olyan funkciót támogatnak, amely lehetővé teszi, hogy a későbbiekben akár hét napig is ütemezze az értesítéseket.


## <a name="schedule-your-notifications"></a>Értesítések időzítése
Egy értesítés küldésekor egyszerűen használja a Notification Hubs SDK [ `ScheduledNotification` osztályát](/dotnet/api/microsoft.azure.notificationhubs.schedulednotification?view=azure-dotnet#microsoft_azure_notificationhubs_schedulednotification) az alábbi példában látható módon:

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Ütemezett értesítések megszakítása
Egy korábban ütemezett értesítést is megszakíthat a saját notificationId:

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Az elküldhető ütemezett értesítések száma nincs korlátozva.

## <a name="next-steps"></a>További lépések

Lásd az alábbi oktatóanyagokat:

 - [Leküldéses értesítések az összes regisztrált eszközre](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Leküldéses értesítések küldése adott eszközökre](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Honosított leküldéses értesítések küldése](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [Leküldéses értesítések küldése adott felhasználóknak](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Helyalapú leküldéses értesítések küldése](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
