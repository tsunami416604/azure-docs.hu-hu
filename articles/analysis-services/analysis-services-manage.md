---
title: Azure Analysis Services kezelése | Microsoft dokumentumok
description: Ez a cikk az Azure Analysis Services-kiszolgáló felügyeleti és felügyeleti feladatainak kezeléséhez használt eszközöket ismerteti.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 28d7b2955c84833841760e441cd2919181e22bc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572794"
---
# <a name="manage-analysis-services"></a>Az Analysis Services felügyelete
Miután létrehozott egy Analysis Services-kiszolgálót az Azure-ban, előfordulhat, hogy néhány felügyeleti és felügyeleti feladatot azonnal el kell végeznie, vagy valamikor az úton lefelé. Futtassa például a feldolgozást a frissítési adatokra, szabályozhatja, hogy ki férhet hozzá a kiszolgálón lévő modellekhez, vagy figyelheti a kiszolgáló állapotát. Egyes felügyeleti feladatok csak az Azure Portalon, mások az SQL Server Management Studio (SSMS), és bizonyos feladatok at lehet végezni bármelyik.

## <a name="azure-portal"></a>Azure portál
[Az Azure Portal](https://portal.azure.com/) az a hely, ahol kiszolgálókat hozhat létre és törölhet, figyelheti a kiszolgálók erőforrásait, módosíthatja a méretet, és kezelheti, hogy ki férhet hozzá a kiszolgálókhoz.  Ha valamilyen problémája van, támogatási kérelmet is benyújthat.

![A kiszolgáló nevének lekérése az Azure-ban](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Az Azure-beli kiszolgálóhoz való csatlakozás olyan, mintha a saját szervezetében lévő kiszolgálópéldányhoz csatlakozna. Az SSMS-ből számos ugyanazokat a feladatokat hajthathatja végre, például az adatok feldolgozását, vagy létrehozhat egy feldolgozási parancsfájlt, kezelheti a szerepköröket, és használhatja a PowerShellt.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Az SSMS letöltése és telepítése
A legújabb funkciók és a legsimább élmény az Azure Analysis Services-kiszolgálóhoz való csatlakozáskor, győződjön meg arról, hogy az SSMS legújabb verzióját használja. 

[Töltse le az SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)alkalmazást.


### <a name="to-connect-with-ssms"></a>Csatlakozás az SSMS-hez
 Az SSMS használatakor, mielőtt első alkalommal csatlakozna a kiszolgálóhoz, győződjön meg arról, hogy a felhasználóneve szerepel az Analysis Services Rendszergazdák csoportjában. További információ: A cikk későbbi részében a [Kiszolgáló rendszergazdái és az adatbázis-felhasználók című](#server-administrators-and-database-users) témakörben olvashat.

1. A csatlakozás előtt be kell szereznie a kiszolgáló nevét. Másolja a kiszolgáló nevét az **Azure Portal** > kiszolgáló > **Áttekintés** > **Kiszolgálónév** részéből.
   
    ![A kiszolgáló nevének lekérése az Azure-ban](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. Az SSMS > **Object Explorerben** kattintson a **Csatlakozás** > **Analysis Services** lehetőségre.
3. A **Csatlakozás kiszolgálóhoz** párbeszédpanelen illessze be a kiszolgáló nevét, majd a **Hitelesítés**mezőbe válassza az alábbi hitelesítési típusok egyikét:   
    > [!NOTE]
    > Hitelesítési típus, **Active Directory - Univerzális MFA-támogatással**ajánlott.

    > [!NOTE]
    > Ha Microsoft-fiókkal, Live ID azonosítóval, Yahoo-val, Gmail-lel stb. A Csatlakozás gombra való kattintás után a rendszer jelszót kér.

    **Windows-hitelesítés** a Windows-tartomány\felhasználónév és jelszó hitelesítő adatainak használatához.

    **Active Directory jelszó-hitelesítés** szervezeti fiók használatához. Ha például nem tartományhoz csatlakozó számítógépről csatlakozik.

    **Active Directory – Univerzális, többtényezős hitelesítéssel támogatott MFA-támogatással** a [nem interaktív vagy többtényezős hitelesítés használatához.](../sql-database/sql-database-ssms-mfa-authentication.md) 
   
    ![Csatlakozás az SSMS-ben](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Kiszolgálórendszergazdák és adatbázis-felhasználók
Az Azure Analysis Services ben kétféle felhasználó, kiszolgáló-rendszergazdák és adatbázis-felhasználók vannak. Mindkét típusú felhasználónak az Azure Active Directoryban kell lennie, és szervezeti e-mail-cím vagy upn által kell megadni. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Csatlakozási problémák elhárítása
Ha az SSMS használatával csatlakozik, ha problémákba ütközik, előfordulhat, hogy törölnie kell a bejelentkezési gyorsítótárat. Semmi sincs elrejtve a lemezre. A gyorsítótár törléséhez zárja be és indítsa újra a kapcsolódási folyamatot. 

## <a name="next-steps"></a>További lépések
Ha még nem telepített táblázatos modellt az új kiszolgálóra, most van itt az ideje. További információkért lásd [az Azure Analysis Servicesben történő üzembe helyezést](analysis-services-deploy.md) ismertető cikket.

Ha telepített egy modellt a kiszolgálóra, készen áll arra, hogy ügyfélvagy böngésző használatával csatlakozzon hozzá. További információ: [Adatok beszedése az Azure Analysis Services-kiszolgálóról](analysis-services-connect.md).

