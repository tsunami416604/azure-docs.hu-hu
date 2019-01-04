---
title: Létrehozása, módosítása vagy törlése az Azure nyilvános IP-cím előtagja
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan létrehozása, módosítása vagy törlése egy nyilvános IP-cím előtagja.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: anavin
ms.openlocfilehash: 4207698c57b907cf60fd860bc409c8f8d5a4c565
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015287"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Létrehozása, módosítása vagy törlése egy nyilvános IP-cím előtagja

További tudnivalók a nyilvános IP-címelőtag és létrehozása, módosítása és törlése egy. Egy nyilvános IP-címelőtag címek megadott nyilvános IP-címek száma szerint egybefüggő tartományát. Az előfizetés a címek vannak hozzárendelve. Amikor létrehoz egy nyilvános IP-cím erőforráshoz, egy statikus nyilvános IP-cím hozzárendelését az előtag és rendelheti hozzá a virtuális gépek, betölteni a terheléselosztók vagy más erőforrások, az internet-kapcsolat. Ha még nem ismeri a nyilvános IP-címelőtagokat, [nyilvános IP-cím cím előtag – áttekintés](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Előkészületek

> [!IMPORTANT]
> Nyilvános IP-előtag van, korlátozott számú régióban egy nyilvános előzetes verzióban érhető el. Is [ismerje meg, hogy mit jelent az előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Nyilvános IP-előtag jelenleg érhető el: USA nyugati középső RÉGIÓJA, USA nyugati RÉGIÓJA, USA nyugati RÉGIÓJA 2, USA középső RÉGIÓJA, Észak-Európa, Nyugat-Európa és Délkelet-Ázsia. Régiók frissített listáját, keresse fel [Azure-frissítések](https://azure.microsoft.com/updates/?product=virtual-network).

Ez a cikk bármely szakaszban ismertetett lépések elvégzése előtt hajtsa végre a következőket:

- Ha még nincs Azure-fiókkal, regisztráljon egy [ingyenes próbafiókot](https://azure.microsoft.com/free).
- Ha a portál használatával, nyissa meg a https://aka.ms/publicipprefixportal, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsokkal cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy a számítógépről futtatja a Powershellt. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ehhez az oktatóanyaghoz az AzureRm.Network PowerShell-modul verzióját 6.3.1 vagy újabb. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM.Network`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://github.com/Azure/azure-powershell/releases/tag/AzureRm.Network.6.3.1) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha használja az Azure parancssori felület (CLI) parancsai cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/bash), vagy a parancssori felület futtatásával a számítógépről. Ehhez az oktatóanyaghoz az Azure CLI 2.0.41 verzió vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI, is futtatni szeretné `az login` kapcsolat létrehozása az Azure-ral.

Jelentkezzen be, vagy csatlakozhat az Azure-ban, a fiókot hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepel a megfelelő műveleteket rendelt [engedélyek ](#permissions).

Nyilvános IP-címelőtagokat díj rendelkezik. További információkért lásd: [díjszabás](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address-prefix"></a>Hozzon létre egy nyilvános IP-cím előtagja

1. A portál bal felső sarokban, válassza **+ erőforrás létrehozása**.
2. Adja meg *nyilvános ip-címelőtag* a a *keresés a piactéren* mezőbe. Amikor **nyilvános IP-címelőtag** megjelenik a keresési eredmények között, válassza ki.
3. A **nyilvános IP-címelőtag**válassza **létrehozás**.
4. Adja meg, vagy válassza ki a következő beállítások értékeit a **létrehozása nyilvános IP-címelőtag**, majd **létrehozás**:

   |Beállítás|Kötelező?|Részletek|
   |---|---|---|
   |Előfizetés|Igen|Léteznie kell az azonos [előfizetés](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) és az erőforrásnak a nyilvános IP-címet társítani szeretné.|
   |Erőforráscsoport|Igen|Az azonos vagy eltérő létrejöhet [erőforráscsoport](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) és az erőforrásnak a nyilvános IP-címet társítani szeretné.|
   |Name (Név)|Igen|A nevét, válassza ki az erőforráscsoporton belül egyedinek kell lennie.|
   |Régió|Igen|Léteznie kell az azonos [régió](https://azure.microsoft.com/regions)nyilvános IP-címeket, a tartomány címek fogja tudni hozzárendelni. Előtag jelenleg az USA nyugati középső Régiója, USA nyugati RÉGIÓJA, USA nyugati RÉGIÓJA 2, USA középső RÉGIÓJA, Észak-Európa, Nyugat-Európa és Délkelet-Ázsia előzetes van.|
   |Előtag hossza|Igen| Van szüksége az előtag méretét. Egy/28 vagy 16 IP-címet az alapértelmezett érték. 

**Parancsok**


|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network public-IP-előtag létrehozása](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[Új AzureRmPublicIpPrefix](/powershell/module/azurerm.network/new-azurermpublicipprefix)|

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Hozzon létre egy statikus nyilvános IP-címet az előtag
Miután létrehozta az előtag, az előtag a statikus IP-címeket kell létrehoznia. Ehhez kövesse az alábbi lépéseket.

1. A szöveget tartalmazó mezőbe *erőforrások keresése* írja be az Azure portal tetején *nyilvános ip-címelőtag*. Amikor **nyilvános IP-címelőtagokat** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki a nyilvános IP-címek a létrehozni kívánt prefx.
3. Amikor megjelenik a keresési eredmények között, válassza ki, és kattintson a **+ IP-cím hozzáadása** az Áttekintés szakaszban. Abban az esetben ez nem látható, ellenőrizze, hogy előzetes használja a megfelelő hivatkozásra: https://aka.ms/publicipprefixportal
4. Adja meg vagy válassza ki a következő beállítások alatt értékeket **nyilvános IP-cím létrehozása**. Mivel egy előtagot a Standard Termékváltozat, IPv4, és a statikus, csak adja meg a következő információkat kell:

   |Beállítás|Kötelező?|Részletek|
    |---|---|---|
    |Name (Név)|Igen|A nyilvános IP-cím nevére, válassza ki az erőforráscsoporton belül egyedinek kell lennie.|
   |Üresjárat időkorlátja (perc)|Nem|Hány perc a TCP- vagy HTTP-kapcsolat nyitva tartása anélkül, hogy az ügyfelek életben tartási üzenetek küldéséhez. |
   |DNS-névcímke|Nem|(Között az összes előfizetés és az összes ügyfél) a nevét a hoz létre az Azure-régión belül egyedinek kell lennie. Az Azure automatikusan regisztrálja a nevét és IP-cím a DNS-ben úgy csatlakozhat a nevű erőforrás. Azure hozzáfűz egy alapértelmezett alhálózat például *location.cloudapp.azure.com* (ahol a helye az választja) nevet ad meg, hozhat létre teljesen minősített DNS-neve. További információkért lásd: [használata az Azure DNS az Azure nyilvános IP-címet](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

## <a name="view-or-delete-a-prefix"></a>Megtekintése és törlése előtag

1. A szöveget tartalmazó mezőbe *erőforrások keresése* írja be az Azure portal tetején *nyilvános ip-címelőtag*. Amikor **nyilvános IP-címelőtagokat** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki a nevét a nyilvános IP-címelőtag szeretné tekinteni, beállításainak módosítása vagy törlése a listából.
3. Fejezze be a következő beállításokat, attól függően, hogy a megtekintése, törlése és módosítása a nyilvános IP-címelőtag egyikét.
    - **Nézet**: A **áttekintése** szakasz bemutatja a nyilvános IP-címelőtagot, például előtag-beállításait.
    - **Törlés**: A nyilvános IP-címelőtag törléséhez válassza ki **törlése** a a **áttekintése** szakaszban. Ha az előtag belül cím nyilvános IP-cím erőforrás van társítva, a nyilvános IP-cím erőforrás először törölnie kell. Lásd: [egy nyilvános IP-cím törlése](virtual-network-public-ip-address.md#view-change-settings-for-or-delete-a-public-ip-address).

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network public-IP-előtag lista](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) nyilvános IP-címeinek listáját, hogy [az network public-IP-előtag show](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) ; beállítások megjelenítése [az network public-IP-előtag frissítés](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) frissítése; [az network public-IP-előtag delete](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) törlése|
|PowerShell|[Get-AzureRmPublicIpPrefix](/powershell/module/azurerm.network/get-azurermpublicipprefix) és egy nyilvános IP-címobjektumot, beállítások megtekintéséhez [Set-AzureRmPublicIpPrefix](/powershell/module/azurerm.network/set-azurermpublicipprefix) ;-beállítások frissítése [Remove-AzureRmPublicIpPrefix](/powershell/module/azurerm.network/remove-azurermpublicipprefix) törlése|

## <a name="permissions"></a>Engedélyek

Feladatok végrehajtásához a nyilvános IP-címelőtagokat, a fiókot hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy olyan [egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkör, amely hozzá van rendelve a megfelelő műveleteket az alábbi táblázatban felsorolt:

| Műveletek                                                                   | Name (Név)                                                           |
| ---------                                                                | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Olvassa el a nyilvános IP-cím előtagja                                |
| Microsoft.Network/publicIPPrefixes/write                          | Létrehozás vagy frissítés egy nyilvános IP-cím előtagja                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Egy nyilvános IP-címelőtag törlése                              |
|Microsoft.Network/publicIPPrefixes/join/action | Az előtag egy nyilvános IP-cím létrehozása |

## <a name="next-steps"></a>További lépések

- További információ a forgatókönyvek és használatának előnyei egy [nyilvános IP-előtag](public-ip-address-prefix.md)
