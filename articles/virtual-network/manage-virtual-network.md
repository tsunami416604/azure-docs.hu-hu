---
title: Létrehozása, módosítása vagy törlése az Azure-beli virtuális hálózathoz
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan létrehozása, módosítása vagy az Azure-beli virtuális hálózat törlése.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: 3f158d040654b251faebceaa2e89d0462f13c217
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016026"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Létrehozása, módosítása vagy egy virtuális hálózat törlése

Ismerje meg, hogyan hozhat létre és a virtuális hálózat törlése és a beállításokat, például a DNS-kiszolgálók és az IP-címtereket, meglévő virtuális hálózat. Ha most ismerkedik a virtuális hálózatok, többet is megtudhat a velük kapcsolatos a [virtuális hálózatok áttekintése](virtual-networks-overview.md) vagy; Ehhez hajtsa végre egy [oktatóanyag](quick-create-portal.md). A virtuális hálózatok alhálózatokat tartalmaznak. Ismerje meg, hogyan létrehozása, módosítása és alhálózatok törlése, lásd: [alhálózatok kezelését](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk bármely szakaszban ismertetett lépések elvégzése előtt hajtsa végre a következőket:

- Ha még nincs Azure-fiókkal, regisztráljon egy [ingyenes próbafiókot](https://azure.microsoft.com/free).
- Ha a portál használatával, nyissa meg a https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsokkal cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy a számítógépről futtatja a Powershellt. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Az oktatóanyaghoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha használja az Azure parancssori felület (CLI) parancsai cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/bash), vagy a parancssori felület futtatásával a számítógépről. Ehhez az oktatóanyaghoz az Azure CLI 2.0.31-es verzió vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI, is futtatni szeretné `az login` kapcsolat létrehozása az Azure-ral.
- Jelentkezzen be, vagy csatlakozhat az Azure-ban, a fiókot hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepel a megfelelő műveleteket rendelt [engedélyek ](#permissions).

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Válassza ki **+ erőforrás létrehozása** > **hálózatkezelés** > **virtuális hálózati**.
2. Adja meg vagy válassza ki a következő beállítások értékeit, majd válassza ki **létrehozás**:
    - **Név**: A nevének egyedinek kell lennie a [erőforráscsoport](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) válassza ki a virtuális hálózat létrehozásához. A virtuális hálózat létrehozása után a név nem módosítható. Idővel több virtuális hálózatot hozhat létre. Javaslatok és elnevezéséről, lásd: [elnevezési konvenciók](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). Az alábbi elnevezési segítségével könnyebben kezelheti a több virtuális hálózat.
    - **Címtér**: Virtuális hálózat címterének egy vagy több egymást nem átfedő címtartományt a CIDR-jelöléssel megadott tevődik össze. Meghatározhatja a címtartomány nyilvános vagy privát (RFC 1918) lehet. A címtartomány nyilvános vagy privát határozza meg, hogy a címtartomány érhető el csak a virtuális hálózaton belül, összekapcsolt virtuális hálózatok, és a virtuális hálózathoz csatlakozó bármely helyszíni hálózatokról. Nem adhat hozzá a következő-címtartományokat:
        - 224.0.0.0/4 (csoportos küldés)
        - 255.255.255.255/32 (közvetítés)
        - 127.0.0.0/8 (visszacsatolás)
        - 169.254.0.0/16 (kapcsolatszintű)
        - 168.63.129.16/32 (belső DNS)

      Bár csak egy címtartományt a virtuális hálózat létrehozásakor definiálhat, hozzáadhat további címtartományok a címtér a virtuális hálózat létrehozása után. Meglévő virtuális hálózaton címtartomány hozzáadása kapcsolatban lásd: [hozzáadása vagy eltávolítása egy címtartományt](#add-or-remove-an-address-range).

      >[!WARNING]
      >Ha egy virtuális hálózati címtartományt, amely átfedésben egy másik virtuális hálózattal rendelkezik, vagy a helyszíni hálózat, a két hálózat nem lehet csatlakozni. Előtt határozza meg egy címtartományt, gondolja át, hogy előfordulhat, hogy szeretne a virtuális hálózat csatlakoztatása másik virtuális hálózatok vagy a helyszíni hálózatok a jövőben.
      >
      >

    - **Alhálózat neve**: Az alhálózat nevének egyedinek kell lennie a virtuális hálózaton belül. Az alhálózat neve nem módosítható, miután az alhálózat létrejött. A portál megköveteli, hogy definiáljon egy alhálózatot egy virtuális hálózat létrehozásakor annak ellenére, hogy a virtuális hálózat nem rendelkezik alhálózatokkal. A portálon csak egy alhálózatot egy virtuális hálózat létrehozásakor határozhatja meg. Később is hozzáadhat további alhálózatokat a virtuális hálózathoz, a virtuális hálózat létrehozása után. Adjon hozzá egy alhálózatot egy virtuális hálózatot, lásd: [alhálózatok kezelését](virtual-network-manage-subnet.md). Létrehozhat egy virtuális hálózatot, amely már több alhálózat működik az Azure CLI vagy a PowerShell használatával.

      >[!TIP]
      >Egyes esetekben rendszergazdák hozhatnak létre különböző alhálózatokon szűrését, és szabályozhatja a forgalom-útválasztást az alhálózatok között. Előtt definiálhat alhálózatokat, fontolja meg, hogyan lehet, hogy szeretné szűrni és irányíthatja a forgalmat az alhálózatok között. Az alhálózatok közötti adatforgalom szűrésével kapcsolatos további tudnivalókért lásd: [hálózati biztonsági csoportok](security-overview.md). Az Azure automatikusan irányítja a forgalmat az alhálózatok, de közötti felülbírálhatja az Azure alapértelmezett útvonalakat. Azures alapértelmezett alhálózati forgalom-útválasztást kapcsolatos további információkért lásd: [Útválasztás áttekintése](virtual-networks-udr-overview.md).
      >

    - **Alhálózati címtartomány**: A tartomány megadott virtuális hálózathoz megadott címtéren belül kell lennie. A legkisebb is megadhat tartománya akár/29 méretű, ami nyolc IP-címet biztosít az alhálózat. Az Azure lefoglalja a első és utolsó cím mindegyik olyan alhálózatban, a protokoll irányítópultja. Három további címek Azure-szolgáltatás használati számára vannak fenntartva. Ennek eredményeképpen a/29 méretű egy alhálózati címtartományt a virtuális hálózat csak három használható IP-címmel rendelkezik. Ha azt tervezi, a virtuális hálózat összekapcsolása egy VPN-átjáróhoz, létre kell hoznia egy átjáró-alhálózatot. Tudjon meg többet [címhez tartomány szempontjai átjáróalhálózatok](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Meghatározott körülmények között az alhálózat létrehozását követően módosíthatja a címtartomány. Ismerje meg, hogyan módosíthatja egy alhálózati címtartományt, lásd: [alhálózatok kezelését](virtual-network-manage-subnet.md).
    - **Előfizetés**: Válassza ki a [előfizetés](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Egynél több Azure-előfizetés nem használhat ugyanabban a virtuális hálózatban. Azonban az egy előfizetéshez tartozó virtuális hálózat csatlakoztatása az más előfizetésekben található virtuális hálózatok [virtuális hálózatok közötti társviszony](virtual-network-peering-overview.md). Azure-erőforrásokkal, a virtuális hálózathoz csatlakozik a virtuális hálózatnak ugyanabban az előfizetésben kell lennie.
    - **Erőforráscsoport**: Válasszon egy meglévő [erőforráscsoport](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) , vagy hozzon létre egy újat. Egy Azure-erőforrás a virtuális hálózathoz csatlakozó lehet a virtuális hálózatnak ugyanazt az erőforráscsoportot, vagy egy másik erőforráscsoportban található.
    - **Hely**: Válassza ki az Azure-beli [hely](https://azure.microsoft.com/regions/), más néven egy régiót. Virtuális hálózat csak egy Azure-helyen lehet. Azonban akkor csatlakozhat egy virtuális hálózatot egyetlen helyen egy másik helyen található virtuális hálózat VPN-átjáró használatával. A virtuális hálózathoz csatlakozó Azure-erőforrásokkal, a virtuális hálózat azonos helyen kell lennie.

**Parancsok**

- Az Azure CLI: [az network vnet létrehozása](/cli/azure/network/vnet)
- PowerShell: [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Virtuális hálózatok megjelenítése és beállítások

1. Írja be a keresőmezőbe, a portál tetején, *virtuális hálózatok* kifejezést a keresőmezőbe. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények közül válassza ki azt.
2. A virtuális hálózatok listájában jelölje ki a virtuális hálózat, amely meg szeretné tekinteni a beállításokat.
3. A kiválasztott virtuális hálózat fel vannak sorolva a következő beállításokat:
    - **Áttekintés**: A virtuális hálózat, beleértve a címteret és a DNS-kiszolgálók arról nyújt tájékoztatást. A következő képernyőképen látható nevű virtuális hálózat áttekintése beállításait **MyVNet**:

        ![Hálózati adapter – áttekintés](./media/manage-virtual-network/vnet-overview.png)

      Áthelyezheti egy virtuális hálózat egy másik előfizetést vagy az erőforrást csoporthoz kiválasztásával **módosítása** melletti **erőforráscsoport** vagy **előfizetésnevet**. Ismerje meg, hogyan helyezheti át egy virtuális hálózatot, lásd: [erőforrások áthelyezése másik erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). A cikk felsorolja az előfeltételeket és erőforrások áthelyezése az Azure portal, PowerShell és az Azure CLI használatával. A virtuális hálózathoz csatlakozó összes erőforrás át kell helyezni a virtuális hálózattal.
    - **Címtér**: A címterek a virtuális hálózathoz rendelt jelennek meg. Megtudhatja, hogyan adhat hozzá, és távolítsa el a címtér címtartományt, hajtsa végre a lépéseit [hozzáadása vagy eltávolítása egy címtartományt](#add-or-remove-an-address-range).
    - **Csatlakoztatott eszközök**: Szerepelnek olyan erőforrások, a virtuális hálózathoz csatlakoznak. Az előző képernyőképen látható három hálózati adapter és a egy terheléselosztót a virtuális hálózathoz csatlakozik. Minden új erőforrás létrehozása és csatlakoztatása a virtuális hálózathoz jelennek meg. Ha törli a virtuális hálózathoz csatlakozó erőforrás, akkor már nem szerepelnek a listán.
    - **Alhálózatok**: A virtuális hálózaton belül található alhálózatok listája látható. Megtudhatja, hogyan adhat hozzá, és távolítsa el az alhálózatot, lásd: [alhálózatok kezelését](virtual-network-manage-subnet.md).
    - **DNS-kiszolgálók**: Megadhatja, hogy az Azure belső DNS-kiszolgáló vagy egy egyéni DNS-kiszolgáló névfeloldása a virtuális hálózathoz csatlakozó eszközöket. Az Azure portal használatával létrehozott virtuális hálózat, az Azure DNS-kiszolgálók névfeloldásra egy virtuális hálózaton belüli által használt alapértelmezett. Módosítsa a DNS-kiszolgálókat, hajtsa végre a lépéseket a [módosítása DNS-kiszolgálók](#change-dns-servers) ebben a cikkben.
    - **Társviszony-Létesítéseket**: Ha az előfizetés meglévő társviszony-létesítéseket, azok Itt láthatók. Meglévő társviszony-létesítéseket, beállításainak megtekintése vagy létrehozása, módosítása vagy törlése a társviszony-létesítéseket. Társviszony-létesítéseket kapcsolatos további információkért lásd: [virtuális hálózatok közötti társviszony](virtual-network-peering-overview.md).
    - **Tulajdonságok**: A virtuális hálózattal, többek között a virtuális hálózati erőforrás-azonosító és az Azure-előfizetés van kapcsolatos beállításokat jeleníti meg.
    - **Diagram**: A diagram az összes olyan eszköz, a virtuális hálózathoz csatlakozó vizuálisan biztosít. Az ábrán néhány fő eszközökre vonatkozó adatokat tartalmaz. Ebben a nézetben, a diagram az eszköz felügyelete válassza ki az eszközt.
    - **Gyakori Azure beállításai**: Gyakori Azure beállításaival kapcsolatos további tudnivalókért tekintse meg a következő információkat:
        *   [Tevékenységnapló](../azure-monitor/platform/activity-logs-overview.md)
        *   [Hozzáférés-vezérlés (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
        *   [Címkék](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Zárolások](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Automation-szkript](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Parancsok**

- Az Azure CLI: [az network vnet show](/cli/azure/network/vnet#az_network_vnet_show)
- PowerShell: [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Adja hozzá, vagy távolítsa el a címtartomány

Adja hozzá, és távolítsa el a virtuális hálózat címtartományát. Címtartományt a CIDR-jelölésrendszerben kell megadni, és ugyanazon a virtuális hálózaton lévő más címtartományok nem lehet átfedésben. Meghatározhatja a címtartomány nyilvános vagy privát (RFC 1918) lehet. A címtartomány nyilvános vagy privát határozza meg, hogy a címtartomány érhető el csak a virtuális hálózaton belül, összekapcsolt virtuális hálózatok, és a virtuális hálózathoz csatlakozó bármely helyszíni hálózatokról. Nem adhat hozzá a következő-címtartományokat:

- 224.0.0.0/4 (csoportos küldés)
- 255.255.255.255/32 (közvetítés)
- 127.0.0.0/8 (visszacsatolás)
- 169.254.0.0/16 (kapcsolatszintű)
- 168.63.129.16/32 (belső DNS)

Adja hozzá, vagy távolítsa el a címtartomány:

1. Írja be a keresőmezőbe, a portál tetején, *virtuális hálózatok* kifejezést a keresőmezőbe. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények közül válassza ki azt.
2. A virtuális hálózatok listájában jelölje ki a virtuális hálózatot, amelyhez szeretné hozzáadni vagy eltávolítani egy címtartományt.
3. Válassza ki **címtér**alatt **beállítások**.
4. Hajtsa végre az alábbi lehetőségek közül:
    - **Adjon hozzá egy címtartományt**: Az új címtartományt adjon meg. A címtartomány nem lehetnek átfedésben a virtuális hálózatban a meglévő címtartománnyal.
    - **Távolítsa el a címtartomány**: A címtartomány el kívánja távolítani a jobb oldalon válassza ki a **...** , majd **eltávolítása**. Ha egy alhálózat címtartománya már létezik, a címtartomány nem távolítható el. Címtartomány törléséhez először törölnie kell esetleges olyan alhálózatokra (és az alhálózatok benne erőforrást), amely a címtartomány szerepel.
5. Kattintson a **Mentés** gombra.

**Parancsok**

- Az Azure CLI: [az network vnet update](/cli/azure/network/vnet#az_network_vnet_update)
- PowerShell: [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)

## <a name="change-dns-servers"></a>Módosítsa a DNS-kiszolgálók

Virtuális gépek csatlakoznak a virtuális hálózat nyilvántartásba, adja meg a virtuális hálózat DNS-kiszolgálókkal. Akkor is használhatja a megadott DNS-kiszolgáló a névfeloldáshoz. A virtuális gép minden hálózati adapteréhez (NIC) a saját DNS-kiszolgáló beállításainak rendelkezhet. Ha egy hálózati Adaptert a saját DNS-kiszolgálójának beállításait, azok felülírják a virtuális hálózat DNS-kiszolgálójának beállításait. Hálózati adapter DNS-beállítások kapcsolatos további információkért lásd: [hálózati adapter feladatokról és konfigurálási](virtual-network-network-interface.md#change-dns-servers). További információ a névfeloldást a virtuális gépek és szerepkörpéldányok az Azure Cloud Servicesben, lásd: [névfeloldás virtuális gépek és szerepkörpéldányok](virtual-networks-name-resolution-for-vms-and-role-instances.md). Hozzáadása, módosítása vagy egy DNS-kiszolgáló eltávolítása:

1. Írja be a keresőmezőbe, a portál tetején, *virtuális hálózatok* kifejezést a keresőmezőbe. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények közül válassza ki azt.
2. A virtuális hálózatok listájában jelölje ki a virtuális hálózatot, amelynek meg szeretné változtatni a DNS-kiszolgálók számára.
3.  Válassza ki **DNS-kiszolgálók**alatt **beállítások**.
4. Válasszon az alábbi lehetőségek közül:
    - **Alapértelmezett (Azure által biztosított)**: Összes erőforrás nevét, és a magánhálózati IP-címek automatikusan regisztrálja az Azure DNS-kiszolgálókra. Nevek közötti bármely virtuális hálózathoz csatlakozó erőforrás oldható meg. Ez a beállítás nevek feloldása több virtuális hálózaton nem használható. A névfeloldás virtuális hálózatok között, egy egyéni DNS-kiszolgálót kell használnia.
    - **Egyéni**: Az Azure korlátig egy virtuális hálózat egy vagy több kiszolgálót is hozzáadhat. DNS-kiszolgálói korlátok kapcsolatos további információkért lásd: [Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). A következő lehetőségek közül választhat:
        - **Cím hozzáadása**: A kiszolgáló hozzáadása a virtuális hálózat DNS-kiszolgálók listájához. Ezzel a beállítással a DNS-kiszolgáló is regisztrálja az Azure-ral. Ha már regisztrálta egy DNS-kiszolgálót az Azure-ral, kiválaszthatja, hogy a DNS-kiszolgáló a listában.
        - **Egy cím eltávolítása**: Mellett a kiszolgáló, amely a el kívánja távolítani, jelölje be a **...** , majd **eltávolítása**. A kiszolgáló törlésével eltávolítja a kiszolgáló csak a virtuális hálózat listából. A DNS-kiszolgáló használatára a virtuális hálózatokat Azure-ban regisztrált marad.
        - **DNS-kiszolgálócímek átrendezése**: Fontos győződjön meg arról, hogy a DNS-kiszolgálók, a környezetnek megfelelő sorrendben listázza. DNS-kiszolgáló listákat, melyek a megadott sorrendben kell használni. A Ciklikus időszeleteléses telepítési során nem működnek. Ha a lista első DNS-kiszolgáló elérhető, az ügyfél használja a DNS-kiszolgálónak, függetlenül attól, hogy a DNS-kiszolgáló megfelelően működik-e. Távolítsa el az összes felsorolt DNS-kiszolgálók, és adja őket vissza a kívánt sorrendben.
        - **Módosítsa a címet**: Jelölje ki a listát a DNS-kiszolgáló, és adja meg az új címet.
5. Kattintson a **Mentés** gombra.
6. Indítsa újra a virtuális gépek csatlakoznak a virtuális hálózathoz, így az új DNS-beállításokat kap. Virtuális gépek továbbra is használhatja az aktuális DNS-beállításait, azok újraindításáig.

**Parancsok**

- Az Azure CLI: [az network vnet update](/cli/azure/network/vnet#az_network_vnet_update)
- PowerShell: [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Virtuális hálózat törlése

Virtuális hálózat csak akkor, ha egyetlen erőforrás sem csatlakozik, vagy törölheti. Ha a virtuális hálózaton belül minden olyan alhálózathoz csatlakozó erőforrásokat, az erőforrásokat a virtuális hálózaton lévő összes alhálózathoz csatlakozó először törölnie kell. Erőforrás törlése a lépéseket, az erőforrás változhat. Az alhálózatokhoz csatlakoztatott források törlése, olvassa el az egyes erőforrástípusok törli dokumentációját. Virtuális hálózat törlése:

1. Írja be a keresőmezőbe, a portál tetején, *virtuális hálózatok* kifejezést a keresőmezőbe. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények közül válassza ki azt.
2. A virtuális hálózatok listájában jelölje ki a törölni kívánt virtuális hálózatot.
3. Győződjön meg arról, hogy nincsenek-e választva a virtuális hálózathoz csatlakozó eszközök **csatlakoztatott eszközök**alatt **beállítások**. Ha vannak csatlakoztatott eszközök, törölnie kell azokat a virtuális hálózat törlése előtt. Ha nincsenek csatlakoztatott eszközök, válassza ki a **áttekintése**.
4. Válassza a **Törlés** elemet.
5. A virtuális hálózat a törlés megerősítéséhez válassza **Igen**.

**Parancsok**

- Az Azure CLI: [az azure network vnet törlése](/cli/azure/network/vnet#az_network_vnet_delete)
- PowerShell: [Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork)

## <a name="permissions"></a>Engedélyek

Feladatok végrehajtásához a virtuális hálózatokon, a fiókot hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy olyan [egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkör, amely hozzá van rendelve a megfelelő műveleteket az alábbi táblázatban felsorolt:

| Műveletek                                  |   Name (Név)                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft.Network/virtualNetworks/read   |   Olvassa el a virtuális hálózat              |
|Microsoft.Network/virtualNetworks/write  |   Egy virtuális hálózat létrehozása vagy módosítása  |
|Microsoft.Network/virtualNetworks/delete |   Virtuális hálózat törlése            |

## <a name="next-steps"></a>További lépések

- Hozzon létre egy virtuális hálózatot az [PowerShell](powershell-samples.md) vagy [Azure CLI-vel](cli-samples.md) parancsfájlokat, vagy az Azure-minta [Resource Manager-sablonok](template-samples.md)
- Hozzon létre, és a alkalmazni [az Azure policy](policy-samples.md) virtuális hálózatok