---
title: "Az Azure Mobile Engagement Windows Universal SDK-integráció |} Microsoft Docs"
description: "Windows Universal-integráció SDK és Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 9ded187d-5c07-4377-a41c-ce205dd38b50
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.openlocfilehash: d616ad58156a19e89b3e106639a38df67cbd0abb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-sdk-integration-for-azure-mobile-engagement"></a>Az Azure Mobile Engagement Windows Universal SDK-integráció
Ez a dokumentum ismerteti az összes integrációs és konfigurációs lehetséges érhető el az Azure Mobile Engagement univerzális Windows SDK.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elindítása előtt először végezze el a [15 perces oktatóanyag](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="advanced-features"></a>Speciális funkciók
### <a name="reporting-features"></a>Jelentési szolgáltatások
Ezeket a szolgáltatásokat is hozzáadhat:

1. [Speciális jelentéskészítési beállítások](mobile-engagement-windows-store-advanced-reporting.md)
2. [Speciális konfigurációs beállítások](mobile-engagement-windows-store-advanced-configuration.md)

### <a name="notifications"></a>Értesítések
[Az univerzális Windows-alkalmazás Reach (értesítések) integrálása](mobile-engagement-windows-store-integrate-engagement-reach.md)

### <a name="tag-plan-implementation"></a>Címke terv végrehajtására:
[A speciális a Mobile Engagement az univerzális Windows-alkalmazás szerinti címkézését API használata](mobile-engagement-windows-store-use-engagement-api.md)

## <a name="release-notes"></a>Kibocsátási megjegyzések
### <a name="341-11032016"></a>3.4.1 (11/03/2016)

* Jobb stabilitás.

Korábbi verzióiban, tekintse meg a [végezze el a kibocsátási megjegyzések](mobile-engagement-windows-store-release-notes.md)

## <a name="upgrade-procedures"></a>Frissítési eljárások
Ha Ön már rendelkezik integrált Engagement régebbi verzióját az alkalmazásba, hogy az SDK-val történő frissítése során, vegye figyelembe a következő szempontokat.

Ha az SDK több verziói nem talált, előfordulhat, hogy számos eljárást kövesse. Tekintse meg a teljes [frissítése eljárások](mobile-engagement-windows-store-upgrade-procedure.md). Például ha áttelepít 0.10.1 0.11.0 először hajtsa végre a "from a 0.10.1 0.9.0-s" eljárást kell majd a "from a 0.11.0 0.10.1" eljárást.

### <a name="from-330-to-340"></a>A 3.3.0 3.4.0 számára
#### <a name="test-logs"></a>Vizsgálati naplók
Az SDK által előállított konzolnaplófájlokban mostantól lehet engedélyezve vagy letiltva/szűrve. Testre szabhatja, hogy frissítse a tulajdonságát `EngagementAgent.Instance.TestLogEnabled` érhetők el az értékek egyikének a `EngagementTestLogLevel` számbavételi, például:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

#### <a name="resources"></a>Erőforrások
A Reach átfedő javult. Az SDK NuGet-csomag erőforrások része.

Az SDK újabb verziójára frissít, miközben dönthet úgy, hogy szeretné-e az átmeneti területre mappából az erőforrások mindig a meglévő fájlokat, vagy nem:

* Ha az előző átfedés működik-e meg, vagy az integrációhoz a `WebView` elemek manuálisan, majd beállíthatja úgy, hogy hagyja a program kilép fájlokat, hogy továbbra is működni fognak.
* Frissítse az új átmeneti területre, cserélje ki a teljes `overlay` mappa az erőforrások közül az újjal az SDK-csomagból (UWP-alkalmazások: a frissítés után az % USERPROFILE % kérheti le az új átfedő mappa\\.nuget\packages\ MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [!WARNING]
> Az új átfedés használatával felülírja az előző verzió végzett testreszabások.
> 
> 

### <a name="upgrade-from-older-versions"></a>A régebbi verziók frissítése
Lásd: [eljárások frissítése](mobile-engagement-windows-store-upgrade-procedure.md)

