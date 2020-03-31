---
title: Csatlakozás SQL Server virtuális géphez (Erőforrás-kezelő) | Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakozhat az Azure-ban virtuális gépen futó SQL Serverhez. Ez a témakör a klasszikus üzembe helyezési modellt használja. A forgatókönyvek a hálózati konfigurációtól és az ügyfél helyétől függően eltérőek lehetnek.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: deb337d989a3658e909cefa7a9ab028e37792562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243171"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Csatlakozás SQL Server rendszerű virtuális gépekhez az Azure-ban

## <a name="overview"></a>Áttekintés

Ez a témakör azt ismerteti, hogyan csatlakozhat az Azure virtuális gépen futó SQL Server-példányhoz. Néhány [általános kapcsolódási forgatókönyvet](#connection-scenarios) foglal magában, majd a portálon lépéseket tartalmaz [a kapcsolódási beállítások módosításához.](#change) Ha a portálon kívüli kapcsolódási hibákat kell elhárítania vagy konfigurálnia, olvassa el a témakör végén található [manuális konfigurációt.](#manual) 

Ha inkább egy teljes végigvezeti mind a kiépítés és a kapcsolat, [lásd: Egy SQL Server virtuális gép kiépítése az Azure-ban.](virtual-machines-windows-portal-sql-server-provision.md)

## <a name="connection-scenarios"></a>Csatlakozási forgatókönyvek

Az ügyfél virtuális gépen futó SQL Server kiszolgálóhoz való csatlakozásának módja az ügyfél helyétől és a hálózati konfigurációtól függően eltérő lehet.

Ha sql Server virtuális gépet hoz létre az Azure Portalon, megadhatja az **SQL-kapcsolat**típusát.

![Nyilvános SQL-kapcsolat i. beállítás kiépítése során](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

A kapcsolódási lehetőségek a következők:

| Beállítás | Leírás |
|---|---|
| **Nyilvános** | Csatlakozás az SQL Server kiszolgálóhoz az interneten keresztül |
| **Privát** | Csatlakozás az SQL Server kiszolgálóhoz ugyanabban a virtuális hálózatban |
| **Helyi** | Csatlakozás az SQL Server kiszolgálóhoz helyileg ugyanazon a virtuális gépen | 

A következő szakaszok részletesebben ismertetik a **nyilvános** és **a magánbeállításokat.**

## <a name="connect-to-sql-server-over-the-internet"></a>Csatlakozás az SQL Server kiszolgálóhoz az interneten keresztül

Ha az internetről szeretne csatlakozni az SQL Server adatbázis-motorjához, válassza a **Nyilvános** lehetőséget a **portálSQL-kapcsolat** típusához a kiépítés során. A portál automatikusan a következő lépéseket teszi:

* Engedélyezi a TCP/IP protokollt az SQL Server számára.
* Tűzfalszabályt állít be az SQL Server TCP-port (alapértelmezett 1433) megnyitásához.
* Engedélyezi az SQL Server hitelesítést, amely a nyilvános hozzáféréshez szükséges.
* A virtuális gép hálózati biztonsági csoportját az SQL Server port összes TCP-forgalmára konfigurálja.

> [!IMPORTANT]
> Az SQL Server Developer and Express kiadások virtuálisgép-lemezképei nem engedélyezik automatikusan a TCP/IP protokollt. Fejlesztői és express kiadások esetén az SQL Server Configuration Manager használatával [manuálisan kell engedélyeznie a TCP/IP protokollt](#manualtcp) a virtuális gép létrehozása után.

Bármely internet-hozzáféréssel rendelkező ügyfél csatlakozhat az SQL Server-példányhoz a virtuális gép nyilvános IP-címének megadásával, vagy az adott IP-címhez rendelt BÁRMELY DNS-címke megadásával. Ha az SQL Server port 1433, nem kell megadnia a kapcsolati karakterláncban. A következő kapcsolati karakterlánc egy SQL virtuális géphez csatlakozik az SQL-hitelesítés `sqlvmlabel.eastus.cloudapp.azure.com` tanaséval (használhatja a nyilvános IP-címet is).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Bár ez lehetővé teszi a kapcsolatot az ügyfelek számára az interneten keresztül, ez nem jelenti azt, hogy bárki csatlakozhat az SQL Server. A külső ügyfeleknek a megfelelő felhasználónévvel és jelszóval kell rendelkezniük. A nagyobb biztonság érdekében azonban elkerülheti a jól ismert 1433-as portot. Ha például úgy állította be az SQL Server rendszert, hogy az 1500-as porton figyeljen, és megfelelő tűzfal- és hálózati biztonsági csoportszabályokat hozott létre, a portszámnak a kiszolgáló nevéhez való hozzáfűzésével kapcsolódhat. A következő példa az előzőt egy **1500-as**egyéni portszám hozzáadásával módosítja a kiszolgáló nevéhez:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Amikor az SQL Server lekérdezése egy virtuális gép az interneten keresztül, az összes kimenő adatokat az Azure datacenter a normál [díjszabás a kimenő adatátvitel.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Csatlakozás az SQL Server kiszolgálóhoz virtuális hálózaton belül

Ha a portálon az **SQL-kapcsolat** típusának **privát** beállítását választja, az Azure a **Nyilvános**beállítással azonos beállítások többségét konfigurálja. Az egyetlen különbség az, hogy nincs olyan hálózati biztonsági csoportszabály, amely engedélyezne külső forgalmat az SQL Server porton (alapértelmezett 1433).

> [!IMPORTANT]
> Az SQL Server Developer and Express kiadások virtuálisgép-lemezképei nem engedélyezik automatikusan a TCP/IP protokollt. Fejlesztői és express kiadások esetén az SQL Server Configuration Manager használatával [manuálisan kell engedélyeznie a TCP/IP protokollt](#manualtcp) a virtuális gép létrehozása után.

A privát kapcsolatot gyakran használják a [virtuális hálózattal](../../../virtual-network/virtual-networks-overview.md)együtt, amely számos forgatókönyvet tesz lehetővé. A virtuális gépeket ugyanabban a virtuális hálózatban is csatlakoztathatja, még akkor is, ha ezek a virtuális gépek különböző erőforráscsoportokban léteznek. A [helyek közötti VPN-nel](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)pedig létrehozhat egy hibrid architektúrát, amely összeköti a virtuális gépeket a helyszíni hálózatokkal és gépekkel.

Virtuális hálózatok is lehetővé teszi, hogy csatlakozzon az Azure-beli virtuális gépek egy tartományhoz. Ez az egyetlen módja annak, hogy a Windows-hitelesítést az SQL Server kiszolgálóra használja. A többi kapcsolati forgatókönyv hez SQL-hitelesítés szükséges felhasználónevekkel és jelszavakkal.

Feltéve, hogy konfigurálta a DNS-t a virtuális hálózatban, csatlakozhat az SQL Server-példányhoz, ha megadja az SQL Server virtuálisgép számítógépnevét a kapcsolati karakterláncban. A következő példa azt is feltételezi, hogy a Windows-hitelesítés is konfigurálva van, és hogy a felhasználó hozzáférést kapott az SQL Server-példányhoz.

```
Server=mysqlvm;Integrated Security=true
```

## <a name="change-sql-connectivity-settings"></a><a id="change"></a>SQL-kapcsolati beállítások módosítása

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Módosíthatja az SQL Server virtuális gép kapcsolódási beállításait az Azure Portalon.

1. Az Azure Portalon válassza az **SQL virtuális gépek**lehetőséget.

2. Válassza ki az SQL Server virtuális gép.

3. A **Beállítások csoportban**válassza a **Biztonság**lehetőséget.

4. Módosítsa az **SQL-kapcsolat szintjét** a szükséges beállításra. Ezen a területen tetszés szerint módosíthatja az SQL Server portvagy az SQL authentication beállításokat.

   ![SQL-kapcsolat módosítása](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. Várjon néhány percet, amíg a frissítés befejeződik.

   ![SQL virtuális gép frissítési értesítése](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a name="enable-tcpip-for-developer-and-express-editions"></a><a id="manualtcp"></a>TCP/IP engedélyezése fejlesztői és expressz kiadásokhoz

Az SQL Server kapcsolódási beállításainak módosításakor az Azure nem engedélyezi automatikusan a TCP/IP protokollt az SQL Server Developer és Express kiadásokhoz. Az alábbi lépések ismertetik, hogyan lehet manuálisan engedélyezni a TCP/IP protokollt, hogy távolról is csatlakozhasson IP-címmel.

Először csatlakozzon az SQL Server számítógéphez a távoli asztallal.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Ezután engedélyezze a TCP/IP protokollt az **SQL Server Configuration Manager**segítségével.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Csatlakozás SSMS segítségével

A következő lépések bemutatják, hogyan hozhat létre egy opcionális DNS-címkét az Azure virtuális géphez, majd csatlakozhat az SQL Server Management Studio (SSMS) szolgáltatáshoz.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="manual-configuration-and-troubleshooting"></a><a id="manual"></a>Kézi konfigurálás és hibaelhárítás

Bár a portál lehetőséget biztosít a kapcsolat automatikus konfigurálására, hasznos tudni, hogyan kell manuálisan konfigurálni a kapcsolatot. A követelmények megértése segítheti a hibaelhárítást is.

Az alábbi táblázat az Azure-gépen futó SQL Server-kiszolgálóhoz való csatlakozás követelményeit sorolja fel.

| Követelmény | Leírás |
|---|---|
| [SQL Server hitelesítési mód engedélyezése](/sql/database-engine/configure-windows/change-server-authentication-mode#use-ssms) | Az SQL Server-hitelesítés a virtuális géphez való távoli csatlakozáshoz szükséges, kivéve, ha konfigurálta az Active Directoryt egy virtuális hálózaton. |
| [SQL-bejelentkezés létrehozása](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) | Ha SQL-hitelesítést használ, olyan SQL bejelentkezésre van szüksége, amelynek felhasználóneve és jelszava is rendelkezik a céladatbázishoz. |
| [TCP/IP protokoll engedélyezése](#manualtcp) | Az SQL Server kiszolgálónak engedélyeznie kell a TCP-n keresztüli kapcsolatokat. |
| [Tűzfalszabály engedélyezése az SQL Server porthoz](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | A virtuális gép tűzfalának engedélyeznie kell a bejövő forgalmat az SQL Server porton (alapértelmezett 1433). |
| [Hálózati biztonsági csoportszabály létrehozása a TCP 1433-hoz](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Ha az interneten keresztül szeretne csatlakozni, engedélyeznie kell, hogy a virtuális gép fogadja a forgalmat az SQL Server porton (alapértelmezett 1433). A helyi és a csak virtuális hálózatot létesítő kapcsolatok nem igénylik ezt. Ez az egyetlen szükséges lépés az Azure Portalon. |

> [!TIP]
> A fenti táblázatlépései a portálon való kapcsolat konfigurálásakor történnek. Csak ezekkel a lépésekkel erősítse meg a konfigurációt, vagy manuálisan állítsa be a kapcsolatot az SQL Server hez.

## <a name="next-steps"></a>Következő lépések

A létesítési utasítások, valamint ezekkel a kapcsolódási lépésekkel együtt tekintse meg [az SQL Server virtuális gép kiépítése az Azure-ban](virtual-machines-windows-portal-sql-server-provision.md)című témakört.

Az SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további témaköröket [az SQL Server azure-beli virtuális gépeken című témakörben talál.](virtual-machines-windows-sql-server-iaas-overview.md)