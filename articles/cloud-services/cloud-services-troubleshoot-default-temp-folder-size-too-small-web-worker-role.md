---
title: Az alapértelmezett ideiglenes mappa mérete túl kicsi a szerepkörhöz | Microsoft Docs
description: A felhőalapú szolgáltatások szerepköre korlátozott mennyiségű hellyel rendelkezik a TEMP mappához. Ez a cikk néhány javaslatot tartalmaz arra vonatkozóan, hogyan kerülheti el a lemezterületet.
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
ms.openlocfilehash: bea9e163e9dde6c42de87048379d663222cb56e9
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734620"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Az alapértelmezett ideiglenes mappa mérete túl kicsi a Cloud Service webes és feldolgozói szerepkörén
A Cloud Service Worker vagy webes szerepkör alapértelmezett ideiglenes könyvtára legfeljebb 100 MB méretű lehet, ami egy bizonyos ponton megtelt. Ez a cikk azt ismerteti, hogyan kerülhető el a szabad hely az ideiglenes címtárban.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Miért fogy el a szabad terület?
A szabványos Windows környezeti változók TEMP és TMP az alkalmazásban futó kód számára elérhetők. Mind a TEMP, mind a TMP egy olyan könyvtárba mutat, amelynek maximális mérete 100 MB. Az ebben a könyvtárban tárolt összes adatmennyiséget a Cloud Service életciklusa során nem őrzi meg a rendszer. Ha a felhőalapú szolgáltatásban lévő szerepkör-példányok újra lesznek hasznosítva, a rendszer megtisztítja a könyvtárat.

## <a name="suggestion-to-fix-the-problem"></a>A probléma megoldására vonatkozó javaslat
A következő alternatívák egyikének implementálása:

* Konfiguráljon egy helyi tárolási erőforrást, és közvetlenül a TEMP vagy a TMP használata helyett. Ha egy helyi tárolási erőforrást szeretne elérni az alkalmazáson belül futó kódból, hívja meg a [RoleEnvironment. GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) metódust.
* Konfiguráljon egy helyi tárolási erőforrást, és mutasson a TEMP és TMP könyvtárakra, hogy a helyi tárolási erőforrás elérési útjára mutasson. Ezt a módosítást a [RoleEntryPoint. OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) metóduson belül kell végrehajtani.

A következő mintakód bemutatja, hogyan módosítható a TEMP és a TMP cél könyvtára a OnStart metóduson belül:

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
Olvassa el az [Azure web Role ASP.net ideiglenes mappa méretének növelését](https://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx)ismertető blogot.

További [hibaelhárítási cikkek](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-debugging-cloud-services-overview) a Cloud Services szolgáltatáshoz.

Ha meg szeretné tudni, hogyan lehet elhárítani a Cloud Service szerepkörrel kapcsolatos problémákat az Azure Pásti számítógép-diagnosztikai adataival, tekintse [meg a Kevin Williamson blog-sorozatát](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
