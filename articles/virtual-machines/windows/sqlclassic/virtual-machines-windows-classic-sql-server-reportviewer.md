---
title: Használja a ReportViewer webhely |} Microsoft Docs
description: Ez a témakör ismerteti, hogyan hozhat létre a Visual Studio ReportViewer vezérlő, amely a Microsoft Azure virtuális gépeket tárolt jelentés megjeleníti a Microsoft Azure webhely.
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 78b76318-d9bf-48ef-9d9e-d1b7d8cf3042
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: maghan
ms.openlocfilehash: af8a4a9c25005925bed3ddb78ced618e669f7f09
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>A ReportViewer használata Azure-ban üzemeltetett webhelyen
> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

A Visual Studio ReportViewer vezérlő, amely a Microsoft Azure virtuális gépeket tárolt jelentés megjeleníti a Microsoft Azure webhely hozhat létre. A ReportViewer vezérlőelem egy webes alkalmazás használatával az ASP.NET webalkalmazás-sablon létrehozása.

> [!IMPORTANT]
> Az ASP.NET MVC webalkalmazás sablonok nem támogatják a ReportViewer vezérlő.

A Microsoft Azure webhely funkcióját beépítse ReportViewer, hajtsa végre a következő feladatokat kell.

* **Adja hozzá** szerelvényeket a központi telepítési csomaghoz
* **Konfigurálása** hitelesítés és engedélyezés
* **Közzététel** az ASP.NET webalkalmazás az Azure-bA

## <a name="prerequisites"></a>Előfeltételek
Tekintse át a "általános javasolt és ajánlott eljárások" részt [SQL Server Business Intelligence Azure virtuális gépek](../classic/ps-sql-bi.md).

> [!NOTE]
> A Visual Studio, a Standard Edition vagy újabb mellékeltük a ReportViewer vezérlő. Ha a Web Developer Express Edition használ, telepítenie kell a [MICROSOFT jelentés VIEWER 2012 FUTÁSIDEJŰ](https://www.microsoft.com/download/details.aspx?id=35747) a ReportViewer futásidejű szolgáltatásai.
> 
> Helyi feldolgozási üzemmód ReportViewer nem támogatott a Microsoft Azure-ban.

## <a name="adding-assemblies-to-the-deployment-package"></a>Szerelvények hozzáadása a központi telepítési csomag
Ha ASP.NET alkalmazás a helyszíni, a ReportViewer szerelvényeket a globális szerelvény-gyorsítótárban (GAC), az IIS-kiszolgálót közvetlenül a Visual Studio telepítése során általában telepített, és közvetlenül az alkalmazás által elérhető. Azonban ha az ASP.NET-alkalmazás a felhőben, Microsoft Azure nem engedélyezi a GAC-ban történő telepíteni, győződjön meg arról, hogy a ReportViewer szerelvények érhetők el helyileg az alkalmazás semmit. Ehhez a projekt hivatkozik rájuk hozzáadásával, és úgy konfigurálja azokat, helyileg kell átmásolnia.

Távoli feldolgozás módban a ReportViewer vezérlő használja a következő szerelvények:

* **Microsoft.ReportViewer.WebForms.dll**: a ReportViewer kódot tartalmaz, amelyek kell használnia a ReportViewer a lapon. Az ASP.NET lapra ReportViewer vezérlőelem dobja el a projekt egy hivatkozást az adott szerelvény hozzáadódik a projekthez.
* **Microsoft.ReportViewer.Common.dll**: futás közben a ReportViewer vezérlő által használt osztályok tartalmazza. Automatikusan nincs hozzáadva a projekthez.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Microsoft.ReportViewer.Common mutató hivatkozás hozzáadása
* Kattintson a jobb gombbal a projekt **hivatkozások** csomópont, és válassza **hivatkozás hozzáadása**, jelölje ki a szerelvény a .NET-fülre, majd kattintson **OK**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Ahhoz, hogy helyileg elérhető a szerelvények által az ASP.NET-alkalmazás
1. Az a **hivatkozások** mappát, kattintson a Microsoft.ReportViewer.Common szerelvény, hogy a Tulajdonságok panelen jelennek meg a tulajdonságait.
2. A Tulajdonságok ablakban állítsa **másolása helyi** igaz értékre.
3. A Microsoft.ReportViewer.WebForms ismételje meg az 1. és 2.

### <a name="to-get-reportviewer-language-pack"></a>A beolvasandó ReportViewer nyelvi csomagja
1. Telepítse a megfelelő Microsoft jelentés Viewer 2012 futásidejű újraterjeszthető csomagot [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=317386).
2. A legördülő listából válassza ki a nyelvet, és az oldal download center oldalára lekérdezi átirányítva.
3. Kattintson a **letöltése** a ReportViewerLP.exe letöltésének elindításához.
4. ReportViewerLP.exe letöltése után kattintson **futtatása** azonnal telepítse, vagy kattintson **mentése** menti azt a számítógépet. Ha **mentése**, ne feledje, ahol mentse a fájlt a mappa nevét.
5. Keresse meg a mappát, ahová a fájlt mentette. Kattintson a jobb gombbal a ReportViewerLP.exe, kattintson a **Futtatás rendszergazdaként**, és kattintson a **Igen**.
6. Miután ReportViewerLP.exe, látni fogja a c:\windows\assembly rendelkezik az Erőforrásfájlok **Microsoft.ReportViewer.Webforms.Resources** és **Microsoft.ReportViewer.Common.Resources**.

### <a name="to-configure-for-localized-reportviewer-control"></a>Honosított ReportViewer vezérlő konfigurálása
1. Töltse le és telepítse a Microsoft jelentés Viewer 2012 futásidejű újraterjeszthető csomagot a fenti megadott utasításokat követve.
2. Hozzon létre <language> mappa a projektet, és másolja a kapcsolódó erőforrás szerelvény fájlok van. Az erőforrás szerelvény fájlok másolása: **Microsoft.ReportViewer.Webforms.Resources.dll** és **Microsoft.ReportViewer.Common.Resources.dll**. Válassza ki az erőforrás-szerelvény fájlokat, és a Tulajdonságok panelen **másolása a kimeneti könyvtárba** a "**mindig másolása**".
3. A kulturális környezet & UICulture beállítása, a webes projektet. A kulturális környezet és a felhasználói felületi kulturális környezet egy ASP.NET-weblap beállításával kapcsolatos további információkért lásd: [hogyan: állítsa be a kulturális környezet és a felhasználói felületi kulturális környezet ASP.NET-weblap globalizációs](http://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Hitelesítési és engedélyezési konfigurálása
A ReportViewer szüksége van a jelentéskészítő kiszolgálóval való hitelesítéséhez szükséges hitelesítő adatokat használja, és a hitelesítő adatok engedéllyel kell rendelkeznie a jelentéskészítő kiszolgáló által a jelentések eléréséhez. A hitelesítés további információkért tekintse meg a [Reporting Services jelentés megjelenítő vezérlő és a Microsoft Azure virtuálisgép-alapú jelentés kiszolgálók](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Tegye közzé az ASP.NET webalkalmazást az Azure-bA
Az Azure-bA ASP.NET webalkalmazás közzétételét, lásd: [hogyan: át, és tegye közzé a webalkalmazást a Visual Studio Azure](../../../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) és [Ismerkedés a webalkalmazásokkal és az ASP.NET](../../../app-service/app-service-web-get-started-dotnet.md).

> [!IMPORTANT]
> Ha az Azure-telepítési projekt hozzáadása vagy hozzáadása Azure Cloud Service-projekt parancs nem jelenik meg a helyi menüben a Megoldáskezelőben, esetleg módosítsa a célkeretrendszerben a projektben a .NET-keretrendszer 4.
> 
> A két parancs lényegében ugyanazokat a funkciókat biztosítanak. Egy vagy a más parancs fog megjelenni a helyi menü attól függően, hogy melyik verzióját a Microsoft Azure SDK telepítése.
> 
> 

## <a name="resources"></a>További források
[Microsoft-jelentések](http://go.microsoft.com/fwlink/?LinkId=205399)

[Az SQL Server Business Intelligence használata Azure-beli virtuális gépeken](../classic/ps-sql-bi.md)

[Natív üzemmódú jelentéskészítő kiszolgálót futtató Azure-beli virtuális gép létrehozása a PowerShell-lel](../classic/ps-sql-report.md)

