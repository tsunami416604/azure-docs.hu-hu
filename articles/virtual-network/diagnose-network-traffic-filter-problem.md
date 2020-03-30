---
title: Virtuálisgép hálózati forgalomszűrőjének diagnosztizálása | Microsoft dokumentumok
description: Ismerje meg, hogyan diagnosztizálhatja a virtuális gép hálózati forgalomszűrő problémáját a virtuális gépek hatékony biztonsági szabályainak megtekintésével.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: kumud
ms.openlocfilehash: 6939ea2497a9f12321e1a6dfb9bf9fbb353bc7db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240779"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Virtuálisgép hálózati forgalomszűrőjének diagnosztizálása

Ebben a cikkben megtudhatja, hogyan diagnosztizálhatja a hálózati forgalom szűrő probléma megtekintésével a hálózati biztonsági csoport (NSG) biztonsági szabályok, amelyek hatékonyak a virtuális gép (VM).

Az NSG-k lehetővé teszik a virtuális gépekbe be- és kiáramló forgalom típusainak szabályozását. NSG-t társíthat egy alhálózathoz egy Azure virtuális hálózatban, egy virtuális géphez csatlakoztatott hálózati adapterben, vagy mindkettőben. A hálózati adapterre alkalmazott hatékony biztonsági szabályok a hálózati adapterhez társított NSG-ben és a hálózati csatoló alhálózatában található szabályok összesítése. A különböző NSG-k ben lévő szabályok néha ütközhetnek egymással, és hatással lehetnek a virtuális gép hálózati kapcsolatára. Megtekintheti az nsg-k összes hatékony biztonsági szabályát, amelyek a virtuális gép hálózati felületein vannak alkalmazva. Ha nem ismeri a virtuális hálózat, a hálózati adapter vagy az NSG-fogalmakat, olvassa el a [Virtuális hálózat áttekintése](virtual-networks-overview.md), [A hálózati adapter](virtual-network-network-interface.md)és a Hálózati biztonsági csoportok [áttekintése című témakört.](security-overview.md)

## <a name="scenario"></a>Forgatókönyv

Az internetről megpróbál csatlakozni egy virtuális géphez a 80-as porton keresztül, de a kapcsolat sikertelen. Annak megállapításához, hogy miért nem érhető el a 80-as port az internetről, megtekintheti a hálózati adapter ek érvényes biztonsági szabályait az Azure [Portal](#diagnose-using-azure-portal), a [PowerShell](#diagnose-using-powershell)vagy az [Azure CLI](#diagnose-using-azure-cli)használatával.

A következő lépések feltételezik, hogy rendelkezik egy meglévő virtuális gép a hatályos biztonsági szabályok megtekintéséhez. Ha nem rendelkezik egy meglévő virtuális gép, először telepítse n [a Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [a Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gép a jelen cikkben szereplő feladatok elvégzéséhez. A példák ebben a cikkben egy virtuális gép nevű *myVM* nevű hálózati adapter nevű *myVMVMNic*. A virtuális gép és a hálózati adapter egy *myResourceGroup*nevű erőforráscsoportban található, és az *USA keleti régiójában* található. Módosítsa az értékeket a lépésekben, a probléma diagnosztizálása érdekében a virtuális gép.

## <a name="diagnose-using-azure-portal"></a>Diagnosztizálás az Azure Portal használatával

1. Jelentkezzen be az Azure [Portalra](https://portal.azure.com) egy azure-fiókkal, amely rendelkezik a [szükséges engedélyekkel.](virtual-network-network-interface.md#permissions)
2. Az Azure Portal tetején adja meg a virtuális gép nevét a keresőmezőbe. Amikor a virtuális gép neve megjelenik a keresési eredmények között, jelölje ki azt.
3. A **BEÁLLÍTÁSOK**csoportban válassza a **Hálózat**lehetőséget, ahogy az az alábbi képen látható:

   ![Biztonsági szabályok megtekintése](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   Az előző képen látható szabályok a **myVMVMNic**nevű hálózati adapterre vonatkoznak. Láthatja, hogy két különböző hálózati biztonsági csoport **bejövő portszabályai** vannak a hálózati kapcsolathoz:
   
   - **mySubnetNSG**: Ahhoz az alhálózathoz társítva, amelyben a hálózati illesztő található.
   - **myVMNSG**: A virtuális gép **myVMVMNic**nevű hálózati interfészéhez társítva.

   A **DenyAllInBound** nevű szabály megakadályozza a bejövő kommunikációt a virtuális géphez a 80-as porton keresztül, az internetről, a [forgatókönyvben](#scenario)leírtak szerint. A szabály a *0.0.0.0/0-t* sorolja fel a **FORRÁS**, amely magában foglalja az internetet is. Nincs más magasabb prioritású szabály (alacsonyabb szám) a 80-as port bejövő számát. Ha engedélyezni szeretné, hogy a 80-as port az internetről bejövő a virtuális gépre, [olvassa el a Probléma megoldása című témakört.](#resolve-a-problem) Ha többet szeretne tudni a biztonsági szabályokról és arról, hogy az Azure hogyan alkalmazza őket, olvassa el a Hálózati biztonsági csoportok ( Hálózati biztonsági csoportok ) ( [Hálózati biztonsági csoportok](security-overview.md)) ( További információ).

   A kép alján a KIMENŐ **PORTSZABÁLYOK**is látható. Ez alatt a hálózati csatoló kimenő portszabályai találhatók. Bár a képen csak négy bejövő szabályok minden NSG, az NSG-k sokkal több, mint négy szabályt. A képen a **VirtualNetwork** a **FORRÁS** és **a DESTINATION,** az **AzureLoadBalancer** **csoportban**található. **A VirtualNetwork** és **az AzureLoadBalancer** [szolgáltatáscímkék.](security-overview.md#service-tags) A szolgáltatáscímkék IP-címelőtagok csoportját jelölik, így minimálisra csökkenthető a biztonsági szabályok létrehozása.

4. Győződjön meg arról, hogy a virtuális gép fut, majd válassza **a Hatékony biztonsági szabályok**lehetőséget , ahogy az előző képen látható, az alábbi képen látható hatékony biztonsági szabályok megtekintéséhez:

   ![Hatékony biztonsági szabályok megtekintése](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   A felsorolt szabályok ugyanazok, mint a 3. Mint látható a képen, csak az első 50 szabályok jelennek meg. Az összes szabályt tartalmazó .csv fájl letöltéséhez válassza a **Letöltés**lehetőséget.

   Az egyes szolgáltatáscímkek által képviselt előtagok megtekintéséhez jelöljön ki egy szabályt, például az **AllowAzureLoadBalancerInbound**nevű szabályt. Az alábbi képen az **AzureLoadBalancer** szolgáltatáscímke előtaglátható:

   ![Hatékony biztonsági szabályok megtekintése](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Bár az **AzureLoadBalancer** szolgáltatáscímke csak egy előtagot jelöl, más szolgáltatáscímkék több előtagot képviselnek.

5. Az előző lépések a **myVMVMNic**nevű hálózati illesztő biztonsági szabályait mutatták be, de az előző képekben egy **myVMVMNic2** nevű hálózati illesztő is látható. A példában szereplő virtuális géphez két hálózati adapter van csatolva. A hatékony biztonsági szabályok az egyes hálózati adapterek esetében eltérőek lehetnek.

   A **myVMVMNic2** hálózati adapter szabályainak megtekintéséhez jelölje ki azt. Ahogy az a következő képen látható, a hálózati adapter alhálózatához ugyanazok a szabályok társítva vannak, mint a **myVMVMNic** hálózati csatoló, mivel mindkét hálózati adapter ugyanabban az alhálózatban található. Amikor egy NSG-t egy alhálózathoz társít, a szabályok az alhálózat összes hálózati kapcsolatára vonatkoznak.

   ![Biztonsági szabályok megtekintése](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   A **myVMVMNic** hálózati adapterrel ellentétben a **myVMVMNic2** hálózati csatolóhoz nem tartozik hálózati biztonsági csoport. Minden hálózati adapterhez és alhálózathoz nulla vagy egy NSG tartozhat. Az egyes hálózati adapterekhez vagy alhálózatokhoz társított NSG lehet azonos vagy eltérő. Ugyanazt a hálózati biztonsági csoportot annyi hálózati adapterhez és alhálózathoz társíthatja, amennyit csak akar.

Bár a hatékony biztonsági szabályokat a virtuális gépen keresztül tekintették meg, a hatékony biztonsági szabályokat egy adott személyen keresztül is megtekintheti:
- **Hálózati adapter**: További információ a [hálózati adapter ek megtekintéséről.](virtual-network-network-interface.md#view-network-interface-settings)
- **NSG:** Ismerje meg, hogyan kell [megtekinteni egy NSG](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnosztizálás a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Futtathatja az Azure Cloud [Shellben](https://shell.azure.com/powershell)követett parancsokat, vagy a PowerShell t a számítógépről futtathatja. Az Azure Cloud Shell egy ingyenes interaktív rendszerhéj. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a PowerShell t a számítógépről futtatja, szüksége van az Azure PowerShell-modul 1.0.0-s vagy újabb verzióra. Futtassa `Get-Module -ListAvailable Az` a számítógépen, hogy megtalálja a telepített verziót. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, `Connect-AzAccount` akkor is futnia kell, hogy olyan fiókkal jelentkezzen be az Azure-ba, amely rendelkezik a [szükséges engedélyekkel](virtual-network-network-interface.md#permissions).

A [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)hálózati adapterek hatékony biztonsági szabályainak beszereznie. A következő példa a *myVMVMNic*nevű hálózati adapter hatékony biztonsági szabályait kapja meg, amely egy *myResourceGroup*nevű erőforráscsoportban található:

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

A kimenet json formátumban kerül visszaadásra. A kimenet megértéséhez olvassa el a parancskimenet értelmezése című [témakört.](#interpret-command-output)
A kimenet csak akkor ad vissza, ha nsg van társítva a hálózati adapterhez, a hálózati adapter alhálózatához, vagy mindkettőhöz. A virtuális gépnek futó állapotban kell lennie. A virtuális gép több hálózati adapterrel is rendelkezhet különböző NSG-kkel. Hibaelhárításkor futtassa az egyes hálózati adapterek parancsát.

Ha továbbra is kapcsolódási probléma jelentkezik, tekintse meg a [további diagnózist](#additional-diagnosis) és [szempontokat.](#considerations)

Ha nem tudja a hálózati adapter nevét, de tudja annak a virtuális gépnek a nevét, amelyhez a hálózati adapter csatlakozik, a következő parancsok a virtuális géphez csatlakoztatott összes hálózati csatoló azonosítóit adják vissza:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

A következő példához hasonló kimenetet kap:

```output
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

Az előző kimenetben a hálózati csatoló neve *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnosztizálás az Azure CLI használatával

Ha az Azure parancssori felület (CLI) parancsokkal hajthatja végre a cikkben szereplő feladatokat, futtassa a parancsokat az [Azure Cloud Shellben,](https://shell.azure.com/bash)vagy a CLI futtatásával a számítógépről. Ez a cikk az Azure CLI 2.0.32-es vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI-t, akkor a szükséges `az login` [engedélyekkel](virtual-network-network-interface.md#permissions)rendelkező fiókkal is futtatnia kell és be kell jelentkeznie az Azure-ba.

Az [az hálózati adapter-effektív-nSg](/cli/azure/network/nic#az-network-nic-list-effective-nsg)hálózati adapterek hatékony biztonsági szabályainak beolvasása. A következő példa a *myVMVMNic nevű, myResourceGroup* nevű erőforráscsoportban található hálózati adapter ek érvényes biztonsági szabályait *kapja meg:*

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

A kimenet json formátumban kerül visszaadásra. A kimenet megértéséhez olvassa el a parancskimenet értelmezése című [témakört.](#interpret-command-output)
A kimenet csak akkor ad vissza, ha nsg van társítva a hálózati adapterhez, a hálózati adapter alhálózatához, vagy mindkettőhöz. A virtuális gépnek futó állapotban kell lennie. A virtuális gép több hálózati adapterrel is rendelkezhet különböző NSG-kkel. Hibaelhárításkor futtassa az egyes hálózati adapterek parancsát.

Ha továbbra is kapcsolódási probléma jelentkezik, tekintse meg a [további diagnózist](#additional-diagnosis) és [szempontokat.](#considerations)

Ha nem tudja a hálózati adapter nevét, de tudja annak a virtuális gépnek a nevét, amelyhez a hálózati adapter csatlakozik, a következő parancsok a virtuális géphez csatlakoztatott összes hálózati csatoló azonosítóit adják vissza:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

A visszaadott kimeneten belül a következő példához hasonló információk jelennek meg:

```output
"networkProfile": {
    "additionalProperties": {},
    "networkInterfaces": [
      {
        "additionalProperties": {},
        "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
        "primary": true,
        "resourceGroup": "myResourceGroup"
      },
```

Az előző kimenetben a hálózati csatoló neve *myVMVMNic csatoló*.

## <a name="interpret-command-output"></a>Parancskimenet értelmezése

Függetlenül attól, hogy a [PowerShellt](#diagnose-using-powershell)vagy az [Azure CLI-t](#diagnose-using-azure-cli) használta-e a probléma diagnosztizálására, a következő információkat tartalmazó kimenetet kapja:

- **NetworkSecurityGroup**: A hálózati biztonsági csoport azonosítója.
- **Társítás**: A hálózati biztonsági csoport *hálózati kapcsolathoz* vagy *alhálózathoz*van-e társítva. Ha mindkettőhöz NSG van társítva, a kimenetet a NetworkSecurityGroup , **Association**és **EffectiveSecurityRules**( **NetworkSecurityGroup**) adja vissza minden NSG-hez. Ha az NSG közvetlenül a parancs futtatása előtt van társítva vagy nincs társítva a hatékony biztonsági szabályok megtekintéséhez, előfordulhat, hogy várnia kell néhány másodpercet, amíg a módosítás tükröződni fog a parancs kimenetében.
- **EffectiveSecurityRules**: Az egyes tulajdonságok magyarázatát a [Biztonsági szabály létrehozása](manage-network-security-group.md#create-a-security-rule)című részben részletezi. A *defaultSecurityRules/* előzött szabálynevek olyan alapértelmezett biztonsági szabályok, amelyek minden NSG-ben léteznek. A *securityRules/* előzött szabálynevek a létrehozott szabályok. A **célCímPrefix** vagy **sourceAddressPrefix** tulajdonságokhoz [szolgáltatáscímkét](security-overview.md#service-tags)(például **Internet**, **VirtualNetwork**és **AzureLoadBalancer)** megadó szabályok szintén értékekkel rendelkeznek a **expandaddressAddressPrefix** tulajdonsághoz. A **expandedDestinationAddressPrefix** tulajdonság felsorolja a szolgáltatáscímke által képviselt összes címelőtagot.

Ha ismétlődő szabályok jelennek meg a kimenetben, annak az az oka, hogy egy NSG van társítva mind a hálózati adapterhez, mind az alhálózathoz. Mindkét NSG-k azonos alapértelmezett szabályok, és lehet, hogy további ismétlődő szabályok, ha már létrehozott saját szabályokat, amelyek azonosak mindkét NSG-k.

A **defaultSecurityRules/DenyAllInBound** nevű szabály megakadályozza a bejövő kommunikációt a virtuális gépszámára a 80-as porton keresztül, az internetről, a [forgatókönyvben](#scenario)leírtak szerint. Nincs más szabály, amelymagasabb prioritású (alacsonyabb szám) lehetővé teszi a 80-as port bejövő az internetről.

## <a name="resolve-a-problem"></a>Probléma megoldása

Akár az Azure [Portalon](#diagnose-using-azure-portal), [a PowerShell,](#diagnose-using-powershell)vagy az [Azure CLI](#diagnose-using-azure-cli) használatával diagnosztizálja a cikkben bemutatott [problémát,](#scenario) a megoldás a következő tulajdonságokkal rendelkező hálózati biztonsági szabály létrehozása:

| Tulajdonság                | Érték                                                                              |
|---------                |---------                                                                           |
| Forrás                  | Bármelyik                                                                                |
| Forrásporttartományok      | Bármelyik                                                                                |
| Cél             | A virtuális gép IP-címe, ip-címek tartománya, vagy az alhálózat összes címe. |
| Célporttartományok | 80                                                                                 |
| Protocol (Protokoll)                | TCP                                                                                |
| Műveletek                  | Engedélyezés                                                                              |
| Prioritás                | 100                                                                                |
| Név                    | Engedélyezés-HTTP-Minden                                                                     |

A szabály létrehozása után a 80-as port bevihető az internetről, mert a szabály prioritása magasabb, mint a *DenyAllInBound*nevű alapértelmezett biztonsági szabály, amely megtagadja a forgalmat. További információ a [biztonsági szabályok létrehozásáról.](manage-network-security-group.md#create-a-security-rule) Ha a hálózati adapterhez és az alhálózathoz különböző NSG-k vannak társítva, mindkét NSG-ben létre kell hoznia ugyanazt a szabályt.

Amikor az Azure feldolgozza a bejövő forgalmat, az alhálózathoz társított NSG-ben dolgozza fel a szabályokat (ha van társított NSG), majd feldolgozza a hálózati adapterhez társított NSG-ben lévő szabályokat. Ha a hálózati adapterhez és az alhálózathoz NSG van társítva, a portnak mindkét NSG-ben nyitva kell lennie ahhoz, hogy a forgalom elérje a virtuális gép. A felügyeleti és kommunikációs problémák megkönnyítése érdekében azt javasoljuk, hogy az NSG-t az egyes hálózati adapterek helyett egy alhálózathoz társítsa. Ha egy alhálózaton belül a virtuális gépeknek különböző biztonsági szabályokra van szükségük, beállíthatja az alkalmazásbiztonsági csoport (ASG) hálózati csatolóit, és megadhat egy ASG-t a biztonsági szabály forrásaként és céljaként. További információ az [alkalmazásbiztonsági csoportokról.](security-overview.md#application-security-groups)

Ha továbbra is kommunikációs problémái vannak, olvassa el [a Szempontok](#considerations) és további diagnózis című témakört.

## <a name="considerations"></a>Megfontolandó szempontok

A kapcsolódási problémák elhárításakor vegye figyelembe az alábbi szempontokat:

* Az alapértelmezett biztonsági szabályok blokkolják az internetről érkező bejövő hozzáférést, és csak a virtuális hálózatról érkező bejövő forgalmat engedélyezik. Az internetről érkező bejövő forgalom engedélyezéséhez adjon hozzá az alapértelmezett szabályoknál magasabb prioritású biztonsági szabályokat. További információ az [alapértelmezett biztonsági szabályokról](security-overview.md#default-security-rules)és [a biztonsági szabályok hozzáadásáról.](manage-network-security-group.md#create-a-security-rule)
* Ha társviszonyt létesített virtuális hálózatok, alapértelmezés szerint a **VIRTUAL_NETWORK** szolgáltatáscímke automatikusan kibővül, hogy tartalmazza a társviszony-létesített virtuális hálózatok előtagok. A virtuális hálózati társviszony-létesítéssel kapcsolatos problémák elhárításához tekintse meg az előtagokat a **ExpandedAddressPrefix** listában. További információ a [virtuális hálózati társviszony-létesítésről](virtual-network-peering-overview.md) és [a szolgáltatáscímkékről.](security-overview.md#service-tags)
* A hatékony biztonsági szabályok csak akkor jelennek meg a hálózati adapterek nél, ha a virtuális gép hálózati adapteréhez és alhálózatához nsg van társítva, és ha a virtuális gép futó állapotban van.
* Ha nincsenek NSG-k társítva a hálózati adapter hez vagy alhálózathoz, és egy virtuális géphez nyilvános [IP-címmel](virtual-network-public-ip-address.md) rendelkezik, akkor minden port nyitva áll a bejövő és kimenő hozzáféréshez bárhol. Ha a virtuális gép rendelkezik nyilvános IP-címmel, javasoljuk, hogy nsg-t alkalmazzon a hálózati adapter alhálózatára.

## <a name="additional-diagnosis"></a>További diagnózis

* Ha egy gyors tesztet futtatannak megállapítására, hogy a forgalom engedélyezett-e egy virtuális gép, használja az Azure Network Watcher [IP-folyamat-ellenőrzési](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) képességét. Az IP-folyamat ellenőrzése jelzi, ha a forgalom engedélyezett vagy megtagadott. Ha nincs megtagadva, az IP-folyamat ellenőrzése jelzi, hogy melyik biztonsági szabály tagadja meg a forgalmat.
* Ha nincsenek olyan biztonsági szabályok, amelyek a virtuális gép hálózati kapcsolatának meghiúsulását okoznák, a probléma oka a következő lehet:
  * A virtuális gép operációs rendszerén belül futó tűzfalszoftver
  * Virtuális berendezésekhez vagy helyszíni forgalomhoz konfigurált útvonalak. Az internetes forgalom átirányítható a helyszíni hálózatra [kényszerített bújtatás útján.](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Ha kényszeríti az alagút internetes forgalmat egy virtuális készülék, vagy a helyszínen, előfordulhat, hogy nem tud csatlakozni a virtuális géphez az internetről. Ha tudni szeretné, hogyan diagnosztizálhatja az olyan útvonalproblémákat, amelyek akadályozhatják a virtuális gépből a forgalom áramlását, olvassa [el a Virtuálisgép hálózati forgalom útválasztási problémájának diagnosztizálása című témakört.](diagnose-network-routing-problem.md)

## <a name="next-steps"></a>További lépések

- Ismerje meg a [hálózati biztonsági csoport](manage-network-security-group.md#work-with-network-security-groups) és biztonsági szabályok összes feladatát, tulajdonságát és [beállítását.](manage-network-security-group.md#work-with-security-rules)
- Ismerje meg [az alapértelmezett biztonsági szabályokat](security-overview.md#default-security-rules), [szolgáltatáscímkéket,](security-overview.md#service-tags)és [hogyan dolgozza fel az Azure a virtuális gépek bejövő és kimenő forgalmára vonatkozó biztonsági szabályokat.](security-overview.md#network-security-groups)
