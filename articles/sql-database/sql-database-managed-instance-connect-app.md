---
title: Az Azure SQL Database felügyelt példányain-alkalmazás csatlakoztatása |} A Microsoft Docs
description: Ez a cikk leírja, hogyan alkalmazását az Azure SQL Database felügyelt példányába való kapcsolódáshoz.
ms.service: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.custom: managed instance
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 82e8836892b033ccbb3c3ad9806257348afe3702
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818402"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Csatlakoztassa az alkalmazását az Azure SQL Database felügyelt példányához

A mai több lehetőség közül választhat annak eldöntése, hogyan és hol üzemelteti az alkalmazást során. 
 
Választhatja a felhőben alkalmazás üzemeltetése az Azure App Service vagy az Azure virtuális hálózat (VNet) integrált lehetőség hasonlóan az Azure App Service-környezet, a virtuális gép, virtuálisgép-méretezési némelyike használva. Sikerült a hibrid felhőalapú megközelítést, és tartsa a helyszíni alkalmazásokhoz. 
 
Bármilyen tetszőleges végzett, csatlakozhat, a felügyelt példány (előzetes verzió).  

![magas rendelkezésre állás](./media/sql-database-managed-instance/application-deployment-topologies.png)  

## <a name="connect-an-application-inside-the-same-vnet"></a>Az azonos virtuális hálózaton belüli alkalmazások csatlakoztatása 

Ebben a forgatókönyvben a legegyszerűbb. A virtuális hálózaton belüli virtuális gépek csatlakozhatnak egymáshoz közvetlenül akkor is, ha külön alhálózatokon találhatók. Ez azt jelenti, hogy az összes alkalmazás egy alkalmazás az Azure-környezet vagy a virtuális gépen belül csatlakoznia kell, hogy megfelelően beállítjuk a kapcsolati karakterláncot.  
 
Abban az esetben a kapcsolat nem lehet létrehozni, ellenőrizze, hogy van-e alkalmazás-alhálózat beállítása a hálózati biztonsági csoport. Ebben az esetben meg kell nyitnia az 1433-as port SQL, valamint a 11000-12000 porttartományt átirányítási kimenő kapcsolat. 

## <a name="connect-an-application-inside-a-different-vnet"></a>Egy másik virtuális hálózaton belül alkalmazások csatlakoztatása 

Ebben a forgatókönyvben nem egy kicsit összetettebb, mert felügyelt példány magánhálózati IP-címmel rendelkezik a saját virtuális hálózatában. Szeretne csatlakozni, egy alkalmazást kell tudnia érnie a virtuális hálózathoz, ahol a felügyelt példány üzemel. Tehát először szüksége az alkalmazás és a felügyelt példány virtuális hálózatok közötti kapcsolat. A virtuális hálózatok nem kell lennie ahhoz, hogy ez a forgatókönyv működjön ugyanabban az előfizetésben. 
 
A virtuális hálózatok csatlakoztatása két lehetőség van: 
- [Az Azure virtuális hálózatok közötti társviszony](../virtual-network/virtual-network-peering-overview.md) 
- A virtuális hálózatok közötti VPN-átjáró ([az Azure portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI-vel](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)) 
 
A társviszony-létesítési beállítást azért előnyösebb egy társviszony-létesítés használja a Microsoft gerinchálózatán tehát kapcsolat szempontjából, a társított virtuális hálózat és az azonos virtuális hálózaton található virtuális gépek között nincs észrevehető különbség a késés. Virtuális hálózatok közötti társviszony korlátozva a hálózatok ugyanabban a régióban.  
 
> [!IMPORTANT]
> A hálózatok ugyanabban a régióban, mert a virtuális hálózatok közötti társviszony-létesítési forgatókönyv a felügyelt példány korlátozódik [korlátokat a globális virtuális hálózati társviszony](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). 

## <a name="connect-an-on-premises-application"></a>A helyi alkalmazások csatlakoztatása 

Felügyelt példány csak egy magánhálózati IP-címen keresztül érhető el. Elérni a helyszíni, kell az alkalmazás és a felügyelt példány virtuális hálózatok között helyek közötti kapcsolat. 
 
Két lehetőség van a helyszíni csatlakozás Azure vnethez: 
- Site-to-Site VPN-kapcsolat ([az Azure portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI-vel](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)) 
- [Az ExpressRoute](../expressroute/expressroute-introduction.md) kapcsolat  
 
Sikeresen létrehozta az Azure-kapcsolat helyszíni, és nem létesít kapcsolatot egy felügyelt példányra, ellenőrzi, hogy a tűzfal tartalmazza-e kimenő kapcsolat megnyitása az 1433-as port SQL, valamint a 11000-12000 átirányítás portok tartománya. 

## <a name="connect-an-azure-app-service-hosted-application"></a>Azure App Service szolgáltatásban üzemeltetett alkalmazások csatlakoztatása 

Így az Azure App Service-ben való eléréséhez először kell az alkalmazás és a felügyelt példány virtuális hálózatok közötti kapcsolat létrehozása csak egy privát IP-címen keresztül elérhető lesz a felügyelt példánynak. Lásd: [az alkalmazás integrálása az Azure-beli virtuális hálózathoz](../app-service/web-sites-integrate-with-vnet.md).  
 
Című témakörben [hibaelhárítási virtuális hálózatok és az alkalmazások](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Ha a kapcsolat nem jön, próbálja meg [a hálózati konfiguráció szinkronizálása](sql-database-managed-instance-sync-network-configuration.md). 
 
Csatlakozás az Azure App Service-ben egy felügyelt példányra egy különleges esetben akkor, ha integrált felügyelt példány virtuális hálózat társviszonyba Azure App Service-hálózathoz. Adott esetben szükséges, állítsa be a következő konfigurációt: 

- Felügyelt példány virtuális hálózati átjáró nem lehet  
- Felügyelt példány virtuális hálózathoz kell rendelkeznie a távoli átjárók használata a beállításhalmaz 
- Társviszonyban lévő virtuális hálózatok közötti engedélyezési átjáró átviteli beállítás kell rendelkeznie. 
 
Ebben a forgatókönyvben a következő ábra mutatja be:

![integrált alkalmazás társviszony-létesítés](./media/sql-database-managed-instance/integrated-app-peering.png)
 
## <a name="connect-an-application-on-the-developers-box"></a>A fejlesztők Box alkalmazások csatlakoztatása 

A felügyelt példánynak keresztül érhetők el csak magánhálózati IP-cím így a fejlesztői boxból eléréséhez, először létre kell a fejlesztői box és a felügyelt példány virtuális hálózatok közötti kapcsolat létrehozása.  
 
Azure natív Tanúsítványalapú hitelesítés cikkek virtuális hálózat pont – hely kapcsolat konfigurálása ([az Azure portal](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md), [Azure CLI-vel](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)) részletesen bemutatja, hogyan sikerült elvégezni. 

## <a name="required-versions-of-drivers-and-tools"></a>Az illesztőprogramok és eszközök szükséges verzióinak

Az alábbi minimális verziók az eszközökkel és illesztőprogramokkal használata javasolt, ha azt szeretné, felügyelt példányhoz való csatlakozáshoz:

| Illesztőprogram vagy eszköz | Verzió |
| --- | --- |
|.NET-keretrendszer | 4.6.1 (vagy a .NET Core) | 
|ODBC-illesztő    | v17 |
|PHP-illesztőprogram | 5.2.0 |
|JDBC-illesztőprogram    | 6.4.0 |
|NODE.js-illesztőprogram | 2.1.1 |
|OLEDB illesztőprogram   | 18.0.2.0 |
|SSMS   | 17.8.1 vagy [magasabb](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) |

## <a name="next-steps"></a>További lépések

- Felügyelt példánnyal kapcsolatos további információkért lásd: [mit jelent a felügyelt példány](sql-database-managed-instance.md).
- Bemutatja, hogyan hozzon létre egy új felügyelt példány oktatóanyagért lásd: [létrehoz egy felügyelt példányt](sql-database-managed-instance-get-started.md).
