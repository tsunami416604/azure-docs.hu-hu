---
title: Felügyelt példányösszekötő alkalmazás
description: Ez a cikk ismerteti, hogyan csatlakoztathatja az alkalmazást az Azure SQL Database felügyelt példány.
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
ms.openlocfilehash: 8d920fb7815e5a9fe30d8f3b4e40f36133d83222
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538086"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Alkalmazás csatlakoztatása felügyelt Azure SQL Database-példányhoz

Ma már több választási lehetőség, amikor eldönti, hogyan és hol a fogadó az alkalmazás.

Dönthet úgy, hogy az azure App Service használatával vagy az Azure virtuális hálózati (VNet) integrált beállításaival, például az Azure App Service Environment, a Virtual Machine, a Virtual Machine Scale Set használatával üzemelteti az alkalmazást a felhőben. A hibrid felhőalapú megközelítést is igénybe veheti, és az alkalmazásokat a helyszínen tarthatja.

Bármilyen döntést is hozott, csatlakoztathatja egy felügyelt példányhoz.  

![magas rendelkezésre állás](./media/sql-database-managed-instance/application-deployment-topologies.png)

## <a name="connect-an-application-inside-the-same-vnet"></a>Alkalmazás csatlakoztatása ugyanazon a virtuális hálózaton belül

Ez a forgatókönyv a legegyszerűbb. A virtuális hálózaton belüli virtuális gépek közvetlenül csatlakozhatnak egymáshoz, még akkor is, ha különböző alhálózatokon belül vannak. Ez azt jelenti, hogy az Azure-alkalmazáskörnyezetben vagy virtuális gépen belüli alkalmazás csatlakoztatásához mindössze annyit kell csatlakoztatnia, hogy megfelelően állítsa be a kapcsolati karakterláncot.  

## <a name="connect-an-application-inside-a-different-vnet"></a>Alkalmazás csatlakoztatása egy másik virtuális hálózaton belül

Ez a forgatókönyv egy kicsit összetettebb, mert a felügyelt példány saját virtuális hálózatában rendelkezik privát IP-címmel. A csatlakozáshoz az alkalmazásnak hozzáférésre van szüksége a virtuális hálózathoz, ahol a felügyelt példány telepítve van. Így először létre kell hoznia egy kapcsolatot az alkalmazás és a felügyelt példány virtuális hálózata között. A virtuális hálózatok nem kell ugyanabban az előfizetésben ahhoz, hogy ez a forgatókönyv működjön.

A virtuális hálózatok csatlakoztatásának két lehetősége van:

- [Az Azure virtuális hálózat társviszony-létesítése](../virtual-network/virtual-network-peering-overview.md)
- VNet-vNet VPN átjáró ([Azure portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

A társviszony-létesítési lehetőség az előnyösebb, mert a társviszony-létesítés a Microsoft gerinchálózatát használja, így a kapcsolat szempontjából nincs észrevehető különbség a társviszony-létesített virtuális hálózatban és ugyanabban a virtuális hálózatban lévő virtuális gépek közötti késésben. A virtuális hálózatok társviszony-létesítése az ugyanabban a régióban lévő hálózatokra korlátozódik.  

> [!IMPORTANT]
> A felügyelt példány virtuális hálózati társviszony-létesítési forgatókönyve [a globális virtuális hálózati társviszony-létesítés korlátai](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)miatt ugyanabban a régióban lévő hálózatokra korlátozódik. További részletekért tekintse meg az [Azure virtuális hálózatok gyakori kérdések](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) című cikkének vonatkozó szakaszát. 

## <a name="connect-an-on-premises-application"></a>Helyszíni alkalmazás csatlakoztatása

A felügyelt példány csak privát IP-címen keresztül érhető el. Annak érdekében, hogy a helyszíni elérése érdekében, az alkalmazás és a felügyelt példány virtuális hálózat közötti kapcsolat létrehozása szükséges.

A helyszíni azure-beli virtuális hálózathoz való csatlakozás két lehetősége van:

- Helyek közötti VPN-kapcsolat ([Azure portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [ExpressRoute-kapcsolat](../expressroute/expressroute-introduction.md)  

Ha sikeresen létrehozott helyszíni Azure-kapcsolatot, és nem tud kapcsolatot létesíteni a felügyelt példánylal, ellenőrizze, hogy a tűzfal nyitott kimenő kapcsolattal rendelkezik-e az 1433-as SQL-porton, valamint az 11000-11999 porttartományát átirányításra.

## <a name="connect-an-application-on-the-developers-box"></a>Alkalmazás csatlakoztatása a fejlesztői felületen

A felügyelt példány csak egy privát IP-címen érhető el, így ahhoz, hogy a fejlesztői mezőből való eléréséhez először kapcsolatot kell létesítenie a fejlesztői mező és a felügyelt példány virtuális hálózata között. Ehhez konfigurálja a point-to-site kapcsolatot egy virtuális hálózattal natív Azure-tanúsítvány hitelesítés használatával. További információt a [Point-to-Site kapcsolat konfigurálása az Azure SQL Database felügyelt példányhoz helyszíni számítógépről való csatlakozáshoz](sql-database-managed-instance-configure-p2s.md)című témakörben talál.

## <a name="connect-from-on-premises-with-vnet-peering"></a>Csatlakozás a helyszíni virtuális hálózatról való társviszony-létesítéssel

Az ügyfelek által megvalósított másik forgatókönyv az, ahol a VPN-átjáró egy külön virtuális hálózatban és egy előfizetésben van telepítve az egyik üzemeltetésű felügyelt példányból. A két virtuális hálózat ezután társviszonyba. A következő mintaarchitektúra-diagram bemutatja, hogyan valósítható meg.

![Virtuális hálózatok közötti társviszony](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

Miután beállította az alapvető infrastruktúrát, módosítania kell néhány beállítást, hogy a VPN-átjáró láthassa a felügyelt példányt tartalmazó virtuális hálózat IP-címeit. Ehhez hajtsa végre a következő nagyon speciális módosításokat a **Társviszony-létesítési beállítások**alatt.

1. A VPN-átjárót tároló virtuális hálózatban nyissa meg a **Társviszony-létesítési**, majd a felügyelt példány társviszony-létesített virtuális hálózati kapcsolatát, majd kattintson az **Átjáróátvitel engedélyezése**elemre.
2. A felügyelt példányt tartalmazó virtuális hálózatban nyissa meg a **Társviszony-létesítési**, majd a VPN-átjáró társviszony-létesítési virtuális hálózatát, majd kattintson a **Távoli átjárók használata**elemre.

## <a name="connect-an-azure-app-service-hosted-application"></a>Az Azure App Service által üzemeltetett alkalmazás csatlakoztatása

A felügyelt példány csak egy privát IP-címen keresztül érhető el, így az Azure App Service-ből való eléréséhez először kapcsolatot kell létesítenie az alkalmazás és a felügyelt példány virtuális hálózata között. Lásd: [Az alkalmazás integrálása egy Azure virtuális hálózattal.](../app-service/web-sites-integrate-with-vnet.md)  

A hibaelhárításról a [Virtuális hálózatok és alkalmazások hibaelhárítása című témakörben lehet.](../app-service/web-sites-integrate-with-vnet.md#troubleshooting) Ha nem hozható létre kapcsolat, próbálja meg [szinkronizálni a hálózati konfigurációt.](sql-database-managed-instance-sync-network-configuration.md)

Az Azure App Service felügyelt példányhoz való csatlakoztatásának egy különleges esete, amikor integrálta az Azure App Service-t egy felügyelt példány virtuális hálózatba társviszonyba adott hálózatba. Ebben az esetben a következő konfigurációt kell beállítani:

- A felügyelt példány virtuális hálózatának NEM lehet átjárója  
- A felügyelt példány virtuális hálózatának rendelkeznie kell a Távoli átjárók használata beállítással
- A társviszonyba állított virtuális hálózatnak rendelkeznie kell az átjáró átvitelének engedélyezése beállítással

Ezt a forgatókönyvet az alábbi ábra szemlélteti:

![integrált alkalmazástársviszony-létesítés](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>A Virtuálishálózat-integráció szolgáltatás nem integrálja az alkalmazást egy ExpressRoute-átjáróval rendelkező virtuális hálózattal. Még akkor is, ha az ExpressRoute-átjáró együttélési módban van konfigurálva, a virtuális hálózat-integráció nem működik. Ha egy ExpressRoute-kapcsolaton keresztül kell hozzáférnie az erőforrásokhoz, akkor használhatja az App Service-környezetet, amely a virtuális hálózaton fut.

## <a name="troubleshooting-connectivity-issues"></a>Kapcsolati problémák hibaelhárítása

A kapcsolódási problémák elhárításához olvassa el az alábbi témakört:

- Ha nem tud csatlakozni a felügyelt példányegy Azure virtuális gép ugyanazon a virtuális hálózaton belül, de különböző alhálózaton belül, ellenőrizze, hogy van-e hálózati biztonsági csoport beállítása a virtuális gép alhálózat, amely blokkolja a hozzáférést. Emellett vegye figyelembe, hogy meg kell nyitnia a kimenő kapcsolatot az 1433-as SQL-porton, valamint az 11000-11999 tartományban lévő portokat, mivel ezek szükségesek az Azure-határon belüli átirányításon keresztül történő csatlakozáshoz.
- Győződjön meg arról, hogy a BGP propagálása **engedélyezve** van a virtuális hálózathoz társított útvonaltáblához.
- Ha P2S VPN-t használ, ellenőrizze a konfigurációt az Azure Portalon, hogy látja-e **a bejövő/kimenő** számokat. A nulla szám azt jelzi, hogy az Azure a helyszíni forgalom-/-innen irányuló forgalmat irányítja.

   ![be- és kilépési számok](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Ellenőrizze, hogy az ügyfélgép (amely a VPN-ügyfelet futtatja) rendelkezik-e útvonalbejegyzésekkel az összes elérhető virtuális hálózathoz. Az útvonalakat a `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`tárolják a ban.

   ![útvonal.txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Ahogy az ezen a képen látható, két bejegyzés minden egyes érintett virtuális hálózat, és egy harmadik bejegyzés a VPN-végpont, amely a portálon konfigurálva van.

   Az útvonalak ellenőrzésének másik módja a következő parancs. A kimenet a különböző alhálózatokhoz vezető útvonalakat jeleníti meg:

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

- Virtuális hálózati társviszony-létesítés használata esetén győződjön meg arról, hogy betartotta az [Átjáró-átvitel engedélyezése és a távoli átjárók használata](#connect-from-on-premises-with-vnet-peering)beállítására vonatkozó utasításokat.

- Ha virtuális hálózati társviszony-létesítés használatával csatlakozik egy Azure App Service üzemeltetett alkalmazás, és a felügyelt példány virtuális hálózat rendelkezik egy nyilvános IP-címtartomány, győződjön meg arról, hogy a hosztött alkalmazás beállításai lehetővé teszik a kimenő forgalom a nyilvános IP-hálózatokra. Kövesse a [regionális virtuális hálózatok integrációja](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)című útmutatóutasításait.

## <a name="required-versions-of-drivers-and-tools"></a>Az illesztőprogramok és eszközök szükséges verziói

Ha a Felügyelt példányhoz szeretne csatlakozni, az eszközök és illesztőprogramok alábbi minimális verziói ajánlottak:

| Illesztőprogram/eszköz | Verzió |
| --- | --- |
|.NET-keretrendszer | 4.6.1 (vagy .NET Core) |
|ODBC-illesztő| v17 |
|PHP-illesztő| 5.2.0 |
|JDBC-illesztő| 6.4.0 |
|Node.js-illesztő| 2.1.1 |
|OLEDB-illesztő| 18.0.2.0 |
|SSMS| 18.0 vagy [újabb](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) vagy magasabb |

## <a name="next-steps"></a>További lépések

- A felügyelt példányokkal kapcsolatos információért lásd: [Mi az a felügyelt példány?](sql-database-managed-instance.md)
- Az új felügyelt példány okainak létrehozásáról a [Felügyelt példány létrehozása című](sql-database-managed-instance-get-started.md)témakörben t.
