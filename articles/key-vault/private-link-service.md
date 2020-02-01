---
title: Integráció az Azure Private link Service szolgáltatással
description: Ismerje meg, hogyan integrálható Azure Key Vault az Azure Private link Service használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/28/2020
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: e058e643f4c37336f09b43c41cd09aa361a23d15
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907074"
---
# <a name="integrate-key-vault-with-azure-private-link-preview"></a>Key Vault integrálása az Azure Private Linktel (előzetes verzió)

Az Azure Private link Service lehetővé teszi az Azure-szolgáltatások (például az Azure Key Vault, az Azure Storage és a Azure Cosmos DB) és az Azure által üzemeltetett ügyfél-és partneri szolgáltatások elérését a virtuális hálózat privát végpontján keresztül.

Az Azure Private-végpontok olyan hálózati adapterek, amelyek az Azure Private-kapcsolaton keresztül csatlakoznak a szolgáltatáshoz. A privát végpont egy magánhálózati IP-címet használ a VNet, és hatékonyan hozza a szolgáltatást a VNet. A szolgáltatáshoz irányuló összes forgalom a privát végponton keresztül lesz irányítva, ezért nincs szükség átjárókra, NAT-eszközökre, ExpressRoute- vagy VPN-kapcsolatokra és nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Kapcsolódhat egy Azure-erőforrás egy példányához, amely a legmagasabb szintű részletességet nyújtja a hozzáférés-vezérlésben.

További információ: [Mi az az Azure Private link (előzetes verzió)?](../private-link/private-link-overview.md)

## <a name="prerequisites"></a>Előfeltételek

A Key Vault és az Azure Private link (előzetes verzió) integrálásához a következőkre lesz szüksége:

- Key Vault.
- Egy Azure-beli virtuális hálózat.
- Egy alhálózat a virtuális hálózaton.
- Tulajdonosi vagy közreműködői engedélyek a Key vaulthoz és a virtuális hálózathoz.

A privát végpontnak és a virtuális hálózatnak ugyanabban a régióban kell lennie. Ha a portál használatával kiválaszt egy régiót a privát végponthoz, akkor az automatikusan csak az adott régióban lévő virtuális hálózatokat fogja szűrni. A kulcstartó lehet egy másik régióban is.

A privát végpont egy magánhálózati IP-címet használ a virtuális hálózaton.

## <a name="establish-a-private-link-connection-to-key-vault"></a>Magánhálózati kapcsolat létesítése a Key vaulttal

Először hozzon létre egy virtuális hálózatot a [virtuális hálózat létrehozása a Azure Portal használatával](../virtual-network/quick-create-portal.md) című témakör lépéseit követve.

Ezután létrehozhat egy új kulcstartót, vagy létesíthet kapcsolatot egy meglévő kulcstartóval.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Új kulcstartó létrehozása és magánhálózati kapcsolat létesítése

Új kulcstartó létrehozásához kövesse a [titkos kód beállítása és lekérése Azure Key Vault használatával](quick-create-portal.md) című témakör lépéseit a Azure Portal

A Key Vault alapalapjainak konfigurálása után válassza a hálózatkezelés fület, és kövesse az alábbi lépéseket:

1. Válassza a privát végpont (előnézet) választógombot a hálózatkezelés lapon.
1. Privát végpont hozzáadásához kattintson a "+ Hozzáadás" gombra.

    ![Lemezkép](./media/private-link-service-1.png)
 
1. A privát végpont létrehozása panel "location" mezőjében válassza ki azt a régiót, amelyben a virtuális hálózat található. 
1. A "név" mezőben hozzon létre egy leíró nevet, amely lehetővé teszi a privát végpont azonosítását. 
1. Válassza ki azt a virtuális hálózatot és alhálózatot, amelyhez a privát végpontot létre kívánja hozni a legördülő menüből. 
1. Hagyja változatlanul az "integráció a saját DNS-zónával" beállítást.  
1. Válassza az OK gombot.

    ![Lemezkép](./media/private-link-service-2.png)
 
Ekkor megtekintheti a konfigurált privát végpontot. Most már lehetősége van a privát végpont törlésére és szerkesztésére. Válassza a "felülvizsgálat + létrehozás" gombot, és hozza létre a Key vaultot. A telepítés befejezéséhez 5-10 percet vesz igénybe. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Magánhálózati kapcsolat létesítése meglévő kulcstartóval

Ha már rendelkezik kulcstartóval, a következő lépések végrehajtásával hozhat létre privát kapcsolati kapcsolatot:

1. Jelentkezzen be az Azure portálra. 
1. A keresősáv mezőbe írja be a "Key Vaults" kifejezést.
1. Válassza ki a kulcstárolót a listából, amelyhez privát végpontot szeretne hozzáadni.
1. Válassza a "hálózatkezelés" fület a beállítások alatt.
1. Válassza ki a Private Endpoint Connections (előzetes verzió) fület az oldal tetején.
1. Válassza ki a "+ Private Endpoint" gombot az oldal tetején.

    ![rendszerkép](./media/private-link-service-3.png) ![rendszerkép](./media/private-link-service-4.png)

A panel használatával bármely Azure-erőforráshoz létrehozhat egy privát végpontot. A legördülő menük segítségével kiválaszthat egy erőforrástípust, és kiválaszthat egy erőforrást a címtárban, vagy bármely Azure-erőforráshoz kapcsolódhat erőforrás-azonosító használatával. Hagyja változatlanul az "integráció a saját DNS-zónával" beállítást.  

![rendszerkép](./media/private-link-service-3.png)
![rendszerkép](./media/private-link-service-4.png)

## <a name="manage-private-link-connection"></a>Privát kapcsolat kapcsolatának kezelése

Privát végpont létrehozásakor jóvá kell hagyni a kapcsolódást. Ha az erőforrás, amelyhez privát végpontot hoz létre, akkor jóváhagyhatja a megfelelő engedélyekkel rendelkező kapcsolódási kérelmet. Ha egy másik címtárban található Azure-erőforráshoz csatlakozik, meg kell várnia, hogy az erőforrás tulajdonosa jóváhagyja a kapcsolódási kérelmet.

Négy kiépítési állapot létezik:

| Szolgáltatás-nyújtási művelet | A szolgáltatás fogyasztói magánhálózati végpontjának állapota | Leírás |
|--|--|--|
| None | Függőben | A kapcsolat manuálisan lett létrehozva, és jóváhagyásra vár a Private link erőforrás-tulajdonostól. |
| Jóváhagyás | Approved | A kapcsolódás automatikusan vagy manuálisan lett jóváhagyva, és készen áll a használatra. |
| Elutasítás | Elutasította | A magánhálózati kapcsolat erőforrásának tulajdonosa elutasította a kapcsolatot. |
| Eltávolítás | Leválasztott | A kapcsolatot a privát kapcsolat erőforrás-tulajdonosa eltávolította, a magánhálózati végpont informatív lesz, és törölni kell a tisztításhoz. |
 
###  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault"></a>Privát végponti kapcsolatok kezelése a Key vaulttal

1. Jelentkezzen be az Azure Portalra.
1. A keresősáv mezőbe írja be a "Key Vaults" kifejezést.
1. Válassza ki a kezelni kívánt kulcstartót.
1. Válassza a "hálózatkezelés" fület.
1. Ha van függőben lévő kapcsolat, a kiépítési állapotban "függőben" állapotú kapcsolat jelenik meg. 
1. Válassza ki a jóváhagyni kívánt privát végpontot
1. Kattintson a jóváhagyás gombra.
1. Ha van olyan privát végponti kapcsolat, amelyet el szeretne utasítani, legyen az egy függőben lévő kérelem vagy létező kapcsolat, válassza ki a kapcsolatot, és kattintson az "elutasítás" gombra.

    ![Lemezkép](./media/private-link-service-7.png)

## <a name="validate-that-the-private-link-connection-works"></a>Annak ellenőrzése, hogy a magánhálózati kapcsolat működik-e

Ellenőriznie kell, hogy a magánhálózati végpont erőforrásának ugyanazon alhálózatán belüli erőforrásai csatlakoznak-e a kulcstartóhoz egy magánhálózati IP-címen keresztül, és hogy rendelkeznek-e a megfelelő magánhálózati DNS-zónák integrálásával.

Először hozzon létre egy virtuális gépet a [Windows rendszerű virtuális gép létrehozása a Azure Portalban](../virtual-machines/windows/quick-create-portal.md) című témakör lépéseit követve.

A "hálózatkezelés" lapon:

1. Határozza meg a virtuális hálózatot és az alhálózatot. Létrehozhat egy új virtuális hálózatot, vagy kijelölhet egy meglévőt is. Ha egy meglévőt választ ki, győződjön meg arról, hogy a régió megfelel.
1. Nyilvános IP-erőforrást kell megadni.
1. A "NIC hálózati biztonsági csoport" területen válassza a "nincs" lehetőséget.
1. A "terheléselosztás" területen válassza a "nem" lehetőséget.

Nyissa meg a parancssort, és futtassa a következő parancsot:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

Ha a Key Vault IP-címének nyilvános végponton keresztüli feloldásához futtatja az NS lookup parancsot, a következőhöz hasonló eredményt fog látni:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

Ha a Key Vault IP-címének feloldásához egy privát végponton futtatja az NS lookup parancsot, a következőhöz hasonló eredményt fog látni:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="limitations-and-design-considerations"></a>Korlátozások és kialakítási szempontok

**Díjszabás**: díjszabási információkért tekintse meg az [Azure Private link (előzetes verzió) díjszabását](https://azure.microsoft.com/pricing/details/private-link/).

**Korlátozások**: a Azure Key Vault magánhálózati végpontja nyilvános előzetes verzióban érhető el. Ez a funkció az összes Azure-beli nyilvános régióban elérhető.

**Privát végpontok maximális száma Key Vault**: 64.

**A privát végpontok által előfizetett kulcstartók maximális száma**: 64.

További információ [: Azure Private link Service: korlátozások](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure Private linkről (előzetes verzió)](../private-link/private-link-service-overview.md)
- További információ a [Azure Key Vault](key-vault-overview.md)
