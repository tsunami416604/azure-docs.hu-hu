---
title: "Egy Azure hálózati illesztő IP-címek konfigurálása |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzáadása, módosítása és eltávolítása a privát és nyilvános IP-címek a hálózati illesztő."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: 478a2ebfa6a4cc504119734ac2f67b1f7c77dd5a
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Módosítsa, vagy távolítsa el az Azure hálózati illesztő IP-címek

Megtudhatja, hogyan hozzáadása, módosítása és eltávolítása a nyilvános és magánhálózati IP-címek a hálózati illesztő. Egy adott hálózati csatoló rendelt privát IP-címek engedélyezése egy virtuális gép más Azure virtuális hálózat és a hálózatokhoz csatlakozó erőforrásokkal folytatott kommunikációhoz. A magánhálózati IP-cím is lehetővé teszi, hogy a kimenő kommunikációt az internethez, előre nem látható IP-cím használatával. A [nyilvános IP-cím](virtual-network-public-ip-address.md) hozzárendelt hálózati illesztő lehetővé teszi bejövő kommunikációs egy virtuális géphez az internetről. A cím azt is lehetővé teszi, hogy a kimenő kommunikáció a virtuális gépről egy előre jelezhető IP-címet használja az internethez. További információkért lásd: [ismertetése az Azure-ban kimenő kapcsolatok](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Ha meg kell létrehozni, módosítsa vagy törölje a hálózati adaptert, olvassa el a [kezelheti egy adott hálózati csatoló](virtual-network-network-interface.md) cikk. Ha szeretné a hálózati adapterek hozzáadása vagy eltávolítása a hálózati adapterek virtuális gépről, olvassa el a [hozzáadása vagy eltávolítása a hálózati adapterek](virtual-network-network-interface-vm.md) cikk. 


## <a name="before-you-begin"></a>Előkészületek

Ez a cikk bármely szakaszának lépéseit befejezése előtt hajtsa végre a következőket:

- Ha még nem rendelkezik Azure-fiókja, regisztráljon egy [ingyenes próbafiók](https://azure.microsoft.com/free).
- Ha a portál használatával, nyissa meg a https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsokkal ebben a cikkben a feladatokat, vagy futtassa a parancsokat a [Azure Cloud rendszerhéj](https://shell.azure.com/powershell), vagy a PowerShell futtatásával a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ebben az oktatóanyagban az Azure PowerShell modul verziója 5.2.0 szükséges vagy újabb. Futtatás `Get-Module -ListAvailable AzureRM` telepített verziója található. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Azure parancssori felület (CLI) parancsok használata ebben a cikkben a feladatokat, vagy futtassa a parancsokat a [Azure Cloud rendszerhéj](https://shell.azure.com/bash), vagy a CLI-t a számítógépen való futtatásával. Ez az oktatóanyag az Azure parancssori felület 2.0.26 verziója szükséges, vagy később. Futtatás `az --version` telepített verziója található. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure parancssori felület, is futtatásához szükséges `az login` az Azure VPN-kapcsolat létrehozásához.

## <a name="add-ip-addresses"></a>IP-címek hozzáadása

Annyi adhat hozzá [titkos](#private) és [nyilvános](#public) [IPv4](#ipv4) szünteti meg, hogy egy adott hálózati csatoló felsorolt keretein belül a [Azure korlátozza](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) cikk. A portál használatával nem IPv6-cím hozzáadása egy meglévő hálózati illesztő (bár a portál segítségével saját IPv6-cím hozzáadása a hálózati illesztő hálózati kapcsolat létrehozásakor). Saját IPv6-cím hozzáadása egy használhatja PowerShell vagy a CLI [másodlagos IP-konfiguráció](#secondary) (feltéve, nincsenek nincs meglévő másodlagos IP-konfigurációk) egy meglévő hálózati adapter, amely nem csatlakozik egy virtuális gépet. Minden eszköz egy nyilvános IPv6-cím hozzáadása a hálózati illesztő nem használható. Lásd: [IPv6](#ipv6) IPv6-címek használatával. 

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a hálózat közreműködő szerepkört az előfizetés (minimum) hozzárendelt engedélyeit. Olvassa el a [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) cikk tudhat meg többet a szerepköröket és engedélyeket hozzárendelése a fiókokhoz.
2. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *hálózati illesztőt*. Ha **hálózati illesztőt** jelenik meg a keresési eredmények között kattintson rá.
3. Az a **hálózati illesztőt** panel, amelyen megjelenik, kattintson a hálózati illesztő hozzá szeretné adni egy IPv4-cím.
4. Kattintson a **IP-konfigurációk** a a **beállítások** részében találhatja a kiválasztott hálózati adapter.
5. Kattintson a **+ Hozzáadás** , amely megnyitja a panelen az IP-konfigurációhoz.
6. Adja meg a következő beállításokat, majd kattintson a **OK** bezárásához a **hozzáadása IP-konfiguráció** panel:

    |Beállítás|Kötelező?|Részletek|
    |---|---|---|
    |Name (Név)|Igen|A hálózati illesztő egyedinek kell lennie|
    |Típus|Igen|Mivel a meglévő hálózati illesztő IP-konfigurációt adunk, és mindegyik hálózati interfész rendelkeznie kell egy [elsődleges](#primary) IP-konfiguráció egyetlen választása marad: **másodlagos**.|
    |Privát IP-cím hozzárendelési módszert|Igen|[**Dinamikus**](#dynamic): Azure rendeli hozzá a következő elérhető címek a alhálózati címtartomány a hálózati adapter van telepítve. [**Statikus**](#static): az alhálózati címtartományt, a hálózati adapter van telepítve a nem használt címet hozzárendelni.|
    |Nyilvános IP-cím|Nem|**Letiltva:** nincs nyilvános IP-cím erőforrás IP-konfigurációja jelenleg társítva. **Engedélyezve:** válasszon ki egy meglévő IPv4 nyilvános IP-címet, vagy hozzon létre egy újat. A nyilvános IP-cím létrehozása, olvassa el a [nyilvános IP-címek](virtual-network-public-ip-address.md#create-a-public-ip-address) cikk.|
7. Manuálisan adja hozzá másodlagos magánhálózati IP-címek a virtuális gép operációs rendszerének; Ehhez hajtsa végre a utasításait a [több IP-címek hozzárendelése a virtuális gép operációs rendszerek](virtual-network-multiple-ip-addresses-portal.md#os-config) cikk. Lásd: [titkos](#private) IP-címek előtt manuálisan IP-címek hozzáadása a virtuális gép operációs rendszerének különleges szempontjait. Ne vegyen fel nyilvános IP-címek a virtuális gép operációs rendszerének.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az hálózati hálózati adapter ip-konfiguráció létrehozása](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_ip_config_create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-ip-address-settings"></a>IP-cím beállításainak módosítása

Akkor lehet, hogy kell az IPv4-cím, a hozzárendelés módszerének módosítása módosíthatja statikus IPv4-címet, vagy a nyilvános IP-cím, egy adott hálózati csatoló rendelt. Ha módosítja a privát IPv4-cím, egy másodlagos IP-konfiguráció, a másodlagos hálózati adaptert egy virtuális gép társított (További információ [elsődleges és másodlagos hálózati adapterek](virtual-network-network-interface-vm.md)), helyezze a virtuális gépet a leállított (felszabadított) állapot előtt kövesse az alábbi lépéseket: 

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a hálózat közreműködő szerepkört az előfizetés (minimum) hozzárendelt engedélyeit. Olvassa el a [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) cikk tudhat meg többet a szerepköröket és engedélyeket hozzárendelése a fiókokhoz.
2. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *hálózati illesztőt*. Ha **hálózati illesztőt** jelenik meg a keresési eredmények között kattintson rá.
3. Az a **hálózati illesztőt** panel, amelyen megjelenik, kattintson a hálózati illesztő szeretné megtekinteni vagy módosítani az IP-címbeállítások.
4. Kattintson a **IP-konfigurációk** a a **beállítások** részében találhatja a kiválasztott hálózati adapter.
5. Kattintson a listából, amely megnyitja a panel az IP-konfiguráció a módosítani kívánt IP-konfigurációja.
6. Módosítsa a beállításokat, szükség szerint, a beállításaival kapcsolatos információk segítségével a 6. lépésében a [adja hozzá egy IP-konfiguráció](#create-ip-config) című szakaszát. Kattintson a **mentése** a módosított IP-konfiguráció panel bezárásához.

>[!NOTE]
>Ha az elsődleges hálózati adapter több IP-konfigurációk és módosíthatja az elsődleges IP-konfiguráció magánhálózati IP-címe, akkor manuálisan kell újra hozzárendelnie az elsődleges és másodlagos IP-címek a hálózati illesztő a Windows (Linux nem kötelező) . Ha manuálisan IP-címek kiosztása a hálózati adaptert, az operációs rendszerben, olvassa el a [több IP-címek hozzárendelése a virtuális gépek](virtual-network-multiple-ip-addresses-portal.md#os-config) cikk. Lásd: [titkos](#private) IP-címek előtt manuálisan IP-címek hozzáadása a virtuális gép operációs rendszerének különleges szempontjait. Ne vegyen fel nyilvános IP-címek a virtuális gép operációs rendszerének.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az hálózat hálózati adapter ip-konfiguráció frissítése](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_ip_config_update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-ip-addresses"></a>Távolítsa el az IP-címek

Eltávolíthatja [titkos](#private) és [nyilvános](#public) hálózati illesztő IP-címet, de egy adott hálózati csatoló mindig rendelkeznie kell legalább egy privát IPv4-cím hozzárendelve.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a hálózat közreműködő szerepkört az előfizetés (minimum) hozzárendelt engedélyeit. Olvassa el a [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) cikk tudhat meg többet a szerepköröket és engedélyeket hozzárendelése a fiókokhoz.
2. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *hálózati illesztőt*. Ha **hálózati illesztőt** jelenik meg a keresési eredmények között kattintson rá.
3. Az a **hálózati illesztőt** panel, amelyen megjelenik, kattintson a hálózati illesztő el szeretné távolítani az IP-címek a.
4. Kattintson a **IP-konfigurációk** a a **beállítások** részében találhatja a kiválasztott hálózati adapter.
5. Kattintson a jobb gombbal egy [másodlagos](#secondary) IP-konfiguráció (nem lehet törölni a [elsődleges](#primary) konfigurációs) szeretne törölni, kattintson a **törlése**, majd kattintson a **Igen** gombra a törlés jóváhagyásához. Ha a konfigurációban a hozzá tartozó nyilvános IP-cím erőforrás szerepel, az erőforrás van elválasztja az IP-konfiguráció, de az erőforrás nem törlődik.
6. Zárja be a **IP-konfigurációk** panelen.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az hálózati hálózati adapter ip-konfiguráció törlése](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_ip_config_delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-configurations"></a>IP-konfigurációk

[Személyes](#private) és (opcionálisan) [nyilvános](#public) IP-címek vannak rendelve egy vagy több hozzárendelt hálózati illesztő IP-konfigurációk. Az IP-konfigurációjának két típusa van:

### <a name="primary"></a>Elsődleges

Mindegyik hálózati interfész hozzá van rendelve egy elsődleges IP-konfigurációval. Egy elsődleges IP-konfiguráció:

- Rendelkezik egy [titkos](#private) [IPv4](#ipv4) cím. Nem rendelhet hozzá egy olyan magánhálózat [IPv6](#ipv6) cím egy elsődleges IP-konfigurációra.
- Is egy [nyilvános](#public) IPv4-cím hozzárendelve. Nem rendelhet egy nyilvános IPv6-cím egy elsődleges vagy másodlagos IP-konfigurációt. Ugyanakkor a nyilvános IPv6-cím hozzárendelése az Azure terheléselosztó, amely be tudják tölteni oszthatja el a forgalmat a virtuális gép saját IPv6-címére. További információkért lásd: [részleteit és az IPv6 korlátozások](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

### <a name="secondary"></a>Másodlagos

Egy elsődleges IP-konfiguráció mellett egy adott hálózati csatoló lehet nulla vagy több másodlagos IP-konfigurációk rendelve. Egy másodlagos IP-konfiguráció:

- Kell egy privát IPv4 vagy IPv6-címet rendelte hozzá. Ha a cím IPv6, a hálózati illesztő csak van egy másodlagos IP-konfigurációval. Ha a cím IPv4, a hálózati adapter rendelkezhet több másodlagos IP-konfigurációk rendelve. Hány privát és nyilvános IPv4-címek rendelhetők hozzá egy adott hálózati csatoló kapcsolatos további tudnivalókért tekintse meg a [Azure korlátozza](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) cikk.  
- Előfordulhat, hogy is egy nyilvános IPv4-cím hozzá rendelt, ha a magánhálózati IP-cím IPv4-alapú. Ha a magánhálózati IP-cím IPv6-alapú, az IP-konfiguráció nem rendelhet egy nyilvános IPv4- vagy IPv6-címet. Több IP-cím hozzárendelése egy adott hálózati csatoló a következő esetekben hasznos, mint:
    - Több webhely vagy szolgáltatás üzemeltetése különböző IP-címekkel és SSL-tanúsítványokkal egyetlen kiszolgálón.
    - Egy virtuális gépet, a hálózati virtuális készülék, például egy tűzfal vagy terheléselosztó szolgál.
    - A titkos IPv4-címek bármely, a hálózati adapterek hozzáadása egy Azure Load Balancer háttér címkészletet lehetősége. Korábban csak az elsődleges IPv4-cím elsődleges hálózati adapterének sikerült felvenni egy háttér címkészletet. További információt a terhelés elosztása érdekében több IPv4-konfiguráció, lásd: a [terheléselosztás több IP-konfigurációk](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cikk. 
    - Képes betölteni egy IPv6-cím, egy adott hálózati csatoló rendelt elosztása. Saját IPv6-címére terheléselosztásáról kapcsolatos további tudnivalókért tekintse meg a [IPv6-címek terheléselosztásához](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cikk.


## <a name="address-types"></a>Cím típusa

A következő típusú IP-címek rendelhet egy [IP-konfiguráció](#ip-configurations):

### <a name="private"></a>Saját

Személyes [IPv4](#ipv4) címek engedélyezése egy virtuális gép virtuális hálózat vagy más hálózatokhoz csatlakozó egyéb erőforrások folytatott kommunikációhoz. Egy virtuális gépet nem lehetett továbbítani a bejövő, sem a virtuális gép kommunikálhatnak egy olyan magánhálózat a kimenő [IPv6](#ipv6) cím, egy kivétellel. Egy virtuális gépet az Azure load balancer egy IPv6-cím használatával kommunikálhatnak. További információkért lásd: [részleteit és az IPv6 korlátozások](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations). 

Alapértelmezés szerint az Azure DHCP-kiszolgálók a privát IPv4-cím hozzárendelése a [elsődleges IP-konfiguráció](#primary) Azure hálózati illesztő a virtuális gép operációs rendszerben a hálózati illesztőhöz. Ha szükséges, akkor kell soha nem manuális módon állítsa be a virtuális gép operációs rendszerében a hálózati illesztő IP-címét. 

> [!WARNING]
> Ha az elsődleges IP-címét a hálózati adaptert egy virtuális gép operációs rendszerből állítja be az IPv4-cím legalább egyszer eltér a privát IPv4-cím, az elsődleges IP-konfigurációja az elsődleges hálózati adapter rendelt csatolva van egy virtuális gépet az Azure akkor megszakad a kapcsolat a virtuális géphez.

Nincsenek forgatókönyvekben, ahol beállítani a virtuális gép operációs rendszerében a hálózati illesztő IP-címét. Például manuálisan be kell az elsődleges és másodlagos IP-címek, a Windows operációs rendszer amikor több IP-címet ad hozzá egy Azure virtuális gépen. A Linux virtuális gép csak szükség lehet a másodlagos IP-címek kézi beállítására. Lásd: [hozzáadása IP-címek egy virtuális gép operációs rendszerre](virtual-network-multiple-ip-addresses-portal.md#os-config) részleteiről. Ha valaha is módosítani szeretné az IP-konfigurációt rendelt cím, javasoljuk, hogy:

1. Győződjön meg arról, hogy a virtuális gép egy címet fogadja az Azure DHCP-kiszolgálók. Miután, állítsa vissza az IP-cím hozzárendelése DHCP operációs rendszerből, és indítsa újra a virtuális gépet.
2. Állítsa le (felszabadítása) a virtuális gép.
3. Az IP-konfiguráció Azure-ban az IP-címének módosítása.
4. Virtuális gép elindítása.
5. [Manuálisan konfigurálnia a](virtual-network-multiple-ip-addresses-portal.md#os-config) az operációs rendszer (és belül is a Windows az elsődleges IP-cím) megfelelően beállítani az Azure másodlagos IP-címét.
 
Az előző lépések, a magánhálózati IP-címet hozzárendelni az Azure-ban, és a virtuális gép operációs rendszerben, a hálózati adapter által változatlanok maradnak. Nyomon követéséhez belül az előfizetés, amely a manuálisan beállított IP-címek az operációs rendszerben a virtuális gépek, fontolja meg az Azure [címke](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags) virtuális gépekhez. Használhatja a "IP-cím hozzárendelése: statikus", például. Ezzel a módszerrel könnyedén megtalálhatja a virtuális gépek, amelyek manuálisan beállította az operációs rendszerben az IP-cím az előfizetésen belül.

Egy virtuális gép más erőforrások, az azonos, vagy csatlakoztatott virtuális hálózatokon belül kommunikálni engedélyezniük, egy magánhálózati IP-cím is lehetővé teszi, hogy való kommunikációhoz az internethez kimenő virtuális gép. Kifelé irányuló kapcsolatok olyan forrás hálózati címe lefordítani az Azure előre nem látható nyilvános IP-címet. Az Azure kimenő internetkapcsolattal kapcsolatos további tudnivalókért olvassa el a [Azure kimenő internetkapcsolat](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cikk. Nem lehet kommunikálni a bejövő egy virtuális gép privát IP-címre az interneten. Ha a kimenő kapcsolatok egy előre jelezhető nyilvános IP-címet igényel, társítson egy nyilvános IP-cím erőforrás hálózati illesztőhöz.

### <a name="public"></a>Nyilvános

Nyilvános IP-címtartományból keresztül egy nyilvános IP-cím erőforrás az internetről bejövő kapcsolat engedélyezése egy virtuális géphez. Kimenő kapcsolatok az interneten egy előre jelezhető IP-címet használja. Lásd: [ismertetése az Azure-ban kimenő kapcsolatok](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) részleteiről. Előfordulhat, hogy a nyilvános IP-címet rendel egy IP-konfiguráció, de nem szükséges. Ha egy nyilvános IP-cím erőforrás társításával egy nyilvános IP-cím ne rendelje hozzá egy virtuális gépet, a virtuális gép továbbra is kommunikálhatnak kimenő internetkapcsolat. Ebben az esetben a magánhálózati IP-címet az forrás hálózati cím lefordítani az Azure előre nem látható nyilvános IP-címet. Nyilvános IP-cím erőforrásokkal kapcsolatos további információkért lásd: [nyilvános IP-cím erőforrás](virtual-network-public-ip-address.md).

Nincsenek korlátozások rendelhet egy adott hálózati csatoló privát és nyilvános IP-címek számát. További információkért olvassa el a [Azure korlátozza](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) cikk.

> [!NOTE]
> Azure fordítja le a virtuális gépek magánhálózati IP-cím egy nyilvános IP-cím. Ennek eredményeképpen a virtuális gép operációs rendszer nem észleli a bármely nyilvános IP-cím hozzárendelve, így nincs szükség minden eddiginél rendelheti hozzá kézzel az operációs rendszer egy nyilvános IP-címnek.

## <a name="assignment-methods"></a>Hozzárendelési módszert

Nyilvános és magánhálózati IP-címek hozzá a következő hozzárendelési módszerek egyikének használatával:

### <a name="dynamic"></a>Dinamikus

Dinamikus privát IPv4 és IPv6-alapú (opcionális) címek alapértelmezés szerint vannak hozzárendelve. 

- **Csak nyilvános**: Azure tartomány rendeli hozzá a cím egyedi az egyes Azure-régiót. Mely tartományok rendelt minden egyes régió, lásd: [Microsoft Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653). A cím módosíthatja, ha egy virtuális gép leállított (felszabadított), majd újra el. Nem rendelhet egy nyilvános IPv6-cím hozzárendelés módszerek használatával IP-konfigurációt.
- **Csak a saját**: Azure fenntartja az egyes alhálózati címtartományt első négy címeit, és nem a címeket rendelni. Az Azure az alhálózat címtartományának egyik erőforrásához rendeli hozzá a következő elérhető címet. Például, ha az alhálózat címtartománya 10.0.0.0/16, és a 10.0.0.0.4-10.0.0.14 közötti címek már hozzá lettek rendelve (a .0–.3 címek fenn vannak tartva), az Azure az erőforráshoz rendeli a 10.0.0.15 címet. Az alapértelmezett lefoglalási módszer a dinamikus. Kiosztás után a dinamikus IP-címek csak a hálózati adapter törlésekor, a virtuális hálózaton belüli másik alhálózatra történő kiosztáskor vagy a kiosztási módszer statikusra váltása és másik IP-cím megadása esetén szabadulnak fel. Alapértelmezés szerint, amikor a lefoglalási módszert dinamikusról statikusra váltja, az Azure statikus címként osztja ki az előzőleg dinamikusan kiosztott címet. Csak egy titkos IPv6-címet, dinamikus hozzárendelése módszerrel rendelhet hozzá.

### <a name="static"></a>Statikus

IP-konfigurációt rendelhet a saját vagy nyilvános statikus IPv4-cím (nem kötelező). Nem rendelhető hozzá egy statikus nyilvános vagy privát IPv6-címet az IP-konfigurációt. Hogyan Azure rendel statikus nyilvános IPv4-címek kapcsolatos további tudnivalókért tekintse meg a [nyilvános IP-cím](virtual-network-public-ip-address.md) cikk.

- **Csak nyilvános**: Azure tartomány rendeli hozzá a cím egyedi az egyes Azure-régiót. Mely tartományok rendelt minden egyes régió, lásd: [Microsoft Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653). A cím nem változik, amíg a rendszer törli a nyilvános IP-cím erőforrás hozzá van rendelve, vagy dinamikus megváltozott a hozzárendelési módszert. Ha a nyilvános IP-cím erőforrás tartozik, az IP-konfigurációt, az IP-konfigurációja a hozzárendelési módszert módosítása előtt kell leválasztása.
- **Csak a saját**: válassza ki, és az alhálózati címtartományt a címet hozzárendelni. A hozzárendelt cím az alhálózat címtartományán belül bármilyen cím lehet, amely nem tartozik az alhálózat címtartományának első négy címébe, és nincs hozzárendelve más erőforráshoz az alhálózatban. A statikus címek csak egy hálózati adapter törlése esetén szabadulnak fel. Amennyiben a kiosztási módszert statikusra váltja, az Azure az előzőleg hozzárendelt statikus IP-címeket dinamikus IP-címként osztja ki akkor is, ha a cím nem az alhálózat címtartományának következő elérhető címe. A cím akkor is megváltozik, ha a hálózati adapter ugyanazon a virtuális hálózaton belül egy másik alhálózathoz lesz kiosztva, de ahhoz, hogy a hálózati adaptert egy másik alhálózathoz ossza ki, a kiosztási módszert először statikusról dinamikusra kell váltani. Miután hozzárendelte a hálózati adaptert egy másik alhálózathoz, a kiosztási módszer visszaváltható statikusra, és hozzárendelhet egy IP-címet az új alhálózat címtartományából.

## <a name="ip-address-versions"></a>IP-cím verziók

A következő verziók cím hozzárendelésekor adhatja meg:

### <a name="ipv4"></a>IPv4

Mindegyik hálózati interfész rendelkeznie kell egy [elsődleges](#primary) egy hozzárendelt IP-beállítását [titkos](#private) [IPv4](#ipv4) cím. Hozzáadhat egy vagy több [másodlagos](#secondary) IP-konfiguráció magánhálózati IPv4- és (opcionálisan) IPv4 tartalmazó [nyilvános](#public) IP-címet.

### <a name="ipv6"></a>IPv6

Nulla vagy egy személyes rendelhet [IPv6](#ipv6) címét, hogy egy hálózati adapter egy másodlagos IP-konfigurációval. A hálózati adapter nem rendelkezhet minden meglévő másodlagos IP-konfigurációt. Nem adható hozzá egy IP-konfiguráció a portál használatával IPv6-címmel. Használja a Powershellt vagy a parancssori felület egy IP-konfiguráció magánhálózati IPv6-cím hozzáadása a meglévő hálózati illesztő. A hálózati adapter nem lehet csatolni, egy meglévő virtuális gépre.

> [!NOTE]
> Bár létrehozhat egy adott hálózati csatoló a portál használatával IPv6-címmel, nem adhat egy meglévő hálózati illesztő egy új vagy meglévő virtuális gépen, a portál használatával. PowerShell vagy az Azure CLI 2.0 használatával hozzon létre egy adott hálózati csatoló magán IPv6-címet, majd a hálózati adapter csatlakoztatása egy virtuális gép létrehozásakor. Egy meglévő virtuális géphez rendelt titkos IPv6-címmel rendelkező hálózati illesztő nem lehet csatolni. Bármely olyan eszközöket (portál, parancssori felületen vagy a PowerShell) segítségével virtuális géphez csatlakozik hálózati illesztő IP-konfigurációt a saját IPv6-cím nem lehet hozzáadni.

Nem rendelhet egy nyilvános IPv6-cím egy elsődleges vagy másodlagos IP-konfigurációt.

## <a name="skus"></a>Termékváltozatok

Egy nyilvános IP-címet az alap vagy standard Termékváltozat hozza létre.  Termékváltozat különbségekről további részletekért lásd: [nyilvános IP-címeinek kezelése](virtual-network-public-ip-address.md).

> [!NOTE]
> Ha egy standard termékváltozatú nyilvános IP-címet hozzárendel egy virtuális gép hálózati adapteréhez, kifejezetten engedélyeznie kell a kívánt forgalmat egy [hálózati biztonsági csoporttal](security-overview.md#network-security-groups). Az erőforrással történő kommunikáció meghiúsul, amíg nem hoz létre és rendel hozzá egy hálózati biztonsági csoportot, és kifejezetten nem engedélyezi a kívánt forgalmat.

## <a name="next-steps"></a>További lépések
Virtuális gép létrehozása a különböző IP-konfigurációk, olvassa el a következő cikkeket:

|Tevékenység|Eszköz|
|---|---|
|Több hálózati adapterrel rendelkező virtuális gép létrehozása|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Hozzon létre egy hálózati adapter virtuális több IPv4-címekkel|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Hozzon létre egy hálózati adapter virtuális magánhálózati IPv6-cím (mögött egy Azure Load Balancer)|[Parancssori felület](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-sablon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
