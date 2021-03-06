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
ms.custom: devx-track-csharp
ms.openlocfilehash: 56eedda7f79fedce1e34ad837c92006e5cd8f191
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88998271"
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

## <a name="next-steps"></a>Következő lépések

Lásd az alábbi oktatóanyagokat:

 - [Leküldéses értesítések az összes regisztrált eszközre](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Leküldéses értesítések küldése adott eszközökre](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Honosított leküldéses értesítések küldése](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [Leküldéses értesítések küldése adott felhasználóknak](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Helyalapú leküldéses értesítések küldése](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
