---
title: Hozzon létre és hibrid kapcsolatok kezelése |} Microsoft Docs
description: Tudnivalók a hibrid kapcsolat létrehozása, a kapcsolat kezelése és a Hybrid Connection Manager telepítéséhez. MABS, WABS
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
ms.openlocfilehash: 1751d33b5f6f6a506654daedd15bbd75ae271483
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
ms.locfileid: "26628846"
---
# <a name="create-and-manage-hybrid-connections"></a>Hibridkapcsolatok létrehozása és felügyelete

> [!IMPORTANT]
> A BizTalk Hybrid Connections ki van vezetve, helyét az App Service Hybrid Connections vette át. További tájékoztatást egyebek között a meglévő BizTalk Hybrid Connections-kapcsolatok kezeléséről az [Azure App Service Hybrid Connections](../app-service/app-service-hybrid-connections.md) című cikkben talál.

>[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="overview-of-the-steps"></a>Lépéseinek áttekintése
1. Írja be a hibrid kapcsolat létrehozása a **állomásnév** vagy **FQDN** a magánhálózaton a helyi erőforrás.
2. Az Azure-webalkalmazásokban vagy az Azure mobile apps csatolja a hibrid kapcsolat.
3. A Hybrid Connection Manager telepíthető a helyi erőforrás, és csatlakozzon az adott hibrid kapcsolat. Az Azure-portál telepítése és csatlakozás kattintással élményt nyújt.
4. Hibrid kapcsolatok és a kapcsolat kulcsok kezelése.

Ez a témakör az alábbi lépéseket. 

> [!IMPORTANT]
> Akkor lehet beállítani a hibrid kapcsolat végpontjának IP-címet. Ha egy IP-címet használ, előfordulhat, hogy, vagy attól függően, hogy az ügyfél helyszíni erőforrás nem jut el. A hibrid kapcsolat attól függ, hogy az ügyfél DNS-címkeresést történt. A legtöbb esetben a **ügyfél** van az alkalmazás kódjában. Ha az ügyfél nem végez egy DNS-címkeresés (ez nem megpróbálja feloldani az IP-cím, mintha egy tartomány nevét (x.x.x.x)), majd a forgalmat nem küldi el a hibrid kapcsolaton keresztül.
> 
> Megadhatja például (pseudocode) **10.4.5.6** helyszíni gazdakörnyezeteként:
> 
> **Az alábbi forgatókönyvet működik:**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on-prem host`
> 
> **A következő forgatókönyv nem működik:**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`
> 
> 

## <a name="CreateHybridConnection"></a>A hibrid kapcsolat létrehozása
A hibrid kapcsolat hozhatók létre [Azure App Service hibrid kapcsolatok](../app-service/app-service-hybrid-connections.md) **vagy** használatával [BizTalk szolgáltatás REST API-k](https://msdn.microsoft.com/library/azure/dn232347.aspx). 

<!-- **To create Hybrid Connections using Web Apps**, see [Connect Azure Web Apps to an On-Premises Resource](../app-service-web/web-sites-hybrid-connection-get-started.md). You can also install the Hybrid Connection Manager (HCM) from your web app, which is the preferred method.  -->

#### <a name="additional"></a>További
* Több hibrid kapcsolatok hozhatók létre. Tekintse meg a [BizTalk szolgáltatások: kiadások diagram](biztalk-editions-feature-chart.md) az engedélyezett kapcsolatok számát. 
* Minden egyes hibrid kapcsolat jön létre a kapcsolati karakterláncok két: alkalmazás kulcsok, a KÜLDÉS és a helyszíni kulcsok figyelő. Minden párt tartalmaz egy elsődleges és másodlagos kulcsot. 

## <a name="LinkWebSite"></a>Az Azure App Service webalkalmazás vagy mobilalkalmazás hivatkozás
A webes alkalmazás vagy az Azure App Service Mobile Apps összekapcsolása egy meglévő hibrid kapcsolat, válassza ki a **meglévő hibrid kapcsolat használata** a hibrid kapcsolatok paneljén. 
<!-- See [Access on-premises resources using hybrid connections in Azure App Service](../app-service-web/web-sites-hybrid-connection-get-started.md). -->

## <a name="InstallHCM"></a>A hibrid Csatlakozáskezelő helyszíni telepítése
A hibrid kapcsolat létrehozása után a Hybrid Connection Manager telepíthető a helyi erőforrás. Az Azure-webalkalmazásokban, illetve a BizTalk szolgáltatás is letölthetők. 

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]
 
[Az Azure App Service hibrid kapcsolatok](../app-service/app-service-hybrid-connections.md) jó erőforrásként is szerepel.

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>További
* Hybrid Connection Manager a következő operációs rendszerekre telepíthető:
  
  * Windows Server 2008 R2 (a .NET-keretrendszer 4.5-ös + és a Windows Management Framework 4.0-s vagy újabb verzió szükséges)
  * Windows Server 2012 (a Windows Management Framework 4.0-s vagy újabb verzió szükséges)
  * Windows Server 2012 R2
* Miután telepítette a Hybrid Connection Manager, az alábbiak történnek: 
  
  * A hibrid kapcsolat az Azure-platformon futó automatikusan az alkalmazás elsődleges kapcsolódási karakterlánc használatára van konfigurálva. 
  * Az On-Premises erőforrás automatikusan az elsődleges helyszíni kapcsolati karakterlánc használatára van konfigurálva.
* A Hybrid Connection Manager engedélyezési érvényes a helyszíni kapcsolati karakterláncot kell használnia. Az Azure Web Apps vagy a Mobile Apps engedélyezési érvényes kapcsolati karakterláncot kell használnia.
* Hibrid kapcsolatok egy másik kiszolgálón egy másik példány a Hybrid Connection Manager telepítésével méretezheti. A helyszíni figyelő ugyanazt a címet az első helyszíni figyelőként használatára konfigurálja. Ebben a helyzetben a forgalmat a helyszíni active figyelői között a véletlenszerűen elosztott (ciklikus időszeletelés). 

## <a name="ManageHybridConnection"></a>Hibrid kapcsolatok kezelése

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

[Az Azure App Service hibrid kapcsolatok](../app-service/app-service-hybrid-connections.md) jó erőforrásként is szerepel.

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>A hibrid kapcsolati karakterláncok másolási/újragenerálása

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

[Az Azure App Service hibrid kapcsolatok](../app-service/app-service-hybrid-connections.md) jó erőforrásként is szerepel.

#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>A csoportházirendben szabályozhatja a hibrid kapcsolat által használt helyi erőforrások
1. Töltse le a [hibrid Csatlakozáskezelő felügyeleti sablonok](http://www.microsoft.com/download/details.aspx?id=42963).
2. Bontsa ki a fájlokat.
3. A számítógépen, amely módosítja a csoportházirend tegye a következőket:  
   
   * Másolás a. ADMX fájljainak a *%WINROOT%\PolicyDefinitions* mappa.
   * Másolás a. ADML-fájlokat a *%WINROOT%\PolicyDefinitions\en-us* mappa.

A másolást követően a Helyicsoportházirend-szerkesztő segítségével módosítsa a házirendet.

## <a name="next"></a>Következő lépés
[Hibrid kapcsolatok – áttekintés](integration-hybrid-connection-overview.md)

## <a name="see-also"></a>Lásd még:
[A Microsoft Azure BizTalk szolgáltatások kezelésére szolgáló REST API-n](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[BizTalk Services: Kiadások diagramja](biztalk-editions-feature-chart.md)  
[BizTalk szolgáltatás létrehozása](biztalk-provision-services.md)  
[BizTalk Services: Irányítópult, Figyelés és Méret lapok](biztalk-dashboard-monitor-scale-tabs.md)

[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 
