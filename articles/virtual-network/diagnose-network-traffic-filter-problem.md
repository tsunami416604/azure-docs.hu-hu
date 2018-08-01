---
title: A virtuális gép hálózati forgalomszűrési problémáinak diagnosztizálása |} A Microsoft Docs
description: Ismerje meg, hogyan egy virtuális gép hálózati forgalomszűrési problémáinak diagnosztizálása a virtuális gép érvényes biztonsági szabályok megtekintésével.
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
ms.openlocfilehash: 67b2babcd19268a61794d123f5aa9780af16976b
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364012"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>A virtuális gép hálózati forgalomszűrési problémáinak diagnosztizálása

Ebben a cikkben megismerheti, hogyan diagnosztizálhatja a hálózat forgalomszűrési a hálózati biztonsági csoport (NSG) biztonsági szabályok, amelyek a virtuális gép (VM) hatékony megtekintésével.

Az NSG-k lehetővé teszik a típusú forgalom a virtuális gép adataikkal folyamatot. Egy Azure virtuális hálózaton, egy virtuális Gépet, vagy mindkét csatolt hálózati adapter egy alhálózati NSG-KET társíthat. A hálózati adapter érvényes biztonsági szabályokat egy hálózati adapterhez rendelt összesítést a szabályok, amelyek szerepelnek az NSG-t, és a hálózati adapter alhálózat. Különböző NSG-ket a szabályok néha ütköznek egymással, és hatással van a virtuális gép hálózati kapcsolat. Az NSG-ket a virtuális gép hálózati adapteren alkalmazott összes érvényben lévő biztonsági szabályok tekintheti meg. Ha még nem ismeri a virtuális hálózathoz, a hálózati adapter vagy a NSG fogalmakat, tanulmányozza [virtuális hálózatok áttekintése](virtual-networks-overview.md), [hálózati adapter](virtual-network-network-interface.md), és [hálózati biztonsági csoportok áttekintése](security-overview.md).

## <a name="scenario"></a>Forgatókönyv

Próbál kapcsolódni egy virtuális gép 80-as porton keresztül az internetről, de a kapcsolat hibája esetén. Annak megállapításához, hogy miért nem férhet hozzá 80-as porton az internetről, megtekintheti az Azure hálózati adapter érvényes biztonsági szabályok [portál](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), vagy a [AzureCLI-vel](#diagnose-using-azure-cli).

A következő lépések azt feltételezik, hogy rendelkezik egy meglévő virtuális Gépet, tekintse meg az érvényben lévő biztonsági szabályokat a. Ha egy meglévő virtuális gép nem rendelkezik, először telepítse a [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális Gépet ebben a cikkben a feladatok végrehajtásához. Ebben a cikkben szereplő példák vannak nevű virtuális gép *myVM* nevű hálózati adapter *myVMVMNic*. A virtuális gép és a hálózati adapter nevű erőforráscsoportban vannak *myResourceGroup*, és a *USA keleti RÉGIÓJA* régióban. Módosítsa az értékeket a megfelelő a lépéseket, a virtuális gép a probléma diagnosztizálása vannak.

## <a name="diagnose-using-azure-portal"></a>Diagnosztika az Azure portal használatával

1. Jelentkezzen be az Azure [portál](https://portal.azure.com) egy Azure-fiókkal, amely rendelkezik a [szükséges engedélyek](virtual-network-network-interface.md#permissions).
2. Az Azure portal tetején adja meg a virtuális gép nevét a keresőmezőbe. Amikor a virtuális gép neve megjelenik a keresési eredmények között, kattintson rá.
3. Alatt **beállítások**válassza **hálózatkezelés**, ahogy az alábbi képen is látható:

   ![Biztonsági szabályok megtekintése](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   A szabályokat, megjelenik az előző ábrán felsorolt kell nevű hálózati adapter **myVMVMNic**. Láthatja, hogy nincsenek **BEJÖVŐPORT-szabályok** a hálózati adapter két különböző hálózati biztonsági csoportokból:
   
   - **mySubnetNSG**: az alhálózatot, amelyet a hálózati adapterhez van társítva.
   - **myVMNSG**: a virtuális gép nevű hálózati adapterhez társított **myVMVMNic**.

   A nevű szabályt **DenyAllInBound** van, mi akadályozza bejövő kommunikációt a virtuális géphez, 80-as porton keresztül az internetről, leírtak szerint a [forgatókönyv](#scenario). A szabály listák *0.0.0.0/0* a **forrás**, amely tartalmazza az interneten. Nincs más szabály, a magasabb prioritású (alacsonyabb sorszámú) lehetővé teszi, hogy a 80-as porton bejövő. A 80-as port engedélyezése a virtuális géphez az internetről bejövő, lásd: [egy probléma megoldásához](#resolve-a-problem). Biztonsági szabályok, és hogyan Azure alkalmazza őket kapcsolatos további tudnivalókért lásd: [hálózati biztonsági csoportok](security-overview.md).

   A kép alján látható is **kimenő PORT szabályok**. Amely alatt a hálózati adaptert a porton keresztüli kimenő szabályok vonatkoznak. A képen látható négy bejövő szabályok, csak egyes NSG-khez, de az NSG-k sok négynél több szabály előfordulhat, hogy rendelkezik. Az ábrán látható **VirtualNetwork** alatt **forrás** és **cél** és **AzureLoadBalancer** alatt  **FORRÁS**. **VirtualNetwork** és **AzureLoadBalancer** vannak [szolgáltatáscímkéket](security-overview.md#service-tags). Szolgáltatáscímkék IP-címelőtagokat a segítségükkel csökkenthető a biztonsági szabályok létrehozásának összetettsége egy csoportját képviselik.

4. Győződjön meg arról, hogy a virtuális gép futó állapotban, és adja meg **érvényben lévő biztonsági szabályokat**az érvényben lévő biztonsági szabályokat, az alábbi képen is látható az előző képen látható módon:

   ![Érvényes biztonsági szabályok megtekintése](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   Felsorolt szabályokat kell, hogy azonos, ahogy azt a 3. lépés, abban az esetben, ha az a hálózati adapter és az alhálózathoz társított NSG különböző lap található. Ahogy az ábrán látható, csak az első 50 szabályokat jelennek meg. Válassza ki, amely tartalmazza az összes szabály .csv-fájl letöltésére, **letöltése**.

   Megtekintéséhez, amely előtagjainak minden szolgáltatás címke jelöli, jelöljön ki egy szabályt, például a nevű szabályt **AllowAzureLoadBalancerInbound**. Az alábbi képen látható előtagjait a **AzureLoadBalancer** szolgáltatáscímke:

   ![Érvényes biztonsági szabályok megtekintése](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Bár a **AzureLoadBalancer** csak jelölik, egy előtagot, további szolgáltatáscímkék több előtagok jelölik.

5. Az előző lépések bemutatta a biztonsági szabályok nevű hálózati adapter **myVMVMNic**, de azt is bemutattuk nevű hálózati adapter **myVMVMNic2** néhány az előző képek. Ebben a példában a virtuális Géphez csatlakoztatott két hálózati adaptert tartalmaz. Az érvényben lévő biztonsági szabályokat minden egyes hálózati adapter esetén eltérők lehetnek.

   A szabályok megtekintéséhez a **myVMVMNic2** hálózati adapter, jelölje ki azt. Ahogy az alábbi képen is látható, a hálózati adaptert, az alhálózathoz rendelt ugyanazokkal a szabályokkal rendelkezik-e a **myVMVMNic** hálózati adapter, mivel mindkét hálózati adapterek ugyanabban az alhálózatban. Társít egy NSG-t egy alhálózathoz, amikor a szabályok az alhálózat összes hálózati adapter érvényesek.

   ![Biztonsági szabályok megtekintése](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   Ellentétben a **myVMVMNic** hálózati kapcsolat a **myVMVMNic2** hálózati adapter nem rendelkezik egy hálózati biztonsági csoport társítva van hozzá. Minden egyes hálózati adapter és alhálózati rendelkezhet, nulla vagy egy, az NSG tartozik. Minden egyes hálózati adapterhez rendelt az NSG-t vagy alhálózat is változatlan marad, vagy eltérő. Az egyazon hálózati biztonsági csoport tetszőleges számú hálózati adapterek és alhálózatok, amikor a társíthat.

Bár érvényben lévő biztonsági szabályokat is tekinthetők meg a virtuális gép, érvényes biztonsági szabályok egyéni keresztül is megtekintheti:
- **Hálózati adapter**: ismerje meg, hogyan [egy hálózati adapter megtekintésére](virtual-network-network-interface.md#view-network-interface-settings).
- **NSG-t**: ismerje meg, hogyan [megtekintése az NSG-KET](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnosztizálhatja a PowerShell használatával

A következő parancsokat futtathat a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy a számítógépről futtatja a Powershellt. Az Azure Cloud Shell olyan ingyenes interaktív kezelőfelület. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha futtatja a PowerShell a számítógépről, akkor a *AzureRM* PowerShell-modult, 6.0.1 verzió vagy újabb. Futtatás `Get-Module -ListAvailable AzureRM` a számítógépen, a telepített verzió azonosításához. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha Ön helyileg futtatja a Powershellt, is futtatni szeretné `Login-AzureRmAccount` bejelentkezni az Azure-bA egy olyan fiókkal, amely rendelkezik a [szükséges engedélyek](virtual-network-network-interface.md#permissions)].

Hálózati illesztő – esetén az érvényes biztonsági szabályainak lekérése [Get-AzureRmEffectiveNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermeffectivenetworksecuritygroup). Az alábbi példa lekéri az érvényben lévő biztonsági szabályokat nevű hálózati adapter *myVMVMNic*, azaz egy erőforráscsoportba tartozó nevű *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic interface `
  -ResourceGroupName myResourceGroup
```

Kimenet json formátumban adja vissza. A kimenet ismertetése: [parancs eredményét értelmezik](#interpret-command-output).
Kimeneti csak akkor ad vissza, ha az NSG-t a hálózati adapter vagy az alhálózatot a hálózati adapter van társítva. A virtuális gép futó állapotban kell lennie. Virtuális gép több hálózati adapterrel rendelkezhet a alkalmazni különböző NSG-ket. Hibaelhárítás, futtassa a parancsot minden egyes hálózati adapter.

Ha még nem kapcsolati probléma, lásd: [további elemzés céljából](#additional-diagnosis) és [szempontok](#considerations).

Ha nem ismeri a hálózati adapter nevét, de tudja a nevét, a virtuális gép a hálózati adapter csatlakoztatva van, az alábbi parancsokat a virtuális Géphez csatlakoztatott összes hálózati adapter azonosítóit adja vissza:

```azurepowershell-interactive
$VM = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Az alábbi példához hasonló kimenetet kapja:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

Az előző kimeneti hálózati adapter neve van *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnosztika az Azure CLI-vel

Ha használja az Azure parancssori felület (CLI) parancsai cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/bash), vagy a parancssori felület futtatásával a számítógépről. Ehhez a cikkhez az Azure CLI 2.0.32 verzió vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI, is futtatni szeretné `az login` , és jelentkezzen be Azure-fiókkal, amely rendelkezik a [szükséges engedélyek](virtual-network-network-interface.md#permissions).

Hálózati illesztő – esetén az érvényes biztonsági szabályainak lekérése [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg). Az alábbi példa lekéri az érvényben lévő biztonsági szabályokat nevű hálózati adapter *myVMVMNic* nevű erőforráscsoportban ez *myResourceGroup*:

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Kimenet json formátumban adja vissza. A kimenet ismertetése: [parancs eredményét értelmezik](#interpret-command-output).
Kimeneti csak akkor ad vissza, ha az NSG-t a hálózati adapter vagy az alhálózatot a hálózati adapter van társítva. A virtuális gép futó állapotban kell lennie. Virtuális gép több hálózati adapterrel rendelkezhet a alkalmazni különböző NSG-ket. Hibaelhárítás, futtassa a parancsot minden egyes hálózati adapter.

Ha még nem kapcsolati probléma, lásd: [további elemzés céljából](#additional-diagnosis) és [szempontok](#considerations).

Ha nem ismeri a hálózati adapter nevét, de tudja a nevét, a virtuális gép a hálózati adapter csatlakoztatva van, az alábbi parancsokat a virtuális Géphez csatlakoztatott összes hálózati adapter azonosítóit adja vissza:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

A visszaadott kimenetet belül jelenik meg az alábbi példához hasonló információkat:

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

Az előző kimeneti hálózati adapter neve van *myVMVMNic felület*.

## <a name="interpret-command-output"></a>A parancs kimenete értelmezése

Függetlenül attól, hogy használta a [PowerShell](#diagnose-using-powershell), vagy a [Azure CLI-vel](#diagnose-using-azure-cli) kimenete, amely tartalmazza a következő információkat kap a probléma diagnosztizálása érdekében:

- **NetworkSecurityGroup**: a hálózati biztonsági csoport azonosítója.
- **Társítás**:-e a hálózati biztonsági csoport társítva egy *hálózati* vagy *alhálózati*. Ha is társítva egy NSG-t, a kimenetet visszaadja **NetworkSecurityGroup**, **társítás**, és **EffectiveSecurityRules**, minden NSG. Ha az NSG-hez kapcsolódó, vagy közvetlenül a tekintse meg az érvényben lévő biztonsági szabályokat, a parancs futtatása előtt le, szükség lehet Várjon néhány másodpercet a módosítás megjelenik a parancs kimenete.
- **EffectiveSecurityRules**: részletes magyarázatát tartalmazza minden egyes tulajdonság [hozzon létre egy biztonsági szabályt](manage-network-security-group.md#create-a-security-rule). Szabály neve előtt a létrehozás *defaultSecurityRules /* vannak alapértelmezett biztonsági szabályokat, minden NSG-ben léteznek. Szabály neve előtt a létrehozás *securityRules /* létrehozott szabályok vonatkoznak. Meghatározott szabályok egy [szolgáltatáscímke](security-overview.md#service-tags), mint például **Internet**, **VirtualNetwork**, és **AzureLoadBalancer** számára a  **destinationAddressPrefix** vagy **sourceAddressPrefix** tulajdonságai között is szerepel érték a **expandedDestinationAddressPrefix** tulajdonság. A **expandedDestinationAddressPrefix** tulajdonság a szolgáltatáscímke által jelölt összes címelőtagok sorolja fel.

A kimenetben szereplő duplikált szabályok megtekintéséhez, hogy mivel az NSG-t mind a hálózati adaptert, és az alhálózathoz van társítva. Mind az NSG-ket az ugyanazon alapértelmezett szabályokkal rendelkeznek, és további ismétlődő szabályokat, előfordulhat, ha saját szabályok, amelyek megegyeznek az NSG-ket is létrehozott.

A nevű szabályt **defaultSecurityRules/DenyAllInBound** van, mi akadályozza bejövő kommunikációt a virtuális géphez, 80-as porton keresztül az internetről, leírtak szerint a [forgatókönyv](#scenario). Nincs más szabály, a magasabb prioritású (alacsonyabb sorszámú) lehetővé teszi, hogy a port 80-as bejövő az internetről.

## <a name="resolve-a-problem"></a>A probléma megoldásához

Ha használja az Azure [portál](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), vagy a [Azure CLI-vel](#diagnose-using-azure-cli) jelenik meg a probléma diagnosztizálása érdekében a [forgatókönyv](#scenario) a jelen a cikkben a megoldás az, ha egy hálózati biztonsági szabály létrehozása a következő tulajdonságokkal:

| Tulajdonság                | Érték                                                                              |
|---------                |---------                                                                           |
| Forrás                  | Bármelyik                                                                                |
| Forrásporttartományok      | Bármelyik                                                                                |
| Cél             | A virtuális gép IP-címét, egy IP-címtartomány vagy az alhálózaton levő összes cím. |
| Célporttartományok | 80                                                                                 |
| Protocol (Protokoll)                | TCP                                                                                |
| Műveletek                  | Engedélyezés                                                                              |
| Prioritás                | 100                                                                                |
| Name (Név)                    | Lehetővé teszi a HTTP-All                                                                     |

Miután létrehozta a szabályt, a 80-as porton engedélyezve van-e az internetről bejövő, mert a szabály prioritása nagyobb, mint az alapértelmezett biztonsági szabály nevű *DenyAllInBound*, amely a forgalom megtagadásához. Ismerje meg, hogyan [hozzon létre egy biztonsági szabályt](manage-network-security-group.md#create-a-security-rule). Ha a hálózati adaptert, és az alhálózathoz társított különböző NSG-ket, létre kell hoznia ugyanaz a szabály a mindkét NSG-k.

Azure által feldolgozott bejövő forgalmat, amikor feldolgozza a szabályokat az NSG az alhálózathoz (ha van egy társított NSG-t) rendelt, és majd dolgozza fel a szabályokat a hálózati adapterhez társított NSG-ben. Ha a hálózati adapter és az alhálózathoz társított NSG-t, a port mindkét NSG-t, a forgalom elérhesse a virtuális gép nyitva kell lennie. Megkönnyítése érdekében a felügyeleti és kommunikációs problémák, azt javasoljuk, hogy társít egy NSG-t egy alhálózathoz, mint az egyes hálózati adapterek. Egy alhálózaton belüli virtuális gépek különböző biztonsági szabályok van szüksége, ellenőrizze a hálózati adapterek tagjai egy alkalmazásbiztonsági csoportot (Alkalmazásbiztonsági), és adja meg a forrás- és egy biztonsági szabály egy Alkalmazásbiztonsági. Tudjon meg többet [az alkalmazásbiztonsági csoportok](security-overview.md#application-security-groups).

Ha továbbra is kommunikációs problémákat tapasztal, tekintse meg [szempontok](#considerations) és [további elemzés céljából](#additional-dignosis).

## <a name="considerations"></a>Megfontolandó szempontok

Kapcsolódási problémák elhárításához, vegye figyelembe a következőket:

* Alapértelmezett biztonsági szabályok blokkolja az internetről bejövő hozzáférést, és csak a virtuális hálózatról a bejövő forgalom engedélyezése. Ahhoz, hogy a bejövő forgalom az internetről, adjon hozzá egy magasabb prioritású, mint az alapértelmezett szabályok biztonsági szabályokat. Tudjon meg többet [alapértelmezett biztonsági szabályokat](security-overview.md#default-security-rules), vagy a [biztonsági szabály hozzáadására](manage-network-security-group.md#create-a-security-rule).
* Ha rendelkezik társviszonyban álló virtuális hálózatok alapértelmezés szerint a **VIRTUAL_NETWORK** szolgáltatáscímke automatikusan kiegészíthetjük társviszonyban lévő virtuális hálózatok előtagjait. Minden olyan virtuális hálózatok közötti társviszony létesítésével kapcsolatos problémák elhárítása, a címelőtagokat is megtekintheti a **ExpandedAddressPrefix** listája. Tudjon meg többet [virtuális hálózatok közötti társviszony](virtual-network-peering-overview.md) és [szolgáltatáscímkéket](security-overview.md#service-tags).
* Érvényes biztonsági szabályok esetében ha van egy NSG-t egy hálózati adaptert a virtuális gép hálózati interfészhez társított csak látható és, vagy alhálózathoz, és ha a virtuális gép futó állapotba kerül.
* Ha nincs a hálózati adapter vagy az alhálózathoz társított hálózati biztonsági csoportok, és rendelkezik egy [nyilvános IP-cím](virtual-network-public-ip-address.md) egy virtuális Géphez hozzárendelt, az összes portok nyitva a bejövő hozzáférést és a kimenő hozzáférést bárhonnan. Ha a virtuális gép nyilvános IP-címmel rendelkezik, azt javasoljuk alkalmazása egy NSG-t az alhálózathoz a hálózati adaptert.

## <a name="additional-diagnosis"></a>További elemzés céljából

* Határozza meg, ha a forgalom engedélyezve van, vagy egy virtuális gépről való gyors teszteléséhez használja a [IP-folyamat ellenőrzésével](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) Azure Network Watcher képességét. IP-folyamat ellenőrzésével arra kéri, ha engedélyezett vagy tiltott forgalmat. Ha megtagadja, IP-folyamat ellenőrzésével jelzi, hogy melyik biztonsági szabály megtagadja a forgalmat.
* Ha nem okoz a virtuális gép hálózati kapcsolat sikertelen biztonsági szabályokat, a probléma előfordulhat, hogy a következő lehet:
  * A virtuális gép operációs rendszerén belül futó tűzfalszoftverek
  * Konfigurált virtuális berendezések vagy a helyszíni forgalom útválasztását. Internetes forgalom átirányítható a helyszíni hálózathoz keresztül [kényszerített bújtatást végez](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ha kényszeríti alagút internetes forgalmat egy olyan virtuális berendezésre vagy a helyi, nem lehet tud csatlakozni a virtuális Géphez az internetről. Című cikk nyújt tájékoztatást, amelyek akadályozhatják az adatforgalom a virtuális gép útválasztási problémák diagnosztizálása, [virtuális gép hálózati forgalom útválasztási probléma diagnosztizálása](diagnose-network-routing-problem.md).

## <a name="next-steps"></a>További lépések

- Ismerje meg az összes feladatok, tulajdonságai és beállításai egy [hálózati biztonsági csoport](manage-network-security-group.md#work-with-network-security-groups) és [biztonsági szabályok](manage-network-security-group.md#work-with-security-rules).
- Ismerje meg [alapértelmezett biztonsági szabályokat](security-overview.md#default-security-rules), [szolgáltatáscímkéket](security-overview.md#service-tags), és [miként dolgozza fel a az Azure a biztonsági szabályok a bejövő és kimenő forgalmat](security-overview.md#network-security-groups) egy virtuális géphez.
