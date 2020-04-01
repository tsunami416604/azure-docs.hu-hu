---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: e0db3ce7d31b838ca6f7d566083a33ee215d3399
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419526"
---
A regionális virtuális hálózat-integráció használata lehetővé teszi az alkalmazás számára a következők elérését:

* A virtuális hálózat erőforrásai ugyanabban a régióban, amelyintegrálható.
* A virtuális hálózatokban lévő erőforrások, amelyek ugyanabban a régióban vannak a virtuális hálózatban.
* Szolgáltatásvégpont biztonságos szolgáltatásai.
* Erőforrások az Azure ExpressRoute-kapcsolatokon keresztül.
* A virtuális hálózat erőforrásai, amelyekhez csatlakozik.
* Erőforrások társviszony-létesítési kapcsolatok között, amely magában foglalja az Azure ExpressRoute-kapcsolatokat.
* Privát végpontok.

Ha a virtuális hálózat integrációja a virtuális hálózatok ugyanabban a régióban, használhatja a következő Azure hálózati funkciók:

* **Hálózati biztonsági csoportok (NSG-k):** Az integrációs alhálózaton elhelyezett NSG-vel letilthatja a kimenő forgalmat. A bejövő szabályok nem vonatkoznak, mert nem használhatja a Virtuálishálózat-integráció az alkalmazás hoz való bejövő hozzáférést.
* **Útvonaltáblák (UDRs):** Az integrációs alhálózaton elhelyezhet egy útvonaltáblát, hogy a kimenő forgalmat a kívánt helyre küldje.

Alapértelmezés szerint az alkalmazás csak az RFC1918 forgalmat irányítja a virtuális hálózatba. Ha az összes kimenő forgalmat a virtuális hálózatba szeretné irányítani, alkalmazza az alkalmazásbeállítást WEBSITE_VNET_ROUTE_ALL az alkalmazásra. Az alkalmazásbeállítás konfigurálása:

1. Nyissa meg a **konfigurációs** felhasználói felületet az alkalmazásportálon. Válassza **az Új alkalmazás beállítás lehetőséget.**
1. Írja be **WEBSITE_VNET_ROUTE_ALL** a **Név** mezőbe, az **1** értéket pedig az **Érték** mezőbe.

   ![Alkalmazásbeállítás biztosítása][4]

1. Válassza **az OK gombot.**
1. Kattintson a **Mentés** gombra.

Ha az összes kimenő forgalmat a virtuális hálózatra irányítja, az integrációs alhálózatra alkalmazott NSG-k és UD-k hatálya alá tartozik. Amikor az összes kimenő forgalmat a virtuális hálózatba irányítja, a kimenő címek továbbra is azok a kimenő címek, amelyek szerepelnek az alkalmazás tulajdonságaiban, kivéve, ha útvonalakat biztosít a forgalom máshol történő elküldéséhez.

A virtuális hálózat integrációja az ugyanabban a régióban lévő virtuális hálózatokkal való használata bizonyos korlátozásokat alkalmaz:

* Globális társviszony-létesítési kapcsolatokon keresztül nem érhető el erőforrás.
* A funkció csak a PremiumV2 App Service-csomagokat támogató újabb Azure App Service-méretezési egységekből érhető el.
* Az integrációs alhálózatot csak egy App Service-csomag használhatja.
* A funkciót nem használhatják az App Service-környezetben lévő elkülönített csomagalkalmazások.
* A szolgáltatás hoz egy nem használt alhálózat, amely egy /27 32-es vagy nagyobb egy Azure Resource Manager virtuális hálózat.
* Az alkalmazásnak és a virtuális hálózatnak ugyanabban a régióban kell lennie.
* Integrált alkalmazással nem törölhetvirtuális hálózatot. A virtuális hálózat törlése előtt távolítsa el az integrációt.
* Csak az alkalmazással azonos előfizetésben integrálható a virtuális hálózatokkal.
* Az App Service-csomagonként csak egy regionális virtuális hálózat-integrációt rendelkezhet. Ugyanabban az App Service-csomagban több alkalmazás is használhatja ugyanazt a virtuális hálózatot.
* Nem módosíthatja egy alkalmazás vagy csomag előfizetését, amíg van egy olyan alkalmazás, amely regionális virtuális hálózat-integrációt használ.

Minden csomagpéldányhoz egy cím használatos. Ha az alkalmazást öt példányra méretezi, akkor öt címet használ. Mivel az alhálózat mérete nem módosítható a hozzárendelés után, olyan alhálózatot kell használnia, amely elég nagy ahhoz, hogy az alkalmazás által elérhető méreteket elférjen. A /26 64 címmel az ajánlott méret. A /26 64 címmel egy 30 példányú prémium csomag nak ad helyet. Amikor egy tervet fel- vagy leskáláz, rövid ideig kétszer annyi címre van szüksége.

Ha azt szeretné, hogy egy másik csomagban lévő alkalmazásai olyan virtuális hálózatot érjenek el, amelyhez már egy másik csomagban lévő alkalmazások csatlakoznak, válasszon egy másik alhálózatot, mint amelyet a már meglévő virtuális hálózat-integráció használ.

A funkció előzetes verzióban érhető el Linux esetén. A funkció Linux formája csak az 1918-as RFC-címekre (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) történő hívásokat támogatja.

### <a name="web-or-function-app-for-containers"></a>Web- vagy függvényalkalmazás tárolókhoz

Ha az alkalmazást Linuxon, a beépített rendszerképek, regionális virtuális hálózat integrációja további módosítások nélkül működik. Ha web- vagy függvényalkalmazást használ tárolókhoz, a vnet-integráció használatához módosítania kell a docker-lemezképet. A docker-lemezkép ben használja a PORT környezeti változó, mint a fő webkiszolgáló figyelő port, használata helyett egy kódolt port számát. A PORT környezeti változót a platform automatikusan beállítja a tároló indítási időpontjában. SSH használata esetén az SSH démont úgy kell konfigurálni, hogy a SSH_PORT környezeti változó által megadott portszámon figyelje a regionális virtuális hálózat-integráció használatakor. Nincs támogatja az átjáró által igényelt virtuális hálózat integráció Linux on.

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

A regionális virtuális hálózat-integráció, a szolgáltatás végpontok. Ha szolgáltatásvégpontokat szeretne használni az alkalmazással, használja a regionális virtuális hálózat integrációját a kijelölt virtuális hálózathoz való csatlakozáshoz. Ezután konfigurálja a szolgáltatásvégpontokat az integrációhoz használt alhálózaton.

### <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)

A hálózati biztonsági csoportok segítségével blokkolhatja a virtuális hálózat erőforrásaiba irányuló bejövő és kimenő forgalmat. A regionális virtuális hálózat integrációt használó alkalmazások [hálózati biztonsági csoport][VNETnsg] segítségével blokkolhatják a virtuális hálózat vagy az internet erőforrásainak kimenő forgalmát. A nyilvános címekre irányuló forgalom blokkolásához az alkalmazásbeállításWEBSITE_VNET_ROUTE_ALL 1-re kell állítva. Az NSG bejövő szabályai nem vonatkoznak az alkalmazásra, mert a virtuális hálózat integrációja csak az alkalmazásból érkező kimenő forgalmat érinti.

Az alkalmazás baérkező forgalom szabályozásához használja a Hozzáférési korlátozások funkciót. Az integrációs alhálózatra alkalmazott NSG érvényben van, függetlenül az integrációs alhálózatra alkalmazott útvonalaktól. Ha WEBSITE_VNET_ROUTE_ALL 1-re van állítva, és nincs olyan útvonala, amely befolyásolja a nyilvános cím forgalmát az integrációs alhálózaton, akkor a kimenő forgalom egészére továbbra is az integrációs alhálózathoz rendelt NSG-k vonatkoznak. Ha WEBSITE_VNET_ROUTE_ALL nincs beállítva, az NSG-k csak az RFC1918 forgalomra lesznek alkalmazva.

### <a name="routes"></a>Útvonalak

Az útvonaltáblák segítségével az alkalmazásból oda irányíthatja a kimenő forgalmat, ahová csak szeretné. Alapértelmezés szerint az útvonaltáblák csak az RFC1918 célforgalmára vannak hatással. Ha WEBSITE_VNET_ROUTE_ALL 1-re állítja, az összes kimenő hívást érinti. Az integrációs alhálózaton beállított útvonalak nem befolyásolják a bejövő alkalmazáskérelmekre adott válaszokat. A gyakori célhelyek közé tartozhatnak a tűzfaleszközök vagy átjárók.

Ha azt szeretné, hogy az összes kimenő forgalmat a helyszínen, egy útvonaltábla segítségével küldheti az összes kimenő forgalmat az ExpressRoute-átjáró. Ha a forgalmat egy átjáróhoz irányítja, ügyeljen arra, hogy a külső hálózaton lévő útvonalakat állítsa be a válaszok visszaküldéséhez.

A Border Gateway Protocol (BGP) útvonalai szintén hatással vannak az alkalmazás forgalmára. Ha a BGP-útvonalak valami, mint egy ExpressRoute-átjáró, az alkalmazás kimenő forgalma t. Alapértelmezés szerint a BGP-útvonalak csak az RFC1918 célforgalmat érintik. Ha WEBSITE_VNET_ROUTE_ALL 1-re van állítva, az összes kimenő forgalmat befolyásolhatják a BGP-útvonalak.


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/