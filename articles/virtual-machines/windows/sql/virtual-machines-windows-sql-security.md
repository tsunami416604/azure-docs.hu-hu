---
title: Az SQL Server az Azure-ban történő futtatásának biztonsági szempontjai |} A Microsoft Docs
description: Ez a témakör általános útmutatást az Azure virtuális gépen futó SQL Server védelme.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 69b6bd07699d179fc87ac6c5364a7a34b23d14eb
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731716"
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Az SQL Server Azure-beli virtuális gépeken történő futtatásának biztonsági szempontjai

Ez a témakör általános biztonsági irányelveket, amelyek segítenek a biztonságos hozzáférés az SQL Server-példányok létrehozásához az Azure virtuális gépen (VM) tartalmaz.

Az Azure számos iparági előírásoknak és szabványoknak, ami lehetővé teszi, hogy a megfelelő megoldás készítése egy virtuális gépen futó SQL Server megfelel. A szabályozásoknak való megfelelőséget az Azure-ral kapcsolatos információkért lásd: [Azure adatvédelmi központ](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Az SQL virtuális gép hozzáférés-vezérlése

Az SQL Servert futtató virtuális gép létrehozásakor fontolja meg alaposan a kinek van hozzáférése az SQL Server és a gép vezérlése. Általánosságban elmondható tegye a következőket:

- Az SQL Server való hozzáférés korlátozása csak az alkalmazások és ügyfelek számára, amelyek azt.
- Kövesse az ajánlott eljárásokat, felhasználói fiókok és jelszavak kezeléséhez.

Az alábbi szakaszok ezen a ponton keresztül terhelése a javaslatokat.

## <a name="secure-connections"></a>Biztonságos kapcsolatok

A katalógus-lemezkép, egy SQL Servert futtató virtuális gép létrehozásakor a **SQL Server-kapcsolódás** lehetőséget ad a kiválasztott **(a virtuális gépen) helyi**, **magánhálózati (virtuális hálózaton belül)**, vagy **nyilvános (Internet)**.

![Az SQL Server-kapcsolódás](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

A nagyobb biztonság érdekében válassza ki a korlátozást a forgatókönyvhöz. Például ha egy alkalmazást futtat, amely hozzáfér az azonos virtuális Gépen futó SQL Server majd **helyi** a legbiztonságosabb megoldás. Azure-alkalmazások, majd az SQL Server hozzáférésre van szüksége futtatásakor **privát** teszi biztonságossá a kommunikációt az SQL Server csak a megadott belül [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Ha szüksége van **nyilvános** (internet) eléréséhez a SQL Server virtuális géphez, majd ellenőrizze, hogy ez a témakör a támadási felület csökkentése érdekében a többi ajánlott eljárások követése.

A portál a kiválasztott beállításokkal bejövő biztonsági szabályok használata a virtuális gép [hálózati biztonsági csoport](../../../virtual-network/security-overview.md) (NSG) és a virtuális gép hálózati adatforgalom engedélyezéséhez vagy letiltásához. Módosíthatja, vagy hozzon létre új bejövő NSG-szabályok az SQL Server portja (alapértelmezés szerint 1433) forgalom engedélyezésére. Megadhatja, hogy adott IP-címek, amelyek ezen a porton keresztül engedélyezett való kommunikációhoz.

![Hálózat biztonsági csoportok szabályai](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

NSG-szabályokat a hálózati forgalom korlátozására, mellett is használhatja a Windows tűzfal a virtuális gépen.

Ha végpontokat használ a klasszikus üzemi modellel, távolítsa el olyan végpontok, a virtuális gépen, ha nem használja. A hozzáférés-vezérlési listák használata a végpontok útmutatásért lásd: [kezelése az ACL-végponton](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint). Ez nem szükséges a Resource Manager használó virtuális gépek esetében.

Végül érdemes megfontolni az Azure-beli virtuális gépen az SQL Server adatbázismotor-példány titkosított kapcsolatokat. SQL server-példány beállítása egy aláírt tanúsítvánnyal rendelkező. További információkért lásd: [titkosított kapcsolatokat engedélyezi az adatbázismotor](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) és [kapcsolati karakterlánc szintaxisát](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="use-a-non-default-port"></a>Egy nem alapértelmezett portot használja.

Alapértelmezés szerint az SQL Server a jól ismert porton figyel, 1433-as. A fokozott biztonság érdekében az SQL Server nem alapértelmezett porton, például az 1401 konfigurálása. Üzembe helyez egy SQL Server-katalógus lemezképet az Azure Portalon, ha ezt a portot megadhatja a **SQL Server-beállítások** panelen.

Ennek konfigurálásához üzembe helyezés után két lehetősége van:

- A Resource Manager virtuális gépek esetében választhatja **SQL Server-konfiguráció** a virtuális gép – áttekintés panelről. Ez lehetőséget biztosít, hogy módosítsa a portot.

  ![A portál módosítsa TCP-port](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- A klasszikus virtuális gépek vagy az SQL Server virtuális gépek, a portál használatával nem kiépített manuálisan konfigurálható a port távolról csatlakozik a virtuális Gépet. A konfigurálás lépéseinek végrehajtásához tekintse meg a [konfigurálása a Server to Listen on a Specific TCP Port](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Ha ezt a kézi módszert használja, is hozzá kell egy Windows tűzfalszabály átengedje a TCP-portot a bejövő forgalmat.

> [!IMPORTANT]
> Egy nem alapértelmezett portot határozza meg, célszerű, ha az SQL Server port meg nyitva a nyilvános internetes kapcsolatok.

Ha az SQL Server nem alapértelmezett porton figyel, meg kell adnia a port csatlakozáskor. Például vegyünk egy forgatókönyvet, ahol a kiszolgáló IP-cím 13.55.255.255 és az SQL Server 1401 portot figyel. Csatlakozás az SQL Serverhez, meg `13.55.255.255,1401` a kapcsolati karakterláncban.

## <a name="manage-accounts"></a>Fiókok kezelése

Nem érdemes a támadók könnyen kitalálni a fiók nevét és jelszavát. Használja az alábbi tippek segítségével:

- Hozzon létre egy egyedi helyi rendszergazdai fiók nem nevű **rendszergazda**.

- Összetett erős jelszavakat használja az összes fiók. Erős jelszó létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy erős jelszót](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) cikk.

- Alapértelmezés szerint az Azure SQL Servert futtató virtuális gép telepítése során kiválasztja a Windows-hitelesítés. Ezért a **SA** bejelentkezés le van tiltva, és a egy jelszó telepítője által hozzárendelt. Azt javasoljuk, hogy a **SA** bejelentkezési nem szabad használt vagy engedélyezve. SQL-bejelentkezési kell rendelkeznie, ha használja a következő stratégiák egyikét:

  - Hozzon létre egy SQL-fiók egy egyedi névvel rendelkező **SysAdmin (rendszergazda)** tagságát. Ezt megteheti is a portálról engedélyezésével **SQL-hitelesítés** üzembe helyezés során.

    > [!TIP] 
    > Ha SQL-hitelesítés nem engedélyezi a kiépítés során, manuálisan módosítania kell a hitelesítési módot **SQL Server és a Windows-hitelesítési mód**. További információkért lásd: [kiszolgáló-hitelesítési mód váltása](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Ha kell használnia a **SA** bejelentkezés, a bejelentkezés engedélyezése a kiépítés után, és rendelje hozzá egy erős jelszót.

## <a name="follow-on-premises-best-practices"></a>A helyszíni ajánlott eljárások követése

A jelen témakörben ismertetett eljárások mellett azt javasoljuk, hogy tekintse át, és a hagyományos helyszíni biztonsági eljárások megvalósításához, ha vannak ilyenek. További információkért lásd: [egy SQL Server telepítése történő futtatásának biztonsági szempontjai](https://docs.microsoft.com/sql/sql-server/install/security-considerations-for-a-sql-server-installation)

## <a name="next-steps"></a>További lépések

Ha Ön is ajánlott eljárások a teljesítmény körül érdekli, lásd: [teljesítmény az Azure Virtual machines gépeken az SQL Server ajánlott eljárásai](virtual-machines-windows-sql-performance.md).

Azure virtuális gépeken futó SQL Server rendszerrel kapcsolatos témaköröket talál [SQL Server on Azure Virtual Machines – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md). Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](virtual-machines-windows-sql-server-iaas-faq.md).

