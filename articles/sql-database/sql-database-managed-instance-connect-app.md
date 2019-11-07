---
title: Azure SQL Database felügyelt példányok összekapcsolására szolgáló alkalmazás
description: Ez a cikk azt ismerteti, hogyan csatlakoztatható az alkalmazás Azure SQL Database felügyelt példányhoz.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab, vanto
ms.date: 11/09/2018
ms.openlocfilehash: 5dc287ab58636b4921c14ae71f9a62ab36d9c596
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73688244"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Az alkalmazás összekötése Azure SQL Database felügyelt példánnyal

Ma már több lehetőség áll rendelkezésére az alkalmazás üzemeltetésének és helyének meghatározásakor.

Dönthet úgy is, hogy a felhőben futtatja az alkalmazást Azure App Service vagy egy Azure Virtual Network (VNet) integrált lehetőséggel, például a Azure App Service Environment, a virtuális géppel, a virtuálisgép-méretezési csoporttal. Hibrid felhőalapú megközelítést is használhat, és a helyszínen is megőrizheti alkalmazásait.

Bármit is megadhat, csatlakozhat egy felügyelt példányhoz.  

![magas rendelkezésre állás](./media/sql-database-managed-instance/application-deployment-topologies.png)

## <a name="connect-an-application-inside-the-same-vnet"></a>Alkalmazás összekötése ugyanazon a VNet belül

Ez a forgatókönyv a legegyszerűbb. A VNet belüli virtuális gépek közvetlenül is csatlakozhatnak egymáshoz, még akkor is, ha különböző alhálózatokon belül vannak. Ez azt jelenti, hogy mindössze annyit kell tennie, hogy az alkalmazást egy Azure-alkalmazási környezetben vagy virtuális gépen kell csatlakoztatnia, hogy megfelelően állítsa be a kapcsolati karakterláncot.  

## <a name="connect-an-application-inside-a-different-vnet"></a>Alkalmazás összekötése egy másik VNet belül

Ez a forgatókönyv egy kicsit összetettebb, mert a felügyelt példány saját IP-címmel rendelkezik saját VNet. A kapcsolódáshoz az alkalmazásnak hozzá kell férnie ahhoz a VNet, ahol a felügyelt példány telepítve van. Ezért először kapcsolatot kell létesítenie az alkalmazás és a felügyelt példány VNet között. A virtuális hálózatok nem kell ugyanabban az előfizetésben lennie ahhoz, hogy ez a forgatókönyv működjön.

A virtuális hálózatok csatlakoztatásának két lehetősége van:

- [Azure Virtual Network-társítás](../virtual-network/virtual-network-peering-overview.md)
- VNet – VNet VPN Gateway ([Azure Portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [POWERSHELL](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

A társítási lehetőség az előnyben részesített megoldás, mivel a Microsoft gerinc hálózatot használja, a kapcsolati perspektívából azonban nincs észrevehető különbség a virtuális gépek között a VNet és ugyanabban a VNet. A VNet-társítás az azonos régióban található hálózatokra korlátozódik.  

> [!IMPORTANT]
> A felügyelt példányok VNet-társítási forgatókönyve az azonos régióban található hálózatokra korlátozódik, a [globális Virtual Network](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)-társítás korlátai miatt. További részletekért tekintse meg az [Azure Virtual Networks – gyakori kérdések](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) című cikket is. 

## <a name="connect-an-on-premises-application"></a>Helyszíni alkalmazás összekapcsolása

A felügyelt példány csak magánhálózati IP-címen keresztül érhető el. A helyszíni környezetből való hozzáféréshez helyek közötti kapcsolatot kell létrehoznia az alkalmazás és a felügyelt példány VNet között.

Két lehetőség áll rendelkezésre a helyszíni és az Azure VNet közötti kapcsolódásra:

- Helyek közötti VPN-kapcsolat ([Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [POWERSHELL](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [ExpressRoute](../expressroute/expressroute-introduction.md) -kapcsolatok  

Ha a helyszíni és az Azure közötti kapcsolat sikeresen létrejött, és nem tud kapcsolatot létesíteni a felügyelt példánnyal, ellenőrizze, hogy a tűzfal rendelkezik-e nyitott kimenő kapcsolattal a 11000-11999 1433-es SQL-porton, valamint az átirányításhoz használt portok tartományán.

## <a name="connect-an-application-on-the-developers-box"></a>Alkalmazás összekapcsolása a Developers box-ban

A felügyelt példányok csak magánhálózati IP-címeken keresztül érhetők el, így a fejlesztői Box-ból való hozzáféréshez először kapcsolatot kell létesítenie a fejlesztői és a felügyelt példány VNet. Ehhez konfiguráljon egy pont – hely típusú kapcsolatokat egy VNet natív Azure tanúsítványalapú hitelesítés használatával. További információ: [pont – hely kapcsolat konfigurálása Azure SQL Database felügyelt példányhoz a helyszíni számítógépről való kapcsolódáshoz](sql-database-managed-instance-configure-p2s.md).

## <a name="connect-from-on-premises-with-vnet-peering"></a>Helyszíni kapcsolat VNet-társítással

Az ügyfelek által megvalósított egy másik forgatókönyv, amelyben a VPN-átjáró egy különálló virtuális hálózatban van telepítve, és egy, a felügyelt példányok egyikének előfizetése. Ezt követően a két virtuális hálózat összevonásra kerül. A következő minta architektúra-diagram bemutatja, hogyan valósítható meg ez a megoldás.

![Virtuális hálózatok közötti társviszony](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

Miután beállította az alapszintű infrastruktúrát, módosítania kell néhány beállítást, hogy a VPN Gateway láthassa a felügyelt példányt futtató virtuális hálózat IP-címeit. Ehhez végezze el a következő nagyon specifikus módosításokat a társítási **Beállítások**alatt.

1. A VPN-átjárót futtató VNet **válassza a társítások, majd**a felügyelt példányhoz tartozó VNet-kapcsolat lehetőséget, majd kattintson az **átjáró-átvitel engedélyezése**lehetőségre.
2. A felügyelt példányt futtató VNet **válassza a társítások, majd**a VPN Gateway társ VNet-kapcsolatot, majd kattintson a **távoli átjárók használata**elemre.

## <a name="connect-an-azure-app-service-hosted-application"></a>Azure App Service üzemeltetett alkalmazás összekötése

A felügyelt példányok csak a magánhálózati IP-címeken keresztül érhetők el, így ahhoz, hogy hozzáférhessenek a Azure App Service az alkalmazás és a felügyelt példány VNet közötti kapcsolatot kell létrehoznia. Lásd: [az alkalmazás integrálása Azure-Virtual Networkokkal](../app-service/web-sites-integrate-with-vnet.md).  

Hibaelhárítást a [virtuális hálózatok és az alkalmazások hibaelhárítása](../app-service/web-sites-integrate-with-vnet.md#troubleshooting)című témakörben talál. Ha nem létesíthető kapcsolat, próbálja meg [szinkronizálni a hálózati konfigurációt](sql-database-managed-instance-sync-network-configuration.md).

A Azure App Service felügyelt példányhoz való csatlakoztatásának különleges esete az, amikor integrált Azure App Service a felügyelt példányok VNet a hálózatba. Ebben az esetben a következő konfigurációt kell beállítani:

- A felügyelt példány VNet nem rendelkezhet átjáróval  
- A felügyelt példány VNet rendelkeznie kell a távoli átjárók használata beállítással
- A társ-VNet engedélyezni kell az átjáró átviteli beállítását

Ezt a forgatókönyvet a következő ábra szemlélteti:

![integrált alkalmazás-társítás](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>A VNet integrációs funkciója nem integrálja az alkalmazást olyan VNet, amely ExpressRoute-átjáróval rendelkezik. Még akkor is, ha az ExpressRoute-átjáró párhuzamosan van konfigurálva, a VNet-integráció nem működik. Ha egy ExpressRoute-kapcsolaton keresztül kell hozzáférni az erőforrásokhoz, használhat egy App Service Environment, amely a VNet fut.

## <a name="troubleshooting-connectivity-issues"></a>Kapcsolódási problémák elhárítása

A kapcsolódási problémák elhárításához tekintse át a következőket:

- Ha nem tud csatlakozni a felügyelt példányhoz egy olyan Azure-beli virtuális gépről, amely ugyanabban a VNet, de eltérő alhálózatban található, ellenőrizze, hogy van-e olyan hálózati biztonsági csoport beállítva a virtuálisgép-alhálózaton, amely blokkolja a hozzáférést. Azt is vegye figyelembe, hogy a 1433-es SQL-porton és a 11000-11999-es tartományon belüli portokon is meg kell nyitnia a kimenő kapcsolatot, mivel ezek az Azure-határon belül történő átirányítás esetén szükségesek.
- Győződjön meg arról, hogy a BGP-propagálás **engedélyezve** van a VNet társított útválasztási táblázathoz.
- Ha P2S VPN-t használ, ellenőrizze a konfigurációt a Azure Portalban, hogy megjelenjen-e a **bejövő/kimenő** számok. A nullától eltérő számok azt jelzik, hogy az Azure átirányítja a forgalmat a helyszíni vagy a szolgáltatásba.

   ![bejövő/kimenő számok](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Győződjön meg arról, hogy az ügyfélszámítógép (amely a VPN-ügyfelet futtatja) útválasztási bejegyzéseket tartalmaz az összes elérni kívánt virtuális hálózatok. Az útvonalak tárolása `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`történik.

   ![Route. txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Ahogy az ebben a képen is látható, két bejegyzés van az érintett VNet, valamint a portálon konfigurált VPN-végpont harmadik bejegyzése.

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

- VNet-társítás használata esetén győződjön meg arról, hogy követte az [átjáró engedélyezése és a távoli átjárók használata](#connect-from-on-premises-with-vnet-peering)beállításra vonatkozó utasításokat.

## <a name="required-versions-of-drivers-and-tools"></a>Az illesztőprogramok és az eszközök szükséges verziói

Az eszközök és illesztőprogramok következő minimális verziói ajánlottak, ha a felügyelt példányhoz szeretne csatlakozni:

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

- A felügyelt példányokkal kapcsolatos információért lásd: [Mi az a felügyelt példány?](sql-database-managed-instance.md)
- Az új felügyelt példányok létrehozásával kapcsolatos oktatóanyagért lásd: [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md).
