---
title: Csatlakozás az SQL Server virtuális gép (klasszikus) Azure-on |} A Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat az Azure-beli virtuális gépen futó SQL Server. Ez a témakör a klasszikus üzemi modellt használja. A forgatókönyvek eltér attól függően, a hálózati konfigurációt, és az ügyfél helyét.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.reviewer: jroth
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: b8994d4c1eabf4381bf8364c76f7328d225f7e1a
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55732056"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Csatlakozás Azure-beli SQL Server-alapú virtuális géphez (hagyományos üzembe helyezési modell)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-connect.md)
> * [Klasszikus](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Áttekintés
Ez a témakör azt ismerteti, hogyan csatlakozhat egy Azure virtuális gépen futó SQL Server-példányhoz. Áttekint néhány [általános kapcsolódási forgatókönyvek](#connection-scenarios) majd [részletes lépéseit az SQL Server-kapcsolat konfigurálása Azure-beli virtuális gépen](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ha a Resource Manager virtuális gépeket használ, tekintse meg [Csatlakozás SQL Server virtuális gépekhez az Azure Resource Manager használatával](../sql/virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Kapcsolódási forgatókönyvek
Az ügyfél csatlakozik a virtuális gépen futó SQL Server módja az ügyfél és a gép/hálózati konfiguráció helyétől függően eltér. Ezek a forgatókönyvek a következőket biztosítják:

* [Csatlakozhat az SQL Server ugyanazon a felhőszolgáltatáson](#connect-to-sql-server-in-the-same-cloud-service)
* [Csatlakozás az SQL Serverhez az interneten keresztül](#connect-to-sql-server-over-the-internet)
* [Csatlakozás az SQL Serverhez az azonos virtuális hálózatba](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Ezen módszerek bármelyikével csatlakozás előtt hajtsa végre a [-kapcsolat konfigurálása az ebben a cikkben lépések](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Csatlakozhat az SQL Server ugyanazon a felhőszolgáltatáson
Több virtuális gép is létrehozható az ugyanazon felhőszolgáltatásban. A virtuális gépek forgatókönyv ismertetése: [virtuális gépek csatlakoztatása virtuális hálózattal vagy felhőszolgáltatással szolgáltatással](/previous-versions/azure/virtual-machines/windows/classic/connect-vms-classic#connect-vms-in-a-standalone-cloud-service). Ebben a forgatókönyvben van, amikor az ügyfél egy virtuális gép ugyanazon a felhőszolgáltatáson egy másik virtuális gépen futó SQL-kiszolgálóhoz való csatlakozáshoz.

Ebben a forgatókönyvben csatlakoztathatja a virtuális gép **neve** (is látható **számítógépnév** vagy **állomásnév** a portálon). Ez a virtuális gép létrehozása során megadott. Például, ha az SQL virtuális gép elnevezett **mysqlvm**, ugyanazon a felhőszolgáltatáson az ügyfél virtuális gép a következő kapcsolati karakterlánc használatával csatlakozzon:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Csatlakozás az SQL Serverhez az interneten keresztül
Ha azt szeretné, az internetről az SQL Server adatbázismotorhoz csatlakozni, létre kell hoznia a bejövő TCP-kommunikációt egy virtuális gép végpontjának. Ez az Azure-konfigurációs lépés a TCP-port bejövő forgalmát egy olyan TCP-portra irányítja, amelyet elér a virtuális gép.

Az interneten keresztül csatlakozni a virtuális gép DNS-név és a virtuális gép végpont portszámára (a cikk későbbi részében konfigurálva) kell használnia. Keresse meg a DNS-nevet, nyissa meg az Azure Portalon, és válassza **virtuális gépek (klasszikus)**. Ezután válassza ki a virtuális gép. A **DNS-név** látható a **áttekintése** szakaszban.

Vegyük példaként egy klasszikus virtuális gép nevű **mysqlvm** a DNS-név **mysqlvm7777.cloudapp.net** és a egy virtuális gép végpontja **57500**. Ha megfelelően konfigurált kapcsolati, a következő kapcsolati karakterlánc segítségével bárhonnan elérheti a virtuális gép az interneten:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Ez lehetővé teszi a kapcsolat az ügyfelek az interneten keresztül, bár ez nem jelenti azt, hogy bárki csatlakozhat az SQL Server. Kívüli ügyfelek számára a helyes felhasználónévvel és jelszóval rendelkezik. A fokozott biztonság érdekében ne használja a jól ismert 1433-as portot a nyilvános virtuálisgép-végpont. Ha lehetséges, fontolja meg a ACL hozzáadását, és csak az ügyfelek korlátozzák a forgalmat szeretné engedélyezni. A hozzáférés-vezérlési listák használata a végpontok útmutatásért lásd: [kezelése az ACL-végponton](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

> [!NOTE]
> Fontos tudni, hogy ez a módszer az SQL Serverrel való kommunikáció használatakor az Azure-adatközpontba a kimenő adatok teljes normál vonatkoznak [kimenő adatátvitelek díjszabását](https://azure.microsoft.com/pricing/details/data-transfers/).
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Csatlakozás az SQL Serverhez az azonos virtuális hálózatba
[Virtuális hálózat](../../../virtual-network/virtual-networks-overview.md) további olyan forgatókönyveket tesz lehetővé. Virtuális gépek csatlakozhatnak az azonos virtuális hálózatba, akkor is, ha a virtuális gépeken található különböző felhőszolgáltatások. És a egy [site-to-site VPN](../../../vpn-gateway/vpn-gateway-site-to-site-create.md), létrehozhat egy hibrid architektúra, amely a virtuális gépekhez kapcsolódik a helyszíni hálózatok és a gépek.

Virtuális hálózatok lehetővé teszi az Azure-beli virtuális gépek tartományhoz csatlakoztatására. Ez az egyetlen módja az SQL Server Windows-hitelesítés használatára. A más helyzetekben a felhasználónevek és jelszavak SQL-hitelesítés szükséges.

Ha egy tartományi környezetben és a Windows-hitelesítés konfigurálása, nem kell a nyilvános végpont vagy az SQL-hitelesítés és a bejelentkezések konfigurálásához kövesse a lépéseket ebben a cikkben. Ebben a forgatókönyvben csatlakozhat az SQL Server-példányhoz az SQL Server rendszerű virtuális gép neve a kapcsolati karakterlánc megadásával. Az alábbi példa azt feltételezi, hogy Windows-hitelesítést is lett konfigurálva, és, hogy a felhasználó kapott hozzáférést az SQL Server-példányt.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Egy Azure-beli virtuális gépen az SQL Server-kapcsolat konfigurálásának lépései
A következő lépések bemutatják, hogyan csatlakozhat az SQL Server-példány SQL Server Management Studio (SSMS) használatával az interneten keresztül. Ugyanazokat a lépéseket azonban, hogy elérhető-e az Ön alkalmazásait, a helyszínen fut, és az Azure-ban az SQL Servert futtató virtuális gép alkalmazni.

Az SQL Server példánya csatlakozhat egy másik virtuális Géphez vagy az internetről, mielőtt a következő szakaszok leírtak szerint a következő feladatokat kell elvégeznie:

* [A virtuális gép számára a TCP-végpont létrehozása](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Nyissa meg a Windows tűzfal TCP-portok](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [A TCP protokollal az SQL Server konfigurálása](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [SQL Server konfigurálása vegyes módú hitelesítéshez](#configure-sql-server-for-mixed-mode-authentication)
* [Az SQL Server-hitelesítési bejelentkezés létrehozása](#create-sql-server-authentication-logins)
* [A virtuális gép DNS-nevének meghatározása](#determine-the-dns-name-of-the-virtual-machine)
* [Csatlakozzon az adatbázismotorhoz egy másik számítógépről](#connect-to-the-database-engine-from-another-computer)

Az elérési útban összesítése az alábbi ábra szerint:

![Egy SQL Server virtuális gép csatlakoztatása](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>További lépések
Ha is magas rendelkezésre állás és vészhelyreállítás AlwaysOn rendelkezésre állási csoportok használatára, érdemes megfontolni egy figyelő megvalósítását. A figyelővel, hanem az SQL Server-példányok egyike az adatbázis-ügyfelek csatlakoznak. A figyelő továbbítja az ügyfelek a rendelkezésre állási csoport elsődleges replikáját. További információkért lásd: [AlwaysOn rendelkezésre állási csoportok ILB figyelő konfigurálása az Azure-ban](../classic/ps-sql-int-listener.md).

Fontos, az összes ajánlott biztonsági eljárások az Azure virtuális gépen futó SQL Serverhez. További információkért lásd [az SQL Server Azure-beli virtuális gépeken történő futtatásának biztonsági szempontjait](../sql/virtual-machines-windows-sql-security.md).

Az Azure virtuális gépeken futó SQL Server [képzési tervének felfedezése](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/). 

Azure virtuális gépeken futó SQL Server rendszerrel kapcsolatos témaköröket talál [SQL Server Azure virtuális gépeken](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

