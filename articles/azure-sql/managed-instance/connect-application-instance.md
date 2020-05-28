---
title: Alkalmazás összekötése felügyelt példánnyal
titleSuffix: Azure SQL Managed Instance
description: Ez a cikk azt ismerteti, hogyan csatlakoztatható az alkalmazás az Azure SQL felügyelt példányához.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab, vanto
ms.date: 11/09/2018
ms.openlocfilehash: 051d589ec13c1fa8642701fe94a361e1dfbe4aab
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84044387"
---
# <a name="connect-your-application-to-azure-sql-managed-instance"></a>Az alkalmazás összekötése az Azure SQL felügyelt példányával
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ma már több lehetőség áll rendelkezésére az alkalmazás üzemeltetésének és helyének meghatározásakor.

Dönthet úgy is, hogy a felhőben futtatja az alkalmazást Azure App Service vagy egy Azure virtuális hálózat (Virtual Network) integrált beállításai (például Azure App Service Environment, virtuális gép, virtuálisgép-méretezési csoport) használatával. Hibrid felhőalapú megközelítést is használhat, és a helyszínen is megőrizheti alkalmazásait.

Bármilyen választási lehetőséget igénybe vehet, csatlakozhat egy SQL felügyelt példányhoz. 

![magas rendelkezésre állás](./media/connect-application-instance/application-deployment-topologies.png)

Ez a cikk azt ismerteti, hogyan csatlakoztatható egy alkalmazás az Azure SQL felügyelt példányához számos különböző alkalmazási forgatókönyvben. 

## <a name="connect-inside-the-same-vnet"></a>Kapcsolat ugyanazon a VNet belül

Egy alkalmazás csatlakoztatása ugyanazon a virtuális hálózaton belül, ahol az SQL felügyelt példánya a legegyszerűbb forgatókönyv. A virtuális hálózaton belüli virtuális gépek közvetlenül is csatlakozhatnak egymáshoz, még akkor is, ha különböző alhálózatokon belül vannak. Ez azt jelenti, hogy mindössze annyit kell tennie, hogy az alkalmazást egy Azure-alkalmazási környezetben vagy virtuális gépen kell csatlakoztatnia, hogy megfelelően állítsa be a kapcsolati karakterláncot.  

## <a name="connect-inside-a-different-vnet"></a>Kapcsolat egy másik VNet belül

Ha egy olyan alkalmazást csatlakoztat, amely egy másik virtuális hálózaton belül található, mert az SQL felügyelt példánya egy kicsit összetettebb, mert az SQL felügyelt példány magánhálózati IP-címmel rendelkezik a saját virtuális hálózatában. A kapcsolódáshoz az alkalmazásnak hozzá kell férnie ahhoz a virtuális hálózathoz, amelyen az SQL felügyelt példánya telepítve van. Ezért kapcsolatot kell létesítenie az alkalmazás és az SQL felügyelt példányának virtuális hálózata között. A virtuális hálózatoknak nem kell ugyanabban az előfizetésben lenniük ahhoz, hogy ez a forgatókönyv működjön.

A virtuális hálózatok összekapcsolásának két lehetősége van:

- [Azure VPN-partneri kapcsolat](../../virtual-network/virtual-network-peering-overview.md)
- VNet – VNet VPN Gateway: ([Azure Portal](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [POWERSHELL](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

A társítás előnyben részesített, mivel a Microsoft gerinc hálózatot használja, így a kapcsolati szempontból nem észlelhető különbség a virtuális gépek és az ugyanazon virtuális hálózat közötti késések között. A virtuális hálózat társítása az azonos régióban található hálózatokra korlátozódik.  

> [!IMPORTANT]
> Az SQL felügyelt példányának virtuális hálózati társítási forgatókönyve az azonos régióban található hálózatokra korlátozódik, a [ofGlobal megkötések miatt Virtual Network](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). További részletekért tekintse meg az [Azure Virtual Networks – gyakori kérdések](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) című cikket is. 

## <a name="connect-from-on-premises"></a>Helyi kapcsolat 

A helyszíni alkalmazást a felügyelt SQL-példányhoz is összekapcsolhatja. Az SQL felügyelt példánya csak magánhálózati IP-címen keresztül érhető el. A helyszíni környezetből való hozzáféréshez helyek közötti kapcsolatot kell létrehozni az alkalmazás és az SQL felügyelt példányának virtuális hálózata között.

A helyszíni és az Azure-beli virtuális hálózat közötti kapcsolat két lehetőség közül választhat:

- Helyek közötti VPN-kapcsolat ([Azure Portal](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [POWERSHELL](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [ExpressRoute](../../expressroute/expressroute-introduction.md) -kapcsolatok  

Ha a helyszíni és az Azure közötti kapcsolat sikeresen létrejött, és nem tud kapcsolatot létesíteni az SQL felügyelt példányával, ellenőrizze, hogy a tűzfal rendelkezik-e nyitott kimenő kapcsolattal a 1433-es SQL-porton 11000-11999, valamint az átirányításhoz használt portok tartományát.

## <a name="connect-the-developers-box"></a>A Developers Box összekötése

A fejlesztői Box is csatlakoztatható az SQL felügyelt példányához. A felügyelt SQL-példányok csak magánhálózati IP-címeken keresztül érhetők el, így a fejlesztői Box-ból való hozzáféréshez először kapcsolatot kell létesítenie a fejlesztői és az SQL felügyelt példány virtuális hálózata között. Ehhez konfiguráljon egy pont – hely kapcsolatot egy virtuális hálózathoz natív Azure tanúsítványalapú hitelesítés használatával. További információ: [pont – hely kapcsolat konfigurálása az Azure SQL felügyelt példányhoz a helyszíni számítógépről való kapcsolódáshoz](point-to-site-p2s-configure.md).

## <a name="connect-with-vnet-peering"></a>VNet-társítással való kapcsolat

Az ügyfelek által megvalósított egy másik forgatókönyv, ahol a VPN-átjáró egy különálló virtuális hálózatban van telepítve, és az SQL felügyelt példányának egyik üzemeltetése az előfizetés. Ezt követően a két virtuális hálózat összevonásra kerül. A következő minta architektúra-diagram bemutatja, hogyan valósítható meg ez a megoldás.

![virtuális hálózati társítás](./media/connect-application-instance/vnet-peering.png)

Miután beállította az alapszintű infrastruktúrát, módosítania kell néhány beállítást, hogy a VPN Gateway láthassa az SQL felügyelt példányt futtató virtuális hálózat IP-címeit. Ehhez végezze el a következő nagyon specifikus módosításokat a társítási **Beállítások**alatt.

1. A VPN-átjárót futtató virtuális hálózaton **válassza a társítások, majd**az SQL felügyelt példány társ virtuális hálózati kapcsolat lehetőséget, majd kattintson az **átjáró**átirányítása lehetőségre.
2. A felügyelt SQL-példányt futtató virtuális hálózaton **válassza a társítások, majd**a VPN Gateway társ virtuális hálózati kapcsolatok lehetőséget, majd kattintson a **távoli átjárók használata**elemre.

## <a name="connect-azure-app-service"></a>Azure App Service összekötése 

A Azure App Service által üzemeltetett alkalmazások is csatlakoztathatók. A felügyelt SQL-példányok csak magánhálózati IP-címeken keresztül érhetők el, így ahhoz, hogy hozzáférhessenek a Azure App Service az alkalmazás és az SQL felügyelt példány virtuális hálózata közötti kapcsolatot kell létrehoznia. Lásd: [az alkalmazás integrálása Azure-Virtual Networkokkal](../../app-service/web-sites-integrate-with-vnet.md).  

Hibaelhárítást a [virtuális hálózatok és alkalmazások hibaelhárítása](../../app-service/web-sites-integrate-with-vnet.md#troubleshooting)című témakörben talál. Ha nem létesíthető kapcsolat, próbálja meg [szinkronizálni a hálózati konfigurációt](azure-app-sync-network-configuration.md).

A Azure App Service SQL felügyelt példányhoz való csatlakoztatásának különleges esete, ha a Azure App Service integrálása az SQL felügyelt példányok virtuális hálózatához kapcsolódó hálózatra történik. Ebben az esetben a következő konfigurációt kell beállítani:

- Az SQL felügyelt példányának virtuális hálózata nem rendelkezhet átjáróval  
- A felügyelt SQL-példány virtuális hálózatának rendelkeznie kell `Use remote gateways` beállításhalmaz
- A társ virtuális hálózatnak engedélyezve kell lennie az átjáró átviteli beállításának

Ezt a forgatókönyvet a következő ábra szemlélteti:

![integrált alkalmazás-társítás](./media/connect-application-instance/integrated-app-peering.png)

>[!NOTE]
>A virtuális hálózat integrációs funkciója nem integrál egy alkalmazást olyan virtuális hálózattal, amely rendelkezik ExpressRoute-átjáróval. Még akkor is, ha az ExpressRoute-átjáró párhuzamosan van konfigurálva, a virtuális hálózat integrációja nem működik. Ha egy ExpressRoute-kapcsolaton keresztül kell hozzáférni az erőforrásokhoz, használhat egy App Service Environment, amely a virtuális hálózaton fut.

## <a name="troubleshooting-connectivity-issues"></a>Kapcsolati problémák hibaelhárítása

A kapcsolódási problémák elhárításához tekintse át a következőket:

- Ha nem tud csatlakozni az SQL felügyelt példányához egy ugyanazon a virtuális hálózaton belül található Azure-beli virtuális gépről, de más alhálózatból, akkor ellenőrizze, hogy van-e olyan hálózati biztonsági csoport beállítva a virtuálisgép-alhálózaton, amely blokkolja a hozzáférést. Emellett nyissa meg a kimenő kapcsolatot a 1433-es SQL-porton, valamint a 11000-11999-es tartományban található portokat, mivel ezek szükségesek az Azure határán belüli átirányítás útján történő kapcsolódáshoz.
- Győződjön meg arról, hogy a BGP-propagálás **engedélyezve** van a virtuális hálózathoz társított útválasztási táblázathoz.
- Ha P2S VPN-t használ, ellenőrizze a konfigurációt a Azure Portalban, hogy megjelenjen-e a **bejövő/kimenő** számok. A nullától eltérő számok azt jelzik, hogy az Azure átirányítja a forgalmat a helyszíni vagy a szolgáltatásba.

   ![bejövő/kimenő számok](./media/connect-application-instance/ingress-egress-numbers.png)

- Győződjön meg arról, hogy az ügyfélszámítógép (amely a VPN-ügyfelet futtatja) útválasztási bejegyzéseket tartalmaz az összes elérni kívánt virtuális hálózathoz. Az útvonalak a-ben tárolódnak `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt` .

   ![Route. txt](./media/connect-application-instance/route-txt.png)

   Ahogy az a képen is látható, két bejegyzés van az érintett virtuális hálózatokhoz, valamint a portálon konfigurált VPN-végpont harmadik bejegyzése.

   Az útvonalak vizsgálatának másik módja a következő parancson keresztül történik. A kimenet a különböző alhálózatokhoz tartozó útvonalakat mutatja:

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

- Ha virtuális hálózati kapcsolatot használ, győződjön meg arról, hogy követte az [átjáró engedélyezése és a távoli átjárók használata](#connect-from-on-premises)beállításra vonatkozó utasításokat.

- Ha virtuális hálózati társítást használ egy Azure App Service üzemeltetett alkalmazáshoz való kapcsolódáshoz, és az SQL felügyelt példány virtuális hálózata nyilvános IP-címtartományt tartalmaz, győződjön meg arról, hogy az üzemeltetett alkalmazás beállításai lehetővé teszik a kimenő forgalom átirányítását a nyilvános IP-hálózatokra. Kövesse a [regionális virtuális hálózatok integrációjának](../../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)utasításait.

## <a name="required-versions-of-drivers-and-tools"></a>Az illesztőprogramok és az eszközök szükséges verziói

Az eszközök és illesztőprogramok következő minimális verziói ajánlottak, ha csatlakozni szeretne az SQL felügyelt példányához:

| Illesztőprogram/eszköz | Verzió |
| --- | --- |
|.NET-keretrendszer | 4.6.1 (vagy .NET Core) |
|ODBC-illesztő| v17 |
|PHP-illesztő| 5.2.0 |
|JDBC-illesztő| 6.4.0 |
|Node.js-illesztő| 2.1.1 |
|OLEDB-illesztő| 18.0.2.0 |
|SSMS| 18,0 vagy [újabb](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) vagy újabb |

## <a name="next-steps"></a>További lépések

- További információ az SQL felügyelt példányáról: [Mi az SQL felügyelt példánya?](sql-managed-instance-paas-overview.md).
- Az új, felügyelt SQL-példányok létrehozását bemutató oktatóanyagért lásd: [SQL felügyelt példány létrehozása](instance-create-quickstart.md).
