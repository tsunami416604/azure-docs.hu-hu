---
title: "JSON-formátumú címkék használata az Azure VM állapotának ütemezése |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan címkék JSON karakterláncok használatával automatizálhatja a virtuális gép indítási és leállítási ütemezését."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 6afed5d2-e939-4749-8b2c-9312b4c16fb2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: magoedte;paulomarquesc
ms.openlocfilehash: cae4020741003be54b133efa121b3c09b859a176
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario-using-json-formatted-tags-to-create-a-schedule-for-azure-vm-startup-and-shutdown"></a>Azure Automation-forgatókönyv: JSON-formátumú címkék használatával az Azure virtuális gép indítási és leállítási ütemezés létrehozása
Az ügyfelek gyakran szeretné ütemezni a indításakor és leállásakor előfizetés költségek csökkentése vagy üzleti és műszaki követelményeken támogatja a virtuális gépek.

A következő forgatókönyv az automatikus indítási és leállítási a virtuális gépek beállítása nevű ütemezés egy erőforráscsoport szintjén vagy Azure virtuális gépek szintjén címke használatával teszi lehetővé. Az ütemezés konfigurálható vasárnaptól szombatig egy indítási és leállítási ideje.

Néhány, a-kész lehetőség van. Ezek a következők:

* [Virtuálisgép-méretezési csoportok](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) az automatikus skálázási beállításokat, amelyek lehetővé teszik, hogy a bejövő vagy kimenő méretezni.
* [DevTest Labs](../devtest-lab/devtest-lab-overview.md) szolgáltatást, amely az ütemezés indítási és leállítási műveletek a beépített alkalmas.

Azonban ezek a beállítások csak támogatja az adott forgatókönyveket és infrastruktúra,--szolgáltatás (IaaS) virtuális gépeket nem lehet alkalmazni.

Ütemezés címke erőforráscsoporthoz alkalmazása esetén is vonatkozik, amelyeket belül az összes virtuális gép. Ha ütemezés közvetlenül is vonatkozik egy virtuális Gépet, az utolsó ütemezési elsőbbséget a következő sorrendben:

1. Az erőforráscsoporthoz alkalmazott ütemezése
2. Egy erőforráscsoport és a virtuális gép erőforráscsoportban alkalmazott ütemezése
3. A virtuális gépek alkalmazott ütemezése

Ebben a forgatókönyvben lényegében lép a megadott formátumban JSON karakterláncnak, és hozzáadja azt a értékeként egy címke nevű ütemezés. A runbook majd felsorolja az összes erőforráscsoport-sablonok és virtuális gépeket, és az ütemezések azonosítja az egyes virtuális gépek a korábban felsorolt forgatókönyvek alapján. Ezután végighalad a virtuális gépek, amelyeken a csatolt, és értékeli ki, mit kell tenni. Például meghatározza, hogy igénylő virtuális gépek leállítása, állítsa le vagy figyelmen kívül hagyja.

Ezek a runbookok hitelesítéshez használja a [Azure-beli futtató fiók](automation-sec-configure-azure-runas-account.md).

## <a name="download-the-runbooks-for-the-scenario"></a>Töltse le a runbookok a forgatókönyvhöz
Ez a forgatókönyv áll négy PowerShell-munkafolyamati forgatókönyvek tölthet le a [TechNet Gallery](https://gallery.technet.microsoft.com/Azure-Automation-Runbooks-84f0efc7) vagy a [GitHub](https://github.com/paulomarquesdacosta/azure-automation-scheduled-shutdown-and-startup) tárház ebben a projektben.

| Forgatókönyv | Leírás |
| --- | --- |
| Teszt-ResourceSchedule |Minden virtuális gép ütemezés ellenőrzi, és leállítása vagy indítása attól függően, hogy az ütemezés hajt végre. |
| Adja hozzá ResourceSchedule |Az ütemezés címke hozzáadása a virtuális gép vagy az erőforrás-csoportot. |
| Frissítés-ResourceSchedule |Módosítja a meglévő ütemezés címke cserélje le a egy újat. |
| Remove-ResourceSchedule |Az ütemezés címke eltávolítása a virtuális gép vagy az erőforrás-csoportot. |

## <a name="install-and-configure-this-scenario"></a>A forgatókönyv telepítése és konfigurálása
### <a name="install-and-publish-the-runbooks"></a>A forgatókönyv telepítése és közzététele
A runbookok a letöltés után importálhatja azokat az eljárás használatával [létrehozása vagy importálása az Azure Automationben runbook](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation).  Minden runbook közzététele, miután sikeresen importálta az Automation-fiók be.

### <a name="add-a-schedule-to-the-test-resourceschedule-runbook"></a>A Test-ResourceSchedule runbook ütemezés hozzáadása
Kövesse az alábbi lépéseket ahhoz, hogy a teszt-ResourceSchedule runbookhoz ütemezést. Ez azért, amely ellenőrzi a virtuális gépek kell indítása, leállítása, vagy marad, a runbookot.

1. Azure-portálról, nyissa meg az Automation-fiók, és kattintson a **Runbookok** csempére.
2. Az a **teszt-ResourceSchedule** panelen kattintson a **ütemezések** csempére.
3. Az a **ütemezések** panelen kattintson a **ütemezés hozzáadása**.
4. Az a **ütemezések** panelen válassza **ütemezés kapcsolása a forgatókönyvhöz**. Válassza ki **hozzon létre egy új ütemezést**.
5. Az a **új ütemezés** panelen típus nevében ezt az ütemezést, például: *HourlyExecution*.
6. Az ütemezés **Start**, állítsa be a kezdési időpont egy óra növelésére.
7. Válassza ki **ismétlődési**, és ezután a **megismétlődik minden időköz**, jelölje be **1 óra**.
8. Ellenőrizze, hogy **beállíthatja a lejárati idejét** értéke **nem**, és kattintson a **létrehozása** menteni az új ütemezést.
9. Az a **ütemterv forgatókönyv** beállítások panelen válassza **paraméterek és futtatási beállítások**. A Test-ResourceSchedule **paraméterek** panelen adja meg az előfizetését a **SubscriptionName** mező.  Ez az az egyetlen paraméter, amely szükséges a runbookhoz.  Amikor végzett, kattintson a **OK**.

A runbook-ütemezés a következőképpen kell kinéznie kész:

![Runbook. teszt-ResourceSchedule konfigurált.](./media/automation-scenario-start-stop-vm-wjson-tags/automation-schedule-config.png)<br>

## <a name="format-the-json-string"></a>A JSON-karakterláncban formázása
Ez a megoldás alapvetően vesz egy JSON megadott formátumú karakterláncot, és hozzáadja azt a címke értéke nevű ütemezés. Ezután a runbook összes erőforráscsoport-sablonok és virtuális gépek sorolja fel, és azonosítja az egyes virtuális gépek ütemezések.

A runbook fut a hurokban, a csatolt rendelkező virtuális gépeket, és ellenőrzi, milyen műveleteket kell végezni. A következő egy példa a megoldások hogyan kell formázni:

```json
{
    "TzId": "Eastern Standard Time",
    "0": {
        "S": "11",
        "E": "17"
    },
    "1": {
        "S": "9",
        "E": "19"
    },
    "2": {
        "S": "9",
        "E": "19"
    },
}
```

Ez a struktúra kapcsolatos részletes információkat a következő:

1. Ez a JSON-struktúra formátuma arra optimalizálták, hogy áthidalni a 256 karakteres korlátozást az Azure-ban egyetlen címke értéke.
2. *TzId* jelenti. a virtuális gép időzónáját. Ezt az Azonosítót kérhetők le a TimeZoneInfo .NET-osztály használatával egy PowerShell-munkamenet--**[System.TimeZoneInfo]:: GetSystemTimeZones()**.

   ![A PowerShell GetSystemTimeZones](./media/automation-scenario-start-stop-vm-wjson-tags/automation-get-timzone-powershell.png)

   * Napok vannak egy hat a nulla érték jelölik. A nulla érték vasárnap.
   * A kezdési időpontot jelöli a **S** attribútum, értéke pedig egy 24 órás formátumban.
   * A végfelhasználók vagy -leállítás időt jelöli a **E** attribútum, értéke pedig egy 24 órás formátumban.

     Ha a **S** és **E** attribútumok minden értéket veheti fel nulla (0), a virtuális gép értékelése során marad a jelenlegi állapotában.
3. Ha ki szeretné hagyni a hét egy adott napjára értékelése, nem adja hozzá a szakaszt, hogy a hét napjára. A következő példában csak hétfő ki lesz értékelve, és az egyéb napokat a hét figyelmen kívül hagyja:

    ```json
    {
        "TzId": "Eastern Standard Time",
        "1": {
            "S": "11",
            "E": "17"
        }
    }
    ```

## <a name="tag-resource-groups-or-vms"></a>Címke erőforráscsoport-sablonok és virtuális gépeken
Virtuális gépek leállítását, vagy a virtuális gépeket, vagy az erőforráscsoportok, amelyben fontosságúak található címkét kell. A ütemezés címkével nem rendelkező virtuális gépek nem értékeli ki. Nem, ezért azok elindítva, vagy állítsa le.

Ezen megoldás címke erőforráscsoportok vagy a virtuális gépek két módja van. Megteheti közvetlenül a portálról. Vagy az Add-ResourceSchedule, a frissítés-ResourceSchedule és a Remove-ResourceSchedule runbookok is használhatja.

### <a name="tag-through-the-portal"></a>A portálon keresztül címke
Virtuális gép vagy erőforráscsoport címkét a portálon lépések végrehajtásával:

1. A JSON-karakterláncban egybesimítására, és győződjön meg arról, hogy nincs-e szóközt.  A JSON karakterláncnak kell kinéznie:

    ```json
    {"TzId":"Eastern Standard Time","0":{"S":"11","E":"17"},"1":{"S":"9","E":"19"},"2": {"S":"9","E":"19"},"3":{"S":"9","E":"19"},"4":{"S":"9","E":"19"},"5":{"S":"9","E":"19"},"6":{"S":"11","E":"17"}}
    ```

2. Válassza ki a **címke** egy virtuális gép vagy erőforrás csoport ütemezés alkalmazása ikonra.

   ![Virtuálisgép-kód beállítását](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-tag-option.png)

3. Címkék vannak definiálva a következő egy kulcs/érték pár. Típus **ütemezés** a a **kulcs** mezőben, majd illessze be a JSON-karakterláncban a **érték** mező. Kattintson a **Save** (Mentés) gombra. Az új címke ekkor meg kell jelennie az erőforrás címkék listájában.

   ![Virtuális gép ütemezés címke](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-schedule-tag.png)

### <a name="tag-from-powershell"></a>PowerShell-címke
Minden importált runbookokat, amely leírja, hogyan hajthat végre a runbookok közvetlenül a PowerShell parancsfájl elején súgó-információkat tartalmaznak. Az Add-ScheduleResource és frissítés-ScheduleResource runbookok powershellből hívása. Ehhez úgy, hogy a szükséges paramétereket, amelyek lehetővé teszik, hogy létrehozni vagy frissíteni a virtuális gép vagy az erőforrás-csoporton a portálon kívül az ütemezés címke.

Szeretne létrehozni, adja hozzá, és a PowerShell segítségével, először meg kell címkék törlése [állítsa be a PowerShell környezetet az Azure-](/powershell/azure/overview). A telepítés befejezése után továbbléphet a következő lépéssel.

### <a name="create-a-schedule-tag-with-powershell"></a>Egy ütemezés címke létrehozása a PowerShell használatával
1. Nyisson meg egy PowerShell-munkamenetet. A hitelesítést a Futtatás mint fiókkal, és adja meg az előfizetés, kövesse az alábbi példa:

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

2. Adjon meg egy ütemezést kivonatoló táblát. Íme egy példa hogyan kell létrehozni:

    ```powershell
    $schedule= @{ "TzId"="Eastern Standard Time"; "0"= @{"S"="11";"E"="17"};"1"= @{"S"="9";"E"="19"};"2"= @{"S"="9";"E"="19"};"3"= @{"S"="9";"E"="19"};"4"= @{"S"="9";"E"="19"};"5"= @{"S"="9";"E"="19"};"6"= @{"S"="11";"E"="17"}}
    ```

3. A runbook által igényelt paramétereinek megadása. A következő példában azt céloz meg egy virtuális Gépet:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; "VmName"="VM01";"Schedule"=$schedule}
    ```

    Ha az erőforráscsoport folyamatban címkézést, távolítsa el a *VMName* paraméter $params kivonatát a tábla az alábbiak szerint:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; "Schedule"=$schedule}
    ```

4. Futtassa az Add-ResourceSchedule runbook ütemezés címke létrehozásához a következő paraméterekkel:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Add-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

5. Egy erőforráscsoport vagy a virtuális gép címke frissítéséhez hajtsa végre a **frissítés-ResourceSchedule** runbook a következő paraméterekkel:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Update-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

### <a name="remove-a-schedule-tag-with-powershell"></a>A PowerShell-lel ütemezés címke eltávolítása
1. Nyisson meg egy PowerShell-munkamenetet, és futtassa a következő, a futtató fiókhoz hitelesítéséhez, és adja meg az előfizetés:

    ```powershell
    Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

2. A runbook által igényelt paramétereinek megadása. A következő példában azt céloz meg egy virtuális Gépet:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01";"VmName"="VM01"}
    ```

    Ha szeretne eltávolítani egy címkét egy erőforráscsoportból, távolítsa el a *VMName* paraméter $params kivonatát a tábla az alábbiak szerint:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"}
    ```

3. Az ütemezés címke eltávolítása a Remove-ResourceSchedule runbook hajtható végre:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

4. Egy erőforráscsoport vagy a virtuális gép címke frissítéséhez hajtsa végre a Remove-ResourceSchedule runbook a következő paraméterekkel:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

> [!NOTE]
> Azt javasoljuk, hogy proaktív nyomon, hogy ezek a runbookok (és a virtuális gép állapota) ellenőrzése, hogy a virtuális gépek vannak épp most állítja le, és ennek megfelelően elindult.
>

Válassza ki, ha a teszt-ResourceSchedule runbook-feladat részletes adatait az Azure portálon, a **feladatok** csempe a runbook. A feladat összegzésében megtekinthetők a bemeneti paraméterek, a kimeneti stream, valamint a feladattal kapcsolatos általános információk és a kivételek (ha sor került rájuk).

A **Feladat összegzése** a kimeneti, figyelmeztető és hibastreamek üzeneteit tartalmazza. A forgatókönyv végrehajtásának részletes eredményeinek megtekintéséhez válassza a **Kimenet** csempét.

![Teszt-ResourceSchedule kimeneti](./media/automation-scenario-start-stop-vm-wjson-tags/automation-job-output.png)

## <a name="next-steps"></a>Következő lépések
* A PowerShell-alapú munkafolyamat-runbookok első lépéseit [Az első PowerShell-alapú munkafolyamat-runbookom](automation-first-runbook-textual.md) című témakör ismerteti.
* Runbook típusait, és azok előnyeit és korlátozások kapcsolatos további információkért lásd: [Azure Automation-runbook típusok](automation-runbook-types.md).
* PowerShell parancsfájl támogatási funkciókkal kapcsolatos további információkért lásd: [natív PowerShell-parancsfájl támogatás az Azure Automationben](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
* Runbook-naplózás és a kimeneti kapcsolatos további információkért lásd: [Runbook-kimenet és üzenetek az Azure Automationben](automation-runbook-output-and-messages.md).
* Egy Azure-beli futtató fiókot, és hogyan kell elvégezni a hitelesítést a runbookok azt kapcsolatos további információkért lásd: [hitelesítéséhez az Azure-beli futtató fiók runbookok](automation-sec-configure-azure-runas-account.md).
