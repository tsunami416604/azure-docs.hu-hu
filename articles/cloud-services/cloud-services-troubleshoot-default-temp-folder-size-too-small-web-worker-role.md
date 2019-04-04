---
title: Alapértelmezett TEMP mappa mérete túl kicsi a szerepkörhöz |} A Microsoft Docs
description: A felhőszolgáltatási szerepkör egy korlátozott a TEMP mappa lemezterülettel rendelkezik. Ez a cikk néhány javaslatot hogyan kerülheti el a futó lemezterületét.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 7862e4d5c4dd603dacf5784df6c4194392ebc351
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918195"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Alapértelmezett TEMP mappa mérete túl kicsi egy felhőalapú szolgáltatás webes/feldolgozói szerepkör
Egy cloud service feldolgozó vagy webes szerepkör alapértelmezett ideiglenes könyvtár maximális mérete 100 MB, amely teljes körű valamikor válhat rendelkezik. Ez a cikk bemutatja, hogyan kevés a hely az ideiglenes könyvtár elkerülése érdekében.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Miért futtassa lemezterületét?
A szabványos Windows környezeti változók TEMP és TMP érhetők el a kódot, amely az alkalmazás fut-e. A TEMP és TMP egyetlen címtárban legfeljebb 100 MB méretű rendelkező mutasson. Ebben a könyvtárban tárolt adatokat a felhőszolgáltatás; fázisban nincs megőrizve a szerepkörpéldányok egy felhőalapú szolgáltatásban lehetőség, ha a könyvtár törlődik.

## <a name="suggestion-to-fix-the-problem"></a>A probléma megoldása érdekében
A következő alternatív megoldások egyikét valósítják:

* Konfigurálja a helyi tároló egyik erőforrásához, és közvetlenül a TEMP és TMP használata helyett férni. Helyi tároló erőforrások eléréséhez az alkalmazáson belül futó kódból, hívja meg a [RoleEnvironment.GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) metódust.
* Konfigurálja a helyi tároló egyik erőforrásához, és mutasson a TEMP és TMP könyvtárak átirányítása a helyi tárolási erőforrás elérési útja. Ez a módosítás belül kell végrehajtani a [RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) metódust.

Az alábbi példakód bemutatja, hogyan módosíthatja a cél-könyvtárak a TEMP és TMP a belül az OnStart metódus:

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
Olvassa el a blogot, amely leírja [az Azure webes szerepkör ASP.NET ideiglenes mappa méretének növelése](https://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Továbbiak megtekintése [hibaelhárítási cikkek](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/vs-azure-tools-debugging-cloud-services-overview.md) a cloud services.

Ismerje meg a felhőalapú szolgáltatás szerepkör kapcsolatos problémák elhárítása Azure PaaS számítógép-diagnosztikai adatok használatával, tekintse meg [Kevin Williamson blogsorozatot](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
