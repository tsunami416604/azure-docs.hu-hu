---
title: Virtuális hálózatok közötti társviszony-létesítési problémák hibaelhárítása
description: A virtuális hálózati társviszony-létesítési problémák többségének megoldásához szükséges lépések.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 662619e101b45d1dd8b34ea97e31f214b254124a
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521887"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Virtuális hálózatok közötti társviszony-létesítési problémák hibaelhárítása

Ez a hibaelhárítási útmutató a virtuális [hálózati társviszony-létesítési](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) problémák többségének megoldásához szükséges lépéseket ismerteti.

![Virtuális hálózati társviszony-létesítés diagramja](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Virtuális hálózati társviszony-létesítés konfigurálása két virtuális hálózat között

A virtuális hálózatok ugyanabban az előfizetésben vagy különböző előfizetésekben vannak?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>A virtuális hálózatok ugyanabban az előfizetésben vannak

Az azonos előfizetésben lévő virtuális hálózatok virtuális hálózati társviszony-létesítésének konfigurálásához használja a következő cikkekben ismertethetőket:

* Ha a virtuális hálózatok *ugyanabban*a régióban találhatók, olvassa el a [Társviszony-létesítés létrehozása](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering)című témakört.
* Ha a virtuális hálózatok a *különböző régiókban*találhatók, olvassa el a [Virtuális hálózati társviszony-létesítés című témakört.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 

> [!Note]
> A kapcsolat nem működik a globális virtuális hálózati társviszony-létesítésen keresztül a következő erőforrások esetében: 
>
> * Virtuális gépek (VM-ek) az alapszintű belső terheléselosztó (ILB) termékváltozat mögött
> * Redis-gyorsítótár (egyszerű ILB-termékváltozatot használ)
> * Alkalmazásátjáró (egyszerű ILB-termékváltozatot használ)
> * Virtuálisgép-méretezési csoportok (egyszerű ILB-termékhalmazt használ)
> * Azure Service Fabric-fürtök (egyszerű ILB-termékváltozatot használ)
> * SQL Server Mindig be van kapcsolva (egyszerű ILB-termékváltozatot használ)
> * Azure App Service Environment for PowerApps (alapszintű ILB-termékváltozatot használ)
> * Azure API-kezelés (egyszerű ILB-termékváltozatot használ)
> * Azure Active Directory tartományi szolgáltatások (Azure AD DS) (egyszerű ILB-termékváltozatot használ)

További információt a globális társviszony-létesítés [követelményei és korlátai](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) tartalmaznak.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>A virtuális hálózatok különböző előfizetésekben vagy Active Directory-bérlőkben találhatók

A virtuális hálózati társviszony-létesítés konfigurálása a virtuális hálózatok különböző előfizetések vagy Active Directory-bérlők, [lásd: Társviszony-létesítés létrehozása különböző előfizetések az Azure CLI.](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)

> [!Note]
> A hálózati társviszony-létesítés konfigurálásához mindkét előfizetésben **hálózati közreműködői** engedélyekkel kell rendelkeznie. További információt a [Társviszony-létesítési engedélyek című témakörben talál.](virtual-network-manage-peering.md#permissions)

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Virtuális hálózati társviszony-létesítés konfigurálása a helyszíni erőforrásokat használó, küllős topológiával

![Virtuális hálózati társviszony-létesítés diagramja helyszíni küllővel](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>Helyek közötti vagy ExpressRoute-kapcsolat esetén

Kövesse a következő lépéseket: [A VPN-átjáró átvitelének konfigurálása a virtuális hálózati társviszony-létesítéshez.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)

### <a name="for-point-to-site-connections"></a>Pont-hely kapcsolatok esetén

1. Kövesse a következő lépéseket: [A VPN-átjáró átvitelének konfigurálása a virtuális hálózati társviszony-létesítéshez.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)
2. A virtuális hálózati társviszony-létesítés létrehozása vagy módosítása után töltse le és telepítse újra a pont-hely csomagot, hogy a pont-hely ügyfelek megkapják a frissített útvonalakat a küllővirtuális hálózatra.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Virtuális hálózati társviszony-létesítés konfigurálása a központi küllős topológia virtuális hálózatával

![Virtuális hálózati társviszony-létesítés immár virtuális hálózati küllővel](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>A virtuális hálózatok ugyanabban a régióban találhatók


1. A központi virtuális hálózatban konfiguráljon egy hálózati virtuális berendezést (NVA).
1. A küllővirtuális hálózatokban a felhasználó által definiált útvonalak a következő ugrás típusú "hálózati virtuális berendezés" alkalmazott.

További információ: [Service chaining](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining).

> [!Note]
> Ha segítségre van szüksége az NVA beállításához, [forduljon az NVA szállítójához.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

Az NVA-eszköz beállításával és útválasztásával kapcsolatos hibák elhárításához lásd: [Hálózati virtuális berendezésekkel kapcsolatos problémák az Azure-ban.](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)

### <a name="the-virtual-networks-are-in-different-regions"></a>A virtuális hálózatok különböző régiókban vannak

A globális virtuális hálózati társviszony-létesítésen keresztüli átvitel mostantól támogatott. A kapcsolat nem működik a globális virtuális hálózati társviszony-létesítésen keresztül a következő erőforrások esetében:

* Virtuális gépek az alapvető ILB-termékváltozat mögött
* Redis-gyorsítótár (egyszerű ILB-termékváltozatot használ)
* Alkalmazásátjáró (egyszerű ILB-termékváltozatot használ)
* Méretezési csoportok (egyszerű ILB-termékhalmazt használ)
* Szolgáltatásháló-fürtök (egyszerű ILB-termékváltozatot használ)
* SQL Server Mindig be van kapcsolva (egyszerű ILB-termékváltozatot használ)
* App Service-környezet (alapszintű ILB-termékváltozatot használ)
* API-kezelés (egyszerű ILB-termékváltozatot használ)
* Azure AD DS (egyszerű ILB-termékváltozatot használ)

Ha többet szeretne megtudni a globális társviszony-létesítési követelményekről és korlátozásokról, olvassa el a [Virtuális hálózati társviszony-létesítés](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints).

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Két társviszony-létesített virtuális hálózat közötti kapcsolódási probléma elhárítása

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) egy olyan fiókkal, amely rendelkezik a szükséges [szerepkörökkel és engedélyekkel.](virtual-network-manage-peering.md#permissions) Jelölje ki a virtuális **hálózatot, válassza a Társviszony-létesítés**lehetőséget, majd jelölje be az **Állapot** mezőt. Mi a helyzet?

### <a name="the-peering-status-is-connected"></a>A társviszony-létesítésállapota "Csatlakoztatva"

A probléma elhárítása:

1. Ellenőrizze a hálózati forgalom áramlását:

   A [Kapcsolathiba elhárítása](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) és [az IP-folyamat ellenőrzése](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) a forrás virtuális gépről a cél virtuális gépre segítségével határozza meg, hogy van-e olyan NSG vagy UDR, amely interferenciát okoz a forgalomban.

   Ha tűzfalat vagy NVA-t használ: 
   1. Dokumentálja az UDR-paramétereket, hogy a lépés befejezése után visszaállíthassa őket.
   2. Távolítsa el az UDR-t a forrás virtuálisgép-alhálózatból vagy hálózati adapterből, amely az NVA-ra mutat a következő ugrásként. Ellenőrizze a kapcsolatot a forrás virtuális gép közvetlenül a cél, amely megkerüli az NVA. Ha ez a lépés nem működik, olvassa el az [NVA hibaelhárító című témakört.](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)

2. A hálózat nyomon követése: 
   1. Hálózati nyomkövetés indítása a célvirtuális gépen. Windows esetén használhatja a **Netsh**. Linux esetén használja a **TCPDump**.
   2. Futtassa a **TcpPing** vagy **a PsPing protokollt** a forrásból a cél IP-címre.

      Ez egy példa a **TcpPing parancsra:**`tcping64.exe -t <destination VM address> 3389`

   3. A **TcpPing** befejezése után állítsa le a hálózati nyomkövetést a célon.
   4. Ha csomagok érkeznek a forrásból, nincs hálózati probléma. Vizsgálja meg mind a virtuális gép tűzfala és az alkalmazás figyelése a porton, hogy keresse meg a konfigurációs probléma.

   > [!Note]
   > A következő erőforrástípusokhoz nem csatlakozhat globális virtuális hálózati társviszony-létesítésen (különböző régiókban lévő virtuális hálózatokon):
   >
   > * Virtuális gépek az alapvető ILB-termékváltozat mögött
   > * Redis-gyorsítótár (egyszerű ILB-termékváltozatot használ)
   > * Alkalmazásátjáró (egyszerű ILB-termékváltozatot használ)
   > * Méretezési csoportok (egyszerű ILB-termékhalmazt használ)
   > * Szolgáltatásháló-fürtök (egyszerű ILB-termékváltozatot használ)
   > * SQL Server Mindig be van kapcsolva (egyszerű ILB-termékváltozatot használ)
   > * App Service-környezet (alapszintű ILB-termékváltozatot használ)
   > * API-kezelés (egyszerű ILB-termékváltozatot használ)
   > * Azure AD DS (egyszerű ILB-termékváltozatot használ)

További információt a globális társviszony-létesítés [követelményei és korlátai](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) tartalmaznak.

### <a name="the-peering-status-is-disconnected"></a>A társviszony-létesítésállapota "Leválasztva"

A probléma megoldásához törölje a társviszony-létesítést mindkét virtuális hálózatról, majd hozza létre újra őket.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>A központi küllős virtuális hálózat és a helyszíni erőforrás közötti kapcsolódási probléma elhárítása

A hálózat külső NVA- vagy VPN-átjárót használ?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>A hálózatom külső NVA- vagy VPN-átjárót használ

A külső nva- vagy VPN-átjárót érintő kapcsolódási problémák elhárításához olvassa el az alábbi cikkeket:

* [NVA hibaelhárító](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Szolgáltatásláncolás](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>A hálózatom nem használ külső NVA- vagy VPN-átjárót

A központi virtuális hálózat és a küllővirtuális hálózat rendelkezik VPN-átjáróval?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>Mind a hub virtuális hálózat, mind a küllővirtuális hálózat rendelkezik VPN-átjáróval

A távoli átjáró használata nem támogatott.

Ha a küllővirtuális hálózat már rendelkezik VPN-átjáróval, a **Távoli átjáró használata** beállítás nem támogatott a küllővirtuális hálózaton. Ennek oka a virtuális hálózati társviszony-létesítéskorlátozása.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>Mind a központi virtuális hálózat, mind a küllővirtuális hálózat nem rendelkezik VPN-átjáróval

Helyek közötti vagy Azure ExpressRoute-kapcsolatok esetén ellenőrizze a helyszíni távoli virtuális hálózattal kapcsolatos kapcsolódási problémák elsődleges okait:

* Az átjáróval rendelkező virtuális hálózaton ellenőrizze, hogy be van-e jelölve a **Továbbított forgalom engedélyezése** jelölőnégyzet.
* Az átjárónélküli virtuális hálózaton ellenőrizze, hogy a **Távoli átjáró használata** jelölőnégyzet be van-e jelölve.
* A hálózati rendszergazdának ellenőrizze a helyszíni eszközöket, és ellenőrizze, hogy mindegyikhez hozzáadták-e a távoli virtuális hálózati címterületet.

Pont-hely kapcsolatok esetén:

* Az átjáróval rendelkező virtuális hálózaton ellenőrizze, hogy be van-e jelölve a **Továbbított forgalom engedélyezése** jelölőnégyzet.
* Az átjárónélküli virtuális hálózaton ellenőrizze, hogy a **Távoli átjáró használata** jelölőnégyzet be van-e jelölve.
* Töltse le és telepítse újra a pont-hely ügyfélcsomagot. Az újonnan társviszonyba helyezett virtuális hálózati útvonalak nem adnak hozzá automatikusan útvonalakat a pont-hely ügyfelekhez.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Központi küllős hálózati kapcsolati probléma elhárítása az ugyanabban a régióban lévő küllővirtuális hálózatok között

A központi hálózatnak nva-t kell tartalmaznia. Konfigurálja az udrs-t olyan küllőkben, amelyeken a következő ugrás nva-készlet e, és **engedélyezze** a továbbított forgalom engedélyezése a központi virtuális hálózaton.

További információ: [Szolgáltatásláncolás](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining), és beszélje meg ezeket a követelményeket az Ön által kiválasztott [NVA szállítóval.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>A különböző régiókban lévő küllős virtuális hálózatok közötti küllős hálózati kapcsolattal kapcsolatos probléma elhárítása

A globális virtuális hálózati társviszony-létesítésen keresztüli átvitel mostantól támogatott. A kapcsolat nem működik a globális virtuális hálózati társviszony-létesítésen keresztül a következő erőforrások esetében:

* Virtuális gépek az alapvető ILB-termékváltozat mögött
* Redis-gyorsítótár (egyszerű ILB-termékváltozatot használ)
* Alkalmazásátjáró (egyszerű ILB-termékváltozatot használ)
* Méretezési csoportok (egyszerű ILB-termékhalmazt használ)
* Szolgáltatásháló-fürtök (egyszerű ILB-termékváltozatot használ)
* SQL Server Mindig be van kapcsolva (egyszerű ILB-termékváltozatot használ)
* App Service-környezet (alapszintű ILB-termékváltozatot használ)
* API-kezelés (egyszerű ILB-termékváltozatot használ)
* Azure AD DS (egyszerű ILB-termékváltozatot használ)

További információt a globális társviszony-létesítés és a [különböző VPN-topológiák](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/) [követelményei és korlátai](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) tartalmaznak.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>A webalkalmazás és a küllős virtuális hálózat közötti küllős hálózati kapcsolattal kapcsolatos probléma elhárítása

A probléma elhárítása:

1. Jelentkezzen be az Azure portálra. 
1. A webalkalmazásban válassza a **hálózati**lehetőséget, majd a **Virtuálishálózat-integráció**lehetőséget.
1. Ellenőrizze, hogy látható-e a távoli virtuális hálózat. Adja meg manuálisan a távoli virtuális hálózati címteret (**Hálózati szinkronizálás** és **Útvonalak hozzáadása**).

További információkért tekintse át a következő cikkeket:

* [Az alkalmazás integrálása az Azure virtuális hálózatával](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [Tudnivalók a pont–hely VPN-útválasztásról](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Virtuális hálózati társviszony-létesítési konfigurációs hibaüzenet elhárítása 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>A `<TENANT ID>` jelenlegi bérlő nem jogosult az összekapcsolt előfizetés elérésére

A probléma megoldásához olvassa el a [Társviszony-létesítés létrehozása - Azure CLI című témakört.](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)

### <a name="not-connected"></a>Nincs kapcsolat

A probléma megoldásához törölje a társviszony-létesítést mindkét virtuális hálózatról, majd hozza létre újra őket.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Nem sikerült társviszonyt létesíteni egy Databricks virtuális hálózattal

A probléma megoldásához konfigurálja a virtuális hálózati társviszony-létesítést az **Azure Databricks**csoportban, majd adja meg a célvirtuális hálózatot **az Erőforrás-azonosító**használatával. További információ: [Peer a Databricks virtuális hálózat egy távoli virtuális hálózathoz.](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2)

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>A távoli virtuális hálózatból hiányzik az átjáró

Ez a probléma akkor fordul elő, ha különböző bérlőkvirtuális hálózatait társviszonyba állítja, majd később konfigurálni `Use Remote Gateways`szeretné. Az Azure Portal egyik korlátozása, hogy nem tudja érvényesíteni a virtuális hálózati átjáró jelenlétét egy másik bérlő virtuális hálózatában.

A probléma kétféleképpen oldható meg:

 * Törölje a társviszony-létesítést, és aktiválja a `Use Remote Gateways` lehetőséget új társviszony-létesítés létrehozásakor.
 * Az Azure Portal helyett a PowerShell vagy `Use Remote Gateways`a CLI használatával engedélyezheti a használatát.

## <a name="next-steps"></a>További lépések

* [Azure-beli virtuális gépek közötti kapcsolatok hibaelhárítása](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)
