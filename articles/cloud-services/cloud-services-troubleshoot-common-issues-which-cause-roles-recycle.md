---
title: Felhőszolgáltatásbeli szerepkörök újrahasznosításának gyakori okai |} A Microsoft Docs
description: A felhőszolgáltatási szerepkör, amely hirtelen újraindul jelentős állásidőt okozhatnak. Az alábbiakban néhány gyakori probléma, amely szerepkörök újrahasznosítását, amely esetleg segítséget csökkenthető a leállások miatt.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 533930d1-8035-4402-b16a-cf887b2c4f85
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 20b98fa9656c9c66a81ff98a70fcdbfb29d4dad6
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35923834"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Gyakori hibák, melyek a szerepkörök újrahasznosítását okozzák
Ez a cikk ismerteti az egyes alkalmazástelepítéssel kapcsolatos problémák leggyakoribb okait és hibaelhárítási tippekkel szolgál az ilyen problémák megoldásához. Azt jelzi, hogy probléma van egy alkalmazás akkor, ha a szerepkörpéldány nem indul el, vagy azt a inicializálása során, foglalt és leállítása állapotok közötti ciklusok.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Hiányzó futtatókörnyezeti függőségek
Ha egy szerepkörhöz az alkalmazás az esetleges támaszkodik szerelvényt, amely nem része a .NET-keretrendszer vagy az Azure által felügyelt erőforrástár, explicit módon szerepelnie kell ezt a szerelvényt az alkalmazáscsomagot. Ne feledje, hogy más Microsoft-keretrendszerek nem érhetők el az Azure-ban alapértelmezés szerint. Ha a szerepkör kereteinek támaszkodik, hozzá kell adnia az alkalmazáscsomag e szerelvények.

Mielőtt hozhat létre, és az alkalmazás becsomagolása, ellenőrizze a következőket:

* A Visual studio használata esetén ellenőrizze, hogy a **másolási helyi** tulajdonság értéke **igaz** esetében minden egyes hivatkozott szerelvényt a projekt, amely nem része az Azure SDK-t vagy a .NET-keretrendszer.
* Győződjön meg arról, hogy a web.config fájl bármely fel nem használt szerelvények a fordítási elem nem hivatkozik.
* A **Build Action** minden .cshtml fájl van állítva **tartalom**. Ez biztosítja, hogy a fájlok megfelelően fog megjelenni a csomagot, és lehetővé teszi, hogy más hivatkozott fájlok megjelennek a csomag.

## <a name="assembly-targets-wrong-platform"></a>Célok helytelen platforma sestavení
Az Azure egy 64 bites környezet áll. Ezért egy 32 bites cél lefordított .NET-szerelvények nem fognak működni az Azure-ban.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Szerepkör nem kezelt kivételt jelez inicializálás vagy a leállítás során
A módszerek által okozott kivételek a [RoleEntryPoint] osztály, amely tartalmazza a [ONSTART érvényesség], [OnStop], és [Futtatás] módszereket, nem kezelt kivételeket. Nem kezelt kivétel lép fel az alábbi módszerek egyikét, ha a szerepkör-újraindítás lesz. A szerepkör mindig többször újraindul, ha, előfordulhat, hogy lehet hívása nem kezelt kivétel minden alkalommal, amikor megpróbálnak elindítani.

## <a name="role-returns-from-run-method"></a>A szerepkör visszatér a Run metódus
A [Futtatás] metódus az célja, hogy határozatlan ideig szeretné futtatni. Ha a kód felülírja az [Futtatás] metódus, azt kell alvó határozatlan időre. Ha a [Futtatás] metódus adja vissza, a szerepkör újraindul.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Nem megfelelő DiagnosticsConnectionString beállítása
Ha az alkalmazás az Azure Diagnostics használja, a szolgáltatáskonfigurációs fájlban meg kell adnia a `DiagnosticsConnectionString` konfigurációs beállítás. Ez a beállítás meg kell adnia a HTTPS-kapcsolatot, a storage-fiókba az Azure-ban.

Annak érdekében, hogy a `DiagnosticsConnectionString` beállítás helyességéről, mielőtt alkalmazáscsomag üzembe helyezése az Azure-ba, ellenőrizze a következőket:  

* A `DiagnosticsConnectionString` pontok beállítást egy érvényes tárfiókot az Azure-ban.  
  Alapértelmezés szerint ez a beállítás mutat a emulált storage-fiókban, explicit módon módosítania kell ezt a beállítást az alkalmazáscsomag üzembe helyezése előtt. Ne változtassa ezt a beállítást, ha a program kivételt vált ki, ha a szerepkörpéldány próbálja meg elindítani a diagnosztikai figyelő. Emiatt előfordulhat, hogy a szerepkörpéldány határozatlan időre újraindításához.
* A kapcsolati karakterlánc van megadva a következő [formátum](../storage/common/storage-configure-connection-string.md). (A protokoll meg kell adni, HTTPS.) Cserélje le *MyAccountName* a tárfiók nevével és *MyAccountKey* a hívóbetűre:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Az alkalmazás a Microsoft Visual Studióhoz készült Azure-eszközök használatával fejleszt, a tulajdonságlapok használatával ezt az értéket.

## <a name="exported-certificate-does-not-include-private-key"></a>Exportált tanúsítvány nem tartalmaz titkos kulcsot
Az SSL a webes szerepkör futtatásához, gondoskodnia kell arról, hogy a exportált felügyeleti tanúsítványát tartalmazza a titkos kulcs. Ha a *Tanúsítványkezelő Windows* exportálja a tanúsítványt, ügyeljen arra, hogy válassza ki **Igen** a a **exportálja a titkos kulcsot** lehetőséget. A tanúsítványt a PFX-formátum, ami a jelenleg támogatott csak formátuma kell exportálni.

## <a name="next-steps"></a>További lépések
Továbbiak megtekintése [hibaelhárítási cikkek](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) a cloud services.

További szerepkör-újrahasznosítás: forgatókönyvek megtekintése [Kevin Williamson blogsorozatot](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[ONSTART érvényesség]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Futtatás]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
