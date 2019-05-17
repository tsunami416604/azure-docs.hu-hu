---
title: Az Azure Blockchain consortium szolgáltatásfelügyeleti PowerShell-lel
description: PowerShell-lel az Azure Blockchain-szolgáltatás consortium tagok kezelése
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: f15fa3b4972a2ac54d1d9bce916fdd42c2951d2f
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550885"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>A PowerShell-lel az Azure Blockchain-szolgáltatás consortium tagok kezelése

Az Azure Blockchain-szolgáltatás blockchain consortium tagok kezelése a PowerShell használatával is. Egy rendszergazdai jogosultságokkal rendelkező tagokat is meghívhat, hozzáadása, eltávolítása, és a szerepkörök a blockchain consortium minden részt semmilyen változást. Felhasználói jogosultsággal rendelkező tagjai tekintheti meg a blockchain consortium minden részt, és módosíthatja a tag megjelenítendő neve.

## <a name="prerequisites"></a>Előfeltételek

* [Az Azure portal használatával a blockchain-tag létrehozása](create-member.md)
* Konzorcium, a tagok és a csomópontok kapcsolatos további információkért lásd: [consortium Azure Blockchain-szolgáltatás](consortium.md)

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/powershell](https://shell.azure.com/powershell) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="install-powershell-module"></a>PowerShell-modul telepítése

Telepítse a Microsoft.AzureBlockchainService.ConsortiumManagement.PS csomagot a PowerShell-galériából.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-information-preference"></a>Set-információk szabályozó

További információkat szerezhet információkat preferenciaváltozó beállítás szerint a parancsmag végrehajtása közben. Alapértelmezés szerint *$InformationPreference* értékre van állítva *Folytatáscsendben*.

-Parancsmagokkal a részletesebb információért alábbiaknak megfelelők a szabályozó a PowerShellben:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Kapcsolat létrehozása a weben 3

Consortium tagok kezelése, az Azure Blockchain tag szolgáltatásvégpont weben 3 kapcsolatot létesíteni a kell. Ez a szkript használatával globális változókat a consortium felügyeleti parancsmagok hívásakor használható.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Cserélje le \<tag fiók jelszavának\> a tag fiók jelszavára, a tag létrehozásakor használt.

Az Azure Portalon keresse meg a többi érték:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg az alapértelmezett Azure Blockchain-szolgáltatás tag **áttekintése** lapot.

    ![Tag áttekintése](./media/manage-consortium-powershell/member-overview.png)

    Cserélje le \<tag fiókneve\>, és \<RootContract cím\> a portálon megadott értékekkel.

1. Válassza ki a végpont címe **tranzakció csomópontok** , és válassza ki a **alapértelmezett** tranzakció csomópont. Az alapértelmezett tranzakció csomópont van a neve megegyezik a blockchain-tag.
1. Válassza ki **kapcsolati karakterláncok**.

    ![Kapcsolati sztringek](./media/manage-consortium-powershell/connection-strings.png)

    Cserélje le \<végpont címe\> értékkel **HTTPS (hozzáférési kulcs: 1)** vagy **HTTPS (hozzáférési kulcs: 2)**.

## <a name="network-and-smart-contract-management"></a>Hálózati és az intelligens szerződés kezelése

A hálózati és az intelligens szerződés parancsmagok segítségével egy kapcsolatot a blockchain végpont az intelligens szerződések consortium kezeléséért felelős.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

A consortium felügyeleti intelligens szerződések tagok találhatóak a consortium kikényszerítéséhez és kezeléséhez használt csatlakozik.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| RootContractAddress | A legfelső szintű szerződés cím consortium felügyeleti intelligens szerződések | Igen |
| Web3Client | New-Web3Connection szerzett Web3Client objektum | Igen |

**Példa**

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Ez a parancsmag segítségével hozzon létre egy objektumot, amely tárolja az adatokat távoli csomópont felügyeleti fiókja.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Blockchain tag fiók címe | Igen |
| ManagedAccountPassword | Cím fiókjelszó | Igen |

**Példa**

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

A tranzakció csomópont az RPC-végpont egy kapcsolatot hoz létre.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Blockchain tag végpont címe | Igen |

**Példa**

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="consortium-member-management"></a>Consortium tag felügyeleti

Consortium tag felügyeleti parancsmagok segítségével kezelheti a tagok a consortium belül. Rendelkezésre álló műveletek consortium munkaköre függenek.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Tag adatai vagy lista tagjainak beolvasása.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| Name (Név) | Az Azure Blockchain-szolgáltatás tag szeretné beolvasni a részleteket a neve. Ha megad egy tag nevét, a rendszer a tag részleteit adja vissza. Ha a name nincs megadva, az összes consortium tagok listáját adja vissza. | Nem |
| Tagok | Importálás – ConsortiumManagementContracts származó tagok objektum | Igen |
| Web3Client | New-Web3Connection szerzett Web3Client objektum | Igen |

**Példa**

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

**Példa a kimenetre**

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Eltávolítja a blockchain tagja.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| Name (Név) | Tag neve eltávolítása | Igen |
| Tagok | Importálás – ConsortiumManagementContracts származó tagok objektum | Igen |
| Web3Account | Importálás – Web3Account szerzett Web3Account objektum | Igen |
| Web3Client | New-Web3Connection szerzett Web3Client objektum | Igen |

**Példa**

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Beállítja a blockchain tag attribútumai, beleértve a megjelenítendő név és consortium szerepkör.

Consortium rendszergazdák **DisplayName** és **szerepkör** összes tagjához. A felhasználói szerepkör tagjának Consortium csak módosíthatja a saját tag megjelenítendő neve.

`Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| Name (Név) | A blockchain tag neve | Igen |
| Megjelenítendő név | Új megjelenített név | Nem |
| AccountAddress | Fiók címe | Nem |
| Tagok | Importálás – ConsortiumManagementContracts származó tagok objektum | Igen |
| Web3Account | Importálás – Web3Account szerzett Web3Account objektum | Igen |
| Web3Client |  New-Web3Connection szerzett Web3Client objektum| Igen |

**Példa**

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="consortium-member-invitation-management"></a>Consortium tag meghívó kezelése

Consortium tag meghívó management-parancsmagok használatával consortium tag meghívók kezelése. Rendelkezésre álló műveletek consortium munkaköre függenek.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Meghívja a consortium új tagokat.

`New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| Előfizetés-azonosító | Meghívott tagok az Azure-előfizetés azonosítója | Igen |
| Szerepkör | Consortium szerepkör. Az értékek lehetnek a rendszergazda vagy felhasználó. A rendszergazda a consortium rendszergazdai szerepkör nem. FELHASZNÁLÓ a consortium szerepét. | Igen |
| Tagok | Importálás – ConsortiumManagementContracts származó tagok objektum | Igen |
| Web3Account | Importálás – Web3Account szerzett Web3Account objektum | Igen |
| Web3Client | New-Web3Connection szerzett Web3Client objektum | Igen |

**Példa**

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Beolvassa vagy listázza az consortium tag meghívó állapota.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| Előfizetés-azonosító | Meghívott tagok az Azure előfizetés-azonosítója. Ha az előfizetés-azonosító nincs megadva, a meghívás részleteit az előfizetés-Azonosítót adja vissza. Ha az előfizetés-azonosító nincs megadva, a rendszer az összes tag meghívók listáját adja vissza. | Nem |
| Tagok | Importálás – ConsortiumManagementContracts származó tagok objektum | Igen |
| Web3Client | New-Web3Connection szerzett Web3Client objektum | Igen |

**Példa**

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

**Példa a kimenetre**

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Egy tag consortium meghívás visszavonása.

`Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>`

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| Előfizetés-azonosító | Azure-előfizetés azonosítója tag visszavonása | Igen |
| Tagok | Importálás – ConsortiumManagementContracts származó tagok objektum | Igen |
| Web3Account | Importálás – Web3Account szerzett Web3Account objektum | Igen |
| Web3Client | New-Web3Connection szerzett Web3Client objektum | Igen |

**Példa**

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Beállítja a **szerepkör** meglévő meghívót. Csak a consortium rendszergazdák módosíthatják a meghívót.

`Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| Előfizetés-azonosító | Meghívott tagok az Azure-előfizetés azonosítója | Igen |
| Szerepkör | Meghívó új consortium szerepkör. Az értékek lehetnek **felhasználói** vagy **rendszergazda** | Igen |
| Tagok |  Importálás – ConsortiumManagementContracts származó tagok objektum | Igen |
| Web3Account | Importálás – Web3Account szerzett Web3Account objektum | Igen |
| Web3Client | New-Web3Connection szerzett Web3Client objektum | Igen |

**Példa**

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>További lépések

Konzorcium, a tagok és a csomópontok kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [Az Azure Blockchain-szolgáltatás consortium](consortium.md)