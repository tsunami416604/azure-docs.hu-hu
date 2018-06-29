---
title: Végezzen hibaelhárítást a hibák bevezetési frissítéskezelés, a változások követése és a készlet
description: Ismerje meg, a frissítéskezelés, a változások követése és a készlet megoldások bevezetése hibák elhárítása
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 044cb56b8991a1eb2dd6a1d35be621f2ffab3250
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063842"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>Hibaelhárítás során bevezetési megoldások

Bevezetési megoldások, például frissítéskezelés vagy a változások követése és a készlet során felmerülő hibákat. Ez a cikk a különböző előforduló hibákat, és azok megoldását ismerteti.

## <a name="general-errors"></a>Általános hiba

### <a name="computer-grou-query-format-error"></a>Forgatókönyv: ComputerGroupQueryFormatError

#### <a name="issue"></a>Probléma

Ez a hibaüzenet azt jelenti, hogy a megoldás célozzák mentett keresés számítógép lekérdezés formázása helytelen. 

#### <a name="cause"></a>Ok

Előfordulhat, hogy a lekérdezés módosítva, vagy hogy módosították a rendszer.

#### <a name="resolution"></a>Megoldás:

Törölheti a lekérdezés a megoldás, és a reonboard a megoldást, amely a lekérdezést újból létrehozza. A lekérdezés található belül a munkaterületen a **mentett keresések**. A lekérdezés neve **MicrosoftDefaultComputerGroup**, és a lekérdezés a kategória a lekérdezéshez tartozó megoldás nevét. Ha több megoldások engedélyezve vannak, a **MicrosoftDefaultComputerGroup** jeleníti meg többször a **mentett keresések**.

### <a name="policy-violation"></a>Forgatókönyv: PolicyViolation

#### <a name="issue"></a>Probléma

Ez a hibaüzenet azt jelenti, hogy a központi telepítés egy vagy több házirend megsértése miatt nem sikerült.

#### <a name="cause"></a>Ok 

A házirend van, amely blokkolja a művelet végrehajtását.

#### <a name="resolution"></a>Megoldás:

Ahhoz, hogy sikeresen üzembe helyezéséhez, kell figyelembe venni, a megadott házirend módosítása. Nincsenek házirendek, amelyek számos különböző típusú, mint az adott szükséges módosításokat a házirendet, amely sérül függ. Például, ha egy házirend lett adva egy erőforráscsoport, amely engedéllyel bizonyos típusú erőforrások erőforrás csoporton belüli tartalmának módosítása, hogy, például végrehajtja az alábbi:

* Távolítsa el teljesen a házirendet.
* Próbálja érheti el a egy másik erőforráscsoportban található.
* Vizsgálja felül a házirendet, például:
  * Újra eljuttatása a szabályzat egy adott erőforrás (például egy adott Automation-fiók).
  * A készlet módosítása erőforrások házirend lett beállítva, hogy megtagadja.

Ellenőrizze az értesítések az Azure portál jobb felső sarkában található, vagy keresse meg, amely tartalmazza az automation-fiók, és válassza ki az erőforráscsoport **központi telepítések** alatt **beállítások** megtekintéséhez a sikertelen központi telepítés. További információt az Azure házirend látogasson el: [áttekintése Azure házirend](../../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fautomation%2ftoc.json).

## <a name="mma-extension-failures"></a>MMA bővítmény hibák

Ha egy megoldás telepítésére, a különböző kapcsolódó erőforrások vannak telepítve. Ezeket az erőforrásokat az egyik a Microsoft Monitoring Agent kiterjesztés vagy a Linux OMS-ügynököt. Ezek a virtuálisgép-bővítmény, amely az újabb összehangolása a bináris fájlok letöltése céljából a konfigurált Operations Management Suite (OMS) munkaterület kommunikál a virtuális gép vendég ügynök és egyéb fájlok, amelyek a megoldás bevezetése áll függenek egyszer végrehajtása kezdődik.
Általában először tudomást MMA vagy az OMS-ügynököt Linux telepítési hibák jelennek meg az értesítési központ egy értesítésből. Kattintson az értesítés a további tájékoztatást nyújt az adott problémát. Az erőforráscsoportok erőforrás, majd azt a központi telepítések elemet navigációs is részleteit a központi telepítési hiba történt.
A MMA és az OMS-ügynököt telepítése Linux számos okból futhat hibára, és ezek a hibák elhárítása érvénybe szereplő lépések eltérnek attól függően, hogy a problémát. Hajtsa végre az adott hibaelhárítási lépéseket.

A következő szakasz léphet, amikor bevezetési okozó hiba a telepítésben az MMA bővítmény különböző problémákat ismerteti.

### <a name="webclient-exception"></a>Forgatókönyv: Kivétel történt egy WebClient-kérelem közben

A MMA bővítményt a virtuális gép nem tud kommunikálni külső erőforrások és a központi telepítés sikertelen.

#### <a name="issue"></a>Probléma

A következő példák visszaadott hibaüzenetek:

```
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Ok

Ez a hiba lehetséges oka a következők:

* Nincs konfigurálva a virtuális gépen, amely csak bizonyos portokat engedélyez proxy.

* A tűzfal beállítása letiltotta a hozzáférést a szükséges portokon és címek.

#### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy rendelkezik a megfelelő portok címek nyissa meg a kommunikációhoz. Portok és címek listájáért lásd: [a hálózat tervezése](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="transient-environment-issue"></a>Forgatókönyv: Telepítési hibái miatt nem sikerült átmeneti környezet

A Microsoft Monitoring Agent-bővítmény telepítése üzemelő példányt, mert egy másik telepítés vagy blokkolja a telepítési művelet során nem sikerült

#### <a name="issue"></a>Probléma

Az alábbiakban visszaadott hibaüzenetek példái:

```
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Ok

Ez a hiba lehetséges oka a következők:

* Egy másik telepítés folyamatban van
* A rendszer az újraindításra a sablon-üzembehelyezés lett elindítva.

#### <a name="resolution"></a>Megoldás:

Ez a hiba nem egy átmeneti jellegű. Ismételje meg a központi telepítés, a bővítmény telepítéséhez.

### <a name="installation-timeout"></a>Forgatókönyv: Telepítési időtúllépés

A telepítés az MMA bővítmény időtúllépése miatt nem fejeződött be.

#### <a name="issue"></a>Probléma

A következő egy példa egy, a visszaadott hibaüzenet:

```
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Ok

Ez a hiba miatt a virtuális gép telepítése során a nagy terhelés alatt folyamatban van.

### <a name="resolution"></a>Megoldás:

Kísérlet történt az MMA bővítményének telepítése, amikor a virtuális gép egy alacsonyabb terhelés alatt van.

## <a name="next-steps"></a>További lépések

Ha nem talál a problémát, vagy nem tudja megoldani a problémát, látogasson el a következő csatornák további támogatásért:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, az Azure támogatási incidens fájl is. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az beszerzése**.
