---
title: Hálózati biztonsági csoport – PowerShell hibaelhárítása |} Microsoft Docs
description: Ismerje meg a hálózati biztonsági csoportok hibaelhárítása az Azure PowerShell használata Azure Resource Manager üzembe helyezési modellben.
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: 4c732bb7-5cb1-40af-9e6d-a2a307c2a9c4
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: edbf76ef5dcf581acfec17970becdf698445cbeb
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>Hálózati biztonsági csoportok az Azure PowerShell hibaelhárítása
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-nsg-troubleshoot-portal.md)
> * [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

Ha konfigurálva a hálózati biztonsági csoportokkal (NSG-k) a virtuális gépen (VM), és virtuális gép kapcsolódási problémákat tapasztal, ez a cikk áttekintést diagnosztika képességet biztosít a További hibaelhárítás elősegítése érdekében az NSG-ket.

Az NSG-k lehetővé teszik a típusú forgalom, hogy a virtuális gépek (VM) mindkét folyamata. NSG-ket alhálózatokra egy Azure virtuális hálózatot (VNet), hálózati adapterek (NIC) vagy mindkettőt alkalmazhatók. A hatékony szabályokat egy hálózati adapter egyszerűsítése érdekében a szabályokat, amelyek szerepelnek az NSG-ket egy hálózati adapterre alkalmazza, és az alhálózat van csatlakoztatva. Ezek az NSG-k között szabályok néha ütköznek egymással, és hatással lehet a virtuális gépek hálózati kapcsolattal.

Az NSG-k, az összes hatékony biztonsági szabály tekintheti meg a virtuális gép hálózati adapterek alkalmazott. Ez a cikk bemutatja, hogyan VM csatlakozási problémák ezek a szabályok használatával az Azure Resource Manager üzembe helyezési modellben. Ha még nem ismeri a virtuális hálózat és NSG fogalmait, tekintse meg a [virtuális hálózat áttekintése](virtual-networks-overview.md) és [hálózati biztonsági csoport – áttekintés](security-overview.md).

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Hatékony biztonsági szabályok használata hibák elhárításához a virtuális gép forgalom áramlását
A következő forgatókönyv gyakori kapcsolati probléma példája:

A virtuális gépek nevű *VM1* nevű alhálózat része *Alhalozat_1* nevű egy Vneten belül *WestUS-VNet1*. A virtuális gép RDP Funkciót használnak a 3389-es TCP-porton keresztül történő csatlakozásra tett kísérlet sikertelen lesz. Az NSG-k két hálózati adapter szintjén alkalmazhatóak *VM1-NIC1* és az alhálózati *Alhalozat_1*. Forgalom 3389-es TCP-port engedélyezve van a hálózati interfészhez társított NSG *VM1-NIC1*, azonban a TCP ping VM1 a következőre port 3389 meghiúsul.

Ebben a példában a 3389-es portot használja, amíg az alábbi lépések segítségével határozza meg a bejövő és kimenő kapcsolódási hibák bármely porton keresztül.

## <a name="detailed-troubleshooting-steps"></a>Részletes hibaelhárítási lépéseket
Az alábbi lépésekkel hibáinak elhárítása az NSG-ket a virtuális gépek:

1. Indítsa el az Azure PowerShell-munkamenetet és a bejelentkezés az Azure-bA. Ha nem ismeri az Azure PowerShell használatával, olvassa el a [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview) cikk. Ön fiókjához társítva kell lenni a *Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action* műveletet a hálózati adapter. Műveletek fiókokhoz rendeléséhez, lásd: [egyedi szerepkörök létrehozását, hozzáférés-vezérlési átruházásához](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Adja meg a következő parancs sikeresen lefut egy nevű hálózati adapterre alkalmazza minden NSG-szabályok *VM1-NIC1* erőforráscsoportban *RG1*:
   
        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > Ha egy hálózati adapter neve nem tudja, adjon meg egy erőforráscsoportot az összes hálózati adapter nevének beolvasása a következő parancsot: 
   > 
   > `Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`
   > 
   > 
   
    A következő szöveg látható egy minta vissza hatékony szabályok kimenetét a *VM1-NIC1* NIC:
   
        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
   
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]
   
    Vegye figyelembe a kimenete a következő információkat:
   
   * Két **hálózati biztonsági csoporthoz tartozik** szakaszok: tartozik hozzá egy alhálózat (*Alhalozat_1*) és egy hálózati Adapterhez társított (*VM1-NIC1*). Ebben a példában egy NSG-t az egyes telepítve van.
   * **Társítás** jeleníti meg az erőforrás (alhálózati vagy hálózati), egy adott NSG társítva. Ha az NSG-erőforrás áthelyezése vagy hozzárendelésének megszüntetése a parancs futtatása előtt azonnal, szükség lehet Várjon néhány másodpercet, hogy tükrözze a parancs kimenetében a változtatás. 
   * A szabály nevét, amelyek végrehajtásával kerüli meg a *defaultSecurityRules*: amikor egy NSG jön létre, néhány alapértelmezett biztonsági szabályok jönnek létre benne. Alapértelmezett szabályok nem távolítható el, de magasabb prioritású szabályokkal felülbírálható lesz. További információt az [alapértelmezett biztonsági szabályokkal](security-overview.md#default-security-rules) foglalkozó cikkben találhat.
   * **ExpandedAddressPrefix** bővíti a címelőtagokat NSG alapértelmezett címkék. Címkék több címelőtagokat jelölik. A címkék bővítése akkor lehet hasznos, amikor adott címelőtagokat és a virtuális gép hibaelhárítása. Például a VNETBEN társviszony-létesítést, VIRTUAL_NETWORK címke bontja ki társviszonyban VNet-előtagok az előző kimenet megjelenítése.
     
     > [!NOTE]
     > A parancs csak azt mutatja be hatékony szabályokat Ha egy NSG tartozik alhálózat, egy hálózati Adaptert, vagy mindkettőt. Előfordulhat, hogy a virtuális gépek több hálózati adapterrel alkalmazott különböző NSG. Hibaelhárításához, futtassa a parancsot az egyes hálózati adapterhez.
     > 
     > 
3. Megkönnyítése érdekében NSG-szabályok nagyobb számú szűrés, adja meg a további hibaelhárítást kell végeznie a következő parancsokat: 
   
        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView
   
    Az RDP-forgalmát (TCP-port 3389-es), a szűrő alkalmazása a Rács nézet, az alábbi ábrán látható módon:
   
    ![Szabályok listája](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
4. Ahogy látja, a táblázatos nézetben, vannak-e mindkét engedélyezése és megtagadási szabályoknak RDP. A kimenet a 2. lépésben azt mutatja, hogy a *DenyRDP* szabály van alkalmazva az NSG. A bejövő szabályok NSG-ket alkalmazva először dolgoznak. Ha a program egyezést talál, a hálózati illesztő alkalmazott NSG nem lett feldolgozva. Ebben az esetben a *DenyRDP* az alhálózatból szabály blokkolja a virtuális gép RDP (**VM1**).
   
   > [!NOTE]
   > Előfordulhat, hogy a virtuális gépek több hálózati adapter nem csatlakoztatható. Minden egyes lehet, hogy kapcsolódik egy másik alhálózat. Mivel a parancs az előző lépésben futtatása ellen egy hálózati Adaptert, fontos győződjön meg arról, hogy megadja a hálózati kapcsolat kapcsolatos hibát tapasztal. Ha nem biztos benne, mindig a parancsok alapján futtathatók egyes hálózati adapterek, a virtuális Géphez csatlakozik.
   > 
   > 
5. Távoli asztali eléréséhez VM1, módosítsa a *megtagadása RDP (3389-es)* történő *engedélyezése RDP(3389)* a a **Alhalozat_1-NSG** NSG. Győződjön meg arról, hogy a virtuális gép egy RDP-kapcsolat megnyitásával, vagy a PsPing eszköz használata nyitva-e 3389-es TCP-port. További tudnivalók a PsPing olvasásával a [PsPing letöltési oldala](https://technet.microsoft.com/sysinternals/psping.aspx)
   
    Lehet, vagy a szabályok eltávolítása egy NSG-t a következő parancs kimenetében található információk segítségével:
   
        Get-Help *-AzureRmNetworkSecurityRuleConfig

## <a name="considerations"></a>Megfontolandó szempontok
Kapcsolódási problémák elhárításakor, vegye figyelembe a következő szempontokat:

* Alapértelmezett NSG-szabályok lesz az internetről bejövő hozzáférésének letiltására, és csak a virtuális hálózat engedélyezése érkező bejövő forgalmat. Szabályok explicit módon meg kell adni a bejövő hozzáférés engedélyezése az internetről, szükség szerint.
* Ha nincs NSG biztonsági szabály, amely a virtuális gépek hálózati kapcsolattal sikertelen lesz, a probléma oka a következő lehet:
  * A virtuális gép operációs rendszerben futó tűzfal szoftver
  * A virtuális készülékek vagy a helyszíni forgalmi útvonalak. Internetes forgalmat a helyszíni kényszerített bújtatás keresztül lehet átirányítani. Az RDP/SSH-kapcsolatot az internetről a virtuális gép nem feltétlenül ezt a beállítást, attól függően, hogy miként kezeli a helyszíni hálózati hardver a forgalmat. Olvassa el a [hibaelhárítási útvonalak](virtual-network-routes-troubleshoot-powershell.md) cikk áttekintésével megismerheti, hogyan, előfordulhat, hogy a forgalmat a virtuális Gépet a bejövő és kimenő adatforgalma kell akadályozó útvonal problémák diagnosztizálásához. 
* Ha Vnetek, alapértelmezés szerint rendelkezik társviszonyban, VIRTUAL_NETWORK címke automatikusan bővített előtagok tartalmazza az társviszonyban Vnetek. Ezeket az előtagok megtekintheti a **ExpandedAddressPrefix** lista bármely Vnetben társviszony-létesítési kapcsolat kapcsolatos problémák elhárítása. 
* Hatékony biztonsági szabályok csak jelennek-e a virtuális hálózati adapter és vagy alhálózat társított NSG-t. 
* Ha nincs hálózati adapter társított NSG-k, vagy alhálózat és rendelkezik a nyilvános IP-cím, a virtuális Géphez rendelt, minden port nyissa meg a bejövő és kimenő hozzáférést lesz. Ha a virtuális gép egy nyilvános IP-címmel rendelkezik, erősen ajánlott NSG-ket alkalmaz a hálózati adapter vagy az alhálózatot.  

