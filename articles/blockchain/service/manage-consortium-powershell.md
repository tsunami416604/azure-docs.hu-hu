---
title: Az Azure Blockchain Service konzorciumi tagjainak kezelése - PowerShell
description: Ismerje meg, hogyan kezelheti az Azure Blockchain Service konzorciumi tagjait az Azure PowerShell használatával.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: zeyadr
ms.openlocfilehash: e819dd39481b58d446384a5e2253c548ce0c267c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505980"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Konzorciumi tagok kezelése az Azure Blockchain Szolgáltatásban a PowerShell használatával

A PowerShell segítségével kezelheti az Azure Blockchain Szolgáltatás blockchain konzorciumi tagjait. A rendszergazdai jogosultságokkal rendelkező tagok meghívhatják, hozzáadhatják, eltávolíthatják és módosíthatják a blokklánc-konzorcium összes résztvevőjének szerepköreit. A felhasználói jogosultságokkal rendelkező tagok megtekinthetik a blockchain konzorcium összes résztvevőjét, és módosíthatják a tagok megjelenítendő nevét.

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy blockchain tagot az [Azure Portal](create-member.md)használatával.
* A konzorciumokról, a tagokról és a csomópontokról az [Azure Blockchain Service konzorciumban](consortium.md)talál további információt.

## <a name="open-azure-cloud-shell"></a>Az Azure Cloud Shell megnyitása

Az Azure Cloud Shell egy ingyenes interaktív rendszerhéj, amely segítségével futtathatja a cikkben leírt lépéseket. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shellt egy külön böngészőlapon is megnyithatja, ha [shell.azure.com/powershell](https://shell.azure.com/powershell). Válassza a **Másolás** lehetőséget a kódblokkok másolásához, beillesztését a Cloud Shellbe, és válassza az **Enter** lehetőséget a futtatásához.

## <a name="install-the-powershell-module"></a>A PowerShell-modul telepítése

Telepítse a Microsoft.AzureBlockchainService.ConsortiumManagement.PS csomagot a PowerShell-galériából.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Az információbeállítás beállítása

A parancsmagok végrehajtásakor további információkat kaphat az információs beállításváltozó beállításával. Alapértelmezés szerint *$InformationPreference* a *Csendes folytatás*értékre van állítva.

További részletes információkért a parancsmagokból az alábbiak szerint állítsa be a beállítást a PowerShellben:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Web3-kapcsolat létrehozása

A konzorciumi tagok kezeléséhez hozzon létre egy Web3-kapcsolatot a Blockchain Service tagi végpontjával. Ezzel a parancsfájllal globális változókat állíthat be a konzorciumkezelési parancsmagok hívásához.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Cserélje le * \<\> a tagfiók jelszavát* a tag létrehozásakor használt tagfiók-jelszóra.

Keresse meg a többi értéket az Azure Portalon:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Nyissa meg a Blockchain szolgáltatás alapértelmezett tagjának **áttekintése** lapot.

    ![Tag áttekintése](./media/manage-consortium-powershell/member-overview.png)

    Cserélje le * \<a tagfiókot\> * és * \<a RootContract címet\> * a portál értékeire.

1. A végpontcímhez válassza a **Tranzakciócsomópontok**lehetőséget, majd válassza ki az **alapértelmezett tranzakciócsomópontot.** Az alapértelmezett csomópont neve megegyezik a blokklánc-tag nevével.
1. Válassza **a Kapcsolati karakterláncok lehetőséget**.

    ![Kapcsolati sztringek](./media/manage-consortium-powershell/connection-strings.png)

    Cserélje * \<\> * le a végpont címét a **HTTPS (1. hozzáférési kulcs)** vagy **a HTTPS (Access key 2)** értékére.

## <a name="manage-the-network-and-smart-contracts"></a>A hálózat és az intelligens szerződések kezelése

A hálózat és az intelligens szerződés parancsmagokkal kapcsolatot létesítanek a blokklánc-végpont konzorciumkezelésért felelős intelligens szerződéseivel.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Ezzel a parancsmagmal csatlakozhat a konzorciumi menedzsment intelligens szerződéseihez. Ezeket a szerződéseket a konzorciumtagjainak kezelésére és érvényesítésére használják.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| RootContractAddress cím | A konzorciumkezelési intelligens szerződések gyökérszerződésének címe | Igen |
| Web3Ügyfél | A New-Web3Connection programból beszerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Ezzel a parancsmagmal objektumot hozhat létre egy távoli csomópont felügyeleti fiókjának adatainak tárolására.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| ManagedAccountAddress cím | Blokklánc-tagfiók címe | Igen |
| ManagedAccountPassword | Fiókcím jelszava | Igen |

#### <a name="example"></a>Példa

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>Új-Web3Kapcsolat

Ezzel a parancsmagval kapcsolatot létesítanek egy tranzakciócsomópont RPC-végpontjával.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| RemoteRPCVégpont | Blokklánc-tag végpontjának címe | Igen |

#### <a name="example"></a>Példa

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>A konzorcium tagjainak kezelése

Konzorciumi tagkezelési parancsmagokkal kezelheti a konzorcium tagjait. A rendelkezésre álló műveletek a konzorciumi szerepkörtől függenek.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Ezzel a parancsmaggal lekaphatja a tagok adatait vagy listázta a konzorcium tagjait.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| Név | Annak a Blockchain szolgáltatástagnak a neve, amelyről részleteket szeretne beolvasni. Név beírásakor a tag adatait adja vissza. Ha egy nevet kihagy, az összes konzorciumi tag listáját adja vissza. | Nem |
| Tagok | Az Import-ConsortiumManagementContracts programból beszerzett Tagok objektum | Igen |
| Web3Ügyfél | A New-Web3Connection programból beszerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

[Web3-kapcsolat létrehozása](#establish-a-web3-connection) a $ContractConnection változó beállításához.

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>Példa kimenetre

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Eltávolítás-BlockchainMember

Ezzel a parancsmaggal eltávolíthatja a blokklánc-tagot.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| Név | Eltávolítandó tag név | Igen |
| Tagok | Az Import-ConsortiumManagementContracts programból beszerzett Tagok objektum | Igen |
| Web3Fiók | Az Import-Web3Account programból beszerzett Web3Account objektum | Igen |
| Web3Ügyfél | A New-Web3Connection programból beszerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

[Web3-kapcsolat létrehozása](#establish-a-web3-connection) a $ContractConnection és a $MemberAccount változók beállításához.

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Ezzel a parancsmaggal beállíthatja a blokklánc tag attribútumait, beleértve a megjelenítendő nevet és a konzorciumi szerepkört.

A konzorciumi rendszergazdák beállíthatják a **DisplayName** és a **Role szerepkört** az összes tag számára. A felhasználói szerepkörrel rendelkező konzorciumi tagok csak a saját tagjuk megjelenítendő nevét módosíthatják.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| Név | A blokklánc-tag neve | Igen |
| DisplayName | Új megjelenítendő név | Nem |
| Fiókcím | Fiók címe | Nem |
| Tagok | Az Import-ConsortiumManagementContracts programból beszerzett Tagok objektum | Igen |
| Web3Fiók | Az Import-Web3Account programból beszerzett Web3Account objektum | Igen |
| Web3Ügyfél |  A New-Web3Connection programból beszerzett Web3Client objektum| Igen |

#### <a name="example"></a>Példa

[Web3-kapcsolat létrehozása](#establish-a-web3-connection) a $ContractConnection és a $MemberAccount változók beállításához.

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>A konzorciumtagok meghívásának kezelése

A konzorciumi tag meghíváskezelési parancsmagjaival kezelheti a konzorciumtagok meghívásait. A rendelkezésre álló műveletek a konzorciumi szerepkörtől függenek.

### <a name="new-blockchainmemberinvitation"></a>Új-BlockchainMemberInvitation

Ezzel a parancsmaggal új tagokat hívhat meg a konzorciumba.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| SubscriptionId | A meghívandó tag Azure-előfizetés-azonosítója | Igen |
| Szerepkör | A konzorcium szerepe. Az értékek lehetnek ADMIN vagy USER. Az ADMIN a konzorciumi rendszergazdai szerepkör. User a konzorcium tagszerepe. | Igen |
| Tagok | Az Import-ConsortiumManagementContracts programból beszerzett Tagok objektum | Igen |
| Web3Fiók | Az Import-Web3Account programból beszerzett Web3Account objektum | Igen |
| Web3Ügyfél | A New-Web3Connection programból beszerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

[Web3-kapcsolat létrehozása](#establish-a-web3-connection) a $ContractConnection és a $MemberAccount változók beállításához.

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Ezzel a parancsmaggal lekérheti vagy listázheti a konzorciumi tagok meghívási állapotát.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| SubscriptionId | A meghívandó tag Azure-előfizetés-azonosítója. Ha az előfizetés-azonosító meg van adva, az az előfizetés-azonosító meghívójának részleteit adja vissza. Ha az előfizetés-azonosító nincs megadva, az összes tagmeghívás listáját adja vissza. | Nem |
| Tagok | Az Import-ConsortiumManagementContracts programból beszerzett Tagok objektum | Igen |
| Web3Ügyfél | A New-Web3Connection programból beszerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

[Web3-kapcsolat létrehozása](#establish-a-web3-connection) a $ContractConnection változó beállításához.

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation –SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Példa kimenetre

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Eltávolítás-BlockchainMemberInvitation

Ezzel a parancsmaggal visszavonhatja egy konzorciumi tag meghívását.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| SubscriptionId | A visszavonandó tag Azure-előfizetés-azonosítója | Igen |
| Tagok | Az Import-ConsortiumManagementContracts programból beszerzett Tagok objektum | Igen |
| Web3Fiók | Az Import-Web3Account programból beszerzett Web3Account objektum | Igen |
| Web3Ügyfél | A New-Web3Connection programból beszerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

[Web3-kapcsolat létrehozása](#establish-a-web3-connection) a $ContractConnection és a $MemberAccount változók beállításához.

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Ezzel a parancsmagval beállíthatja a **szerepkört** egy meglévő meghíváshoz. Csak a konzorciumi rendszergazdák módosíthatják a meghívókat.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| SubscriptionId | A meghívandó tag Azure-előfizetés-azonosítója | Igen |
| Szerepkör | Új konzorciumi szerepkör a meghíváshoz. Az értékek lehetnek **USER** vagy **ADMIN**. | Igen |
| Tagok |  Az Import-ConsortiumManagementContracts programból beszerzett Tagok objektum | Igen |
| Web3Fiók | Az Import-Web3Account programból beszerzett Web3Account objektum | Igen |
| Web3Ügyfél | A New-Web3Connection programból beszerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

[Web3-kapcsolat létrehozása](#establish-a-web3-connection) a $ContractConnection és a $MemberAccount változók beállításához.

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>További lépések

A konzorciumokról, a tagokról és a csomópontokról az [Azure Blockchain Service konzorciumában](consortium.md) talál további információt.
