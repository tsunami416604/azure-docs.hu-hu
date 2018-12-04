---
title: Azure Analysis Services kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti az Azure Analysis Services-kiszolgáló.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 536ae926ad8f401f952a41e17074536cc2b3c5f1
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52832037"
---
# <a name="manage-analysis-services"></a>Analysis Services kezelése
Miután az Azure-ban létrehozott egy Analysis Services-kiszolgálón, néhány felügyelet és kezelés feladatot kell elvégeznie a azonnal, vagy egy ideig le lehet. Például futtassa a frissítés adatokhoz, el tudja érni a modellek a kiszolgálón, vagy a kiszolgáló állapotának figyeléséhez vezérléséhez feldolgozása. Egyes felügyeleti feladatok csak az Azure Portalon, az SQL Server Management Studio (SSMS), mások hajtható végre, és néhány feladatot végezhető, vagy.

## <a name="azure-portal"></a>Azure Portal
[Az Azure portal](https://portal.azure.com/) is hozzon létre és kiszolgálók törlése, figyelheti a kiszolgáló-erőforrásokon, méretének módosítása, és ki férhet hozzá a kiszolgálók kezelése.  Ha problémákat tapasztal, küldje el egy támogatási kérést.

![A kiszolgáló nevének lekérése az Azure-ban](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Kapcsolódás a kiszolgálóhoz az Azure-ban van, csakúgy, mint egy kiszolgálópéldányt, saját cégen belüli csatlakozik. Az ssms-ben hajtsa végre számos ugyanazokat a műveleteket, például az adatok feldolgozásához, vagy feldolgozási parancsfájl létrehozása, szerepkörök kezelése és PowerShell-lel.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Töltse le és telepítse az ssms használatával
Töltse le a legújabb funkciókról és a legegyenletesebb élmény az Azure Analysis Services-kiszolgálóhoz való csatlakozáshoz, az SSMS legújabb verzióját használja-e lennie. 

[Töltse le az SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Csatlakozás az ssms használatával
 Ssms-t, mielőtt a kiszolgálóhoz való kapcsolódás első alkalommal használatakor ellenőrizze, hogy a felhasználónevét az Analysis Services-rendszergazdák csoport része. További tudnivalókért lásd: [kiszolgálói rendszergazdák](#server-administrators) a cikk későbbi részében.

1. Csatlakozás előtt kell a kiszolgáló nevét. Másolja a kiszolgáló nevét az **Azure Portal** > kiszolgáló > **Áttekintés** > **Kiszolgálónév** részéből.
   
    ![A kiszolgáló nevének lekérése az Azure-ban](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. Az SSMS > **Object Explorerben** kattintson a **Csatlakozás** > **Analysis Services** lehetőségre.
3. Az a **kapcsolódás a kiszolgálóhoz** párbeszédablakban illessze be a kiszolgáló nevét, majd a **hitelesítési**, válasszon a következő hitelesítési típusok közül:   
    > [!NOTE]
    > Hitelesítés típusa, **Active Directory - MFA-támogatással rendelkező univerzális**, ajánlott.

    > [!NOTE]
    > Ha egy Microsoft-Account, Live ID, Yahoo, Gmail, stb. jelentkezik be, a jelszó mezőt üresen. Kattint, a csatlakozás után a jelszó megerősítését.

    **Windows-hitelesítés** , a Windows tartomány\felhasználónév és a jelszó hitelesítő adatok használatával.

    **Active Directory jelszavas hitelesítést** használjon szervezeti fiókot. Például, ha csatlakozik egy tartományhoz csatlakoztatott számítógépre.

    **Az Active Directory - Universal MFA-támogatással rendelkező** használandó [nem interaktív vagy a multi-factor authentication](../sql-database/sql-database-ssms-mfa-authentication.md). 
   
    ![Csatlakozás az SSMS-ben](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Kiszolgáló-rendszergazdák és az adatbázis-felhasználók
Az Azure Analysis Servicesben, két típusa van a felhasználók, a kiszolgálói rendszergazdák és az adatbázis-felhasználók. Mindkét típusú felhasználók az Azure Active Directoryban kell lennie, és meg kell adni a munkahelyi e-mail-címét vagy egyszerű felhasználónév alapján. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>A kapcsolódási problémák elhárításához
Amikor az ssms-ben, abban az esetben, ha hibákba ütközne, szükség lehet a bejelentkezési gyorsítótár kiürítése. Semmi nem gyorsítótárazott lemezre. A gyorsítótár kiürítése, zárja be és indítsa újra a connect folyamatot. 

## <a name="next-steps"></a>További lépések
Ha az új kiszolgáló már nem léptetett életbe egy táblázatos modellhez, most már van egy időben. További információkért lásd [az Azure Analysis Servicesben történő üzembe helyezést](analysis-services-deploy.md) ismertető cikket.

Ha egy modellt a kiszolgálóra telepített, készen áll egy ügyfél vagy a böngésző használatával csatlakozni. További tudnivalókért lásd: [le az adatokat az Azure Analysis Services-kiszolgáló](analysis-services-connect.md).

