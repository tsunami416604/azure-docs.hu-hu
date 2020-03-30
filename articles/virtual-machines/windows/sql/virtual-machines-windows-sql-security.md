---
title: Az Azure-beli SQL Server biztonsági szempontjai | Microsoft dokumentumok
description: Ez a témakör általános útmutatást nyújt az Azure virtuális gépen futó SQL Server védelméhez.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f5ea0ddff38532b119d8d984f2dabd6d898b44a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77031356"
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Az SQL Server Azure-beli virtuális gépeken történő futtatásának biztonsági szempontjai

Ez a témakör általános biztonsági irányelveket tartalmaz, amelyek segítenek biztonságos hozzáférést biztosítani az SQL Server-példányokhoz egy Azure virtuális gépen (VM).

Az Azure számos iparági előírásnak és szabványnak felel meg, amelyek lehetővé teszik, hogy megfelelő megoldást hozzon létre a virtuális gépen futó SQL Server használatával. Az Azure-nak való szabályozási megfelelőségről az [Azure Adatvédelmi központban](https://azure.microsoft.com/support/trust-center/)talál további információt.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Az SQL virtuális géphez való hozzáférés szabályozása

Az SQL Server virtuális gép létrehozásakor fontolja meg, hogyan szabályozhatja, hogy ki férhet hozzá a számítógéphez és az SQL Serverhez. Általánosságban elmondható, hogy a következőket kell tennie:

- Korlátozza az SQL Server hez való hozzáférést csak az okat igénylő alkalmazásokra és ügyfelekre.
- Kövesse a felhasználói fiókok és jelszavak kezelésével kapcsolatos gyakorlati tanácsokat.

A következő szakaszok javaslatokat adnak a pontok átgondolására.

## <a name="secure-connections"></a>Biztonságos kapcsolatok

Amikor egy katalógusképpel rendelkező SQL Server virtuális gépet hoz létre, az **SQL Server-kapcsolat** beállítás **helyi (virtuális gépen belüli)**, **privát (virtuális hálózaton belüli)** vagy **nyilvános (internetes)** lehetőséget biztosít.

![SQL Server-kapcsolat](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

A legjobb biztonság érdekében válassza ki a forgatókönyvhöz legszigorúbb beállítást. Ha például egy olyan alkalmazást futtat, amely ugyanazon a virtuális gépen éri el az SQL Server t, akkor a **Helyi** a legbiztonságosabb választás. Ha olyan Azure-alkalmazást futtat, amely hozzáférést igényel az SQL Server kiszolgálóhoz, akkor a **Private** csak a megadott [Azure virtuális hálózaton](../../../virtual-network/virtual-networks-overview.md)belül biztosítja az SQL Server rel való kommunikációt. Ha **nyilvános** (internet) hozzáférésre van szüksége az SQL Server virtuális géphez, akkor a támadási felület csökkentése érdekében kövesse a jelen témakörben található egyéb ajánlott eljárásokat.

A portálon kiválasztott beállítások bejövő biztonsági szabályokat használnak a virtuális gép [hálózati biztonsági csoportján](../../../virtual-network/security-overview.md) (NSG) a virtuális gép hálózati forgalmának engedélyezéséhez vagy megtagadásához. Módosíthatja vagy létrehozhat új bejövő NSG-szabályokat, hogy lehetővé tegye a forgalmat az SQL Server port (alapértelmezett 1433). Megadhatja azokat az IP-címeket is, amelyek ezen a porton keresztül kommunikálhatnak.

![Hálózat biztonsági csoportok szabályai](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

A hálózati forgalom korlátozására vonatkozó NSG-szabályok mellett használhatja a Windows tűzfalat is a virtuális gépen.

Ha a klasszikus központi telepítési modell, végpontok használata a klasszikus központi telepítési modell, távolítsa el a végpontok a virtuális gépen, ha nem használja őket. Az ACL végpontokkal való használatával kapcsolatos tudnivalókért olvassa el [az ACL kezelése végponton című témakört.](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint) Ez nem szükséges az erőforrás-kezelőt használó virtuális gépek esetében.

Végül fontolja meg a titkosított kapcsolatok engedélyezését az SQL Server adatbázis-kezelő motor az Azure virtuális gép példányához. Sql-kiszolgálópéldány konfigurálása aláírt tanúsítvánnyal. További információt az Adatbázis-kezelő motor és a kapcsolati karakterlánc [szintaxisának titkosított kapcsolatainak engedélyezése](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) című [témakörben talál.](https://msdn.microsoft.com/library/ms254500.aspx)

## <a name="encryption"></a>Titkosítás

A felügyelt lemezek kiszolgálóoldali titkosítást és Azure lemeztitkosítást kínálnak. [A Kiszolgálóoldali titkosítás](/azure/virtual-machines/windows/disk-encryption) inaktív titkosítást biztosít, és védi az adatokat, hogy megfeleljenek a szervezeti biztonsági és megfelelőségi kötelezettségeknek. [Az Azure Disk Encryption](/azure/security/fundamentals/azure-disk-encryption-vms-vmss) bitlocker vagy DM-Crypt technológiát használ, és integrálja az Azure Key Vault-tal az operációs rendszer és az adatlemezek titkosításához. 

## <a name="use-a-non-default-port"></a>Nem alapértelmezett port használata

Alapértelmezés szerint az SQL Server a jól ismert 1433-as portot figyeli. A fokozott biztonság érdekében állítsa be úgy az SQL Server kiszolgálót, hogy nem alapértelmezett porton, például 1401-es porton figyeljen. Ha egy SQL Server-gyűjtemény lemezképét az Azure Portalon építi ki, ezt a portot az **SQL Server beállítások** panelen adhatja meg.

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

A kiépítés után történő konfiguráláshoz két lehetőség közül választhat:

- Az Erőforrás-kezelő virtuális gépei esetén az [SQL virtuális gépek erőforrás](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) **biztonsága** lehetőséget választhatja. Ez lehetővé teszi a port módosítását.

  ![A TCP-port módosítása a portálon](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- Klasszikus virtuális gépek vagy a portállal nem kiépített SQL Server virtuális gépek esetén manuálisan konfigurálhatja a portot a virtuális géphez távolról csatlakoztatva. A konfigurációs lépésekről a [Kiszolgáló konfigurálása adott TCP-porton](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port)című témakörben található. Ha ezt a manuális módszert használja, akkor hozzá kell adnia egy Windows tűzfal szabályt is, hogy engedélyezze a bejövő forgalmat az adott TCP-porton.

> [!IMPORTANT]
> A nem alapértelmezett port megadása akkor ajánlott, ha az SQL Server-port nyilvános internetkapcsolat előtt nyitva áll.

Ha az SQL Server nem alapértelmezett porton figyel, a csatlakozáskor meg kell adnia a portot. Tegyünk például egy olyan forgatókönyvet, amelyben a kiszolgáló IP-címe 13.55.255.255, és az SQL Server az 1401-es porton figyel. Az SQL Server kiszolgálóhoz `13.55.255.255,1401` való csatlakozáshoz a kapcsolati karakterláncban kell megadnia.

## <a name="manage-accounts"></a>Fiókok kezelése

Nem szeretné, ha a támadók könnyen kitalálnák a fiókneveket vagy jelszavakat. Az alábbi tippek segítségével:

- Hozzon létre egy egyedi helyi rendszergazdai fiókot, amelynek neve nem **Rendszergazda**.

- Összetett erős jelszavakat használjon az összes fiókjához. Az erős jelszó létrehozásáról az [Erős jelszó](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) létrehozása című cikkben olvashat bővebben.

- Alapértelmezés szerint az Azure a Windows-hitelesítést választja az SQL Server virtuális gép telepítése során. Ezért a **sa** bejelentkezési le van tiltva, és a jelszó van rendelve a telepítő. Azt javasoljuk, hogy a **SA** bejelentkezési nem használható vagy engedélyezve. Ha SQL-bejelentkezéssel kell rendelkeznie, használja az alábbi stratégiák egyikét:

  - Hozzon létre egy egyedi nevű, **rendszergazdai** tagsággal rendelkező SQL-fiókot. Ezt a portálról úgy teheti meg, hogy engedélyezi az **SQL-hitelesítést** a kiépítés során.

    > [!TIP] 
    > Ha a kiépítés során nem engedélyezi az SQL-hitelesítést, manuálisan kell módosítania a hitelesítési módot **SQL Server és Windows hitelesítési módra.** További információt a [Kiszolgálóhitelesítési mód módosítása című témakörben](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode)talál.

  - Ha a sa bejelentkezési adatokat kell **használnia,** engedélyezze a bejelentkezést a kiépítés után, és rendeljen hozzá egy új erős jelszót.

## <a name="additional-best-practices"></a>További ajánlott eljárások

A jelen témakörben ismertetett eljárásokon kívül azt javasoljuk, hogy tekintse át és valósítsa meg a legújabb biztonsági eljárások biztonsági bevált módszereit, valamint a virtuális gépek biztonságának ajánlott eljárásokat. 

A helyszíni biztonsági eljárásokról az SQL [Server telepítésével](/sql/sql-server/install/security-considerations-for-a-sql-server-installation) és a biztonsági központtal kapcsolatos biztonsági szempontok [című témakörben](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database)talál további információt. 

A virtuális gépek biztonságáról a virtuális gépek biztonsági áttekintésében olvashat [bővebben.](/azure/security/fundamentals/virtual-machines-overview)


## <a name="next-steps"></a>Következő lépések

Ha a teljesítménysel kapcsolatos gyakorlati tanácsok is érdekelnek, olvassa el az Azure virtuális gépek SQL Server hez vonatkozó gyakorlati tanácsok című [témakört.](virtual-machines-windows-sql-performance.md)

Az SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további témakörökről az [SQL Server az Azure virtuális gépekről című témakörben olvashat.](virtual-machines-windows-sql-server-iaas-overview.md) Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](virtual-machines-windows-sql-server-iaas-faq.md).

