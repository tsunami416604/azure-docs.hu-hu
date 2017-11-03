---
title: "Alapértelmezett ideiglenes mappa mérete túl kicsi a szerepkör |} Microsoft Docs"
description: "Egy felhőalapú szolgáltatás szerepkör egy korlátozott az ideiglenes mappa lemezterülettel rendelkezik. Ez a cikk megjelöl néhány hogyan kerülheti fut, nincs elég lemezterület."
services: cloud-services
documentationcenter: 
author: simonxjx
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 7/26/2017
ms.author: v-six
ms.openlocfilehash: 02a185fbe3603aa7f033ea3d50dc6ad36dd7f8f6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Alapértelmezett TEMP mappa mérete túl kicsi a felhőalapú szolgáltatás webes/munkavégző szerepkör
Az alapértelmezett ideiglenes könyvtár egy felhőalapú szolgáltatás munkavégző vagy webes szerepkör van 100 MB-os, amelyek egy bizonyos ponton teljes mérete. Ez a cikk ismerteti, hogyan kevés a hely az ideiglenes könyvtár elkerülése érdekében.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Miért futtatásához nincs elegendő lemezterület?
A szabványos Windows környezeti változók TEMP és TMP elérhetők, hogy az alkalmazás a kód. A TEMP és TMP, mely legfeljebb 100 MB-os egyetlen könyvtárra mutat. Ebben a könyvtárban tárolt adatokat nem őrzi meg a felhőalapú szolgáltatás; életciklus között a szerepkörpéldányok felhőszolgáltatásban lehetőség, ha a könyvtár karbantartása.

## <a name="suggestion-to-fix-the-problem"></a>A probléma megoldása érdekében javaslat
Valósítja meg az alábbi megoldások egyikét:

* A helyi tároló egyik erőforrásához konfigurálja, és közvetlenül a TEMP és TMP használata helyett-e érni. A helyi tároló egyik erőforrásához elérje az alkalmazáson belül futó kód, hívja meg a [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) metódust.
* A helyi tároló egyik erőforrásához konfigurálja, és mutasson a TEMP és TMP könyvtárak, hogy az erőforrás elérési útja a helyi tároló mutasson. Ez a módosítás belül kell végrehajtani a [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) metódust.

Az alábbi példakód bemutatja, hogyan módosíthatja a cél könyvtárak TEMP és TMP az OnStart metódus belül:

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

## <a name="next-steps"></a>Következő lépések
Ismerteti, bloghoz [Azure webes szerepkör ASP.NET ideiglenes mappa méretének növelése](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

További [cikkek hibaelhárítási](/?tag=top-support-issue&product=cloud-services) felhőszolgáltatásai számára.

Felhőalapú szolgáltatás szerepkör kapcsolatos problémák elhárítása az Azure PaaS diagnosztikai adatainak használatával további tudnivalókért tekintse meg [Kevin Williamson blog adatsorozat](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
