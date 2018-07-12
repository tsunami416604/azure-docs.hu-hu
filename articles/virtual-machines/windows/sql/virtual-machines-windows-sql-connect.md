---
title: Csatlakozás az SQL Server virtuális géphez (Resource Manager) |} A Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat az Azure-beli virtuális gépen futó SQL Server. Ez a témakör a klasszikus üzemi modellt használja. A forgatókönyvek eltér attól függően, a hálózati konfigurációt, és az ügyfél helyét.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: jroth
ms.openlocfilehash: 522ece2528e43c1037dc6bb707201ecda8074dd9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38705962"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Csatlakozhat egy SQL Server virtuális gépet az Azure-ban

## <a name="overview"></a>Áttekintés

Ez a témakör azt ismerteti, hogyan csatlakozhat egy Azure virtuális gépen futó SQL Server-példányhoz. Áttekint néhány [általános kapcsolódási forgatókönyvek](#connection-scenarios) majd [kapcsolat beállításait. a portálon lépések](#change). Ha szeretne hibaelhárítása vagy a portálon kívül-kapcsolat konfigurálása, lásd: a [manuális konfigurációs](#manual) Ez a témakör végén található. 

Ha szeretné inkább kiépítést és a kapcsolat teljes körű útmutatót szeretne, olvassa el [kiépítése egy SQL Server virtuális gépet az Azure-ban](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Kapcsolódási forgatókönyvek

Az ügyfél csatlakozik a virtuális gépen futó SQL Server módja az ügyfél és a hálózati konfiguráció helyétől függően eltér.

Ha egy SQL Server virtuális Gépet az Azure Portalon üzembe helyez, lehetősége van a típusát megadó **SQL-kapcsolat**.

![Üzembe helyezés során nyilvános SQL kapcsolati lehetőséget](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

A hálózati kapcsolatot a lehetőségek a következők:

| Beállítás | Leírás |
|---|---|
| **Public** | Csatlakozás az SQL Serverhez az interneten keresztül |
| **Privát** | Csatlakozás az SQL Serverhez az azonos virtuális hálózatba |
| **helyi** | Csatlakozás az SQL Server helyi ugyanazon a virtuális gépen | 

Az alábbi szakaszok ismertetik a **nyilvános** és **privát** lehetőségek részletes.

## <a name="connect-to-sql-server-over-the-internet"></a>Csatlakozás az SQL Serverhez az interneten keresztül

Ha az internetről az SQL Server adatbázismotorhoz csatlakozni, jelölje be **nyilvános** számára a **SQL-kapcsolat** írja be a portálon a kiépítés során. A portál automatikusan hajtja végre az alábbi lépéseket:

* Lehetővé teszi, hogy a TCP/IP protokollt az SQL Serverhez.
* Nyissa meg az SQL Server TCP port (alapértelmezés szerint 1433) tűzfalszabály konfigurálása.
* Lehetővé teszi, hogy az SQL Server-hitelesítés, nyilvános hozzáférés szükséges.
* Konfigurálja a hálózati biztonsági csoportot a virtuális gép összes TCP-forgalom az SQL Server port.

> [!IMPORTANT]
> A virtuálisgép-lemezképek esetében az SQL Server Developer és Express kiadásaihoz nem engedélyezi automatikusan a TCP/IP-protokollt. A Developer és Express kiadásokhoz kell használnia az SQL Server Configuration Manager használatával [manuálisan engedélyezze a TCP/IP protokollt](#manualtcp) a virtuális gép létrehozása után.

Internet-hozzáféréssel rendelkező bármely ügyfél vagy a virtuális gép nyilvános IP-címét vagy az IP-cím rendelve bármilyen DNS-címke megadásával csatlakozhat az SQL Server-példány. Ha az SQL Server portja 1433-as, nem kell adja meg azt a kapcsolati karakterláncban. Az alábbi kapcsolati karakterláncot a DNS-címke, az SQL virtuális gép csatlakozik `sqlvmlabel.eastus.cloudapp.azure.com` SQL-hitelesítéssel (is használhatja a nyilvános IP-cím).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Ez lehetővé teszi a kapcsolat az ügyfelek az interneten keresztül, bár ez nem jelenti azt, hogy bárki csatlakozhat az SQL Server. Kívüli ügyfelek számára a helyes felhasználónévvel és jelszóval rendelkezik. A fokozott biztonság érdekében elkerülhetők a jól ismert 1433-as porton. Például ha az SQL Server port 1500 és megalapozott megfelelő tűzfal- és hálózati biztonsági csoport szabályai a konfigurált, összekapcsolhatja fűzze a portnak a számát, a kiszolgáló nevét. Az alábbi példa módosítja az előzőre, egy egyéni portszám hozzáadásával **1500**, az a kiszolgáló nevét:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Az interneten keresztül lekérdezheti, ha az SQL Server virtuális Gépen, ha van-e az Azure-adatközpontba a kimenő adatok teljes normál vonatkoznak [kimenő adatátvitelek díjszabását](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Csatlakozás az SQL Serverhez egy virtuális hálózaton belül

Ha úgy dönt **privát** számára a **SQL-kapcsolat** írja be az Azure Portalon konfigurálja a beállításai megegyeznek a legtöbb **nyilvános**. Az egyetlen különbség, hogy nincs hálózati biztonsági csoportra vonatkozó szabályt engedélyezi a külső forgalmat, az SQL Server portja (alapértelmezés szerint 1433) az nem létezik.

> [!IMPORTANT]
> A virtuálisgép-lemezképek esetében az SQL Server Developer és Express kiadásaihoz nem engedélyezi automatikusan a TCP/IP-protokollt. A Developer és Express kiadásokhoz kell használnia az SQL Server Configuration Manager használatával [manuálisan engedélyezze a TCP/IP protokollt](#manualtcp) a virtuális gép létrehozása után.

A privát kapcsolatok gyakran használják együtt [virtuális hálózat](../../../virtual-network/virtual-networks-overview.md), amely lehetővé teszi számos forgatókönyv. Virtuális gépek csatlakoztathatja ugyanahhoz a virtuális hálózathoz, még akkor is, ha eltérő erőforráscsoportokban található virtuális gépeken. És a egy [site-to-site VPN](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), létrehozhat egy hibrid architektúra, amely a virtuális gépekhez kapcsolódik a helyszíni hálózatok és a gépek.

Virtuális hálózatok lehetővé teszi az Azure-beli virtuális gépek tartományhoz csatlakoztatására. Ez az egyetlen módja az SQL Server Windows-hitelesítés használatára. A más helyzetekben a felhasználónevek és jelszavak SQL-hitelesítés szükséges.

Feltételezve, hogy a virtuális hálózaton konfigurálta a DNS, csatlakozhat az SQL Server-példányhoz az SQL Server rendszerű virtuális gép számítógép nevét a kapcsolati karakterlánc megadásával. Az alábbi példában is feltételezi, hogy Windows-hitelesítést is lett konfigurálva, és, hogy a felhasználó kapott hozzáférést az SQL Server-példányt.

```
Server=mysqlvm;Integrated Security=true
```

## <a id="change"></a> Az SQL csatlakozási beállításainak módosítása

Az SQL Server virtuális gép az Azure Portalon módosíthatja a csatlakozási beállításokat.

1. Az Azure Portalon válassza ki a **virtuális gépek**.

2. Válassza ki az SQL Server virtuális gép.

3. A **beállítások**, kattintson a **SQL Server-konfiguráció**.

4. Módosítsa a **SQL-kapcsolat szintje** a kötelező beállítás. Ez a terület segítségével igény szerint módosítsa az SQL Server-portot vagy az SQL-hitelesítés beállításai.

   ![SQL-kapcsolat módosítása](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. Várjon néhány percet a frissítés befejezéséhez.

   ![Értesítés az SQL virtuális gép](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a id="manualtcp"></a> TCP/IP engedélyezése a Developer és Express kiadásokhoz

Ha az SQL Server-kapcsolódási beállítások módosítása, az Azure nem engedélyezi automatikusan a TCP/IP protokollt az SQL Server Developer és Express kiadásaihoz. Az alábbi lépések ismertetik, hogyan lehet manuálisan engedélyezni a TCP/IP protokollt, hogy távolról is csatlakozhasson IP-címmel.

Először csatlakozzon a távoli asztalról az SQL Servert futtató gép.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Következő lépésként engedélyezze a TCP/IP protokollt a **SQL Server Configuration Manager**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Csatlakozás SSMS segítségével

A következő lépések bemutatják, hogyan hozzon létre egy nem kötelező DNS-címke az Azure virtuális gép, és hogyan csatlakozhat az SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a id="manual"></a> Manuális konfigurációs és hibaelhárítás

Bár a portál automatikusan beállíthatja a kapcsolati lehetőségeket kínál, hasznos lehet tudni, hogyan konfigurálhatja manuálisan a kapcsolatot. A követelmények ismertetése is segítheti hibaelhárítás.

Az alábbi táblázat a szeretne csatlakozni egy Azure-beli virtuális gépen futó SQL Server követelményeit.

| Követelmény | Leírás |
|---|---|
| [SQL Server-hitelesítési mód engedélyezése](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode#SSMSProcedure) | A virtuális gép távolról csatlakozhat, kivéve, ha egy virtuális hálózaton konfigurálta az Active Directory SQL Server-hitelesítés szükséges. |
| [Hozzon létre egy SQL-bejelentkezés](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) | Ha az SQL-hitelesítést használ, egy felhasználónevet és jelszót, amely a céladatbázisba is jogosult az SQL-bejelentkezési kell. |
| [Engedélyezze a TCP/IP protokoll](#manualTCP) | Az SQL Server TCP-n keresztül kapcsolatok engedélyeznie kell. |
| [Az SQL Server-portot a tűzfalszabály engedélyezése](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | A virtuális gép tűzfala engedélyeznie kell az SQL Server portja (alapértelmezés szerint 1433) a bejövő forgalmat. |
| [Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt a TCP 1433-as](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Engedélyeznie kell a virtuális gép az SQL Server portja (alapértelmezés szerint 1433) a forgalom fogadására, ha az interneten keresztül csatlakozni kíván. Helyi, mind a virtuális hálózat kapcsolatok nem igényelnek ez. Ez az az egyetlen lépés szükséges az Azure Portalon. |

> [!TIP]
> A fenti táblázatban szereplő lépéseket kell elvégezni, amikor konfigurálja a kapcsolatot a portálon. Csak kövesse az alábbi lépéseket a konfiguráció megerősítése és a kapcsolat manuális beállítása az SQL Serverhez.

## <a name="next-steps"></a>További lépések

Megtekintheti az üzembe helyezési útmutatót, valamint a kapcsolat ezeket a lépéseket, tekintse meg [kiépítése egy SQL Server virtuális gépet az Azure-ban](virtual-machines-windows-portal-sql-server-provision.md).

Azure virtuális gépeken futó SQL Server rendszerrel kapcsolatos témaköröket talál [SQL Server Azure virtuális gépeken](virtual-machines-windows-sql-server-iaas-overview.md).