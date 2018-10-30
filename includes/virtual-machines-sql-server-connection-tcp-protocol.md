---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: c9df9e0812d5c4b5a581d4b7da677623f45f8053
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226659"
---
1. Miközben a távoli asztalról csatlakozik a virtuális géphez, keressen rá a **Configuration Manager** kifejezésre:

    ![Az SSCM megnyitása](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. Az SQL Server Konfigurációkezelő konzolablakában bontsa ki az **SQL Server Network Configuration** elemet.

1. A konzolablakban kattintson az **MSSQLSERVER protokolljai** (az alapértelmezett példány neve) lehetőségre. A részleteket tartalmazó ablaktáblán kattintson a jobb gombbal a **TCP** elemre, majd az **Engedélyezés** lehetőségre, ha még nincs engedélyezve.

    ![A TCP engedélyezése](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. A konzolablakban kattintson az **SQL Server Services** lehetőségre. A részleteket tartalmazó ablaktáblán kattintson a jobb gombbal **SQL Server (*példánynév*)** (az alapértelmezett példány **SQL Server (MSSQLSERVER)**), és kattintson a **újraindítása**, hogy állítsa le és indítsa újra az SQL Server példányát.

    ![Az Adatbázismotor újraindítása](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Zárja be az SQL Server Konfigurációkezelőt.

További információ az SQL Server Adatbázismotor protokolljainak engedélyezéséről: [Kiszolgálói hálózati protokoll engedélyezése vagy letiltása](http://msdn.microsoft.com/library/ms191294.aspx).
