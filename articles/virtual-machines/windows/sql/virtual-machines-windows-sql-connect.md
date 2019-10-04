---
title: Kapcsolódás SQL Server virtuális géphez (Resource Manager) | Microsoft Docs
description: Megtudhatja, hogyan csatlakozhat az Azure-beli virtuális gépeken futó SQL Serverhoz. Ez a témakör a klasszikus telepítési modellt használja. A forgatókönyvek a hálózati konfigurációtól és az ügyfél helyétől függően eltérőek.
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
ms.openlocfilehash: ae5c4cdd76f164d13da349c355a30d8b6dc83058
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102088"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Kapcsolódás SQL Server Azure-beli virtuális géphez

## <a name="overview"></a>Áttekintés

Ez a témakör azt ismerteti, hogyan lehet csatlakozni az Azure-beli virtuális gépen futó SQL Server-példányhoz. Néhány [általános kapcsolódási forgatókönyvet](#connection-scenarios) tárgyal, majd [a kapcsolódási beállítások módosításához a portálon lépéseket](#change)tartalmaz. Ha a portálon kívülről kell a kapcsolatot elhárítani vagy konfigurálnia, tekintse meg a témakör végén található [manuális konfigurálást](#manual) . 

Ha inkább a kiépítés és a kapcsolat teljes körű ismertetését szeretné látni, tekintse [meg a SQL Server virtuális gép üzembe helyezését az Azure](virtual-machines-windows-portal-sql-server-provision.md)-ban című témakört.

## <a name="connection-scenarios"></a>Kapcsolatok forgatókönyvei

Az ügyfél virtuális gépen futó SQL Serverhoz való csatlakozásának módja eltér az ügyfél helyétől és a hálózati konfigurációtól függően.

Ha kiépít egy SQL Server VM a Azure Portalban, lehetősége van megadnia az **SQL-kapcsolat**típusát.

![Nyilvános SQL-kapcsolati beállítás a kiépítés során](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

A kapcsolódási lehetőségek a következők:

| Beállítás | Leírás |
|---|---|
| **Public** | Kapcsolódás SQL Server az interneten keresztül |
| **Titkos** | Kapcsolódás SQL Server ugyanahhoz a virtuális hálózathoz |
| **Helyi** | Kapcsolódás SQL Server helyileg ugyanazon a virtuális gépen | 

A következő részek részletesebben ismertetik a **nyilvános** és a **magánjellegű** beállításokat.

## <a name="connect-to-sql-server-over-the-internet"></a>Kapcsolódás SQL Server az interneten keresztül

Ha az internetről szeretne csatlakozni a SQL Server adatbázis-motorhoz, válassza a **nyilvános** lehetőséget a portálon az **SQL-kapcsolat** típusaként a kiépítés során. A portál automatikusan a következő lépéseket hajtja végre:

* Engedélyezi a SQL Server TCP/IP protokollját.
* Egy tűzfalszabály konfigurálásával megnyitja a SQL Server TCP-portot (alapértelmezés szerint 1433).
* A nyilvános hozzáféréshez szükséges SQL Server hitelesítés engedélyezése.
* Konfigurálja a hálózati biztonsági csoportot a virtuális gépen a SQL Server porton lévő összes TCP-forgalomra.

> [!IMPORTANT]
> A SQL Server Developer és az expressz kiadások virtuálisgép-rendszerképei nem engedélyezik automatikusan a TCP/IP protokollt. Fejlesztői és expressz kiadások esetén a virtuális gép létrehozása után a [TCP/IP protokoll manuális engedélyezéséhez](#manualtcp) SQL Server konfigurációkezelőt kell használnia.

Bármely internet-hozzáféréssel rendelkező ügyfél csatlakozhat a SQL Server példányhoz a virtuális gép nyilvános IP-címének vagy az IP-címhez rendelt bármely DNS-címkének a megadásával. Ha a SQL Server port 1433, nem kell megadnia azt a kapcsolódási karakterláncban. A következő kapcsolati karakterlánc egy SQL- `sqlvmlabel.eastus.cloudapp.azure.com` hitelesítést használó SQL virtuális géphez csatlakozik (a nyilvános IP-címet is használhatja).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Bár ez lehetővé teszi az ügyfelek számára az interneten keresztüli kapcsolódást, ez nem jelenti azt, hogy bárki csatlakozhat a SQL Serverhoz. Az ügyfeleken kívül a helyes felhasználónévvel és jelszóval kell rendelkeznie. A további biztonság érdekében azonban elkerülheti a jól ismert 1433-as portot is. Ha például úgy konfigurálta SQL Server, hogy figyelje a 1500-es portot, és a megfelelő tűzfal-és hálózati biztonsági csoportokra vonatkozó szabályokat állapított meg, akkor a portszámnak a kiszolgálónévhez való hozzáfűzésével kapcsolódhat. Az alábbi példa az előzőt változtatja meg az **1500**-as számú egyéni portszám hozzáadásával a kiszolgálónévhez:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Amikor interneten keresztül kérdez le SQL Servert egy virtuális gépen, az Azure-adatközpontból érkező összes kimenő adatra a kimenő adatforgalomra vonatkozó normál [díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/)vonatkozik.

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Kapcsolódás SQL Server virtuális hálózaton belül

Ha a portálon az **SQL** -kapcsolati típushoz **privát** lehetőséget választ, az Azure a legtöbb olyan beállítást konfigurálja, amely azonos a **nyilvános**értékkel. Az egyetlen különbség, hogy nincs olyan hálózati biztonsági csoportra vonatkozó szabály, amely engedélyezi a SQL Server porton kívüli forgalom használatát (alapértelmezés szerint 1433).

> [!IMPORTANT]
> A SQL Server Developer és az expressz kiadások virtuálisgép-rendszerképei nem engedélyezik automatikusan a TCP/IP protokollt. Fejlesztői és expressz kiadások esetén a virtuális gép létrehozása után a [TCP/IP protokoll manuális engedélyezéséhez](#manualtcp) SQL Server konfigurációkezelőt kell használnia.

A magánhálózati kapcsolatot gyakran használják Virtual Networkokkal együtt, [](../../../virtual-network/virtual-networks-overview.md)ami számos forgatókönyvet tesz lehetővé. Ugyanazon a virtuális hálózaton is csatlakoztathatók a virtuális gépek, még akkor is, ha ezek a virtuális gépek különböző erőforráscsoportok alatt találhatók. A helyek közötti [VPN](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)használatával olyan hibrid architektúrát is létrehozhat, amely a helyszíni hálózatokkal és számítógépekkel összekapcsolja a virtuális gépeket.

A virtuális hálózatok lehetővé teszik az Azure-beli virtuális gépek tartományhoz való csatlakoztatását is. Ez az egyetlen módszer a Windows-hitelesítés használatára a SQL Server. A többi csatlakoztatási forgatókönyvhöz felhasználónevek és jelszavak szükségesek az SQL-hitelesítéshez.

Feltételezve, hogy konfigurálta a DNS-t a virtuális hálózaton, csatlakozhat a SQL Server-példányhoz úgy, hogy megadja a SQL Server VM számítógép nevét a kapcsolati karakterláncban. Az alábbi példa azt is feltételezi, hogy a Windows-hitelesítés is konfigurálva van, és hogy a felhasználó hozzáférést kapott a SQL Server-példányhoz.

```
Server=mysqlvm;Integrated Security=true
```

## <a id="change"></a>SQL-kapcsolat beállításainak módosítása

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

A Azure Portal a SQL Server virtuális gép csatlakozási beállításait módosíthatja.

1. A Azure Portal válassza az SQL-alapú **virtuális gépek**elemet.

2. Válassza ki a SQL Server VM.

3. A **Beállítások**területen válassza a **Biztonság**elemet.

4. Módosítsa az **SQL-kapcsolat szintjét** a kötelező beállításra. Ezt a körzetet igény szerint módosíthatja a SQL Server portot vagy az SQL-hitelesítési beállításokat.

   ![SQL-kapcsolat módosítása](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. Várjon néhány percet, amíg a frissítés befejeződik.

   ![SQL virtuális gép frissítési értesítése](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a id="manualtcp"></a>A TCP/IP engedélyezése fejlesztőknek és expressz kiadásoknak

SQL Server kapcsolódási beállítások módosításakor az Azure nem engedélyezi automatikusan a TCP/IP protokollt SQL Server Developer és Express kiadásokhoz. Az alábbi lépések ismertetik, hogyan lehet manuálisan engedélyezni a TCP/IP protokollt, hogy távolról is csatlakozhasson IP-címmel.

Először kapcsolódjon a SQL Server géphez a távoli asztal használatával.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Ezután engedélyezze a TCP/IP protokollt **SQL Server konfigurációkezelő**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Csatlakozás SSMS segítségével

Az alábbi lépések bemutatják, hogyan hozhat létre opcionális DNS-címkét az Azure-beli virtuális géphez, majd hogyan csatlakozhat a SQL Server Management Studiohoz (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a id="manual"></a>Manuális konfigurálás és hibaelhárítás

Bár a portál lehetőséget biztosít a kapcsolatok automatikus konfigurálására, hasznos tudni, hogyan konfigurálhatja a kapcsolatot manuálisan. A követelmények megismerése is segítséget nyújthat a hibaelhárításban.

A következő táblázat az Azure-beli virtuális gépeken futó SQL Serverhoz való kapcsolódás követelményeit sorolja fel.

| Követelmény | Leírás |
|---|---|
| [SQL Server hitelesítési mód engedélyezése](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode#SSMSProcedure) | SQL Server hitelesítés szükséges ahhoz, hogy távolról csatlakozhasson a virtuális géphez, hacsak nem konfigurálta a Active Directoryt egy Virtual Networkn. |
| [SQL-bejelentkezés létrehozása](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) | SQL-hitelesítés használata esetén olyan felhasználónévvel és jelszóval rendelkező SQL-bejelentkezésre van szükség, amely a céladatbázis engedélyeivel is rendelkezik. |
| [TCP/IP protokoll engedélyezése](#manualtcp) | A SQL Servernak engedélyeznie kell a TCP protokollon keresztüli kapcsolatokat. |
| [Tűzfalszabály engedélyezése a SQL Server porthoz](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | A virtuális gépen lévő tűzfalnak engedélyeznie kell a bejövő forgalmat a SQL Server porton (alapértelmezett 1433). |
| [Hálózati biztonsági csoportra vonatkozó szabály létrehozása a 1433-as TCP-hez](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Ha az interneten keresztül szeretne csatlakozni, engedélyeznie kell a virtuális gép számára a SQL Server port forgalmának fogadását (alapértelmezett 1433). A helyi és a virtuális hálózati kapcsolatok esetében nincs szükség erre. Ez az egyetlen szükséges lépés a Azure Portalban. |

> [!TIP]
> A fenti táblázatban szereplő lépések a Kapcsolódás a portálon való konfigurálásakor lesznek végrehajtva. Csak ezeket a lépéseket használja a konfiguráció megerősítéséhez vagy a kapcsolat manuális beállításához SQL Server.

## <a name="next-steps"></a>További lépések

Ha meg szeretné tekinteni a telepítési utasításokat ezen csatlakozási lépések mellett, tekintse [meg a SQL Server virtuális gép üzembe helyezése az Azure](virtual-machines-windows-portal-sql-server-provision.md)-ban című témakört.

A SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további témakörökért lásd: [SQL Server az azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).