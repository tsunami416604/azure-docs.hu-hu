---
title: "Azure Automation-runbook hozzáadása az Azure Site Recovery helyreállítási tervek |} Microsoft Docs"
description: "Ismerje meg, hogyan Azure Site Recovery segítségével a helyreállítási terv kiterjesztése az Azure Automation használatával. Útmutató: Azure történő helyreállítás során az összetett feladatok elvégzéséhez."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: ecece14d-5f92-4596-bbaf-5204addb95c2
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 11/28/2017
ms.author: ruturajd@microsoft.com
ms.openlocfilehash: af5de1c262bc55b1aa7513ca91b68eb50b44dbb7
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Azure Automation-runbook hozzáadása a helyreállítási terv
Ez a cikk azt ismerteti hogyan integrálható az Azure Site Recovery Azure Automation segítséget a helyreállítási terv. A helyreállítási terv lehet levezényelni a Site Recovery védett virtuális gépek helyreállítása. A helyreállítási terv a replikációs másodlagos felhőhöz, és a replikálást az Azure-működik. A helyreállítási terv is segít a helyreállítási **következetesen pontos**, **ismételhető**, és **automatizált**. A rendszer átadja a virtuális gépek Azure-ba, ha az integráció az Azure Automation szolgáltatásban, a helyreállítási terv terjeszti ki. A runbookok, amelyek hatékony automatizálási feladatok végrehajtásához használható.

Ha most ismerkedik az Azure Automation, akkor [regisztráljon](https://azure.microsoft.com/services/automation/) és [mintaparancsfájlok letöltése](https://azure.microsoft.com/documentation/scripts/). További információt, és megtudhatja, miként kell levezényelni a helyreállítási Azure használatával [helyreállítási tervek](https://azure.microsoft.com/blog/?p=166264), lásd: [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/).

Ez a cikk azt ismerteti hogyan integrálható az Azure Automation-forgatókönyv be a helyreállítási terv. Példák segítségével, amely korábban a kézi beavatkozás szükséges alapvető feladatok automatizálásához. Azt is ismertetik a többlépéses helyreállítási átalakítása egy kattintással indítható helyreállítási művelet.

## <a name="customize-the-recovery-plan"></a>A helyreállítási terv testreszabása
1. Lépjen a **Site Recovery** helyreállítási terv erőforráspanelen. Az ebben a példában a helyreállítási tervben szereplő két virtuális gépek hozzáadott, a helyreállításhoz. Egy runbook hozzáadása megkezdéséhez kattintson a **Testreszabás** fülre.

    ![A Testreszabás gombra.](media/site-recovery-runbook-automation-new/essentials-rp.png)


2. Kattintson a jobb gombbal **csoport 1: Start**, majd válassza ki **post művelet hozzáadása**.

    ![Kattintson a jobb gombbal csoport 1: Start, és a post művelet hozzáadása](media/site-recovery-runbook-automation-new/customize-rp.png)

3. Kattintson a **válasszon egy parancsfájlt**.

4. Az a **műveletének frissítése** panelen, a parancsfájl nevét **Hello World**.

    ![A frissítési művelet panel](media/site-recovery-runbook-automation-new/update-rp.png)

5. Adja meg az Automation-fiók nevét.
    >[!NOTE]
    > Az Automation-fiók lehet bármely Azure régióban. Az Automation-fiók ugyanazt az előfizetést, és az Azure Site Recovery-tárolónak kell lennie.

6. Az Automation-fiók válasszon egy runbookot. Ez a forgatókönyv az első csoport a helyreállítás után a helyreállítási terv végrehajtása közben futtatott parancsfájl.

7. A parancsfájl mentése, kattintson a **OK**. A parancsfájl hozzáadódik **csoport 1: utáni lépéseket**.

    ![Követő műveletet csoport 1:Start](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="considerations-for-adding-a-script"></a>A parancsfájl hozzáadása szempontjai

* A beállítások **egy lépés törlése** vagy **frissítése a parancsfájl**, kattintson a jobb gombbal a parancsfájl.
* Egy parancsfájlt Azure feladatátvétel során a helyi gépről az Azure-bA. Is futtatható Azure leállítás, mielőtt elsődleges webhelyeken parancsfájlként feladat-visszavétel során az Azure-ból a helyi géphez.
* Parancsfájl futtatása, a helyreállítási terv környezet esetében. A következő példa bemutatja, egy környezeti változó:

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

    A következő táblázat nevének és leírásának minden változó az adott környezetben.

    | **Változó neve** | **Leírás** |
    | --- | --- |
    | RecoveryPlanName |A futtatandó terv neve. Ez a változó segítségével különböző műveletek a helyreállítási terv neve alapján. A parancsfájl is felhasználhatja. |
    | FailoverType |Megadja, hogy a feladatátvételi teszt, tervezett vagy nem tervezett. |
    | FailoverDirection |Megadja, hogy helyreállítási elsődleges vagy másodlagos helyhez. |
    | Csoportazonosító |A helyreállítási terv az számát határozza meg a csomag futtatásakor. |
    | VmMap |A csoportban lévő összes virtuális gép tömbjét. |
    | VMMap kulcs |Egyedi kulcs (GUID) az egyes virtuális gépek. Megegyezik a az Azure Virtual Machine Manager (VMM) azonosítója a virtuális gép szükség szerint. |
    | SubscriptionId |Az Azure-előfizetése Azonosítóját, amelyben a virtuális gép létrehozása történt. |
    | RoleName |A helyreállítás alatt álló Azure virtuális gép neve. |
    | CloudServiceName |A Azure felhőalapú szolgáltatás neve, amely alatt a virtuális gép létrehozásának. |
    | erőforráscsoport-név|A Azure erőforráscsoport-név alapján, amely a virtuális gép létrejött. |
    | RecoveryPointId|Ha a virtuális Gépet helyre lett állítva a időbélyegzőjét. |

* Győződjön meg arról, hogy az Automation-fiók rendelkezik-e a következő modult:
    * AzureRM.profile
    * AzureRM.Resources
    * AzureRM.Automation
    * AzureRM.Network
    * AzureRM.Compute

Minden modul kompatibilis verziók kell lennie. Győződjön meg arról, hogy kompatibilisek-e a minden modul segítségével egyszerűen, hogy a modulok legújabb verzióját használja.

### <a name="access-all-vms-of-the-vmmap-in-a-loop"></a>Az ismétlődő VMMap összes gépek elérésének
Az alábbi kód használatával között a Microsoft VMMap minden virtuális gépeinek hurok:

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
> Az erőforrás csoport nevét és a szerepkör neve értékek nincsenek megadva, ha a parancsfájl egy-egy rendszerindítási csoport tagjai előtti műveletet. Az értékeket a rendszer feltölti, csak akkor, ha az adott csoport a virtuális gép feladatátvételi sikeres lesz. A parancsfájl a rendszerindítási csoport tagjai a követő művelet.

## <a name="use-the-same-automation-runbook-in-multiple-recovery-plans"></a>Használja az ugyanazon Automation-forgatókönyv több helyreállítási tervek

A több helyreállítási tervek egy parancsfájl külső változók segítségével is használhatók. Használhat [Azure Automation-változók](../automation/automation-variables.md) paramétereket, amelyek átadhatók egy helyreállítási terv végrehajtásra tárolásához. A helyreállítási terv nevének előtagjaként hozzáadása a változót, az egyes változók minden helyreállítási terv hozhat létre. Ezután használja a változók paraméterekként. Egy paraméter módosíthatja a parancsfájl módosítása nélkül, de ilyenkor is megváltoztathatják a parancsfájl működésére.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Használhat egy egyszerű karakterlánc változót egy runbook parancsfájl

Ebben a példában egy parancsfájlt a a hálózati biztonsági csoport (NSG) bemenetből fogad adatokat, és alkalmazza azt a virtuális gépeket a helyreállítási terv.

A parancsfájl észleli a helyreállítási terv fut, a helyreállítási terv környezet használata:

```
workflow AddPublicIPAndNSG {
    param (
          [parameter(Mandatory=$false)]
          [Object]$RecoveryPlanContext
    )

    $RPName = $RecoveryPlanContext.RecoveryPlanName
```

Egy meglévő NSG alkalmazni, ismernie kell az NSG neve és az NSG erőforráscsoport neve. Ezek a változók használják bemeneti adatokat a helyreállítási terv parancsprogramokat. Ehhez hozzon létre két változót az Automation-fiók eszközök. Adja hozzá a nevét, a helyreállítási terv létrehozása a paramétereinek előtagjaként való a változó nevét.

1. Hozzon létre egy változót, az NSG neve tárolásához. Előtag a helyreállítási terv neve használatával adja hozzá a változó nevét.

    ![Az NSG neve változó létrehozása](media/site-recovery-runbook-automation-new/var1.png)

2. Hozzon létre egy változó tárolásához az NSG az erőforráscsoport neve. Előtag a helyreállítási terv neve használatával adja hozzá a változó nevét.

    ![Hozzon létre egy NSG-t az erőforráscsoport neve](media/site-recovery-runbook-automation-new/var2.png)


3.  A parancsfájl a következő hivatkozás kódot használja a változó segítségével:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  A runbookban található változók segítségével az NSG-t a hálózati illesztő a virtuális gép feladatátadása vonatkoznak:

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

Minden helyreállítási terv létrehozása független változók, hogy a parancsfájl is felhasználhatja. Egy előtagot a helyreállítási terv nevének megadásával. Ebben a forgatókönyvben egy teljes, végpontok közötti parancsfájlt talál [egy nyilvános IP- és NSG-t a virtuális gépek hozzáadása a Site Recovery helyreállítási terv teszt feladatátvétele során](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>További információk tárolására komplex változók használata

Vegye figyelembe a nyilvános IP-cím, az adott virtuális gépeken futó bekapcsolása egyetlen parancsfájllal használni kívánt forgatókönyvhöz. Más esetben érdemes alkalmazni a különböző NSG-k (nem az összes virtuális gépeken futó) különböző virtuális gépek. Egy parancsfájl, amely a helyreállítási terv újrafelhasználható végezheti el. Minden helyreállítási terv olyan virtuális gépek száma lehet. Például egy SharePoint helyreállítási két elülső végpontja van. Egy alapszintű az üzletági (LOB) alkalmazás csak egy előtér rendelkezik. Külön változók minden helyreállítási terv nem hozható létre. 

A következő példában azt egy új módszerrel, és hozzon létre egy [komplex változó](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396) az Azure Automation-fiók eszközök. Ehhez több érték megadásával. Azure PowerShell használatával kell végeznie az alábbi lépéseket:

1. A PowerShellben jelentkezzen be az Azure-előfizetésbe:

    ```
    login-azurermaccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. A paraméterek vannak tárolva, a helyreállítási terv neve segítségével hozzon létre a komplex változó:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. A komplex változóban **VMDetails** rendszer a virtuális gép Azonosítóját a védett virtuális gép számára. Ahhoz, hogy a virtuális gép azonosítója az Azure portálon, a virtuális gép tulajdonságainak megtekintése. Az alábbi képernyőfelvételen látható egy változó, amely két virtuális gépek adatait tárolja:

    ![A globálisan egyedi Azonosítót használja a virtuális gép azonosítója](media/site-recovery-runbook-automation-new/vmguid.png)

4. Ez a változó használata a runbookokban. Ha a megadott virtuális gép GUID Azonosítóját a helyreállítási terv a környezetben található, alkalmazza az NSG-t a virtuális Gépen:

    ```
    $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName
    ```

4. A runbookban a virtuális gépeket a helyreállítási terv környezet ismétlése. Ellenőrizze, hogy a virtuális gép létezik **$VMDetailsObj**. Ha létezik, a változót, alkalmazza az NSG tulajdonságai érhetők el:

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

A különböző helyreállítási tervek használhatja ugyanazt a parancsfájlt. Adjon meg más paraméterekkel tárolja, amely megfelel a helyreállítási terv különböző változók értékét.

## <a name="sample-scripts"></a>Mintaszkriptek

Az Automation-fiók mintaparancsfájlok telepíteni, kattintson a **az Azure telepítéséhez** gombra.

[![Üzembe helyezés az Azure-ban](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Egy másik példa tekintse meg a következő videó. Azt mutatja be helyreállítani egy kétrétegű WordPress alkalmazásból az Azure-bA:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="additional-resources"></a>További források
* [Azure Automation szolgáltatást futtató fiók](../automation/automation-sec-configure-azure-runas-account.md)
* [Azure Automation áttekintése](http://msdn.microsoft.com/library/azure/dn643629.aspx "Azure Automation – áttekintés")
* [Azure Automation-mintaparancsfájlok](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Azure Automation-mintaparancsfájlok")
