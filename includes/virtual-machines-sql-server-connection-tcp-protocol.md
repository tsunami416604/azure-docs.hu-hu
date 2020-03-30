---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 8b919608dfc562d8db77619d5215a6828a53a4aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179061"
---
1. Miközben a távoli asztalról csatlakozik a virtuális géphez, keressen rá a **Configuration Manager** kifejezésre:

    ![Az SSCM megnyitása](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. Az SQL Server Konfigurációkezelő konzolablakában bontsa ki az **SQL Server Network Configuration** elemet.

1. A konzolablaktáblán kattintson **az MSSQLSERVER protokolljai** (az alapértelmezett példánynév) elemre. A jobb oldali ablaktáblában kattintson a jobb gombbal a **TCP** elemre, és kattintson az **Engedélyezés** parancsra, ha még nincs engedélyezve.

    ![A TCP engedélyezése](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. A konzolablakban kattintson az **SQL Server Services** lehetőségre. A jobb oldali ablaktáblában kattintson a jobb gombbal az **SQL Server *(példányneve)*** elemre (az alapértelmezett példány az **SQL Server (MSSQLSERVER),** majd kattintson az **Újraindítás**parancsra az SQL Server példányának leállításához és újraindításához.

    ![Az Adatbázismotor újraindítása](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Zárja be az SQL Server Konfigurációkezelőt.

További információ az SQL Server Adatbázismotor protokolljainak engedélyezéséről: [Kiszolgálói hálózati protokoll engedélyezése vagy letiltása](https://msdn.microsoft.com/library/ms191294.aspx).
