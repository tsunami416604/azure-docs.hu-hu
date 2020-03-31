---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: c731e2a7d4da1a66aabb50b335d526fbb6a0a302
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671541"
---
A regionális virtuális hálózat-integráció használata lehetővé teszi az alkalmazás számára a következők elérését:

* erőforrásokat a virtuális hálózatban ugyanabban a régióban, amelyet integrál 
* a virtuális hálózatokban lévő erőforrások az ugyanabban a régióban lévő virtuális hálózatra létesítve
* szolgáltatásvégpont által védett szolgáltatások
* erőforrások ExpressRoute-kapcsolatokon keresztül
* a virtuális hálózatban lévő erőforrásokat, amelyekhez csatlakozik
* erőforrások társviszony-létesítési kapcsolatok között, beleértve az ExpressRoute-kapcsolatokat is
* privát végpontok 

Ha a virtuális hálózat tal való integráció t ugyanabban a régióban, használhatja a következő Azure networking funkciók:

* Hálózati biztonsági csoportok (NSG-k) – Az integrációs alhálózaton elhelyezett hálózati biztonsági csoporttal blokkolhatja a kimenő forgalmat. A bejövő szabályok nem vonatkoznak, mivel nem használhatja a virtuális hálózat-integráció t az alkalmazáshoz való bejövő hozzáférés biztosításához.
* Útvonaltáblák (UDRs) – Az integrációs alhálózaton elhelyezhet egy útvonaltáblát, hogy a kimenő forgalmat a kívánt helyre küldje. 

Alapértelmezés szerint az alkalmazás csak az RFC1918 forgalmat irányítja a virtuális hálózatba. Ha az összes kimenő forgalmat a virtuális hálózatba szeretné irányítani, alkalmazza az alkalmazásbeállítást WEBSITE_VNET_ROUTE_ALL az alkalmazásra. Az alkalmazásbeállítás konfigurálása:

1. Nyissa meg a konfigurációs felhasználói felületet az alkalmazásportálon. **Új alkalmazásbeállítás** kiválasztása
1. Írja be **WEBSITE_VNET_ROUTE_ALL** a Név **mezőbe,** 1-et pedig az Érték mezőbe.

   ![Alkalmazásbeállítás biztosítása][4]

1. Kattintson az **OK** gombra.
1. **Mentés** kiválasztása

Ha az összes kimenő forgalmat a virtuális hálózatba irányítja, akkor az integrációs alhálózatra alkalmazott NSG-k és UDRs-ek hatálya alá kerül. Amikor az összes kimenő forgalmat a virtuális hálózatba irányítja, a kimenő címek továbbra is azok a kimenő címek lesznek, amelyek az alkalmazás tulajdonságaiban szerepelnek, kivéve, ha útvonalakat biztosít a forgalom máshol való elküldéséhez. 

A virtuális hálózatok virtuális hálózatokkal való integrációja ugyanabban a régióban bizonyos korlátozásokat alkalmaz:

* Nem érhető el erőforrások a globális társviszony-létesítési kapcsolatokon keresztül
* A funkció csak a PremiumV2 App Service-csomagokat támogató újabb App Service-méretezési egységekből érhető el.
* Az integrációs alhálózatot csak egy App Service-csomag használhatja
* A funkciót nem használhatják az App Service-környezetben lévő elkülönített csomagalkalmazások
* A szolgáltatáshoz egy nem használt alhálózatra van szükség, amely /27 32 címmel vagy nagyobb egy Erőforrás-kezelő virtuális hálózatában
* Az alkalmazásnak és a virtuális hálózatnak ugyanabban a régióban kell lennie
* A virtuális hálózat nem törölhető integrált alkalmazással. A virtuális hálózat törlése előtt távolítsa el az integrációt. 
* Csak az alkalmazással azonos előfizetésben integrálható a virtuális hálózatokkal.
* Az App Service-csomagonként csak egy regionális virtuális hálózat-integrációt rendelkezhet. Ugyanabban az App Service-csomagban több alkalmazás is használhatja ugyanazt a virtuális hálózatot. 
* Nem módosíthatja egy alkalmazás vagy csomag előfizetését, amíg van egy olyan alkalmazás, amely regionális virtuális hálózat-integrációt használ

Minden csomagpéldányhoz egy cím használatos. Ha az alkalmazást öt példányra méretezi, akkor öt címet használ. Mivel az alhálózat mérete nem módosítható a hozzárendelés után, olyan alhálózatot kell használnia, amely elég nagy ahhoz, hogy az alkalmazás által elérhető méreteket elférjen. A /26 64 címmel az ajánlott méret. A /26 64 címmel egy 30 példányú prémium csomagnak fog befogadni. Amikor egy tervet fel- vagy leskáláz, rövid ideig kétszer annyi címre van szüksége. 

Ha azt szeretné, hogy egy másik csomagban lévő alkalmazások eljussanak egy olyan virtuális hálózathoz, amelyhez már egy másik csomagban lévő alkalmazások kapcsolódnak, akkor a már meglévő virtuális hálózat-integráció által használttól eltérő alhálózatot kell választania.  

A funkció előzetes verzióban érhető el Linux esetén. A funkció Linux formája csak az 1918-as RFC-címekre (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) történő hívásokat támogatja.

### <a name="web--function-app-for-containers"></a>Web / Function App tárolókhoz

Ha az alkalmazást Linuxon, a beépített rendszerképek, regionális virtuális hálózat integrációja további módosítások nélkül működik. Ha web/ függvény alkalmazást használ tárolókhoz, a vnet-integráció használatához módosítania kell a docker-lemezképet. A docker-lemezkép ben használja a PORT környezeti változó, mint a fő webkiszolgáló figyelő port, használata helyett egy kódolt port számát. A PORT környezeti változót a platform automatikusan beállítja a tároló indítási időpontjában. Ha SSH-t használ, akkor az SSH démont úgy kell konfigurálni, hogy a SSH_PORT környezeti változó által megadott portszámot figyelje a regionális virtuális hálózat-integráció használatakor.  Nincs támogatja az átjáró szükséges VNet-integráció Linux. 

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

A regionális virtuális hálózat-integráció lehetővé teszi a szolgáltatásvégpontok használatát.  A szolgáltatásvégpontok az alkalmazással, a regionális virtuális hálózat integráció használatával csatlakozzon egy kijelölt virtuális hálózathoz, majd konfigurálja a szolgáltatás végpontok az integrációhoz használt alhálózaton. 

### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)

A hálózati biztonsági csoportok lehetővé teszik a virtuális hálózat erőforrásaiba irányuló bejövő és kimenő forgalom blokkolását. A regionális virtuális hálózat-integrációt használó alkalmazások a [Hálózati biztonsági csoport][VNETnsg] segítségével blokkolhatják a virtuális hálózaton vagy az interneten lévő erőforrások kimenő forgalmát. A nyilvános címekre irányuló forgalom blokkolásához az alkalmazásbeállításWEBSITE_VNET_ROUTE_ALL 1-re kell állítva. Az NSG bejövő szabályai nem vonatkoznak az alkalmazásra, mivel a virtuális hálózati integráció csak az alkalmazásból érkező kimenő forgalmat érinti. Az alkalmazás baérkező forgalom szabályozásához használja a Hozzáférési korlátozások funkciót. Az integrációs alhálózatra alkalmazott NSG a integrációs alhálózatra alkalmazott útvonalaktól függetlenül érvényben lesz. Ha WEBSITE_VNET_ROUTE_ALL 1-re volt állítva, és nem rendelkezett olyan útvonallal, amely hatással lenne a nyilvános cím forgalmára az integrációs alhálózaton, akkor a kimenő forgalom egészére továbbra is az integrációs alhálózathoz rendelt NSG-k vonatkoznának. Ha WEBSITE_VNET_ROUTE_ALL nincs beállítva, az NSG-k csak az RFC1918 forgalomra lesznek alkalmazva.

### <a name="routes"></a>Útvonalak

Az útvonaltáblák lehetővé teszik, hogy az alkalmazásból oda irányítsa a kimenő forgalmat, ahová csak szeretné. Alapértelmezés szerint az útvonaltáblák csak az RFC1918 célforgalmára lesznek hatással.  Ha WEBSITE_VNET_ROUTE_ALL 1-re állítja, az összes kimenő hívást érinti. Az integrációs alhálózaton beállított útvonalak nem befolyásolják a bejövő alkalmazáskérelmekre adott válaszokat. A gyakori célhelyek közé tartozhatnak a tűzfaleszközök vagy átjárók. Ha azt szeretné, hogy az összes kimenő forgalmat a helyszínen, egy útvonaltábla segítségével küldheti az összes kimenő forgalmat az ExpressRoute-átjáró. Ha a forgalmat egy átjáróhoz irányítja, ügyeljen arra, hogy a külső hálózaton lévő útvonalakat állítsa be a válaszok visszaküldéséhez.

A Border Gateway Protocol (BGP) útvonalai az alkalmazásforgalmát is befolyásolják. Ha a BGP-útvonalak valami, mint egy ExpressRoute-átjáró, az alkalmazás kimenő forgalma t. Alapértelmezés szerint a BGP-útvonalak csak az RFC1918 célforgalmára lesznek hatással. Ha WEBSITE_VNET_ROUTE_ALL 1-re van állítva, akkor az összes kimenő forgalmat befolyásolhatják a BGP-útvonalak. 


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/