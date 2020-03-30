---
title: Az alapértelmezett TEMP mappaméret túl kicsi egy szerepkörhöz | Microsoft dokumentumok
description: A felhőalapú szolgáltatási szerepkör korlátozott mennyiségű helyet foglal a TEMP mappában. Ez a cikk néhány javaslatot tartalmaz arra vonatkozóan, hogyan kerülhető el a helyfogyatka.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 0b869b73a79872d9263058bedfead018e18721c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71154996"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Az alapértelmezett TEMP mappaméret túl kicsi a felhőalapú szolgáltatás webes/feldolgozói szerepkörén
A felhőszolgáltatás-feldolgozó vagy a webes szerepkör alapértelmezett ideiglenes könyvtárának maximális mérete 100 MB, amely egy bizonyos ponton megtelhet. Ez a cikk azt ismerteti, hogyan kerülheti el, hogy elfogyjon a hely az ideiglenes könyvtár számára.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Miért fogy ki a hely?
A szabványos Windows környezeti változók TEMP és TMP érhetők el a kódot, amely fut az alkalmazásban. Mind a TEMP, mind a TMP egyetlen könyvtárra mutat, amelynek maximális mérete 100 MB. Az ebben a címtárban tárolt adatok nem maradnak meg a felhőszolgáltatás teljes életciklusa során; ha a felhőszolgáltatás szerepkörpéldányait újrahasznosítja, a könyvtár törlődik.

## <a name="suggestion-to-fix-the-problem"></a>Javaslat a probléma megoldására
Hajtsa végre az alábbi alternatívák egyikét:

* Konfiguráljon egy helyi tárolási erőforrást, és közvetlenül érje el a TEMP vagy a TMP használata helyett. Ha az alkalmazáson belül futó kódból szeretne hozzáférni egy helyi tárolási erőforráshoz, hívja meg a [RoleEnvironment.GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) metódust.
* Konfiguráljon egy helyi tárolási erőforrást, és mutasson a TEMP és TMP könyvtárakra úgy, hogy a helyi tárolási erőforrás elérési útja felé mutasson. Ezt a módosítást a [RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) metóduson belül kell végrehajtani.

A következő kódpélda bemutatja, hogyan módosíthatja a TEMP és a TMP célkönyvtárait az OnStart metóduson belül:

```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>További lépések
Olvassa el az [Azure webszerepkör ASP.NET ideiglenes mappa méretét.](https://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx)

További [hibaelhárítási cikkek](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/vs-azure-tools-debugging-cloud-services-overview.md) a felhőszolgáltatásokhoz.

Ha meg szeretné tudni, hogyan háríthatja el a felhőszolgáltatási szerepkörrel kapcsolatos problémákat az Azure PaaS-számítógép diagnosztikai adatainak használatával, tekintse meg [Kevin Williamson blogsorozatát.](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)
