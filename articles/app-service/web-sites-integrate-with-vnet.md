---
title: Alkalmazások integrálása az Azure Virtual Network – az Azure App Service-ben
description: Bemutatja, hogyan csatlakozhat egy új vagy meglévő Azure virtuális hálózat az Azure App Service-alkalmazás
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a5187ed299f77c11892c6e34c8dfd3f904c7e075
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067706"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Az alkalmazás integrálása az Azure-beli virtuális hálózathoz
Ez a dokumentum ismerteti az Azure App Service virtuális hálózat integrációja, és hogyan állítható be az Apps a [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). [Azure virtuális hálózatok] [ VNETOverview] (Vnetek) lehetővé teszi számos, az Azure-erőforrások nem internetes routeable hálózati helyezni.  

Az Azure App Service rendelkezik két formában. 

1. A több-bérlős rendszerek, amelyek támogatják az elkülönített kivételével díjszabások széles
2. Az App Service Environment (ASE), amely üzembe helyezi a virtuális hálózathoz, és támogatja az elkülönített díjszabású terv alkalmazások

Ez a dokumentum végighalad a két VNet-integráció funkciót, azaz a több-bérlős App Service-ben használható. Ha az alkalmazás [App Service Environment-környezet][ASEintro], akkor már egy virtuális hálózatban, és nem igényli a VNet-integráció funkció az azonos virtuális hálózatban található erőforrások eléréséhez. Az App Service-ben a hálózati funkciók részletes ismertetéséért olvassa el a [hálózati funkcióit az App Service-ben](networking-features.md)

Nincsenek két formáját, a virtuális hálózat integrációja

1. Egy verzió lehetővé teszi az integrációt a virtuális hálózatok ugyanabban a régióban. A funkció a képernyőn egy alhálózatot a virtuális hálózat ugyanabban a régióban van szükség. A szolgáltatás még előzetes verzióként, de az alábbi esetekben bizonyos korlátozásokkal Windows alkalmazás éles számítási feladatok esetében támogatott.
2. A más verzió lehetővé teszi az integrációt a más régiókban található virtuális hálózatok vagy a klasszikus virtuális hálózatok. A szolgáltatás ezen verziója szükséges, a virtuális hálózat egy virtuális hálózati átjáró üzembe helyezési. Ez az a pont – hely VPN-alapú szolgáltatás.

Egy alkalmazás egyszerre csak használhatja a VNet-integráció funkciót egy formája. Ezután a kérdés, mely szolgáltatásokat használja. Számos dologra vagy használhatja. Törölje a versenytársaink, ha a következők:

| Probléma  | Megoldás | 
|----------|----------|
| Szeretné elérni az RFC 1918-cím (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) ugyanabban a régióban | regionális virtuális hálózat integrációja |
| Egy klasszikus virtuális hálózat vagy egy másik régióban található virtuális hálózat eléréséhez szeretné | Átjáró szükséges a VNet-integráció |
| Szeretné elérni az RFC 1918 végpontok között az ExpressRoute | regionális virtuális hálózat integrációja |
| Szeretné elérni erőforrásokhoz Szolgáltatásvégpontok között | regionális virtuális hálózat integrációja |

Sem a szolgáltatás lehetővé teszi, hogy elérje a RFC 1918-címek között ExpressRoute. Tennie, hogy szeretné-e használni az ASE most.

A regionális virtuális hálózatok közötti integráció használatával csatlakozhat virtuális hálózatához, a helyszíni vagy nem konfigurálja a Szolgáltatásvégpontok. Ez külön hálózati konfigurációját. A regionális virtuális hálózat integrációja egyszerűen lehetővé teszi az alkalmazás ezen kapcsolattípusok közötti hívásokat.

Függetlenül a használt verziója a VNet-integráció a webes alkalmazás hozzáférést biztosít a virtuális hálózatban lévő erőforrásokra irányuló, de nem bejövő privát való hozzáférést biztosít a webes alkalmazás a virtuális hálózatról. Privát hozzáférést utal, hogy az alkalmazás csak elérhetőnek magánhálózaton például az Azure virtuális hálózatban. VNet-integráció csak azokat a virtuális hálózatok közötti kimenő hívásokat az alkalmazásból így van. 

A VNet-integráció funkciót:

* Standard, prémium szintű vagy PremiumV2 díjszabási csomag szükséges 
* támogatja a TCP és UDP
* a App Service-alkalmazások és a függvényalkalmazások

Néhány dolog, amely VNet-integráció nem támogatja, beleértve:

* a meghajtó csatlakoztatása
* AD-integráció 
* NetBios

## <a name="regional-vnet-integration"></a>regionális virtuális hálózat integrációja 

VNet-integráció és az alkalmazás ugyanabban a régióban található virtuális hálózatok használata esetén legalább 32 címet tartalmazzon delegált alhálózat használata szükséges. Az alhálózat nem használható bármi más. Kimenő hívások az alkalmazásból a címekről delegált alhálózaton kell tenni. Ha a VNet-integráció verzióját használja, a hívások címek a virtuális hálózatban található kérés érkezett. A virtuális hálózatban található címeket használ lehetővé teszi, hogy az alkalmazás számára:

* a védett szolgáltatásokhoz szolgáltatásvégpont-hívások
* erőforrások eléréséhez ExpressRoute-kapcsolatok között
* a virtuális hálózat csatlakozik az erőforrások eléréséhez
* Access-erőforrások közötti társviszonyban kapcsolat többek között az ExpressRoute-kapcsolatok

Ez a funkció előzetes verzióban érhető el, de a következő korlátozásokkal Windows alkalmazás éles számítási feladatok esetében támogatott:

* csak elérheti a címeket, amelyek az RFC 1918-címtartományba tartoznak. Most már a 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 címblokkok címeit.
* globális társviszony-létesítési kapcsolaton keresztül történő erőforrás nem érhető el
* útvonalak nem állítható be a virtuális hálózat az alkalmazásból érkező forgalom
* a funkció csak a újabb App Service-ben skálázási egységek, amelyek támogatják a PremiumV2 App Service-csomagokban érhető el.
* a funkció nem használható izolált csomag alkalmazásokat, amelyek az App Service Environment-környezetben
* a szolgáltatás használatához legalább 32 címet a Resource Manager virtuális hálózatban található egy nem használt alhálózat.
* Az alkalmazás és a virtuális hálózat ugyanabban a régióban kell lennie
* Az App Service-csomag példányonként egy címet használja. Alhálózat méretét a hozzárendelés után nem módosítható, mivel a is több, mint a maximális skálázhatósága alhálózatot használjon. 32-címekkel rendelkező/27-es nem az ajánlott mérete, mert a 20-példányok méretezett App Service-csomagot, amely helyet biztosít.
* Integrált alkalmazás a virtuális hálózat nem törölhető. Először el kell távolítania az integráció 
* Akkor is csak egy regionális virtuális hálózatok közötti integráció egy App Service-csomag. Több alkalmazás az App Service-csomag az azonos virtuális hálózaton használható. 

A szolgáltatás akkor is a Linux előzetes verzióban érhető el. A VNet-integráció funkció használata a Resource Manager virtuális hálózat ugyanabban a régióban:

1. Nyissa meg a hálózat felhasználói felület a portálon. Ha az alkalmazás tudja használni az új szolgáltatást, majd megjelenik hozzáadása virtuális hálózathoz (előzetes verzió) lehetőséget.  

   ![Válassza ki a VNet-integráció][6]

1. Válassza ki **hozzáadása virtuális hálózathoz (előzetes verzió)** .  

1. Válassza ki a Resource Manager virtuális hálózattal, szeretné integrálni és majd hozzon létre egy új alhálózatot vagy válasszon ki egy meglévő üres alhálózatot. Az integráció végrehajtásához kevesebb mint egy percet vesz igénybe. Az integráció során az alkalmazás újraindul.  Integráció befejezése után látni fogja részletek integrálva van a virtuális hálózatok közötti és a egy módosíthatóvá tájékoztatja, hogy a funkció előzetes verzióban érhető el.

   ![Válassza ki a virtuális hálózat és alhálózat][7]

Miután az alkalmazás integrálva van a virtuális hálózat, a azonos van konfigurálva a virtuális hálózat DNS-kiszolgáló fogja használni. 

Az alkalmazás leválasztása a virtuális hálózaton, válassza ki a **Disconnect**. A művelet újraindítja a webalkalmazás. 

Az új VNet-integráció szolgáltatás lehetővé teszi, hogy a Szolgáltatásvégpontok használatát.  Szolgáltatásvégpontok használatára az alkalmazását, segítségével az új VNet-integráció kapcsolódni egy kijelölt virtuális hálózathoz, majd válassza a Szolgáltatásvégpontok az alhálózaton, az integráció során használt. 

### <a name="how-vnet-integration-works"></a>Hogyan működik a VNet-integráció

Az App Service-ben alkalmazásokat feldolgozói szerepkörökkel kapcsolatos üzemelnek. Futtatási tervek dedikált az alapszintű és a magasabb díjszabási csomagot ahol nincs más ügyfelek futó számítási feladatokat ugyanazon feldolgozók. VNet-integráció csatlakoztatja a virtuális adapterek és a delegált alhálózat működik. Mivel az a cím a virtuális hálózatban található, akkor legtöbb dolgot a hozzáférést a vagy a virtuális hálózaton keresztül, mint a virtuális hálózatban található virtuális gép lenne. A hálózati megvalósítás eltér a virtuális hálózat és, hogy miért néhány hálózati funkció még nem érhető el ez a funkció használata során a virtuális gép futtatása.

![VNet-integráció](media/web-sites-integrate-with-vnet/vnet-integration.png)

Ha engedélyezve van a VNet-integráció, az alkalmazás továbbra is győződjön meg az internethez, a szokásos módon azonos csatornákon keresztül kimenő hívások. A kimenő címek, amelyek az alkalmazás tulajdonságainak portálon láthatók azok továbbra is az az alkalmazás által használt címek. Mi a módosítások az alkalmazás az, hogy a szolgáltatásvégpont-hívások védett szolgáltatásokhoz, vagy RFC 1918-címek hiányzóra változik a virtuális hálózathoz. 

A funkció csak a worker kiszolgálónként több virtuális adapter támogatja.  Több virtuális adapter worker kiszolgálónként egy App Service-csomag egy regionális virtuális hálózatok közötti integráció jelenti. Az alkalmazások az App Service-csomag használhatja ugyanazt a VNet-integráció, de ha egy alkalmazás egy további virtuális hálózathoz való csatlakozáshoz van szüksége, szüksége lesz egy másik App Service-csomag létrehozása. A használt virtuális adapter nem egy erőforrás, amely az ügyfél közvetlen hozzáféréssel rendelkezik.

Ez a technológia működését jellege miatt a VNet-integráció használt forgalom nem jelenik meg a Network Watcher vagy az NSG folyamatnaplóit.  

## <a name="gateway-required-vnet-integration"></a>Átjáró szükséges a VNet-integráció 

Az átjáró a virtuális hálózat integrációja szükséges:

* minden olyan régióban található virtuális hálózatok csatlakozni, legyenek azok a Resource Manager vagy a klasszikus virtuális hálózatok segítségével
* lehetővé teszi az alkalmazás egyszerre csak 1 virtuális hálózathoz való kapcsolódáshoz
* lehetővé teszi, hogy az App Service-csomag integrációját a legfeljebb öt virtuális hálózatok 
* lehetővé teszi az ugyanazon a Vneten, több alkalmazás az App Service-csomag a által használt teljes száma, amelyek segítségével az App Service-csomag befolyásolása nélkül.  Ha rendelkezik 6 ugyanazt az App Service-csomagban ugyanabban a Vnetben használó alkalmazások, hogy számít 1 virtuális hálózat használatban. 
* a pont – hely típusú VPN konfigurált virtuális hálózati átjáró szükséges.
* Nem támogatott Linux-alkalmazásokkal való használatra
* 99,9 %-os szolgáltatói szerződés miatt az SLA-t támogatja az átjárón

Ez a funkció nem támogatja:

* Erőforrásokhoz való hozzáférés ExpressRoute-on keresztül 
* Erőforrásokhoz való hozzáférés szolgáltatásvégpontokon keresztül 

### <a name="getting-started"></a>Első lépések

Az alábbiakban néhány dolog láthat, tartsa szem előtt a webalkalmazás csatlakozik egy virtuális hálózatot:

* Cél virtuális hálózat pont – hely VPN-útválasztó-alapú átjáróval engedélyezve van, mielőtt az alkalmazás csatlakoztatva kell rendelkeznie. 
* A virtuális hálózat, az App Service Plan(ASP) ugyanabban az előfizetésben kell lennie.
* Az alkalmazásokat, amelyek integrálhatók a virtuális hálózat használatához a virtuális hálózathoz tartozó DNS.

### <a name="set-up-a-gateway-in-your-vnet"></a>A vnet-átjáró beállítása ###

Ha már rendelkezik egy pont – hely címekkel konfigurált átjáróval, továbbléphet, a VNet-integráció konfigurálása az alkalmazását.  
Az átjáró létrehozása:

1. [Hozzon létre egy átjáró-alhálózatot] [ creategatewaysubnet] a virtuális hálózatban található.  

1. [A VPN-átjáró létrehozása][creategateway]. Válassza ki egy útvonalalapú VPN-típussal.

1. [A pont beállítása helycímek][setp2saddresses]. Ha az átjáró az alapszintű termékváltozatban nem, majd a pont – hely konfigurációban le kell tiltani az IKEV2 és SSTP ki kell választani. A címtér kell lennie az RFC 1918-címterületet, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Ha csak az átjáró létrehozása App Service VNet-integráció használni, akkor nincs szüksége egy tanúsítvány feltöltéséhez. Az átjáró létrehozása 30 percet is igénybe vehet. Nem lesz mindaddig, amíg az átjáró üzemképes integrálhatják az alkalmazás a virtuális hálózathoz. 

### <a name="configure-vnet-integration-with-your-app"></a>VNet-integráció konfigurálása az App-alkalmazással 

VNet-integráció engedélyezése az alkalmazásban: 

1. Az alkalmazás az Azure Portalon nyissa meg és nyissa meg az alkalmazásbeállítások, és válassza ki a hálózatkezelés > VNet-integráció. Az ASP a Standard Termékváltozatban kell vagy jobb, vagy a VNet-integráció szolgáltatással. 
 ![Felhasználói felület VNet-integráció][1]

1. Válassza ki **adja hozzá a virtuális hálózat**. 
 ![Adja hozzá a VNet-integráció][2]

1. Válassza ki a virtuális hálózathoz. 
  ![Válassza ki a virtuális hálózat][8]
  
Az alkalmazás az utolsó lépés után újraindítja.  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>Hogyan az átjáró szükséges a VNet-integráció a szolgáltatás működése

Az átjáró szükséges, VNet-integráció funkciót a pont – hely VPN-technológia épül. A pont – hely technológia korlátozza a hálózati hozzáférés csak az alkalmazást futtató virtuális géphez. Alkalmazások korlátozva csak kiküldeni forgalom az internethez, vagy VNet-integráció hibrid kapcsolatok keresztül. 

![Hogyan működik a VNet-integráció][3]

## <a name="managing-vnet-integration"></a>VNet-integráció kezelése
Az alkalmazás szintjén van arra, hogy csatlakoztatása és leválasztása a virtuális hálózathoz. A VNet-integráció egyszerre több alkalmazással érintő műveletekre az App Service csomag szintjén. Az alkalmazás > Hálózatkezelés > portal a VNet-integráció, részletekért a virtuális hálózaton. Láthatja, hogy az ASP ASP szinten hasonló információkat > Hálózatkezelés > VNet-integráció portál, beleértve az App Service-csomag mely alkalmazások használják egy adott integráció.

 ![Virtuális hálózat részletei][4]

A rendelkezésre álló információk, a virtuális hálózatok közötti integráció felhasználói felületén akkor ugyanaz az alkalmazás és az ASP Portalon.

* Virtuális hálózat neve – a virtuális hálózat felhasználói felület mutató hivatkozások
* Hely - jeleníti meg a virtuális hálózat helyét. Egy másik helyen található virtuális hálózat integrációja okozhat a késési problémák az alkalmazáshoz. 
* Tanúsítvány állapota – tükrözi, ha az App Service-csomag és a virtuális hálózat között a szinkronban-e a tanúsítványok.
* Átjáró állapota – kell használni az átjáró szükséges a VNet-integráció, láthatja, hogy az átjáró állapotának ellenőrzéséhez.
* Virtuális hálózati címtér - IP-címterét a virtuális hálózat mutatja be. 
* Pont – hely címtér - hely IP-címtér a pont a virtuális hálózat mutatja be. Irányuló hívásokhoz lesz a virtuális hálózati átjáró "szükséges" funkció használata során, ha az alkalmazás-feladó címe lesz címek egyikének. 
* Helyek közötti címtér - segítségével site-to-site VPN-ek a helyszíni erőforrásokhoz vagy más virtuális hálózatok közötti kapcsolatot a virtuális hálózat. Itt jelennek meg az IP-címtartományok VPN-kapcsolatra van definiálva.
* DNS-kiszolgálók – mutat be a virtuális hálózat DNS-kiszolgálók konfigurálva.
* Irányított IP-címek a virtuális hálózathoz – mutat be a címblokkok irányítva használt meghajtó adatforgalom a virtuális hálózat 

Az egyetlen művelet elvégezhető az alkalmazás a VNet-integráció nézetében, hogy az alkalmazás leválasztása a virtuális hálózat jelenleg csatlakoztatva van. Az alkalmazás leválasztása a virtuális hálózaton, válassza ki a **Disconnect**. Az alkalmazás újraindul, amikor leválasztja a virtuális hálózaton. Leválasztja a virtuális hálózat nem változik. Az alhálózatra vagy átjáró nem törlődnek. Ha meg kívánja törölni a virtuális hálózathoz, először az alkalmazás leválasztása a virtuális hálózathoz, és törölni az erőforrásokat, például az átjárók szüksége. 

Szeretné elérni az ASP virtuális hálózatok közötti integráció felhasználói felületén, nyissa meg az ASP felhasználói felületén, és válassza **hálózatkezelés**.  Válassza a VNet-integráció **kattintson ide a konfiguráláshoz** a hálózati szolgáltatás állapota felhasználói felületének megnyitásához.

![Az ASP VNet-integráció információk][5]

Az ASP virtuális hálózatok közötti integráció felhasználói felület bemutatják, a virtuális hálózatok, az alkalmazások az ASP által használt összes. Az egyes virtuális hálózatok részleteinek megtekintéséhez kattintson az Önt érdeklő virtuális hálózaton. Nincsenek két műveletet itt is végezhet.

* **Hálózat szinkronizálása**. A szinkronizálási hálózati művelet van, csak a függő VNet-integráció átjáró szolgáltatás. Egy szinkronizálási hálózati művelet végrehajtása biztosítja, hogy a tanúsítványok és hálózati adatok szinkronizálva. Adja hozzá, vagy módosítsa a virtuális hálózat DNS-ben, ha kíván végezni egy **hálózat szinkronizálása** műveletet. Ez a művelet újraindul a virtuális hálózat használatával bármilyen alkalmazást.
* **Útvonalak hozzáadása** útvonalak hozzáadása a hatékony felhasználhatóságot kimenő forgalom, a virtuális hálózathoz.

**Útválasztás** forgalom irányítása a virtuális hálózat az alkalmazásból, az útvonalakat a virtuális hálózathoz meghatározott szolgálnak. Ha szeretne további kimenő adatforgalmat küldjön a vnetben, ezután adhatja csak hozzá ezen címterületet. A funkció csak akkor működik az átjáró a VNet-integráció szükséges.

**Tanúsítványok** Ha az átjáró a VNet-integráció engedélyezése szükséges, nincs-e a szükséges exchange-tanúsítványok biztosítása a kapcsolat biztonságát. A tanúsítványok együtt vannak a DNS-konfigurációt, útvonalak és más hasonló dolgot, amelyek ismertetik a hálózaton.
Ha tanúsítványokat vagy hálózati adatok megváltozik, kattintson a "Hálózat szinkronizálása" szeretne. Ha "Hálózat szinkronizálása" gombra kattint, akkor miatt a rövid kimaradásokat a kapcsolat az alkalmazás és a virtuális hálózat között. Amíg az alkalmazás újraindítása után a nem a kapcsolat megszakadása okozhat a hely nem működhet megfelelően. 

## <a name="accessing-on-premises-resources"></a>Hozzáférés a helyszíni erőforrások
Alkalmazások integrálása a virtuális hálózatok helyek közötti kapcsolattal rendelkezik a helyszíni erőforrásokat érhetik el. Ha használja az átjáró szükséges a VNet-integráció, frissítenie kell a helyszíni VPN-átjáró útvonalakat a pont – hely címe blokkokkal. Ha a site-to-site VPN először be van állítva, a parancsprogramok segítségével konfigurálja kell útvonalak megfelelően beállítva. A pont – hely címeket ad hozzá, a site-to-site VPN létrehozása után, ha az útvonalak manuálisan frissíteni szeretné. Megtudhatja, hogyan valósítható meg / átjáró eltérőek lehetnek, és nem ebben a témakörben találhatók. Nem lehet konfigurálni a site-to-site VPN-kapcsolaton keresztül a BGP.

Nem tartozik a regionális virtuális hálózatok közötti integráció funkció elérhetik a virtuális hálózaton keresztül, és a helyszíni szükséges további konfiguráció. Egyszerűen csatlakoztassa a virtuális hálózatot a helyszíni ExpressRoute és a egy helyek közötti VPN használatával. 

> [!NOTE]
> Az átjáró szükséges, VNet-integráció funkciót egy alkalmazást egy ExpressRoute-átjáróval rendelkező virtuális hálózat nem integrálható. Akkor is, ha az ExpressRoute-átjárót konfigurált [együttes használata mód] [ VPNERCoex] a VNet-integráció nem működik. Ha egy ExpressRoute-kapcsolaton keresztül erőforrások elérésére van szüksége használhatja a regionális virtuális hálózat integrációja vagy egy [App Service Environment-környezet][ASE], amely fut a virtuális hálózatban található. 
> 
> 

## <a name="peering"></a>Társviszony-létesítés
Ha a társviszony-létesítés a regionális virtuális hálózatok közötti integráció használ, nem kell további konfigurációt elvégezni. 

Ha használja az átjáró szükséges a VNet-integráció a társviszony-létesítés, be kell állítania néhány további elemeket. Konfigurálhatja a társviszony-létesítést úgy az alkalmazását:

1. Adja hozzá az alkalmazás virtuális Társhálózat-kapcsolatot csatlakozik. A társviszony-létesítési kapcsolat hozzáadásakor engedélyezése **virtuális hálózati hozzáférés engedélyezése** , és ellenőrizze **továbbított forgalom engedélyezése** és **átjárótranzit engedélyezése**.
1. A virtuális hálózattal, hogy társviszonyban áll a társviszony-létesítési kapcsolat hozzáadása a virtuális hálózathoz csatlakozik. A cél virtuális hálózatok közötti társviszony-létesítési kapcsolat hozzáadásakor engedélyezése **virtuális hálózati hozzáférés engedélyezése** , és ellenőrizze **továbbított forgalom engedélyezése** és **lehetővé teszi a távoli átjárók**.
1. Nyissa meg az App Service-csomag > Hálózat > virtuális hálózat integrációja felhasználói felület a portálon.  Válassza ki a virtuális hálózat, az alkalmazás csatlakozik. Az útválasztási szakasz alatt adja hozzá a címtartomány azon virtuális hálózat társviszonyban áll a virtuális hálózattal, az alkalmazás csatlakoztatva van.  


## <a name="pricing-details"></a>Díjszabás részletei
A regionális virtuális hálózat integrációja rendelkezik külön díj nélküli használata az ASP árképzési szint költségek mellett.

Nincsenek három kapcsolódó költségek a szükséges átjáró a VNet-integráció szolgáltatás használatára:

* Az ASP árképzési szint díjak – az alkalmazások kell lennie a Standard, prémium szintű vagy PremiumV2 App Service-csomag. További részleteket a költségekre Itt tekintheti meg: [App Service díjszabását][ASPricing]. 
* Az adatátviteli költségek – itt a kimenő adatforgalom díját, akkor is, ha a virtuális hálózat ugyanabban az adatközpontban. Ezeket a díjakat olyan [Data Transfer Díjszabásának részletei][DataPricing]. 
* VPN-átjáró költségek – itt a VNet-átjáró szükséges, a pont – hely VPN-hez költsége. A részleteket a rendszer a [VPN Gateway díjszabás] [ VNETPricing] lapot.


## <a name="troubleshooting"></a>Hibaelhárítás
Amíg a funkció segítségével egyszerűen állíthat, ez nem jelenti azt, hogy a felhasználói élmény lesz-e a probléma ingyenes. Kell észlel a kívánt végpontra elérésével kapcsolatos problémák bizonyos segédprogramokat használhatja az app-konzol közötti kapcsolat teszteléséhez. Nincsenek két konzol használható. Az egyik a Kudu konzol a másik pedig a konzol az Azure Portalon. A Kudu konzol az alkalmazás eléréséhez, válassza a eszközök -> Kudu. Ez megegyezik a helynév: [%] lépjen. scm.azurewebsites.net. Miután, amely megnyílik, nyissa meg a hibakeresési konzolt lapot. Eszközök válassza az Azure-portál központi konzolon, majd az alkalmazásából való beolvasásához konzol ->. 

#### <a name="tools"></a>Eszközök
Az eszközök **ping**, **nslookup** és **tracert** biztonsági okokból a konzolon keresztül nem fog működni. Töltse ki a void, két különálló hozzáadott eszközök. DNS-funkciók teszteléséhez hozzáadtunk egy nameresolver.exe nevű eszközt. A szintaxis a következő:

    nameresolver.exe hostname [optional: DNS Server]

Használhat **nameresolver** ellenőrizze a gazdagép neve, amely az alkalmazása függ. Ezzel a módszerrel tesztelheti Ha semmit a DNS rosszul konfigurálva van, vagy esetleg nem fér hozzá a DNS-kiszolgáló. Láthatja, hogy a DNS-kiszolgáló, amely az alkalmazás a környezeti változók WEBSITE_DNS_SERVER és WEBSITE_DNS_ALT_SERVER megnézzük a konzol fogja használni.

A következő eszköz lehetővé teszi, hogy az állomás és port kombinációjához TCP-kapcsolat tesztelése. Ez az eszköz neve **tcpping** és szintaxisa:

    tcpping.exe hostname [optional: port]

A **tcpping** segédprogram jelzi, hogy egy adott állomás és port elérheti. Csak megmutatja sikeres ha: egy alkalmazás figyeli, az állomás és port kombinációját, és a megadott állomás és port az alkalmazásból a hálózati hozzáférést.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Hibakeresés a virtuális hálózathoz való hozzáférés üzemeltetett erőforrásokhoz
Létezik néhány dolog, amely megakadályozhatja, hogy az alkalmazás egy adott állomás és port elérését. A legtöbbször egyike három dolgot:

* **A tűzfal olyan módon.** Van egy tűzfal módja, ha eléri a a TCP időtúllépési. A TCP időtúllépési érték ebben az esetben 21 másodperc. Használja a **tcpping** eszköz kapcsolat teszteléséhez. TCP-időtúllépések miatt tűzfalak túl sok-sok dolog lehet, de kezdeni. 
* **DNS nem érhető el.** A DNS-időtúllépési érték három másodperc / DNS-kiszolgáló. Ha két DNS-kiszolgálók, az időtúllépési érték 6 másodperc. Nameresolver segítségével megtekintheti, hogy működik-e a DNS. Ne felejtse el, nem használhatja az nslookup, amely nem használ a DNS-ben a virtuális hálózat van beállítva. Nem érhető el, ha sikerült van a tűzfal, vagy le lehet NSG letiltja a DNS, vagy elérését.

Ha azok az elemek nem válaszol a problémákat, tekintse meg első, például: 

**regionális virtuális hálózat integrációja**
* is your destination an RFC 1918 address
* van-e az integráció alhálózatról egy NSG blokkolási kimenő
* Ha több expressroute-on vagy VPN, a helyszíni átjáró úgy irányíthatja a forgalmat az Azure biztonsági mentés? Ha végpontokat a virtuális hálózathoz, de nem a helyszíni érhető el, akkor hasznos ellenőrizni.

**Átjáró szükséges a VNet-integráció**
* a pont – hely címtartomány RFC 1918 tartományok (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* Az átjárót a portálon fel állapottal jelenik meg? Ha az átjáró nem működik, majd hálózatra, készítsen biztonsági mentést.
* Tanúsítványok megjelenítése tegye, hogy szinkronban, vagy azt, gyanítja, hogy megváltozott-e a hálózati konfigurációt?  Ha a tanúsítványok nincsenek szinkronban, vagy azt gyanítja, hogy az a virtuális hálózat konfigurációja, amely nem volt szinkronizálva az ASP-változás történt, majd nyomja le a "Hálózat szinkronizálása".
* Ha több expressroute-on vagy VPN, a helyszíni átjáró úgy irányíthatja a forgalmat az Azure biztonsági mentés? Ha végpontokat a virtuális hálózathoz, de nem a helyszíni érhető el, akkor hasznos ellenőrizni.

Kihívást hálózatkezelési problémákat hibakeresés azért, mert nem látjuk blokkolja a hozzáférést egy adott gazdagép: port kombináció. Okai a következők:

* megakadályozza a hozzáférést az alkalmazásport a webhely IP-címtartomány a pont a gazdagép van a tűzfal. Nyilvános hozzáférés határainak átlépése alhálózatok gyakran van szükség.
* a cél gazdagép nem működik
* az alkalmazás nem működik
* a megfelelő IP- vagy állomásnév kellett
* az alkalmazás a várttól mint egy másik porton figyel. A folyamat azonosítója a figyelőport meg tudja a végponti gazdagépen a "netstat - aon" használatával. 
* a hálózati biztonsági csoportok vannak konfigurálva, hogy azok való hozzáférés letiltása az alkalmazás-állomás és port a helyet iprange pont oly módon

Ne feledje, hogy nem tudja milyen címet az alkalmazás ténylegesen fog használni. Bármely az integrációs alhálózatra vagy a pont – hely címtartományának címe, így engedélyezze a hozzáférést a teljes címtartományt szükséges lehet. 

További hibakeresési lépések az alábbiak:

* csatlakozhat a virtuális hálózatban található virtuális Géphez, és próbálja meg elérni az erőforrást állomás: port onnan. A TCP hozzáférési tesztelni, használja a PowerShell-paranccsal **test-netconnection**. A szintaxis a következő:

      test-netconnection hostname [optional: -Port]

* viszi, megjelenik egy alkalmazást, egy virtuális gép és a teszt hozzáférést az adott gazdagépen, és az alkalmazás használatával a konzolról port **tcpping**

#### <a name="on-premises-resources"></a>A helyszíni erőforrásokhoz ####

Ha az alkalmazás nem érhető el egy erőforrást a helyszíni, majd ellenőrizze, ha az erőforrás a elérheti a virtuális hálózat. Használja a **test-netconnection** TCP-hozzáférés ellenőrzése a PowerShell-parancsot. Ha a virtuális gép nem tudja elérni a helyszíni erőforráshoz, a VPN vagy ExpressRoute-kapcsolat nincs megfelelően konfigurálva.

Ha a virtuális hálózat VM elérheti a helyszíni rendszer, de az alkalmazás nem tudja, majd oka valószínűleg egy, az alábbi okok miatt:

* az útvonalakat az alhálózathoz nincsenek konfigurálva, vagy mutasson a hely címtartományt a helyszíni átjáróban
* a hálózati biztonsági csoportok forgalomszűrők blokkolják a hozzáférést a pont – hely IP-címtartomány
* a helyszíni tűzfal forgalomszűrők blokkolják a pont – hely IP-címtartomány érkező forgalom
* próbál elérni egy RFC 1918-cím használatával a regionális virtuális hálózat integrációja


## <a name="powershell-automation"></a>PowerShell automation

Az App Service integrálható az Azure Virtual Network PowerShell-lel. Az alkalmazást kész parancsprogramok esetén lásd: [az Azure App Service alkalmazás csatlakoztatása az Azure Virtual Networkhöz](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-details.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-aspdetails.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-newvnet.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-newvnetdetails.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-selectvnet.png

<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#gatewaysubnet
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
