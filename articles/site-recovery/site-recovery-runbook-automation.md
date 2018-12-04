---
title: Azure Automation-runbookok hozzáadása a Site Recovery helyreállítási tervek |} A Microsoft Docs
description: Ismerje meg a helyreállítási tervek az Azure Automation szolgáltatással az Azure Site Recovery vész-helyreállítási bővítése.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: c556571e62f6689834f6849dd9b8af0b90ee7539
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52843291"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Azure Automation-runbookok hozzáadása helyreállítási tervekhez
Ebben a cikkben ismertetünk, hogyan integrálható az Azure Site Recovery Azure Automation segítségével kiterjesztheti a helyreállítási tervek. Helyreállítási tervek vezényelhető helyreállítási védett virtuális gépek Site recoveryvel. Helyreállítási tervek működik replikálása egy másodlagos felhőbe, és az Azure-bA. Helyreállítási tervek is érdekében, a helyreállítás **következetesen pontos**, **megismételhető**, és **automatizált**. Ha átadja a feladatokat a virtuális gépek az Azure-ba, az Azure Automation integrációs kiterjeszti a helyreállítási terv. Runbookok, amelyek kínál hatékony automatizálási feladatok végrehajtásához használható.

Ha most ismerkedik az Azure Automation, [regisztráció](https://azure.microsoft.com/services/automation/) és [töltse le a mintaszkriptek](https://azure.microsoft.com/documentation/scripts/). További információk, illetve az Azure-bA helyreállításának összehangolásáról használatával [helyreállítási tervek](./site-recovery-create-recovery-plans.md), lásd: [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/).

Ebben a cikkben ismertetünk, hogyan integrálható az Azure Automation-runbookok a helyreállítási tervekbe. Példák segítségével, amely korábban a kézi beavatkozás szükséges alapvető feladatok automatizálására. Azt is ismertetjük, hogyan egy több lépésből álló helyreállítási átalakítása egy kattintással helyreállítási műveletet.

## <a name="customize-the-recovery-plan"></a>A helyreállítási terv testreszabása
1. Nyissa meg a **Site Recovery** helyreállítási terv erőforrás panelen. Ebben a példában a helyreállítási tervben szereplő, a helyreállítás hozzáadott két virtuális gépet. Egy runbook hozzáadása megkezdéséhez kattintson a **Testreszabás** fülre.

    ![Kattintson a Testreszabás gombra](media/site-recovery-runbook-automation-new/essentials-rp.png)


2. Kattintson a jobb gombbal **csoport 1: Start**, majd válassza ki **utólagos művelet felvétele**.

    ![Kattintson a jobb gombbal csoport 1: Start, és utólagos művelet felvétele](media/site-recovery-runbook-automation-new/customize-rp.png)

3. Kattintson a **szkript kiválasztása**.

4. Az a **műveletének frissítése** panelen nevezze el a szkript **Hello World**.

    ![A frissítési művelet panel](media/site-recovery-runbook-automation-new/update-rp.png)

5. Adjon meg egy Automation-fiók nevét.
    >[!NOTE]
    > Az Automation-fiók lehet bármely Azure-régióban. Az Automation-fiók és az Azure Site Recovery-tárolónak ugyanabban az előfizetésben kell lennie.

6. Válassza ki a runbook az Automation-fiók. Ez a runbook a parancsprogramot, amelynek az első csoport a helyreállítás után a helyreállítási terv végrehajtása közben.

7. A parancsfájl mentéséhez kattintson az **OK**. A parancsfájl adnak hozzá **1. csoport: utólagos lépések**.

    ![Műveletcsoport utáni 1:Start](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="considerations-for-adding-a-script"></a>Szempontok a parancsfájl hozzáadása

* A beállítások **egy lépést Törlés** vagy **frissítse a szkriptet**, kattintson a jobb gombbal a szkriptet.
* Parancsfájl futtatásához az Azure-ban feladatátvétel során a egy helyszíni gépre az Azure-bA. Azt is futtathatja az Azure-ban egy elsődleges helyek parancsprogram leállítása, mielőtt feladat-visszavétel az Azure-ból a helyszíni gépeken során.
* A parancsfájl futtatásakor, a helyreállítási terv környezetek kódtárba. Az alábbi példa bemutatja egy környezeti változót:

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

    A következő táblázat felsorolja a nevét és leírását az egyes környezetében.

    | **Változó neve** | **Leírás** |
    | --- | --- |
    | RecoveryPlanName |A futtatandó terv neve. Ez a változó segítségével különböző műveleteket a helyreállítási terv neve alapján. A parancsfájl is felhasználhatja. |
    | FailoverType |Megadja, hogy a feladatátvételi teszt, tervezett vagy nem tervezett. |
    | FailoverDirection |Megadja, hogy a helyreállítási egy elsődleges vagy másodlagos helyhez. |
    | Csoportazonosító |A csoportazonosító azonosítja a helyreállítási tervben szereplő, a csomag futtatásakor. |
    | VmMap |A csoportban lévő összes virtuális gép tömbje. |
    | VMMap kulcs |Egyedi kulcs (GUID) minden virtuális géphez. Legyen ugyanaz, mint az Azure Virtual Machine Manager (VMM) azonosítója a virtuális gép, ha vannak ilyenek. |
    | SubscriptionId |Az Azure-előfizetési Azonosítóját, amelyben a virtuális gép létrejött. |
    | RoleName |Az Azure, amelyik a helyreállítandó virtuális gép neve. |
    | CloudServiceName |A csoportban, amely a virtuális gép létrehozása az Azure felhőszolgáltatás neve. |
    | ResourceGroupName|Az alapján, amely a virtuális gép létrehozása Azure erőforráscsoport nevét. |
    | RecoveryPointId|Ha a virtuális gép hasznosítják jelölő időbélyegző. |

* Győződjön meg arról, hogy az Automation-fiók rendelkezik-e a következő modulok:
    * AzureRM.profile
    * AzureRM.Resources
    * AzureRM.Automation
    * AzureRM.Network
    * AzureRM.Compute

Minden modul kompatibilis verzióját kell lennie. Győződjön meg arról, hogy a modulok kompatibilisek-e egyszerű módszert, hogy minden a modulok a legújabb verzióra.

### <a name="access-all-vms-of-the-vmmap-in-a-loop"></a>A VMMap hurokba került az összes virtuális gép eléréséhez
A következő kód használatával minden virtuális gépen, a Microsoft VMMap hurok:

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

> [!NOTE]
> Az erőforráscsoport nevét és a szerepkör-nevekhez üresek, ha a parancsfájl egy rendszerindító csoporthoz előzetes műveletét. Az értékek fel van töltve, csak akkor, ha a virtuális gép az adott csoport sikeres feladatátvétel. A parancsfájl nem egy utáni műveletek a rendszerindító csoport.

## <a name="use-the-same-automation-runbook-in-multiple-recovery-plans"></a>Használja ugyanazt az Automation-runbook több helyreállítási tervek

Használhatja egy parancsfájl több helyreállítási tervek a külső változók használatával. Használhat [Azure Automation változó](../automation/automation-variables.md) paramétereket adhat át a helyreállítási terv végrehajtási tárolásához. A helyreállítási terv neve előtagjaként hozzá a változót, az egyes változók minden helyreállítási terv hozhat létre. Ezután használja a változók paraméterekként. Egy paraméter módosítsa a parancsfájl módosítása nélkül, de ilyenkor is megváltoztathatják a parancsfájl működésére.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Használhat egy egyszerű változót olyan forgatókönyv parancsprogramja

Ebben a példában egy parancsfájlt a bemenet egy hálózati biztonsági csoport (NSG) vesz igénybe, és alkalmazza azt a virtuális gépeket a helyreállítási terv.

A parancsfájl észleli a helyreállítási terv fut, használja a helyreállítási terv keretében:

```
workflow AddPublicIPAndNSG {
    param (
          [parameter(Mandatory=$false)]
          [Object]$RecoveryPlanContext
    )

    $RPName = $RecoveryPlanContext.RecoveryPlanName
```

A alkalmazni egy létező NSG-t, ismernie kell az NSG neve és az NSG-t erőforráscsoport nevét. Ezeket a változókat használja bemenetként a helyreállítási terv parancsfájlokhoz. Ehhez hozzon létre két változót az Automation-fiók adategységeket. Adja hozzá a helyreállítási terv előtagjaként való a változó nevét a paraméterek számára létrehozott nevét.

1. Hozzon létre egy változót az NSG neve tárolására. Adjon hozzá egy előtagot a változó nevét, a helyreállítási terv neve használatával.

    ![Az NSG neve változó létrehozása](media/site-recovery-runbook-automation-new/var1.png)

2. Hozzon létre egy változót az NSG-t erőforráscsoport nevét tárolja. Adjon hozzá egy előtagot a változó nevét, a helyreállítási terv neve használatával.

    ![Hozzon létre egy NSG-t erőforráscsoport-név](media/site-recovery-runbook-automation-new/var2.png)


3.  A szkriptben használja a következő hivatkozási kód változó értékének lekéréséhez:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  A runbook a változókat használják az NSG vonatkozik a átvevő virtuális gép hálózati adapterét:

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

Minden helyreállítási terv létrehozása független változók, így újból felhasználhatja a parancsfájlt. A helyreállítási terv neve használatával adjon hozzá egy előtagot. Ebben a forgatókönyvben egy teljes, végpontok közötti parancsfájlt talál [egy nyilvános IP- és NSG-t, virtuális gépek hozzáadása a Site Recovery helyreállítási terv teszt feladatátvétele során](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>További információk tárolására komplex változók használata

Képzeljen el egy forgatókönyvet, amelyben szeretné egy parancsfájl egy nyilvános IP-címet az adott virtuális gépek bekapcsolásához. Egy másik esetben érdemes alkalmazni a különböző NSG-ket különböző virtuális gépek (nem az összes virtuális gép). Meghatározhat egy parancsfájlt, amely minden olyan helyreállítási terv újrahasználható. Minden helyreállítási terv rendelkezhet változó számú virtuális gépeket. Például a SharePoint-helyreállítás, két előtérrendszerek. Egy alapszintű – üzletági (LOB) alkalmazás csak egy előtér rendelkezik. Külön változók minden helyreállítási terv nem hozható létre.

A következő példában, hogy egy új módszerrel, és hozzon létre egy [komplex változó](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureautomationvariable) a az Azure Automation-fiók eszközöket. Ehhez több érték megadásával. Azure PowerShell használatával kell a következő lépéseket:

1. A PowerShellben jelentkezzen be az Azure-előfizetés:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Szeretné tárolni a paramétereket, az összetett változó létrehozása a helyreállítási terv neve használatával:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. Ez a komplex változó **VMDetails** van a virtuális gép azonosítója a védett virtuális gép számára. Kérje le a virtuális gép azonosítója az Azure Portalon, a virtuális gép tulajdonságainak megtekintéséhez. Az alábbi képernyőfelvételen egy változó, amely tárolja a két virtuális gép részletei:

    ![A globálisan egyedi Azonosítót használja a virtuális gép azonosítója](media/site-recovery-runbook-automation-new/vmguid.png)

4. Használja ezt a változót a runbookban. Ha a megadott virtuális gép GUID Azonosítóját a helyreállítási terv a környezetben található, alkalmazza az NSG-KET a virtuális gépen:

    ```
    $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName
    ```

4. A runbookban a virtuális gépeket a helyreállítási terv keretében hurkot. Ellenőrizze, hogy a virtuális gép létezik **$VMDetailsObj**. Ha létezik, a változó a alkalmazni az NSG-t a tulajdonságai érhetők el:

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            Write-output $VMDetailsObj.value.$VMID

            if ($VMDetailsObj.value.$VMID -ne $Null) { #If the VM exists in the context, this will not b Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetailsObj.value.$VMID.'NSGName'
                $NSGRGname = $VMDetailsObj.value.$VMID.'NSGResourceGroupName'

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

Használhatja ugyanezt a szkriptet a különböző helyreállítási tervek. Adja meg a különböző paramétereket, amely megfelel a helyreállítási terv a különböző változók értékét tárolja.

## <a name="sample-scripts"></a>Mintaszkriptek

Mintaszkriptek telepíteni az Automation-fiókot, kattintson a **üzembe helyezés az Azure** gombra.

[![Üzembe helyezés az Azure-ban](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Egy másik példa tekintse meg az alábbi videó. Ebben bemutatjuk, helyreállítása egy kétrétegű WordPress-alkalmazás az Azure-bA:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="additional-resources"></a>További források
* [Az Azure Automation szolgáltatást futtató fiók](../automation/automation-create-runas-account.md)
* [Az Azure Automation áttekintése](https://msdn.microsoft.com/library/azure/dn643629.aspx "Azure Automation áttekintése")
* [Az Azure Automation-mintaszkriptek](https://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Azure Automation-mintaszkriptek")

## <a name="next-steps"></a>További lépések
[További](site-recovery-failover.md) feladatátvétel futtatásával kapcsolatos.
