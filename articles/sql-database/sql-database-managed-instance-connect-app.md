---
title: "Az Azure SQL adatbázis felügyelt példány csatlakozás alkalmazás |} Microsoft Docs"
description: "A cikk ismerteti az Azure SQL adatbázis felügyelt példány alkalmazás összekapcsolása."
ms.service: sql-database
author: srdjan-bozovic
manager: craigg
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: f02311026e3f28d4cf41dfe9b155f928885ae938
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Csatlakozás az Azure SQL adatbázis felügyelt példány alkalmazás

Ma több lehetősége van, amikor arról dönt, hogy hol és hogyan lehet az alkalmazás működteti. 
 
Választhatja a fogadó alkalmazás a felhőben Azure App Service vagy egy Azure virtuális hálózathoz (VNet) integrált beállítások például az Azure App Service Environment-környezet, virtuális gép, virtuálisgép-méretezési csoport használva. Sikerült hibrid felhőalapú megoldást, és tartsa a alkalmazásokhoz a helyszínen. 
 
Bármilyen tetszőleges végrehajtott, csatlakozhat, a felügyelt példánya (előzetes verzió).  

![Magas rendelkezésre állás](./media/sql-database-managed-instance/application-deployment-topologies.png)  

## <a name="connect-an-application-inside-the-same-vnet"></a>Az azonos virtuális hálózaton belüli alkalmazás csatlakoztatása 

Ebben a forgatókönyvben a legegyszerűbb. A virtuális hálózaton belüli virtuális gépek kapcsolódhatnak egymáshoz közvetlenül akkor is, ha különböző alhálózaton belül vannak. Ez azt jelenti, hogy, hogy az összes alkalmazást egy Azure alkalmazás környezet vagy a virtuális gép belül csatlakoztatni kell is, hogy a kapcsolati karakterlánc megfelelően.  
 
Abban az esetben, ha a kapcsolat nem tudott létrehozni, ellenőrizze, hogy van-e a hálózati biztonsági csoportok beállítása alkalmazás alhálózaton. Ebben az esetben szüksége az 1433-as port SQL, valamint az átirányítás 11000-12000 porttartományt kimenő kapcsolat megnyitásához. 

## <a name="connect-an-application-inside-a-different-vnet"></a>Egy másik virtuális hálózaton belüli alkalmazás csatlakoztatása 

Ebben a forgatókönyvben nem egy kicsit bonyolultabb, mert a felügyelt példány a saját virtuális magánhálózati IP-cím rendelkezik. Szeretne csatlakozni, kérelmet kell a hozzáférést a VNet felügyelt példány telepítési helyét. Így először kell az alkalmazás és a felügyelt példány VNet közötti kapcsolat. A Vnetek nem kell lennie ahhoz, hogy ez a forgatókönyv működjön ugyanahhoz az előfizetéshez. 
 
Kapcsolódás Vnetek két lehetőség áll rendelkezésre: 
- [Az Azure virtuális hálózati társviszony-létesítés](../virtual-network/virtual-network-peering-overview.md) 
- VNet – VNet VPN-átjáró ([Azure-portálon](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)) 
 
A társviszony-létesítési beállítás azt előnyösebb mert társviszony-létesítés használ Microsoft hálózat tehát kapcsolat szempontjából, nincs észrevehető különbség a késés VNet társítottak, és ugyanazt a virtuális hálózatot a virtuális gépek között. Vnetben társviszony-létesítés korlátozva ugyanabban a régióban, hálózatok, bár a kereszt-régió társviszony-létesítés engedélyezve van az egyes régiókban által megtekinthető villámnézetként.  
 
> [!IMPORTANT]
> Kereszt-régióban létrehozott hálózatok esetében nem lehet a rendelkezésre állást és megbízhatóságot, mint a nyilvános elérhetőség utáni kiadás esetében azonos szinten. Vnetben társviszony van, korlátozott képességeit, és előfordulhat, hogy nem érhető el az összes Azure-régiók. A rendelkezésre állás és a szolgáltatás állapotát a legfrissebb értesítések, tekintse meg a [Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network) frissítések lap. 

## <a name="connect-an-on-premises-application"></a>A helyszíni alkalmazást 

Felügyelt példány csak egy magánhálózati IP-cím keresztül érhető el. A helyszíni férni van szüksége az alkalmazás és a példány által felügyelt virtuális hálózat közötti pont-pont kapcsolat. 
 
Két módon helyszíni csatlakoztatása az Azure virtuális hálózat: 
- Pont-pont VPN-kapcsolat ([Azure-portálon](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)) 
- [ExpressRoute](../expressroute/expressroute-introduction.md) kapcsolat  
 
Ha a helyszíni Azure kapcsolat sikeresen létrejött, és meg nem lehet kapcsolatot létesíteni a felügyelt példány, ellenőrizze, hogy rendelkezik-e a tűzfal kimenő kapcsolat megnyitása az 1433-as port SQL, valamint az átirányítás 11000-12000 porttartomány. 

## <a name="connect-an-azure-app-service-hosted-application"></a>Azure App Service szolgáltatásban üzemeltetett alkalmazás csatlakoztatása 

Felügyelt példány is elérhetőek csak egy magánhálózati IP-cím, az Azure App Service eléréséhez először az alkalmazás és a felügyelt példány VNet közötti kapcsolat. Lásd: [az alkalmazás integrálása az Azure virtuális hálózat](../app-service/web-sites-integrate-with-vnet.md).  
 
Című témakörben [hibaelhárítási virtuális hálózatokat és alkalmazások](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Ha a kapcsolat nem hozható létre, próbálja meg [szinkronizálása a hálózati konfiguráció](sql-database-managed-instance-sync-network-configuration.md). 
 
Az Azure App Service szolgáltatásban felügyelt példányhoz kapcsolódva egy különleges esetben esetén, integrált felügyelt példány virtuális hálózatba társviszonyban Azure App Service-hálózathoz. Adott esetben beállítása a következő konfiguráció szükséges: 

- Felügyelt példány virtuális hálózat nem átjáróval kell rendelkeznie  
- Felügyelt példány VNet kell rendelkeznie a távoli átjáró beállítás használata 
- Nincsenek társviszonyban, virtuális hálózatot lehet engedélyezése átjáró átvitel beállítás beállítása 
 
Ebben a forgatókönyvben az alábbi ábrán látható:

![integrált alkalmazás társviszony-létesítés](./media/sql-database-managed-instance/integrated-app-peering.png)
 
## <a name="connect-an-application-on-the-developers-box"></a>A fejlesztők mezőben az alkalmazás csatlakoztatása 

Felügyelt példány csak a következőkkel érhetők el a magánhálózati IP-cím így a fejlesztői mezőből eléréséhez, először a fejlesztői be és a felügyelt példány VNet közötti kapcsolat.  
 
A virtuális hálózatba natív Azure tanúsítvány hitelesítési cikkekben pont – hely kapcsolat konfigurálása ([Azure-portálon](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)) részletesen bemutatja, hogyan azt megteheti.  

## <a name="next-steps"></a>További lépések

- Felügyelt példány kapcsolatos információkért lásd: [Mi az, hogy a felügyelt példánya](sql-database-managed-instance.md).
- Az oktatóanyagok esetén lásd: [hozzon létre egy felügyelt példányt](sql-database-managed-instance-tutorial-portal.md).
