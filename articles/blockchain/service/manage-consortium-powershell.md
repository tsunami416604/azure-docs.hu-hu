---
title: Az Azure Blockchain szolgáltatás tagjainak kezelése Azure PowerShell használatával
description: Ismerje meg, hogyan kezelheti az Azure Blockchain Service Consortium-tagokat Azure PowerShell használatával.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: c35a3bd99518825805c2f29cfdc586e1ccf5b0bb
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329177"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-by-using-powershell"></a>A konzorcium tagjainak kezelése az Azure Blockchain szolgáltatásban a PowerShell használatával

A PowerShell használatával kezelheti az Azure Blockchain szolgáltatás blockchain-konzorciumának tagjait. A rendszergazdai jogosultságokkal rendelkező tagok meghívhatják, hozzáadhatják, eltávolíthatják és módosíthatják a blockchain Consortium összes résztvevőjének szerepkörét. A felhasználói jogosultságokkal rendelkező tagok megtekinthetik a blockchain konzorcium összes résztvevőjét, és megváltoztathatják a tagok megjelenítendő nevét.

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy blockchain tagot a [Azure Portal](create-member.md)használatával.
* A konzorciumokkal, tagokkal és csomópontokkal kapcsolatos további információkért lásd: [Azure Blockchain Service Consortium](consortium.md).

## <a name="open-azure-cloud-shell"></a>Az Azure Cloud Shell megnyitása

A Azure Cloud Shell egy ingyenes interaktív felület, amellyel a cikkben ismertetett lépéseket futtathatja. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell egy külön böngésző lapon is megnyithatja a [shell.Azure.com/PowerShell](https://shell.azure.com/powershell). Válassza a **Másolás** lehetőséget a kód blokkok másolásához, illessze be Cloud Shellba, majd válassza az **ENTER billentyűt** a futtatásához.

## <a name="install-the-powershell-module"></a>A PowerShell-modul telepítése

Telepítse a Microsoft.AzureBlockchainService.ConsortiumManagement.PS csomagot a PowerShell-galéria.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Az információs beállítások megadása

A parancsmagok végrehajtásával kapcsolatos további információkat az információs preferencia változó beállításával kaphat. Alapértelmezés szerint a *$InformationPreference* a *SilentlyContinue*értékre van állítva.

A parancsmagok részletes információit a következő módon állíthatja be a PowerShellben:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Web3-kapcsolat létrehozása

A konzorcium tagjainak kezeléséhez hozzon létre egy Web3-kapcsolatot a Blockchain-szolgáltatási tag végpontjának. Ezzel a parancsfájllal globális változókat állíthat be a konzorcium-felügyeleti parancsmagok meghívásához.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Cserélje le a *\<Member fiók jelszavát @ no__t-2* a tag fiókjának jelszavára, amelyet a tag létrehozásakor használt.

A Azure Portal többi értékének megkeresése:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Lépjen az alapértelmezett Blockchain-szolgáltatási tag **– Áttekintés** lapra.

    ![Tag áttekintése](./media/manage-consortium-powershell/member-overview.png)

    Cserélje le a *\<Member fiókot a @ no__t-2* és a *\<RootContract címet @ no__t-5* értékre a portál értékeivel.

1. A végpont címe mezőben válassza a **tranzakciós csomópontok**lehetőséget, majd válassza ki az **alapértelmezett tranzakció csomópontot**. Az alapértelmezett csomópont neve megegyezik a blockchain taggal.
1. Válassza a **kapcsolatok karakterláncok**lehetőséget.

    ![Kapcsolati sztringek](./media/manage-consortium-powershell/connection-strings.png)

    Cserélje le a *\<Endpoint-címe @ no__t-2* értéket a https-ből származó értékre **(1. hozzáférési kulcs)** vagy https-re **(2. hozzáférési kulcs)** .

## <a name="manage-the-network-and-smart-contracts"></a>A hálózat és az intelligens szerződések kezelése

A hálózati és az intelligens szerződési parancsmagokkal kapcsolatot létesíthet a blockchain-végpontnak a konzorciumok felügyeletével kapcsolatos intelligens szerződésekkel.

### <a name="import-consortiummanagementcontracts"></a>Importálás – ConsortiumManagementContracts

Ezzel a parancsmaggal csatlakozhat a konzorciumi felügyelet intelligens szerződésekhez. Ezek a szerződések a tagok a konzorciumon belüli felügyeletére és betartatására szolgálnak.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| RootContractAddress | A konzorcium-felügyeleti intelligens szerződések fő szerződési címe | Igen |
| Web3Client | A New-Web3Connection beszerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Importálás – Web3Account

Ezzel a parancsmaggal hozhat létre egy objektumot, amely a távoli csomópont felügyeleti fiókjához tartozó információkat tárolja.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Blockchain-tag fiókjának címe | Igen |
| ManagedAccountPassword | Fiók címe jelszó | Igen |

#### <a name="example"></a>Példa

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>Új – Web3Connection

Ezzel a parancsmaggal kapcsolatot létesíthet egy tranzakciós csomópont RPC-végpontján.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Blockchain-tag végpontjának címe | Igen |

#### <a name="example"></a>Példa

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>A konzorcium tagjainak kezelése

A konzorcium tagjai a konzorciumon belül kezelhetők. Az elérhető műveletek a konzorciumi szerepkörtől függenek.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Ezzel a parancsmaggal szerezheti be a tagok adatait, vagy listázhatja a konzorcium tagjait.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| Név | Annak a Blockchain-szolgáltatásnak a neve, amelyről adatokat szeretne lekérdezni. Név megadásakor a rendszer a tag adatait adja vissza. Egy név kihagyása esetén a a konzorcium összes tagjának listáját adja vissza. | Nem |
| Tagok | Import-ConsortiumManagementContracts által beszerzett tagok objektum | Igen |
| Web3Client | A New-Web3Connection beszerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

[Hozzon létre egy Web3-kapcsolatot](#establish-a-web3-connection) a $ContractConnection változó beállításához.

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

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Ezzel a parancsmaggal eltávolíthat egy blockchain tagot.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| Név | Eltávolítandó tag neve | Igen |
| Tagok | Import-ConsortiumManagementContracts által beszerzett tagok objektum | Igen |
| Web3Account | Import-Web3Account által beszerzett Web3Account objektum | Igen |
| Web3Client | A New-Web3Connection beszerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

[Hozzon létre egy Web3-kapcsolatot](#establish-a-web3-connection) a $ContractConnection és $MemberAccount változók beállításához.

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Ezzel a parancsmaggal beállíthatja a blockchain, beleértve a megjelenítendő nevet és a konzorciumi szerepkört.

A konzorcium-rendszergazdák minden tag számára megadhatják a **DisplayName** és a **szerepkört** . A felhasználói szerepkörrel rendelkező konzorcium-tag csak a saját tag megjelenítendő nevét módosíthatja.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| Név | A blockchain-tag neve | Igen |
| DisplayName | Új megjelenítendő név | Nem |
| AccountAddress | Fiók címe | Nem |
| Tagok | Import-ConsortiumManagementContracts által beszerzett tagok objektum | Igen |
| Web3Account | Import-Web3Account által beszerzett Web3Account objektum | Igen |
| Web3Client |  A New-Web3Connection beszerzett Web3Client objektum| Igen |

#### <a name="example"></a>Példa

[Hozzon létre egy Web3-kapcsolatot](#establish-a-web3-connection) a $ContractConnection és $MemberAccount változók beállításához.

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>A konzorcium tagjai meghívásának kezelése

A konzorciumi tagok meghívójának kezelési parancsmagokkal felügyelheti a konzorcium tagjainak meghívóit. Az elérhető műveletek a konzorciumi szerepkörtől függenek.

### <a name="new-blockchainmemberinvitation"></a>Új – BlockchainMemberInvitation

Ezzel a parancsmaggal meghívhatja az új tagokat a konzorciumba.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| SubscriptionId | A meghívni kívánt tag Azure-előfizetési azonosítója | Igen |
| Szerepkör | A konzorcium szerepköre. Az értékek lehetnek rendszergazda vagy felhasználó. A rendszergazda a konzorcium rendszergazdai szerepköre. A felhasználó a konzorciumi tag szerepkör. | Igen |
| Tagok | Import-ConsortiumManagementContracts által beszerzett tagok objektum | Igen |
| Web3Account | Import-Web3Account által beszerzett Web3Account objektum | Igen |
| Web3Client | A New-Web3Connection beszerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

[Hozzon létre egy Web3-kapcsolatot](#establish-a-web3-connection) a $ContractConnection és $MemberAccount változók beállításához.

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Ezzel a parancsmaggal lekérdezheti vagy listázhatja a konzorciumi tag Meghívási állapotát.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| SubscriptionId | A meghívni kívánt tag Azure-előfizetés azonosítója. Ha az előfizetés-azonosító van megadva, az előfizetés-azonosító meghívásának részleteit adja vissza. Ha az előfizetés-azonosító ki van hagyva, az összes tag meghívásának listáját adja vissza. | Nem |
| Tagok | Import-ConsortiumManagementContracts által beszerzett tagok objektum | Igen |
| Web3Client | A New-Web3Connection beszerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

[Hozzon létre egy Web3-kapcsolatot](#establish-a-web3-connection) a $ContractConnection változó beállításához.

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Példa kimenetre

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Ezzel a parancsmaggal visszavonhatja a konzorcium tagjainak meghívóját.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| SubscriptionId | A visszavonni kívánt tag Azure-előfizetési azonosítója | Igen |
| Tagok | Import-ConsortiumManagementContracts által beszerzett tagok objektum | Igen |
| Web3Account | Import-Web3Account által beszerzett Web3Account objektum | Igen |
| Web3Client | A New-Web3Connection beszerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

[Hozzon létre egy Web3-kapcsolatot](#establish-a-web3-connection) a $ContractConnection és $MemberAccount változók beállításához.

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Ezzel a parancsmaggal állíthatja be egy meglévő meghívás **szerepkörét** . Csak a konzorcium rendszergazdái módosíthatják a meghívókat.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| SubscriptionId | A meghívni kívánt tag Azure-előfizetési azonosítója | Igen |
| Szerepkör | Új konzorciumi szerepkör a meghívóhoz. Az értékek lehetnek **felhasználó** vagy **rendszergazda**. | Igen |
| Tagok |  Import-ConsortiumManagementContracts által beszerzett tagok objektum | Igen |
| Web3Account | Import-Web3Account által beszerzett Web3Account objektum | Igen |
| Web3Client | A New-Web3Connection beszerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

[Hozzon létre egy Web3-kapcsolatot](#establish-a-web3-connection) a $ContractConnection és $MemberAccount változók beállításához.

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Következő lépések

A konzorciumokkal, tagokkal és csomópontokkal kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [Azure Blockchain Service Consortium](consortium.md)
