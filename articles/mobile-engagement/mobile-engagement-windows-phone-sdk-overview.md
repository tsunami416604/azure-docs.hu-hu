---
title: Az Azure Mobile Engagement Windows Phone Silverlight SDK áttekintése |} Microsoft Docs
description: A Windows Phone Silverlight SDK és Azure Mobile Engagement – áttekintés
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 0e3d2420-0509-4952-8891-392e3dad9aaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.openlocfilehash: fe4df1f914e7b53f24a9855d5f96ecb193986b7f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Windows Phone Silverlight SDK Overview és Azure Mobile Engagement
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

Azure Mobile Engagement Windows Phone Silverlight-alkalmazást a integrációjával kapcsolatos lekérni a részletes adatait, kezdje itt. Ha meg szeretné adni az első egy try, hajtsa végre a [15 perc oktatóanyag](mobile-engagement-windows-phone-get-started.md).

Megtekintéséhez kattintson ide a [SDK tartalom](mobile-engagement-windows-phone-sdk-content.md)

## <a name="integration-procedures"></a>Integráció eljárások
1. Kezdje itt: [integrálása a Mobile Engagement a Windows Phone Silverlight-alkalmazásokban](mobile-engagement-windows-phone-integrate-engagement.md)
2. Az értesítések: [Reach (értesítések) integrálása a Windows Phone Silverlight-alkalmazásokban](mobile-engagement-windows-phone-integrate-engagement-reach.md)
3. Megvalósítási terv címkét: [a speciális API szerinti címkézését a Windows Phone Silverlight-alkalmazást a Mobile Engagement használata](mobile-engagement-windows-phone-use-engagement-api.md)

## <a name="release-notes"></a>Kibocsátási megjegyzések
###<a name="331-11032016"></a>3.3.1 (11/03/2016)
Része a *MicrosoftAzure.MobileEngagement* Nuget-csomag **v3.4.1**

* Jobb stabilitás.

Korábbi verziójához lásd: a [végezze el a kibocsátási megjegyzések](mobile-engagement-windows-phone-release-notes.md)

## <a name="upgrade-procedures"></a>Frissítési eljárások
Ha Ön már rendelkezik integrált egy régebbi verzióját az SDK az alkalmazásba, hogy az SDK-val történő frissítése során, vegye figyelembe a következő szempontokat.

Előfordulhat, hogy kell eljárnia számos eljárást, ha nem fogadta az SDK a különböző verzióiban. Tekintse meg a teljes [frissítése eljárások](mobile-engagement-windows-phone-upgrade-procedure.md). Például ha áttelepít 0.10.1 0.11.0 először hajtsa végre a "from a 0.10.1 0.9.0-s" eljárást kell majd a "from a 0.11.0 0.10.1" eljárást.

### <a name="from-200-to-330"></a>A 2.0.0 3.3.0 számára
#### <a name="test-logs"></a>Vizsgálati naplók
Az SDK által előállított konzolnaplófájlokban mostantól lehet engedélyezve vagy letiltva/szűrve. Ez testreszabásához frissítse a tulajdonságát `EngagementAgent.Instance.TestLogEnabled` egy érhetők el az érték a `EngagementTestLogLevel` számbavételi, például:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="upgrade-from-older-versions"></a>A régebbi verziók frissítése
Lásd: [eljárások frissítése](mobile-engagement-windows-phone-upgrade-procedure.md)

