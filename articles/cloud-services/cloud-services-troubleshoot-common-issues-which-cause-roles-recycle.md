---
title: "A felhőalapú szolgáltatás szerepkörök újrahasznosítása általános okok |} Microsoft Docs"
description: "A felhőalapú szolgáltatás egyik szerepköre, hirtelen újraindul jelentős állásidőt okozhat. Az alábbiakban néhány gyakori problémákat, melyek a szerepkörök újrahasznosítását, amelyek segítségével csökkentheti az állásidőt."
services: cloud-services
documentationcenter: 
author: simonxjx
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 533930d1-8035-4402-b16a-cf887b2c4f85
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: 7f513a7d3fe44cbbf06855059c87c10ddceef7c9
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Gyakori hibák, melyek a szerepkörök újrahasznosítását okozzák
Ez a cikk ismerteti az alkalmazástelepítéssel kapcsolatos problémák gyakori okai és hibaelhárítási tippek a problémák elhárításához. Arra utal, hogy probléma van az alkalmazás akkor, ha a szerepkör példánya nem indul el, vagy azt a inicializálása, foglalt és leállítása állapotok közötti ciklusok.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Hiányzó futásidejű függőségek
Ha az alkalmazás szerepet bármely szerelvény, amely nem része a .NET-keretrendszer vagy az Azure felügyelt kódtárára, explicit módon bele kell ezt a szerelvényt az alkalmazáscsomagot. Ne feledje, hogy más Microsoft-keretrendszer nem érhető el az Azure-on alapértelmezés szerint. Ha a szerepkör ilyen keretrendszer támaszkodik, hozzá kell adnia az alkalmazáscsomag e szerelvényeket.

Mielőtt felépítéséhez és az alkalmazás becsomagolása, ellenőrizze a következőket:

* Ha a Visual studio segítségével, győződjön meg arról, a **másolása helyi** tulajdonsága **igaz** minden hivatkozott szerelvény a projekt, amely nem része az Azure SDK-t vagy a .NET-keretrendszer.
* Győződjön meg arról, hogy a web.config fájl nem hivatkozik a fordítási elemben nem használt szerelvényeket.
* A **Build művelet** minden .cshtml fájl van állítva **tartalom**. Ez biztosítja, hogy a fájlok megfelelően fog megjelenni a csomagot, és lehetővé teszi, hogy más hivatkozott fájlok megjelennek a csomag.

## <a name="assembly-targets-wrong-platform"></a>Szerelvény célok nem megfelelő platformon
Azure egy 64 bites környezetben. Emiatt Azure .NET-szerelvényt a 32 bites cél számára nem fog működni.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Szerepkör nem kezelt kivételek inicializálása vagy leállítása közben jelez.
A módszerek által okozott kivételek a [RoleEntryPoint] osztály, amely tartalmazza a [OnStart], [OnStop], és [futtatása] módszereket, nem kezelt kivételek. Nem kezelt kivétel lép fel az alábbi módszerek egyikét, ha a szerepkör indul újra. Ha ismételten újrahasznosítása a szerepkör, előfordulhat, hogy kell egy nem kezelt kivétel kiváltása minden alkalommal, amikor megkísérli elindítani.

## <a name="role-returns-from-run-method"></a>Szerepkör vissza Run metódus
A [futtatása] metódus az célja, hogy határozatlan ideig futnak. Ha a kód felülírja a [futtatása] metódust, akkor kell alvó határozatlan ideig. Ha a [futtatása] metódus ad vissza, a szerepkör újraindul.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Helytelen DiagnosticsConnectionString beállítása
Ha az alkalmazás Azure Diagnostics használja, a szolgáltatás konfigurációs fájlban kell megadnia a `DiagnosticsConnectionString` konfigurációs beállítás. Ez a beállítás a tárfiók HTTPS-kapcsolat szabad megadni az Azure-ban.

Annak érdekében, hogy a `DiagnosticsConnectionString` beállítás helyességéről, csak az alkalmazáscsomag telepítheti az Azure-ba, ellenőrizze a következőket:  

* A `DiagnosticsConnectionString` pontok beállítása az Azure-ban egy érvényes tárfiókot.  
  Alapértelmezés szerint ez a beállítás mutat az emulált tárfiókot, explicit módon kell változtatni ezt a beállítást, mielőtt a alkalmazáscsomag telepítése. Ha nem módosítja ezt a beállítást, a rendszer kivételt hoz létre, amikor a szerepkör példánya megkísérli a diagnosztikai figyelő elindítása. Ez a szerepkör példánya határozatlan ideig újrahasznosítása okozhatja.
* A kapcsolati karakterlánc szerepel a következő [formátum](../storage/common/storage-configure-connection-string.md). (A protokollt meg kell adni, HTTPS.) Cserélje le *MyAccountName* a tárfiók nevével és *MyAccountKey* a hozzáférési kulccsal:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Ha az alkalmazás Azure-eszközök a Microsoft Visual Studio használatával fejleszt, a tulajdonságlapok segítségével értékre állítja.

## <a name="exported-certificate-does-not-include-private-key"></a>Exportált tanúsítvány nem tartalmazza a titkos kulcs
A webes szerepkör alatt SSL futtatásához győződjön meg arról, hogy saját exportált felügyeleti tanúsítványát tartalmazza a titkos kulcsot. Ha használja a *Windows tanúsítványkezelő* exportálja a tanúsítványt, ügyeljen arra, hogy válassza ki **Igen** a a **a titkos kulcs exportálását választom** lehetőséget. A tanúsítványt a PFX-formátum, ami a jelenleg támogatott csak formátumot kell exportálni.

## <a name="next-steps"></a>Következő lépések
További [cikkek hibaelhárítási](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) felhőszolgáltatásai számára.

Megtekintheti a további szerepkörök újrahasznosítása a következő forgatókönyvek [Kevin Williamson blog adatsorozat](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[futtatása]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
