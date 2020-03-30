---
title: Azure virtuális hálózat létrehozása, módosítása vagy törlése
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan hozhat létre, módosíthat vagy törölhet virtuális hálózatot az Azure-ban.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: kumud
ms.openlocfilehash: 70523dc12f3f20362fcf4a2c3cb456a182038e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280234"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Virtuális hálózat létrehozása, módosítása vagy törlése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Megtudhatja, hogy miként hozhat létre és törölhet virtuális hálózatot, és hogyan módosíthatja a meglévő virtuális hálózat beállításait, például a DNS-kiszolgálókat és az IP-címtereket. Ha új vagy a virtuális hálózatok, akkor többet megtudni róluk a [virtuális hálózat áttekintése](virtual-networks-overview.md) vagy befejezése [tutorial](quick-create-portal.md). A virtuális hálózat alhálózatokat tartalmaz. Az alhálózatok létrehozásáról, módosításáról és törléséről az [Alhálózatok kezelése című](virtual-network-manage-subnet.md)témakörben olvashat.

## <a name="before-you-begin"></a>Előkészületek

A cikk bármely részében ismertetett lépések végrehajtása előtt hajtsa végre a következő feladatokat:

- Ha még nem rendelkezik Azure-fiókkal, regisztráljon egy [ingyenes próbafiókot.](https://azure.microsoft.com/free)
- Ha a portált https://portal.azure.comhasználja, nyissa meg a, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsok at feladatok elvégzéséhez ebben a cikkben, vagy futtassa a parancsokat az [Azure Cloud Shell,](https://shell.azure.com/powershell)vagy a PowerShell futtatásával a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ez az oktatóanyag az Azure PowerShell-modul 1.0.0-s vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha az Azure parancssori felület (CLI) parancsokkal hajthatja végre a cikkben szereplő feladatokat, futtassa a parancsokat az [Azure Cloud Shellben,](https://shell.azure.com/bash)vagy a CLI futtatásával a számítógépről. Ez az oktatóanyag az Azure CLI 2.0.31-es vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha az Azure CLI helyileg fut, akkor `az login` is kell futtatnia, hogy hozzon létre egy kapcsolatot az Azure-ral.
- A fiók, amelybe bejelentkezik, vagy az Azure-hoz csatlakozik, hozzá kell rendelnie a [hálózati közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [olyan egyéni szerepkörhöz,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) amely az [Engedélyek](#permissions)listában felsorolt megfelelő műveletekhez van rendelve.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Válassza **a + Erőforrás létrehozása** > **hálózati** > **virtuális hálózat lehetőséget.**
2. Adja meg vagy jelölje ki az értékeket a következő beállításokhoz, majd válassza a **Létrehozás lehetőséget:**
   - **Név**: A névnek egyedinek kell lennie abban az [erőforráscsoportban,](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) amelyben a virtuális hálózat létrehozásához választott. A virtuális hálózat létrehozása után a név nem módosítható. Idővel több virtuális hálózatot is létrehozhat. Az elnevezési javaslatokról az [Elnevezési konvenciók (Névadási konvenciók) (Elnevezési konvenciók) (Elnevezési konvenciók) (Elnevezési](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources) Az elnevezési konvenciók követése megkönnyítheti több virtuális hálózat kezelését.
   - **Címterület**: A virtuális hálózat címterülete egy vagy több, cidr jelölésben megadott, egymást nem átfedő címtartományból áll. A megadott címtartomány lehet nyilvános vagy privát (RFC 1918). Függetlenül attól, hogy a címtartományt nyilvánosként vagy magánjellegűként határozza meg, a címtartomány csak a virtuális hálózaton belülről, az összekapcsolt virtuális hálózatokból és a virtuális hálózathoz csatlakoztatott helyszíni hálózatokról érhető el. A következő címtartományok nem adhatók hozzá:
     - 224.0.0.0/4 (csoportos küldés)
     - 255.255.255.255/32 (Adás)
     - 127.0.0.0/8 (Visszacsatolás)
     - 169.254.0.0/16 (Link-helyi)
     - 168.63.129.16/32 (Belső DNS-, DHCP- és Azure-terheléselosztó [állapotvizsgálat)](../load-balancer/load-balancer-custom-probe-overview.md#probesource)

     Bár a virtuális hálózat portálon történő létrehozásakor csak egy címtartományt határozhat meg, a virtuális hálózat létrehozása után további címtartományokat adhat a címtérhez. Ha tudni szeretné, hogyan adhat hozzá címtartományt egy meglévő virtuális hálózathoz, olvassa el a [Címtartomány hozzáadása vagy eltávolítása témakört.](#add-or-remove-an-address-range)

     >[!WARNING]
     >Ha egy virtuális hálózat címtartományai átfedésben vannak egy másik virtuális hálózattal vagy helyszíni hálózattal, a két hálózat nem csatlakoztatható. A címtartomány definiálása előtt fontolja meg, hogy a jövőben szeretné-e csatlakoztatni a virtuális hálózatot más virtuális hálózatokhoz vagy helyszíni hálózatokhoz.
     >
     >

     - **Alhálózat neve**: Az alhálózat nevének egyedinek kell lennie a virtuális hálózaton belül. Az alhálózat neve az alhálózat létrehozása után nem módosítható. A portál megköveteli, hogy egy alhálózatot definiáljon, amikor virtuális hálózatot hoz létre, még akkor is, ha a virtuális hálózatnem szükséges alhálózatokkal rendelkeznie. Virtuális hálózat létrehozásakor a portálon csak egy alhálózatot határozhat meg. A virtuális hálózat létrehozása után később további alhálózatokat adhat hozzá a virtuális hálózathoz. Ha alhálózatot szeretne hozzáadni egy virtuális hálózathoz, olvassa el az [Alhálózatok kezelése témakört.](virtual-network-manage-subnet.md) Létrehozhat egy virtuális hálózatot, amely több alhálózattal rendelkezik az Azure CLI vagy a PowerShell használatával.

       >[!TIP]
       >Néha a rendszergazdák különböző alhálózatokat hoznak létre az alhálózatok közötti forgalom útválasztásának szűrésére vagy szabályozására. Az alhálózatok definiálása előtt gondolja át, hogyan szeretné szűrni és irányítani a forgalmat az alhálózatok között. Az alhálózatok közötti forgalom szűréséről a [Hálózati biztonsági csoportok (Hálózati biztonsági csoportok)](security-overview.md)témakörben olvashat bővebben. Az Azure automatikusan irányítja a forgalmat az alhálózatok között, de felülbírálhatja az Azure alapértelmezett útvonalait. Ha többet szeretne tudni az Azures alapértelmezett alhálózati forgalom útválasztásáról, olvassa el [az Útválasztás áttekintése című témakört.](virtual-networks-udr-overview.md)
       >

     - **Alhálózati címtartomány**: A tartománynak a virtuális hálózathoz megadott címterületen belül kell lennie. A legkisebb megadható tartomány a /29, amely nyolc IP-címet biztosít az alhálózathoz. Az Azure fenntartja az első és az utolsó címet az egyes alhálózatokban a protokollok megfelelőségéhez. Három további cím van fenntartva az Azure szolgáltatás használatához. Ennek eredményeképpen a /29 alhálózati címtartományral rendelkező virtuális hálózat csak három használható IP-címmel rendelkezik. Ha virtuális hálózatot kíván csatlakoztatni egy VPN-átjáróhoz, létre kell hoznia egy átjáró alhálózatot. További információ az [átjáróalhálózatok címtartományával kapcsolatos szempontokról.](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) Az alhálózat létrehozása után bizonyos feltételek mellett módosíthatja a címtartományt. Az alhálózati címtartomány módosításáról az [Alhálózatok kezelése témakörben](virtual-network-manage-subnet.md)olvashat.
     - **Előfizetés**: [Előfizetés](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)kiválasztása . Ugyanazt a virtuális hálózatot nem használhatja egynél több Azure-előfizetésben. Az egyik előfizetésben azonban csatlakoztathat virtuális hálózatot más előfizetések virtuális hálózataihoz [virtuális hálózati társviszony-létesítéssel.](virtual-network-peering-overview.md) A virtuális hálózathoz csatlakozó Azure-erőforrásoknak ugyanabban az előfizetésben kell lenniük, mint a virtuális hálózatnak.
     - **Erőforráscsoport**: Jelöljön ki egy meglévő [erőforráscsoportot,](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) vagy hozzon létre egy újat. A virtuális hálózathoz csatlakozó Azure-erőforrás lehet ugyanabban az erőforráscsoportban, mint a virtuális hálózat, vagy egy másik erőforráscsoportban.
     - **Hely**: Válasszon ki egy [Azure-helyet](https://azure.microsoft.com/regions/), más néven régiót. A virtuális hálózat csak egy Azure-helyen lehet. Az egyik helyen lévő virtuális hálózatot azonban vpn-átjáró val egy másik helyen lévő virtuális hálózathoz is csatlakoztathatja. A virtuális hálózathoz csatlakozó Azure-erőforrásoknak ugyanazon a helyen kell lenniük, mint a virtuális hálózatnak.

**Parancsok**

- Azure CLI: [az hálózati virtuális hálózat létrehozása](/cli/azure/network/vnet)
- PowerShell: [Új-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Virtuális hálózatok és beállítások megtekintése

1. A portál tetején lévő keresőmezőbe írja be a *virtuális hálózatokat* a keresőmezőbe. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények között, jelölje ki azt.
2. A virtuális hálózatok listájában válassza ki azt a virtuális hálózatot, amelynek beállításait meg szeretné tekinteni.
3. A kiválasztott virtuális hálózathoz a következő beállítások jelennek meg:
   - **Áttekintés**: A virtuális hálózattal kapcsolatos információkat tartalmaz, beleértve a címterületet és a DNS-kiszolgálókat. A következő képernyőkép a **MyVNet**nevű virtuális hálózat áttekintő beállításait mutatja be:

     ![Hálózati adapter – áttekintés](./media/manage-virtual-network/vnet-overview.png)

     A virtuális hálózatot áthelyezheti egy másik előfizetésbe vagy erőforráscsoportba, ha az **Erőforráscsoport** vagy **az Előfizetés neve**elem melletti Módosítás lehetőséget **választja.** A virtuális hálózatok áthelyezéséről az [Erőforrások áthelyezése másik erőforráscsoportba vagy előfizetésbe (Erőforrások áthelyezése)](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json)témakörben olvashat. A cikk az előfeltételeket, valamint az erőforrások áthelyezését az Azure Portalon, a PowerShell en és az Azure CLI használatával sorolja fel. A virtuális hálózathoz kapcsolódó összes erőforrásnak a virtuális hálózattal együtt kell mozognia.
   - **Címterület**: A virtuális hálózathoz rendelt címterek vannak felsorolva. Ha meg szeretné tudni, hogyan vehet fel és távolíthat el címtartományt a címtérhez, hajtsa végre a [Címtartomány hozzáadása vagy eltávolítása](#add-or-remove-an-address-range)című részben található lépéseket.
   - **Csatlakoztatott eszközök:** A virtuális hálózathoz csatlakoztatott erőforrások listája megjelenik. Az előző képernyőképen három hálózati adapter és egy terheléselosztó csatlakozik a virtuális hálózathoz. A virtuális hálózathoz létrehozott és ahhoz kapcsolódó új erőforrások listája megjelenik. Ha töröl egy olyan erőforrást, amely a virtuális hálózathoz kapcsolódott, az már nem jelenik meg a listában.
   - **Alhálózatok**: A virtuális hálózaton belül létező alhálózatok listája látható. Az alhálózatok hozzáadásáról és eltávolításáról az [Alhálózatok kezelése témakörben](virtual-network-manage-subnet.md)olvashat.
   - **DNS-kiszolgálók:** Megadhatja, hogy az Azure belső DNS-kiszolgálója vagy egy egyéni DNS-kiszolgáló névfeloldást biztosít-e a virtuális hálózathoz csatlakoztatott eszközök számára. Ha virtuális hálózatot hoz létre az Azure Portal használatával, az Azure DNS-kiszolgálói alapértelmezés szerint névfeloldásra szolgálnak egy virtuális hálózaton belül. A DNS-kiszolgálók módosításához hajtsa végre a cikk [DNS-kiszolgálók módosítása](#change-dns-servers) című területén leírt lépéseket.
   - **Társviszony-létesítések:** Ha vannak meglévő társviszony-létesítések az előfizetésben, azok itt vannak felsorolva. Megtekintheti a meglévő társviszony-létesítések beállításait, illetve létrehozhat, módosíthat vagy törölhet társviszony-létesítéseket. Ha többet szeretne megtudni a társviszony-létesítésről, olvassa el a [Virtuális hálózati társviszony-létesítés](virtual-network-peering-overview.md).
   - **Tulajdonságok**: A virtuális hálózat beállításait jeleníti meg, beleértve a virtuális hálózat erőforrás-azonosítóját és azt az Azure-előfizetést, amelyben van.
   - **Diagram**: A diagram a virtuális hálózathoz csatlakoztatott összes eszköz vizuális megjelenítését biztosítja. A diagram néhány fontos információt tartalmaz az eszközökről. Ha ebben a nézetben szeretne kezelni egy eszközt, a diagramon jelölje ki az eszközt.
   - **Általános Azure-beállítások:** Ha többet szeretne megtudni a gyakori Azure-beállításokról, olvassa el az alábbi információkat:
     - [Tevékenységnapló](../azure-monitor/platform/platform-logs-overview.md)
     - [Hozzáférés-vezérlés (IAM)](../role-based-access-control/overview.md)
     - [Címkék](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Zárak](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Automation-szkript](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Parancsok**

- Azure CLI: [az hálózati virtuális hálózat megjelenítése](/cli/azure/network/vnet)
- PowerShell: [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Címtartomány hozzáadása vagy eltávolítása

A virtuális hálózat címtartományait hozzáadhatja és eltávolíthatja. A címtartományt cidr jelölésben kell megadni, és nem lehet átfedésben az ugyanazon virtuális hálózaton belüli más címtartományokkal. A megadott címtartományok lehetnek nyilvánosak vagy privátok (RFC 1918). Függetlenül attól, hogy a címtartományt nyilvánosként vagy magánjellegűként határozza meg, a címtartomány csak a virtuális hálózaton belülről, az összekapcsolt virtuális hálózatokból és a virtuális hálózathoz csatlakoztatott helyszíni hálózatokról érhető el. 

A virtuális hálózat címtartománya mindaddig csökkenthető, amíg az továbbra is tartalmazza a kapcsolódó alhálózatok tartományait. Ezenkívül kiterjesztheti a címtartományt, például a /16 -ról a /8-ra. 

<!-- the above statement has been edited to reflect the most recent comments on the reopened issue: https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

A következő címtartományok nem adhatók hozzá:

- 224.0.0.0/4 (csoportos küldés)
- 255.255.255.255/32 (Adás)
- 127.0.0.0/8 (Visszacsatolás)
- 169.254.0.0/16 (Link-helyi)
- 168.63.129.16/32 (Belső DNS-, DHCP- és Azure-terheléselosztó [állapotvizsgálat)](../load-balancer/load-balancer-custom-probe-overview.md#probesource)

Címtartomány hozzáadása vagy eltávolítása:

1. A portál tetején lévő keresőmezőbe írja be a *virtuális hálózatokat* a keresőmezőbe. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények között, jelölje ki azt.
2. A virtuális hálózatok listájából válassza ki azt a virtuális hálózatot, amelyhez címtartományt szeretne hozzáadni vagy eltávolítani.
3. Válassza **a Címterület**lehetőséget a **BEÁLLÍTÁSOK**csoportban.
4. Hajtsa végre az alábbi lehetőségek egyikét:
    - **Címtartomány hozzáadása**: Adja meg az új címtartományt. A címtartomány nem fedheti át a virtuális hálózathoz definiált meglévő címtartományt.
    - **Címtartomány eltávolítása**: Az eltávolítani kívánt címtartomány jobb oldalán válassza a **...** lehetőséget, majd az **Eltávolítás lehetőséget.** Ha létezik alhálózat a címtartományban, akkor nem távolíthatja el a címtartományt. Címtartomány eltávolításához először törölnie kell a címtartományban található alhálózatokat (és az alhálózatok erőforrásait).
5. Kattintson a **Mentés** gombra.

**Parancsok**

- Azure CLI: [hálózati vnet-frissítés](/cli/azure/network/vnet)
- PowerShell: [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>DNS-kiszolgálók módosítása

A virtuális hálózathoz a virtuális hálózathoz csatlakoztatott összes virtuális gép regisztrála a virtuális hálózathoz megadott DNS-kiszolgálókkal. A megadott DNS-kiszolgálót is használják a névfeloldáshoz. A virtuális gép minden hálózati adapterének saját DNS-kiszolgáló-beállításai lehetnek. Ha a hálózati adapter saját DNS-kiszolgáló-beállításokkal rendelkezik, azok felülbírálják a virtuális hálózat DNS-kiszolgálójának beállításait. A hálózati adapter DNS-beállításairól a [Hálózati csatoló feladatai és beállításai](virtual-network-network-interface.md#change-dns-servers)című témakörben olvashat bővebben. Ha többet szeretne tudni a virtuális gépek és a szerepkörpéldányok névfeloldásáról az Azure Cloud Servicesben, olvassa el [a virtuális gépek és szerepkörpéldányok névfeloldása című témakört.](virtual-networks-name-resolution-for-vms-and-role-instances.md) DNS-kiszolgáló hozzáadása, módosítása vagy eltávolítása:

1. A portál tetején lévő keresőmezőbe írja be a *virtuális hálózatokat* a keresőmezőbe. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények között, jelölje ki azt.
2. A virtuális hálózatok listájából válassza ki azt a virtuális hálózatot, amelynek DNS-kiszolgálóit módosítani szeretné.
3. Válassza a **DNS-kiszolgálók**lehetőséget a **BEÁLLÍTÁSOK csoportban.**
4. Válasszon egyet az alábbi lehetőségek közül:
   - **Alapértelmezett (Azure által biztosított)**: Minden erőforrásnév és privát IP-cím automatikusan regisztrálva lesz az Azure DNS-kiszolgálókon. Az ugyanahhoz a virtuális hálózathoz kapcsolódó erőforrások nevei feloldhatók. Ezzel a beállítással nem oldhatja fel a neveket virtuális hálózatokon keresztül. A virtuális hálózatokon keresztüli nevek feloldásához egyéni DNS-kiszolgálót kell használnia.
   - **Egyéni**: Hozzáadhat egy vagy több kiszolgálót, a virtuális hálózat Azure-korlátjáig. A DNS-kiszolgálók korlátairól az Azure-korlátozások című témakörben olvashat [bővebben.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic) A következő lehetőségek közül választhat:
   - **Cím hozzáadása**: Hozzáadja a kiszolgálót a virtuális hálózati DNS-kiszolgálók listájához. Ez a beállítás a DNS-kiszolgálót is regisztrálja az Azure-ral. Ha már regisztrált egy DNS-kiszolgálót az Azure-ban, kiválaszthatja a DNS-kiszolgálót a listában.
   - **Cím eltávolítása:** Az eltávolítani kívánt kiszolgáló mellett válassza a **...** lehetőséget, majd **az Eltávolítás lehetőséget.** A kiszolgáló törlése csak a virtuális hálózatok listájából távolítja el a kiszolgálót. A DNS-kiszolgáló továbbra is regisztrálva marad az Azure-ban a többi virtuális hálózatok használata.
   - **DNS-kiszolgálócímek átrendezése**: Fontos ellenőrizni, hogy a DNS-kiszolgálókat a környezetének megfelelő sorrendben sorolja-e fel. A DNS-kiszolgálólisták a megadott sorrendben használatosak. Nem ciklikus multiplexelésbeállításként működnek. Ha a lista első DNS-kiszolgálója elérhető, az ügyfél ezt a DNS-kiszolgálót használja, függetlenül attól, hogy a DNS-kiszolgáló megfelelően működik-e. Távolítsa el a felsorolt összes DNS-kiszolgálót, majd adja hozzá őket a kívánt sorrendben.
   - **Cím módosítása:** Jelölje ki a DNS-kiszolgálót a listában, majd írja be az új címet.
5. Kattintson a **Mentés** gombra.
6. Indítsa újra a virtuális hálózathoz csatlakoztatott virtuális gépeket, hogy hozzárendelve kapják meg az új DNS-kiszolgáló beállításait. A virtuális gépek továbbra is az aktuális DNS-beállításokat használják, amíg újra nem indítják őket.

**Parancsok**

- Azure CLI: [hálózati vnet-frissítés](/cli/azure/network/vnet)
- PowerShell: [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Virtuális hálózat törlése

A virtuális hálózat csak akkor törölhető, ha nincs hozzá kapcsolódó erőforrás. Ha a virtuális hálózaton belül bármely alhálózathoz kapcsolódó erőforrások vannak, először törölnie kell a virtuális hálózaton belüli összes alhálózathoz kapcsolódó erőforrásokat. Az erőforrások törléséhez szükséges lépések az erőforrástól függően változnak. Az alhálózatokhoz kapcsolódó erőforrások törléséről az egyes törölni kívánt erőforrástípusok dokumentációjában tájékozódhat. Virtuális hálózat törlése:

1. A portál tetején lévő keresőmezőbe írja be a *virtuális hálózatokat* a keresőmezőbe. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények között, jelölje ki azt.
2. A virtuális hálózatok listájából válassza ki a törölni kívánt virtuális hálózatot.
3. Ellenőrizze, hogy nincsenek-e eszközök a virtuális hálózathoz, ha a Beállítások csoportban a **Csatlakoztatott eszközök** **lehetőséget választja.** Ha vannak csatlakoztatott eszközök, törölnie kell őket, mielőtt törölheti a virtuális hálózatot. Ha nincsenek csatlakoztatott eszközök, válassza **az Áttekintés**lehetőséget.
4. Válassza a **Törlés** elemet.
5. A virtuális hálózat törlésének megerősítéséhez válassza az **Igen**lehetőséget.

**Parancsok**

- Azure CLI: [azure network vnet delete](/cli/azure/network/vnet)
- PowerShell: [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>Engedélyek

A virtuális hálózatokon végzett feladatok végrehajtásához a fiókot hozzá kell rendelni a [hálózati közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [olyan egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkörhöz, amely az alábbi táblázatban felsorolt megfelelő műveletekhez van rendelve:

| Műveletek                                  |   Név                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft.Network/virtualNetworks/read   |   Virtuális hálózat olvasása              |
|Microsoft.Network/virtualNetworks/write  |   Virtuális hálózat létrehozása vagy frissítése  |
|Microsoft.Network/virtualNetworks/delete |   Virtuális hálózat törlése            |

## <a name="next-steps"></a>További lépések

- Virtuális hálózat létrehozása [PowerShell-](powershell-samples.md) vagy Azure CLI-mintaparancsfájlokkal vagy Azure [Resource Manager-sablonok](template-samples.md) használatával [Azure CLI](cli-samples.md)
- [Azure-szabályzat](policy-samples.md) létrehozása és alkalmazása virtuális hálózatokra
