---
title: Az Azure SQL Database felügyelt példányain-alkalmazás csatlakoztatása |} A Microsoft Docs
description: Ez a cikk leírja, hogyan alkalmazását az Azure SQL Database felügyelt példányába való kapcsolódáshoz.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: 0221965c51f2287cb6042c33b9ab3402e104abc3
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48870478"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Csatlakoztassa az alkalmazását az Azure SQL Database felügyelt példányához

A mai több lehetőség közül választhat annak eldöntése, hogyan és hol üzemelteti az alkalmazást során. 
 
Választhatja a felhőben alkalmazás üzemeltetése az Azure App Service vagy az Azure virtuális hálózat (VNet) integrált lehetőség hasonlóan az Azure App Service-környezet, a virtuális gép, virtuálisgép-méretezési némelyike használva. Sikerült a hibrid felhőalapú megközelítést, és tartsa a helyszíni alkalmazásokhoz. 
 
Bármilyen tetszőleges végzett, csatlakozhat, a felügyelt példány.  

![magas rendelkezésre állás](./media/sql-database-managed-instance/application-deployment-topologies.png)  
## <a name="connect-an-application-inside-the-same-vnet"></a>Az azonos virtuális hálózaton belüli alkalmazások csatlakoztatása 

Ebben a forgatókönyvben a legegyszerűbb. A virtuális hálózaton belüli virtuális gépek csatlakozhatnak egymáshoz közvetlenül akkor is, ha külön alhálózatokon találhatók. Ez azt jelenti, hogy az összes alkalmazás egy alkalmazás az Azure-környezet vagy a virtuális gépen belül csatlakoznia kell, hogy megfelelően beállítjuk a kapcsolati karakterláncot.  
 
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

## <a name="connect-an-application-on-the-developers-box"></a>A fejlesztők Box alkalmazások csatlakoztatása

A felügyelt példánynak keresztül érhetők el csak magánhálózati IP-cím így a fejlesztői boxból eléréséhez, először létre kell a fejlesztői box és a felügyelt példány virtuális hálózatok közötti kapcsolat létrehozása. Ehhez a virtuális hálózathoz az Azure natív tanúsítványalapú hitelesítésének használatával pont – hely kapcsolat konfigurálása. További információkért lásd: [csatlakozni egy Azure SQL Database felügyelt példánya a helyi számítógépről egy pont – hely kapcsolat konfigurálása](sql-database-managed-instance-configure-p2s.md).

## <a name="connect-from-on-premises-with-vnet-peering"></a>Csatlakozás a másodlagos adatbázisoké pedig a virtuális hálózatok közötti társviszony
Ügyfelek által megvalósított egy másik helyzet lehet, ahol a VPN-átjáró telepítve van a különálló virtuális hálózat és a egy üzemeltetési felügyelt példányhoz az előfizetés. A két virtuális etworks majd társviszonyban állnak. A mintául szolgáló alábbi Architektúradiagram bemutatja, hogyan ez alkalmazásokon és szolgáltatásokon futtatható.

![Társviszony létesítése virtuális hálózatok között](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

Miután az alapszintű infrastruktúra készen áll, módosítania néhány beállítás a VPN-átjáró láthatják, az IP-címek a virtuális hálózat, amelyen a felügyelt példányhoz. Ehhez a következő módosításokat jellemző alatt a **társviszony-létesítés beállítások**.
1.  A virtuális hálózat, amelyen a VPN-átjáró, lépjen a **Társviszonyok**, majd a felügyelt példánynak társviszonyban álló virtuális hálózatok közötti kapcsolat, és kattintson **Átjárótranzit engedélyezése**.
2.  A virtuális hálózat, amelyen a felügyelt példány, lépjen a **Társviszonyok**, majd a VPN-átjáróhoz társviszonyban álló virtuális hálózatok közötti kapcsolat, és kattintson **távoli átjárók használata**.

## <a name="connect-an-azure-app-service-hosted-application"></a>Azure App Service szolgáltatásban üzemeltetett alkalmazások csatlakoztatása 

Így az Azure App Service-ben való eléréséhez először kell az alkalmazás és a felügyelt példány virtuális hálózatok közötti kapcsolat létrehozása csak egy privát IP-címen keresztül elérhető lesz a felügyelt példánynak. Lásd: [az alkalmazás integrálása az Azure-beli virtuális hálózathoz](../app-service/web-sites-integrate-with-vnet.md).  
 
Című témakörben [hibaelhárítási virtuális hálózatok és az alkalmazások](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Ha a kapcsolat nem jön, próbálja meg [a hálózati konfiguráció szinkronizálása](sql-database-managed-instance-sync-network-configuration.md). 
 
Csatlakozás az Azure App Service-ben egy felügyelt példányra egy különleges esetben akkor, ha integrált felügyelt példány virtuális hálózat társviszonyba Azure App Service-hálózathoz. Adott esetben szükséges, állítsa be a következő konfigurációt: 

- Felügyelt példány virtuális hálózati átjáró nem lehet  
- Felügyelt példány virtuális hálózathoz kell rendelkeznie a távoli átjárók használata a beállításhalmaz 
- Társviszonyban lévő virtuális hálózatok közötti engedélyezési átjáró átviteli beállítás kell rendelkeznie. 
 
Ebben a forgatókönyvben a következő ábra mutatja be:

![integrált alkalmazás társviszony-létesítés](./media/sql-database-managed-instance/integrated-app-peering.png)
 
## <a name="troubleshooting-connectivity-issues"></a>Csatlakozási problémák elhárítása

Csatlakozási problémák elhárítása, tekintse át a következőket:
- Ha Ön nem lehet csatlakozni egy Azure virtuális gépen belül a ugyanazon a Vneten, de különböző alhálózati felügyelt példányra, ellenőrizze, hogy van-e állítva a Virtuálisgép-alhálózatot, amelyek esetleg blokkolják a hozzáférést a hálózati biztonsági csoport. Továbbá vegye figyelembe, hogy meg kell nyitnia az 1433-as port SQL, valamint a portokat a kimenő kapcsolatot 11000-12000 tartományban, mivel azok szükségesek, például az Azure határain belül létesít átirányítás használatával csatlakozik. 
- Győződjön meg arról, hogy a BGP-propagálási értéke **engedélyezve** a Vnethez társított útvonaltáblához.
- Ha a P2S típusú VPN-kapcsolattal, ellenőrizze a konfigurációt, ha láthatja, hogy az Azure Portalon **bejövő/kimenő forgalom** számokat. Nem nulla értékű számok azt jelzi, hogy az Azure és-tárolókról a helyszíni irányítaná a forgalmat.

   ![bejövő/kimenő forgalom számok](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Ellenőrizze, hogy az ügyfél (futtató gép a VPN-ügyfél) rendelkezik-e az összes virtuális hálózatok eléréséhez szükséges útvonal bejegyzések. Az útvonalak tárolt `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`.


   ![Route.txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Ahogy az a képen, nincsenek két bejegyzést az egyes virtuális hálózatok és a egy harmadik bejegyzést a VPN-végpontnak konfigurált a portálon.

   Ellenőrizze az útvonalakat a másik módja, az alábbi parancs segítségével. A kimenet mutatja az útvonalakat a különböző alhálózatokhoz: 

   ```cmd
   C:\ >route print -4
   ===========================================================================
   Interface List
   14...54 ee 75 67 6b 39 ......Intel(R) Ethernet Connection (3) I218-LM
   57...........................rndatavnet
   18...94 65 9c 7d e5 ce ......Intel(R) Dual Band Wireless-AC 7265
   1...........................Software Loopback Interface 1
   Adapter===========================================================================
   
   IPv4 Route Table
   ===========================================================================
   Active Routes:
   Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.83.72.1     10.83.74.112     35
         10.0.0.0    255.255.255.0         On-link       172.26.34.2     43
     
         10.4.0.0    255.255.255.0         On-link       172.26.34.2     43
   ===========================================================================
   Persistent Routes:
   None
   ```

- Ha a virtuális hálózatok közötti társviszony használja, győződjön meg arról, hogy követte a következő útmutatót: beállítás [Átjárótranzit engedélyezése és a távoli átjárók használata](#connect-from-on-premises-with-vnet-peering). 

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
|SSMS   | 17.8.1 vagy [magasabb](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) |

## <a name="next-steps"></a>További lépések

- A felügyelt példányokkal kapcsolatos információért lásd: [Mi az a felügyelt példány?](sql-database-managed-instance.md)
- Bemutatja, hogyan hozzon létre egy új felügyelt példány oktatóanyagért lásd: [létrehoz egy felügyelt példányt](sql-database-managed-instance-get-started.md).
