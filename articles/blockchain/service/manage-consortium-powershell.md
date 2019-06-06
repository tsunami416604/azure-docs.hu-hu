---
title: Az Azure Blockchain-szolgáltatás consortium tagjait kezelheti az Azure PowerShell-lel
description: Ismerje meg az Azure Blockchain-szolgáltatás consortium tagok kezelése az Azure PowerShell használatával.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 4bb72bc3fe8b85a8d2aed88e02f5f3150abb6899
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66493667"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-by-using-powershell"></a>Az Azure Blockchain-szolgáltatás consortium tagok kezelése a PowerShell használatával

Az Azure Blockchain-szolgáltatás blockchain consortium tagok kezelése a PowerShell használatával is. Rendszergazdai jogosultságokkal rendelkező tagokat meghívása, hozzáadása, távolítsa el, és módosítsa a szerepkörök a blockchain consortium minden résztvevője számára. Felhasználói jogosultságokkal rendelkező tagokat a blockchain consortium minden résztvevője megtekinthetik és módosíthatják a tag megjelenítendő neve.

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy blockchain tag a [az Azure portal](create-member.md).
* Konzorcium, a tagok és a csomópontok kapcsolatos további információkért lásd: [Azure Blockchain-szolgáltatás consortium](consortium.md).

## <a name="open-azure-cloud-shell"></a>Az Azure Cloud Shell megnyitása

Az Azure Cloud Shell olyan ingyenes interaktív kezelőfelület, amely ebben a cikkben található lépések futtatására használhat is. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

Is megnyithatja a Cloud Shell egy külön böngészőlapon a [shell.azure.com/powershell](https://shell.azure.com/powershell). Válassza ki **másolási** szeretné másolni a kódblokkok, illessze be a Cloud Shell, és válassza ki **Enter** a futtatáshoz.

## <a name="install-the-powershell-module"></a>A PowerShell-modul telepítése

Telepítse a Microsoft.AzureBlockchainService.ConsortiumManagement.PS csomagot a PowerShell-galériából.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Állítsa be az adatokat szabályozó

További információkat szerezhet az információk preferenciaváltozó beállításával a parancsmagok végrehajtása közben. Alapértelmezés szerint *$InformationPreference* értékre van állítva *Folytatáscsendben*.

-Parancsmagokkal a részletesebb információért alábbiaknak megfelelők a szabályozó a PowerShellben:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Kapcsolat létrehozása a weben 3

Consortium tagjait kezelheti, a Blockchain szolgáltatásvégpont tag weben 3 kapcsolatot létesíteni. Ez a szkript használatával a consortium felügyeleti parancsmagok meghívására szolgáló globális változókat.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Cserélje le *\<tag fiók jelszava\>* a tag fiók jelszavára, amelyet a tag létrehozásakor használt.

Az Azure Portalon keresse meg a többi érték:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Nyissa meg az alapértelmezett Blockchain-szolgáltatás tag **áttekintése** lapot.

    ![Tag áttekintése](./media/manage-consortium-powershell/member-overview.png)

    Cserélje le *\<tag fiókneve\>* és *\<RootContract cím\>* a portálon megadott értékekkel.

1. Válassza ki a végpont címe **tranzakció csomópontok**, majd válassza ki a **alapértelmezett tranzakció csomópont**. Az alapértelmezett csomópont van a neve megegyezik a blockchain tag.
1. Válassza ki **kapcsolati karakterláncok**.

    ![Kapcsolati sztringek](./media/manage-consortium-powershell/connection-strings.png)

    Cserélje le *\<végpont címe\>* értékkel **HTTPS (hozzáférési kulcs: 1)** vagy **HTTPS (hozzáférési kulcs: 2)** .

## <a name="manage-the-network-and-smart-contracts"></a>A hálózati és az intelligens szerződések kezelése

A hálózati és az intelligens szerződés parancsmagok segítségével egy kapcsolatot a blockchain-végpont az intelligens szerződések consortium kezeléséért felelős.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Ez a parancsmag használatával a consortium felügyeleti intelligens szerződések csatlakozhat. Ezek a szerződések tagok találhatóak a consortium kikényszerítéséhez és kezeléséhez használhatók.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| RootContractAddress | Legfelső szintű szerződés címe consortium felügyeleti intelligens szerződések | Igen |
| Web3Client | New-Web3Connection szerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Ez a parancsmag segítségével hozzon létre egy objektumot a távoli csomópont felügyeleti fiók adatainak tárolására.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Blockchain tag fiók címe | Igen |
| ManagedAccountPassword | Cím fiókjelszó | Igen |

#### <a name="example"></a>Példa

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

Ez a parancsmag használatával egy kapcsolatot az RPC-végpont a tranzakció csomópont.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Blockchain tag végpont címe | Igen |

#### <a name="example"></a>Példa

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>A consortium tagok kezelése

Consortium tag felügyeleti parancsmagok segítségével kezelheti a tagok a consortium belül. A rendelkezésre álló műveletek consortium munkaköre függenek.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Ez a parancsmag használatával részletek a tag vagy tagjainak listázása.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| Name (Név) | A Blockchain-szolgáltatás tag kapcsolatos részleteket lekérni kívánt nevét. Ha egy név van megadva, a tag adatait adja vissza. Ha a name nincs megadva, az összes consortium tagok listáját adja vissza. | Nem |
| A tagok | Importálás – ConsortiumManagementContracts származó tagok objektum | Igen |
| Web3Client | New-Web3Connection szerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>Példa a kimenetre

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Ez a parancsmag használatával a blockchain-tag eltávolítása.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| Name (Név) | Tag neve eltávolítása | Igen |
| A tagok | Importálás – ConsortiumManagementContracts származó tagok objektum | Igen |
| Web3Account | Importálás – Web3Account szerzett Web3Account objektum | Igen |
| Web3Client | New-Web3Connection szerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Ez a parancsmag segítségével beállíthatja a blockchain tag attribútumai, beleértve a megjelenített nevét és a consortium szerepkör.

Consortium rendszergazdák **DisplayName** és **szerepkör** összes tagjához. A felhasználói szerepkör tagjának consortium módosíthatja csak a saját tag megjelenítendő neve.

`Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| Name (Név) | A blockchain tag neve | Igen |
| displayName | Új megjelenített név | Nem |
| AccountAddress | Fiók címe | Nem |
| A tagok | Importálás – ConsortiumManagementContracts származó tagok objektum | Igen |
| Web3Account | Importálás – Web3Account szerzett Web3Account objektum | Igen |
| Web3Client |  New-Web3Connection szerzett Web3Client objektum| Igen |

#### <a name="example"></a>Példa

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>A consortium tagok meghívók kezelése

A consortium tag meghívó felügyeleti parancsmagok használatával consortium tagok meghívók kezelése. A rendelkezésre álló műveletek consortium munkaköre függenek.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Ez a parancsmag használatával új tagok számára a consortium meghívása.

`New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Paraméter | Leírás | Szükséges |
|-----------|-------------|:--------:|
| SubscriptionId | A tag meghívása az Azure-előfizetés azonosítója | Igen |
| Szerepkör | A consortium szerepkör. Az értékek lehetnek a rendszergazda vagy felhasználó. A rendszergazda a consortium rendszergazdai szerepkör nem. FELHASZNÁLÓ a consortium szerepét. | Igen |
| A tagok | Importálás – ConsortiumManagementContracts származó tagok objektum | Igen |
| Web3Account | Importálás – Web3Account szerzett Web3Account objektum | Igen |
| Web3Client | New-Web3Connection szerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Ez a parancsmag használatával beolvasása vagy listázása egy consortium tag meghívó állapota.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| SubscriptionId | A tag meghívása az Azure előfizetés-azonosítója. Ha az előfizetés-azonosító nincs megadva, a adja vissza, az előfizetés-azonosítók a meghívás részletei. Ha az előfizetés-azonosító, a rendszer az összes tag meghívók listáját adja vissza. | Nem |
| A tagok | Importálás – ConsortiumManagementContracts származó tagok objektum | Igen |
| Web3Client | New-Web3Connection szerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Példa a kimenetre

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Ez a parancsmag használatával consortium tagja meghívás visszavonása.

`Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>`

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-előfizetési Azonosítóját a tag visszavonása | Igen |
| A tagok | Importálás – ConsortiumManagementContracts származó tagok objektum | Igen |
| Web3Account | Importálás – Web3Account szerzett Web3Account objektum | Igen |
| Web3Client | New-Web3Connection szerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Ez a parancsmag segítségével beállíthatja a **szerepkör** meglévő meghívót. Csak a consortium rendszergazdák módosíthatják a meghívót.

`Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Paraméter | Leírás | Kötelező |
|-----------|-------------|:--------:|
| SubscriptionId | A tag meghívása az Azure-előfizetés azonosítója | Igen |
| Szerepkör | Meghívó új consortium szerepkör. Az értékek lehetnek **felhasználói** vagy **rendszergazdai**. | Igen |
| A tagok |  Importálás – ConsortiumManagementContracts származó tagok objektum | Igen |
| Web3Account | Importálás – Web3Account szerzett Web3Account objektum | Igen |
| Web3Client | New-Web3Connection szerzett Web3Client objektum | Igen |

#### <a name="example"></a>Példa

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>További lépések

Konzorcium, a tagok és a csomópontok kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [Az Azure Blockchain-szolgáltatás consortium](consortium.md)
