---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: 7f2b011b2de5af0e4ace9cbeb4399911d8e83b7f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312817"
---
A regionális virtuális hálózat-integráció használata lehetővé teszi az alkalmazás számára a következők elérését:

* Erőforrások egy virtuális hálózatban ugyanabban a régióban, mint az alkalmazás.
* A virtuális hálózatokban az alkalmazás ba integrált virtuális hálózatra társviszonyba épített erőforrások.
* Szolgáltatásvégpont biztonságos szolgáltatásai.
* Erőforrások az Azure ExpressRoute-kapcsolatokon keresztül.
* A virtuális hálózatban lévő erőforrások, amelyekkel integrálva van.
* Erőforrások társviszony-létesítési kapcsolatok között, amely magában foglalja az Azure ExpressRoute-kapcsolatokat.
* Privát végpontok – Megjegyzés: A DNS-t külön kell kezelni az Azure DNS privát zónái helyett.

Ha a virtuális hálózat tal való integrációt használja ugyanabban a régióban, a következő Azure hálózati funkciókat használhatja:

* **Hálózati biztonsági csoportok (NSG-k):** Az integrációs alhálózaton elhelyezett NSG-vel letilthatja a kimenő forgalmat. A bejövő szabályok nem vonatkoznak, mert nem használhatja a Virtuálishálózat-integráció az alkalmazás hoz való bejövő hozzáférést.
* **Útvonaltáblák (UDRs):** Az integrációs alhálózaton elhelyezhet egy útvonaltáblát, hogy a kimenő forgalmat a kívánt helyre küldje.

Alapértelmezés szerint az alkalmazás csak az RFC1918 forgalmat irányítja a virtuális hálózatba. Ha az összes kimenő forgalmat a virtuális hálózatba szeretné irányítani, alkalmazza az alkalmazásbeállítást WEBSITE_VNET_ROUTE_ALL az alkalmazásra. Az alkalmazásbeállítás konfigurálása:

1. Nyissa meg a **konfigurációs** felhasználói felületet az alkalmazásportálon. Válassza **az Új alkalmazás beállítás lehetőséget.**
1. Írja be **WEBSITE_VNET_ROUTE_ALL** a **Név** mezőbe, az **1** értéket pedig az **Érték** mezőbe.

   ![Alkalmazásbeállítás biztosítása][4]

1. Válassza **az OK gombot.**
1. Kattintson a **Mentés** gombra.

Ha az összes kimenő forgalmat a virtuális hálózatra irányítja, az integrációs alhálózatra alkalmazott NSG-k és UDRs-ek függvényében van kitéve. Amikor az összes kimenő forgalmat a virtuális hálózatba irányítja, a kimenő címek továbbra is azok a kimenő címek, amelyek szerepelnek az alkalmazás tulajdonságaiban, kivéve, ha útvonalakat biztosít a forgalom máshol való küldéséhez.

A virtuális hálózatok virtuális hálózatokkal való integrációja ugyanabban a régióban bizonyos korlátozásokat alkalmaz:

* Globális társviszony-létesítési kapcsolatokon keresztül nem érhető el erőforrás.
* A funkció csak a PremiumV2 App Service-csomagokat támogató újabb Azure App Service-méretezési egységekből érhető el.
* Az integrációs alhálózatot csak egy App Service-csomag használhatja.
* A funkciót nem használhatják az App Service-környezetben lévő elkülönített csomagalkalmazások.
* A szolgáltatás hoz egy nem használt alhálózat, amely egy /27 32-es vagy nagyobb egy Azure Resource Manager virtuális hálózat.
* Az alkalmazásnak és a virtuális hálózatnak ugyanabban a régióban kell lennie.
* Nem törölheti a virtuális hálózat integrált alkalmazással. A virtuális hálózat törlése előtt távolítsa el az integrációt.
* Csak az alkalmazással azonos előfizetésben integrálható a virtuális hálózatokkal.
* Az App Service-csomagonként csak egy regionális virtuális hálózat-integrációt rendelkezhet. Ugyanabban az App Service-csomagban több alkalmazás is használhatja ugyanazt a virtuális hálózatot.
* Nem módosíthatja egy alkalmazás vagy csomag előfizetését, amíg van egy olyan alkalmazás, amely regionális virtuális hálózat-integrációt használ.
* Az alkalmazás nem tudja feloldani a címeket az Azure DNS-beli privát zónákban.

Minden csomagpéldányhoz egy cím használatos. Ha az alkalmazást öt példányra méretezi, akkor öt címet használ. Mivel az alhálózat mérete nem módosítható a hozzárendelés után, olyan alhálózatot kell használnia, amely elég nagy ahhoz, hogy az alkalmazás által elérhető méreteket elférjen. A /26 64 címmel az ajánlott méret. A /26 64 címmel egy 30 példányú prémium csomag nak ad helyet. Amikor egy tervet fel- vagy leskáláz, rövid ideig kétszer annyi címre van szüksége.

Ha azt szeretné, hogy egy másik csomagban lévő alkalmazások elérjék azt a virtuális hálózatot, amelyhez már egy másik csomagban lévő alkalmazások csatlakoznak, válasszon egy másik alhálózatot, mint amelyet a már meglévő virtuális hálózat-integráció használ.

A funkció előzetes verzióban érhető el Linux esetén. A funkció Linux formája csak az 1918-as RFC-címekre (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) történő hívásokat támogatja.

### <a name="web-or-function-app-for-containers"></a>Web- vagy függvényalkalmazás tárolókhoz

Ha az alkalmazást Linuxon, a beépített rendszerképek, regionális virtuális hálózat integrációja további módosítások nélkül működik. Ha web- vagy függvényalkalmazást használ tárolókhoz, a vnet-integráció használatához módosítania kell a docker-lemezképet. A docker-lemezkép ben használja a PORT környezeti változó, mint a fő webkiszolgáló figyelő port, használata helyett egy kódolt port számát. A PORT környezeti változót a platform automatikusan beállítja a tároló indítási időpontjában. SSH használata esetén az SSH démont úgy kell konfigurálni, hogy a SSH_PORT környezeti változó által megadott portszámon figyelje a regionális virtuális hálózat-integráció használatakor. Nincs támogatja az átjáró által igényelt virtuális hálózat integráció Linux on.

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

A regionális virtuális hálózat-integráció lehetővé teszi a szolgáltatásvégpontok használatát. A szolgáltatásvégpontok az alkalmazással, a regionális virtuális hálózat integráció használatával csatlakozzon egy kijelölt virtuális hálózathoz, majd konfigurálja a szolgáltatásvégpontok a célszolgáltatás az integrációhoz használt alhálózaton. Ha ezután egy szolgáltatáshoz szeretne hozzáférni a szolgáltatás végpontjai felett:

1. konfigurálja a helyi virtuális hálózat integrációját a webalkalmazással
1. lépjen a célszolgáltatásra, és konfigurálja a szolgáltatásvégpontokat az integrációhoz használt alhálózattal

### <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)

A hálózati biztonsági csoportok segítségével blokkolhatja a virtuális hálózat erőforrásainak bejövő és kimenő forgalmát. A regionális virtuális hálózat integrációt használó alkalmazások [egy hálózati biztonsági csoport][VNETnsg] segítségével blokkolhatják a virtuális hálózaton vagy az interneten lévő erőforrások kimenő forgalmát. A nyilvános címekre irányuló forgalom blokkolásához az alkalmazásbeállításWEBSITE_VNET_ROUTE_ALL 1-re kell állítva. Az NSG bejövő szabályai nem vonatkoznak az alkalmazásra, mert a virtuális hálózat integrációja csak az alkalmazásból érkező kimenő forgalmat érinti.

Az alkalmazás baérkező forgalom szabályozásához használja a Hozzáférési korlátozások funkciót. Az integrációs alhálózatra alkalmazott NSG érvényben van, függetlenül az integrációs alhálózatra alkalmazott útvonalaktól. Ha WEBSITE_VNET_ROUTE_ALL 1-re van állítva, és nincs olyan útvonala, amely befolyásolja a nyilvános cím forgalmát az integrációs alhálózaton, akkor a kimenő forgalom egészére továbbra is az integrációs alhálózathoz rendelt NSG-k vonatkoznak. Ha WEBSITE_VNET_ROUTE_ALL nincs beállítva, az NSG-k csak az RFC1918 forgalomra lesznek alkalmazva.

### <a name="routes"></a>Útvonalak

Az útvonaltáblák segítségével az alkalmazásból oda irányíthatja a kimenő forgalmat, ahová csak szeretné. Alapértelmezés szerint az útvonaltáblák csak az RFC1918 célforgalmára vannak hatással. Ha WEBSITE_VNET_ROUTE_ALL 1-re állítja, az összes kimenő hívást érinti. Az integrációs alhálózaton beállított útvonalak nem befolyásolják a bejövő alkalmazáskérelmekre adott válaszokat. A gyakori célhelyek közé tartozhatnak a tűzfaleszközök vagy átjárók.

Ha azt szeretné, hogy az összes kimenő forgalmat a helyszínen, egy útvonaltábla segítségével küldheti az összes kimenő forgalmat az ExpressRoute-átjáró. Ha a forgalmat egy átjáróhoz irányítja, ügyeljen arra, hogy a külső hálózaton lévő útvonalakat állítsa be a válaszok visszaküldéséhez.

A Border Gateway Protocol (BGP) útvonalai szintén hatással vannak az alkalmazás forgalmára. Ha a BGP-útvonalak valami, mint egy ExpressRoute-átjáró, az alkalmazás kimenő forgalma t. Alapértelmezés szerint a BGP-útvonalak csak az RFC1918 célforgalmat érintik. Ha WEBSITE_VNET_ROUTE_ALL 1-re van állítva, az összes kimenő forgalmat befolyásolhatják a BGP-útvonalak.


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
