---
title: A hibaelhárítási problémák az Azure Automation szükséges konfiguráló (DSC)
description: Ez a cikk információt nyújt segítséget a kívánt állapot konfigurációs szolgáltatása (DSC)
services: automation
ms.service: automation
ms.component: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5d2eae67fcff74a7016f7f6125e31a9c8c2bda97
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064005"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Hibaelhárítás a célállapot-konfiguráció (DSC)

Ez a cikk tájékoztatást nyújt a kívánt állapot konfigurációs szolgáltatása (DSC) problémák elhárításához.

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>A kívánt állapot konfigurációs szolgáltatása (DSC) használatakor előforduló gyakori hibák

### <a name="failed-not-found"></a>Forgatókönyv: Az csomópont "Nem található" hiba miatt sikertelen állapotban van.

#### <a name="issue"></a>Probléma

A csomópont rendelkezik egy jelentés ezzel **sikertelen** állapotát, és tartalmazza a hiba:

```
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Ok

Ez a hiba rendszerint azért fordul elő, amikor a csomópont hozzá van rendelve a konfiguráció nevét (például ABC) helyett egy csomópont-konfiguráció nevét (például ABC. Webkiszolgáló).

#### <a name="resolution"></a>Megoldás:

* Győződjön meg arról, hogy a csomópont nem a "konfiguráció neve" és "csomópont-konfiguráció neve" rendeli.
* A csomópont-konfiguráció rendelhet hozzá egy csomópont, az Azure portál használatával, vagy egy PowerShell-parancsmaggal.

  * Ahhoz, hogy a csomópont-konfiguráció hozzárendelése az Azure portál használatával csomóponthoz, nyissa meg a **DSC-csomópontok** lapon, majd válasszon ki egy csomópontot, és kattintson a **csomópont-konfiguráció hozzárendelése** gombra.  
  * Ahhoz, hogy a csomópont-konfiguráció hozzárendelése egy PowerShell-parancsmaggal csomópontra, használjon **Set-AzureRmAutomationDscNode** parancsmag

### <a name="no-mof-files"></a>Forgatókönyv: Nincsenek csomópont-konfigurációt (MOF-fájlok) keletkezett, amikor egy konfigurációs fordítása

#### <a name="issue"></a>Probléma

A DSC-fordítási feladat felfüggeszti a következő hibaüzenettel:

```
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Ok

Ha a kifejezés következő a **csomópont** a DSC-konfiguráció kulcsszót értékelődik ki `$null`, majd készül egyetlen csomópont-konfigurációt.

#### <a name="resolution"></a>Megoldás:

A probléma elhárításához a következő megoldásokat bármelyikét:

* Győződjön meg arról, hogy a kifejezés a **csomópont** $null az nem értékeli a kulcsszót a konfiguráció definíciója.
* Ha a konfiguráció fordítása során átadott ConfigurationData, győződjön meg arról, hogy a várt értékek, amelyek szükséges, hogy a konfigurációs átadott [ConfigurationData](../automation-dsc-compile.md#configurationdata).

### <a name="dsc-in-progress"></a>Forgatókönyv: A DSC-csomópont jelentés változik akadt-e "folyamatban" állapota

#### <a name="issue"></a>Probléma

A DSC-ügynök kimenete:

```
No instance found with given property values
```

#### <a name="cause"></a>Ok

A WMF verzióra frissítette, és rendelkezik a WMI sérült.

#### <a name="resolution"></a>Megoldás:

Javítsa ki a probléma kövesse az utasításokat, hogy a [ismert problémák és korlátozások DSC](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) cikk.

### <a name="issue-using-credential"></a>Forgatókönyv: Nem sikerült a hitelesítő adatok használata a DSC-konfiguráció

#### <a name="issue"></a>Probléma

A DSC-fordítási feladat fel lett függesztve, a következő hibaüzenettel:

```
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Ok

Konfigurációban használt hitelesítő adatokat, de nem adott meg megfelelő **ConfigurationData** beállítása **PSDscAllowPlainTextPassword** minden csomópont-konfiguráció igaz.

#### <a name="resolution"></a>Megoldás:

* Ügyeljen arra, hogy a megfelelő adjon át **ConfigurationData** beállítása **PSDscAllowPlainTextPassword** minden csomópont-konfiguráció a konfigurációs említett igaz. További információkért lásd: [Azure Automation DSC eszközök](../automation-dsc-compile.md#assets).

## <a name="next-steps"></a>További lépések

Ha nem talál a problémát, vagy nem tudja megoldani a problémát, látogasson el a következő csatornák további támogatásért:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, az Azure támogatási incidens fájl is. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az beszerzése**.