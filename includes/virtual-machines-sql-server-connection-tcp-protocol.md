---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 8b919608dfc562d8db77619d5215a6828a53a4aa
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263913"
---
1. Miközben a távoli asztalról csatlakozik a virtuális géphez, keressen rá a **Configuration Manager** kifejezésre:

    ![Az SSCM megnyitása](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. Az SQL Server Konfigurációkezelő konzolablakában bontsa ki az **SQL Server Network Configuration** elemet.

1. A konzolablakban kattintson az **MSSQLSERVER protokolljai** (az alapértelmezett példány neve) lehetőségre. A részleteket tartalmazó ablaktáblán kattintson a jobb gombbal a **TCP** elemre, majd az **Engedélyezés** lehetőségre, ha még nincs engedélyezve.

    ![A TCP engedélyezése](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. A konzolablakban kattintson az **SQL Server Services** lehetőségre. A részleteket tartalmazó ablaktáblán kattintson a jobb gombbal **SQL Server (*példánynév*)** (az alapértelmezett példány **SQL Server (MSSQLSERVER)**), és kattintson a **újraindítása**, hogy állítsa le és indítsa újra az SQL Server példányát.

    ![Az Adatbázismotor újraindítása](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Zárja be az SQL Server Konfigurációkezelőt.

További információ az SQL Server Adatbázismotor protokolljainak engedélyezéséről: [Kiszolgálói hálózati protokoll engedélyezése vagy letiltása](https://msdn.microsoft.com/library/ms191294.aspx).
