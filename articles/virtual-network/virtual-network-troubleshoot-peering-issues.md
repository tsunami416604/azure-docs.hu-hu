---
title: Virtuális hálózatok közötti társviszony-létesítési problémák hibaelhárítása
description: A legtöbb virtuális hálózati társítási probléma megoldásához szükséges lépések.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: fc01a20a077a1c624ed490600db919fe3197556c
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901762"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Virtuális hálózatok közötti társviszony-létesítési problémák hibaelhárítása

Ez a hibaelhárítási útmutató a legtöbb [virtuális hálózati](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) társítási probléma megoldásához nyújt segítséget.

![LEMEZKÉP](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="scenario-1-configure-virtual-network-peering-between-two-virtual-networks"></a>forgatókönyv 1: Virtuális hálózati társítás konfigurálása két virtuális hálózat között

Azonos előfizetésben vagy eltérő előfizetésben található virtuális hálózatok?

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-subscription"></a>1\. kapcsolattípus: A virtuális hálózatok ugyanahhoz az előfizetéshez tartoznak.

Az azonos előfizetésben található virtuális hálózatok virtuális hálózati társításának konfigurálásához használja a következő cikkekben megadott módszereket a megfelelő módon:

* Ha a virtuális hálózatok **ugyanabban a régióban**találhatók, kövesse az [azonos előfizetésben található virtuális hálózatokhoz való társítás létrehozásához](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering)szükséges lépéseket.
* Ha a virtuális hálózatok a **különböző régiókban**találhatók, kövesse a [globális virtuális hálózati](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)társítás beállításának lépéseit.  

> [!Note]
> A kapcsolat nem fog működni a globális VNet-társítással a következő erőforrásokhoz: 
>
> * Alapszintű ILB SKU mögötti virtuális gépek
> * Redis Cache (alapszintű ILB SKU-t használ)
> * Application Gateway (alapszintű ILB SKU-t használ)
> * Méretezési csoportok (alapszintű ILB SKU-t használ)
> * Service Fabric-fürtök (alapszintű ILB SKU-t használ)
> * SQL always-on (alapszintű ILB SKU-t használ)
> * App Service környezetek (bemutató) (alapszintű ILB SKU-t használ)
> * API Management (alapszintű ILB SKU-t használ)
> * Azure Active Directory tartományi szolgáltatás (Hozzáadás) (alapszintű ILB SKU-t használ)

További információért lásd a globális társítás [követelményeit és korlátozásait](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) .

### <a name="connection-type-2-the-virtual-networks-are-in-different-subscriptions-or-ad-tenants"></a>2\. kapcsolattípus: A virtuális hálózatok különböző előfizetésekben vagy AD-bérlőben találhatók

Ha a virtuális hálózatokat különböző előfizetésekben vagy Active Directory-bérlőben lévő virtuális hálózatokhoz szeretné konfigurálni, kövesse a társítás [létrehozása az Azure CLI-hez különböző előfizetésekben](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)című témakör lépéseit.

> [!Note]
> A hálózati társítás konfigurálásához a hálózat **közreműködői** engedélyekkel kell rendelkeznie mindkét előfizetésben. További információ: [egyenrangú engedélyek](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering%23permissions).

## <a name="scenario-2-configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>2\. forgatókönyv: A virtuális hálózati topológia konfigurálása a helyi erőforrásokat használó sugaras topológiával

![LEMEZKÉP](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="connection-type-1-for-site-to-site-connection-or-expressroute-connection"></a>1\. kapcsolattípus: Helyek közötti kapcsolathoz vagy ExpressRoute-kapcsolathoz

Kövesse a következő témakörben ismertetett lépéseket: [Konfigurálja a VPN Gateway-átvitelt a virtuális hálózati](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)társításokhoz.

### <a name="connection-type-2-for-point-to-site-connections"></a>2\. kapcsolattípus: Pont – hely kapcsolatok esetén

1. Kövesse a következő témakörben ismertetett lépéseket: [Konfigurálja a VPN Gateway-átvitelt a virtuális hálózati](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)társításokhoz.
2. A virtuális hálózati társítás létrehozása vagy módosítása után a pont-hely típusú ügyfeleknek le kell tölteni és újra kell telepíteni, hogy a pont – hely ügyfelek megkapják a frissített útvonalakat a küllős virtuális hálózathoz.

## <a name="scenario-3-configure-virtual-network-peering-with-hub-spoke-topology-for-azure-virtual-network"></a>3\. forgatókönyv: Virtuális hálózati társítás konfigurálása az Azure-Virtual Network küllős topológiával

![LEMEZKÉP](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-region"></a>1\. kapcsolattípus: A virtuális hálózatok ugyanabban a régióban találhatók

Konfigurálnia kell egy hálózati virtuális berendezést (NVA) a hub virtuális hálózatban, és felhasználói útvonalakat kell használnia a küllős virtuális hálózatokon alkalmazott következő ugrású "hálózati virtuális berendezéssel". További információ: [Service láncolása](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining).

> [!Note]
> Ha segítségre van szüksége egy NVA beállításához, [forduljon a NVA gyártójához](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

A NVA-eszközök beállításával és útválasztásával kapcsolatos segítségért lásd: a [hálózati virtuális berendezések problémái az Azure-ban](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

### <a name="connection-type-2-the-virtual-networks-are-in-different-regions"></a>2\. kapcsolattípus: A virtuális hálózatok különböző régiókban találhatók

A globális VNet-társítás továbbítása mostantól támogatott. A kapcsolat nem működik globális VNet-társítás esetén a következő erőforrásokhoz:

* Alapszintű ILB SKU mögötti virtuális gépek
* Redis Cache (alapszintű ILB SKU-t használ)
* Application Gateway (alapszintű ILB SKU-t használ)
* Méretezési csoportok (alapszintű ILB SKU-t használ)
* Service Fabric-fürtök (alapszintű ILB SKU-t használ)
* SQL always-on (alapszintű ILB SKU-t használ)
* App Service környezetek (bemutató) (alapszintű ILB SKU-t használ)
* API Management (alapszintű ILB SKU-t használ)
* Azure Active Directory tartományi szolgáltatás (Hozzáadás) (alapszintű ILB SKU-t használ)

Ha többet szeretne megtudni a globálisan megjelenő követelményekről és a korlátozásokról, tekintse meg a [virtuális hálózat](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)társítása című témakört.

## <a name="scenario-4-i-have-a-connectivity-issue-between-two-peered-virtual-networks"></a>4\. forgatókönyv: Kapcsolati probléma van két, egymástól független virtuális hálózat között

Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) egy olyan fiókkal, amely rendelkezik a szükséges [szerepkörökkel és engedélyekkel](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions). Válassza ki a virtuális hálózatot, válassza a **társítás lehetőséget,** majd jelölje be az **állapot** mezőt. Mi az az állapot?

### <a name="connection-type-1-the-peering-status-shows-connected"></a>1\. kapcsolattípus: Az egyenrangú állapot a "Connected"

A probléma megoldásához kövesse az alábbi lépéseket:

1. A hálózati forgalom forgalmának ellenõrzése:

   [Kapcsolódási hibák](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) és [IP-forgalom ellenőrzése](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) a forrás virtuális gépről a cél virtuális gépre annak megállapításához, hogy van-e olyan NSG vagy UDR, amely interferenciát okoz a forgalmi folyamatokban.

   Ha tűzfalat vagy NVA készüléket használ, kövesse az alábbi lépéseket: 
   1. Dokumentálja a UDR paramétereket, hogy a lépés befejezése után vissza tudja állítani őket.
   2. Távolítsa el a UDR a forrás virtuálisgép-alhálózatról vagy hálózati adapterről, amely a NVA mutat a következő ugrásként. Győződjön meg arról, hogy a forrás virtuális gép kapcsolata közvetlenül a NVA megkerülő célhelyre mutat. Ha ez a lépés működik, tekintse meg a [NVA-hibakeresőt](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

2. Hálózati nyomkövetés készítése: 
   1. Indítsa el a hálózati nyomkövetést a cél virtuális gépen. A Windows rendszerben a **netsh**használható. Linux esetén használja a **TCPDump**-t.
   2. Futtassa a **TcpPing** vagy a **PsPing** a FORRÁSRÓL a cél IP-címhez.

   * Ez egy példa egy **TcpPing** -parancsra:`tcping64.exe -t <destination VM address> 3389`

   3. A **TcpPing** befejezése után állítsa le a hálózati nyomkövetést a célhelyen.
   4. Ha a csomagok érkeznek a forrásból, nincs hálózati probléma. Vizsgálja meg a virtuálisgép-tűzfalat és a portot figyelő alkalmazást a konfigurációs probléma megkereséséhez.

   > [!Note]
   > Nem lehet csatlakozni a következő típusú erőforrásokhoz a globális virtuális hálózati kapcsolaton keresztül (a különböző régiókban található virtuális hálózatok esetében):
   >
   > * Alapszintű ILB SKU mögötti virtuális gépek
   > * Redis Cache (alapszintű ILB SKU-t használ)
   > * Application Gateway (alapszintű ILB SKU-t használ)
   > * Méretezési csoportok (alapszintű ILB SKU-t használ)
   > * Service Fabric-fürtök (alapszintű ILB SKU-t használ)
   > * SQL always-on (alapszintű ILB SKU-t használ)
   > * App Service környezetek (bemutató) (alapszintű ILB SKU-t használ)
   > * API Management (alapszintű ILB SKU-t használ)
   > * Azure Active Directory tartományi szolgáltatás (Hozzáadás) (alapszintű ILB SKU-t használ)

További információért lásd a globális társítás [követelményeit és korlátozásait](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) .

### <a name="connection-type-2-the-peering-status-shows-disconnected"></a>2\. kapcsolattípus: A társítás állapota a "leválasztott" állapotot jeleníti meg

Mindkét virtuális hálózatok törölni kell a társításokat, és újból létre kell őket hozni.

## <a name="scenario-5-i-have-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-on-premises-resource"></a>5\. forgatókönyv: Kapcsolati probléma van egy sugaras virtuális hálózat és egy helyszíni erőforrás között

Külső gyártótól származó NVA-vagy VPN-átjárót használ?

### <a name="connection-type-1-my-network-uses-a-third-party-nva-or-vpn-gateway"></a>1\. kapcsolattípus: A hálózat egy harmadik féltől származó NVA-vagy VPN-átjárót használ

A harmadik féltől származó NVA vagy VPN-átjárót érintő csatlakozási problémák elhárításához tekintse meg a következő cikkeket:

* [NVA-hibakereső](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Szolgáltatások láncolása](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="connection-type-2-my-network-does-not-a-third-party-nva-or-vpn-gateway"></a>2\. kapcsolattípus: A hálózatom nem harmadik féltől származó NVA vagy VPN-átjáró

A hub és a küllős virtuális hálózatok egyaránt rendelkeznek VPN-átjáróval?

#### <a name="both-the-hub-and-spoke-virtual-networks-have-a-vpn-gateway"></a>A hub és a küllő virtuális hálózatok is rendelkeznek VPN-átjáróval

Távoli átjáró használata nem támogatott.

A VNet-társítási korlátozás miatt a **távoli átjáró használata** nem támogatott a küllős VNet, ha a küllő VNet már rendelkezik egy VPN-átjáróval.

#### <a name="both-the-hub-and-spoke-virtual-networks-dont-have-a-vpn-gateway"></a>A hub és a küllős virtuális hálózatok nem rendelkeznek VPN-átjáróval

Helyek közötti vagy ExpressRoute kapcsolatok esetén a kapcsolódási problémák elsődleges okait a távoli virtuális hálózathoz a helyszíni környezetből ellenőrizheti.

* Ellenőrizze, hogy a **továbbított forgalom engedélyezése** jelölőnégyzet be van-e jelölve azon a virtuális hálózaton, amelyen átjáró található.
* Ellenőrizze, hogy a **távoli átjáró használata** jelölőnégyzet be van-e jelölve azon a virtuális hálózaton, amely nem rendelkezik átjáróval.
* Győződjön meg arról, hogy a hálózati rendszergazda ellenőrzi a helyszíni eszközöket annak ellenőrzéséhez, hogy az összes virtuális hálózati címtartomány hozzá lett-e adva.

Pont – hely kapcsolatok esetén:

* Ellenőrizze, hogy a **továbbított forgalom engedélyezése** jelölőnégyzet be van-e jelölve azon a virtuális hálózaton, amelyen átjáró található.
* Ellenőrizze, hogy a **távoli átjáró használata** jelölőnégyzet be van-e jelölve azon a virtuális hálózaton, amely nem rendelkezik átjáróval.
* Töltse le és telepítse újra a pont – hely ügyfélszoftvert. Az újonnan létrehozott virtuális hálózati útvonalak nem vesznek fel automatikusan útvonalakat a pont – hely ügyfelekhez.

## <a name="scenario-6-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>6\. forgatókönyv: Sugaras hálózati kapcsolattal rendelkezem a küllős virtuális hálózatok között ugyanabban a régióban

Egy NVA kell rendelkeznie a hub-hálózatban, konfigurálnia kell a UDR olyan küllőket, amelyek a következő ugrásként beállított NVA rendelkeznek, és engedélyezik a **továbbított forgalom** engedélyezését a hub virtuális hálózatban.

További információ: a [szolgáltatás láncolása](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining), és a követelmények megvitatása a választott [NVA-gyártóval](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) .

## <a name="scenario-7-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>7\. forgatókönyv: Sugaras hálózati kapcsolati probléma van a küllős virtuális hálózatok között különböző régiókban

A globális VNet-társítás továbbítása mostantól támogatott. A kapcsolat nem fog működni a globális VNet-társítással a következő erőforrásokhoz:

* Alapszintű ILB SKU mögötti virtuális gépek
* Redis Cache (alapszintű ILB SKU-t használ)
* Application Gateway (alapszintű ILB SKU-t használ)
* Méretezési csoportok (alapszintű ILB SKU-t használ)
* Service Fabric-fürtök (alapszintű ILB SKU-t használ)
* SQL always-on (alapszintű ILB SKU-t használ)
* App Service környezetek (bemutató) (alapszintű ILB SKU-t használ)
* API Management (alapszintű ILB SKU-t használ)
* Azure Active Directory tartományi szolgáltatás (Hozzáadás) (alapszintű ILB SKU-t használ)

További információért lásd a globális és a [különböző VPN-topológiák](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/) [követelményeit és korlátozásait](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) .

## <a name="scenario-8-i-have-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>8\. forgatókönyv: Sugaras hálózati kapcsolattal kapcsolatos probléma van a webalkalmazás és a küllős virtuális hálózat között

A probléma megoldásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a Azure Portalba. Nyissa meg a webalkalmazást, válassza a **hálózatkezelés**, majd a **VNet-integráció**elemet.
2. Ellenőrizze, hogy látható-e a távoli virtuális hálózat. Adja meg manuálisan a távoli virtuális hálózati címtartomány (**szinkronizált hálózat** és **útvonalak hozzáadása**).

További információkért tekintse át a következő cikkeket:

* [Az alkalmazás integrálása Azure-Virtual Network](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [Tudnivalók a pont – hely VPN-útválasztásról](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="scenario-9-i-receive-an-error-when-configuring-virtual-network-peering"></a>9\. forgatókönyv: Hibaüzenetet kapok a virtuális hálózati társak konfigurálásakor

### <a name="error-1-current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>1\. hiba: A jelenlegi `<TENANT ID>` bérlő nem jogosult a csatolt előfizetés elérésére

A probléma megoldásához kövesse a társítás [létrehozása – Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)című témakör lépéseit.

### <a name="error-2-not-connected"></a>2\. hiba: Nincs csatlakozva

Mindkét virtuális hálózatok törölni kell a társait, és újból létre kell hozni őket.

### <a name="error-3-failed-to-peer-a-databricks-virtual-network"></a>3\. hiba: Nem sikerült a Databricks virtuális hálózat társítása

A probléma megoldásához konfigurálja a virtuális hálózat társítását a **Azure Databricks** panelről, majd adja meg a cél virtuális hálózatot az erőforrás- **azonosító**használatával. További információ: [Databricks virtuális hálózat létrehozása távoli virtuális hálózathoz](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2).

## <a name="next-steps"></a>További lépések

* [Azure-beli virtuális gépek közötti kapcsolatok hibaelhárítása](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)