---
title: "Az Azure Analysis Services kezelése |} Microsoft Docs"
description: "Útmutató az Azure Analysis Services-kiszolgáló kezelhető."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 79491d0b-b00d-4e02-9ca7-adc99bc02fdb
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: 11aa42fbe15dd68dfac128b707fd6bdbfd0b24d1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="manage-analysis-services"></a>Manage Analysis Services
Ha az Analysis Services-kiszolgáló létrehozta az Azure-ban, néhány adminisztrációs és kezelőkonzol feladatot kell elvégeznie a azonnal, vagy valamikor le lehet. Például futtassa a frissítési adatokhoz, személyek képes hozzáférni a modelleket a kiszolgálón, vagy a kiszolgáló állapotának figyeléséhez feldolgozása. Egyes felügyeleti feladatok csak az Azure portálon, az SQL Server Management Studio (SSMS), mások hajtható végre, és néhány feladatot teheti meg.

## <a name="azure-portal"></a>Azure Portal
[Azure-portálon](http://portal.azure.com/) is hozzon létre és törölhetnek kiszolgálókat, figyelheti a kiszolgáló erőforrásait, Oldalméret módosítása oly módon, és kik férhetnek hozzá a kiszolgálók kezeléséhez.  Ha olyan problémákat tapasztal, is küldhet a támogatási kérelmet.

![A kiszolgáló nevének lekérése az Azure-ban](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Csakúgy, mint a server-példány a saját szervezetben való kapcsolódás a kiszolgálóhoz, az Azure-ban való kapcsolódás van. Az SSMS a nagy ugyanazokhoz a feladatokhoz, például a folyamat vagy hozzon létre egy feldolgozási parancsprogramot, szerepkörök kezelése és használhatja a PowerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Töltse le és telepítse az SSMS
Ahhoz, hogy a legújabb szolgáltatásokhoz és a legegyenletesebb tapasztalattal az Azure Analysis Services-kiszolgálóhoz való csatlakozáskor, győződjön meg az SSMS legújabb verziójának használata. 

[Töltse le az SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Csatlakozás az ssms alkalmazásával
 SSMS, használata előtt először a kiszolgáló csatlakozik, ellenőrizze, a felhasználónév megtalálható az Analysis Services-rendszergazdák csoport. További tudnivalókért lásd: [rendszergazdái](#server-administrators) című cikkben.

1. Csatlakozás előtt kell a kiszolgáló nevét. Másolja a kiszolgáló nevét az **Azure Portal** > kiszolgáló > **Áttekintés** > **Kiszolgálónév** részéből.
   
    ![A kiszolgáló nevének lekérése az Azure-ban](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. Az SSMS > **Object Explorer**, kattintson a **Connect** > **Analysis Services**.
3. Az a **kapcsolódás a kiszolgálóhoz** párbeszédpanelen illessze be a kiszolgáló nevét, majd a **hitelesítési**, válassza a következő hitelesítési típusok egyikét:
   
    **Windows-hitelesítés** használni a Windows tartomány\felhasználónév és jelszó hitelesítő adatokat.

    **Active Directory jelszavas hitelesítést** szervezeti fiók használatára. Például ha egy a tartományhoz nem csatlakozó csatlakozó számítógép.

    **Active Directory univerzális hitelesítési** használandó [nem interaktív vagy a multi-factor authentication](../sql-database/sql-database-ssms-mfa-authentication.md). 
   
    ![Csatlakozás a szolgáltatáshoz az ssms](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Kiszolgáló-rendszergazdák és adatbázis-felhasználók
Az Azure Analysis Services, két típusa van a felhasználók, a kiszolgáló-rendszergazdák és az adatbázis-felhasználók. Mindkét típusú felhasználók kell lennie az Azure Active Directoryban, és a szervezeti e-mail címet, vagy az egyszerű Felhasználónevet kell megadni. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Kapcsolati problémák elhárítása
Ha SSMS, ha problémát tapasztal, szükség lehet a bejelentkezési gyorsítótár. Nem gyorsítótárazott lemezre. A gyorsítótár kiürítése, zárja be, és újra kell indítani a csatlakozás. 

## <a name="next-steps"></a>További lépések
Ha egy táblázatos modell már még nem telepítette, az új kiszolgálóhoz, most már van egy időben. További információkért lásd [az Azure Analysis Servicesben történő üzembe helyezést](analysis-services-deploy.md) ismertető cikket.

Ha a kiszolgáló telepítése után a modell, készen áll egy ügyfél vagy a böngésző használatával kapcsolódik hozzá. További tudnivalókért lásd: [adatok beolvasása az Azure Analysis Services-kiszolgáló](analysis-services-connect.md).

