---
title: Azure nyilvános IP-címelőtag létrehozása, módosítása vagy törlése
titlesuffix: Azure Virtual Network
description: További információ nyilvános IP-címelőtag létrehozásáról, módosításáról és törléséről.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2019
ms.author: anavin
ms.openlocfilehash: 26d8ee34c735cab8f1033a9aad897ec0b1bed524
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65952681"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Nyilvános IP-címelőtag létrehozása, módosítása vagy törlése

Információ a nyilvános IP-cím előtagról, valamint az adatok létrehozásáról, módosításáról és törléséről. A nyilvános IP-cím előtag a megadott nyilvános IP-címek számán alapuló összefüggő címtartomány. A címek az előfizetéshez vannak rendelve. Nyilvános IP-címerőforrás létrehozásakor statikus nyilvános IP-címet rendelhet az előtagból, és a címet virtuális gépekhez, terheléselosztókhoz vagy más erőforrásokhoz társíthatja az internetkapcsolat engedélyezéséhez. Ha nem ismeri a nyilvános IP-címelőtagokat, olvassa el a [Nyilvános IP-cím előtag áttekintése című témakört.](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A cikk bármely részében ismertetett lépések végrehajtása előtt hajtsa végre a következő feladatokat:

- Ha még nem rendelkezik Azure-fiókkal, regisztráljon egy [ingyenes próbafiókot.](https://azure.microsoft.com/free)
- Ha a portált https://portal.azure.comhasználja, nyissa meg a, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsok at feladatok elvégzéséhez ebben a cikkben, vagy futtassa a parancsokat az [Azure Cloud Shell,](https://shell.azure.com/powershell)vagy a PowerShell futtatásával a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ez az oktatóanyag az Azure PowerShell-modul 1.0.0-s vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha az Azure parancssori felület (CLI) parancsokkal hajthatja végre a cikkben szereplő feladatokat, futtassa a parancsokat az [Azure Cloud Shellben,](https://shell.azure.com/bash)vagy a CLI futtatásával a számítógépről. Ez az oktatóanyag az Azure CLI 2.0.41-es vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha az Azure CLI helyileg fut, akkor `az login` is kell futtatnia, hogy hozzon létre egy kapcsolatot az Azure-ral.

A fiók, amelybe bejelentkezik, vagy az Azure-hoz csatlakozik, hozzá kell rendelnie a [hálózati közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [olyan egyéni szerepkörhöz,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) amely az [Engedélyek](#permissions)listában felsorolt megfelelő műveletekhez van rendelve.

A nyilvános IP-cím előtagok díja van. További információt az [árképzés ben](https://azure.microsoft.com/pricing/details/ip-addresses)talál.

## <a name="create-a-public-ip-address-prefix"></a>Nyilvános IP-címelőtag létrehozása

1. A portál bal felső sarkában válassza a **+ Erőforrás létrehozása**lehetőséget.
2. Adja meg a *nyilvános IP-cím előtagot* a *Keresés a piactéren* mezőbe. Amikor **a nyilvános IP-cím előtagja** megjelenik a keresési eredmények között, jelölje ki azt.
3. A **Nyilvános IP-cím előtagja**csoportban válassza a **Létrehozás lehetőséget.**
4. Írja be vagy jelölje ki a következő beállítások értékeit a **Nyilvános IP-címelőtag létrehozása**csoportban, majd válassza a **Létrehozás lehetőséget:**

   |Beállítás|Kötelező?|Részletek|
   |---|---|---|
   |Előfizetés|Igen|Ugyanebben az [előfizetésben](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) kell léteznie, mint az az erőforrás, amelyhez a nyilvános IP-címet társítani szeretné.|
   |Erőforráscsoport|Igen|A nyilvános IP-címet társítani kívánt erőforrással azonos vagy eltérő [erőforráscsoportban](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) is létezhet.|
   |Név|Igen|A névnek egyedinek kell lennie a kiválasztott erőforráscsoporton belül.|
   |Régió|Igen|Ugyanabban a [régióban](https://azure.microsoft.com/regions)kell léteznie, mint a tartományból hozzárendelt nyilvános IP-címeknek.|
   |Előtag mérete|Igen| A szükséges előtag mérete. Az alapértelmezett 0,28 vagy 16 IP-cím.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az hálózati nyilvános ip előtag létrehozása](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[Új-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Statikus nyilvános IP-cím létrehozása előtagból
Miután létrehozott egy előtagot, statikus IP-címeket kell létrehoznia az előtagból. Ehhez kövesse az alábbi lépéseket.

1. Az Azure Portal tetején található *Keresési erőforrások* at tartalmazó mezőbe írja be a *nyilvános IP-cím előtagot.* Ha **nyilvános IP-cím előtagok** jelennek meg a keresési eredmények között, jelölje ki azt.
2. Válassza ki azt az előtagot, amelyből nyilvános IP-ket szeretne létrehozni.
3. Amikor megjelenik a keresési eredmények között, jelölje ki, és kattintson az Áttekintő szakasz **+IP-cím hozzáadása** gombjára.
4. Adja meg vagy jelölje ki a következő beállítások értékeit a **Nyilvános IP-cím létrehozása csoportban.** Mivel az előtag a szabványos termékváltozathoz, az IPv4-hez és a statikus hoz, csak a következő adatokat kell megadnia:

   |Beállítás|Kötelező?|Részletek|
   |---|---|---|
   |Név|Igen|A nyilvános IP-cím nevének egyedinek kell lennie a kiválasztott erőforráscsoporton belül.|
   |Tétlen időelés (perc)|Nem|Hány perc ig kell nyitva tartani a TCP- vagy HTTP-kapcsolatot anélkül, hogy az ügyfelekre támaszkodna az életben maradó üzenetek küldéséhez. |
   |DNS-névcímke|Nem|Egyedinek kell lennie az Azure-régióban, amelyben létrehozza a nevet (az összes előfizetésben és az összes ügyfélnél). Az Azure automatikusan regisztrálja a nevet és az IP-címet a DNS-ben, így csatlakozhat egy erőforráshoz a névvel. Az Azure hozzáfűzegy alapértelmezett alhálózatot, például *location.cloudapp.azure.com* (ahol a hely a kiválasztott hely) a megadott névhez, a teljesen minősített DNS-név létrehozásához. További információ: [Azure DNS használata Azure nyilvános IP-címmel.](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)|

Másik lehetőségként használhatja az alábbi CLI és PS parancsokat a --public-ip-előtag (CLI) és a -PublicIpPrefix (PS) paraméterekkel nyilvános IP-cím erőforrás létrehozásához. 

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)|
|PowerShell|[Új-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=azps-2.0.0)|

## <a name="view-or-delete-a-prefix"></a>Előtag megtekintése vagy törlése

1. Az Azure Portal tetején található *Keresési erőforrások* at tartalmazó mezőbe írja be a *nyilvános IP-cím előtagot.* Ha **nyilvános IP-cím előtagok** jelennek meg a keresési eredmények között, jelölje ki azt.
2. Válassza ki annak a nyilvános IP-címelőtagnak a nevét, amelyet meg szeretne tekinteni, módosítani vagy törölni szeretné a listából.
3. Hajtsa végre az alábbi lehetőségek egyikét, attól függően, hogy meg szeretné-e tekinteni, törölni vagy módosítani szeretné a nyilvános IP-cím előtagot.
   - **Nézet**: **Az Áttekintés** szakasz a nyilvános IP-cím előtagja, például az előtag kulcsbeállításait jeleníti meg.
   - **Törlés**: A nyilvános IP-címelőtag törléséhez válassza a **Törlés** lehetőséget az **Áttekintés** szakaszban. Ha az előtagban lévő címek nyilvános IP-cím-erőforrásokhoz vannak társítva, először törölnie kell a nyilvános IP-cím erőforrásokat. Lásd [nyilvános IP-cím törlése](virtual-network-public-ip-address.md#view-change-settings-for-or-delete-a-public-ip-address).

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az hálózati nyilvános ip előtag lista](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) a nyilvános IP-címek listázásához, [az hálózati nyilvános ip előtag a](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) beállítások megjelenítéséhez; [frissítésre való hálózati nyilvános ip előtag frissítése;](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) [az hálózati nyilvános ip előtag törlése](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) a törléshez|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) egy nyilvános IP-cím objektum lekéréséhez és a beállítások megtekintéséhez, [Set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) a beállítások frissítéséhez; A törölni kívánt [remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix)|

## <a name="permissions"></a>Engedélyek

A nyilvános IP-címelőtagokkal kapcsolatos feladatok végrehajtásához a fiókot hozzá kell rendelni a [hálózati közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [olyan egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkörhöz, amely az alábbi táblázatban felsorolt megfelelő műveletekhez van rendelve:

| Műveletek                                                            | Név                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Nyilvános IP-cím előtag olvasása                                |
| Microsoft.Network/publicIPPrefixes/write                          | Nyilvános IP-címelőtag létrehozása vagy frissítése                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Nyilvános IP-címelőtag törlése                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | Nyilvános IP-cím létrehozása előtagból |

## <a name="next-steps"></a>További lépések

- További információ a [nyilvános IP-előtag](public-ip-address-prefix.md) használatának forgatókönyveiről és előnyeiről
