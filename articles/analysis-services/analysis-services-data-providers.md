---
title: Azure Analysis Services ügyfélkódtárak | Microsoft dokumentumok
description: Az Ügyfélalkalmazásokhoz és az Azure Analysis Services összekapcsolásához szükséges ügyfélkódtárak ismertetése
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c5914c7987d5a54a6bcc779231287309517f5121
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129225"
---
# <a name="client-libraries-for-connecting-to-analysis-services"></a>Ügyféltárak az Analysis Services szolgáltatáshoz való csatlakozáshoz

Az ügyfélkódtárak az Analysis Services-kiszolgálókhoz való csatlakozáshoz szükséges ügyfélalkalmazásokhoz és eszközökhöz szükségesek. A Microsoft ügyfélalkalmazások, például a Power BI Desktop, az Excel, az SQL Server Management Studio (SSMS) és az Analysis Services-projektek Visual Studio-projektek bővítménye imények mind a három ügyféltárat telepítik, és rendszeres alkalmazásfrissítésekkel együtt frissítik azokat. Bizonyos esetekben előfordulhat, hogy az ügyfélkódtárak újabb verzióit kell telepítenie. Az egyéni ügyfélalkalmazások hoznak létre ügyfélkódtárakat is.

## <a name="download-the-latest-client-libraries-windows-installer"></a>A legújabb ügyféltárak letöltése (Windows Installer)  

|Letöltés  |Termékváltozat  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.29.25    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.29.25       |
|[Amo](https://go.microsoft.com/fwlink/?linkid=829578)     |   18.4.8.0    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    18.4.8.0     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO és ADOMD (NuGet csomagok)

Az Analysis Services Management Objects (AMO) és az ADOMD ügyféltárak telepíthető csomagokként érhetők el [NuGet.org.](https://www.nuget.org/) Javasoljuk, hogy a Windows Installer használata helyett a NuGet-hivatkozásokra térjen át. 

|Csomag  | Termékváltozat  | 
|---------|---------|
|[Amo](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    18.4.8.0     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   18.4.8.0      |

NuGet csomagszerelvények AssemblyVersion kövesse szemantikai verziószámozás: MAJOR. Kisebb. Javítás. A NuGet-hivatkozások akkor is betöltik a várt verziót, ha a GAC-ban van egy másik verzió (amely az MSI telepítésből ered). A PATCH minden egyes kiadáshoz növekszik. Az AMO és az ADOMD verziók szinkronban maradnak.

## <a name="understanding-client-libraries"></a>Az ügyféltárak ismertetése

Az Analysis Services három ügyfélkódtárat, más néven adatszolgáltatókat használ. ADOMD.NET és Analysis Services Management Objects (AMO) felügyelt ügyfélkódtárak. Az Analysis Services OLE DB Provider (MSOLAP DLL) egy natív ügyféltár. Általában mindhárom egyszerre van telepítve. **Az Azure Analysis Services mindhárom könyvtár legújabb verzióját igényli.** 

A Microsoft ügyfélalkalmazások, például a Power BI Desktop és az Excel mindhárom ügyféltárat telepítik, és frissítik azokat, ha új verziók érhetők el. A frissítések verziójától vagy gyakoriságától függően előfordulhat, hogy egyes ügyfélkódtárak nem az Azure Analysis Services által megkövetelt legújabb verziók. Ugyanez vonatkozik az egyéni alkalmazásokra vagy olyan egyéb felületekre, mint például az AsCmd, a TOM vagy az ADOMD.NET. Ezek az alkalmazások a könyvtárak manuális vagy programozott telepítését igénylik. A manuális telepítéshez használható ügyfélkódtárak at az SQL Server szolgáltatáscsomagok terjeszthető csomagokként tartalmazzák. Ezek az ügyfélkódtárak azonban az SQL Server verziójához vannak kötve, és nem feltétlenül a legújabbak.  

Az ügyfélkapcsolatok ügyfélkódtárai eltérnek az Azure Analysis Services-kiszolgálóról egy adatforráshoz való csatlakozáshoz szükséges adatszolgáltatóktól. Az adatforrás-kapcsolatokról az [Adatforrás-kapcsolatok](analysis-services-datasource.md)témakörben olvashat bővebben.

## <a name="client-library-types"></a>Ügyféltártípusok

### <a name="analysis-services-ole-db-provider-msolap"></a>Analysis Services OLE DB Szolgáltató (MSOLAP) 

 Az Analysis Services OLE DB Provider (MSOLAP) az Analysis Services adatbázis-kapcsolatainak natív ügyfélkönyvtára. A ADOMD.NET és az AMO közvetve használja, és a kapcsolatkérelmeket delegálja az adatszolgáltatótól. Az OLE DB-szolgáltatót közvetlenül az alkalmazáskódból is meghívhatja.  
  
 Az Analysis Services OLE DB Provider telepítése automatikusan történik az Analysis Services-adatbázisok eléréséhez használt legtöbb eszköz és ügyfélalkalmazás által. Az Analysis Services-adatok eléréséhez használt számítógépekre kell telepíteni.  
  
 Az OLE DB-szolgáltatók gyakran vannak megadva a kapcsolati karakterláncokban. Az Analysis Services kapcsolati karakterlánca izolált nómenklatúrát használ az OLE DB Szolgáltató: MSOLAP megjegyzéséhez. \<verzió>.dll.

### <a name="amo"></a>Amo  

 Az AMO egy felügyelt ügyfélkódtár, amelyet kiszolgálófelügyelethez és adatdefinícióhoz használnak. Ez által telepített és használt eszközök és ügyfélalkalmazások. Az SQL Server Management Studio (SSMS) például az AMO segítségével csatlakozik az Analysis Services szolgáltatáshoz. Az AMO-t használó kapcsolat általában `"data source=\<servername>"`minimális, amely a - A kapcsolat létrejötte után az API-val együttműködve adatbázis-gyűjtemények és főbb objektumok. A Visual Studio és az SSMS egyaránt amo-t használja az Analysis Services-példányhoz való csatlakozáshoz.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET az Analysis Services-adatok lekérdezésére használt felügyelt adatügyfél-tár. Ez által telepített és használt eszközök és ügyfélalkalmazások. 
  
 Adatbázishoz való csatlakozáskor mindhárom kódtár kapcsolati karakterlánc-tulajdonságai hasonlóak. A [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) az AMO és az Analysis Services OLE DB Provider (MSOLAP) használatával definiált szinte minden ADOMD.NET kapcsolati karakterláncot is használ. További információ: [Connection string properties &#40;Analysis Services&#41;](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="how-to-determine-client-library-version"></a><a name="bkmk_LibUpdate"></a>Az ügyféltár verziójának megállapítása   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Nyissa meg a következőt: `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`. Ha egynél több mappával rendelkezik, válassza ki a magasabb számot.
  
2.  Kattintson a jobb gombbal **az msolap.dll** > **tulajdonságainak részletei** > **parancsra.** Ha a fájlnév msolap140.dll, akkor régebbi, mint a legújabb verzió, és frissíteni kell.
    
    ![Ügyféltár adatai](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>Amo

1. Nyissa meg a következőt: `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Ha egynél több mappával rendelkezik, válassza ki a magasabb számot.
2. Kattintson a jobb gombbal a **Microsoft.AnalysisServices** > **tulajdonságainak** > **részletei parancsra.**  

### <a name="adomd"></a>ADOMD

1. Nyissa meg a következőt: `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Ha egynél több mappával rendelkezik, válassza ki a magasabb számot.
2. Kattintson a jobb gombbal a **Microsoft.AnalysisServices.AdomdClient** > **tulajdonságainak** > **részletei parancsra.**  


## <a name="next-steps"></a>További lépések
[Csatlakozás az Excellel](analysis-services-connect-excel.md)    
[Kapcsolódás Power BI-jal](analysis-services-connect-pbi.md)
