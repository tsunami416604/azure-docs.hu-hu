---
title: "Csatlakozni az SQL Server virtuális gép (klasszikus) Azure-on |} Microsoft Docs"
description: "Útmutató az Azure virtuális gépen futó SQL-kiszolgálóhoz való csatlakozáshoz. Ez a témakör a klasszikus üzembe helyezési modellt használ. A forgatókönyvek eltérőek lehetnek attól függően, hogy a hálózati konfiguráció és az ügyfél helye."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: jroth
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: e986440edc0b683d1b1cd49207a355dac060c53d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Csatlakozás Azure-beli SQL Server-alapú virtuális géphez (hagyományos üzembe helyezési modell)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-connect.md)
> * [Klasszikus](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Áttekintés
Ez a témakör ismerteti, hogyan egy Azure virtuális gépen futó SQL Server-példányhoz való csatlakozáshoz. Bemutatja, néhány [általános kapcsolati forgatókönyvek](#connection-scenarios) , majd [egy Azure virtuális gép az SQL Server-kapcsolat beállításának lépései részletes](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ha az erőforrás-kezelő virtuális gépeket használ, tekintse meg [csatlakozás az SQL Server virtuális gép az Azure Resource Manager használatával](../sql/virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Kapcsolat-forgatókönyvek
Az ügyfél csatlakozik egy virtuális gépen futó SQL Server módja attól függően, hogy az ügyfél és a machine/hálózati konfigurációja helyét. Ezek a forgatókönyvek a következőket biztosítják:

* [Kapcsolódás SQL Server ugyanazon a felhőszolgáltatásban található](#connect-to-sql-server-in-the-same-cloud-service)
* [Csatlakozás az SQL-kiszolgálón az interneten keresztül](#connect-to-sql-server-over-the-internet)
* [Kapcsolódás SQL Server ugyanazon a virtuális hálózaton](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Ezek a módszerek bármelyikével csatlakoztat, ahhoz el kell végeznie a [kapcsolat konfigurálása a cikkben ismertetett visszaállítási lépésekkel](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Kapcsolódás SQL Server ugyanazon a felhőszolgáltatásban található
Több virtuális gép is létrehozható az ugyanazon a felhőalapú szolgáltatás. A virtuális gépek forgatókönyv ismertetése: [kapcsolódás a virtuális gépek virtuális hálózati vagy a felhőalapú szolgáltatással](../classic/connect-vms-classic.md#connect-vms-in-a-standalone-cloud-service). Ebben a forgatókönyvben egy ügyfél egy virtuális gép csatlakozik ugyanahhoz a felhőszolgáltatásban található egy másik virtuális gépen futó SQL Server.

Ebben a forgatókönyvben is elérheti a virtuális gép használatával **neve** (is megjelennek az helyeként **számítógépnév** vagy **állomásnév** a portálon). Azt a nevet, a virtuális gép létrehozásakor megadott. Például, ha az SQL virtuális gép elnevezett **mysqlvm**, egy ügyfél virtuális gép ugyanazon a felhőszolgáltatásban található használhatja a következő kapcsolati karakterlánc való csatlakozáshoz:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Csatlakozás az SQL-kiszolgálón az interneten keresztül
Ha szeretné az SQL Server adatbázismotor az internetről kapcsolódnak, létre kell hoznia egy virtuális gép végpontjának bejövő TCP-kommunikációhoz. Ez az Azure-konfigurációs lépés a TCP-port bejövő forgalmát egy olyan TCP-portra irányítja, amelyet elér a virtuális gép.

Az interneten keresztül csatlakozzanak a virtuális Gépet DNS-név és a virtuális gép végpont portszámot (a cikk későbbi részében konfigurált) kell használnia. A DNS-név, navigáljon az Azure portálra, és meg kiválasztása **virtuális gépek (klasszikus)**. Ezután válassza ki a virtuális gép. A **DNS-név** is megjelennek a **áttekintése** szakasz.

Vegyük példaként nevű klasszikus virtuális gép **mysqlvm** a DNS-név **mysqlvm7777.cloudapp.net** és egy virtuális gép végpontja **57500**. Ha megfelelően konfigurált kapcsolattal, a következő kapcsolati karakterlánc segítségével fér hozzá a virtuális géphez bárhonnan az interneten:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Ez a kapcsolati karakterlánc kapcsolódást engedélyezi az ügyfelek az interneten keresztül, de ez nem feltétlenül jelenti azt, hogy bárki csatlakozhat az SQL Server. Kívül az ügyfelek a helyes felhasználónévvel és jelszóval rendelkezik. A fokozott biztonság érdekében ne használjon a jól ismert 1433-as port a nyilvános virtuális gép végpontja. Ha lehetséges, fontolja meg, hozzáférés-vezérlési Listában, és a végponton való korlátozzák a forgalmat. csak az ügyfelek számára teszi lehetővé. Az ACL-ekkel végpontokon útmutatásért lásd: [végponti ACL kezelése](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint).

> [!NOTE]
> Ez a módszer használatával kommunikálni az SQL Server, az Azure-adatközpontban kimenő adatok teljes tartozik normál [a kimenő adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/).
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Kapcsolódás SQL Server ugyanazon a virtuális hálózaton
[Virtuális hálózati](../../../virtual-network/virtual-networks-overview.md) további olyan forgatókönyveket tesz lehetővé. Az azonos virtuális hálózatban lévő virtuális gépek is csatlakozhat, ha virtuális gépek különböző felhőszolgáltatások szerepel. És egy [telephelyek közötti VPN](../../../vpn-gateway/vpn-gateway-site-to-site-create.md), létrehozhat egy hibrid architektúra, amely a virtuális gépek a helyszíni hálózatokkal és gépek.

Virtuális hálózatok lehetővé teszik az Azure virtuális gépek csatlakoztatása a tartományhoz. Az egyetlen lehetőség a Windows-hitelesítés használata az SQL Server egy tartományhoz való csatlakozás. A más helyzetekben a felhasználónevek és jelszavak SQL-hitelesítés szükséges.

Ha egy tartományi környezetben és a Windows-hitelesítés konfigurálása, nem szükséges a nyilvános végpontot, vagy az SQL-hitelesítést és bejelentkezések konfigurálása. Ebben a forgatókönyvben csatlakozhat az SQL Server-példány az SQL Server virtuális gép nevét a kapcsolati karakterláncban megadásával. Az alábbi példa feltételezi, hogy a Windows-hitelesítést konfigurálták-e, és, hogy a felhasználó kapott-e az SQL Server-példányhoz való hozzáférés.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Egy Azure virtuális gép az SQL Server-kapcsolat beállításának lépései
A következő lépések bemutatják, hogyan lehet csatlakozni az SQL Server-példány SQL Server Management Studio (SSMS) használatával az interneten keresztül. Ugyanezek a lépések azonban, hogy az SQL Server virtuális gép elérhető, az alkalmazások, a helyszínen fut, és az Azure-ban alkalmazni.

Mielőtt keresztül csatlakozhat az SQL Server-példányt másik virtuális gép vagy az internetről, végre kell hajtania a következő feladatokat:

* [A virtuális gép TCP-végpont létrehozása](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Nyissa meg a TCP portokat a Windows tűzfal](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [A TCP protokollt az SQL Server konfigurálása](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [SQL Server vegyes üzemmódú hitelesítés konfigurálása](#configure-sql-server-for-mixed-mode-authentication)
* [SQL Server-hitelesítési bejelentkezési létrehozása](#create-sql-server-authentication-logins)
* [Határozza meg a virtuális gép DNS-neve](#determine-the-dns-name-of-the-virtual-machine)
* [Csatlakozás az adatbázismotorhoz való csatlakozáshoz egy másik számítógépről](#connect-to-the-database-engine-from-another-computer)

A kapcsolati útvonal a következő ábra szerint összegzése:

![Egy SQL Server virtuális géphez való kapcsolódás](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>További lépések
Ha is tervezi használni az AlwaysOn rendelkezésre állási csoportokat magas rendelkezésre állású és vész-helyreállítási, érdemes megfontolni egy figyelő megvalósítását. Adatbázis-ügyfelek csatlakoznak, a figyelőhöz, hanem az SQL Server példányai közül. A figyelő továbbítja az ügyfelek az elsődleges másodpéldányt a rendelkezésre állási csoportban. További információkért lásd: [egy ILB figyelőt az AlwaysOn rendelkezésre állási csoportok konfigurálása az Azure-](../classic/ps-sql-int-listener.md).

Fontos tekintse át a biztonsági gyakorlati tanácsok az SQL Server rendszert futtató Azure virtuális géphez. További információkért lásd [az SQL Server Azure-beli virtuális gépeken történő futtatásának biztonsági szempontjait](../sql/virtual-machines-windows-sql-security.md).

Az Azure virtuális gépeken futó SQL Server [képzési tervének felfedezése](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/). 

Egyéb Azure virtuális gépeken futó SQL Server kapcsolatos témaköröket, lásd: [SQL Server Azure virtuális gépeken](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

