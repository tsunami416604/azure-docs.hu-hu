---
title: Nyilvános végponti kapcsolat a Virtual Machines az Azure standard Load Balancer használata az SAP magas rendelkezésre állási helyzetekben
description: Nyilvános végponti kapcsolat a Virtual Machines az Azure standard Load Balancer használata az SAP magas rendelkezésre állási helyzetekben
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
ms.openlocfilehash: 52179f15829981d59ff060784a49eccef89bb186
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083709"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>Nyilvános végponti kapcsolat a Virtual Machines az Azure standard Load Balancer használata az SAP magas rendelkezésre állási helyzetekben

A cikk hatóköre a konfigurációk leírására szolgál, amelyek lehetővé teszik a kimenő kapcsolódást a nyilvános végpont (ok) hoz. A konfigurációk főleg a magas rendelkezésre állás kontextusában vannak a SUSE/RHEL esetében.  

Ha a magas rendelkezésre állású megoldásban az Azure kerítés ügynökkel együtt használja a pacemakert, akkor a virtuális gépeknek kimenő kapcsolattal kell rendelkezniük az Azure felügyeleti API-val.  
A cikk több lehetőséget is kínál, amelyekkel kiválaszthatja a forgatókönyvhöz legjobban illő lehetőséget.  

## <a name="overview"></a>Áttekintés

Ha a magas rendelkezésre állást az SAP-megoldások fürtözésen keresztüli megvalósítása révén hajtja végre, a szükséges összetevők egyike [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). Az Azure két terheléselosztó SKU-t kínál: standard és alapszintű.

A standard szintű Azure Load Balancer számos előnyt kínál az alapszintű Load Balancerhez képest. Például az Azure-beli rendelkezésre állási zónákon keresztül működik, így jobb monitorozási és naplózási képességeket biztosít a hibaelhárításhoz, csökkentve a késést. A "HA ports" szolgáltatás az összes portot lefedi, azaz már nem szükséges az összes különálló port listázása.  

Fontos különbségek vannak az Azure Load Balancer alapszintű és standard SKU-jának között. Ezek egyike a nyilvános végpontra irányuló kimenő forgalom kezelésére szolgál. Az alapszintű SKU Load Balancer összehasonlításával kapcsolatban lásd: [Load BALANCER SKU-összehasonlítás](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).  
 
Ha a nyilvános IP-címek nélküli virtuális gépek a belső (nincs nyilvános IP-cím) standard Azure Load Balancer háttér-készletbe kerülnek, nincs kimenő kapcsolat a nyilvános végpontokkal, kivéve, ha további konfigurálás történik.  

Ha a virtuális gép nyilvános IP-címet kap, vagy a virtuális gép egy, a terheléselosztó egy nyilvános IP-címmel rendelkező készletében van, akkor kimenő kapcsolattal fog rendelkezni a nyilvános végpontokkal.  

Az SAP-rendszerek gyakran bizalmas üzleti adatokat tartalmaznak. Az SAP-rendszereket üzemeltető virtuális gépek számára ritkán fogadható el nyilvános IP-címek. Ugyanakkor vannak olyan forgatókönyvek is, amelyek a virtuális gépről kimenő kapcsolatot igényelnek a nyilvános végpontok számára.  

Az Azure nyilvános végponthoz való hozzáférést igénylő forgatókönyvek például a következők:  
- Az Azure kerítés-ügynök használata a pacemaker-fürtökben lévő kerítési mechanizmusként
- Azure Backup
- Azure Site Recovery  
- Nyilvános tárház használata az operációs rendszer javításához
- Előfordulhat, hogy az SAP-alkalmazás adatforgalmának kimenő kapcsolatra van szüksége a nyilvános végponthoz

Ha az SAP üzembe helyezése nem igényel kimenő kapcsolatot a nyilvános végpontokhoz, nem kell végrehajtania a további konfigurálást. Elegendő belső standard SKU-Azure Load Balancer létrehozni a magas rendelkezésre állási forgatókönyvhöz, feltételezve, hogy nincs szükség a nyilvános végpontokból érkező bejövő kapcsolatokra is.  

> [!Note]
> Ha a nyilvános IP-címek nélküli virtuális gépek a belső (nincs nyilvános IP-cím) standard Azure Load Balancer háttér-készletbe kerülnek, nem lesz kimenő internetkapcsolat, kivéve, ha további konfigurálást végeznek a nyilvános végpontok útválasztásának engedélyezéséhez.  
>Ha a virtuális gépek nyilvános IP-címekkel rendelkeznek, vagy már a nyilvános IP-címmel rendelkező Azure Load Balancer háttér-készletében vannak, akkor a virtuális gép már rendelkezik kimenő kapcsolattal a nyilvános végpontokhoz.


Először olvassa el az alábbi dokumentumokat:

* Azure-standard Load Balancer
  * Az [azure standard Load Balancer áttekintése](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) – átfogó áttekintés az Azure standard Load balancerről, a fontos alapelvekről, fogalmakról és oktatóanyagokról 
  * [Kimenő kapcsolatok az Azure-ban](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios) – forgatókönyvek az Azure-beli kimenő kapcsolat eléréséhez
  * Terheléselosztó [kimenő szabályai](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)– ismerteti a terheléselosztó kimenő szabályainak fogalmait és a kimenő szabályok létrehozását.
* Azure Firewall
  * [Azure Firewall áttekintése](https://docs.microsoft.com/azure/firewall/overview)– a Azure Firewall áttekintése
  * [Oktatóanyag: Azure Firewall üzembe helyezése és konfigurálása](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) – útmutatás a Azure Firewall konfigurálásához a Azure Portal használatával
* [Virtual Networks – felhasználó által definiált szabályok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) – Azure-útválasztási fogalmak és szabályok  
* [Biztonsági csoportok szolgáltatás címkéi](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) – a hálózati biztonsági csoportok és a tűzfal konfigurációjának leegyszerűsítése a szolgáltatás-címkékkel

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>További külső Azure-standard Load Balancer az internet felé irányuló kimenő kapcsolatokhoz

Az egyik lehetőség a nyilvános végpontok felé irányuló kimenő kapcsolat elérése anélkül, hogy a virtuális gép felé irányuló bejövő kapcsolat a nyilvános végpontról is elérhető legyen, egy második, nyilvános IP-címmel rendelkező terheléselosztó létrehozása, a virtuális gépek hozzáadása a második Load Balancer háttér-készletéhez, és csak [Kimenő szabályok](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)definiálása.  
[Hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/security-overview) vezérelheti a nyilvános végpontokat, amelyek elérhetők a virtuális gép kimenő hívásainak számára.  
További információ: 2. forgatókönyv a [Kimenő kapcsolatok](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios)dokumentumában.  
A konfiguráció a következőképpen fog kinézni:  

![Nyilvános végponti pontokhoz való kapcsolódás vezérlése hálózati biztonsági csoportokkal](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>Fontos szempontok

- Az azonos alhálózatban található több virtuális gép számára egy további nyilvános Load Balancer is használhatja, hogy a kimenő kapcsolatok elérhetők legyenek a nyilvános végponthoz, és optimalizálja a költségeket  
- [Hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview) használatával szabályozhatja, hogy mely nyilvános végpontok érhetők el a virtuális gépekről. A hálózati biztonsági csoportot az alhálózathoz vagy az egyes virtuális gépekhez rendelheti hozzá. Ha lehetséges, használja a [szolgáltatási címkéket](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) a biztonsági szabályok bonyolultságának csökkentése érdekében.  
- Az Azure standard Load Balancer nyilvános IP-címmel és kimenő szabályokkal közvetlen hozzáférést biztosít a nyilvános végponthoz. Ha rendelkezik a vállalati biztonsági követelményekkel, hogy az összes kimenő forgalom a központosított vállalati megoldáson keresztül legyen naplózva és naplózva, előfordulhat, hogy nem tudja teljesíteni a követelményt ebben a forgatókönyvben.  

>[!TIP]
>Ha lehetséges, a [szolgáltatási címkék](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) használatával csökkentse a hálózati biztonsági csoport összetettségét. 

### <a name="deployment-steps"></a>A központi telepítés lépései

1. Load Balancer létrehozása  
   1. A [Azure Portal](https://portal.azure.com) kattintson az összes erőforrás elemre, adja hozzá, majd keressen rá **Load Balancer**  
   1. Kattintson a **Create** (Létrehozás) gombra 
   1. Load Balancer név **MyPublicILB**  
   1. Válasszon **nyilvános** típusként, **standard** as SKU-ként  
   1. Válassza a **nyilvános IP-cím létrehozása** lehetőséget, és adja meg a nevet **MyPublicILBFrondEndIP**  
   1. Válassza ki a **felesleges zónát** a rendelkezésre állási zónában  
   1. Kattintson a felülvizsgálat és létrehozás, majd a Létrehozás elemre.  
2. Hozzon létre egy háttér-készlet **MyBackendPoolOfPublicILB** , és adja hozzá a virtuális gépeket.  
   1. Válassza ki a virtuális hálózatot  
   1. Válassza ki a virtuális gépeket és azok IP-címeit, és vegye fel őket a háttér-készletbe.  
3. [Hozzon létre kimenő szabályokat](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-cli#create-outbound-rule). Jelenleg nem lehet kimenő szabályokat létrehozni a Azure Portalból. Az [Azure CLI](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)-vel kimenő szabályokat hozhat létre.  

   ```
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. Hozzon létre hálózati biztonsági csoportokra vonatkozó szabályokat a megadott nyilvános végpontokhoz való hozzáférés korlátozására. Ha van meglévő hálózati biztonsági csoport, módosíthatja azt. Az alábbi példa bemutatja, hogyan engedélyezheti a hozzáférést az Azure felügyeleti API-hoz: 
   1. Navigáljon a hálózati biztonsági csoportra
   1. Kattintson a kimenő biztonsági szabályok elemre.
   1. Adjon hozzá egy szabályt az **Internet**felé irányuló összes kimenő hozzáférés **elutasításához** .
   1. Adjon hozzá egy szabályt, amely **lehetővé teszi** a **AzureCloud**való hozzáférést, a szabály prioritása szerint alacsonyabb prioritással, hogy megtagadja az összes internet-hozzáférést.


   A kimenő biztonsági szabályok a következőképpen néznek ki: 

   ![Kimenő kapcsolatok a második Load Balancer nyilvános IP-címmel](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Az Azure-beli hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [biztonsági csoportok ](https://docs.microsoft.com/azure/virtual-network/security-overview). 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>Az internet felé irányuló kimenő kapcsolatok Azure Firewall

Egy másik lehetőség a nyilvános végpontok felé irányuló kimenő kapcsolatok elérésére anélkül, hogy a nyilvános végpontokból érkező, a virtuális géphez való bejövő kapcsolódást nem engedélyezi Azure Firewall. A Azure Firewall egy felügyelt szolgáltatás, amely beépített, magas rendelkezésre állású, és több Availability Zones is terjedhet.  
Emellett telepítenie kell a [felhasználó által megadott útvonalat](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes), amely ahhoz az alhálózathoz van társítva, amelyben a virtuális gépek és az Azure Load Balancer telepítve van, és az Azure tűzfalra mutat, hogy átirányítsa a forgalmat a Azure Firewallon keresztül.  
Az Azure Firewall telepítésének részletes ismertetését lásd: [Azure Firewall telepítése és konfigurálása](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal).  

Az architektúra így néz ki:

![Kimenő kapcsolatok Azure Firewall](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>Fontos szempontok

- Az Azure Firewall a felhőalapú natív szolgáltatás, amely beépített magas rendelkezésre állású, és támogatja a zónákhoz való üzembe helyezést.
- További alhálózatot igényel, amelynek a neve AzureFirewallSubnet kell legyen. 
- Ha a nagyméretű adatkészletek továbbítása a virtuális hálózatban, ahol az SAP virtuális gépek találhatók, egy másik virtuális hálózatban vagy nyilvános végponton keresztül, nem költséghatékony megoldás lehet. Az egyik ilyen példa a nagyméretű biztonsági mentések másolása virtuális hálózatokon keresztül. További részletek: Azure Firewall díjszabása.  
- Ha a vállalati tűzfal megoldás nem Azure Firewall, és biztonsági követelményeknek kell megfelelnie, hogy az összes kimenő forgalom áthaladjon a központosított vállalati megoldással, ez a megoldás nem lehet praktikus.  

>[!TIP]
>Ha lehetséges, használja a [szolgáltatási címkéket](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) a Azure Firewall szabályok bonyolultságának csökkentése érdekében.  

### <a name="deployment-steps"></a>A központi telepítés lépései

1. A központi telepítés lépései azt feltételezik, hogy már rendelkezik a virtuális gépekhez definiált virtuális hálózattal és alhálózattal.  
2. Hozzon létre alhálózati **AzureFirewallSubnet** ugyanabban a Virtual Networkban, ahol a virtuális gépek és a standard Load Balancer üzembe vannak helyezve.  
   1. Azure Portalban navigáljon a Virtual Network: kattintson az összes erőforrás elemre, keresse meg a Virtual Network, kattintson a Virtual Networkra, majd válassza az alhálózatok elemet.  
   1. Kattintson az alhálózat hozzáadása lehetőségre. Adja meg a **AzureFirewallSubnet** nevet. Adja meg a megfelelő címtartományt. Mentse el.  
3. Azure Firewall létrehozása.  
   1. Azure Portal válassza az összes erőforrás lehetőséget, majd kattintson a Hozzáadás, a tűzfal, a Létrehozás elemre. Válassza az erőforráscsoport lehetőséget (válassza ki ugyanazt az erőforráscsoportot, ahol a Virtual Network).  
   1. Adja meg a Azure Firewall erőforrás nevét. Például: **MyAzureFirewall**.  
   1. Válassza ki a régiót, és válasszon ki legalább két rendelkezésre állási zónát, amelyek a virtuális gépek üzembe helyezéséhez szükséges rendelkezésre állási zónákhoz vannak igazítva.  
   1. Válassza ki a Virtual Networkt, ahol az SAP virtuális gépek és az Azure standard Load Balancer telepítve van.  
   1. Nyilvános IP-cím: kattintson a Létrehozás gombra, és adjon meg egy nevet. Például **MyFirewallPublicIP**.  
4. Hozzon létre Azure Firewall szabályt, hogy engedélyezze a kimenő kapcsolatokat a megadott nyilvános végpontok számára. A példa bemutatja, hogyan engedélyezheti az Azure Management API nyilvános végpontjának elérését.  
   1. Válassza a szabályok, hálózati szabályok gyűjtése, majd a hálózati szabálygyűjtemény hozzáadása elemet.  
   1. Név: **MyOutboundRule**, adja meg a prioritást, válassza a művelet **engedélyezése lehetőséget**.  
   1. Szolgáltatás: név **ToAzureAPI**.  Protokoll: válassza **a bármelyik**lehetőséget. Forrás címe: adja meg az alhálózat tartományát, ahol a virtuális gépek és a standard Load Balancer telepítve vannak a következő példányhoz: **11.97.0.0/24**. Célport: írja be a <b>*</b>.  
   1. Mentés
   1. Ha továbbra is a Azure Firewall elhelyezni, válassza az Áttekintés lehetőséget. Jegyezze fel a Azure Firewall magánhálózati IP-címét.  
5. Útvonal létrehozása a Azure Firewallhoz  
   1. Azure Portal válassza a minden erőforrás elemet, majd kattintson a Hozzáadás, útválasztási tábla, Létrehozás elemre.  
   1. Adja meg a MyRouteTable nevet, válassza az előfizetés, az erőforráscsoport és a hely lehetőséget (a virtuális hálózat és a tűzfal helyének megfelelő).  
   1. Mentés  

   A tűzfalszabály a következőhöz hasonlóan néz ki: ![kimenő kapcsolatok Azure Firewall](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. Hozzon létre felhasználó által megadott útvonalat a virtuális gépek alhálózatáról a **MyAzureFirewall**magánhálózati IP-címére.
   1. Ahogy az útválasztási táblázatban van elhelyezve, kattintson az útvonalak elemre. Válassza a Hozzáadás lehetőséget. 
   1. Útvonal neve: ToMyAzureFirewall, címzési előtag: **0.0.0.0/0**. Következő ugrás típusa: válassza a virtuális berendezés elemet. Következő ugrási cím: adja meg a konfigurált tűzfal magánhálózati IP-címét: **11.97.1.4**.  
   1. Mentés

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Proxy használata a pacemaker-hívások számára az Azure felügyeleti API-hoz

A proxy használatával engedélyezheti a pacemaker-hívásokat az Azure felügyeleti API nyilvános végpontjának.  

### <a name="important-considerations"></a>Fontos szempontok

  - Ha már van vállalati proxy, a kimenő hívásokat átirányíthatja a nyilvános végponti pontokra. A nyilvános végpontok felé irányuló kimenő hívások a vállalati ellenőrzési ponton haladnak át.  
  - Győződjön meg arról, hogy a proxy konfigurációja engedélyezi a kimenő kapcsolatot az Azure felügyeleti API-val: https://management.azure.com  
  - Ellenőrizze, hogy van-e útvonal a virtuális gépekről a proxyra  
  - A proxy csak a HTTP/HTTPS-hívásokat fogja kezelni. Ha a nyilvános végpontnak a különböző protokollok (például az RFC) felé irányuló kimenő hívásokat is el kell végeznie, alternatív megoldásra lesz szükség.  
  - A proxy megoldásnak nagyon széles körben elérhetőnek kell lennie a pacemaker-fürt instabilitásának elkerülése érdekében  
  - A proxy helyétől függően előfordulhat, hogy az Azure kerítési ügynök hívásait az Azure felügyeleti API-ba további késések is bevezetik. Ha a vállalati proxy továbbra is a helyszínen van, míg a pacemaker-fürt az Azure-ban van, mérje fel a késést, és vegye figyelembe, hogy ha a megoldás megfelel az Ön számára  
  - Ha még nem áll rendelkezésre a nagyvállalati szintű vállalati proxy, nem javasoljuk ezt a lehetőséget, mivel az ügyfél extra költségekkel és összetettséggel jár. Ha azonban úgy dönt, hogy további proxy-megoldást helyez üzembe, azzal a céllal, hogy engedélyezze a Pacemakerről az Azure felügyeleti nyilvános API-hoz való kimenő kapcsolatot, győződjön meg arról, hogy a proxy nagyon elérhető, és a virtuális gépektől a proxy felé irányuló késés alacsony.  

### <a name="pacemaker-configuration-with-proxy"></a>Pacemaker-konfiguráció proxyval 

Az iparágban számos különböző proxy lehetőség áll rendelkezésre. A proxy üzembe helyezésének lépésenkénti utasításai a dokumentum hatókörén kívül esnek. Az alábbi példában feltételezzük, hogy a proxy válaszol a **MyProxyService** , és figyeli a port **MyProxyPort**.  
Ha engedélyezni szeretné, hogy a pacemaker kommunikáljon az Azure felügyeleti API-val, hajtsa végre a következő lépéseket az összes fürtcsomóponton:  

1. Szerkessze a pacemaker konfigurációs fájl/etc/sysconfig/pacemaker, és adja hozzá a következő sorokat (a fürt összes csomópontja):  
   ```
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. Indítsa újra a pacemaker szolgáltatást az **összes** fürtcsomóponton.  
  - SUSE  
     ```
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  
     ```
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="next-steps"></a>Következő lépések

* [Ismerje meg, hogyan konfigurálhatja a pacemakert a SUSE-ben az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)
* [Ismerje meg, hogyan konfigurálhatja a pacemakert az Azure-beli Red Hat-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)
