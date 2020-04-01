---
title: IP-címek konfigurálása Azure hálózati csatolóhoz | Microsoft dokumentumok
description: Megtudhatja, hogy miként adhat hozzá, módosíthat és távolíthat el privát és nyilvános IP-címeket a hálózati adapterekhez.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: kumud
ms.openlocfilehash: b5655a58c3538ac47e8649619b079dc46ee01242
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473215"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Azure hálózati adapter IP-címének hozzáadása, módosítása vagy eltávolítása

Megtudhatja, hogy miként adhat hozzá, módosíthat és távolíthat el nyilvános és privát IP-címeket egy hálózati adapterhez. A hálózati adapterhez rendelt privát IP-címek lehetővé teszik, hogy a virtuális gépek kommunikáljanak az Azure virtuális hálózatában és a csatlakoztatott hálózatokmás erőforrásaival. A privát IP-cím is lehetővé teszi a kimenő kommunikáció az internethez egy kiszámíthatatlan IP-címet. A hálózati adapterhez rendelt [nyilvános IP-cím](virtual-network-public-ip-address.md) lehetővé teszi a virtuális géphez való bejövő kommunikációt az internetről. A cím lehetővé teszi a kimenő kommunikációt a virtuális gépről az internetre egy kiszámítható IP-cím használatával. További információt [a Kimenő kapcsolatok megértése az Azure-ban című témakörben talál.](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Ha hálózati adaptert kell létrehoznia, módosítania vagy törölnie, olvassa el a [Hálózati csatoló kezelése](virtual-network-network-interface.md) című cikket. Ha hálózati csatolőket kell hozzáadnia a virtuális géphez, vagy el kell távolítania a hálózati adaptereket, olvassa el a [Hálózati csatolók hozzáadása vagy eltávolítása](virtual-network-network-interface-vm.md) című cikket.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A cikk bármely részében ismertetett lépések végrehajtása előtt hajtsa végre a következő feladatokat:

- Ha még nem rendelkezik Azure-fiókkal, regisztráljon egy [ingyenes próbafiókot.](https://azure.microsoft.com/free)
- Ha a portált https://portal.azure.comhasználja, nyissa meg a, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsok at feladatok elvégzéséhez ebben a cikkben, vagy futtassa a parancsokat az [Azure Cloud Shell,](https://shell.azure.com/powershell)vagy a PowerShell futtatásával a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ez az oktatóanyag az Azure PowerShell-modul 1.0.0-s vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha az Azure parancssori felület (CLI) parancsokkal hajthatja végre a cikkben szereplő feladatokat, futtassa a parancsokat az [Azure Cloud Shellben,](https://shell.azure.com/bash)vagy a CLI futtatásával a számítógépről. Ez az oktatóanyag az Azure CLI 2.0.31-es vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha az Azure CLI helyileg fut, akkor `az login` is kell futtatnia, hogy hozzon létre egy kapcsolatot az Azure-ral.

A fiók, amelybe bejelentkezik, vagy amelyhez csatlakozik az Azure-hoz, hozzá kell rendelnie a [hálózati közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [olyan egyéni szerepkörhöz,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) amely a [hálózati csatoló engedélyei](virtual-network-network-interface.md#permissions)között felsorolt megfelelő műveletekhez van rendelve.

## <a name="add-ip-addresses"></a>IP-címek hozzáadása

Az [Azure-korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) ról szóló cikkben felsorolt korlátokon belül annyi [magán-](#private) és [nyilvános](#public) [IPv4-címet](#ipv4) adhat hozzá, amennyi szükséges. Hozzáadhat egy privát IPv6-címet egy [másodlagos IP-konfigurációhoz](#secondary) (feltéve, hogy nincsenek meglévő másodlagos IP-konfigurációk) egy meglévő hálózati adapterhez. Minden hálózati adapterlegek számára lehet egy IPv6-privát cím. Az IPv6 hálózati adapter konfigurációjához hozzáadhat nyilvános IPv6-címet. Az [IPv6-címek](#ipv6) használatáról az IPv6-címek használatáról az IPv6-címeket találja.

1. Az Azure Portal tetején található *Keresési erőforrások at* tartalmazó mezőbe írja be a hálózati *csatolók*kifejezést. Amikor **hálózati adapterek** jelennek meg a keresési eredmények között, jelölje ki azt.
2. A listából válassza ki azt a hálózati adaptert, amelyhez IPv4-címet szeretne hozzáadni.
3. A **BEÁLLÍTÁSOK**csoportban válassza az **IP-konfigurációk lehetőséget.**
4. Az **IP-konfigurációk csoportban**válassza a **+ Add**lehetőséget.
5. Adja meg a következőket, majd válassza az **OK gombot:**

   |Beállítás|Kötelező?|Részletek|
   |---|---|---|
   |Név|Igen|A hálózati adapterhez egyedinek kell lennie|
   |Típus|Igen|Mivel egy meglévő hálózati adapterhez IP-konfigurációt ad hozzá, és minden hálózati adapternek [elsődleges](#primary) IP-konfigurációval kell rendelkeznie, az egyetlen beállítás a **Másodlagos**.|
   |Privát IP-cím hozzárendelési módszer|Igen|[**Dinamikus**](#dynamic): Az Azure hozzárendeli a következő elérhető címet az alhálózati címtartományhoz, amelyben a hálózati adapter telepítve van. [**Statikus**](#static): Nem használt címet rendel hozzá az alhálózati címtartományhoz, amelyben a hálózati adapter telepítve van.|
   |Nyilvános IP-cím|Nem|**Letiltva:** Jelenleg nincs nyilvános IP-címerőforrás társítva az IP-konfigurációhoz. **Engedélyezve:** Jelöljön ki egy meglévő IPv4 nyilvános IP-címet, vagy hozzon létre egy újat. Ha meg szeretné tudni, hogyan hozhat létre nyilvános IP-címet, olvassa el a [Nyilvános IP-címek](virtual-network-public-ip-address.md#create-a-public-ip-address) ről szóló cikket.|
6. Manuálisan adjon hozzá másodlagos magánhálózati IP-címeket a virtuálisgép-operációs rendszerhez a [Több IP-cím hozzárendelése a virtuálisgép-operációs rendszerekhez](virtual-network-multiple-ip-addresses-portal.md#os-config) cikkben található utasítások kitöltésével. Az IP-címek virtuálisgép-operációs rendszerhez való manuális hozzáadása előtt tekintse meg a [privát](#private) IP-címeket. Ne adjon nyilvános IP-címeket a virtuális gép operációs rendszeréhez.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>IP-címbeállítások módosítása

Előfordulhat, hogy módosítania kell egy IPv4-cím hozzárendelési módját, módosítania kell a statikus IPv4-címet, vagy módosítania kell a hálózati adapterhez rendelt nyilvános IP-címet. Ha egy virtuális gép másodlagos hálózati adapteréhez társított másodlagos IP-konfiguráció privát IPv4-címét módosítja (további információ az [elsődleges és másodlagos hálózati adapterekről),](virtual-network-network-interface-vm.md)helyezze a virtuális gépet a leállított (felszabadított) állapotba az alábbi lépések végrehajtása előtt:

1. Az Azure Portal tetején található *Keresési erőforrások at* tartalmazó mezőbe írja be a hálózati *csatolók*kifejezést. Amikor **hálózati adapterek** jelennek meg a keresési eredmények között, jelölje ki azt.
2. Jelölje ki azt a hálózati illesztőt, amelynek ip-címbeállításait meg szeretné tekinteni vagy módosítani szeretné a listából.
3. A **BEÁLLÍTÁSOK**csoportban válassza az **IP-konfigurációk lehetőséget.**
4. Válassza ki a listából a módosítani kívánt IP-konfigurációt.
5. Módosítsa a beállításokat, ha szükséges, az [IP-konfiguráció hozzáadása](#add-ip-addresses)című 5.
6. Kattintson a **Mentés** gombra.

>[!NOTE]
>Ha az elsődleges hálózati adapter több IP-konfigurációval rendelkezik, és módosítja az elsődleges IP-konfiguráció privát IP-címét, manuálisan kell újra hozzárendelnie az elsődleges és másodlagos IP-címeket a Windows hálózati adapteréhez (linuxos kapcsolat esetén nem szükséges). Ha manuálisan szeretne IP-címeket rendelni egy operációs rendszeren belüli hálózati adapterhez, olvassa el a [Több IP-cím hozzárendelése virtuális gépekhez](virtual-network-multiple-ip-addresses-portal.md#os-config)című témakört. Az IP-címek virtuálisgép-operációs rendszerhez való manuális hozzáadása előtt további szempontokat a [magánhálózati](#private) IP-címek című témakörben lehet figyelembe venni. Ne adjon nyilvános IP-címeket a virtuális gép operációs rendszeréhez.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic ip-config frissítés](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>IP-címek eltávolítása

A hálózati adapterről eltávolíthatja [a magán-](#private) és [nyilvános](#public) IP-címeket, de a hálózati adapterhez mindig legalább egy privát IPv4-címnek kell hozzárendelnie.

1. Az Azure Portal tetején található *Keresési erőforrások at* tartalmazó mezőbe írja be a hálózati *csatolók*kifejezést. Amikor **hálózati adapterek** jelennek meg a keresési eredmények között, jelölje ki azt.
2. Jelölje ki azt a hálózati adaptert, amelyet el szeretne távolítani a listából.
3. A **BEÁLLÍTÁSOK**csoportban válassza az **IP-konfigurációk lehetőséget.**
4. Válassza ki jobb oldali választott [ip-konfigurációját](#secondary) (nem törölheti az [elsődleges](#primary) konfigurációt), válassza a **Törlés**lehetőséget, majd a törlés megerősítéséhez kattintson az **Igen**gombra. Ha a konfigurációhoz nyilvános IP-címerőforrás van társítva, az erőforrás el lesz haítható az IP-konfigurációtól, de az erőforrás nem törlődik.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic ip-config törlés](/cli/azure/network/nic/ip-config)|
|PowerShell|[Eltávolítás-AzNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>IP-konfigurációk

[A magán-](#private) és (opcionálisan) [nyilvános](#public) IP-címek egy vagy több hálózati adapterhez rendelt IP-konfigurációhoz vannak hozzárendelve. Az IP-konfigurációknak két típusa van:

### <a name="primary"></a>Elsődleges

Minden hálózati adapterhez egy elsődleges IP-konfiguráció tartozik. Elsődleges IP-konfiguráció:

- Privát [private](#private) [IPv4-címmel](#ipv4) van hozzárendelve. Elsődleges IP-konfigurációhoz nem rendelhet [magánalapú IPv6-címet.](#ipv6)
- Nyilvános [public](#public) IPv4-cím is rendelhető hozzá. Nyilvános IPv6-cím nem rendelhető elsődleges (IPv4) IP-konfigurációhoz. 

### <a name="secondary"></a>Másodlagos

Az elsődleges IP-konfigurációmellett a hálózati adapterhez nulla vagy több másodlagos IP-konfiguráció is rendelhető. Másodlagos IP-konfiguráció:

- Privát IPv4- vagy IPv6-címmel kell rendelkeznie. Ha a cím IPv6, a hálózati adapternek csak egy másodlagos IP-konfigurációja lehet. Ha a cím IPv4, a hálózati adapterhez több másodlagos IP-konfiguráció is rendelhető. Ha többet szeretne megtudni arról, hogy hány magán- és nyilvános IPv4-cím rendelhető hozzá egy hálózati adapterhez, tekintse meg az [Azure limits](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) cikket.
- Nyilvános IPv4- vagy IPv6-cím is rendelhető hozzá. Több IPv4-cím hozzárendelése hálózati adapterhez olyan esetekben hasznos, mint például:
  - Több webhely vagy szolgáltatás üzemeltetése különböző IP-címekkel és TLS/SSL-tanúsítványokkal egyetlen kiszolgálón.
  - Hálózati virtuális berendezésként, például tűzfalként vagy terheléselosztóként szolgáló virtuális gép.
  - Az a képesség, hogy adjunk hozzá a hálózati adapterek bármelyikének privát IPv4-címét egy Azure Load Balancer háttérkészlethez. A múltban csak az elsődleges hálózati adapter elsődleges IPv4-címe adható hozzá egy háttérkészlethez. Ha többet szeretne tudni atöbb IPv4-konfiguráció terheléselosztásáról, olvassa el a [Több IP-konfiguráció terheléselosztásáról](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szóló cikket. 
  - A hálózati adapterhez rendelt egy IPv6-cím elelosztásának lehetősége. Ha többet szeretne tudni arról, hogy miként töltheti be az egyenleget egy privát IPv6-címre, olvassa el a [Terheléselosztás IPv6-címek ről szóló](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cikket.

## <a name="address-types"></a>Címtípusok

A következő típusú IP-címeket rendelheti [ip-konfigurációhoz:](#ip-configurations)

### <a name="private"></a>Privát

A privát [IPv4-](#ipv4) vagy IPv6-címek lehetővé teszik, hogy a virtuális gépek kommunikáljanak a virtuális hálózat vagy más csatlakoztatott hálózatok más erőforrásaival. 

Alapértelmezés szerint az Azure DHCP-kiszolgálók hozzárendelik a privát IPv4-címet az Azure hálózati adapter [elsődleges IP-konfigurációjához](#primary) a virtuális gép operációs rendszerén belüli hálózati adapterhez. Ha csak szükséges, soha ne állítsa be manuálisan a virtuális gép operációs rendszerén belüli hálózati adapter IP-címét.

> [!WARNING]
> Ha a virtuális gép operációs rendszerén belül a hálózati adapter elsődleges IP-címeként beállított IPv4-cím valaha is eltér az Azure-on belüli virtuális géphez csatlakoztatott elsődleges hálózati adapter elsődleges IP-konfigurációjához rendelt privát IPv4-címtől, elveszíti a virtuális géphez való kapcsolódást.

Vannak olyan esetek, amikor manuálisan kell beállítani a virtuális csatoló IP-címét a virtuális gép operációs rendszerén belül. Például manuálisan kell beállítania a Windows operációs rendszer elsődleges és másodlagos IP-címét, amikor több IP-címet ad hozzá egy Azure virtuális géphez. Linux os virtuális gép esetén előfordulhat, hogy csak a másodlagos IP-címeket kell manuálisan beállítania. A részletekért [lásd: IP-címek hozzáadása virtuális gép operációs rendszerhez](virtual-network-multiple-ip-addresses-portal.md#os-config) című témakörben. Ha bármikor módosítania kell az IP-konfigurációhoz rendelt címet, javasoljuk, hogy:

1. Győződjön meg arról, hogy a virtuális gép címet kap az Azure DHCP-kiszolgálóktól. Miután elkészült, módosítsa az IP-cím hozzárendelését DHCP-re az operációs rendszeren belül, és indítsa újra a virtuális gépet.
2. Állítsa le (szabadítsa fel) a virtuális gépet.
3. Módosítsa az IP-cím az Azure-on belüli IP-konfiguráció.
4. Indítsa el a virtuális gépet.
5. [Manuálisan konfigurálja](virtual-network-multiple-ip-addresses-portal.md#os-config) a másodlagos IP-címeket az operációs rendszeren belül (és a Windows elsődleges IP-címét is) úgy, hogy azok megfeleljenek az Azure-ban beállított nak.

Az előző lépések végrehajtásával az Azure-on belüli és a virtuális gép operációs rendszerén belül a hálózati adapterhez rendelt privát IP-cím változatlan marad. Ha nyomon szeretné követni, hogy az előfizetésen belül mely virtuális gépekhez, amelyekhez manuálisan beállított a felhasználói címeket egy operációs rendszeren belül, fontolja meg egy [Azure-címke](../azure-resource-manager/management/tag-resources.md) hozzáadása a virtuális gépekhez. Használhatja például az "IP-címhozzárendelés: Statikus" kifejezést. Így könnyedén megtalálhatja azokat a virtuális gépeket az előfizetésében, amelyekhez manuálisan beállította az IP-címet az operációs rendszeren belül.

Amellett, hogy egy virtuális gép kommunikálhat az ugyanazon vagy csatlakoztatott virtuális hálózatokon belüli más erőforrásokkal, a magánhálózati IP-cím lehetővé teszi, hogy a virtuális gép kommunikáljon az internettel. A kimenő kapcsolatok az Azure által előre nem látható nyilvános IP-címre fordított forráshálózati cím. Ha többet szeretne megtudni az Azure kimenő internetkapcsolatról, olvassa el az [Azure kimenő internetkapcsolatról](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szóló cikkét. A virtuális gépek privát IP-címéhez nem lehet bejövő kapcsolatot kommunikálni az internetről. Ha a kimenő kapcsolatok előre jelezhető nyilvános IP-címet igényelnek, társítson egy nyilvános IP-címerőforrást egy hálózati adapterhez.

### <a name="public"></a>Nyilvános

A nyilvános IP-cím erőforráson keresztül hozzárendelt nyilvános IP-címek lehetővé teszik a bejövő kapcsolatot egy virtuális géphez az internetről. Az internethez való kimenő kapcsolatok kiszámítható IP-címet használnak. A részleteket [az Azure-beli kimenő kapcsolatok ismertetése](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) című témakörben találja. Nyilvános IP-címet rendelhet egy IP-konfigurációhoz, de nem szükséges. Ha nem rendel nyilvános IP-címet egy virtuális géphez egy nyilvános IP-címerőforrás társításával, a virtuális gép továbbra is kommunikálhat az internettel. Ebben az esetben a magánhálózati IP-cím az Azure által lefordított forráshálózati cím egy előre nem látható nyilvános IP-címre. A nyilvános IP-cím erőforrásokról a [Nyilvános IP-cím erőforrás](virtual-network-public-ip-address.md)című témakörben olvashat bővebben.

A hálózati adapterhez rendelhető magán- és nyilvános IP-címek száma korlátozott. A részletekért olvassa el az [Azure-korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) ról szóló cikket.

> [!NOTE]
> Az Azure egy virtuális gép privát IP-címét egy nyilvános IP-címre fordítja le. Ennek eredményeképpen a virtuális gép operációs rendszere nem ismeri a hozzá rendelt nyilvános IP-címet, így nem szükséges manuálisan hozzárendelni egy nyilvános IP-címet az operációs rendszeren belül.

## <a name="assignment-methods"></a>Hozzárendelési módszerek

A nyilvános és privát IP-címek hozzárendelése az alábbi hozzárendelési módszerek egyikével történik:

### <a name="dynamic"></a>Dinamikus

A dinamikus személyes IPv4- és IPv6-címek (opcionálisan) alapértelmezés szerint hozzá vannak rendelve.

- **Csak nyilvános:** Az Azure a címet az egyes Azure-régiókszámára egyedi tartományból rendeli hozzá. Ha meg szeretné tudni, hogy mely tartományok vannak hozzárendelve az egyes régiókhoz, olvassa el a [Microsoft Azure Datacenter IP-tartományok című témakört.](https://www.microsoft.com/download/details.aspx?id=41653) A cím megváltozhat, ha egy virtuális gép leáll (feloldva), majd újraindul. Nyilvános IPv6-cím nem rendelhet ip-konfigurációhoz egyik hozzárendelési módszerrel sem.
- **Csak magánjellegű:** Az Azure minden alhálózati címtartományban lefoglalja az első négy címet, és nem rendeli hozzá a címeket. Az Azure az alhálózat címtartományának egyik erőforrásához rendeli hozzá a következő elérhető címet. Például, ha az alhálózat címtartománya 10.0.0.0/16, és a 10.0.0.0.4-10.0.0.14 közötti címek már hozzá lettek rendelve (a .0–.3 címek fenn vannak tartva), az Azure az erőforráshoz rendeli a 10.0.0.15 címet. Az alapértelmezett lefoglalási módszer a dinamikus. Kiosztás után a dinamikus IP-címek csak a hálózati adapter törlésekor, a virtuális hálózaton belüli másik alhálózatra történő kiosztáskor vagy a kiosztási módszer statikusra váltása és másik IP-cím megadása esetén szabadulnak fel. Alapértelmezés szerint, amikor a lefoglalási módszert dinamikusról statikusra váltja, az Azure statikus címként osztja ki az előzőleg dinamikusan kiosztott címet. 

### <a name="static"></a>Statikus

Ip-konfigurációhoz (tetszés szerint) nyilvános vagy privát statikus IPv4- vagy IPv6-címet rendelhet. Ha többet szeretne tudni arról, hogy az Azure hogyan rendel statikus nyilvános IPv4-címeket, olvassa el a [Nyilvános IP-címek című témakört.](virtual-network-public-ip-address.md)

- **Csak nyilvános:** Az Azure a címet az egyes Azure-régiókszámára egyedi tartományból rendeli hozzá. Letöltheti a tartományok (előtagok) listáját az Azure [nyilvános](https://www.microsoft.com/download/details.aspx?id=56519), valamint [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) és [Germany](https://www.microsoft.com/download/details.aspx?id=57064) felhője esetében. A cím nem változik, amíg a nyilvános IP-cím erőforrás, amelyhez hozzá van rendelve, törlődik, vagy a hozzárendelési módszer dinamikusra. Ha a nyilvános IP-cím erőforrás IP-konfigurációhoz van társítva, a hozzárendelési módszer módosítása előtt el kell választani az IP-konfigurációtól.
- **Csak magánjellegű**: Az alhálózat címtartományából választ ki és rendel hozzá egy címet. A hozzárendelt cím az alhálózat címtartományán belül bármilyen cím lehet, amely nem tartozik az alhálózat címtartományának első négy címébe, és nincs hozzárendelve más erőforráshoz az alhálózatban. A statikus címek csak egy hálózati adapter törlése esetén szabadulnak fel. Ha statikusra módosítja a foglalási módszert, az Azure dinamikusan a korábban hozzárendelt dinamikus IP-címet rendeli statikus címként, még akkor is, ha a cím nem a következő elérhető cím az alhálózat címtartományában. A cím akkor is megváltozik, ha a hálózati adapter ugyanazon a virtuális hálózaton belül egy másik alhálózathoz lesz kiosztva, de ahhoz, hogy a hálózati adaptert egy másik alhálózathoz ossza ki, a kiosztási módszert először statikusról dinamikusra kell váltani. Miután hozzárendelte a hálózati adaptert egy másik alhálózathoz, a kiosztási módszer visszaváltható statikusra, és hozzárendelhet egy IP-címet az új alhálózat címtartományából.

## <a name="ip-address-versions"></a>IP-cím verziók

A címek hozzárendelésekénél a következő verziókadhatók meg:

### <a name="ipv4"></a>IPv4

Minden hálózati adapternek egy [elsődleges](#primary) IP-konfigurációval kell rendelkeznie hozzárendelt [magánHálózati](#private) [IPv4-címmel.](#ipv4) Hozzáadhat egy vagy több [másodlagos](#secondary) IP-konfigurációt, amelyek mindegyike rendelkezik egy IPv4-magán- és (opcionálisan) egy IPv4 [nyilvános](#public) IP-címmel.

### <a name="ipv6"></a>IPv6

A hálózati adapter ek egyik másodlagos IP-konfigurációjához nulla vagy egy privát [IPv6-címet](#ipv6) rendelhet. A hálózati adapter nem rendelkezhet meglévő másodlagos IP-konfigurációkkal. Minden hálózati adapterlegek számára lehet egy IPv6-privát cím. Az IPv6 hálózati adapter konfigurációjához hozzáadhat nyilvános IPv6-címet.

> [!NOTE]
> Bár a portál használatával iPv6-címmel rendelkező hálózati adaptert hozhat létre, a portál használatával nem adhat hozzá meglévő hálózati adaptert egy új vagy meglévő virtuális géphez. A PowerShell vagy az Azure CLI használatával hozzon létre egy hálózati adaptert egy privát IPv6-címmel, majd csatlakoztassa a hálózati adaptert egy virtuális gép létrehozásakor. Meglévő virtuális géphez rendelt magánhálózati IPv6-címmel rendelkező hálózati adapter nem csatolható. A virtuális gépekhez csatlakoztatott hálózati adapterek IP-konfigurációjához nem adhat hozzá privát IPv6-címet bármilyen eszközzel (portál, CLI vagy PowerShell).

Nyilvános IPv6-cím nem rendelhet elsődleges vagy másodlagos IP-konfigurációhoz.

## <a name="skus"></a>Termékváltozatok

Nyilvános IP-cím jön létre az alapszintű vagy szabványos termékváltozattal. A termékváltozat-különbségekről a [Nyilvános IP-címek kezelése című](virtual-network-public-ip-address.md)témakörben talál további információt.

> [!NOTE]
> Ha egy standard termékváltozatú nyilvános IP-címet hozzárendel egy virtuális gép hálózati adapteréhez, kifejezetten engedélyeznie kell a kívánt forgalmat egy [hálózati biztonsági csoporttal](security-overview.md#network-security-groups). Az erőforrással történő kommunikáció meghiúsul, amíg nem hoz létre és rendel hozzá egy hálózati biztonsági csoportot, és kifejezetten nem engedélyezi a kívánt forgalmat.

## <a name="next-steps"></a>További lépések
Ha különböző IP-konfigurációkkal rendelkező virtuális gépet szeretne létrehozni, olvassa el az alábbi cikkeket:

|Tevékenység|Eszköz|
|---|---|
|Több hálózati adapterrel rendelkező virtuális gép létrehozása|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Egyetlen hálózati adapter virtuális gép létrehozása több IPv4-címmel|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Egyetlen hálózati adapter virtuális gép létrehozása privát IPv6-címmel (egy Azure Load Balancer mögött)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager sablon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
