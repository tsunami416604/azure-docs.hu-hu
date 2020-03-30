---
title: Csatlakozás SQL Server virtuális géphez az Azure-ban (Klasszikus) | Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakozhat az Azure-ban virtuális gépen futó SQL Serverhez. Ez a témakör a klasszikus üzembe helyezési modellt használja. A forgatókönyvek a hálózati konfigurációtól és az ügyfél helyétől függően eltérőek lehetnek.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.reviewer: jroth
experimental: true
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: 4627d9c4fa5c87e8e80ab80892062dabd77e9229
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249710"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Csatlakozás Azure-beli SQL Server-alapú virtuális géphez (klasszikus üzembe helyezési modell)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-connect.md)
> * [Klasszikus](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Áttekintés
Ez a témakör azt ismerteti, hogyan csatlakozhat az Azure virtuális gépen futó SQL Server-példányhoz. Néhány [általános kapcsolódási forgatókönyvet tartalmaz,](#connection-scenarios) majd részletes lépéseket tartalmaz [az SQL Server-kapcsolat konfigurálásához egy Azure virtuális gépben.](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)

> [!IMPORTANT] 
> Az Azure két különböző üzembe helyezési modellt rendelkezik az erőforrások létrehozásához és az erőforrásokkal való munkához: [az Erőforrás-kezelő és a Klasszikus.](../../../azure-resource-manager/management/deployment-models.md) Ez a cikk a klasszikus üzembe helyezési modell használatával ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ha Erőforrás-kezelő virtuális gépeket használ, olvassa el [a Csatlakozás sql server virtuális géphez az Azure-ban az Erőforrás-kezelő használatával](../sql/virtual-machines-windows-sql-connect.md)című témakört.

## <a name="connection-scenarios"></a>Csatlakozási forgatókönyvek
Az ügyfél virtuális gépen futó SQL Server kiszolgálóhoz való csatlakozásának módja az ügyfél helyétől és a gép/hálózat konfigurációjától függően eltérő lehet. Ezek a forgatókönyvek a következőket biztosítják:

* [Csatlakozás az SQL Server kiszolgálóhoz ugyanabban a felhőszolgáltatásban](#connect-to-sql-server-in-the-same-cloud-service)
* [Csatlakozás az SQL Server kiszolgálóhoz az interneten keresztül](#connect-to-sql-server-over-the-internet)
* [Csatlakozás az SQL Server kiszolgálóhoz ugyanabban a virtuális hálózatban](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Mielőtt kapcsolatba lépne ezekkel a módszerekkel, a kapcsolat konfigurálásához kövesse a [jelen cikkben leírt lépéseket.](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Csatlakozás az SQL Server kiszolgálóhoz ugyanabban a felhőszolgáltatásban
Több virtuális gép is létrehozható ugyanabban a felhőszolgáltatásban. A virtuális gépek forgatókönyvének megértéséhez olvassa el [A virtuális gépek csatlakoztatása virtuális hálózattal vagy felhőszolgáltatással című témakört.](/previous-versions/azure/virtual-machines/windows/classic/connect-vms-classic#connect-vms-in-a-standalone-cloud-service) Ebben a forgatókönyvben az egyik virtuális gépen lévő ügyfél megpróbál csatlakozni az SQL Server kiszolgálóhoz, amely ugyanazon a felhőszolgáltatásban fut egy másik virtuális gépen.

Ebben a forgatókönyvben csatlakozhat a virtuális gép **neve** (más néven is megjelenik **a számítógép név** vagy **állomásnév** a portálon). Ezt a nevet adta meg a virtuális gépnek a létrehozás során. Ha például az SQL VM **mysqlvm**nevet kapta, az ugyanabban a felhőszolgáltatásban lévő ügyfélvirtuális gép a következő kapcsolati karakterláncot használhatja a csatlakozáshoz:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Csatlakozás az SQL Server kiszolgálóhoz az interneten keresztül
Ha az internetről szeretne csatlakozni az SQL Server adatbázis-motorjához, létre kell hoznia egy virtuális gép végpontját a bejövő TCP-kommunikációhoz. Ez az Azure-konfigurációs lépés a TCP-port bejövő forgalmát egy olyan TCP-portra irányítja, amelyet elér a virtuális gép.

Az interneten keresztül történő csatlakozáshoz a virtuális gép DNS-nevét és a virtuális gép végpontjának portszámát kell használnia (a cikk későbbi részében konfigurálva). A DNS-név megkereséséhez keresse meg az Azure Portalt, és válassza a **Virtuális gépek (klasszikus)** lehetőséget. Ezután válassza ki a virtuális gépet. A **DNS-név** az **Áttekintés** szakaszban látható.

Vegyünk például egy **mysqlvm** nevű klasszikus virtuális **gépet, amelynek** DNS-neve mysqlvm7777.cloudapp.net és **egy 57500-as**virtuálisgép-végpont. Feltételezve, hogy megfelelően konfigurált kapcsolat, a következő kapcsolati karakterlánc használható a virtuális gép eléréséhez bárhonnan az interneten:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Bár ez lehetővé teszi a kapcsolatot az ügyfelek számára az interneten keresztül, ez nem jelenti azt, hogy bárki csatlakozhat az SQL Server. A külső ügyfeleknek a megfelelő felhasználónévvel és jelszóval kell rendelkezniük. A további biztonság érdekében ne használja a jól ismert 1433-as portot a nyilvános virtuális gép végpontjához. És ha lehetséges, fontolja meg egy ACL hozzáadása a végponthoz, hogy korlátozza a forgalmat csak az ügyfelek számára lehetővé teszi. Az ACL végpontokkal való használatával kapcsolatos tudnivalókért olvassa el [az ACL kezelése végponton című témakört.](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint)

> [!NOTE]
> Fontos megjegyezni, hogy ha ezt a technikát használja az SQL Server szolgáltatással való kommunikációhoz, az Azure-adatközpontból érkező összes kimenő adat normál díjszabás alá esik [a kimenő adatátvitelek esetén.](https://azure.microsoft.com/pricing/details/data-transfers/)
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Csatlakozás az SQL Server kiszolgálóhoz ugyanabban a virtuális hálózatban
[A virtuális hálózat](../../../virtual-network/virtual-networks-overview.md) további forgatókönyveket tesz lehetővé. A virtuális gépeket ugyanabban a virtuális hálózatban csatlakoztathatja, még akkor is, ha ezek a virtuális gépek különböző felhőszolgáltatásokban léteznek. A [helyek közötti VPN-nel](../../../vpn-gateway/vpn-gateway-site-to-site-create.md)pedig létrehozhat egy hibrid architektúrát, amely összeköti a virtuális gépeket a helyszíni hálózatokkal és gépekkel.

Virtuális hálózatok is lehetővé teszi, hogy csatlakozzon az Azure-beli virtuális gépek egy tartományhoz. Ez az egyetlen módja annak, hogy a Windows-hitelesítést az SQL Server kiszolgálóra használja. A többi kapcsolati forgatókönyv hez SQL-hitelesítés szükséges felhasználónevekkel és jelszavakkal.

Ha tartományi környezetet és Windows-hitelesítést szeretne konfigurálni, nem kell a jelen cikkben ismertetett lépéseket használnia a nyilvános végpont vagy az SQL-hitelesítés és bejelentkezések konfigurálásához. Ebben az esetben csatlakozhat az SQL Server-példányhoz az SQL Server virtuális gép nevének megadásával a kapcsolati karakterláncban. A következő példa feltételezi, hogy a Windows-hitelesítés is konfigurálva van, és hogy a felhasználó hozzáférést kapott az SQL Server-példányhoz.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Az SQL Server-kapcsolat azure-beli virtuális gépben való konfigurálásának lépései
Az alábbi lépések bemutatják, hogyan csatlakozhat az SQL Server-példányhoz az SQL Server Management Studio (SSMS) használatával. Azonban ugyanazok a lépések vonatkoznak arra, hogy az SQL Server virtuális gép elérhetővé az alkalmazások, a helyszíni és az Azure-ban futó.

Mielőtt egy másik virtuális gépről vagy az internetről csatlakozhatna az SQL Server példányához, a következő szakaszokban leírtak szerint el kell végeznie a következő feladatokat:

* [TCP-végpont létrehozása a virtuális géphez](#create-a-tcp-endpoint-for-the-virtual-machine)
* [TCP-portok megnyitása a Windows tűzfalon](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Az SQL Server konfigurálása a TCP-protokoll figyeléséhez](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Az SQL Server konfigurálása vegyes módú hitelesítéshez](#configure-sql-server-for-mixed-mode-authentication)
* [SQL Server-hitelesítési bejelentkezés létrehozása](#create-sql-server-authentication-logins)
* [A virtuális gép DNS-nevének meghatározása](#determine-the-dns-name-of-the-virtual-machine)
* [Csatlakozás az adatbázismotorhoz egy másik számítógépről](#connect-to-the-database-engine-from-another-computer)

A kapcsolat elérési útját az alábbi ábra foglalja össze:

![Csatlakozás SQL Server virtuális géphez](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Következő lépések
Ha azt is tervezi, hogy alwayson rendelkezésre állási csoportok magas rendelkezésre állási és vész-helyreállítási, érdemes egy figyelő megvalósítását. Az adatbázis-ügyfelek nem közvetlenül az SQL Server-példányokhoz, hanem a figyelőhöz csatlakoznak. A figyelő az ügyfeleket az elsődleges replika az elérhetőségi csoportban. További információt az [ILB-figyelő konfigurálása az AlwaysOn rendelkezésre állási csoportokhoz az Azure-ban című témakörben talál.](../classic/ps-sql-int-listener.md)

Fontos, hogy tekintse át az Összes biztonsági gyakorlati az Sql Server azure-beli virtuális gépen futó. További információkért lásd [az SQL Server Azure-beli virtuális gépeken történő futtatásának biztonsági szempontjait](../sql/virtual-machines-windows-sql-security.md).

Az Azure virtuális gépeken futó SQL Server [képzési tervének felfedezése](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/). 

Az SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további témaköröket [az SQL Server azure-beli virtuális gépeken című témakörben talál.](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

