---
title: Egy Azure-beli hálózati adapter IP-címek konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan hozzáadása, módosítása és eltávolítása a privát és nyilvános IP-címeket a hálózati illesztő.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: 89b311edbae6b5f6679908b5d07b22b402b5c55e
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888066"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Hozzáadása, módosítása vagy törlése az Azure-beli hálózati adapter IP-címek

Ismerje meg, hogyan hozzáadása, módosítása és eltávolítása a nyilvános és privát IP-címeket a hálózati illesztő. Hozzárendelt hálózati adapter magánhálózati IP-címek engedélyezése más Azure-beli virtuális hálózathoz, és kapcsolattal rendelkező hálózatokban lévő erőforrásokkal kommunikálni a virtuális gép. Magánhálózati IP-cím is lehetővé teszi a kimenő kommunikációt az internethez, előre nem látható IP-cím használatával. A [nyilvános IP-cím](virtual-network-public-ip-address.md) rendelve egy hálózati adapter lehetővé teszi bejövő a kommunikációt egy virtuális géphez az internetről. A címet is lehetővé teszi az internethez, kiszámítható IP-címet használ a virtuális gépről kimenő kommunikáció. További információkért lásd: [az Azure kimenő kapcsolatainak ismertetése](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Ha meg kell létrehozása, módosítása vagy törlése egy hálózati adapter, olvassa el a [egy hálózati adapter kezelése](virtual-network-network-interface.md) cikk. Ha szeretné a hálózati adapterek hozzáadása vagy eltávolítása a hálózati adaptereket a virtuális gépről, olvassa el a [hozzáadása vagy eltávolítása a hálózati adapterek](virtual-network-network-interface-vm.md) cikk.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez a cikk bármely szakaszban ismertetett lépések elvégzése előtt hajtsa végre a következőket:

- Ha még nincs Azure-fiókkal, regisztráljon egy [ingyenes próbafiókot](https://azure.microsoft.com/free).
- Ha a portál használatával, nyissa meg a https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsokkal cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy a számítógépről futtatja a Powershellt. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ehhez az oktatóanyaghoz az Azure PowerShell-modul verzióját 1.0.0 vagy újabb. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha használja az Azure parancssori felület (CLI) parancsai cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/bash), vagy a parancssori felület futtatásával a számítógépről. Ehhez az oktatóanyaghoz az Azure CLI 2.0.31-es verzió vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI, is futtatni szeretné `az login` kapcsolat létrehozása az Azure-ral.

Jelentkezzen be, vagy csatlakozhat az Azure-ban, a fiókot hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepel a megfelelő műveleteket rendelt [hálózati engedélyek csatoló](virtual-network-network-interface.md#permissions).

## <a name="add-ip-addresses"></a>IP-címek hozzáadása

Hozzáadhat több [privát](#private) és [nyilvános](#public) [IPv4](#ipv4) szünteti meg, hogy egy hálózati adaptert, szereplő keretein belül a [Azure korlátairól](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) cikk. A portál használatával nem IPv6-cím hozzáadása egy meglévő hálózati adaptert (bár a magánhálózati IPv6-címek hozzáadása egy hálózati adaptert a hálózati adapter létrehozásakor a portál segítségével). A magánhálózati IPv6-címek hozzáadása egy használhatja PowerShell vagy a parancssori felület [másodlagos IP-konfiguráció](#secondary) (feltéve, nincsenek nem létező másodlagos IP-konfigurációk) egy meglévő hálózati adapter, amely nem csatlakozik egy virtuális gépet. Bármely eszköz nyilvános IPv6-cím hozzáadása egy hálózati adapter nem használható. Lásd: [IPv6](#ipv6) IPv6-címek használatával.

1. A szöveget tartalmazó mezőbe *erőforrások keresése* írja be az Azure portal tetején *hálózati adapterek*. Amikor **hálózati adapterek** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki a hálózati adaptert szeretne hozzáadni egy IPv4-címet a listából.
3. A **beállítások**válassza **IP-konfigurációk**.
4. A **IP-konfigurációk**válassza **+ Hozzáadás**.
5. Adja meg a következőket, majd válassza ki **OK**:

   |Beállítás|Kötelező?|Részletek|
   |---|---|---|
   |Name (Név)|Igen|A hálózati adapter egyedinek kell lennie|
   |Typo|Igen|Mivel ha ad hozzá egy IP-konfigurációt egy meglévő hálózati adaptert, és mindegyik hálózati interfész rendelkeznie kell egy [elsődleges](#primary) IP-konfiguráció egyetlen lehetősége van **másodlagos**.|
   |Magánhálózati IP-cím-hozzárendelési módszer|Igen|[**A dinamikus**](#dynamic): Az Azure hozzárendeli az a hálózati adapter van üzembe helyezve az alhálózat címtartományának következő elérhető címe. [**Statikus**](#static): Hozzárendelhet egy nem használt cím az alhálózat címtartományának a hálózati adapter van üzembe helyezve.|
   |Nyilvános IP-cím|Nem|**Letiltva:** Nincs nyilvános IP-cím erőforrás nem jelenleg társított IP-konfigurációhoz. **Engedélyezve:** Válasszon ki egy meglévő nyilvános IPv4 IP-címet, vagy hozzon létre egy újat. Ismerje meg, hogyan hozhat létre egy nyilvános IP-címet, olvassa el a [nyilvános IP-címek](virtual-network-public-ip-address.md#create-a-public-ip-address) cikk.|
6. Manuálisan adja hozzá másodlagos magánhálózati IP-címek a virtuális gép operációs rendszerének; Ehhez hajtsa végre a következő témakör utasításait a [több IP-cím hozzárendelése a virtuális gép operációs rendszerek](virtual-network-multiple-ip-addresses-portal.md#os-config) cikk. Lásd: [privát](#private) IP-címek előtt manuálisan ad hozzá egy virtuális gép operációs rendszerének IP-címek különleges szempontjait. Nem adható hozzá bármilyen nyilvános IP-címek a virtuális gép operációs rendszerének.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>IP-cím beállításainak módosítása

Előfordulhat, hogy kell az IPv4-cím, a hozzárendelési módszer módosításához módosítsa a statikus IPv4-címet, vagy egy hálózati adapterhez hozzárendelt nyilvános IP-cím. Ha módosítja egy virtuális gépen egy másodlagos hálózati adapterhez társított másodlagos IP-konfiguráció magánhálózati IPv4-címét (További információ [elsődleges és másodlagos hálózati adapterek](virtual-network-network-interface-vm.md)), helyezze a virtuális gépet azokat a leállított (felszabadított) állapotba előtt végezze el az alábbi lépéseket:

1. A szöveget tartalmazó mezőbe *erőforrások keresése* írja be az Azure portal tetején *hálózati adapterek*. Amikor **hálózati adapterek** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki a hálózati adapter, amelyet szeretne megtekinteni vagy módosítani az IP-cím beállításait a listából.
3. A **beállítások**válassza **IP-konfigurációk**.
4. Válassza ki a listából a módosítani kívánt IP-konfigurációja.
5. Módosítsa a beállításokat, szükség szerint, 5. lépésében a beállításaival kapcsolatos információk segítségével [adjon hozzá egy IP-konfiguráció](#add-ip-addresses).
6. Kattintson a **Mentés** gombra.

>[!NOTE]
>Ha az elsődleges hálózati adapter több IP-konfigurációval rendelkezik, és módosítja az elsődleges IP konfigurációjának magánhálózati IP-cím, manuálisan kell rendeli az elsődleges és másodlagos IP-címeket a hálózati adapterhez belül Windows (Linux rendszeren nem kötelező) . IP-címeket manuálisan rendel egy hálózati adapter egy operációs rendszeren belül, lásd: [több IP-cím hozzárendelése a virtuális gépek](virtual-network-multiple-ip-addresses-portal.md#os-config). Különleges szempontok előtt manuálisan ad hozzá egy virtuális gép operációs rendszerének IP-címek, lásd: [privát](#private) IP-címeket. Nem adható hozzá bármilyen nyilvános IP-címek a virtuális gép operációs rendszerének.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>Távolítsa el az IP-címek

Eltávolíthatja [privát](#private) és [nyilvános](#public) IP-címek a hálózati adapterre, de egy hálózati adapter mindig rendelkeznie kell legalább egy privát IPv4-cím rendelve.

1. A szöveget tartalmazó mezőbe *erőforrások keresése* írja be az Azure portal tetején *hálózati adapterek*. Amikor **hálózati adapterek** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki a hálózati adaptert, amely a el kívánja távolítani az IP-címeket a listában.
3. A **beállítások**válassza **IP-konfigurációk**.
4. Jobb gombbal válassza egy [másodlagos](#secondary) IP-konfiguráció (nem lehet törölni a [elsődleges](#primary) konfigurációs) válassza ki a törölni kívánt **törlése**, majd  **Igen**, a törlés megerősítéséhez. Ha a konfiguráció egy nyilvános IP-cím erőforrás tartozik, az erőforrás van leválasztása az IP-konfigurációból, de az erőforrás nem törlődik.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic ip-config delete](/cli/azure/network/nic/ip-config)|
|PowerShell|[Remove-AzNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>IP-konfigurációk

[Privát](#private) és (opcionálisan) [nyilvános](#public) IP-címek vannak rendelve egy hálózati adapterhez hozzárendelt egy vagy több IP konfigurációval. IP-konfiguráció két típusa van:

### <a name="primary"></a>Elsődleges

Mindegyik hálózati interfész hozzá van rendelve egy elsődleges IP-konfigurációval. Egy elsődleges IP-konfiguráció:

- Rendelkezik egy [privát](#private) [IPv4](#ipv4) hozzárendelt cím. Nem rendelhet hozzá egy privát [IPv6](#ipv6) címet elsődleges IP-konfigurációhoz.
- Az is előfordulhat egy [nyilvános](#public) IPv4-cím rendelve. Nyilvános IPv6-cím nem rendelhető hozzá egy elsődleges vagy másodlagos IP-konfigurációhoz. Ugyanakkor nyilvános IPv6-cím hozzárendelése egy Azure load balancert, amely képes a magánhálózati IPv6-címek a virtuális gépek bejövő forgalmának terheléselosztása terhelést. További információkért lásd: [részleteit és az IPv6 korlátozások](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

### <a name="secondary"></a>Másodlagos

Egy elsődleges IP-konfiguráció mellett egy hálózati adapter rendelkezhet nulla vagy több másodlagos IP-konfiguráció hozzárendelve. Egy másodlagos IP-konfiguráció:

- Kell egy privát IPv4- vagy IPv6-címet rendelte hozzá. Ha a cím IPv6, a hálózati adapterhez legfeljebb egy másodlagos IP-konfigurációval. Ha a cím IPv4, a hálózati adapter rendelkezhet több másodlagos IP-konfiguráció hozzárendelve. Hány privát és nyilvános IPv4-cím rendelhető egy hálózati adapterrel kapcsolatos további információkért tekintse meg a [Azure korlátairól](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) cikk.
- Előfordulhat, hogy is egy nyilvános IPv4-cím rendelt, ha a magánhálózati IP-cím IPv4. Ha a magánhálózati IP-cím IPv6-alapú, nem rendelhető hozzá egy nyilvános IPv4- vagy IPv6-címet az IP-konfigurációhoz. Több IP-címet rendel egy hálózati adapter hasznos lehet a forgatókönyvek például:
  - Több webhely vagy szolgáltatás üzemeltetése különböző IP-címekkel és SSL-tanúsítványokkal egyetlen kiszolgálón.
  - Egy virtuális gépet, mint egy hálózati virtuális berendezésen, például egy tűzfal vagy a load balancert.
  - Lehetővé teszi, hogy a magánhálózati IPv4-címek bármely, a hálózati adapterek valamelyikét, egy Azure Load Balancer háttérkészlethez. Korábban csak az elsődleges IPv4-címet az elsődleges hálózati adapter sikerült felvenni egy háttér-címkészletet. Tudjon meg többet terheléselosztásáról több IPv4-konfigurációt, tekintse meg a [terheléselosztás több IP-konfiguráció](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cikk. 
  - Betöltéséhez egyenleg egy hozzárendelt hálózati adapter IPv6-cím. A magánhálózati IPv6-címek terheléselosztásáról kapcsolatos további információkért tekintse meg a [terheléselosztása IPv6-címek](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cikk.

## <a name="address-types"></a>Cím-típusok

A következő típusú IP-címeket rendelhet egy [IP-konfiguráció](#ip-configurations):

### <a name="private"></a>Privát

Privát [IPv4](#ipv4) címét engedélyezése másik virtuális hálózathoz vagy más kapcsolattal rendelkező hálózatokban lévő erőforrásokkal kommunikálni a virtuális gép. Egy virtuális gépet nem lehet elérhető a bejövő, és nem képes a virtuális gép kimenő kommunikációra az egy privát [IPv6](#ipv6) cím, egy kivétellel. A virtuális gépek az Azure load balancer IPv6-cím használatával kommunikálhat. További információkért lásd: [részleteit és az IPv6 korlátozások](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

Alapértelmezés szerint az Azure DHCP-kiszolgálók a privát IPv4-cím hozzárendelése a [elsődleges IP-konfiguráció](#primary) az Azure hálózati adapter a virtuális gép operációs rendszeren belül a hálózati adapterhez. Kivéve, ha szükséges, manuálisan soha nem célszerű a virtuális gép operációs rendszerén belül egy hálózati adapter IP-címét.

> [!WARNING]
> Ha egy virtuális gép operációs rendszerén belül hálózati adapter elsődleges IP-címet az IPv4-címet az elsődleges hálózati adapter elsődleges IP-konfigurációhoz rendelt magánhálózati IPv4-címet, mint valaha különböző csatolva van egy virtuális gépet Azure-on belüli megszakad a kapcsolat a virtuális gépet.

Nincsenek olyan forgatókönyvekben, ahol azt a virtuális gép operációs rendszerén belül hálózati adapter IP-címet beállítani. Például kell manuálisan beállítani az elsődleges és másodlagos IP-címeket, a Windows operációs rendszer több IP-cím egy Azure virtuális gépen való hozzáadásakor. Linux rendszerű virtuális gép csak szükség lehet a másodlagos IP-címek kézi beállítására. Lásd: [hozzáadása IP-címek a virtuális gép operációs rendszerre](virtual-network-multiple-ip-addresses-portal.md#os-config) részleteiről. Ha átállítására lenne szükség módosítsa a címet, IP-konfigurációhoz rendelt, azt javasoljuk, hogy Ön:

1. Győződjön meg arról, hogy a virtuális gép egy cím fogadja az Azure DHCP-kiszolgálók. Ha már rendelkezik, állítsa vissza az IP-cím hozzárendelését a operációs rendszerben a DHCP, és indítsa újra a virtuális gépet.
2. Állítsa le (szabadítsa fel) a virtuális gépet.
3. Módosítsa az IP-címet az IP-konfiguráció Azure-ban.
4. Virtuális gép elindítása.
5. [Manuálisan konfigurálnia a](virtual-network-multiple-ip-addresses-portal.md#os-config) a másodlagos IP-címeket az operációs rendszer (és belül is az elsődleges IP-cím Windows belül) egyeznie beállítása Azure-ban.

Az előző lépések, Azure-ban, és a egy virtuális gép operációs rendszerében a hálózati adapterhez rendelt magánhálózati IP-cím szerint változatlan marad. Nyomon követéséhez az előfizetésben, amely a manuálisan beállított IP-címek egy operációs rendszerből a virtuális gépek, fontolja meg a Azure-beli hozzáadását [címke](../azure-resource-manager/resource-group-using-tags.md) a virtuális gépekhez. Használhatja a "IP-cím hozzárendelése: Statikus", például. Így megtalálhatja a virtuális gépek az előfizetésben, amely a manuálisan beállított IP-címét az operációs rendszeren belül.

Az azonos vagy csatlakoztatott virtuális hálózatokon belül más erőforrásokkal kommunikálni a virtuális gép egyrészt magánhálózati IP-cím is lehetővé teszi, hogy egy virtuális gép kimenő kommunikációját az internethez az. Kimenő kapcsolatok a forrás hálózati cím Azure fordíthatók le az előre nem látható nyilvános IP-címekhez. Az Azure kimenő internetkapcsolattal kapcsolatos további információkért olvassa el a [az Azure kimenő internetkapcsolattal](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cikk. Nem lehet kommunikálni a bejövő, a virtuális gép magánhálózati IP-címet az internetről. Ha a kimenő kapcsolatokat igényelnek kiszámítható nyilvános IP-címet, társítson egy nyilvános IP-cím erőforrás egy hálózati adapterhez.

### <a name="public"></a>Nyilvános

Keresztül egy nyilvános IP-cím erőforráshoz rendelt nyilvános IP-címek bejövő kapcsolat engedélyezése egy virtuális géphez az internetről. Kimenő kapcsolatok az interneten egy előre jelezhető IP-címet használja. Lásd: [az Azure kimenő kapcsolatainak ismertetése](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) részleteiről. Előfordulhat, hogy egy nyilvános IP-cím hozzárendelése egy IP-konfigurációt, de nem szükséges. Ha társít egy nyilvános IP-cím erőforrás nyilvános IP-cím nem rendel egy virtuális gépet, a virtuális gép is továbbra is képes kimenő kommunikációra az interneten. Ebben az esetben a magánhálózati IP-cím a forrás hálózati cím Azure fordíthatók le az előre nem látható nyilvános IP-címekhez. Nyilvános IP-cím erőforrás kapcsolatos további információkért lásd: [nyilvános IP-cím erőforrás](virtual-network-public-ip-address.md).

Egy hálózati adapterhez rendelhet privát és nyilvános IP-címek száma korlátozva van. További információkért olvassa el a [Azure korlátairól](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) cikk.

> [!NOTE]
> Azure virtuális gép magánhálózati IP-címet egy nyilvános IP-cím fordítja le. Ennek eredményeképpen a virtuális gépek operációs rendszer nem észleli a bármely nyilvános IP-cím rendelhető, így nem kell minden eddiginél rendelheti hozzá kézzel a operációs rendszeren belül nyilvános IP-címet.

## <a name="assignment-methods"></a>Hozzárendelési módszer

Nyilvános és privát IP-címeket rendel a következő hozzárendelési módszer egyikével:

### <a name="dynamic"></a>Dinamikus

Dinamikus magánhálózati IPv4- és IPv6-alapú (nem kötelező) címek alapértelmezés szerint vannak hozzárendelve.

- **Csak nyilvános**: Az Azure egyedi tartományból a címet rendel minden egyes Azure-régióban. Ha szeretné megtudni, melyik címtartományok egyes régiókban vannak rendelve, lásd: [a Microsoft Azure adatközpont IP-címtartományait](https://www.microsoft.com/download/details.aspx?id=41653). A cím módosíthatja, ha egy virtuális gép van leállítva (felszabadítva), majd újra elindult. Nyilvános IPv6-cím nem rendelhető hozzá egy IP-konfigurációt vagy hozzárendelési módszer használatával.
- **Csak privát**: Az Azure fenntart minden egyes alhálózat címtartományának első négy címét, és a címek nem osztja ki. Az Azure az alhálózat címtartományának egyik erőforrásához rendeli hozzá a következő elérhető címet. Például, ha az alhálózat címtartománya 10.0.0.0/16, és a 10.0.0.0.4-10.0.0.14 közötti címek már hozzá lettek rendelve (a .0–.3 címek fenn vannak tartva), az Azure az erőforráshoz rendeli a 10.0.0.15 címet. Az alapértelmezett lefoglalási módszer a dinamikus. Kiosztás után a dinamikus IP-címek csak a hálózati adapter törlésekor, a virtuális hálózaton belüli másik alhálózatra történő kiosztáskor vagy a kiosztási módszer statikusra váltása és másik IP-cím megadása esetén szabadulnak fel. Alapértelmezés szerint, amikor a lefoglalási módszert dinamikusról statikusra váltja, az Azure statikus címként osztja ki az előzőleg dinamikusan kiosztott címet. Hozzárendelhet egy magánhálózati IPv6-címet, a dinamikus hozzárendelési módszer használatával.

### <a name="static"></a>Statikus

(Opcionális) egy nyilvános vagy magánhálózati statikus IPv4-címet rendelhet egy IP-konfigurációt. Nem rendelhető hozzá egy statikus nyilvános vagy magánhálózati IPv6-címet az IP-konfigurációjához. Milyen az Azure hozzárendeli az statikus nyilvános IPv4-címeket kapcsolatos további információkért lásd: [nyilvános IP-címek](virtual-network-public-ip-address.md).

- **Csak nyilvános**: Az Azure egyedi tartományból a címet rendel minden egyes Azure-régióban. Letöltheti a tartományok (előtagok) listáját az Azure [nyilvános](https://www.microsoft.com/download/details.aspx?id=56519), valamint [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) és [Germany](https://www.microsoft.com/download/details.aspx?id=57064) felhője esetében. A cím nem módosul, amíg nem törlik a nyilvános IP-cím erőforrás van hozzárendelve, vagy a dinamikus megváltozott a hozzárendelési módszer. A nyilvános IP-cím erőforrás-t hozzárendelik egy IP-konfigurációhoz, ha az IP-konfigurációja a hozzárendelési módszer módosítása előtt kell leválasztása.
- **Csak privát**: Válassza ki, és az alhálózat címtartománya-címet hozzárendelni. A hozzárendelt cím az alhálózat címtartományán belül bármilyen cím lehet, amely nem tartozik az alhálózat címtartományának első négy címébe, és nincs hozzárendelve más erőforráshoz az alhálózatban. A statikus címek csak egy hálózati adapter törlése esetén szabadulnak fel. Amennyiben a kiosztási módszert statikusra váltja, az Azure az előzőleg hozzárendelt statikus IP-címeket dinamikus IP-címként osztja ki akkor is, ha a cím nem az alhálózat címtartományának következő elérhető címe. A cím akkor is megváltozik, ha a hálózati adapter ugyanazon a virtuális hálózaton belül egy másik alhálózathoz lesz kiosztva, de ahhoz, hogy a hálózati adaptert egy másik alhálózathoz ossza ki, a kiosztási módszert először statikusról dinamikusra kell váltani. Miután hozzárendelte a hálózati adaptert egy másik alhálózathoz, a kiosztási módszer visszaváltható statikusra, és hozzárendelhet egy IP-címet az új alhálózat címtartományából.

## <a name="ip-address-versions"></a>IP-cím verziója

A következő verziók címek hozzárendelésekor is megadhatja:

### <a name="ipv4"></a>IPv4

Mindegyik hálózati interfész rendelkeznie kell egy [elsődleges](#primary) kijelölt IP-konfiguráció [privát](#private) [IPv4](#ipv4) címet. Hozzáadhat egy vagy több [másodlagos](#secondary) , amelyek mindegyikére magánhálózati IPv4- és (opcionálisan) egy IPv4 IP-konfigurációk [nyilvános](#public) IP-címet.

### <a name="ipv6"></a>IPv6

Nulla vagy egy privát rendelhet [IPv6](#ipv6) egy másodlagos IP-konfiguráció hálózati adapter címe. A hálózati adapter nem lehet minden meglévő másodlagos IP-konfigurációt. Az IPv6-címet, a portál használatával nem adhat hozzá egy IP-konfigurációt. Használja a Powershellt vagy a parancssori Felületet egy IP-konfigurációt a magánhálózati IPv6-címek hozzáadása egy meglévő hálózati adaptert. A hálózati adapter nem lehet csatolni a meglévő virtuális géphez.

> [!NOTE]
> Bár létrehozhat egy hálózati adapter IPv6-címet, a portál használatával, nem adhat egy meglévő hálózati adaptert egy új vagy meglévő virtuális gépet, a portál használatával. PowerShell vagy az Azure CLI használatával hozzon létre egy hálózati adapter magánhálózati IPv6-címmel, akkor a hálózati adapter csatlakoztatása egy virtuális gép létrehozásakor. Egy hálózati adapterrel rendelkező meglévő virtuális géphez rendelt magánhálózati IPv6-címek nem csatolható. Egy IP-konfiguráció bármely olyan eszközöket (portal, PowerShell vagy parancssori felület) használatával virtuális géphez csatolt hálózati adapter magánhálózati IPv6-címek nem lehet hozzáadni.

Nyilvános IPv6-cím nem rendelhető hozzá egy elsődleges vagy másodlagos IP-konfigurációhoz.

## <a name="skus"></a>Termékváltozatok

Az alapszintű vagy standard termékváltozatú nyilvános IP-cím jön létre. Termékváltozat különbségek kapcsolatos további információkért lásd: [nyilvános IP-címek kezelése](virtual-network-public-ip-address.md).

> [!NOTE]
> Ha egy standard termékváltozatú nyilvános IP-címet hozzárendel egy virtuális gép hálózati adapteréhez, kifejezetten engedélyeznie kell a kívánt forgalmat egy [hálózati biztonsági csoporttal](security-overview.md#network-security-groups). Az erőforrással történő kommunikáció meghiúsul, amíg nem hoz létre és rendel hozzá egy hálózati biztonsági csoportot, és kifejezetten nem engedélyezi a kívánt forgalmat.

## <a name="next-steps"></a>További lépések
Hozzon létre egy virtuális gépet másik IP-konfigurációval, olvassa el a következő cikkeket:

|Tevékenység|Eszköz|
|---|---|
|Több hálózati adapterrel rendelkező virtuális gép létrehozása|[Parancssori felület](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Hozzon létre egy egyetlen hálózati adapterrel rendelkező virtuális több IPv4-cím|[Parancssori felület](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Hozzon létre egy egyetlen hálózati adapterrel rendelkező virtuális magánhálózati IPv6-címek (mögött az Azure Load Balancerhez)|[Parancssori felület](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-sablon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
