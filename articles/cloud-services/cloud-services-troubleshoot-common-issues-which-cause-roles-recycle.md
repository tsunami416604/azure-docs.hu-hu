---
title: A Cloud Service-szerepkörök újrahasznosításának gyakori okai | Microsoft Docs
description: A hirtelen újrahasznosítható felhőalapú szolgáltatási szerepkör jelentős állásidőt eredményezhet. Íme néhány gyakori probléma, amely a szerepkörök újrahasznosítását okozza, ami segíthet az állásidő csökkentésében.
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
ms.openlocfilehash: 0484eb919a9de11b64dcc3334c5a9a942d875ca6
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92075127"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Gyakori hibák, melyek a szerepkörök újrahasznosítását okozzák
Ez a cikk a telepítési problémák gyakori okait ismerteti, és hibaelhárítási tippeket nyújt a problémák megoldásához. Annak jelzése, hogy az alkalmazással kapcsolatban probléma merült fel, ha a szerepkör-példány nem indul el, vagy az inicializálás, a foglalt és a leállítási állapotok között ciklust jelez.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Hiányzó futtatókörnyezeti függőségek
Ha az alkalmazás egyik szerepköre bármely olyan szerelvényre támaszkodik, amely nem része a .NET-keretrendszernek vagy az Azure Managed Library-nek, explicit módon fel kell vennie az adott szerelvényt az alkalmazáscsomag részébe. Ne feledje, hogy a Microsoft egyéb keretrendszerek alapértelmezés szerint nem érhetők el az Azure-ban. Ha a szerepkör egy ilyen keretrendszerre támaszkodik, ezeket a szerelvényeket hozzá kell adnia az alkalmazáscsomag számára.

Az alkalmazás létrehozása és becsomagolása előtt ellenőrizze a következőket:

* Ha a Visual studiót használja, győződjön meg róla, hogy a **Másolás helyi** tulajdonság értéke **true (igaz** ) értékre van állítva a projektben szereplő összes hivatkozott szerelvény esetében, amely nem része az Azure SDK-nak vagy a .NET-keretrendszernek.
* Győződjön meg arról, hogy a web.config fájl nem hivatkozik a fordítási elemben lévő nem használt szerelvényekre.
* Minden. cshtml-fájl **létrehozási művelete** a **Content (tartalom**) értékre van állítva. Ez biztosítja, hogy a fájlok megfelelően jelenjenek meg a csomagban, és lehetővé teszi más hivatkozott fájlok megjelenítését a csomagban.

## <a name="assembly-targets-wrong-platform"></a>A szerelvény helytelen platformot céloz meg
Az Azure egy 64 bites környezet. Ezért a 32 bites célra fordított .NET-szerelvények nem fognak működni az Azure-ban.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>A szerepkör nem kezelt kivételeket dob az inicializálás vagy a leállítás során
A [RoleEntryPoint] osztály metódusai által kiváltott kivételek, beleértve a [OnStart], a [OnStop]és a [futtatási] metódusokat, nem kezelt kivételek. Ha nem kezelt kivétel lép fel az egyik metódusban, a szerepkör újra fog indulni. Ha a szerepkör ismételt újrahasznosítást végez, előfordulhat, hogy a rendszer minden indításkor nem kezelt kivételt dobott.

## <a name="role-returns-from-run-method"></a>A szerepkör visszatér a Run metódusból
A [futtatási] módszer határozatlan idejű futtatást szolgál. Ha a kód felülbírálja a [futtatási] módszert, határozatlan ideig alvó állapotba kell lépnie. Ha a [Run] metódus visszatér, a szerepkör újrahasznosítja.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Helytelen DiagnosticsConnectionString-beállítás
Ha az alkalmazás Azure Diagnostics használ, a szolgáltatás konfigurációs fájljának meg kell adnia a `DiagnosticsConnectionString` konfigurációs beállítást. Ezzel a beállítással HTTPS-kapcsolat adható meg a Storage-fiókhoz az Azure-ban.

Győződjön meg arról, hogy a `DiagnosticsConnectionString` beállítás megfelelő, mielőtt az alkalmazáscsomag üzembe helyezését az Azure-ba telepítené, ellenőrizze a következőket:  

* A `DiagnosticsConnectionString` beállítás egy érvényes Storage-fiókra mutat az Azure-ban.  
  Alapértelmezés szerint ez a beállítás az emulált Storage-fiókra mutat, ezért az alkalmazáscsomag üzembe helyezése előtt explicit módon módosítania kell ezt a beállítást. Ha nem módosítja ezt a beállítást, akkor kivétel keletkezik, amikor a szerepkör-példány megkísérli elindítani a diagnosztikai figyelőt. Ez azt eredményezheti, hogy a szerepkör-példány határozatlan időre újrahasznosítható.
* A kapcsolatok karakterlánca a következő [formátumban](../storage/common/storage-configure-connection-string.md)van megadva. (A protokollt HTTPS-ként kell megadni.) Cserélje le a *MyAccountName* nevet a Storage-fiók nevére, és *MyAccountKey* a hozzáférési kulccsal:    

```console
DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey
```

  Ha a Microsoft Visual studióhoz készült Azure Tools használatával fejleszti az alkalmazást, a tulajdonságlapok használatával állíthatja be ezt az értéket.

## <a name="exported-certificate-does-not-include-private-key"></a>Az exportált tanúsítvány nem tartalmaz titkos kulcsot.
Ha webes szerepkört szeretne futtatni a TLS alatt, győződjön meg arról, hogy az exportált felügyeleti tanúsítvány tartalmazza a titkos kulcsot. Ha a *Windows Tanúsítványkezelőt* használja a tanúsítvány exportálásához, akkor ügyeljen arra, hogy a **titkos kulcs exportálása** lehetőségnél válassza az **Igen** lehetőséget. A tanúsítványt PFX formátumban kell exportálni, amely az egyetlen jelenleg támogatott formátum.

## <a name="next-steps"></a>Következő lépések
További [hibaelhárítási cikkek](../index.yml?product=cloud-services&tag=top-support-issue) a Cloud Services szolgáltatáshoz.

További szerepkör-újrahasznosítási forgatókönyvek megtekintése a [Kevin Williamson blog-sorozatában](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).

[RoleEntryPoint]: /previous-versions/azure/reference/ee758619(v=azure.100)
[OnStart]: /previous-versions/azure/reference/ee772851(v=azure.100)
[OnStop]: /previous-versions/azure/reference/ee772844(v=azure.100)
[Futtassa]: /previous-versions/azure/reference/ee772746(v=azure.100)