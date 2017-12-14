---
title: "Azure Automation kapcsolatos gyakori hibák elhárítása |} Microsoft Docs"
description: "Ez a cikk bővebb információt talál, és Azure Automation gyakori hibák elhárítása."
services: automation
documentationcenter: 
author: georgewallace
manager: stevenka
editor: tysonn
tags: top-support-issue
keywords: "Automation-hiba, hibaelhárítási, probléma"
ms.assetid: 5f3cfe61-70b0-4e9c-b892-d02daaeee07d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: sngun; v-reagie
ms.openlocfilehash: c958bc149cc617b5c9e99a2d3fc6fb2d425b2772
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="troubleshooting-common-issues-in-azure-automation"></a>Az Azure Automationben kapcsolatos gyakori hibák elhárítása 
Ez a cikk ismerteti a hibaelhárítást előforduló hibákat tapasztalhat az Azure Automationben és azok megoldását lehetséges megoldások javasolja.

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Az Azure Automation-runbook használatakor hitelesítési hibák
### <a name="scenario-sign-in-to-azure-account-failed"></a>Forgatókönyv: Nem sikerült Azure-fiókkal bejelentkezni
**Hiba:** hibaüzenet jelenik a "Unknown_user_type: Ismeretlen felhasználó típusa" az Add-AzureAccount vagy Login-AzureRmAccount parancsmagok használata.

**A hiba oka:** Ez a hiba akkor fordul elő, ha a hitelesítőadat-eszköz neve nem érvényes, vagy ha a felhasználónév és jelszó, amellyel az Automation szolgáltatásbeli hitelesítőadat-eszköz beállítása nem érvényes.

**Hibaelhárítási tippek:** annak megállapítására, hogy mi, tegye a következőket:  

1. Győződjön meg arról, hogy nincs-e a különleges karaktereket, beleértve a  **@**  az automatizálási hitelesítő adatok objektumnév Azure való kapcsolódáshoz használt karakter.  
2. Ellenőrizze, hogy használhatja-e a felhasználónévvel és jelszóval, tárolódnak az Azure Automation szolgáltatásbeli hitelesítőadat a helyi PowerShell ISE-szerkesztőben. Ez a PowerShell ISE a következő parancsmag futtatásával teheti meg:  

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred
3. Ha a hitelesítés sikertelen helyileg, ez azt jelenti, hogy nem állított be az Azure Active Directorybeli hitelesítő adatokat megfelelően. Tekintse meg [hitelesítéséhez az Azure-bA az Azure Active Directoryval](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) blogbejegyzés az Azure Active Directory-fiók helytelenül eléréséhez.  

### <a name="scenario-unable-to-find-the-azure-subscription"></a>Forgatókönyv: Nem található az Azure-előfizetés
**Hiba:** hibaüzenet "nevű előfizetést ``<subscription name>`` nem található" Ha a Select-AzureSubscription vagy Select-AzureRmSubscription parancsmagok használata.

**A hiba oka:** Ez a hiba akkor fordul elő, ha az előfizetés neve érvénytelen, vagy ha az Azure Active Directory felhasználó próbál szerezni az előfizetés részletei nem van konfigurálva, az előfizetés rendszergazdája.

**Hibaelhárítási tippek:** annak meghatározására, ha Azure megfelelően hitelesített, és válassza ki a kívánt előfizetés hozzáférése, tegye a következőket:  

1. Győződjön meg arról, hogy futtatja a **Add-AzureAccount** futtatása előtt a **válasszon-AzureSubscription** parancsmag.  
2. Ha továbbra is megjelenik ez a hibaüzenet, a kód módosítása hozzáadásával a **Get-AzureSubscription** parancsmag következő a **Add-AzureAccount** parancsmag és a kód hajthat végre.  Most ellenőrizheti, hogy tartalmazza-e a Get-AzureSubscription kimenete az előfizetés részletei.  

   * Ha nem lát minden előfizetés részletei, a kimenetben, ez azt jelenti, hogy az előfizetés még nincs inicializálva.  
   * Ha az előfizetés részletei kimenet látja, akkor győződjön meg arról, hogy a megfelelő előfizetés neve vagy azonosítója használ a **válasszon-AzureSubscription** parancsmag.   

### <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a>Forgatókönyv: Az Azure-bA hitelesítés sikertelen, mert a többtényezős hitelesítés engedélyezett-e
**Hiba:** , hibaüzenet jelenik meg a "Hozzáadás-AzureAccount: AADSTS50079: erős hitelesítés beléptetési (igazolása-up) szükség" hitelesítéséhez az Azure-bA az Azure felhasználónévvel és jelszóval.

**A hiba oka:** többtényezős hitelesítés az Azure-fiókjával rendelkezik, ha nem használhatja az Azure Active Directory-felhasználók hitelesítéséhez az Azure-bA.  Ehelyett szükség tanúsítvány vagy egy egyszerű szolgáltatásnév hitelesítéshez használni kívánt Azure-bA.

**Hibaelhárítási tippek:** tanúsítvány használatára az Azure Szolgáltatáskezelés-parancsmagokkal, tekintse meg [létrehozása és kezelése az Azure-szolgáltatások tanúsítvány hozzáadása.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Egy egyszerű szolgáltatást az Azure Resource Manager parancsmagjainak használatához tekintse meg [szolgáltatás egyszerű Azure-portál használatával létrehozása](../azure-resource-manager/resource-group-create-service-principal-portal.md) és [hitelesítése egy egyszerű szolgáltatást az Azure Resource Manager eszközzel.](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="common-errors-when-working-with-runbooks"></a>A forgatókönyvek használata során előforduló hibákat
### <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-it-failed-to-start-each-time"></a>Forgatókönyv: A runbook-feladat kezdési háromszor történt kísérlet, de nem minden alkalommal, amikor indult el
**Hiba:** a runbook hibával meghiúsul, "" feladat volt próbált háromszor, de nem sikerült."

**A hiba oka:** ezt a hibát okozhatja a következők lehetnek az okai:  

1. Memóriára vonatkozó korlátozást.  Igazolnia kell dokumentált védőfalat lefoglalt memória vonatkozó korlátozások [Automation szolgáltatásra vonatkozó korlátozások](../azure-subscription-service-limits.md#automation-limits) , a feladatok sikertelenek lehetnek, ha 400 MB-nál több memóriát használ. 

2. A modul nem kompatibilis.  Ez akkor fordulhat elő, ha a modul függőségek nem helyesek, és ha nem, a runbook általában visszaállítja egy "parancs nem található" vagy "Paraméter nem lehet kötni" üzenet. 

**Hibaelhárítási tippek:** bármely, a következő megoldásokat kijavítja a problémát:  

* Javasolt a memóriakorlátot belül működéséhez módszereket ossza fel a munkakörnyezet elérhetővé tétele több runbook, nem mértékű adatfeldolgozás a memória, nem a runbookok a szükségtelen kimeneti írási vagy fontolja meg a PowerShell munkafolyamat runbookot írt hány ellenőrzőpontokat.  

* Frissítenie kell az Azure modulok lépések [frissítése az Azure PowerShell-modulok az Azure Automationben](automation-update-azure-modules.md).  


### <a name="scenario-runbook-fails-because-of-deserialized-object"></a>Forgatókönyv: A Runbook nem deszerializált objektum miatt
**Hiba:** a runbook végrehajtása sikertelen, hiba: a "paraméter nem lehet kötni ``<ParameterName>``. Nem alakítható át a ``<ParameterType>`` Deserialized típusú érték ``<ParameterType>`` be ``<ParameterType>``".

**A hiba oka:** Ha a runbookban egy PowerShell-munkafolyamat, tárol összetett objektumok deszerializált formátumban érdekében megőrizni a runbook állapota, ha a munkafolyamat fel van függesztve.  

**Hibaelhárítási tippeket:**  
A következő három megoldások bármelyikét fogja ezt a problémát:

1. Ha a másik egy parancsmag összetett objektumok vannak ismertetett, tegye ezeket a parancsmagokat egy InlineScript.  
2. Az összetett objektumot ahelyett, hogy át a teljes objektum átadása nevét és értékét, amelyekre szüksége van.  
3. Használja a PowerShell-forgatókönyv egy PowerShell-munkafolyamati forgatókönyv helyett.  

### <a name="scenario-runbook-job-failed-because-the-allocated-quota-exceeded"></a>Forgatókönyv: Runbook-feladat meghiúsult, mert túllépte a számára lefoglalt kvótát
**Hiba:** "a havi teljes feladat-futásidő kvótáját elérte az előfizetés" hibával a runbook-feladat meghiúsul.

**A hiba oka:** Ez a hiba akkor fordul elő, ha a feladat végrehajtásának meghaladja fiókjához 500 perces szabad kvótát. Ez a kvóta feladat végrehajtási feladatokat, mint a tesztelése a feladatok, a feladat elindítása a portálról, egy feladat végrehajtása webhookok használatával, és futtatni vagy az Azure portál használatával, vagy egy feladat ütemezésének az adatközpontban található összes típusú vonatkozik. További információt az Automation lásd díjszabása [Automation árképzési](https://azure.microsoft.com/pricing/details/automation/).

**Hibaelhárítási tippek:** Ha 500 percnél hosszabb feldolgozási havonta használni kívánt szüksége lesz a módosítása az ingyenesről szint az alapszintű rétegben. Az alapszintű rétegben frissítsen a következő lépéseket:  

1. Jelentkezzen be az Azure-előfizetésébe  
2. Válassza ki a frissíteni kívánt Automation-előfizetést  
3. Kattintson a **beállítások** > **árképzési**.
4. Kattintson a **engedélyezése** frissítse a fiókját, hogy a lap alján a **alapvető** réteg.

### <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a>Forgatókönyv: A parancsmag nem ismerhető fel, amikor egy runbook futtatását
**Hiba:** hibával meghiúsul a runbook-feladat "``<cmdlet name>``: A kifejezés ``<cmdlet name>`` nem ismerhető fel egy parancsmag, a függvény, a parancsfájl vagy a futtatható program neve."

**A hiba oka:** ezt a hibát az okozza, ha a PowerShell-motor nem található a parancsmag a runbookban használ.  Lehetséges, hogy a modul, amely tartalmazza a parancsmag nem található a fiókot, egy neve ütközik, a runbook nevét, vagy a parancsmag is létezik egy másik modul és automatizálás a neve nem oldható fel.

**Hibaelhárítási tippek:** bármely, a következő megoldásokat kijavítja a problémát:  

* Ellenőrizze, hogy megfelelően van megadva a parancsmag neve.  
* Ellenőrizze, hogy a parancsmag az Automation-fiók létezik, és, hogy nincsenek-e ütközések. Győződjön meg arról, ha jelen-e a parancsmag, nyissa meg a runbook szerkesztési módban, és keresse meg a keresett a könyvtárban, vagy futtassa a parancsmagot a **Get-Command ``<CommandName>``** .  Miután ellenőrzése, hogy a parancsmag érhető el a fiókot, és hogy nincsenek nincs más parancsmagok vagy a runbookok neve ütközik, vegye fel a vászonra, és győződjön meg arról, hogy a runbookban egy érvényes paraméterkészlet használunk.  
* Ha egy ütköző és a parancsmag érhető el a két különböző modulok, a megoldást a teljes nevet, a parancsmag használatával. Használhat például **ModuleName\CmdletName**.  
* Ha a runbook a helyszíni egy hibrid feldolgozócsoport állnak végrehajtás alatt, majd győződjön meg arról, hogy a modul parancsmag telepítve van-e a számítógépen, amelyen a hibrid feldolgozó.

### <a name="scenario-a-long-running-runbook-consistently-fails-with-the-exception-the-job-cannot-continue-running-because-it-was-repeatedly-evicted-from-the-same-checkpoint"></a>Forgatókönyv: Egy hosszú ideig futó runbook rendszeresen nem a következő kivétellel: "a feladat nem tovább fut, mert azt a ismételten fürtből a azonos ellenőrzőpont".
**A hiba oka:** Ez szándékosan van folyamatok belül Azure Automation, amely automatikusan felfüggeszti a runbook, ha tovább, mint három órán keresztül hajtja végre a "Méltányosan" figyelési miatt. A következő hibaüzenet jelenik nem rendelkezik "a következő teendő" beállítások. Számos oka egy runbook is fel kell függeszteni. Főleg hibák miatt felfüggeszti a hiba akkor fordulhat elő. Például egy runbookot, hálózati hiba vagy a Runbook Worker futtatásához a runbookot a crash nem kezelt kivétel, az összes, akkor fel kell függeszteni, és indítsa el az utolsó ellenőrzőponttól folytatásakor a runbook.

**Hibaelhárítási tippek:** a dokumentált megoldás a probléma elkerülése érdekében, hogy használjon ellenőrzőpontokat a munkafolyamatban.  További tudnivalókért tekintse meg [tanulási PowerShell-munkafolyamatok](automation-powershell-workflow.md#checkpoints).  "Valós Share utasítást" és ellenőrzőpont alaposabb magyarázata blog cikkben található [ellenőrzőpontok használata a Runbookokban](https://azure.microsoft.com/en-us/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).

## <a name="common-errors-when-importing-modules"></a>A modulok importálása során előforduló hibákat
### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a>Forgatókönyv: A modul importálása sikertelen, vagy parancsmagok nem hajtható végre importálása után
**Hiba:** modul importálása sikertelen vagy sikeres importálása, de nincs parancsmagok ki kell olvasni.

**A hiba oka:** néhány általános oka, hogy egy modul előfordulhat, hogy nem sikerült importálni az Azure Automation vannak:  

* A struktúra nem egyezik meg az Automation kell, hogy a struktúra.  
* A modul az egy másik modul, amely nem lett alkalmazva van az Automation-fiók függ.  
* A modul a mappában függősége hiányzik.  
* A **New-AzureRmAutomationModule** parancsmaggal töltse fel a modult használja, és nem adott a teljes tárolási elérési útja, vagy nem töltődtek be a modul egy nyilvánosan elérhető URL-cím segítségével.  

**Hibaelhárítási tippeket:**  
A probléma elhárításához a következő megoldásokat bármelyikét:  

* Győződjön meg arról, hogy a modul a következő formátumot követi:  
  ModuleName.Zip  **->**  ModuleName vagy a verziószám  **->**  (ModuleName.psm1, ModuleName.psd1)
* Nyissa meg a .psd1 fájlt, és a modul vannak-e az összes függőséget.  Ha igen, töltse fel ezeket a modulokat az Automation-fiók.  
* Győződjön meg arról, hogy minden hivatkozott .dll a modul mappában találhatók.  

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>A kívánt állapot konfigurációs szolgáltatása (DSC) használatakor előforduló gyakori hibák
### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a>Forgatókönyv: Az csomópont "Nem található" hiba miatt sikertelen állapotban van.
**Hiba:** a csomópont rendelkezik egy jelentés ezzel **sikertelen** állapotát, és tartalmazza a hiba "beolvasása a művelet a kiszolgáló https://``<url>``//accounts/``<account-id>``/Nodes(AgentId=``<agent-id>``)/GetDscAction sikertelen volt, mert egy érvényes konfigurációs ``<guid>`` nem található."

**A hiba oka:** Ez a hiba rendszerint azért fordul elő, amikor a csomópont hozzá van rendelve a konfiguráció nevét (például ABC) helyett egy csomópont-konfiguráció nevét (például ABC. Webkiszolgáló).  

**Hibaelhárítási tippeket:**  

* Győződjön meg arról, hogy a csomópont nem a "konfiguráció neve" és "csomópont-konfiguráció neve" rendeli.  
* A csomópont-konfiguráció rendelhet hozzá egy csomópont, az Azure portál használatával, vagy egy PowerShell-parancsmaggal.

  * Ahhoz, hogy a csomópont-konfiguráció hozzárendelése az Azure portál használatával csomóponthoz, nyissa meg a **DSC-csomópontok** lapon, majd válasszon ki egy csomópontot, és kattintson a **csomópont-konfiguráció hozzárendelése** gombra.  
  * Ahhoz, hogy a csomópont-konfiguráció hozzárendelése egy PowerShell-parancsmaggal csomópontra, használjon **Set-AzureRmAutomationDscNode** parancsmag

### <a name="scenario--no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a>Forgatókönyv: Nincsenek csomópont-konfigurációt (MOF-fájlok) keletkezett, amikor egy konfigurációs fordítása
**Hiba:** a DSC-fordítási feladat felfüggeszti a következő hibaüzenettel: "fordítási sikeresen befejeződött, de nem a csomópont konfigurációs .mofs létrehozott".

**A hiba oka:** Ha a kifejezés következő a **csomópont** a DSC-konfiguráció kulcsszót $null értékelődik ki, majd készül egyetlen csomópont-konfigurációt.    

**Hibaelhárítási tippeket:**  
A probléma elhárításához a következő megoldásokat bármelyikét:  

* Győződjön meg arról, hogy a kifejezés a **csomópont** $null az nem értékeli a kulcsszót a konfiguráció definíciója.  
* Ha a konfiguráció fordítása során átadott ConfigurationData, győződjön meg arról, hogy a várt értékek, amelyek szükséges, hogy a konfigurációs átadott [ConfigurationData](automation-dsc-compile.md#configurationdata).

### <a name="scenario--the-dsc-node-report-becomes-stuck-in-progress-state"></a>Forgatókönyv: A DSC-csomópont jelentés változik akadt-e "folyamatban" állapota
**Hiba:** DSC ügynök kiírja a "Nem található a megadott tulajdonságok értékeit az adott példány."

**A hiba oka:** a WMF verzióra frissítette, és rendelkezik WMI sérült.  

**Hibaelhárítási tippek:** utasításait a [ismert problémák és korlátozások DSC](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) kijavítani a problémát.

### <a name="scenario--unable-to-use-a-credential-in-a-dsc-configuration"></a>Forgatókönyv: Nem sikerült a hitelesítő adatok használata a DSC-konfiguráció
**Hiba:** a DSC-fordítási feladat fel lett függesztve, a következő hibaüzenettel: "System.InvalidOperationException hiba történt a tulajdonság"-hitelesítőadat"típusú feldolgozásakor ``<some resource name>``: konvertálása és tárolja a titkosított jelszót egyszerű szöveges érték adható meg, csak akkor, ha PSDscAllowPlainTextPassword van beállítva, igaz".

**A hiba oka:** konfigurációban használt hitelesítő adatokat, de nem adott meg megfelelő **ConfigurationData** beállítása **PSDscAllowPlainTextPassword** minden csomópont-konfiguráció igaz.  

**Hibaelhárítási tippeket:**  

* Ügyeljen arra, hogy a megfelelő adjon át **ConfigurationData** beállítása **PSDscAllowPlainTextPassword** minden csomópont-konfiguráció a konfigurációs említett igaz. További információkért tekintse meg [Azure Automation DSC eszközök](automation-dsc-compile.md#assets).

## <a name="next-steps"></a>Következő lépések
Ha követte a fenti hibaelhárítási lépéseket, és nem találja a választ, az alábbi további támogatási lehetőségek tekintheti meg.

* Az Azure-szakértők segítséget kérhet. Küldje el a problémát a [MSDN Azure vagy a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/).
* A fájl az Azure támogatási incidens. Lépjen a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) kattintson **segítségre van szüksége** alatt **műszaki és a számlázási támogatás**.
* A parancsprogram-kérelem továbbfejlesztéséről [Script Center](https://azure.microsoft.com/documentation/scripts/) Ha egy Azure Automation-runbook megoldás vagy az integrációs modul keres.
* Visszajelzés vagy szolgáltatás-kérelmek POST az Azure Automation a [User Voice](https://feedback.azure.com/forums/34192--general-feedback).
