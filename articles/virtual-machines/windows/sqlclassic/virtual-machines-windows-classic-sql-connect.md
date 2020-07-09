---
title: Kapcsolódás SQL Server virtuális géphez az Azure-ban (klasszikus) | Microsoft Docs
description: Megtudhatja, hogyan csatlakozhat az Azure-beli virtuális gépeken futó SQL Serverhoz. Ez a témakör a klasszikus telepítési modellt használja. A forgatókönyvek a hálózati konfigurációtól és az ügyfél helyétől függően eltérőek.
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
ms.openlocfilehash: 52f25d89691a2a721025848e28d119a0fbe5e322
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135973"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Csatlakozás Azure-beli SQL Server-alapú virtuális géphez (klasszikus üzembe helyezési modell)
> [!div class="op_single_selector"]
> * [Resource Manager](../../../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md)
> * [Klasszikus](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Áttekintés
Ez a témakör azt ismerteti, hogyan lehet csatlakozni az Azure-beli virtuális gépen futó SQL Server-példányhoz. [Általános csatlakozási forgatókönyveket](#connection-scenarios) tartalmaz, és [részletesen ismerteti a SQL Server-kapcsolatok Azure-beli virtuális gépen való konfigurálásának lépéseit](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../../azure-resource-manager/management/deployment-models.md). Ez a cikk a klasszikus üzembe helyezési modell használatát ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ha Resource Manager-alapú virtuális gépeket használ, tekintse meg a következőt: [kapcsolódás SQL Server virtuális géphez az Azure-ban a Resource Manager használatával](../../../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md).

## <a name="connection-scenarios"></a>Kapcsolatok forgatókönyvei
Az ügyfélnek a virtuális gépen futó SQL Serverhoz való csatlakozásának módja eltér az ügyfél helyétől és a számítógép/hálózat konfigurációjától függően. Ezek a forgatókönyvek a következőket biztosítják:

* [Kapcsolódás SQL Server ugyanahhoz a felhőalapú szolgáltatáshoz](#connect-to-sql-server-in-the-same-cloud-service)
* [Kapcsolódás SQL Server az interneten keresztül](#connect-to-sql-server-over-the-internet)
* [Kapcsolódás SQL Server ugyanahhoz a virtuális hálózathoz](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> A fenti módszerek bármelyikének használata előtt a [kapcsolat konfigurálásához a cikkben ismertetett lépéseket](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)kell követnie.
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Kapcsolódás SQL Server ugyanahhoz a felhőalapú szolgáltatáshoz
Ugyanabban a felhőalapú szolgáltatásban több virtuális gép is létrehozható. A virtuális gépek példájának megismeréséhez tekintse meg a virtuális [gépek virtuális hálózattal vagy felhőalapú szolgáltatással való összekapcsolását](/previous-versions/azure/virtual-machines/windows/classic/connect-vms-classic#connect-vms-in-a-standalone-cloud-service)ismertető témakört. Ez a forgatókönyv akkor fordul elő, ha egy virtuális gépen lévő ügyfél megpróbál csatlakozni egy másik virtuális gépen futó SQL Serverhoz ugyanazon a felhőalapú szolgáltatásban.

Ebben az esetben a virtuális gép **neve** (a portálon **számítógépnév** vagy **állomásnév** ) használatával kapcsolódhat. A virtuális gép számára a létrehozás során megadott név. Ha például az SQL-alapú virtuális gép **mysqlvm**nevezte el, akkor az azonos felhőalapú szolgáltatásban lévő ügyfél virtuális gépe a következő kapcsolati karakterláncot használhatja a kapcsolódáshoz:

`Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>`

### <a name="connect-to-sql-server-over-the-internet"></a>Kapcsolódás SQL Server az interneten keresztül
Ha az internetről szeretne csatlakozni a SQL Server adatbázis-motorhoz, létre kell hoznia egy virtuálisgép-végpontot a bejövő TCP-kommunikációhoz. Ez az Azure-konfigurációs lépés a TCP-port bejövő forgalmát egy olyan TCP-portra irányítja, amelyet elér a virtuális gép.

Az interneten keresztüli csatlakozáshoz a virtuális gép DNS-nevét és a virtuális gép végpontjának portszámát kell használnia (a cikk későbbi részében). A DNS-név megkereséséhez navigáljon a Azure Portal, és válassza a **virtuális gépek (klasszikus)** lehetőséget. Ezután válassza ki a virtuális gépet. A **DNS-név** az **Áttekintés** szakaszban látható.

Vegyünk például egy **mysqlvm** nevű klasszikus virtuális gépet a **Mysqlvm7777.cloudapp.net** DNS-nevével, valamint egy **57500**-es virtuálisgép-végpontot. Ha megfelelően konfigurált kapcsolatot használ, a következő kapcsolati karakterlánc használatával érheti el a virtuális gépet bárhonnan az interneten:

`Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>`

Bár ez lehetővé teszi az ügyfelek számára az interneten keresztüli kapcsolódást, ez nem jelenti azt, hogy bárki csatlakozhat a SQL Serverhoz. Az ügyfeleken kívül a helyes felhasználónévvel és jelszóval kell rendelkeznie. A további biztonság érdekében ne használja a jól ismert 1433-as portot a nyilvános virtuális gép végpontja számára. Ha lehetséges, érdemes lehet egy ACL-t hozzáadni a végponthoz, hogy csak az Ön által engedélyezett ügyfelekre korlátozza a forgalmat. Az ACL-ek végpontokkal való használatával kapcsolatos utasításokért lásd: [az ACL kezelése egy végponton](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

> [!NOTE]
> Fontos megjegyezni, hogy ha ezt a technikát a SQL Serversal való kommunikációra használja, az Azure-adatközpontból érkező összes kimenő adatforgalomra a kimenő adatforgalomra vonatkozó normál [díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/)érvényes.
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Kapcsolódás SQL Server ugyanahhoz a virtuális hálózathoz
A [Virtual Network](../../../virtual-network/virtual-networks-overview.md) további forgatókönyveket tesz lehetővé. Ugyanazon a virtuális hálózaton is csatlakoztathatók a virtuális gépek, még akkor is, ha ezek a virtuális gépek különböző felhőalapú szolgáltatásokban vannak. A helyek közötti [VPN](../../../vpn-gateway/vpn-gateway-site-to-site-create.md)használatával olyan hibrid architektúrát is létrehozhat, amely a helyszíni hálózatokkal és számítógépekkel összekapcsolja a virtuális gépeket.

A Virtual Networks lehetővé teszi az Azure-beli virtuális gépek tartományhoz való csatlakoztatását is. Ez az egyetlen módszer a Windows-hitelesítés használatára a SQL Server. A többi csatlakoztatási forgatókönyvhöz felhasználónevek és jelszavak szükségesek az SQL-hitelesítéshez.

Ha tartományi környezetet és Windows-hitelesítést fog konfigurálni, nem kell a cikkben ismertetett lépéseket használnia a nyilvános végpont vagy az SQL-hitelesítés és-bejelentkezések konfigurálásához. Ebben az esetben a kapcsolati karakterláncban a SQL Server VM nevének megadásával csatlakozhat a SQL Server-példányhoz. Az alábbi példa azt feltételezi, hogy a Windows-hitelesítés is konfigurálva van, és hogy a felhasználó hozzáférést kapott a SQL Server-példányhoz.

`Server=mysqlvm;Integrated Security=true`

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>A SQL Server-kapcsolat Azure-beli virtuális gépen való konfigurálásának lépései
A következő lépések bemutatják, hogyan csatlakozhat az interneten keresztül az SQL Server-példányhoz SQL Server Management Studio (SSMS) használatával. Ugyanezek a lépések érvényesek arra, hogy a SQL Server virtuális gépet elérhetővé tegye az alkalmazásai számára, a helyszíni és az Azure-ban egyaránt.

Ahhoz, hogy csatlakozni lehessen SQL Server egy másik virtuális gépről vagy az internetről, a következő szakaszokban leírtak szerint kell végrehajtania az alábbi feladatokat:

* [TCP-végpont létrehozása a virtuális géphez](#create-a-tcp-endpoint-for-the-virtual-machine)
* [A TCP-portok megnyitása a Windows tűzfalban](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Az SQL Server konfigurálása a TCP-protokoll figyeléséhez](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Az SQL Server konfigurálása vegyes módú hitelesítéshez](#configure-sql-server-for-mixed-mode-authentication)
* [SQL Server-hitelesítési bejelentkezés létrehozása](#create-sql-server-authentication-logins)
* [A virtuális gép DNS-nevének meghatározása](#determine-the-dns-name-of-the-virtual-machine)
* [Csatlakozás az adatbázismotorhoz egy másik számítógépről](#connect-to-the-database-engine-from-another-computer)

A következő ábra összegzi a kapcsolatok elérési útját:

![Csatlakozás SQL Server virtuális géphez](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Következő lépések
Ha a magas rendelkezésre állás és a vész-helyreállítás AlwaysOn rendelkezésre állási csoportok használatát is tervezi, érdemes fontolóra vennie egy figyelő megvalósítását. Az adatbázis-ügyfelek nem közvetlenül az egyik SQL Server példányhoz csatlakoznak a figyelőhöz. A figyelő a rendelkezésre állási csoportban lévő elsődleges replikára irányítja az ügyfeleket. További információ: [ILB-figyelő konfigurálása AlwaysOn rendelkezésre állási csoportokhoz az Azure-ban](../classic/ps-sql-int-listener.md).

Fontos, hogy áttekintse az Azure-beli virtuális gépeken futó SQL Serverra vonatkozó összes ajánlott biztonsági gyakorlatot. További információkért lásd [az SQL Server Azure-beli virtuális gépeken történő futtatásának biztonsági szempontjait](../../../azure-sql/virtual-machines/windows/security-considerations-best-practices.md).

Az Azure virtuális gépeken futó SQL Server [képzési tervének felfedezése](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/). 

A SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további témakörökért lásd: [SQL Server az azure Virtual Machines](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

