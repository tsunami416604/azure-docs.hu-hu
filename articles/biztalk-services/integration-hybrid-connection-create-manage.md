---
title: A hibrid kapcsolatok létrehozása és kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan hozzon létre egy hibrid kapcsolatot, a kapcsolat kezelése és a Hibridkapcsolat-kezelő telepítése. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
ms.assetid: aac0546b-3bae-41e0-b874-583491a395ea
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: ccompy
ms.openlocfilehash: 0a9197c8c46cc480f5f096f1d2ef98c0d5823f35
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455785"
---
# <a name="create-and-manage-hybrid-connections"></a>Hibridkapcsolatok létrehozása és felügyelete

> [!IMPORTANT]
> A BizTalk Hybrid Connections ki van vezetve, helyét az App Service Hybrid Connections vette át. További tájékoztatást egyebek között a meglévő BizTalk Hybrid Connections-kapcsolatok kezeléséről az [Azure App Service Hybrid Connections](../app-service/app-service-hybrid-connections.md) című cikkben talál.

>[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="overview-of-the-steps"></a>A lépések áttekintése
1. Írja be a hibrid kapcsolat létrehozása a **állomásnév** vagy **FQDN** a magánhálózaton lévő helyszíni erőforrás.
2. Az Azure web apps vagy az Azure mobile apps összekapcsolása a hibrid kapcsolat.
3. Telepítse a Hibridkapcsolat-kezelő a helyszíni erőforráshoz, és csatlakozzon az adott hibrid kapcsolat. Az Azure Portalon való telepítése, majd csatlakozás egy kattintással élményt nyújt.
4. Hibrid kapcsolatok és a kapcsolati kulcsok kezelése.

Ez a témakör felsorolja az alábbi lépéseket. 

> [!IMPORTANT]
> Célszerű lehet egy hibrid kapcsolati végpont IP-címet. Ha egy IP-címet használ, előfordulhat, hogy, vagy nem jut el a helyszíni erőforráshoz, az ügyfél függően. A hibrid kapcsolat az ügyfél egy DNS-címkeresés Ez függ. A legtöbb esetben a **ügyfél** van az alkalmazás kódjában. Ha az ügyfél nem végez egy DNS-címkeresés (ez nem próbálja meg feloldani az IP-cím, mintha egy tartomány nevét (x.x.x.x)), majd a forgalmat a rendszer nem küldi el a hibrid kapcsolaton keresztül.
> 
> Megadhat például (pseudocode) **10.4.5.6** a helyszíni gazdagépként:
> 
> **A következő esetben működik:**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on premises host`
> 
> **Az alábbi forgatókönyv nem működik:**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`
> 
> 

## <a name="CreateHybridConnection"></a>Hibrid kapcsolat létrehozása
A hibrid kapcsolat hozható létre [Azure App Service hibrid kapcsolataira](../app-service/app-service-hybrid-connections.md) **vagy** használatával [BizTalk Services – REST API-k](https://msdn.microsoft.com/library/azure/dn232347.aspx). 

<!-- **To create Hybrid Connections using Web Apps**, see [Connect Azure Web Apps to an On-Premises Resource](../app-service-web/web-sites-hybrid-connection-get-started.md). You can also install the Hybrid Connection Manager (HCM) from your web app, which is the preferred method.  -->

#### <a name="additional"></a>További
* Több hibrid kapcsolatot lehet létrehozni. Tekintse meg a [a BizTalk Services: Kiadások diagramja](biztalk-editions-feature-chart.md) a kapcsolatok engedélyezett számát. 
* Kapcsolati karakterláncok két minden hibrid kapcsolat jön létre: Alkalmazás kulcsainak, KÜLDÉSE és a helyszíni kulcsok, amelyek FIGYELIK. Minden átjárópárhoz egy elsődleges és másodlagos kulcsot tartalmaz. 

## <a name="LinkWebSite"></a>Az Azure App Service-webalkalmazás vagy hivatkozás
Egy webalkalmazás vagy az Azure App Service Mobile Apps összekapcsolása egy meglévő hibrid kapcsolatot, jelölje be a **egy létező hibrid kapcsolat használata** a hibrid kapcsolatok panelen. 
<!-- See [Access on-premises resources using hybrid connections in Azure App Service](../app-service-web/web-sites-hybrid-connection-get-started.md). -->

## <a name="InstallHCM"></a>Telepítse a Hibridkapcsolat-kezelő a helyszínen
Hibrid kapcsolat létrehozása után telepítse a Hibridkapcsolat-kezelő a helyszíni erőforráshoz. Az Azure-webalkalmazások, illetve a BizTalk-szolgáltatás letölthető. 

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]
 
[Az Azure App Service hibrid kapcsolataira](../app-service/app-service-hybrid-connections.md) a leghasznosabb is van.

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>További
* Hibridkapcsolat-kezelő a következő operációs rendszereken telepíthető:
  
  * A Windows Server 2008 R2 (a .NET-keretrendszer 4.5-ös + és a Windows Management Framework 4.0 + szükséges)
  * A Windows Server 2012 (Windows Management Framework 4.0 + szükséges)
  * Windows Server 2012 R2
* Miután telepítette a Hibridkapcsolat-kezelő, a következő történik: 
  
  * A hibrid kapcsolat az Azure-ban üzemeltetett automatikusan az alkalmazás elsődleges kapcsolati karakterlánc használatára van konfigurálva. 
  * Az On-Premises erőforrás automatikusan az On-Premises elsődleges kapcsolati karakterlánc használatára van konfigurálva.
* A Hibridkapcsolat-kezelő engedélyezési helyszíni érvényes kapcsolati karakterláncot kell használnia. Az Azure Web Apps vagy a Mobile Apps hitelesítést kell használnia egy érvényes kapcsolati karakterláncot.
* Hibrid kapcsolatok méretezhetők, ha egy másik példány a Hybrid Connection Manager telepítése egy másik kiszolgálóra. Konfigurálja a helyszíni figyelőt, hogy az első helyszíni figyelő használja ugyanazt a címet. Ebben a helyzetben a forgalmat a helyszíni active figyelői között a véletlenszerűen elosztott (ciklikus időszeletelés). 

## <a name="ManageHybridConnection"></a>A hibrid kapcsolatok kezelése

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

[Az Azure App Service hibrid kapcsolataira](../app-service/app-service-hybrid-connections.md) a leghasznosabb is van.

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>A hibrid kapcsolati karakterláncok másolása és újragenerálása

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

[Az Azure App Service hibrid kapcsolataira](../app-service/app-service-hybrid-connections.md) a leghasznosabb is van.

#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>Csoportházirend használata a hibrid kapcsolat által használt helyszíni erőforrások szabályozásához
1. Töltse le a [Hibridkapcsolat-kezelő felügyeleti sablonok](https://www.microsoft.com/download/details.aspx?id=42963).
2. Bontsa ki a fájlokat.
3. Azon a számítógépen, amely módosítja a csoportházirend tegye a következőket:  
   
   * Másolás a. ADMX fájljainak a *%WINROOT%\PolicyDefinitions* mappát.
   * Másolás a. ADML-fájlokat a *%WINROOT%\PolicyDefinitions\en-us* mappát.

Ha már másolva lettek, a Helyicsoportházirend-szerkesztő használatával módosítsa a házirendet.

## <a name="next"></a>Következő lépés
[A hibrid kapcsolatok áttekintése](integration-hybrid-connection-overview.md)

## <a name="see-also"></a>Lásd még:
[REST API a BizTalk Services felügyeletéhez a Microsoft Azure-ban](https://msdn.microsoft.com/library/azure/dn232347.aspx)  
[A BizTalk Services: Kiadások diagramja](biztalk-editions-feature-chart.md)  
[BizTalk-szolgáltatás létrehozása](biztalk-provision-services.md)  
[A BizTalk Services: Irányítópult, figyelés és méret lapok](biztalk-dashboard-monitor-scale-tabs.md)

[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 
