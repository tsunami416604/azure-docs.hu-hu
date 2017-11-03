---
title: "Biztonsági szempontok az SQL Server az Azure-ban |} Microsoft Docs"
description: "Ez a témakör egy Azure virtuális gépen futó SQL Server biztonságához általános útmutatást biztosít."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2017
ms.author: jroth
ms.openlocfilehash: 4ad9156e481eac0bae32bca35a2b126363e5d8b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Az SQL Server Azure-beli virtuális gépeken történő futtatásának biztonsági szempontjai

Ez a témakör általános biztonsági irányelvek felderíthető SQL Server-példányokat a biztonságos hozzáférést egy Azure virtuális gépen (VM) tartalmaz.

Azure megfelel a több iparági szabályozások és szabványok gyűjteménye, amelyek lehetővé teszik a virtuális gépen futó SQL Server egy megfelelő megoldás kiépítését. Az előírásoknak való megfelelés az Azure-ral kapcsolatos információkért lásd: [Azure biztonsági és adatkezelési központ](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Elérés az SQL-virtuális géphez

Az SQL Server virtuális gép létrehozásakor fontolja meg alaposan a kinek van hozzáférése az SQL Server és a gép vezérlése. Általánosságban elmondható tegye a következőket:

- SQL-kiszolgálóhoz való hozzáférés korlátozása csak az alkalmazások és az ügyfelek számára, amelyek azt.
- Kövesse az ajánlott eljárások a felhasználói fiókok és jelszavak kezelése.

A következő szakaszokban javaslatok a ezen a ponton keresztül számbavétele.

## <a name="secure-connections"></a>Biztonságos kapcsolaton

Gyűjteményelem képének, egy SQL Server virtuális gép létrehozásakor a **SQL Server-kapcsolat** lehetőséget ad a választott **(VM) belül helyi**, **titkos (virtuális hálózaton belül)**, vagy **nyilvános (Internet)**.

![SQL Server-kapcsolat](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

A nagyobb biztonság érdekében válassza ki a forgatókönyvnek a legszigorúbb beállítás. Például ha egy alkalmazást futtat, amely hozzáfér az SQL Server az azonos virtuális gépen, majd **helyi** a legbiztonságosabb lehetőség van. Ha futtatja az Azure-alkalmazások, majd az SQL Server-hozzáférést igénylő **titkos** titkosítja a kommunikációt az SQL Server csak a megadott belül [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Ha szüksége **nyilvános** (internest) hozzáférés az SQL Server rendszerű virtuális Géphez, majd ellenőrizze, hogy más Ez a témakör a támadási felület csökkentése érdekében a bevált gyakorlat követéséhez.

A kiválasztott beállításokat, a portál bejövő biztonsági szabályok használata a virtuális gépeken [hálózati biztonsági csoport](../../../virtual-network/virtual-networks-nsg.md) (NSG) és a virtuális gép hálózati adatforgalom engedélyezéséhez vagy letiltásához. Módosíthatja, vagy hozzon létre új bejövő NSG-szabályok az SQL Server portja (alapértelmezés szerint 1433) a forgalom engedélyezésére. Ezen a porton keresztül való kommunikációhoz engedélyezett IP-címek is megadható.

![Hálózati biztonsági csoportszabályok](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

NSG-szabályok korlátozhatja a hálózati forgalom, valamint használhatja a Windows tűzfal a virtuális gépen.

Ha végpontok a klasszikus üzembe helyezési modellt használ, távolítsa el a végpontok a virtuális gépen, ha nem használja őket. Az ACL-ekkel végpontokon útmutatásért lásd: [végponti ACL kezelése](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint). Erre nincs szükség az erőforrás-kezelő használó gépekhez.

Végül fontolja meg az az Azure virtuális gép az SQL Server adatbázismotor példánya titkosított kapcsolatainak engedélyezésére. SQL server-példány konfigurálása egy aláírt tanúsítvánnyal. További információkért lásd: [titkosított kapcsolatok engedélyezése az adatbázismotor](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) és [kapcsolati karakterlánc szintaxisát](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="use-a-non-default-port"></a>Egy nem alapértelmezett portot használja.

Alapértelmezés szerint az SQL Server porton figyel egy jól ismert, 1433. A biztonság fokozása érdekében konfigurálja az SQL Server nem alapértelmezett port, például a 1401. Ha egy SQL Server-gyűjtemény rendszerképet az Azure portálon, ezt a portot is megadhat a **SQL Server-beállítások** panelen.

Ennek konfigurálásához kiépítése után két lehetőség közül választhat:

- Erőforrás-kezelő virtuális gépekhez, kiválaszthatja a **SQL Server-konfigurációs** a virtuális gép – áttekintés paneljéről. Ez lehetővé teszi a port módosítása.

  ![TCP-port a portál módosítása](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- Klasszikus virtuális gépekhez, vagy az SQL Server virtuális gépen, a portál használatával nem kiépített a port manuálisan konfigurálhatja távolról csatlakozzon a virtuális Géphez. A konfigurálás lépéseinek végrehajtásához tekintse meg a [figyelési kiszolgáló konfigurálása egy adott TCP-port](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Ez a manuális módszer használatakor is vegye fel a Windows tűzfal szabályt, hogy a TCP-portot a bejövő adatforgalom engedélyezésére kell.

> [!IMPORTANT]
> Egy nem alapértelmezett port megadásával nem árt, ha az SQL Server port meg nyitva a nyilvános internetes kapcsolatok.

Ha SQL Server egy nem alapértelmezett portot figyel, meg kell adnia a port csatlakoztatásakor. Példaként vegyünk egy forgatókönyvet, ahol a kiszolgáló IP-címe 13.55.255.255, SQL Server 1401 portot figyel. SQL-kiszolgálóhoz való kapcsolódás megadni `13.55.255.255,1401` a kapcsolati karakterláncban.

## <a name="manage-accounts"></a>Fiókok kezelése

Nem szeretné, hogy a támadók számára könnyen kitalálni a fiók nevét és jelszavát. Segítségével a következő tippek:

- Hozzon létre egy egyedi helyi rendszergazdai fiók nem nevű **rendszergazda**.

- A fiókok összetett erős jelszavakat használjon. Erős jelszó létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy erős jelszót](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) cikk.

- Alapértelmezés szerint Azure kiválasztja azokat a Windows-hitelesítés SQL Server virtuális gép telepítése során. Ezért a **SA** bejelentkezés le van tiltva, és egy jelszó telepítője által hozzárendelt. Azt javasoljuk, hogy a **SA** bejelentkezési nem szabad használni vagy engedélyezve. Ha egy SQL-bejelentkezési névvel kell rendelkeznie, használja a következő stratégiák egyikét:

  - Egy SQL-fiók létrehozása egyedi névvel rendelkező **sysadmin** tagságát. Ehhez a portálról engedélyezésével **SQL-hitelesítés** kiépítése során.

    > [!TIP] 
    > Ha nem az SQL-hitelesítés engedélyezi a kiépítés során, manuálisan módosítania kell a hitelesítési mód és **SQL Server és a Windows-hitelesítési mód**. További információkért lásd: [Server hitelesítési mód váltása](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Ha kell használnia a **SA** bejelentkezés, a bejelentkezés engedélyezése kiépítése után, és rendelje hozzá egy új erős jelszót.

## <a name="follow-on-premises-best-practices"></a>Kövesse a helyszíni gyakorlati tanácsok

A jelen témakörben ismertetett eljárások mellett azt javasoljuk, tekintse át, és valósítja meg a hagyományos helyszínen biztonsági megoldásokat alkalmazhatja, ha alkalmazható. További információkért lásd: [biztonsági szempontok az SQL Server telepítéséhez](https://docs.microsoft.com/sql/sql-server/install/security-considerations-for-a-sql-server-installation)

## <a name="next-steps"></a>Következő lépések

Ha Ön is ajánlott eljárások teljesítményének körül iránt érdeklődik, lásd: [teljesítmény ajánlott eljárások az SQL Server Azure virtuális gépek](virtual-machines-windows-sql-performance.md).

Egyéb Azure virtuális gépeken futó SQL Server kapcsolatos témaköröket, lásd: [SQL Server Azure virtuális gépek – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md).

