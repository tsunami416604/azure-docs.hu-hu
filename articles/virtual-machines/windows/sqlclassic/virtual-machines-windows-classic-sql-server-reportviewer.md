---
title: A ReportViewer használata egy webhely |} A Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan hozhat létre a Visual Studio a ReportViewer vezérlő, amely a Microsoft Azure virtuális gépen tárolt jelentés megjeleníti a Microsoft Azure webhely.
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
ms.openlocfilehash: b554dc1fa33519d87aa0c9c5ba9130b47cbea142
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971750"
---
# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>A ReportViewer használata Azure-ban üzemeltetett webhelyen
> [!IMPORTANT]
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

A Visual Studio a ReportViewer vezérlő, amely a Microsoft Azure virtuális gépen tárolt jelentés megjeleníti a Microsoft Azure webhelyet hozhat létre. A ReportViewer vezérlő egy webalkalmazást hoz létre az ASP.NET webalkalmazás-sablon használatával.

> [!IMPORTANT]
> Az ASP.NET MVC-webalkalmazás-sablonok nem támogatják a ReportViewer vezérlő.

A ReportViewer építhet be a Microsoft Azure webhely, hajtsa végre a következő feladatokat kell.

* **Adjon hozzá** szerelvények a központi telepítési csomag
* **Konfigurálása** hitelesítés és engedélyezés
* **Közzététel** az ASP.NET-es webalkalmazás az Azure-bA

## <a name="prerequisites"></a>Előfeltételek
Tekintse át a "általános ajánlás és ajánlott eljárások" részt [SQL Server Business Intelligence az Azure Virtual machines gépeken](../classic/ps-sql-bi.md).

> [!NOTE]
> A ReportViewer vezérlők mellékelt a Visual Studióval, a Standard Edition vagy újabb. Ha a webes fejlesztői Express kiadását használja, telepítenie kell a [MICROSOFT jelentés VIEWER 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747) a ReportViewer futásidejű szolgáltatásai.
>
> A ReportViewer konfigurált helyi feldolgozási üzemmódban nem támogatott Microsoft Azure-ban.

## <a name="adding-assemblies-to-the-deployment-package"></a>Szerelvények hozzáadása a központi telepítési csomag
Az ASP.NET alkalmazás helyszíni üzemeltetése, a ReportViewer szerelvényeket a globális szerelvény-gyorsítótárban (GAC) az IIS-kiszolgáló közvetlenül a Visual Studio telepítése során általában telepített, és közvetlenül az alkalmazás által elérhető. Azonban az ASP.NET-alkalmazását a felhőben üzemelteti, ha a Microsoft Azure nem engedélyezi a semmit sem kell telepíteni a GAC-ba, ezért meg kell győződnie arról, hogy az alkalmazás helyileg érhetők el a ReportViewer szerelvényeket. Ehhez be hozzájuk hivatkozásokat a projekthez való hozzáadásával és a konfigurálásukról helyileg kell másolni.

Távoli feldolgozási üzemmódban a ReportViewer vezérlő használja az alábbi szerelvények:

* **Microsoft.ReportViewer.WebForms.dll**: A ReportViewer kódra, amely a ReportViewer használata az oldalon kell tartalmazza. A ReportViewer vezérlőelem egy ASP.NET-lapra dobja el a projekt egy hivatkozást a szerelvény hozzáadódik a projekthez.
* **Microsoft.ReportViewer.Common.dll**: Futási időben a ReportViewer vezérlő által használt osztályok tartalmazza. Automatikusan nincs hozzáadva a projekthez.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Microsoft.ReportViewer.Common hivatkozás hozzáadása
* Kattintson a jobb gombbal a projekt **hivatkozások** csomópontra, és válassza **hivatkozás hozzáadása**, válassza ki a szerelvény a .NET-lap, és kattintson a **OK**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Elérhetővé a szerelvények helyi az ASP.NET-alkalmazás
1. Az a **hivatkozások** mappát, kattintson a Microsoft.ReportViewer.Common szerelvény, hogy a Tulajdonságok panelen jelennek meg a tulajdonságait.
2. A Tulajdonságok panelen állítsa **másolási helyi** igaz értékre.
3. A Microsoft.ReportViewer.WebForms ismételje meg az 1. és 2.

### <a name="to-get-reportviewer-language-pack"></a>A ReportViewer nyelvi csomag beszerzése
1. A megfelelő Microsoft jelentés Viewer 2012 Runtime terjeszthető csomagot telepítse a [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkId=317386).
2. A legördülő listából válassza ki a nyelvet, és az oldal átirányítja az a megfelelő download center oldalán.
3. Kattintson a **letöltése** ReportViewerLP.exe letöltésének megkezdéséhez.
4. ReportViewerLP.exe a letöltés után kattintson a **futtatása** azonnal telepítse, vagy kattintson **mentése** arra, hogy mentse a számítógépre. Ha rákattint **mentése**, ne felejtse el a mappát, ahol a fájl mentése nevét.
5. Keresse meg a mappát, ahová a fájlt mentette. Kattintson a jobb gombbal a ReportViewerLP.exe, kattintson a **Futtatás rendszergazdaként**, és kattintson a **Igen**.
6. Miután ReportViewerLP.exe, látni fogja a c:\windows\assembly rendelkezik az erőforrások fájljainak **Microsoft.ReportViewer.Webforms.Resources** és **Microsoft.ReportViewer.Common.Resources**.

### <a name="to-configure-for-localized-reportviewer-control"></a>Honosított ReportViewer vezérlő konfigurálása
1. Töltse le és telepítse a Microsoft jelentés Viewer 2012 Runtime újraterjeszthető csomagot a fenti megadott utasítások szerint.
2. Hozzon létre \<nyelvi\> mappát a projekt, és másolja a társított erőforrás szerelvény fájlok van. Erőforrás szerelvény fájlok kell másolni a következők: **Microsoft.ReportViewer.Webforms.Resources.dll** és **Microsoft.ReportViewer.Common.Resources.dll**. Válassza ki az erőforrás tevékenységszerelvény-fájlokhoz, és a Tulajdonságok panelen **Copy to Output Directory** a "**mindig Másolás**".
3. A kulturális környezet & UICulture be a webes projekt. A kulturális környezet és a felhasználói felület kultúrafüggő részletei beállítása az ASP.NET-weblap kapcsolatos további információkért lásd: [hogyan: Állítsa be a kulturális környezet és a felhasználói felület kultúrafüggő részletei az ASP.NET-weblap globalizációs](https://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Hitelesítés és -engedélyezés konfigurálása
A ReportViewer szüksége van a jelentéskészítő kiszolgáló használatának hitelesítéséhez szükséges hitelesítő adatokat használja, és azt szeretné, a jelentések eléréséhez a hitelesítő adatokat kell engedélyezni a jelentéskészítő kiszolgáló által. A hitelesítés további információkért tekintse meg a [Reporting Services jelentésmodell – jelentésmegjelenítő vezérlőelem és a Microsoft Azure-alapú virtuális gép jelentéskiszolgálók](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Tegye közzé a ASP.NET webalkalmazást az Azure-bA
Egy ASP.NET-es webalkalmazás az Azure-bA közzétételével kapcsolatos útmutatásért lásd: [hogyan: Telepítse át, és a Visual Studióból az Azure webalkalmazás közzététele](../../../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) és [Ismerkedés a Web Apps és ASP.NET](../../../app-service/app-service-web-get-started-dotnet.md).

> [!IMPORTANT]
> Ha az Azure üzembe helyezési projektben adja hozzá, vagy adja hozzá az Azure Felhőszolgáltatás-projekt parancs nem jelenik meg a helyi menüben a Megoldáskezelőben, szükség lehet módosítani a projekt Cílová architektura .NET-keretrendszer 4.
>
> A két parancs lényegében ugyanazokat a funkciókat biztosítanak. Egy vagy a többi parancs megjelenik a helyi menü telepítette a Microsoft Azure SDK verziójától függően.
>
>

## <a name="resources"></a>További források
[A Microsoft-jelentések](https://go.microsoft.com/fwlink/?LinkId=205399)

[Az SQL Server Business Intelligence használata Azure-beli virtuális gépeken](../classic/ps-sql-bi.md)

[Natív üzemmódú jelentéskészítő kiszolgálót futtató Azure-beli virtuális gép létrehozása a PowerShell-lel](../classic/ps-sql-report.md)
