---
title: Hálózati biztonsági csoport – portál hibaelhárítása |} Microsoft Docs
description: Ismerje meg a hálózati biztonsági csoportok hibaelhárítása az Azure Resource Manager üzembe helyezési modellel, az Azure portál használatával.
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: be400d674068d89f60d3c999006bc9291944ab1c
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="troubleshoot-network-security-groups-using-the-azure-portal"></a>Hálózati biztonsági csoportok az Azure portál használatával hibaelhárítása
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-nsg-troubleshoot-portal.md)
> * [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

Ha konfigurálva a hálózati biztonsági csoportokkal (NSG-k) a virtuális gépen (VM), és virtuális gép kapcsolódási problémákat tapasztal, ez a cikk áttekintést diagnosztika képességet biztosít a További hibaelhárítás elősegítése érdekében az NSG-ket.

Az NSG-k lehetővé teszik a típusú forgalom, hogy a virtuális gépek (VM) mindkét folyamata. NSG-ket alhálózatokra egy Azure virtuális hálózatot (VNet), hálózati adapterek (NIC) vagy mindkettőt alkalmazhatók. A hatékony szabályokat egy hálózati adapter egyszerűsítése érdekében a szabályokat, amelyek szerepelnek az NSG-ket egy hálózati adapterre alkalmazza, és az alhálózat van csatlakoztatva. Ezek az NSG-k között szabályok néha ütköznek egymással, és hatással lehet a virtuális gépek hálózati kapcsolattal.  

Az NSG-k, az összes hatékony biztonsági szabály tekintheti meg a virtuális gép hálózati adapterek alkalmazott. Ez a cikk bemutatja, hogyan VM csatlakozási problémák ezek a szabályok használatával az Azure Resource Manager üzembe helyezési modellben. Ha még nem ismeri a virtuális hálózat és NSG fogalmakat, olvassa el a [virtuális hálózati](virtual-networks-overview.md) és [hálózati biztonsági csoportok](virtual-networks-nsg.md) áttekintése cikkeket.

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Hatékony biztonsági szabályok használata hibák elhárításához a virtuális gép forgalom áramlását
A következő forgatókönyv gyakori kapcsolati probléma példája:

A virtuális gépek nevű *VM1* nevű alhálózat része *Alhalozat_1* nevű egy Vneten belül *WestUS-VNet1*. A virtuális gép RDP Funkciót használnak a 3389-es TCP-porton keresztül történő csatlakozásra tett kísérlet sikertelen lesz. Az NSG-k két hálózati adapter szintjén alkalmazhatóak *VM1-NIC1* és az alhálózati *Alhalozat_1*. Forgalom 3389-es TCP-port engedélyezve van a hálózati interfészhez társított NSG *VM1-NIC1*, azonban a TCP ping VM1 a következőre port 3389 meghiúsul.

Ebben a példában a 3389-es portot használja, amíg az alábbi lépések segítségével határozza meg a bejövő és kimenő kapcsolódási hibák bármely porton keresztül.

### <a name="vm"></a>A virtuális gép hatékony biztonsági szabályok megtekintése
Az alábbi lépésekkel hibáinak elhárítása az NSG-ket a virtuális gépek:

A hatékony biztonsági szabályok teljes listáját megtekintheti a hálózati Adapterhez, maga a virtuális gépről. Azt is megteheti, módosítása, és a hálózati adapter és az alhálózati NSG-szabályok törlése a hatékony szabályokat paneljéről, ha Ön rendelkezik engedéllyel a műveletek végrehajtására.

1. Jelentkezzen be az Azure portálon, a https://portal.azure.com az Azure-fiókot. Ön fiókjához társítva kell lenni a *Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action* műveletet a hálózati adapter. Műveletek fiókokhoz rendeléséhez, lásd: [egyedi szerepkörök létrehozását, hozzáférés-vezérlési átruházásához](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Kattintson a **minden szolgáltatás**, majd kattintson a **virtuális gépek** a megjelenő listában.
3. Jelöljön ki egy virtuális Gépet, a listában megjelenő hibaelhárítása, és a beállítások egy virtuális gép panel jelenik meg.
4. Kattintson a **derítse & felmerülő problémák megoldásához** , és válassza a gyakori probléma. Ehhez a példához **nem lehet csatlakozni a windowsos virtuális Gépemhez** van kiválasztva. 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image1.png)
5. Az alábbi képen látható módon lépéseket a probléma alatt jelenik meg: 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image2.png)
   
    Kattintson a *hatékony biztonsági csoportszabályok* a javasolt lépéseket listájában.
6. A **lekérni a hatékony biztonsági szabályokat** panel jelenik meg, az alábbi ábrán látható módon:
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image3.png)
   
    Figyelje meg, hogy a kép a következő szakaszok:
   
   * **Hatókör:** beállítása *VM1*, a virtuális Gépet, a 3.
   * **Hálózati adapter:** *VM1-NIC1* van kiválasztva. A virtuális gépek több hálózati adapterek (NIC) lehet. Egyes hálózati adapterek egyedi hatékony biztonsági szabályokat is rendelkezhetnek. Hibaelhárításához, szükség lehet a hatékony biztonsági szabályok megjelenítése minden hálózati adaptert.
   * **Társított NSG-ket:** NSG-ket a hálózati adapter és a hálózati adapter csatlakozik-e az alhálózat alkalmazhatók. A képen látható az NSG telepítve van a hálózati adapter, mind a alhálózathoz csatlakozik. Kattintson az NSG neve közvetlenül a az NSG-ket a szabályok módosítása.
   * **VM1-nsg lap:** a megjelenített szabályok listáján a van alkalmazza az NSG-t a hálózati adaptert. Több alapértelmezett szabályok jönnek létre az Azure-ban, amikor létrejön egy NSG. Nem távolítható el az alapértelmezett szabályokkal, de a nagyobb prioritású szabályokkal felülbírálhatja. Alapértelmezett szabályok kapcsolatos további tudnivalókért olvassa el a [NSG áttekintése](virtual-networks-nsg.md#default-rules) cikk.
   * **Céloszlop:** szabályainak szöveget tartalmaz az oszlopot, míg mások a címelőtagokat. A szöveg érvényesek, a biztonsági szabály lett létrehozva alapértelmezett címkék esetén. A címkék olyan rendszer által biztosított azonosítók, amelyek megfelelnek a több előtagok. A szabály címkével ellátott, például kiválasztásával *AllowInternetOutBound*, a előtag szerepel az a **cím előtagokat** panelen.
   * **Letöltés:** szabályok listáján a hosszú lehet. Egy CSV-fájlt a szabályok kapcsolat nélküli elemzéshez kattintva letöltheti **letöltése** és a fájl mentése.
   * **AllowRDP** bejövő forgalomra vonatkozó szabály: Ez a szabály lehetővé teszi, hogy a virtuális gép RDP-kapcsolatok.
7. Kattintson a **Alhalozat_1-NSG** fülre kattintva megtekintheti az NSG hatékony szabálygyűjtemény alkalmazva, az alábbi ábrán látható módon: 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image4.png)
   
    Figyelje meg a *denyRDP* **bejövő** szabály. Bejövő szabályok az alhálózat alkalmazása előtt a hálózati kapcsolódási szabálya értékeli ki a rendszer. A megtagadási szabály van érvényben, az alhálózat, mert a kérelem való csatlakozáshoz a 3389-es TCP sikertelen, mert a rendszer soha nem értékeli ki, az engedélyezési szabály a hálózati adapter. 
   
    A *denyRDP* szabály az oka miért nem működik a távoli ASZTAL kapcsolaton keresztül. Akkor kell megoldani a problémát.
   
   > [!NOTE]
   > Ha a virtuális gép társított hálózati adapter nem futó állapotban van, vagy az NSG-k alkalmazása még nem történt a hálózati adapter vagy az alhálózat, szabályait nem jelennek meg.
   > 
   > 
8. NSG-szabályok szerkesztéséhez kattintson *Alhalozat_1-NSG* a a **társított NSG-k** szakasz.
   Ekkor megnyílik a **Alhalozat_1-NSG** panelen. Közvetlenül szerkesztheti a szabályok kattintva **bejövő biztonsági szabályok**.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image7.png)
9. Eltávolítása után a *denyRDP* a bejövő forgalomra vonatkozó szabály a **Alhalozat_1-NSG** és hozzáadása egy *allowRDP* szabály, a hatályos szabályok az alábbi képen lista tűnik:
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image8.png)
   
    Győződjön meg arról, hogy a virtuális gép egy RDP-kapcsolat megnyitásával, vagy a PsPing eszköz használata nyitva-e 3389-es TCP-port. További tudnivalók a PsPing olvasásával a [PsPing letöltési oldala](https://technet.microsoft.com/sysinternals/psping.aspx).

### <a name="nic"></a>Egy adott hálózati csatoló hatékony biztonsági szabályok megtekintése
Ha a virtuális gép forgalom áramlását kihatással van a megadott hálózati adapter, az alábbi lépések végrehajtásával a hatékony szabályok teljes listáját megtekintheti a hálózati adapter a hálózati adapterek környezetben:

1. Az Azure portálon, a bejelentkezés https://portal.azure.com.
2. Kattintson a **minden szolgáltatás**, majd kattintson **hálózati illesztőt** a megjelenő listában.
3. Válasszon hálózati interfészt. Az alábbi képen látható, a hálózati adapter nevű *VM1-NIC1* van kiválasztva.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image5.png)
   
    Figyelje meg, hogy a **hatókör** a kiválasztott hálózati adapter van beállítva. További információt kapcsolatos további tudnivalókért olvassa el a 6. lépésében a **hibáinak elhárítása az NSG-k a virtuális gépek** című szakaszát.
   
   > [!NOTE]
   > Az NSG-t egy adott hálózati csatoló törlődik, ha az alhálózat NSG még mindig hatékony, a megadott adapteren zajlik. Ebben az esetben a kimeneti volna csak jelenít meg szabályokat a alhálózatból NSG. Szabályok csak akkor jelennek meg, ha a hálózati Adaptert egy virtuális Géphez van csatolva.
   > 
   > 
4. Közvetlenül szerkesztheti szabályok egy hálózati adapter és alhálózat társított NSG-ket. Megtudhatja, hogyan, olvassa el a 8. lépésével a **tekintse meg a virtuális gép hatékony biztonsági szabályokat** című szakaszát.

## <a name="nsg"></a>A hálózati biztonsági csoport (NSG) hatékony biztonsági szabályok megtekintése
Ha módosítja az NSG-szabályok, érdemes lehet tekintse át a szabályok egy adott virtuális Gépet felvenni a hatását. A hatékony biztonsági szabályok teljes listáját megtekintheti a hálózati adapterek összes adataként megadott NSG, anélkül, hogy a megadott NSG paneljéről környezetben váltson. Az NSG belüli hatékony szabály elhárításához végezze el az alábbi lépéseket:

1. Az Azure portálon, a bejelentkezés https://portal.azure.com.
2. Kattintson a **minden szolgáltatás**, majd kattintson a **hálózati biztonsági csoportok** a megjelenő listában.
3. Válasszon egy NSG. Az alábbi képen látható az NSG nevű VM1-NSG-t választotta ki.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image6.png)
   
    Figyelje meg az előző ábrán alábbi szakaszait:
   
   * **Hatókör:** beállítása a kijelölt NSG.
   * **Virtuális gép:** alhálózat amikor egy NSG-t alkalmazzák, akkor minden hálózati interfészen alhálózathoz kapcsolódó összes virtuális gép csatlakozik, amelyre vonatkozik. Ez a lista tartalmazza az NSG vonatkozik az összes virtuális gépet. A virtuális gép kiválaszthatja a listából.
     
     > [!NOTE]
     > Ha csak egy üres alhálózati alkalmazza az NSG-t, virtuális gépek nem jelenik meg. Ha egy hálózati Adaptert, amely nincs társítva van egy virtuális gép alkalmazza az NSG, ezeket a hálózati adapterek is nem jelenik meg. 
     > 
     > 
   * **Hálózati adapter:** egy virtuális Gépet is több hálózati illesztőre van szükség. Kiválaszthatja a kijelölt virtuális gép hálózati kapcsolatát.
   * **AssociatedNSGs:** bármikor, egy hálózati adapter rendelkezhet legfeljebb két hatékony, egyet a hálózati adapter és egy másik, az alhálózati alkalmazott NSG. A hatókör VM1-NSG-t, ha a hálózati adapter egy hatékony alhálózati NSG van kiválasztva, de ekkor mindkét NSG-ket.
4. Közvetlenül szerkesztheti szabályok egy hálózati adapter vagy az alhálózat társított NSG-ket. Megtudhatja, hogyan, olvassa el a 8. lépésével a **tekintse meg a virtuális gép hatékony biztonsági szabályokat** című szakaszát.

További információt kapcsolatos további tudnivalókért olvassa el a 6. lépésében a **tekintse meg a virtuális gép hatékony biztonsági szabályokat** című szakaszát.

> [!NOTE]
> Abban az esetben, ha egy alhálózat és a hálózati adapter egyes lehet NSG látták el, egy NSG-t több hálózati adapter és több alhálózattal társított lehet.
> 
> 

## <a name="considerations"></a>Megfontolandó szempontok
Kapcsolódási problémák elhárításakor, vegye figyelembe a következő szempontokat:

* Alapértelmezett NSG-szabályok lesz az internetről bejövő hozzáférésének letiltására, és csak a virtuális hálózat engedélyezése érkező bejövő forgalmat. Szabályok explicit módon meg kell adni a bejövő hozzáférés engedélyezése az internetről, szükség szerint.
* Ha nincs NSG biztonsági szabály, amely a virtuális gépek hálózati kapcsolattal sikertelen lesz, a probléma oka a következő lehet:
  * A virtuális gép operációs rendszerben futó tűzfal szoftver
  * A virtuális készülékek vagy a helyszíni forgalmi útvonalak. Internetes forgalmat a helyszíni kényszerített bújtatás keresztül lehet átirányítani. Az RDP/SSH-kapcsolatot az internetről a virtuális gép nem feltétlenül ezt a beállítást, attól függően, hogy miként kezeli a helyszíni hálózati hardver a forgalmat. Olvassa el a [hibaelhárítási útvonalak](virtual-network-routes-troubleshoot-powershell.md) cikk áttekintésével megismerheti, hogyan, előfordulhat, hogy a forgalmat a virtuális Gépet a bejövő és kimenő adatforgalma kell akadályozó útvonal problémák diagnosztizálásához. 
* Ha Vnetek, alapértelmezés szerint rendelkezik társviszonyban, VIRTUAL_NETWORK címke automatikusan bővített előtagok tartalmazza az társviszonyban Vnetek. Ezeket az előtagok megtekintheti a **ExpandedAddressPrefix** lista bármely Vnetben társviszony-létesítési kapcsolat kapcsolatos problémák elhárítása. 
* Hatékony biztonsági szabályok csak jelennek-e a virtuális hálózati adapter és vagy alhálózat társított NSG-t. 
* Ha nincs hálózati adapter társított NSG-k, vagy alhálózat és rendelkezik a nyilvános IP-cím, a virtuális Géphez rendelt, minden port nyissa meg a bejövő és kimenő hozzáférést lesz. Ha a virtuális gép egy nyilvános IP-címmel rendelkezik, erősen ajánlott NSG-ket alkalmaz a hálózati adapter vagy az alhálózatot.

