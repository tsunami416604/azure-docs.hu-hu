---
title: A Felhőszolgáltatás szerepköreinek újrahasznosításának gyakori okai | Microsoft dokumentumok
description: A felhőalapú szolgáltatás szerepkör, amely hirtelen újrahasznosítja okozhat jelentős állásidőt okozhat. Az alábbiakban felkell adnunk néhány gyakori problémát, amelyek a szerepkörök újrahasznosítását okozzák, ami segíthet az állásidő csökkentésében.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 533930d1-8035-4402-b16a-cf887b2c4f85
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 554508b1bf784e306cd12a4a601f908e06320933
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71154982"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Gyakori hibák, melyek a szerepkörök újrahasznosítását okozzák
Ez a cikk a telepítési problémák néhány gyakori okait ismerteti, és hibaelhárítási tippeket tartalmaz a problémák megoldásához. Azt jelzi, hogy probléma van egy alkalmazással, amikor a szerepkörpéldány nem indul el, vagy az inicializálási, foglalt sági és leállítási állapotok között ciklusok között ciklusok.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Hiányzó futtatókörnyezeti függőségek
Ha az alkalmazás egy szerepkör e nem része a .NET framework vagy az Azure felügyelt kódtár, akkor explicit módon fel kell tüntetnie, hogy a szerelvény az alkalmazáscsomagban. Ne feledje, hogy más Microsoft-keretrendszerek alapértelmezés szerint nem érhetők el az Azure-ban. Ha a szerepkör egy ilyen keretrendszerre támaszkodik, hozzá kell adnia ezeket az összeállításokat az alkalmazáscsomaghoz.

Az alkalmazás létrehozása és csomagolása előtt ellenőrizze a következőket:

* Visual studio használata esetén győződjön meg arról, hogy a **Helyi másolás** tulajdonság értéke **True** a projekt minden olyan hivatkozott szerelvényéhez, amely nem része az Azure SDK-nak vagy a .NET Framework-nek.
* Győződjön meg arról, hogy a web.config fájl nem hivatkozik a fordítási elemben nem használt szerelvényekre.
* Minden .cshtml fájl **buildelési művelete** **Tartalom**értékre van állítva. Ez biztosítja, hogy a fájlok megfelelően jelenjenek meg a csomagban, és lehetővé teszi, hogy más hivatkozott fájlok is megjelenjenek a csomagban.

## <a name="assembly-targets-wrong-platform"></a>Az összeszerelési célok rossz platformot céloznak meg
Az Azure egy 64 bites környezet. Ezért a 32 bites célhoz összeállított .NET-szerelvények nem működnek az Azure-ban.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>A szerepkör nem kezelt kivételeket eredményez az inicializálás vagy a leállítás során
A [RoleEntryPoint] osztály metódusai által okozott kivételek, beleértve az [OnStart], [OnStop]és [Run] metódusokat, nem kezelt kivételek. Ha egy nem kezelt kivétel fordul elő az egyik ilyen módszer, a szerepkör újrahasznosítja. Ha a szerepkör ismételtújrahasznosítást ad, előfordulhat, hogy minden indításkor egy kezeletlen kivételt okoz.

## <a name="role-returns-from-run-method"></a>Szerepkör-visszatérés a Futtatás metódusból
A [Futtatás] metódus határozatlan ideig fut. Ha a kód felülbírálja a [Futtatás] metódust, határozatlan ideig alvó állapotban kell aludnia. Ha a [Futtatás] metódus visszaadja, a szerepkör újrahasznosítja.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Helytelen diagnosztikai connectionstring beállítás
Ha az alkalmazás az Azure Diagnostics szolgáltatást `DiagnosticsConnectionString` használja, a szolgáltatás konfigurációs fájljának meg kell adnia a konfigurációs beállítást. Ennek a beállításnak meg kell adnia egy HTTPS-kapcsolatot az Azure-beli tárfiókhoz.

Annak érdekében, hogy a `DiagnosticsConnectionString` beállítás megfelelő legyen, mielőtt telepítene az alkalmazáscsomagot az Azure-ba, ellenőrizze az alábbiakat:  

* A `DiagnosticsConnectionString` beállítás egy érvényes tárfiókra mutat az Azure-ban.  
  Alapértelmezés szerint ez a beállítás az emulált tárfiókra mutat, ezért az alkalmazáscsomag telepítése előtt kifejezetten módosítania kell ezt a beállítást. Ha nem módosítja ezt a beállítást, kivétel történik, amikor a szerepkörpéldány megpróbálja elindítani a diagnosztikai figyelőt. Ez azt eredményezheti, hogy a szerepkörpéldány határozatlan ideig újrahasznosul.
* A kapcsolati karakterlánc a következő [formátumban](../storage/common/storage-configure-connection-string.md)van megadva. (A protokollt HTTPS-ként kell megadni.) Cserélje le a *MyAccountName-t* a tárfiók nevére, a *MyAccountKey-t* pedig a hozzáférési kulcsra:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Ha az azure-t a Microsoft Visual Studio-hoz való azure Tools használatával fejleszti, a tulajdonságlapok segítségével állíthatja be ezt az értéket.

## <a name="exported-certificate-does-not-include-private-key"></a>Az exportált tanúsítvány nem tartalmazza a személyes kulcsot
Ha egy webes szerepkört SSL alatt szeretne futtatni, gondoskodnia kell arról, hogy az exportált felügyeleti tanúsítvány tartalmazza a személyes kulcsot. Ha a *Windows tanúsítványkezelővel* exportálja a tanúsítványt, mindenképpen válassza az **Igen** lehetőséget **a személyes kulcs exportálása** beállításhoz. A tanúsítványt PFX formátumban kell exportálni, amely az egyetlen jelenleg támogatott formátum.

## <a name="next-steps"></a>További lépések
További [hibaelhárítási cikkek](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) a felhőszolgáltatásokhoz.

További szerepkör-újrahasznosítási forgatókönyvek [a Kevin Williamson blog sorozat](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[Kezdő]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop között]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Futtassa a következőt:]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
