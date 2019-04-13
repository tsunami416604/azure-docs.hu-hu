---
title: SQL Servert futtató, Windows rendszerű virtuális gép létrehozása a portálon | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Windows rendszerű SQL Server 2017 virtuális gépet az Azure Portalon.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c9e62cf3dfc259d7064be9fd6b80cad0140336eb
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549269"
---
# <a name="quickstart-create-a-sql-server-2017-windows-virtual-machine-in-the-azure-portal"></a>Gyors útmutató: SQL Server 2017-et futtató, Windows rendszerű virtuális gép létrehozása az Azure Portalon

> [!div class="op_single_selector"]
> * [Windows](quickstart-sql-vm-create-portal.md)
> * [Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)

Ez a rövid útmutató részletesen bemutatja egy SQL Servert futtató virtuális gép létrehozását az Azure Portalon.


  > [!TIP]
  > - Ez a rövid útmutató bemutatja az SQL virtuális gépek gyors kiépítését és az azokhoz való csatlakozást. Az SQL virtuális gépek többi kiépítési lehetőségéről további információért tekintse meg a [Windows SQL Server virtuális gépek Azure Portalon való kiépítésének útmutatóját](virtual-machines-windows-portal-sql-server-provision.md).
  > - Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](virtual-machines-windows-sql-server-iaas-faq.md).

## <a id="subscription"></a> Azure-előfizetés beszerzése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a id="select"></a> SQL Server virtuálisgép-rendszerkép kiválasztása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) fiókjával.

1. Az Azure Portalon válassza az **Erőforrás létrehozása** lehetőséget. 

1. A keresőmezőbe írja be a `SQL Server 2017 Developer on Windows Server 2016`, és nyomja le az ENTER billentyűt.

1. Válassza ki a **ingyenes SQL Server-licenc: Az SQL Server 2017 Developer Windows Server 2016 rendszeren** kép. Ez az oktatóanyag azért a Developer kiadást alkalmazza, mert az az SQL Server teljes körű verziója, amely fejlesztési-tesztelési célokra ingyenesen használható. Csak a virtuális gép futtatásával járó költségeket kell kifizetni. A díjszabással kapcsolatos teljes körű információkért tekintse meg [az SQL Server Azure virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md).

   ![Új keresési ablak](./media/quickstart-sql-vm-create-portal/newsearch.png)


1. Kattintson a **Létrehozás** gombra.

## <a id="configure"></a> Az alapvető adatok megadása

Az a **alapjai** lapra, adja meg a következő információkat:

1. Az a **Project Details** szakaszban, válassza ki az Azure-előfizetésében, majd válassza ki **új létrehozása** egy új erőforráscsoport létrehozásához. Típus _SQLVM-RG_ neve.

   ![Előfizetés](media/quickstart-sql-vm-create-portal/basics-project-details.png)

1. A **példány részletei**:
    1. Típus _SQLVM_ számára a **virtuális gép neve**. 
    1. Válasszon egy helyet a **régió**. 
    1. A rövid útmutató során hagyja **rendelkezésre állási beállítások** beállítása _szükséges infrastruktúra redundancia_. Rendelkezésre állási beállításokkal kapcsolatos további információkért, lásd: [Azure-régiók és rendelkezésre állási](../../windows/regions-and-availability.md). 
    1. Az a **kép** listáról válassza ki _ingyenes SQL Server-licenc: SQL Server 2017 Developer Windows Server 2016 rendszeren_. 
    1. Válassza ki a **méretének módosítása** a a **mérete** , a virtuális gépre, majd válassza a **a2-es alapszintű** ajánlat. Ügyeljen arra, hogy miután végzett az minden olyan váratlan költségek megelőzése érdekében az erőforrások törlése. 

   ![Példány részletei](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

1. A **rendszergazdai fiók**, adjon meg egy felhasználónevet, pl. _azureuser_ és a egy jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Rendszergazdai fiók](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

1. Alatt **bejövőport-szabályok**, válassza a **lehetővé teszi a kiválasztott portok** , majd **RDP (3389-es)** a legördülő listából. 

   ![Bejövőport-szabályok](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)

## <a name="sql-server-settings"></a>SQL Server beállításai

Az a **SQL Server-beállítások** lapra, konfigurálja a következő beállításokat:

1. Alatt **biztonsági és hálózatkezelési**, jelölje be _nyilvános (Internet_) a **SQL-kapcsolat** , és módosítsa a portot, hogy `1401` elkerülése érdekében a jól ismert portszámot használ a nyilvános forgatókönyvben. 
1. A **SQL-hitelesítés**válassza **engedélyezése**. Az SQL-bejelentkezés ugyanarra a felhasználónévre és jelszóra van állítva, amelyet a virtuális géphez konfigurált. Az alapértelmezett beállítások használata **Azure Key Vault-integráció** és **tárolási konfigurációt**.  

   ![SQL server biztonsági beállításai](media/quickstart-sql-vm-create-portal/sql-server-settings.png)

1. Módosítsa az egyéb beállításokat, ha szükséges, és válassza ki **felülvizsgálat + létrehozása**. 

   ![Áttekintés + létrehozás](media/quickstart-sql-vm-create-portal/review-create.png)


## <a name="create-the-sql-server-vm"></a>Az SQL Servert futtató virtuális gép létrehozása

Az a **tekintse át + létrehozása** lapon tekintse át az összefoglalást, és válassza ki **létrehozás** hozhat létre SQL Server, erőforráscsoport és erőforrások virtuális Géphez megadott.

Az üzemelő példány az Azure Portalról monitorozható. A képernyő felső részén látható **Értesítések** gomb megjeleníti az üzemelő példány állapotának alapvető információit. Üzembe helyezés több percet is igénybe vehet. 

## <a name="connect-to-sql-server"></a>Csatlakozás az SQL Serverhez

1. A portálon keresse meg a **nyilvános IP-cím** az SQL Server virtuálisgép-a **áttekintése** a virtuális gép tulajdonságok szakaszában.

1. Nyissa meg egy másik számítógépre, csatlakozik az internethez, [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).


1. A **Kapcsolódás a kiszolgálóhoz** vagy a **Kapcsolódás az adatbázismotorhoz** párbeszédpanelen szerkessze a **Kiszolgáló neve** értéket. Adja meg a virtuális gép nyilvános IP-címét. A név után írjon egy vesszőt, és adja meg az egyéni portot (**1401**), amelyet korábban az új virtuális gép konfigurálásakor beállítottunk. Például: `11.22.33.444,1401`.

1. A **Hitelesítés** mezőben válassza az **SQL Server-hitelesítés** lehetőséget.

1. A **Bejelentkezés** szövegmezőbe írjon be egy érvényes SQL-bejelentkezési nevet.

1. A **Jelszó** szövegmezőbe írja be a bejelentkezési jelszót.

1. Kattintson a **Csatlakozás** gombra.

    ![ssms connect](./media/quickstart-sql-vm-create-portal/ssms-connect.png)

## <a id="remotedesktop"></a> Távoli bejelentkezés a virtuális gépre

A következő lépésekkel csatlakozzon az SQL Server virtuális géphez a távoli asztalról:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Az SQL Server virtuális géphez való csatlakozás után elindíthatja az SQL Server Management Studiót, és a helyi rendszergazdai hitelesítő adataival csatlakozhat Windows-hitelesítés használatával. Ha engedélyezte az SQL Server-hitelesítést, akkor az SQL-hitelesítésen keresztül is csatlakozhat a kiépítés során megadott SQL bejelentkezési azonosítójával és jelszavával.

A géphez való hozzáférés lehetővé teszi, hogy igény szerint közvetlenül módosítsa a gép és az SQL Server beállításait. Például konfigurálhatja a tűzfal beállításait, vagy módosíthatja az SQL Server-konfiguráció beállításait.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem szükséges, hogy az SQL virtuális gép folyamatosan fusson, a szükségtelen költségeket elkerülendő leállíthatja az épp használaton kívüli gépet. Emellett véglegesen törölheti a virtuális géppel társított erőforrásokat, ha törli a társított erőforráscsoportot a portálon. Ez véglegesen törli magát a virtuális gépet is, ezért ezt a parancsot körültekintően alkalmazza. További információk: [Azure-erőforrások kezelése a portálon keresztül](../../../azure-resource-manager/manage-resource-groups-portal.md).


## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy SQL Server 2017 virtuális gépet az Azure Portalon. Az adatok az új SQL Serverre való migrálásával kapcsolatos további információkért lásd a következő cikket.

> [!div class="nextstepaction"]
> [Adatbázisok migrálása SQL virtuális gépekre](virtual-machines-windows-migrate-sql.md)
