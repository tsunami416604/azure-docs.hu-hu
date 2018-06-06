---
title: A virtuális gép hálózati forgalom szűrő probléma diagnosztizálása |} Microsoft Docs
description: Útmutató a hatékony biztonsági szabályok virtuális gép megtekintésével egy virtuális gép hálózati forgalom szűrő probléma diagnosztizálása érdekében.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: jdial
ms.openlocfilehash: 1c33a75363eec2b4e338ba64e3d1ad877d8b1610
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757227"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>A virtuális gép hálózati forgalom szűrő probléma diagnosztizálása

Ebből a cikkből megismerheti, hogyan diagnosztizálhatja a hálózati forgalom szűrő hiba a hálózati biztonsági csoport (NSG) biztonsági szabályokat, amelyek a virtuális gép (VM) hatékony megtekintésével.

Az NSG-k lehetővé teszik a típusú forgalom mindkét virtuális gép adott folyamatot. Egy Azure virtuális hálózatban, a hálózati adaptert egy virtuális Gépet, vagy mindkét alhálózat NSG lehet társítani. Egy adott hálózati csatoló a hatékony biztonsági szabályokat egy adott hálózati csatoló társítva összevonható a szabályokat, amelyek az NSG szerepel a, és az alhálózatot a hálózati illesztőt. Különböző NSG-ket szabályokat néha ütköznek egymással, és hatással lehet a virtuális gép hálózati kapcsolatot. A virtuális gép hálózati interfészeken alkalmazott NSG-ket a hatékony biztonsági szabályok tekintheti meg. Ha még nem ismeri a virtuális hálózat, a hálózati adapter vagy a NSG fogalmakat, lásd: [virtuális hálózat áttekintése](virtual-networks-overview.md), [hálózati illesztő](virtual-network-network-interface.md), és [hálózati biztonsági csoportok áttekintése](security-overview.md).

## <a name="scenario"></a>Forgatókönyv

Próbál csatlakozni a virtuális gépek 80-as porton keresztül az internetről, de a kapcsolódás sikertelen. Annak megállapításához, hogy miért nem férhet hozzá 80-as porton az internetről, megtekintheti a hatékony biztonsági szabályok egy adott hálózati csatoló az Azure használatával [portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), vagy a [Azure CLI](#diagnose-using-azure-cli).

A következő lépések során feltételezzük, hogy egy meglévő virtuális gép megtekintéséhez a hatékony biztonsági szabályokat. Ha egy meglévő virtuális gép nem rendelkezik, először telepítsen egy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM ebben a cikkben szereplő feladatok elvégzéséhez. Ebben a cikkben szereplő példák vannak a virtuális gépek nevű *myVM* az egy adott hálózati csatoló nevű *myVMVMNic*. A virtuális gép és a hálózati adapter szerepelnek nevű erőforráscsoport *myResourceGroup*, és a *USA keleti régiója* régió. A lépéseket, hogy megfelelő értékének módosításához a virtuális gép vannak a probléma diagnosztizálásához.

## <a name="diagnose-using-azure-portal"></a>Diagnosztizálja az Azure-portál használatával

1. Jelentkezzen be a Azure [portal](https://portal.azure.com) az Azure-fiókot, amely rendelkezik a [szükséges engedélyek](virtual-network-network-interface.md#permissions).
2. Az Azure portál felső részén adja meg a virtuális gép nevét a keresőmezőbe. Ha a keresési eredmények között megjelenik a virtuális gép nevét, válassza ki azt.
3. A **beállítások**, jelölje be **hálózati**, az alábbi ábrán látható módon:

    ![Biztonsági szabályok megtekintése](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

    Megjelenik az előző ábrán szereplő paraméterei egy adott hálózati csatoló nevű **myVMVMNic**. Láthatja, hogy nincsenek **PORT BEJÖVŐ szabályok** a hálózati adapter két különböző hálózati biztonsági csoportokból:- **mySubnetNSG**:, amely a hálózati adaptert az alhálózathoz társított.
        - **myVMNSG**: a hálózati illesztő a virtuális gép nevű társított **myVMVMNic**.

    A szabály nevű **DenyAllInBound** van, mi megakadályozza az adatforgalmat a virtuális géphez, 80-as porton keresztül az internetről, lásd: a [forgatókönyv](#scenario). A szabály listák *0.0.0.0/0* a **forrás**, mely tartalmazza az interneten. Nincs más szabály, a magasabb prioritású (kevesebb) lehetővé teszi, hogy a 80-as portot a bejövő. Engedélyezi a 80-as portot a virtuális gép az internetről bejövő című [egy probléma megoldásához](#resolve-a-problem). A biztonsági szabályok és hogyan Azure alkalmazza azok kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](security-overview.md).

    A kép alján látható is **kimenő PORT szabályok**. Az, hogy a hálózati illesztő a kimenő port szabályok vonatkoznak. A kép csak azt mutatja, négy bejövő szabályok egyes NSG-khez, bár az NSG-k sok négynél több szabályok is rendelkezhet. A képen látható **VirtualNetwork** alatt **forrás** és **cél** és **AzureLoadBalancer** alatt  **FORRÁS**. **VirtualNetwork** és **AzureLoadBalancer** vannak [címkék szolgáltatás](security-overview.md#service-tags). Szolgáltatás címkék IP-cím előtagokat minimálisra csökkentése összetettségét, a biztonsági szabály létrehozásához egy csoportját képviselik.

4. Győződjön meg arról, hogy a virtuális gép futó állapotban, és adja **hatékony biztonsági szabályok**, megjelenítheti a hatékony biztonsági szabályt, a következő ábrán látható az előző ábrán látható módon:

    ![Hatékony biztonsági szabályok megtekintése](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

    A felsorolt paraméterei azonos ahogy azt a 3. lépés, bár a hálózati illesztő és az alhálózat társított NSG-t az egyes lapjainak. Mint a képen látható, csak az első 50 szabályokat jelennek meg. Töltse le az összes szabály tartalmazó .csv fájlt, jelölje be **letöltése**.

    Megtekintéséhez, amely előtagok minden szolgáltatás címke jelöli, jelöljön ki egy szabályt, például nevű szabályt **AllowAzureLoadBalancerInbound**. Az alábbi képen látható előtagjait a **AzureLoadBalancer** címke szolgáltatás:

    ![Hatékony biztonsági szabályok megtekintése](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

    Bár a **AzureLoadBalancer** szolgáltatás címke csak egy előtagot jelöli, más szolgáltatás-címkék több előtagok képviseli.

4. Az előző lépések a biztonsági szabályok egy adott hálózati csatoló nevű bemutatta **myVMVMNic**, de is láthatta nevű hálózati illesztő **myVMVMNic2** egyes az előző képek. Ebben a példában a virtuális gép nem csatlakoztatható két hálózati adapterrel rendelkezik. A hatékony biztonsági szabályok mindegyik hálózati interfész eltérő lehet.

    A szabályok megtekintéséhez a **myVMVMNic2** hálózati adapter, válassza ki azt. Az alábbi képen látható, a hálózati illesztőnek ugyanazok a szabályok az alhálózaton társított a **myVMVMNic** hálózati adapter, mert mindkét hálózati adapterek ugyanabban az alhálózatban. Amikor társít egy NSG alhálózathoz, a szabályok érvényesek az alhálózatot a hálózati adaptereken.

    ![Biztonsági szabályok megtekintése](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

    Ellentétben a **myVMVMNic** hálózati kapcsolat a **myVMVMNic2** hálózati adapter nem rendelkezik a hálózati biztonsági csoport társítva. Minden hálózati illesztő és az alhálózati tartozhat nulla, vagy egy NSG társítva. Az NSG társított mindegyik hálózati interfész vagy alhálózat lehet azonos, vagy eltérő. Az azonos hálózati biztonsági csoport annyi hálózati adapterek és alhálózatokkal működik, amikor a társíthat.

Bár a hatékony biztonsági szabályok keresztül a virtuális gép is megtekinthetők, hatékony biztonsági szabályok segítségével is megtekintheti egy:
- **Egyes hálózati adapter**: megtudhatja, hogyan [megtekintheti egy adott hálózati csatoló](virtual-network-network-interface.md#view-network-interface-settings).
- **Az egyes NSG**: megtudhatja, hogyan [megtekintése az NSG](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnosztizálja a PowerShell használatával

A parancsok futtatása a [Azure Cloud rendszerhéj](https://shell.azure.com/powershell), vagy futtassa a PowerShell a számítógépről. Az Azure-felhő rendszerhéj a szabad interaktív rendszerhéjat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha PowerShell a számítógépen futtatja, akkor a *AzureRM* PowerShell-modult, 6.0.1 verzió vagy újabb. Futtatás `Get-Module -ListAvailable AzureRM` a számítógépen, a telepített verzió található. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtat PowerShell, is futtatásához szükséges `Login-AzureRmAccount` Azure bejelentkezni egy olyan fiókkal, amely rendelkezik a [szükséges engedélyek](virtual-network-network-interface.md#permissions)].

A hatékony biztonsági szabályok lekérése a hálózati illesztő – [Get-AzureRmEffectiveNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermeffectivenetworksecuritygroup). Az alábbi példa lekérdezi a hatékony biztonsági szabályok egy adott hálózati csatoló nevű *myVMVMNic*, amely pedig erőforráscsoportban nevű *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic interface `
  -ResourceGroupName myResourceGroup
```

Kimeneti json formátumban ad vissza. A kimeneti ismertetése: [értelmezési parancs kimenetében](#interpret-command-output).
Kimeneti csak akkor ad vissza, ha az NSG tartozik a hálózati adapter vagy az alhálózatot a hálózati adaptert. A virtuális gép futó állapotban kell lennie. A virtuális gépek több hálózati adapterrel rendelkezhet az alkalmazott különböző NSG-ket. Ha a hiba elhárításához, a parancsot mindegyik hálózati interfész.

Ha még nem kapcsolathiba, lásd: [további elemzés céljából](#additional-diagnosis) és [szempontok](#considerations).

Ha nem ismeri a hálózati kapcsolat neve, de tudja a virtuális Gépet, a hálózati adapter csatlakozik a nevét, a következő parancsokat egy virtuális géphez csatolt minden hálózati interfészen azonosítóit adja vissza:

```azurepowershell-interactive
$VM = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Az alábbi példához hasonló kimenetet jelenhet meg:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

Az előző kimeneti, a hálózati kapcsolat neve: *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnosztizálja az Azure parancssori felület használatával

Azure parancssori felület (CLI) parancsok használata ebben a cikkben a feladatokat, vagy futtassa a parancsokat a [Azure Cloud rendszerhéj](https://shell.azure.com/bash), vagy a CLI-t a számítógépen való futtatásával. Ez a cikk igényel az Azure parancssori felület 2.0.32 verzió vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure parancssori felület, is futtatásához szükséges `az login` , és jelentkezzen be a Azure egy olyan fiókkal, amely rendelkezik a [szükséges engedélyek](virtual-network-network-interface.md#permissions).

A hatékony biztonsági szabályok lekérése a hálózati illesztő – [az lista-hatályos-nsg hálózati adapter hálózati](/cli/azure/network/nic#az-network-nic-list-effective-nsg). Az alábbi példa lekérdezi a hatékony biztonsági szabályok egy adott hálózati csatoló nevű *myVMVMNic* nevű erőforráscsoport szerepel *myResourceGroup*:

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Kimeneti json formátumban ad vissza. A kimeneti ismertetése: [értelmezési parancs kimenetében](#interpret-command-output).
Kimeneti csak akkor ad vissza, ha az NSG tartozik a hálózati adapter vagy az alhálózatot a hálózati adaptert. A virtuális gép futó állapotban kell lennie. A virtuális gépek több hálózati adapterrel rendelkezhet az alkalmazott különböző NSG-ket. Ha a hiba elhárításához, a parancsot mindegyik hálózati interfész.

Ha még nem kapcsolathiba, lásd: [további elemzés céljából](#additional-diagnosis) és [szempontok](#considerations).

Ha nem ismeri a hálózati kapcsolat neve, de tudja a virtuális Gépet, a hálózati adapter csatlakozik a nevét, a következő parancsokat egy virtuális géphez csatolt minden hálózati interfészen azonosítóit adja vissza:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

A visszaadott kimeneti belül a következőhöz hasonló információkat lásd:

```azurecli
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

Az előző kimeneti, a hálózati kapcsolat neve: *myVMVMNic felület*.

## <a name="interpret-command-output"></a>Értelmezési parancs kimenete

Függetlenül attól, hogy használja a [PowerShell](#diangose-using-powershell), vagy a [Azure CLI](#diagnose-using-azure-cli) a probléma diagnosztizálása érdekében a következő információkat tartalmazó kimeneti kapni:

- **Hálózati biztonsági csoporthoz tartozik**: a hálózati biztonsági csoport az azonosító.
- **Társítás**: E a hálózati biztonsági csoport társítva egy *Kezdőértékről* vagy *alhálózati*. Ha is társítva egy NSG-t, a kimenetet visszaadja **hálózati biztonsági csoporthoz tartozik**, **társítás**, és **EffectiveSecurityRules**, egyes NSG-khez. Ha az NSG társított vagy hozzárendelésének megszüntetése előtt közvetlenül futtassa a parancsot, és tekintse meg a hatékony biztonsági szabályokat, szükség lehet a Várjon néhány másodpercet, hogy tükrözze a parancs kimenetében a változtatás.
- **EffectiveSecurityRules**: az egyes tulajdonságok magyarázatot részleteit a [biztonsági szabály létrehozása](manage-network-security-group.md#create-a-security-rule). A nevek végrehajtásával kerüli meg a szabály *defaultSecurityRules /* és alapértelmezett biztonsági szabályok, amelyeket minden NSG szerepel. A nevek végrehajtásával kerüli meg a szabály *securityRules /* olyan szabályok, amelyek korábban létrehozott. Meghatározott szabályok egy [címke szolgáltatás](security-overview.md#service-tags), például a **Internet**, **VirtualNetwork**, és **AzureLoadBalancer** a a  **destinationAddressPrefix** vagy **sourceAddressPrefix** tulajdonságok is szerepel érték a **expandedDestinationAddressPrefix** tulajdonság. A **expandedDestinationAddressPrefix** tulajdonság a szolgáltatás címke által meghatározott összes címelőtagokat sorolja fel.

Ismétlődő szabályok kimenetben szereplő megtekintéséhez esetén, mert egy NSG-t a hálózati illesztő és az alhálózati is társítva. Az NSG-ket van ugyanazon alapértelmezett szabályok, és előfordulhat, hogy további ismétlődő szabályokat, ha saját szabályokat, amelyek mindkét NSG-ket a hozott létre.

A szabály nevű **defaultSecurityRules/DenyAllInBound** van, mi megakadályozza az adatforgalmat a virtuális géphez, 80-as porton keresztül az internetről, lásd: a [forgatókönyv](#scenario). Nincs más szabály, a magasabb prioritású (kevesebb) lehetővé teszi, hogy a port 80 az internetről bejövő.

## <a name="resolve-a-problem"></a>A probléma megoldásához

Hogy használ-e az Azure [portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), vagy a [Azure CLI](#diagnose-using-azure-cli) jelenik meg a probléma diagnosztizálása érdekében a [forgatókönyv](#scenario) ezen a cikkben a a megoldás, hogy a hálózati biztonsági szabály létrehozása a következő tulajdonságokkal:

| Tulajdonság                | Érték                                                                              |
|---------                |---------                                                                           |
| Forrás                  | Bármelyik                                                                                |
| Forrásporttartományok      | Bármelyik                                                                                |
| Cél             | A virtuális gép IP-címét, egy adott IP-címek vagy az alhálózaton levő összes cím. |
| Célporttartományok | 80                                                                                 |
| Protocol (Protokoll)                | TCP                                                                                |
| Műveletek                  | Engedélyezés                                                                              |
| Prioritás                | 100                                                                                |
| Name (Név)                    | Engedélyezése – HTTP-minden                                                                     |

A szabály létrehozása után engedélyezett-e a 80-as porton az internetről bejövő, mert a szabály a prioritás értéke magasabb, mint az alapértelmezett biztonsági szabály nevű *DenyAllInBound*, amely megtagadja a forgalmat. Megtudhatja, hogyan [biztonsági szabály létrehozása](manage-network-security-group.md#create-a-security-rule). Különböző NSG-k legyenek társítva a hálózati illesztő és az alhálózatot, ha mindkét NSG-ket ugyanaz a szabály kell létrehoznia.

Azure folyamatok érkező bejövő forgalmat, ha dolgozza fel a szabályokat az alhálózat (ha van egy társított NSG) társított NSG-t, és a hálózati illesztő társított NSG-t szabályai feldolgozza majd. Ha a hálózati illesztő és az alhálózat társított NSG-t, a port elérni a virtuális gép forgalom mindkét NSG-ket nyitva kell lennie. Megkönnyítése érdekében a felügyeleti és kommunikációs problémák, azt javasoljuk, hogy társít egy NSG alhálózat, mint az egyes hálózati adapterek. Ha egy alhálózaton belüli virtuális gépek különböző biztonsági szabályokat, ellenőrizze a hálózati adapterek tagjai egy alkalmazás biztonsági csoport (ASG), és adja meg egy ASG, mint a forrás- és a biztonsági szabály. További információ [biztonsági csoportok](security-overview.md#application-security-groups).

Ha még nem kommunikációs problémák, lásd: [szempontok](#considerations) és [további elemzés céljából](#additional-dignosis).

## <a name="considerations"></a>Megfontolandó szempontok

Kapcsolódási problémák elhárításakor, vegye figyelembe a következő szempontokat:

* Alapértelmezett biztonsági szabályok az internetről bejövő hozzáférésének letiltására, és csak engedélyezi a bejövő forgalom a virtuális hálózati. Bejövő adatforgalom engedélyezésére az internetről, adjon hozzá egy magasabb prioritású, mint az alapértelmezett szabályokat biztonsági szabályokat. További információ [alapértelmezett biztonsági szabályok](security-overview.md#default-security-rules), vagy hogyan [egy biztonsági szabály felvétele](manage-network-security-group.md#create-a-security-rule).
* Ha rendelkezik nincsenek társviszonyban virtuális hálózatok, alapértelmezés szerint a **VIRTUAL_NETWORK** szolgáltatás címke automatikusan kiegészíthetjük előtagok társítottak, virtuális hálózatok. Minden virtuális hálózati társviszony-létesítés kapcsolatos problémák elhárításához, megtekintheti az előtagok a **ExpandedAddressPrefix** listája. További információ [virtuális hálózati társviszony-létesítés](virtual-network-peering-overview.md) és [címkék szolgáltatás](security-overview.md#service-tags).
* Csak akkor jelennek meg az hatékony biztonsági szabályokat, ha van egy NSG-t a hálózati adaptert a virtuális hálózati adapter társított és, vagy alhálózat, és hogy a virtuális gép futó állapotban lévő.
* Ha nem a hálózati adapter vagy az alhálózat társított NSG-ket, és van egy [nyilvános IP-cím](virtual-network-public-ip-address.md) egy virtuális Géphez rendelt, minden portjai nyitva a bejövő hozzáférést és kimenő hozzáférést tetszőleges helyre. Ha a virtuális gép egy nyilvános IP-címmel rendelkezik, ajánlott az NSG alkalmazása az alhálózat a hálózati illesztő.

## <a name="additional-diagnosis"></a>További elemzés céljából

* Határozza meg, ha a forgalom engedélyezve van-e, vagy a virtuális gép gyors vizsgálat futtatásához használja a [IP folyamata ellenőrizze](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) Azure hálózati figyelőt képességét. IP-adatfolyam győződjön meg arról tájékoztat, ha a forgalom engedélyezett vagy megtagadott. Ha megtagadja, IP-adatfolyam ellenőrizze jelzi, hogy mely biztonsági szabály megtagadja a forgalmat.
* Ha nincs biztonsági szabály, amely a virtuális gépek hálózati kapcsolattal sikertelen lesz, a probléma oka a következő lehet:
  * A virtuális gép operációs rendszerben futó tűzfal szoftver
  * A virtuális készülékek vagy a helyszíni forgalmi útvonalak. Internetes forgalmat a helyi hálózaton keresztül átirányíthatók [kényszerített bújtatás](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Kényszerített bújtatás internetes forgalmat a virtuális készülék, vagy a helyszíni, nem lehet csatlakozni a virtuális gép az internetről. Ami károsan befolyásolhatja a forgalmat a virtuális gép kívül útvonal problémák diagnosztizálásához, lásd: [egy virtuális gép hálózati forgalom útválasztási probléma diagnosztizálása](diagnose-network-routing-problem.md).

## <a name="next-steps"></a>További lépések

- Minden feladatok, a tulajdonságok és a beállítások egy [hálózati biztonsági csoport](manage-network-security-group.md#work-with-network-security-groups) és [biztonsági szabályok](manage-network-security-group.md#work-with-security-rules).
- További tudnivalók [alapértelmezett biztonsági szabályok](security-overview.md#default-security-rules), [címkék szolgáltatás](security-overview.md#service-tags), és [hogyan dolgozza fel a Azure a biztonsági szabályok a bejövő és kimenő forgalom](security-overview.md#network-security-groups) egy virtuális gép számára.
