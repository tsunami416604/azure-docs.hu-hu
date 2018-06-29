---
title: Azure Automation-forgatókönyveket a hibák elhárítása
description: Azure Automation-runbook problémáinak hibaelhárítása
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: bb340b8439927f191bc4a22f385d85d4e21b1cdb
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063946"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Runbookok hibák elhárítása

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Az Azure Automation-runbook használatakor hitelesítési hibák

### <a name="sign-in-failed"></a>. Forgatókönyv: Nem sikerült Azure-fiókkal bejelentkezni

#### <a name="issue"></a>Probléma

A következő hibaüzenetet kapja, amikor olyan a `Add-AzureAccount` vagy `Connect-AzureRmAccount` parancsmagok.
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a hitelesítőadat-eszköz neve nem érvényes, vagy ha a felhasználónév és a Automation szolgáltatásbeli hitelesítőadat-eszköz beállításához használt jelszót nem érvényes.

#### <a name="resolution"></a>Megoldás:

Ahhoz, hogy megállapítja, hogy nem megfelelő, a következő lépéseket:  

1. Győződjön meg arról, hogy nincs-e a különleges karaktereket, beleértve a **@** az automatizálási hitelesítő adatok objektumnév Azure való kapcsolódáshoz használt karakter.  
2. Ellenőrizze, hogy használhatja-e a felhasználónévvel és jelszóval, tárolódnak az Azure Automation szolgáltatásbeli hitelesítőadat a helyi PowerShell ISE-szerkesztőben. Ez a PowerShell ISE a következő parancsmag futtatásával teheti meg:  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management   
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Ha a hitelesítés sikertelen helyileg, ez azt jelenti, hogy nem állított be az Azure Active Directorybeli hitelesítő adatokat megfelelően. Tekintse meg [hitelesítéséhez az Azure-bA az Azure Active Directoryval](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) blogbejegyzés az Azure Active Directory-fiók helytelenül eléréséhez.  

### <a name="unable-to-find-subscription"></a>Forgatókönyv: Nem található az Azure-előfizetés

#### <a name="issue"></a>Probléma

A következő hibaüzenetet kapja, amikor olyan a `Select-AzureSubscription` vagy `Select-AzureRmSubscription` parancsmagok.:

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Hiba

Ez a hiba akkor fordul elő, ha az előfizetés neve érvénytelen, vagy ha az Azure Active Directory felhasználó próbál szerezni az előfizetés részletei nem van konfigurálva, az előfizetés rendszergazdája.

#### <a name="resolution"></a>Megoldás:

Annak meghatározására, ha Azure megfelelően hitelesített, és válassza ki a kívánt előfizetés hozzáférése, tegye a következőket:  

1. Győződjön meg arról, hogy futtatja a **Add-AzureAccount** futtatása előtt a **válasszon-AzureSubscription** parancsmag.  
2. Ha továbbra is megjelenik ez a hibaüzenet, a kód módosítása hozzáadásával a **Get-AzureSubscription** parancsmag következő a **Add-AzureAccount** parancsmag és a kód hajthat végre. Most ellenőrizheti, hogy tartalmazza-e a Get-AzureSubscription kimenete az előfizetés részletei.  

   * Ha nem lát minden előfizetés részletei, a kimenetben, ez azt jelenti, hogy az előfizetés még nincs inicializálva.  
   * Ha az előfizetés részletei kimenet látja, akkor győződjön meg arról, hogy a megfelelő előfizetés neve vagy azonosítója használ a **válasszon-AzureSubscription** parancsmag.

### <a name="auth-failed-mfa"></a>Forgatókönyv: Az Azure-bA hitelesítés sikertelen, mert a többtényezős hitelesítés engedélyezett-e

#### <a name="issue"></a>Probléma

Megjelenik a hitelesítéséhez az Azure-bA az Azure felhasználónévvel és jelszóval a következő hibával:

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Ok

Ha a többtényezős hitelesítés az Azure-fiókjával rendelkezik, az Azure Active Directory-felhasználók nem használható a hitelesítésre. Ehelyett szükség tanúsítvány vagy egy egyszerű szolgáltatásnév hitelesítéshez használni kívánt Azure-bA.

#### <a name="resolution"></a>Megoldás:

A tanúsítvány használatára az Azure klasszikus telepítési modell parancsmagokkal, tekintse meg [létrehozása és kezelése az Azure-szolgáltatások tanúsítvány hozzáadása.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Egy egyszerű szolgáltatást az Azure Resource Manager parancsmagjainak használatához tekintse meg [szolgáltatás egyszerű Azure-portál használatával létrehozása](../../azure-resource-manager/resource-group-create-service-principal-portal.md) és [hitelesítése egy egyszerű szolgáltatást az Azure Resource Manager eszközzel.](../../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="common-errors-when-working-with-runbooks"></a>A forgatókönyvek használata során előforduló hibákat

### <a name="job-attempted-3-times"></a>Forgatókönyv: A runbook-feladat kezdési háromszor történt kísérlet, de nem minden alkalommal, amikor indult el

#### <a name="issue"></a>Probléma

A runbook végrehajtása sikertelen, a következő hibaüzenettel:

```
The job was tried three times but it failed
```

#### <a name="cause"></a>Ok

Ezt a hibát okozhatja a következők lehetnek az okai:

1. Memóriára vonatkozó korlátozást. Vannak-dokumentált határértékek Védőfalhoz lefoglalt memória [Automation szolgáltatásra vonatkozó korlátozások](../../azure-subscription-service-limits.md#automation-limits) , egy feladat sikertelen lehet, ha 400 MB-nál több memóriát használ.

2. A modul nem kompatibilis. Ez akkor fordulhat elő, ha a modul függőségek nem helyesek, és ha nem, a runbook általában adja vissza egy "parancs nem található" vagy "Paraméter nem lehet kötni" üzenet.

#### <a name="resolution"></a>Megoldás:

A probléma elhárításához a következő megoldásokat bármelyikét:

* Javasolt a memóriakorlátot belül működéséhez módszereket ossza fel a munkakörnyezet elérhetővé tétele több runbook, nem mértékű adatfeldolgozás a memória, nem a runbookok a szükségtelen kimeneti írási vagy fontolja meg, hány ellenőrzőpontot ír be a PowerShell-munkafolyamat runbookok.  

* Az Azure modulok frissítése a következő lépések [frissítése az Azure PowerShell-modulok az Azure Automationben](../automation-update-azure-modules.md).  

### <a name="fails-deserialized-object"></a>Forgatókönyv: A Runbook nem deszerializált objektum miatt

#### <a name="issue"></a>Probléma

A runbook végrehajtása sikertelen, a következő hibaüzenettel:

```
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Ok

Ha a runbookban egy PowerShell-munkafolyamat, tárol összetett objektumok deszerializált formátumban érdekében megőrizni a runbook állapota, ha a munkafolyamat fel van függesztve.

#### <a name="resolution"></a>Megoldás:

Ezt a problémát a következő három megoldások bármelyikét:

1. Ha a másik egy parancsmag összetett objektumok vannak ismertetett, tegye ezeket a parancsmagokat egy InlineScript.
2. Az összetett objektumot ahelyett, hogy át a teljes objektum átadása nevét és értékét, amelyekre szüksége van.
3. Használja a PowerShell-forgatókönyv egy PowerShell-munkafolyamati forgatókönyv helyett.

### <a name="quota-exceeded"></a>Forgatókönyv: Runbook-feladat meghiúsult, mert túllépte a számára lefoglalt kvótát

#### <a name="issue"></a>Probléma

A runbook-feladat hibával meghiúsul:

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a feladat végrehajtásának meghaladja fiókjához 500 perces szabad kvótát. Ez a kvóta feladat végrehajtási feladatokat, mint a tesztelése a feladatok, a feladat elindítása a portálról, egy feladat végrehajtása webhookok használatával, és futtatni vagy az Azure portál használatával, vagy egy feladat ütemezésének az adatközpontban található összes típusú vonatkozik. Az automatizálási díjszabása kapcsolatos további információkért lásd: [Automation árképzési](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Megoldás:

Ha több, mint 500 percnyi feldolgozásra havonta használni kívánt, az előfizetés az ingyenesről módosítani szeretné az alapszintű rétegben szintet. Az alapszintű rétegben frissítsen a következő lépéseket:  

1. Jelentkezzen be az Azure-előfizetésébe  
2. Válassza ki a frissíteni kívánt Automation-előfizetést  
3. Kattintson a **beállítások** > **árképzési**.
4. Kattintson a **engedélyezése** frissítse a fiókját, hogy a lap alján a **alapvető** réteg.

### <a name="cmdlet-not-recognized"></a>Forgatókönyv: A parancsmag nem ismerhető fel, amikor egy runbook futtatását

#### <a name="issue"></a>Probléma

A runbook-feladat hibával meghiúsul:

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Ok

Ezt a hibát az okozza, ha a PowerShell-motor nem található a parancsmag a runbookban használ. Lehetséges, hogy a modul, amely tartalmazza a parancsmag nem található a fiókot, egy neve ütközik, a runbook nevét, vagy a parancsmag is létezik egy másik modul és automatizálás a neve nem oldható fel.

#### <a name="resolution"></a>Megoldás:

A probléma elhárításához a következő megoldásokat bármelyikét:  

* Ellenőrizze, hogy megfelelően van megadva a parancsmag neve.  
* Ellenőrizze, hogy a parancsmag az Automation-fiók létezik, és, hogy nincsenek-e ütközések. Győződjön meg arról, ha jelen-e a parancsmag, nyissa meg a runbook szerkesztési módban, és keresse meg a keresett a könyvtárban, vagy futtassa a parancsmagot a `Get-Command <CommandName>`. Miután ellenőrzése, hogy a parancsmag érhető el a fiókot, és hogy nincsenek nincs más parancsmagok vagy a runbookok neve ütközik, vegye fel a vászonra, és győződjön meg arról, hogy a runbookban egy érvényes paraméterkészlet használunk.  
* Ha egy ütköző és a parancsmag érhető el a két különböző modulok, a megoldást a teljes nevet, a parancsmag használatával. Használhat például **ModuleName\CmdletName**.  
* Ha a runbook a helyszíni egy hibrid feldolgozócsoport állnak végrehajtás alatt, majd győződjön meg arról, hogy a modul parancsmag telepítve van-e a számítógépen, amelyen a hibrid feldolgozó.

### <a name="evicted-from-checkpoint"></a>Forgatókönyv: Egy hosszú ideig futó runbook rendszeresen nem a következő kivétellel: "a feladat nem tovább fut, mert azt a ismételten fürtből a azonos ellenőrzőpont"

#### <a name="issue"></a>Probléma

Ez szándékosan van folyamatok belül Azure Automation, amely automatikusan felfüggeszti a runbook, ha tovább, mint három órán keresztül hajtja végre a "Méltányosan" figyelési miatt. A következő hibaüzenet jelenik nem rendelkezik "a következő teendő" beállítások.

#### <a name="cause"></a>Ok

Számos oka egy runbook is fel kell függeszteni. Főleg hibák miatt felfüggeszti a hiba akkor fordulhat elő. Például egy runbookot, hálózati hiba vagy a Runbook Worker futtatásához a runbookot a crash nem kezelt kivétel, összes OK felfüggeszteni, és indítsa el az utolsó ellenőrzőponttól folytatásakor a runbook.

#### <a name="resolution"></a>Megoldás:

A dokumentált megoldás a probléma elkerülése érdekében, hogy használjon ellenőrzőpontokat a munkafolyamatban. További tudnivalókért tekintse meg [tanulási PowerShell-munkafolyamatok](../automation-powershell-workflow.md#checkpoints). "Valós Share utasítást" és ellenőrzőpont alaposabb magyarázata blog cikkben található [ellenőrzőpontok használata a Runbookokban](https://azure.microsoft.com/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).

## <a name="common-errors-when-importing-modules"></a>A modulok importálása során előforduló hibákat

### <a name="module-fails-to-import"></a>Forgatókönyv: A modul importálása sikertelen, vagy parancsmagok nem hajtható végre importálása után

#### <a name="issue"></a>Probléma

A modul importálása sikertelen vagy sikeres importálása, de nincs parancsmagok ki kell olvasni.

#### <a name="cause"></a>Ok

Ennek oka, hogy egy modul előfordulhat, hogy nem sikerült importálni az Azure Automation a következők:

* A struktúra nem egyezik meg az Automation kell, hogy a struktúra.
* A modul az egy másik modul, amely nem lett alkalmazva van az Automation-fiók függ.
* A modul a mappában függősége hiányzik.
* A `New-AzureRmAutomationModule` parancsmaggal töltse fel a modult használja, és nem adott a teljes tárolási elérési útja, vagy nem töltődtek be a modul egy nyilvánosan elérhető URL-cím segítségével.

#### <a name="resolution"></a>Megoldás:

A probléma elhárításához a következő megoldásokat bármelyikét:

* Győződjön meg arról, hogy a modul a következő formátumot követi: ModuleName.Zip **->** ModuleName vagy a verziószám **->** (ModuleName.psm1, ModuleName.psd1)
* Nyissa meg a .psd1 fájlt, és a modul vannak-e az összes függőséget. Ha igen, töltse fel ezeket a modulokat az Automation-fiók.
* Győződjön meg arról, hogy minden hivatkozott .dll a modul mappában találhatók.

## <a name="next-steps"></a>További lépések

Ha nem talál a problémát, vagy nem tudja megoldani a problémát, látogasson el a következő csatornák további támogatásért:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, az Azure támogatási incidens fájl is. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az beszerzése**.