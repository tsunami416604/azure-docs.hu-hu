---
title: Az Azure Automation Desired State Configuration (DSC) kapcsolatos hibák elhárítása
description: Ez a cikk nyújt információt hibaelhárításáról Desired State Configuration (DSC)
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 997f332e14fd1accf32d8cc3f51557fe005acab5
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54421645"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Hibaelhárítás a Desired State Configuration (DSC)

Ez a cikk nyújt információkat a Desired State Configuration (DSC) rétegen a kapcsolatos hibák elhárítása.

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Desired State Configuration (DSC) rétegen való használatakor előforduló gyakori hibák

### <a name="failed-not-found"></a>Forgatókönyv: Csomópont "Nem található" hiba miatt sikertelen állapotban van

#### <a name="issue"></a>Probléma

A csomópont rendelkezik egy jelentés **sikertelen** állapotát, és amely tartalmazza a hiba:

```
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Ok

Ez a hiba általában akkor fordul elő, amikor a csomópont hozzá van rendelve a konfiguráció nevét (például ABC) helyett egy csomópont-konfiguráció neve (például ABC. Webkiszolgáló).

#### <a name="resolution"></a>Megoldás:

* Győződjön meg arról, hogy a csomópont nem a "konfiguráció neve" és "csomópont-konfiguráció neve" rendeli hozzá.
* Csomópont-konfiguráció rendelhet egy csomóponthoz, az Azure portal használatával, vagy egy PowerShell-parancsmaggal.

  * Annak érdekében, hogy a csomópont-konfiguráció hozzárendelése egy csomóponthoz, az Azure portal használatával, nyissa meg a **DSC-csomópontok** lapon, majd válasszon ki egy csomópontot, és kattintson a **csomópont-konfiguráció hozzárendelése** gombra.  
  * Annak érdekében, hogy a csomópont-konfiguráció hozzárendelése egy PowerShell-parancsmaggal csomópont, használjon **Set-AzureRmAutomationDscNode** parancsmag

### <a name="no-mof-files"></a>Forgatókönyv: Nincsenek csomópont-konfigurációk (MOF-fájlok) keletkezett, amikor a konfiguráció fordítása

#### <a name="issue"></a>Probléma

A DSC-fordítási feladat felfüggeszti a következő hibával:

```
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Ok

Ha a kifejezés a következő a **csomópont** értékelődik ki kulcsszó a DSC-konfiguráció `$null`, majd nincsenek csomópont-konfigurációkat hoz létre.

#### <a name="resolution"></a>Megoldás:

A probléma elhárításához a következő megoldások valamelyikét:

* Győződjön meg arról, hogy a kifejezés a **csomópont** kulcsszó a konfiguráció-definícióban nem kiértékelése a $null.
* Ha a konfiguráció fordítása közben átadott ConfigurationData, győződjön meg arról, hogy a várt értékek, amelyek a konfigurációs van szükség, az átadott [ConfigurationData](../automation-dsc-compile.md#configurationdata).

### <a name="dsc-in-progress"></a>Forgatókönyv: A DSC-csomópont jelentés beragad "folyamatban" állapota

#### <a name="issue"></a>Probléma

A DSC-ügynök kimenete:

```
No instance found with given property values
```

#### <a name="cause"></a>Ok

A WMF-verzió frissítése, és rendelkezik a WMI sérült.

#### <a name="resolution"></a>Megoldás:

Javítsa ki a probléma kövesse az utasításokat a [ismert problémák és korlátozások DSC](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) cikk.

### <a name="issue-using-credential"></a>Forgatókönyv: Nem lehet hitelesítő adatot használja a DSC-konfiguráció

#### <a name="issue"></a>Probléma

A DSC-fordítási feladat fel lett függesztve a következő hibával:

```
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Ok

Konfigurációban használt hitelesítő adatokat, de nem adott meg megfelelő **ConfigurationData** beállítása **PSDscAllowPlainTextPassword** minden csomópont-konfiguráció esetében igaz.

#### <a name="resolution"></a>Megoldás:

* Győződjön meg arról, hogy adja át a megfelelő **ConfigurationData** beállítása **PSDscAllowPlainTextPassword** minden csomópont-konfiguráció már említettük, a konfigurációban az igaz értékre. További információkért lásd: [eszközök az Azure Automation DSC](../automation-dsc-compile.md#assets).

## <a name="next-steps"></a>További lépések

Ha nem jelenik meg a problémát, vagy nem lehet megoldani a problémát, látogasson el a következő csatornák további támogatás:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, akkor is fájl egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.
