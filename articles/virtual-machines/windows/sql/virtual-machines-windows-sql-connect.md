---
title: Csatlakozás az SQL Server virtuális géphez (Resource Manager) |} Microsoft Docs
description: Útmutató az Azure virtuális gépen futó SQL-kiszolgálóhoz való csatlakozáshoz. Ez a témakör a klasszikus üzembe helyezési modellt használ. A forgatókönyvek eltérőek lehetnek attól függően, hogy a hálózati konfiguráció és az ügyfél helye.
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
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301386"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Csatlakozás az Azure SQL Server virtuális géphez

## <a name="overview"></a>Áttekintés

Ez a témakör ismerteti, hogyan egy Azure virtuális gépen futó SQL Server-példányhoz való csatlakozáshoz. Bemutatja, néhány [általános kapcsolati forgatókönyvek](#connection-scenarios) , majd [kapcsolat beállításainak módosítására a portálon lépések](#change). Ha szeretné hibaelhárítása vagy a portálon kívül kapcsolat konfigurálása, lásd: a [kézi konfigurálás](#manual) Ez a témakör végén. 

Ha inkább a teljes útmutató a kiépítést és a kapcsolat kellene lennie, lásd: [Azure SQL Server virtuális gépek kiépítése](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Kapcsolat-forgatókönyvek

Az ügyfél csatlakozik egy virtuális gépen futó SQL Server módja attól függően, hogy az ügyfél és a hálózati konfiguráció helyét.

Ha egy SQL Server virtuális Gépet az Azure portálon, lehetősége van a típusának megadásával **SQL-kapcsolat**.

![Nyilvános SQL kapcsolati lehetőséget kiépítése során](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

A hálózati kapcsolatot a lehetőségek a következők:

| Beállítás | Leírás |
|---|---|
| **Public** | Csatlakozás az SQL-kiszolgálón az interneten keresztül |
| **Magánfelhő** | Kapcsolódás SQL Server ugyanazon a virtuális hálózaton |
| **helyi** | Csatlakozás SQL Server helyi az azonos virtuális gépen | 

Az alábbi szakaszok ismertetik a **nyilvános** és **titkos** beállítások részletesebben.

## <a name="connect-to-sql-server-over-the-internet"></a>Csatlakozás az SQL-kiszolgálón az interneten keresztül

Ha csatlakozni az SQL Server adatbázismotor az internetről, jelölje be **nyilvános** a a **SQL-kapcsolat** típus a portálon kiépítése során. A portál automatikusan hajtja végre az alábbi lépéseket:

* Az SQL Server lehetővé teszi, hogy a TCP/IP-protokoll.
* Egy tűzfalszabály nyissa meg az SQL Server TCP port (alapértelmezés szerint 1433) konfigurálja.
* Lehetővé teszi az SQL Server-hitelesítést, a nyilvános hozzáférés szükséges.
* Konfigurálja a hálózati biztonsági csoportot az SQL Server port TCP-forgalom a virtuális Gépet.

> [!IMPORTANT]
> A virtuálisgép-lemezképeket, az SQL Server Developer és Express kiadásait nem automatikusan engedélyezze a TCP/IP protokollt. A fejlesztői és Express kiadásait kell használnia az SQL Server Konfigurációkezelő használatával [manuálisan engedélyezze a TCP/IP protokollt](#manualtcp) a virtuális gép létrehozása után.

Bármely, internet-hozzáféréssel rendelkező ügyfél vagy a nyilvános IP-címet a virtuális gép vagy a DNS-címke, IP-címet hozzárendelni megadásával csatlakozhat az SQL Server-példány. Ha az SQL Server portja az 1433-as, nem kell adja meg azt a kapcsolati karakterláncban. A következő kapcsolati karakterlánc SQL virtuális gép és a DNS-címke csatlakozik `sqlvmlabel.eastus.cloudapp.azure.com` SQL-hitelesítéssel (is használhatja a nyilvános IP-cím).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Ez lehetővé teszi a kapcsolatot az ügyfelek az interneten keresztül, de ez nem feltétlenül jelenti azt, hogy bárki csatlakozhat az SQL Server. Kívül az ügyfelek a helyes felhasználónévvel és jelszóval rendelkezik. A fokozott biztonság, elkerülheti a jól ismert 1433-as port. Például ha konfigurálta az SQL Server port 1500 és a meghatározott megfelelő tűzfal és a hálózati biztonsági csoportszabályok, kapcsolódhat résznevekből, ha a port számát a kiszolgáló nevét. A következő példa egy egyéni portszám hozzáadásával az előzőre megváltoztatja **1500**, a kiszolgáló neve:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Ha SQL Server virtuális gépen az interneten keresztül, az Azure-adatközpontban kimenő adatok teljes tartozik normál [a kimenő adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>SQL Server csatlakozni a virtuális hálózaton belül

Ha úgy dönt, **titkos** a a **SQL-kapcsolat** típus az Azure portálon konfigurálja a beállításai megegyeznek a legtöbb **nyilvános**. Az egyetlen különbség, hogy nincs-e az SQL Server portja (alapértelmezés szerint 1433) a külső forgalom engedélyezésére hálózati biztonsági csoport szabály.

> [!IMPORTANT]
> A virtuálisgép-lemezképeket, az SQL Server Developer és Express kiadásait nem automatikusan engedélyezze a TCP/IP protokollt. A fejlesztői és Express kiadásait kell használnia az SQL Server Konfigurációkezelő használatával [manuálisan engedélyezze a TCP/IP protokollt](#manualtcp) a virtuális gép létrehozása után.

Magánhálózati kapcsolat gyakran használják a együtt [virtuális hálózati](../../../virtual-network/virtual-networks-overview.md), több olyan forgatókönyveket tesz lehetővé, amelyek. Az azonos virtuális hálózatban lévő virtuális gépek is csatlakozhat, ha virtuális gépek különböző erőforráscsoportokra szerepel. És egy [telephelyek közötti VPN](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), létrehozhat egy hibrid architektúra, amely a virtuális gépek a helyszíni hálózatokkal és gépek.

Virtuális hálózatok lehetővé teszik az Azure virtuális gépek csatlakoztatása a tartományhoz. Ez az SQL Server Windows-hitelesítés használatára az egyetlen lehetőség. A más helyzetekben a felhasználónevek és jelszavak SQL-hitelesítés szükséges.

Feltételezve, hogy a virtuális hálózaton konfigurálta a DNS, az SQL Server rendszerű virtuális számítógép neve a kapcsolati karakterláncban megadásával csatlakozhat az SQL Server-példányt. Az alábbi példa feltételezi, hogy a Windows-hitelesítés van is konfigurálva, és hogy a felhasználó számára engedélyezett az SQL Server-példányhoz való hozzáférés.

```
Server=mysqlvm;Integrated Security=true
```

## <a id="change"></a> SQL-kapcsolat beállításainak módosítása

A kapcsolat beállításokat az SQL Server virtuális gépen az Azure-portálon módosíthatja.

1. Válassza ki az Azure-portálon **virtuális gépek**.

2. Válassza ki az SQL Server rendszerű virtuális Géphez.

3. A **beállítások**, kattintson a **SQL Server-konfigurációs**.

4. Módosítsa a **SQL kapcsolati szint** a szükséges beállítás. Ez a terület segítségével igény szerint módosítsa az SQL Server portja vagy az SQL-hitelesítés beállításai.

   ![SQL-kapcsolat módosítása](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. Várjon néhány percet a frissítés befejezéséhez.

   ![SQL virtuális gép frissítési értesítés](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a id="manualtcp"></a> Engedélyezze a TCP/IP protokollt a fejlesztői és az Express verziója

SQL Server-kapcsolat beállításainak módosításakor Azure nem automatikusan engedélyezze a TCP/IP protokollt a SQL Server Developer és Express kiadásait. Az alábbi lépések ismertetik, hogyan lehet manuálisan engedélyezni a TCP/IP protokollt, hogy távolról is csatlakozhasson IP-címmel.

Először kapcsolódni a távoli asztalról az SQL Server-számítógépen.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Következő lépésként engedélyezze a TCP/IP protokollt a **SQL Server Configuration Manager**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Csatlakozás SSMS segítségével

A következő lépések bemutatják, hogyan hozzon létre egy nem kötelező DNS-címke az Azure virtuális gép, és csatlakozzon az SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a id="manual"></a> Kézi konfigurálás és hibaelhárítás

Bár a portál beállítások automatikus konfigurálásához, kapcsolatot, akkor célszerű tudja, hogyan kell manuálisan konfigurálnia a kapcsolatot. A felméréséről is segíthet hibaelhárítás.

A következő táblázat a követelmények, egy Azure virtuális Gépen futó SQL-kiszolgálóhoz való csatlakozáshoz.

| Követelmény | Leírás |
|---|---|
| [SQL Server-hitelesítési mód engedélyezése](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode#SSMSProcedure) | Csatlakoztassa a virtuális Gépet távolról kivéve, ha a virtuális hálózaton konfigurálta az Active Directory SQL Server-hitelesítés szükséges. |
| [SQL-bejelentkezés létrehozásával](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) | Ha SQL-hitelesítést használ, meg kell egy SQL-bejelentkezési egy felhasználónevét és jelszavát, amely a céladatbázis engedélyekkel is rendelkezik. |
| [TCP/IP-protokoll engedélyezéséhez](#manualTCP) | SQL Server TCP-n keresztül kell csatlakozhatnak. |
| [Az SQL Server port tűzfalszabály engedélyezése](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | A virtuális Gépen a tűzfalnak engedélyeznie kell az SQL Server portja (alapértelmezés szerint 1433) a bejövő forgalom. |
| [A TCP 1433 egy hálózati biztonsági szabály létrehozása](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Engedélyeznie kell a virtuális gép az SQL Server portja (alapértelmezés szerint 1433) a forgalom fogadására, ha az interneten keresztül csatlakozni szeretne. Helyi és virtuális hálózati-csak kapcsolatok nem is szüksége van. Ez az az egyetlen lépés szükséges az Azure portálon. |

> [!TIP]
> A fenti lépéseket történik meg a kapcsolat konfigurálásakor a portálon. Csak akkor használja ezeket a lépéseket, ellenőrizze a konfigurációját, vagy manuálisan beállítani a kapcsolat az SQL Server.

## <a name="next-steps"></a>További lépések

Kiépítés utasításokat együtt kapcsolat lépések, olvassa el [Azure SQL Server virtuális gépek kiépítése](virtual-machines-windows-portal-sql-server-provision.md).

Egyéb Azure virtuális gépeken futó SQL Server kapcsolatos témaköröket, lásd: [SQL Server Azure virtuális gépeken](virtual-machines-windows-sql-server-iaas-overview.md).