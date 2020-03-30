---
title: Azure Automation-runbookok hozzáadása a webhely-helyreállítási helyreállítási tervekhez
description: Ismerje meg, hogyan bővítheti a helyreállítási terveket az Azure Automation használatával az Azure Site Recovery használatával a vészhelyreállításhoz.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: rajanaki
ms.openlocfilehash: ecfe993a137ca63c84438870ec54ac1e6d6707da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257484"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Azure Automation-runbookok hozzáadása helyreállítási tervekhez

Ez a cikk ismerteti, hogyan integrálható az Azure Automation runbookok, az [Azure Site Recovery](site-recovery-overview.md) helyreállítási tervek kiterjesztése. Bemutatjuk, hogyan automatizálhatja azokat az alapvető feladatokat, amelyek egyébként manuális beavatkozást igényelnének, és hogyan alakíthat át egy többlépéses helyreállítást egykattintásos műveletté.

## <a name="recovery-plans"></a>Helyreállítási tervek 

A helyreállítási tervek et a helyszíni gépek vagy az Azure-beli virtuális gépek feladatátvételkor használhatja. A helyreállítási tervek segítségével meghatározhatja a rendszeres helyreállítási folyamatot, amely meghatározza, hogy a gépek hogyan feladatátvételt, és hogyan indulnak el és helyreállnak a feladatátvétel után. 

A nagy méretű alkalmazások helyreállítása összetett lehet. A helyreállítási tervek segítenek a megrendelés előírásában, így a helyreállítás következetesen pontos, megismételhető és automatizált. A helyreállítási tervben lévő feladatokat parancsfájlok és Azure Automation-runbookok használatával automatizálhatja. Tipikus példák lehet nek beállítások konfigurálása egy Azure-beli virtuális gép feladatátvétel után, vagy újrakonfigurálása egy alkalmazás, amely fut a virtuális gépen.

- [További információ](recovery-plan-overview.md) a helyreállítási tervekről.
- [További információ](../automation/automation-runbook-types.md) az Azure Automation runbookokról.



## <a name="runbooks-in-recovery-plans"></a>Runbookok a helyreállítási tervekben

Hozzáad egy Azure Automation-fiókot és runbookokat egy helyreállítási tervhez. A runbook meghívása a helyreállítási terv futtatásakor.

- Az Automation-fiók bármely Azure-régióban lehet, és ugyanabban az előfizetésben kell lennie, mint a Site Recovery-tároló. 
- A runbook futtatható a helyreállítási terv során feladatátvétel egy elsődleges helyről másodlagos, vagy a feladat-visszavétel során a másodlagos helyről az elsődleges.
- A helyreállítási tervben lévő Runbookok egymás után, a beállított sorrendben futnak.
- Ha a helyreállítási tervben lévő runbookok konfigurálják a virtuális gépeket, hogy különböző csoportokban induljanak el, a helyreállítási terv csak akkor folytatódik, ha az Azure az összes virtuális gépet futóként jelenti.
- A helyreállítási tervek továbbra is futnak, még akkor is, ha egy parancsfájl meghibásodik.

### <a name="recovery-plan-context"></a>Helyreállítási terv környezete

Amikor egy parancsfájl fut, egy helyreállítási terv környezetet a runbook. A környezet a táblázatban összegzett változókat tartalmazza.

| **Változó neve** | **Leírás** |
| --- | --- |
| RecoveryPlanName (Helyreállításitervneve) |Helyreállítási terv neve. A névn alapuló műveletekben használatos. |
| Feladatátvételtípusa |Megadja, hogy teszt ről vagy éles feladatátvételről van-e szó. 
| Feladatátvételirány | Itt adható meg, hogy a helyreállítás elsődleges vagy másodlagos helyre kerüljön.Specifies whether recovery is to a primary or secondary location. |
| Csoportazonosító |A helyreállítási tervben lévő csoportszámot azonosítja a terv futásakor. |
| Vmmap |A csoport összes virtuális gépének tömbje. |
| VMMap-kulcs |Egy egyedi kulcs (GUID) minden virtuális gép. |
| SubscriptionId |Az Azure-előfizetés-azonosító, amelyben a virtuális gép jött létre. |
| ResourceGroupName | Annak az erőforráscsoportnak a neve, amelyben a virtuális gép található.
| CloudServiceName (Felhőszolgáltatásneve) |Az Azure felhőszolgáltatás neve, amely alatt a virtuális gép jött létre. |
| RoleName |Az Azure virtuális gép neve. |
| RecoveryPointId azonosító|A virtuális gép helyreállításának időbélyege. |

A következő példa egy környezeti változót mutat be:

```
{"RecoveryPlanName":"hrweb-recovery",
"FailoverType":"Test",
"FailoverDirection":"PrimaryToSecondary",
"GroupId":"1",
"VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":
    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
    "ResourceGroupName":"ContosoRG",
    "CloudServiceName":"pod02hrweb-Chicago-test",
    "RoleName":"Fabrikam-Hrweb-frontend-test",
    "RecoveryPointId":"TimeStamp"}
    }
}
```

Ha egy ciklusban szeretné elérni a VMMap összes virtuális gépét, a következő kódot használhatja:

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
    foreach($VMID in $VMinfo)
    {
        $VM = $vmMap.$VMID                
            if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
            #this check is to ensure that we skip when some data is not available else it will fail
    Write-output "Resource group name ", $VM.ResourceGroupName
    Write-output "Rolename " = $VM.RoleName
            }
        }
```


Aman Sharma blogja több mint a [betakarítás felhők](http://harvestingclouds.com) egy hasznos példa a helyreállítási [terv összefüggésben script](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/).



## <a name="before-you-start"></a>Előkészületek

- Ha most jön az Azure Automation, [regisztrálhat](https://azure.microsoft.com/services/automation/) és [letöltheti a mintaparancsfájlokat.](https://azure.microsoft.com/documentation/scripts/)
- Győződjön meg arról, hogy az Automation-fiók rendelkezik a következő modulokkal:
    - AzureRM.profile
    - AzureRM.Resources
    - AzureRM.Automation
    - AzureRM.Network
    - AzureRM.Compute

    Minden modulnak kompatibilis verzióból kell lennie. A legegyszerűbb módja az, hogy mindig a legújabb verzióját használja az összes modul.



## <a name="customize-the-recovery-plan"></a>A helyreállítási terv testreszabása

1. A tárolóban válassza a **Helyreállítási tervek (Site Recovery) lehetőséget.**
2. Helyreállítási terv létrehozásához kattintson **a +Helyreállítási terv gombra.** [További információ](site-recovery-create-recovery-plans.md). Ha már rendelkezik helyreállítási tervvel, válassza a megnyitásához.
3. A helyreállítási terv lapon kattintson a **Testreszabás gombra.**

    ![Kattintson a Testreszabás gombra](media/site-recovery-runbook-automation-new/custom-rp.png)

2. Kattintson az **1.** > **Add post action**
3. A **Beszúrás műveletben**ellenőrizze, hogy a **Parancsfájl** ki van-e jelölve, és adja meg a parancsfájl (**Hello World**) nevét.
4. Adjon meg egy automatizálási fiókot, és válasszon ki egy runbookot. A parancsfájl mentéséhez kattintson az **OK gombra.** A szkript hozzáadódik az **1.**


## <a name="reuse-a-runbook-script"></a>Runbook-parancsfájl újrafelhasználása

Egyetlen runbook-parancsfájlt több helyreállítási tervben is használhat külső változók használatával. 

- Az [Azure Automation-változók](../automation/automation-variables.md) segítségével tárolja a helyreállítási terv futtatásához paramétereket.
- Ha a helyreállítási terv nevét előtagként adja hozzá a változóhoz, minden helyreállítási tervhez létrehozhat egyedi változókat. Ezután használja a változókat paraméterként.
- A paramétereket a parancsfájl módosítása nélkül módosíthatja, de a parancsfájl működését mégis módosíthatja.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Egyszerű karakterlánc-változó használata runbook-parancsfájlban

Ebben a példában egy parancsfájl egy hálózati biztonsági csoport (NSG) bemenetét veszi fel, és alkalmazza azt a helyreállítási tervben szereplő virtuális gépekre. 

1. Annak érdekében, hogy a parancsfájl észlelje, hogy melyik helyreállítási terv fut, használja ezt a helyreállítási terv környezetet:

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. Jegyezze fel az NSG nevét és erőforráscsoportját. Ezeket a változókat használja bemenetként a helyreállítási terv parancsfájljaihoz. 
1. Az Automation számlaeszközeiben. hozzon létre egy változót az NSG nevének tárolására. Adjon előtagot a változó nevéhez a helyreállítási terv nevével.

    ![NSG-névváltozó létrehozása](media/site-recovery-runbook-automation-new/var1.png)

2. Hozzon létre egy változót az NSG-erőforrás erőforráscsoport nevének tárolására. Adjon előtagot a változó nevéhez a helyreállítási terv nevével.

    ![NSG-erőforráscsoport nevének létrehozása](media/site-recovery-runbook-automation-new/var2.png)


3.  A parancsfájlban ezzel a hivatkozási kóddal kaphatja le a változóértékeket:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  A runbook változóival alkalmazhatja az NSG-t a sikertelen virtuális gép hálózati felületére:

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```


Minden helyreállítási tervhez hozzon létre független változókat, hogy újra felhasználhassa a parancsfájlt. Adjon hozzá előtagot a helyreállítási terv nevének használatával. 

Ebben a forgatókönyvben egy teljes, teljes körű parancsfájlt tekintse át [ezt a parancsfájlt.](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee)


### <a name="use-a-complex-variable-to-store-more-information"></a>Több információ tárolása összetett változó használatával

Bizonyos esetekben előfordulhat, hogy nem tud külön változót létrehozni az egyes helyreállítási tervekhez. Fontolja meg egy olyan forgatókönyvet, amelyben egyetlen parancsfájlt szeretne hozzárendelni egy nyilvános IP-címet az adott virtuális gépekhez. Egy másik esetben előfordulhat, hogy különböző NSG-ket szeretne alkalmazni a különböző virtuális gépeken (nem minden virtuális gépen). Vegye figyelembe:

- Olyan parancsfájlt hozhat újra, amely minden helyreállítási tervhez újrafelhasználható.
- Minden helyreállítási terv rendelkezhet egy változó számú virtuális gépek.
- Egy SharePoint-helyreállításnak például két előtérvége van. Egy egyszerű üzletági (LOB) alkalmazás csak egy előtérrel rendelkezik.
- Ebben az esetben nem hozhat létre külön változókat az egyes helyreállítási tervekhez.

A következő példában egy [összetett változót](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureautomationvariable) hozunk létre az Azure Automation-fiókban.

Ezt több érték megadásával tesszük meg az Azure PowerShell használatával.

1. A PowerShellben jelentkezzen be az Azure-előfizetésbe:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. A paraméterek tárolásához hozza létre az összetett változót a helyreállítási terv nevével:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. Ebben az összetett változóban **a VMDetails** a védett virtuális gép virtuális gép virtuális gépazonosítója. A virtuálisgép-azonosító leigazolásához az Azure Portalon tekintse meg a virtuális gép tulajdonságait. A következő képernyőképen látható egy változó, amely tárolja a részleteket a két virtuális gép:

    ![A virtuálisgép-azonosító használata GUID azonosítóként](media/site-recovery-runbook-automation-new/vmguid.png)

4. Használja ezt a változót a runbookban. Ha a jelzett virtuálisgép GUID található a helyreállítási terv környezetben, alkalmazza az NSG a virtuális gép:

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. A runbookban a helyreállítási terv környezetében a virtuális gépek között. Ellenőrizze, hogy a virtuális gép létezik-e **$VMDetailsObj.** Ha létezik, az NSG alkalmazásához érje el a változó tulajdonságait:

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            $VMDetails = $VMDetailsObj[$VMID].ToObject([hashtable]);
            Write-output $VMDetails
            if ($VMDetails -ne $Null) { #If the VM exists in the context, this will not be Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetails.NSGName
                $NSGRGname = $VMDetails.NSGResourceGroupName

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

Ugyanazt a parancsfájlt használhatja a különböző helyreállítási tervekhez. Adjon meg különböző paramétereket a különböző változók helyreállítási tervének megfelelő érték tárolásával.

## <a name="sample-scripts"></a>Mintaszkriptek

Ha mintaparancsfájlokat szeretne telepíteni az Automation-fiókba, kattintson az **Azure-ba való üzembe helyezés** gombra.

[![Üzembe helyezés az Azure-ban](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Ez a videó egy másik példa. Bemutatja, hogyan lehet visszaállítani egy kétrétegű WordPress alkalmazást az Azure-ba:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>További lépések

- További információ az [Azure Automation Futtatás másként fiókról](../automation/automation-create-runas-account.md)
- Tekintse át [az Azure Automation mintaparancsfájljait.](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team)
- [További információ](site-recovery-failover.md) a feladatátvételek futtatásáról.



