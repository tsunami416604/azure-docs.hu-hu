---
title: Integrálás az Azure Private Link szolgáltatással
description: Ismerje meg, hogyan integrálható az Azure Key Vault az Azure Private Link szolgáltatással
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 03/08/2020
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.openlocfilehash: 678e91126c04d5b299d9234a1602580260c5aee6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425090"
---
# <a name="integrate-key-vault-with-azure-private-link"></a>A Key Vault integrálása az Azure Private Linkkel

Az Azure Private Link Service lehetővé teszi az Azure-szolgáltatások (például az Azure Key Vault, az Azure Storage és az Azure Cosmos DB) és az Azure által üzemeltetett ügyfél-/partnerszolgáltatások elérését a virtuális hálózat privát végpontján keresztül.

Az Azure Private Endpoint egy olyan hálózati felület, amely privát és biztonságos kapcsolatot biztosít az Azure Private Link által működtetett szolgáltatással. A privát végpont egy privát IP-címet használ a virtuális hálózatból, hatékonyan hozza a szolgáltatást a virtuális hálózatba. A szolgáltatáshoz irányuló összes forgalom átirányítható a privát végponton keresztül, így nincs szükség átjárókra, NAT-eszközökre, ExpressRoute- vagy VPN-kapcsolatokra vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Csatlakozhat egy Azure-erőforrás egy példányához, így a legmagasabb szintű részletességet biztosíthatja a hozzáférés-vezérlésben.

További információ: [Mi az Azure Private Link?](../../private-link/private-link-overview.md)

## <a name="prerequisites"></a>Előfeltételek

Ha integrálni szeretne egy kulcstartót az Azure Private Linkkel, a következőkre lesz szüksége:

- Egy kulcstartó.
- Egy Azure virtuális hálózat.
- A virtuális hálózat alhálózata.
- Tulajdonosi vagy közreműködői engedélyek mind a key vault és a virtuális hálózat.

A privát végpontnak és a virtuális hálózatnak ugyanabban a régióban kell lennie. Amikor kiválaszt egy régiót a privát végponthoz a portál használatával, automatikusan csak az adott régióban lévő virtuális hálózatokat szűri. A kulcstartó lehet egy másik régióban.

A privát végpont egy privát IP-címet használ a virtuális hálózatban.

## <a name="establish-a-private-link-connection-to-key-vault-using-the-azure-portal"></a>Privát kapcsolat létrehozása a Key Vaultszolgáltatással az Azure Portalhasználatával 

Először hozzon létre egy virtuális hálózatot a Virtuális hálózat létrehozása az Azure Portal használatával című részben leírt lépések [végrehajtásával](../../virtual-network/quick-create-portal.md)

Ezután létrehozhat egy új kulcstartót, vagy létrehozhat egy privát kapcsolat kapcsolatot egy meglévő kulcstartóval.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Új kulcstartó létrehozása és privát kapcsolat létrehozása

Új key Vault-ot hozhat létre a Set című csoportlépései és [az Azure Key Vault titkos adatainak beolvasása az Azure Portalon keresztül.](../secrets/quick-create-portal.md)

A kulcstartó alapjainak konfigurálása után válassza a Hálózat lapot, és kövesse az alábbi lépéseket:

1. A Hálózat lapon válassza a Privát végpont választógombot.
1. Privát végpont hozzáadásához kattintson a "+ hozzáadás" gombra.

    ![Kép](../media/private-link-service-1.png)
 
1. A Privát végpont létrehozása panel "Hely" mezőjében válassza ki azt a régiót, amelyben a virtuális hálózat található. 
1. A "Név" mezőben hozzon létre egy leíró nevet, amely lehetővé teszi a személyes végpont azonosítását. 
1. Válassza ki azt a virtuális hálózatot és alhálózatot, amelyben létre szeretné hozni ezt a privát végpontot a legördülő menüből. 
1. Hagyja változatlanul a "Integrálás a privát zóna DNS-ével" lehetőséget.  
1. Válassza az "Ok" lehetőséget.

    ![Kép](../media/private-link-service-8.png)
 
Most már láthatja a konfigurált privát végpontot. Most már törölheti és szerkesztheti ezt a privát végpontot. Válassza a "Véleményezés + Létrehozás" gombot, és hozza létre a key vaultot. A telepítés 5-10 percet vesz igénybe. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Privát kapcsolat létrehozása meglévő kulcstartóval

Ha már rendelkezik key vault, hozhat létre egy privát kapcsolat kapcsolatot az alábbi lépésekkel:

1. Jelentkezzen be az Azure portálra. 
1. A keresősávba írja be a "key vaults" (kulcstartók) kifejezést.
1. Válassza ki a key vault a listából, amelyhez hozzá szeretne adni egy privát végpontot.
1. Válassza a "Hálózat" lapot a Beállítások csoportban
1. A lap tetején a Magánjellegű végpontkapcsolatok fülének kijelölése
1. A lap tetején válassza a "+ privát végpont" gombot.

    ![Kép](../media/private-link-service-3.png) ![](../media/private-link-service-4.png)

Dönthet úgy, hogy hozzon létre egy privát végpont ot bármely Azure-erőforrás ezzel a panel használatával. Használhatja a legördülő menük, hogy válasszon ki egy erőforrástípust, és válasszon ki egy erőforrást a címtárban, vagy csatlakozhat bármely Azure-erőforrás egy erőforrás-azonosító használatával. Hagyja változatlanul a "Integrálás a privát zóna DNS-ével" lehetőséget.  

![Kép](../media/private-link-service-3.png)
![](../media/private-link-service-4.png)

## <a name="establish-a-private-link-connection-to-key-vault-using-cli"></a>Privát kapcsolat létrehozása a Key Vaulttal a CLI használatával

### <a name="login-to-azure-cli"></a>Bejelentkezés az Azure CLI-be
```console
az login 
```
### <a name="select-your-azure-subscription"></a>Válassza ki Azure-előfizetését 
```console
az account set --subscription {AZURE SUBSCRIPTION ID}
```
### <a name="create-a-new-resource-group"></a>Új erőforráscsoport létrehozása 
```console
az group create -n {RG} -l {AZURE REGION}
```
### <a name="register-microsoftkeyvault-as-a-provider"></a>A Microsoft.KeyVault regisztrálása szolgáltatóként 
```console
az provider register -n Microsoft.KeyVault
```
### <a name="create-a-new-key-vault"></a>Új kulcstartó létrehozása
```console
az keyvault create --name {KEY VAULT NAME} --resource-group {RG} --location {AZURE REGION}
```
### <a name="turn-on-key-vault-firewall"></a>A Key Vault tűzfalának bekapcsolása
```console
az keyvault update --name {KEY VAULT NAME} --resource-group {RG} --location {AZURE REGION} --default-action deny
```
### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
```console
az network vnet create --resource-group {RG} --name {vNet NAME} --location {AZURE REGION}
```
### <a name="add-a-subnet"></a>Alhálózat hozzáadása
```console
az network vnet subnet create --resource-group {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}
```
### <a name="disable-virtual-network-policies"></a>Virtuális hálózati házirendek letiltása 
```console
az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
```
### <a name="add-a-private-dns-zone"></a>Privát DNS-zóna hozzáadása 
```console
az network private-dns zone create --resource-group {RG} --name privatelink.vaultcore.azure.net
```
### <a name="link-private-dns-zone-to-virtual-network"></a>Privát DNS-zóna csatolása virtuális hálózathoz 
```console
az network private-dns link vnet create --resoruce-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.vaultcore.azure.net --name {dnsZoneLinkName} --registration-enabled true
```
### <a name="create-a-private-endpoint-automatically-approve"></a>Privát végpont létrehozása (automatikus jóváhagyás) 
```console
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/ {KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION}
```
### <a name="create-a-private-endpoint-manually-request-approval"></a>Saját végpont létrehozása (manuális kérelem jóváhagyása) 
```console
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/ {KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```
### <a name="show-connection-status"></a>Kapcsolat állapotának megjelenítése 
```console
az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
```
## <a name="manage-private-link-connection"></a>Privát kapcsolat kezelése

Privát végpont létrehozásakor a kapcsolatot jóvá kell hagyni. Ha az az erőforrás, amelyhez privát végpontot hoz létre, a címtárban található, jóváhagyhatja a csatlakozási kérelmet, feltéve, hogy rendelkezik a megfelelő engedélyekkel; ha egy másik címtárban csatlakozik egy Azure-erőforráshoz, meg kell várnia, amíg az erőforrás tulajdonosa jóváhagyja a csatlakozási kérelmet.

Négy kiépítési állapot létezik:

| A szolgáltatás műveletet biztosít | Szolgáltatás fogyasztói privát végpont állapota | Leírás |
|--|--|--|
| None | Függőben | A kapcsolat manuálisan jön létre, és a Magánkapcsolat erőforrás tulajdonosának jóváhagyása vár. |
| Jóváhagyás | Approved | A kapcsolat automatikusan vagy manuálisan lett jóváhagyva, és készen áll a használatra. |
| Elutasítás | Elutasítva | A kapcsolatot a magánkapcsolati erőforrás tulajdonosa elutasította. |
| Eltávolítás | Elválasztott | A kapcsolatot a magánkapcsolati erőforrás tulajdonosa eltávolította, a privát végpont informatívsá válik, és törlésre kell törölni. |
 
###  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-the-azure-portal"></a>Privát végpontkapcsolat kezelése a Key Vaultszolgáltatással az Azure Portalhasználatával 

1. Jelentkezzen be az Azure Portalra.
1. A keresősávba írja be a "key vaults" (kulcstartók) kifejezést.
1. Válassza ki a kezelni kívánt kulcstartót.
1. Válassza a "Hálózat" lapot.
1. Ha vannak függőben lévő kapcsolatok, a létesítési állapotban megjelenik a "Függőben" listában szereplő kapcsolat. 
1. Válassza ki a jóváhagyni kívánt privát végpontot.
1. Válassza ki a jóváhagyás gombot.
1. Ha vannak olyan privát végpontkapcsolatok, amelyeket el szeretne utasítani, függetlenül attól, hogy függőben lévő kérelemről vagy meglévő kapcsolatról van-e szó, jelölje ki a kapcsolatot, és kattintson az "Elutasítás" gombra.

    ![Kép](../media/private-link-service-7.png)

##  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-azure-cli"></a>Privát végpontkapcsolat kezelése a Key Vaulthoz az Azure CLI használatával

### <a name="approve-a-private-link-connection-request"></a>Privát kapcsolati kapcsolatkérés jóváhagyása
```console
az keyvault private-endpoint-connection approve --approval-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}
```

### <a name="deny-a-private-link-connection-request"></a>Privát kapcsolati kapcsolatkérés megtagadása
```console
az keyvault private-endpoint-connection reject --rejection-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}
```

### <a name="delete-a-private-link-connection-request"></a>Privát kapcsolati kapcsolatkérelem törlése
```console
az keyvault private-endpoint-connection delete --resource-group {RG} --vault-name {KEY VAULT NAME} --name {PRIVATE LINK CONNECTION NAME}
```

## <a name="validate-that-the-private-link-connection-works"></a>Annak ellenőrzése, hogy a privát kapcsolat kapcsolata működik-e

Ellenőrizze, hogy a privát végponterőforrás ugyanazon alhálózatán belüli erőforrások privát IP-címen keresztül csatlakoznak-e a kulcstartóhoz, és hogy rendelkeznek-e a megfelelő privát DNS-zóna integrációval.

Először hozzon létre egy virtuális gépet a Windows virtuális gép létrehozása az [Azure Portalon](../../virtual-machines/windows/quick-create-portal.md) című csoportban leírt lépések végrehajtásával

A "Hálózat" lapon:

1. Adja meg a virtuális hálózatot és az alhálózatot. Létrehozhat egy új virtuális hálózatot, vagy kiválaszthat egy meglévőt. Ha egy meglévőt választ, győződjön meg arról, hogy a régió egyezik.If selecting an existing one, make sure the region matches.
1. Adjon meg egy nyilvános IP-erőforrást.
1. A "Hálózati adapter hálózati biztonsági csoportjában" válassza a "Nincs" lehetőséget.
1. A "Terheléselosztás" területen válassza a "Nem" lehetőséget.

Nyissa meg a parancssort, és futtassa a következő parancsot:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

Ha az ns lookup parancsot futtatja a kulcstartó IP-címének nyilvános végponton való feloldásához, a következő höz hasonló eredmény jelenik meg:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

Ha az ns lookup parancsot futtatja a kulcstartó IP-címének feloldásához egy privát végponton keresztül, a következő höz hasonló eredmény jelenik meg:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="limitations-and-design-considerations"></a>Korlátozások és tervezési szempontok

**Díjszabás:** A díjszabási információkért lásd: [Azure Private Link díjszabása.](https://azure.microsoft.com/pricing/details/private-link/)

**Korlátozások:** Az Azure Key Vault privát végpontja csak az Azure nyilvános régióiban érhető el.

**A privát végpontok maximális száma key vaultonként:** 64.

**Előfizetésenként privát végponttal rendelkező kulcstartók maximális száma:** 64.

További információkért lásd: [Azure Private Link szolgáltatás: Korlátozások](../../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure Private Linkről](../../private-link/private-link-service-overview.md)
- További információ az [Azure Key Vaultról](overview.md)
