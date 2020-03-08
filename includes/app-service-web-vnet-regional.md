---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: c731e2a7d4da1a66aabb50b335d526fbb6a0a302
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671541"
---
A regionális VNet-integráció használata lehetővé teszi, hogy az alkalmazás hozzáférjen:

* a VNet lévő erőforrások ugyanabban a régióban, amelyhez integrálva van 
* a virtuális hálózatok-ben lévő erőforrások a VNet azonos régióban találhatók
* szolgáltatás-végpont által védett szolgáltatások
* erőforrások ExpressRoute-kapcsolatokon keresztül
* azon VNet erőforrásai, amelyekhez csatlakozik
* többek között a ExpressRoute-kapcsolatokat is tartalmazó erőforrások
* Privát végpontok 

Ha VNet-integrációt használ az virtuális hálózatok-ben ugyanabban a régióban, akkor a következő Azure hálózati funkciókat használhatja:

* Hálózati biztonsági csoportok (NSG) – letilthatja a kimenő forgalmat egy olyan hálózati biztonsági csoporttal, amely az integrációs alhálózaton van elhelyezve. A bejövő szabályok nem érvényesek, mert a VNet-integráció nem használható az alkalmazáshoz való bejövő hozzáférés biztosításához.
* Route Tables (UDR) – az integrációs alhálózaton elhelyezhető egy útválasztási táblázat, amely a kívánt kimenő forgalmat küldi el. 

Alapértelmezés szerint az alkalmazás csak a VNet irányítja át a RFC1918 forgalmat. Ha az összes kimenő forgalmat át szeretné irányítani a VNet, alkalmazza az alkalmazás beállítását WEBSITE_VNET_ROUTE_ALL az alkalmazásra. Az alkalmazás beállításának konfigurálása:

1. Nyissa meg a konfigurációs felhasználói felületet az alkalmazás-portálon. **Új Alkalmazásbeállítás** kiválasztása
1. Írja be a **WEBSITE_VNET_ROUTE_ALL** nevet a név mezőbe, az érték mezőbe pedig az **1** értéket.

   ![Alkalmazásbeállítás megadása][4]

1. Kattintson az **OK** gombra.
1. Kattintson a **Mentés** gombra

Ha az összes kimenő forgalmat átirányítja a VNet, a rendszer az integrációs alhálózatra alkalmazott NSG és UDR fogja alkalmazni. Ha az összes kimenő forgalmat átirányítja a VNet, a kimenő címek továbbra is az alkalmazás tulajdonságaiban felsorolt kimenő címek lesznek, kivéve, ha olyan útvonalakat ad meg, amelyek máshová nem küldik a forgalmat. 

Bizonyos korlátozások vonatkoznak a VNet-integrációnak az azonos régióban található virtuális hálózatok való használatára:

* A globális társ-összekapcsolási kapcsolatok erőforrásai nem érhetők el
* A szolgáltatás csak olyan újabb App Service skálázási egységekből érhető el, amelyek támogatják a PremiumV2 App Service csomagokat.
* Az integrációs alhálózatot csak egy App Service-csomag használhatja
* A szolgáltatás nem használható a App Service Environmentban található elkülönített csomag-alkalmazásokban
* A szolgáltatáshoz egy nem használt alhálózat szükséges, amely egy/27 32-es címmel vagy nagyobb erőforrás-kezelő VNet
* Az alkalmazásnak és a VNet ugyanabban a régióban kell lennie
* Integrált alkalmazással nem törölhet VNet. A VNet törlése előtt távolítsa el az integrációt. 
* Csak a virtuális hálózatok integrálható az alkalmazással megegyező előfizetésben
* App Service-csomag esetében csak egy regionális VNet-integráció lehet. Ugyanazon a App Service csomagon belül több alkalmazás is használhatja ugyanazt a VNet. 
* Egy alkalmazás vagy csomag előfizetése nem módosítható, amíg van olyan alkalmazás, amely regionális VNet-integrációt használ

Az egyes csomag-példányok esetében egy-egy-egy-egy címnek Ha öt példányra méretezi az alkalmazást, akkor öt címet használ a rendszer. Mivel az alhálózat mérete nem módosítható a hozzárendelés után, olyan alhálózatot kell használnia, amely elég nagy ahhoz, hogy megfeleljen az alkalmazásnak. Az ajánlott méret a/26, 64 címmel. Az a/26, 64 címmel rendelkező Prémium csomag 30 példányban. Ha felfelé vagy lefelé méretezi a tervet, a rövid ideig kétszer annyi címre van szüksége. 

Ha azt szeretné, hogy alkalmazásai egy másik csomagban olyan VNet érjenek el, amely már egy másik csomag alkalmazásaihoz csatlakozik, akkor a meglévő VNet-integráció által használttól eltérő alhálózatot kell választania.  

A szolgáltatás előzetes verzióban érhető el a Linux rendszerben. A szolgáltatás Linux-formája csak az RFC 1918-címekre irányuló hívások kezdeményezését támogatja (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web--function-app-for-containers"></a>Web/függvényalkalmazás tárolók számára

Ha az alkalmazást Linux rendszeren futtatja a beépített rendszerképekkel, a regionális VNet-integráció további változtatások nélkül működik. Ha web/függvényalkalmazást használ a tárolók számára, a VNet-integráció használatához módosítania kell a Docker-rendszerképet. A Docker-rendszerképben használja a PORT környezeti változót a fő webkiszolgáló figyelési portjának hardcoded helyett. A PORT környezeti változóját a tároló indítási ideje automatikusan beállítja a platformon. Ha SSH-t használ, az SSH démont úgy kell konfigurálni, hogy figyelje a SSH_PORT környezeti változó által megadott portszámot a regionális VNet-integráció használatakor.  Az átjáró nem támogatja az VNet-integrációt a Linux rendszeren. 

### <a name="service-endpoints"></a>Szolgáltatási végpontok

A regionális VNet-integráció lehetővé teszi a szolgáltatási végpontok használatát.  Ha szolgáltatási végpontokat szeretne használni az alkalmazással, a regionális VNet-integrációval csatlakozhat egy kiválasztott VNet, majd az integrációhoz használt alhálózaton konfigurálhatja a szolgáltatási végpontokat. 

### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)

A hálózati biztonsági csoportok lehetővé teszik a bejövő és kimenő forgalom letiltását egy VNet lévő erőforrásokra. A regionális VNet-integrációt használó alkalmazások [hálózati biztonsági csoporttal][VNETnsg] letilthatják a VNet vagy az interneten lévő erőforrásokra irányuló kimenő forgalmat. A nyilvános címekre irányuló forgalom letiltásához az alkalmazás beállítását WEBSITE_VNET_ROUTE_ALL 1-re kell beállítani. Egy NSG bejövő szabályai nem vonatkoznak az alkalmazásra, mivel a VNet-integráció csak az alkalmazásból érkező kimenő forgalmat érinti. Az alkalmazás bejövő forgalmának szabályozásához használja a hozzáférési korlátozások funkciót. Az integrációs alhálózatra alkalmazott NSG az integrációs alhálózatra alkalmazott útvonalaktól függetlenül érvényesek lesznek. Ha a WEBSITE_VNET_ROUTE_ALL értéke 1, és nem rendelkezett olyan útvonallal, amely az integrációs alhálózaton lévő nyilvános címek forgalmát érintette, akkor az összes kimenő forgalom továbbra is az integrációs alhálózathoz rendelt NSG lesz. Ha WEBSITE_VNET_ROUTE_ALL nem lett beállítva, a NSG csak a RFC1918-forgalomra lesz alkalmazva.

### <a name="routes"></a>Útvonalak

Az útválasztási táblázatok lehetővé teszik a kimenő forgalom továbbítását az alkalmazásból bárhonnan, bárhol is legyenek. Alapértelmezés szerint az útválasztási táblák csak a RFC1918-forgalmat érintik.  Ha a WEBSITE_VNET_ROUTE_ALL 1 értékre állítja, akkor az összes kimenő hívást érinti a rendszer. Az integrációs alhálózaton beállított útvonalak nem érintik a bejövő alkalmazások kéréseire adott válaszokat. A gyakori célhelyek lehetnek tűzfalak vagy átjárók. Ha a helyszíni összes kimenő forgalmat is át szeretné irányítani, az útválasztási táblázat segítségével elküldheti az összes kimenő forgalmat a ExpressRoute-átjárónak. Ha átirányítja a forgalmat egy átjáróra, ügyeljen arra, hogy a külső hálózatban lévő útvonalakat az összes válasz visszaküldéséhez adja meg.

A Border Gateway Protocol (BGP) útvonalak az alkalmazások forgalmát is érintik. Ha az ExpressRoute-átjáróhoz hasonló BGP-útvonalak vannak, akkor az alkalmazás kimenő forgalmát fogja érinteni. Alapértelmezés szerint a BGP-útvonalak csak az RFC1918-cél forgalmát érintik. Ha a WEBSITE_VNET_ROUTE_ALL értéke 1, akkor a BGP-útvonalakon az összes kimenő forgalom hatással lehet. 


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/