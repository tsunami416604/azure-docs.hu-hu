---
title: Az Azure Automation-Runbookokkal kapcsolatos hibák elhárítása
description: Ismerje meg, az Azure Automation-runbookokkal kapcsolatos problémák elhárítása
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 07/13/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ea134cde8b174d020a0adf73256aec3a6c406eaa
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237597"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Runbookokkal kapcsolatos hibák elhárítása

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Az Azure Automation-runbookok használatakor hitelesítési hibák

### <a name="sign-in-failed"></a>. Forgatókönyv: Azure-fiókot nem sikerült bejelentkezni

#### <a name="issue"></a>Probléma

A következő hibaüzenetet kapja, amikor dolgozik a `Add-AzureAccount` vagy `Connect-AzureRmAccount` parancsmagok.
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha az eszköz hitelesítő adat neve nem érvényes, vagy ha a felhasználónevet és jelszót, amellyel az Automation-hitelesítőeszközt beállítása nem érvényesek.

#### <a name="resolution"></a>Megoldás:

Annak megállapításához, hogy mi okozza, tegye a következőket:  

1. Győződjön meg arról, hogy nincs-e különleges karaktereket, beleértve a **@** Automation nevű hitelesítő adat eszköz csatlakozhat az Azure-ban használt karakter.  
2. Ellenőrizze, hogy használhatja-e a felhasználónevet és jelszót, amelyet az Azure Automation hitelesítő adat a helyi PowerShell ISE-szerkesztőben van tárolva. Ez a PowerShell ISE-ben a következő parancsmag futtatásával teheti meg:  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management   
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Ha a hitelesítés helyben nem sikerül, ez azt jelenti, hogy Ön még nem állította be az Azure Active Directory hitelesítő adatai megfelelően. Tekintse meg [hitelesítése az Azure-ban az Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) blogbejegyzés megfelelően állítsa be az Azure Active Directory-fiók létrehozása.  

### <a name="unable-to-find-subscription"></a>Forgatókönyv: Nem található az Azure-előfizetés

#### <a name="issue"></a>Probléma

A következő hibaüzenetet kapja, amikor dolgozik a `Select-AzureSubscription` vagy `Select-AzureRmSubscription` parancsmagok.:

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Hiba

Ez a hiba akkor fordul elő, ha az előfizetés neve nem érvényes, vagy ha az Azure Active Directory felhasználó megpróbálja beolvasni az előfizetés részleteinek nincs beállítva az előfizetés-rendszergazdaként.

#### <a name="resolution"></a>Megoldás:

Ha megfelelően hitelesített Azure-ba, és rendelkezik hozzáféréssel az előfizetéshez, válasszon kívánt meghatározásához tegye a következőket:  

1. Győződjön meg arról, hogy futtassa a **Add-AzureAccount** futtatása előtt a **Select-AzureSubscription** parancsmagot.  
2. Ha továbbra is látja ezt a hibaüzenetet, módosítsa a kódot adja hozzá a **Get-AzureSubscription** parancsmag következő a **Add-AzureAccount** parancsmag majd futtassa a kódot. Most ellenőrizheti, ha a Get-AzureSubscription kimenete tartalmazza az előfizetés adatait.  

   * Ha nem látja a kimeneti bármely előfizetés adatait, akkor ez azt jelenti, hogy az előfizetés még nincs inicializálva.  
   * Ha az előfizetés részleteinek a kimenetben látható, erősítse meg, hogy használja a megfelelő előfizetés neve vagy azonosítója a **Select-AzureSubscription** parancsmagot.

### <a name="auth-failed-mfa"></a>Forgatókönyv: Hitelesítés nem sikerült, mert engedélyezve van a multi-factor authentication szolgáltatás Azure-bA

#### <a name="issue"></a>Probléma

Az Azure-bA az Azure felhasználónevével és jelszavával való hitelesítéskor a következő hibaüzenetet kapja:

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Ok

A multi-factor authentication szolgáltatás az Azure-fiókkal rendelkezik, ha egy Azure Active Directory-felhasználó nem használható az Azure-beli hitelesítésre. Ehelyett kell egy tanúsítványt, vagy egy egyszerű szolgáltatás használatával Azure-beli hitelesítésre.

#### <a name="resolution"></a>Megoldás:

A klasszikus Azure üzemi modell parancsmagokban tanúsítványt használ, tekintse meg [létrehozása és kezelése az Azure-szolgáltatások egy tanúsítvány hozzáadása.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Egy egyszerű szolgáltatást az Azure Resource Manager parancsmagjainak használatához tekintse meg [az egyszerű szolgáltatásnév létrehozása az Azure portal használatával](../../azure-resource-manager/resource-group-create-service-principal-portal.md) és [hitelesítése egy egyszerű szolgáltatást az Azure Resource Managerrel.](../../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="common-errors-when-working-with-runbooks"></a>A forgatókönyvek használata során előforduló gyakori hibák

### <a name="not-recognized-as-cmdlet"></a>Forgatókönyv: A runbook nem hiányzik a parancsmag miatt

#### <a name="issue"></a>Probléma

A runbook egy a következőhöz hasonló hibaüzenettel meghiúsul:

```
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>Ok

Ez a hiba oka lehet a következő okok miatt:

1. A modul, amely tartalmazza a parancsmag nem importálja az automation-fiók
2. A modul a parancsmag containg importálja, de nem naprakész

#### <a name="resolution"></a>Megoldás:

Ez a hiba a következő feladatok végrehajtásával oldható meg:

Ha a modul egy Azure-modul, olvassa el [frissítése az Azure PowerShell-modulok az Azure Automationben](../automation-update-azure-modules.md) megtudhatja, hogyan frissíthető a modulokat az automation-fiók.

Ha külön modul, győződjön meg arról a modul importálása az Automation-fiókban.

### <a name="job-attempted-3-times"></a>Forgatókönyv: A runbook-feladat indítása háromszor történt kísérlet, de nem indult el, hogy minden alkalommal, amikor

#### <a name="issue"></a>Probléma

A runbook a következő hibával meghiúsul:

```
The job was tried three times but it failed
```

#### <a name="cause"></a>Ok

Ez a hiba oka lehet a következő okok miatt:

1. Memória felső korlátja. A védőfal számára lefoglalt memória a dokumentált korlátozva van [Automation Szolgáltatáskorlátok](../../azure-subscription-service-limits.md#automation-limits) , egy feladat sikertelen lehet, ha több mint 400 MB memóriát használ.

2. A modul inkompatibilis. Ez akkor fordulhat elő, ha a modul függőségek nem megfelelőek, és ha nem, a runbook általában adja vissza egy "parancs nem található" vagy "Nem köthető a paraméter" üzenet.

#### <a name="resolution"></a>Megoldás:

A probléma elhárításához a következő megoldások valamelyikét:

* A memória felső korlátja belül működik javasolt módszerek a következők felosztása több runbook között, nem processzormagonkénti memória, nem a szükségtelen kimeneti írási a runbookok az adatok feldolgozása vagy fontolja meg, hány ellenőrzőpontot ír be a PowerShell-munkafolyamat runbookok.  

* A lépéseket követve az Azure-modulok frissítése [frissítése az Azure PowerShell-modulok az Azure Automationben](../automation-update-azure-modules.md).  

### <a name="fails-deserialized-object"></a>Forgatókönyv: A Runbook nem deszerializált objektum miatt

#### <a name="issue"></a>Probléma

A runbook a következő hibával meghiúsul:

```
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Ok

Ha a forgatókönyv egy PowerShell-munkafolyamat, tárolja összetett objektumok deszerializált formában ahhoz, hogy a runbook állapota továbbra is fennáll, ha a munkafolyamat fel van függesztve.

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához a következő három megoldások valamelyikét:

1. Ha egy parancsmag a másikra összetett objektumok is átirányításával, tegye ezeket a parancsmagokat egy InlineScript.
2. A nevét vagy a szükséges érték átadása helyett a teljes objektum átadását a komplex objektumot.
3. Használjon PowerShell-runbook helyett egy PowerShell-munkafolyamati forgatókönyv.

### <a name="quota-exceeded"></a>Forgatókönyv: Forgatókönyv-feladat sikertelen volt, mert túllépte a számára lefoglalt kvótát

#### <a name="issue"></a>Probléma

A runbook-feladat a következő hibával meghiúsul:

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a feladat-végrehajtási meghaladja az 500-as perces ingyenes kvótát a fiókjához. Ez a kvóta vonatkozik minden típusú feladatok végrehajtása például egy feladat tesztelése, egy feladat indítása a portálról, webhookok használatával, és a egy feladatot, amely az Azure portal használatával, vagy ütemezés szerint a feladat végrehajtása a helyi adatközpontban. Az Automation szolgáltatás díjszabása kapcsolatos további információkért lásd: [díjszabásról](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Megoldás:

Ha azt szeretné, 500 percnél több feldolgozási havonta használni, módosítania az előfizetés az ingyenes szint az alapszintű csomag. Az alapszintű csomag az alábbi lépések megtételével frissíthet:  

1. Jelentkezzen be az Azure-előfizetésébe  
2. Válassza ki a frissíteni kívánt Automation-előfizetést  
3. Kattintson a **beállítások** > **díjszabás**.
4. Kattintson a **engedélyezése** lap alján, a fiók frissítését a **alapszintű** szint.

### <a name="cmdlet-not-recognized"></a>Forgatókönyv: A parancsmag nem ismerhető fel, amikor egy runbook futtatását

#### <a name="issue"></a>Probléma

A runbook-feladat a következő hibával meghiúsul:

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Ok

Ezt a hibát az okozza, ha a PowerShell motor a parancsmagot használja a runbook nem található. Ez lehet, mert a modul, amely tartalmazza a parancsmag nem található a fiókból, egy neve ütközik, a runbook nevét, vagy a parancsmag is létezik egy másik modul és automatizálási nem tudja feloldani a nevet.

#### <a name="resolution"></a>Megoldás:

A probléma elhárításához a következő megoldások valamelyikét:  

* Ellenőrizze, hogy megadta a parancsmag neve helyesen.  
* Győződjön meg arról, hogy a parancsmag az Automation-fiók létezik, és, hogy nem lesznek ütközések. Annak ellenőrzéséhez, hogy a parancsmag hogy található-e, nyissa meg a runbook szerkesztési módban, és keresse meg a keresi a tárban, vagy futtassa a parancsmagot a `Get-Command <CommandName>`. Miután ellenőrizte, hogy a parancsmag érhető el a fiókot, és, ha nincs más parancsmagok vagy a runbookok neve ütközik, adja hozzá a vászonhoz, és győződjön meg arról, hogy a runbookban egy érvényes paramétert használja.  
* Ha a parancsmag érhető el két különböző modulok névütközés rendelkezik, akkor ezt megoldhatja a teljes nevet, a parancsmag használatával. Használhatja például **ModuleName\CmdletName**.  
* Ha a runbook a helyszínen futtatja egy hibrid feldolgozói csoportban, majd ellenőrizze, hogy a modul és parancsmag telepítve van a gépen, amelyen a hibrid feldolgozó.

### <a name="evicted-from-checkpoint"></a>Forgatókönyv: Egy hosszú ideig futó runbook folyamatosan meghiúsul, és a kivétel: "a feladat nem folytatható, mert azt többször is ki lett zárva, az azonos ellenőrzőponttól fut"

#### <a name="issue"></a>Probléma

Ez szándékosan van miatt a "Igazságos elosztás" figyelési folyamatokat az Azure Automation, amely automatikusan felfüggeszti a runbook, ha már három órán keresztül hajtja végre. A hibaüzenet nem tartalmaz "következő" beállítások.

#### <a name="cause"></a>Ok

Egy runbook felfüggeszthető, számos oka a. Felfüggeszti történik a fájlátviteli hibák miatt. Például egy nem kezelt kivételek a runbook, hálózati hiba vagy a runbookot futtató Runbook-feldolgozón összeomlás, az összes miatt felfüggesztjük, és indítsa el az utolsó ellenőrzőponttól folytatásakor a runbook.

#### <a name="resolution"></a>Megoldás:

A dokumentált megoldás a probléma elkerülése érdekében, hogy az ellenőrzőpontok a munkafolyamatban. További tudnivalókért tekintse meg [Learning PowerShell-munkafolyamatok](../automation-powershell-workflow.md#checkpoints). Egy alaposabb elvégzett művelet leírását és a "Igazságos elosztás" ellenőrzőpont blog cikkben található [ellenőrzőpontok használata a Runbookokban](https://azure.microsoft.com/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).

### <a name="long-running-runbook"></a>Forgatókönyv: Egy hosszú ideig futó runbook nem lehetett végrehajtani

#### <a name="issue"></a>Probléma

Ez szándékosan van az Azure próbakörnyezetbe lefordítja a folyamatokat az Azure Automation, amely automatikusan felfüggeszti a runbook, ha már három órán keresztül hajtja végre a "Igazságos elosztás" figyelési miatt.

#### <a name="cause"></a>Ok

Igazságos elosztás egy Azure tesztkörnyezetben által engedélyezett 3 óra túllépi a runbook futott

#### <a name="resolution"></a>Megoldás:

Az ajánlott megoldás az, hogy a runbook futtatása egy [hibrid Runbook-feldolgozó](../automation-hrw-run-runbooks.md). Hibrid feldolgozók nem korlátozza a [igazságos elosztás](../automation-runbook-execution.md#fair-share) 3 óra runbook korlát, amely az Azure próbakörnyezetbe lefordítja a rendszer.

## <a name="common-errors-when-importing-modules"></a>A modulok importálása során előforduló gyakori hibák

### <a name="module-fails-to-import"></a>Forgatókönyv: A modul importálása sikertelen, vagy parancsmagok nem hajtható végre, az importálás után

#### <a name="issue"></a>Probléma

Modul importálása sikertelen lesz, vagy sikeresen importálja, de nincs parancsmagok ki kell olvasni.

#### <a name="cause"></a>Ok

Néhány általános oka, hogy egy modul előfordulhat, hogy nem sikerült importálni az Azure Automationhöz a következők:

* A struktúra nem egyezik meg az Automation kell a struktúra.
* A modul szolgáltatás, amely még nem lett telepítve az Automation-fiók egy másik modul függ.
* A modul hiányzó függőségeit a mappában.
* A `New-AzureRmAutomationModule` parancsmagot használják, hogy a modul feltöltése és a teljes elérési útja nem végezték el, vagy a modul nem töltött egy nyilvánosan elérhető URL-cím használatával.

#### <a name="resolution"></a>Megoldás:

A probléma elhárításához a következő megoldások valamelyikét:

* Győződjön meg arról, hogy a modul követi a következő formátumban: ModuleName.Zip **->** ModuleName vagy a verziószám **->** (ModuleName.psm1, ModuleName.psd1)
* Nyissa meg a .psd1 fájlban, és tekintse meg, ha a modul rendelkezik-e függőségek. Ha igen, töltse fel ezeket a modulokat az Automation-fiókot.
* Győződjön meg arról, hogy minden hivatkozott .dll modul mappában találhatók.

## <a name="next-steps"></a>További lépések

Ha nem jelenik meg a problémát, vagy nem lehet megoldani a problémát, látogasson el a következő csatornák további támogatás:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, akkor is fájl egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.