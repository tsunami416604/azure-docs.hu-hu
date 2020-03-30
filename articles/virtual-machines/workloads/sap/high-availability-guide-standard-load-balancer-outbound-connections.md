---
title: Nyilvános végpont-kapcsolat az Azure-beli virtuális gépek&standard ILB SAP HA-forgatókönyvekben
description: Nyilvános végpont-kapcsolat az Azure Standard Load Balancer használatával használó virtuális gépek hez az SAP magas rendelkezésre állású forgatókönyvekben
services: virtual-machines-windows,virtual-network,storage,
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: radeltch
ms.openlocfilehash: 4fd01764c183098a8bd78d502eea7ab173fa22cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293919"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>Nyilvános végpont-kapcsolat az Azure Standard Load Balancer használatával használó virtuális gépek hez az SAP magas rendelkezésre állású forgatókönyvekben

A cikk hatóköre a konfigurációk leírása, amely lehetővé teszi a kimenő kapcsolatot a nyilvános végpont(ok) számára. A konfigurációk elsősorban a magas rendelkezésre állás ú Pacemaker suse / RHEL összefüggésében.  

Ha a szívritmus-rátát azure-függő ügynök a magas rendelkezésre állású megoldás, majd a virtuális gépek nek rendelkeznie kell kimenő kapcsolat az Azure felügyeleti API-t.  
A cikk számos lehetőséget mutat be, amelyek lehetővé teszik a forgatókönyvnek leginkább megfelelő beállítás kiválasztását.  

## <a name="overview"></a>Áttekintés

Az SAP-megoldások fürtözéssel történő magas rendelkezésre állásának megvalósításakor az egyik szükséges összetevő az [Azure Load Balancer.](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) Az Azure két terheléselosztó termékkészletet kínál: szabványos és alapszintű.

Standard Azure-terheléselosztó kínál néhány előnye az alapszintű terheléselosztó. Például az Azure rendelkezésre állási zónák között működik, jobb figyelési és naplózási képességekkel rendelkezik a könnyebb hibaelhárítás, a kisebb késés érdekében. A "HA portok" funkció az összes portra kiterjed, azaz már nem szükséges az összes egyes port listázása.  

Van néhány fontos különbség az alap szintű és az Azure terheléselosztó szabványos termékváltozata között. Ezek közül az egyik a nyilvános végpontra irányuló kimenő forgalom kezelése. A teljes alapszintű és a standard termékváltozat terheléselosztó összehasonlításáról lásd: [Terheléselosztó Termékváltozat összehasonlítása.](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)  
 
Ha a nyilvános IP-címekkel nem rendelkező virtuális gépek a belső (nyilvános IP-cím nélküli) standard Azure-terheléselosztó háttérkészletébe kerülnek, nincs kimenő kapcsolat a nyilvános végpontokhoz, kivéve, ha további konfiguráció történik.  

Ha egy virtuális gép nyilvános IP-címet kap, vagy a virtuális gép egy nyilvános IP-címmel rendelkező terheléselosztó háttérkészletében van, akkor kimenő kapcsolattal rendelkezik a nyilvános végpontokhoz.  

Az SAP-rendszerek gyakran tartalmaznak bizalmas üzleti adatokat. Ritkán elfogadható, hogy az SAP-rendszereket tároló virtuális gépek nyilvános IP-címekkel rendelkeznek. Ugyanakkor vannak olyan forgatókönyvek, amelyek a virtuális gép ről a nyilvános végpontok kimenő kapcsolatára van szükség.  

Példák olyan forgatókönyvek, amelyek hozzáférést igényelnek az Azure nyilvános végpontjához:  
- Az Azure Fence Agent használata kerítésmechanizmusként pacemaker-fürtökben
- Azure Backup
- Azure Site Recovery  
- Nyilvános adattár használata az operációs rendszer javításához
- Az SAP-alkalmazás adatfolyama kimenő kapcsolatot igényelhet a nyilvános végponthoz

Ha az SAP-telepítés nem igényel kimenő kapcsolatot a nyilvános végpontok, nem kell megvalósítani a további konfigurációt. Elegendő belső szabványos Termékváltozat Azure-terheléselosztó létrehozása a magas rendelkezésre állású forgatókönyvhöz, feltéve, hogy nincs szükség bejövő kapcsolatra a nyilvános végpontokról.  

> [!Note]
> Ha a nyilvános IP-címekkel nem rendelkező virtuális gépek a belső (nyilvános IP-cím nélküli) standard Azure-terheléselosztó háttérkészletébe kerülnek, nem lesz kimenő internetkapcsolat, kivéve, ha további konfigurációt hajt végre a nyilvános végpontok útválasztásának engedélyezéséhez.  
>Ha a virtuális gépek nyilvános IP-címekkel rendelkeznek, vagy már az Azure Load balancer nyilvános IP-címmel rendelkező háttérkészletében vannak, a virtuális gép már rendelkezik kimenő kapcsolattal a nyilvános végpontokhoz.


Először olvassa el a következő papírokat:

* Az Azure standard terheléselosztója
  * [Az Azure Standard Load Balancer áttekintése](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) – az Azure Standard Load Balancer átfogó áttekintése, fontos alapelvek, fogalmak és oktatóanyagok 
  * [Kimenő kapcsolatok az Azure-ban](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios) – forgatókönyvek a kimenő kapcsolat azure-beli elérésére
  * [Terheléselosztó kimenő szabályai](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)– ismerteti a terheléselosztó kimenő szabályainak fogalmát és a kimenő szabályok létrehozásának módját
* Azure Firewall
  * [Az Azure tűzfal áttekintése](https://docs.microsoft.com/azure/firewall/overview)– az Azure Tűzfal áttekintése
  * [Oktatóanyag: Az Azure Firewall telepítése és konfigurálása](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) – utasítások az Azure Tűzfal Azure Portalon keresztüli konfigurálásához
* [Virtuális hálózatok – Felhasználó által definiált szabályok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) – Azure útválasztási fogalmak és szabályok  
* [Biztonsági csoportok szolgáltatáscímkék](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) – a hálózati biztonsági csoportok és a tűzfal konfigurációjának egyszerűsítése szolgáltatáscímkékkel

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>További külső Azure standard terheléselosztó az internethez való kimenő kapcsolatokhoz

Az egyik lehetőség a nyilvános végpontokhoz való kimenő kapcsolat elérésére, anélkül, hogy a virtuális géphez a nyilvános végpontról bejövő kapcsolatot engedélyezne, az, hogy létrehoz egy második terheléselosztót nyilvános IP-címmel, hozzáadja a virtuális gépeket a második terheléselosztó háttérkészletéhez, és csak [a kimenő szabályokat](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)határozza meg.  
[A hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview) segítségével szabályozhatja a nyilvános végpontok, amelyek a virtuális gépről érkező kimenő hívások hoz elérhető.  
További információt a kimenő [kapcsolatok](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios)dokumentum 2.  
A konfiguráció így nézne ki:  

![A nyilvános végpontokhoz való kapcsolódás szabályozása a hálózati biztonsági csoportokkal](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>Fontos szempontok

- Egy további nyilvános terheléselosztót használhat több virtuális géphez ugyanabban az alhálózaton a nyilvános végponthoz való kimenő kapcsolat eléréséhez és a költségek optimalizálásához  
- [A hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview) segítségével szabályozhatja, hogy mely nyilvános végpontok érhetők el a virtuális gépekről. A hálózati biztonsági csoportot vagy az alhálózathoz, vagy az egyes virtuális gépekhez rendelheti hozzá. Ahol lehetséges, használja [a Szolgáltatás címkéket](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) a biztonsági szabályok összetettségének csökkentése érdekében.  
- Az Azure szabványos terheléselosztó nyilvános IP-címmel és kimenő szabályokkal közvetlen hozzáférést biztosít a nyilvános végponthoz. Ha vállalati biztonsági követelményekkel rendelkezik, hogy az összes kimenő forgalom át a központi vállalati megoldás naplózása és naplózása, előfordulhat, hogy nem tudja teljesíteni a követelményt ebben a forgatókönyvben.  

>[!TIP]
>Ahol lehetséges, a [Szolgáltatáscímkék](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) segítségével csökkentse a hálózati biztonsági csoport összetettségét. 

### <a name="deployment-steps"></a>A központi telepítés lépései

1. Terheléselosztó létrehozása  
   1. Az [Azure Portalon](https://portal.azure.com) kattintson az Összes erőforrás elemre, majd a **Terheléselosztó** keresése elemre.  
   1. Kattintson a **Létrehozás gombra.** 
   1. Terheléselosztó neve **MyPublicILB**  
   1. **A Nyilvános** típus kiválasztása, **szabványos** termékváltozatként  
   1. Válassza **a Nyilvános IP-cím létrehozása** lehetőséget, és adja meg **névként MyPublicILBFrondEndIP**  
   1. **A Redundáns zóna** kiválasztása rendelkezésre állási zónaként  
   1. Kattintson a Véleményezés és létrehozás gombra, majd a Létrehozás gombra.  
2. Create backend pool **MyBackendPoolOfPublicILB** és adja hozzá a virtuális gépeket.  
   1. A virtuális hálózat kijelölése  
   1. Válassza ki a virtuális gépeket és azok IP-címeit, és adja hozzá őket a háttérkészlethez  
3. [Kimenő szabályok létrehozása](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-cli#create-outbound-rule). Jelenleg nem lehet kimenő szabályokat létrehozni az Azure Portalról. Az [Azure CLI](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)segítségével kimenő szabályokat hozhat létre.  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. Hozzon létre hálózati biztonsági csoportszabályokat az adott nyilvános végpontokhoz való hozzáférés korlátozására. Ha létezik hálózati biztonsági csoport, módosíthatja azt. Az alábbi példa bemutatja, hogyan engedélyezheti a hozzáférést az Azure felügyeleti API-hoz: 
   1. Navigálás a hálózati biztonsági csoportra
   1. Kattintson a Kimenő biztonsági szabályok elemre
   1. Adjon hozzá egy szabályt az összes kimenő **internet-hozzáférés megtagadásához.** **Deny**
   1. Adjon hozzá egy szabályt az **AzureCloud**elérésének **engedélyezése,** prioritással alacsonyabb, mint a prioritás a szabály, hogy megtagadja az összes internet-hozzáférés.


   A kimenő biztonsági szabályok a következőkre néznek ki: 

   ![Kimenő kapcsolat a nyilvános IP-című második terheléselosztóval](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Az Azure Network biztonsági csoportjairól a Biztonsági csoportok című [témakörben ](https://docs.microsoft.com/azure/virtual-network/security-overview)talál további információt. 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>Azure tűzfal az internethez való kimenő kapcsolatokhoz

Egy másik lehetőség a nyilvános végpontokhoz való kimenő kapcsolat elérésére, anélkül, hogy a virtuális géphez a nyilvános végpontokról bejövő kapcsolatot engedélyezne, az Azure Firewall. Az Azure Firewall egy felügyelt szolgáltatás, beépített magas rendelkezésre állású, és több rendelkezésre állási zónára is kiterjed.  
Emellett telepítenie kell [a felhasználó által definiált útvonalat,](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)amely alhálózathoz van társítva, ahol a virtuális gépek és az Azure terheléselosztó üzembe helyezése, az Azure tűzfalra mutatva, az Azure tűzfalon keresztüli forgalom irányításához.  
Az Azure Tűzfal üzembe helyezéséről az [Azure-tűzfal telepítése és konfigurálása](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)című témakörben talál további információt.  

Az architektúra így nézne ki:

![Kimenő kapcsolat az Azure tűzfallal](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>Fontos szempontok

- Az Azure tűzfal a felhőn atív szolgáltatás, a beépített magas rendelkezésre állású, és támogatja a zónaszintű telepítést.
- További alhálózatot igényel, amelynek el kell nevezni az AzureFirewallSubnet nevű. 
- Ha nagy adathalmazok átvitele kimenő a virtuális hálózat, ahol az SAP virtuális gépek találhatók, egy virtuális gép egy másik virtuális hálózat, vagy a nyilvános végpont, előfordulhat, hogy nem költséghatékony megoldás. Az egyik ilyen példa a nagy biztonsági mentések másolása a virtuális hálózatok között. További részletek: Azure Firewall díjszabása.  
- Ha a vállalati tűzfal megoldás nem az Azure Tűzfal, és a biztonsági követelmények, hogy az összes kimenő forgalom át bár központosított vállalati megoldás, ez a megoldás nem lehet praktikus.  

>[!TIP]
>Ahol lehetséges, használja [a Szolgáltatás címkék](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) et az Azure Firewall-szabályok összetettségének csökkentése érdekében.  

### <a name="deployment-steps"></a>A központi telepítés lépései

1. A telepítési lépések feltételezik, hogy már rendelkezik virtuális hálózat és alhálózat definiálva a virtuális gépek.  
2. Hozzon létre alhálózati **AzureFirewallSubnet** ugyanabban a virtuális hálózatban, ahol a VMS és a standard terheléselosztó telepítve van.  
   1. Az Azure Portalon keresse meg a virtuális hálózatot: kattintson az összes erőforrás, a virtuális hálózat keresése, kattintson a virtuális hálózat, válassza az alhálózatok.  
   1. Kattintson az Alhálózat hozzáadása gombra. Adja meg **az AzureFirewallSubnet** nevet. Adja meg a megfelelő címtartományt. Mentse el.  
3. Hozzon létre Azure tűzfalat.  
   1. Az Azure Portalon válassza az Összes erőforrás lehetőséget, kattintson a Hozzáadás, tűzfal, Létrehozás parancsra. Válassza ki az Erőforráscsoportot (válassza ki ugyanazt az erőforráscsoportot, ahol a virtuális hálózat található).  
   1. Adja meg az Azure Firewall erőforrás nevét. Például a **MyAzureFirewall**.  
   1. Válassza ki a régiót, és jelöljön ki legalább két rendelkezésre állási zónát, a rendelkezésre állási zónákhoz igazítva, ahol a virtuális gépek telepítve vannak.  
   1. Válassza ki a virtuális hálózat, ahol az SAP virtuális gépek és az Azure standard terheléselosztó üzembe helyezése.  
   1. Nyilvános IP-cím: Kattintson a Létrehozás gombra, és adjon meg egy nevet. Például **MyFirewallPublicIP**.  
4. Hozzon létre Azure firewall rule, hogy a kimenő kapcsolat a megadott nyilvános végpontok. A példa bemutatja, hogyan engedélyezheti a hozzáférést az Azure Management API nyilvános végpontjához.  
   1. Válassza a Szabályok, a Hálózati szabálygyűjtemény lehetőséget, majd kattintson a Hálózati szabálygyűjtemény hozzáadása parancsra.  
   1. Név: **MyOutboundRule**, írja be a prioritást, válassza a Művelet **engedélyezése lehetőséget.**  
   1. Szolgáltatás: Name **ToAzureAPI**.  Protokoll: Válassza a **Bármelyik lehetőséget.** Forráscíme: adja meg az alhálózat tartományát, ahol a virtuális gépek és a standard terheléselosztó telepítve van, például: **11.97.0.0/24**. Célportok: <b>*</b>enter .  
   1. Mentés
   1. Mivel továbbra is az Azure tűzfalon van elhelyezve, válassza ki az áttekintést. Jegyezze fel az Azure tűzfal privát IP-címét.  
5. Útvonal létrehozása az Azure tűzfalhoz  
   1. Az Azure Portalon válassza a Minden erőforrás lehetőséget, majd kattintson a Hozzáadás, Útvonaltábla, Létrehozás parancsra.  
   1. Írja be a MyRouteTable nevet, válassza az Előfizetés, erőforráscsoport és hely lehetőséget (amely megfelel a virtuális hálózat és a tűzfal helyének).  
   1. Mentés  

   A tűzfalszabály így ![nézne ki: Kimenő kapcsolat az Azure Firewall tűzfallal](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. Felhasználó által definiált útvonal létrehozása a virtuális gépek alhálózatából a **MyAzureFirewall**privát IP-címéhez.
   1. Az Útvonaltáblában az Útvonalak táblázatban való elhelyezéskor kattintson az Útvonalak gombra. Válassza a Hozzáadás lehetőséget. 
   1. Útvonal neve: ToMyAzureFirewall, Cím előtag: **0.0.0/0**. Következő ugrás típusa: Válassza a Virtuális berendezés lehetőséget. Következő ugrási cím: adja meg a konfigurált tűzfal privát IP-címét: **11.97.1.4**.  
   1. Mentés

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Proxy használata az Azure Management API-ba irányuló pacemaker-hívásokhoz

Használhatja a proxy, hogy pacemaker hívások az Azure-felügyeleti API nyilvános végpontra.  

### <a name="important-considerations"></a>Fontos szempontok

  - Ha már van vállalati proxy a helyén, akkor a kimenő hívásokat nyilvános végpontokra irányíthatja rajta keresztül. A nyilvános végpontokra irányuló kimenő hívások a vállalati ellenőrzési ponton keresztül fognak menni.  
  - Győződjön meg arról, hogy a proxykonfiguráció lehetővé teszi a kimenő kapcsolatot az Azure felügyeleti API-val:`https://management.azure.com`  
  - Győződjön meg arról, hogy van egy útvonal a virtuális gépek és a proxy  
  - A proxy csak HTTP/HTTPS hívásokat kezel. Ha további hívásokra van szükség a nyilvános végpontra különböző protokollokon (például az RFC-n) keresztül, alternatív megoldásra lesz szükség  
  - A proxymegoldásnak magas rendelkezésre állásúnak kell lennie, hogy elkerülje a Pacemaker-fürt instabilitását  
  - Attól függően, hogy a proxy helyét, előfordulhat, hogy további késést az Azure-függő ügynök az Azure Management API-t hívásokban. Ha a vállalati proxy még mindig a helyszínen van, miközben a Pacemaker-fürt az Azure-ban van, mérje meg a késést, és fontolja meg, hogy ez a megoldás megfelelő-e az Ön számára  
  - Ha még nincs magas rendelkezésre állású vállalati proxy a helyén, nem javasoljuk ezt a lehetőséget, mivel az ügyfél többletköltséget és összetettséget okozna. Mindazonáltal, ha úgy dönt, hogy további proxymegoldás üzembe helyezése, abból a célból, hogy a kimenő kapcsolat pacemaker az Azure Management nyilvános API-t, győződjön meg arról, hogy a proxy magas rendelkezésre állású, és a késés a virtuális gépek a proxy alacsony.  

### <a name="pacemaker-configuration-with-proxy"></a>Pacemaker konfiguráció proxy 

Sok különböző Proxy lehetőségek állnak rendelkezésre az iparágban. A proxy telepítésének lépésenkénti utasításai kívül esnek a dokumentum hatókörén. Az alábbi példában feltételezzük, hogy a proxy válaszol **a MyProxyService szolgáltatásra,** és hallgatja a **MyProxyPort**portot.  
Ha engedélyezni szeretné a pacemakernek az Azure felügyeleti API-val való kommunikációt, hajtsa végre az alábbi lépéseket az összes fürtcsomóponton:  

1. A pacemaker konfigurációs fájljának /etc/sysconfig/pacemaker szerkesztése és a következő sorok hozzáadása (az összes fürtcsomópont) hozzáadása:

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. Indítsa újra a pacemaker szolgáltatást **az összes** fürtcsomóponton.  
  - SUSE
 
     ```console
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  

     ```console
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="next-steps"></a>További lépések

* [A szívritmus-szabályozók konfigurálása az Azure-ban a SUSE-on](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)
* [Ismerje meg, hogyan konfigurálható a Pacemaker a Red Hat on Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)
