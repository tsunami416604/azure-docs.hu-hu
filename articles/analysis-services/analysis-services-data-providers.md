---
title: Azure Analysis Services ügyféloldali kódtárak | Microsoft Docs
description: Az ügyfélalkalmazások és eszközök kapcsolódásához szükséges ügyféloldali kódtárakat ismerteti Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9db76ca32c97cca622dc68642be5b770254191a1
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73834222"
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Az Azure Analysis Serviceshoz való csatlakozáshoz használható ügyféloldali kódtárak

Az ügyfélalkalmazások és eszközök Analysis Services kiszolgálókhoz való kapcsolódáshoz szükségesek. A Visual studióhoz készült Microsoft-ügyfélalkalmazások, például az Power BI Desktop, az Excel, a SQL Server Management Studio (SSMS) és a Analysis Services projects Extension mind a három ügyféloldali kódtárat telepítik, és a rendszeres alkalmazás-frissítésekkel együtt frissítik azokat. Bizonyos esetekben előfordulhat, hogy az ügyféloldali kódtárak újabb verzióit kell telepítenie. Az egyéni ügyfélalkalmazások esetében az ügyféloldali kódtárak is telepítve vannak.

## <a name="download-the-latest-client-libraries-windows-installer"></a>A legújabb ügyféloldali kódtárak letöltése (Windows Installer)  

|Letöltés  |Termék verziója  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.10.25    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.10.25       |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   18.2.3.0    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    18.2.3.0     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO és ADOMD (NuGet-csomagok)

A Analysis Services Management Objects (AMO) és a ADOMD ügyféloldali kódtárak telepíthető csomagokként érhetők el a [NuGet.org](https://www.nuget.org/). Javasoljuk, hogy a Windows Installer használata helyett NuGet referenciákat telepítsen. 

|Csomag  | Termék verziója  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    18.0.9     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   18.0.9      |

NuGet-csomagok szerelvényei AssemblyVersion a szemantikai verziószámozás követése: MAJOR. Kisebb. Javítás. A NuGet-hivatkozások akkor is betöltik a várt verziót, ha a GAC-ban (az MSI telepítése miatt) egy másik verziója van. A javítás minden kiadásnál növekszik. Az AMO-és ADOMD-verziók szinkronban vannak tárolva.

## <a name="understanding-client-libraries"></a>Az ügyféloldali kódtárak ismertetése

A Analysis Services három ügyféloldali kódtárat (más néven adatszolgáltatót) használ. A ADOMD.NET és a Analysis Services Felügyeleti objektumok (AMO) felügyelt ügyféloldali kódtárak. A Analysis Services OLE DB-szolgáltató (MSOLAP DLL) egy natív ügyféloldali kódtár. Általában mindhárom telepítése egyszerre történik. **Azure Analysis Services a mindhárom függvénytár legújabb verzióit igényli**. 

A Microsoft ügyfélalkalmazások, például a Power BI Desktop és az Excel mind a három ügyféloldali kódtárat telepítik, és új verziók esetén frissítik azokat. A verziótól vagy a frissítések gyakoriságtól függően előfordulhat, hogy egyes ügyféloldali kódtárak nem a Azure Analysis Services által igényelt legújabb verziók. Ugyanez vonatkozik az egyéni alkalmazásokra vagy olyan egyéb felületekre, mint például az AsCmd, a TOM vagy az ADOMD.NET. Ezeknek az alkalmazásoknak manuálisan vagy programozott módon kell telepíteniük a kódtárakat. A manuális telepítéshez szükséges ügyféloldali kódtárak a SQL Server szervizcsomagok terjeszthető csomagként szerepelnek. Ezek az ügyféloldali kódtárak azonban a SQL Server verziójához vannak kötve, és előfordulhat, hogy nem a legújabbak.  

Az ügyfélkapcsolatok ügyféloldali kódtárai eltérnek a Azure Analysis Services kiszolgálóról az adatforráshoz való csatlakozáshoz szükséges adatszolgáltatóktól. További információ az adatforrás-kapcsolatokról: [adatforrás-kapcsolatok](analysis-services-datasource.md).

## <a name="client-library-types"></a>Ügyféloldali függvénytár-típusok

### <a name="analysis-services-ole-db-provider-msolap"></a>Analysis Services OLE DB-szolgáltató (MSOLAP) 

 A Analysis Services OLE DB-szolgáltató (MSOLAP) a natív ügyféloldali kódtár Analysis Services adatbázis-kapcsolatokhoz. Ezt a ADOMD.NET és az AMO is használja, és a kapcsolódási kérelmeket az adatszolgáltatóhoz delegálja. A OLE DB szolgáltatót közvetlenül az alkalmazás kódjából is meghívhatja.  
  
 A Analysis Services OLE DB-szolgáltató a legtöbb eszköz és a Analysis Services adatbázisok eléréséhez használt ügyfélalkalmazások által automatikusan települ. A Analysis Services-adateléréshez használt számítógépeken telepítve kell lennie.  
  
 A OLE DB szolgáltatók gyakran vannak megadva a kapcsolatok karakterláncokban. Egy Analysis Services kapcsolódási karakterlánc egy másik nómenklatúrát használ, amely a OLE DB szolgáltatóra hivatkozik: MSOLAP.\<verziója >. dll.

### <a name="amo"></a>AMO  

 Az AMO a kiszolgálók felügyeletéhez és az adatdefinícióhoz használt felügyelt ügyféloldali kódtár. Az eszközök és ügyfélalkalmazások telepítik és használják. Például SQL Server Management Studio (SSMS) az AMO-t használja a Analysis Serviceshoz való kapcsolódáshoz. Az AMO-t használó kapcsolatok általában minimálisak, amely `"data source=\<servername>"`t tartalmaz. A kapcsolatok létrehozása után az API-val dolgozhat az adatbázis-gyűjtemények és a főbb objektumok használatával. A Visual Studio és a SSMS is az AMO használatával csatlakozhat egy Analysis Services-példányhoz.  

  
### <a name="adomd"></a>ADOMD

 A ADOMD.NET egy felügyelt adatügyfél-függvénytár Analysis Services-adatlekérdezéshez. Az eszközök és ügyfélalkalmazások telepítik és használják. 
  
 Adatbázishoz való kapcsolódáskor a kapcsolati sztring tulajdonságai mindhárom könyvtárhoz hasonlóak. Szinte minden, a [Microsoft. AnalysisServices. AdomdClient. AdomdConnection. ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) használatával ADOMD.net definiált kapcsolati karakterlánc az amo és a ANALYSIS Services OLE DB-szolgáltató (MSOLAP) esetében is működik. További információ: a [kapcsolódási karakterlánc tulajdonságai &#40;Analysis Services&#41;](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a>Az ügyféloldali kódtár verziójának meghatározása   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Nyissa meg a következőt: `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`. Ha egynél több mappával rendelkezik, válassza a nagyobb számot.
  
2.  Kattintson a jobb gombbal a **MSOLAP. dll** > **Tulajdonságok** > **részletek**elemre. Ha a fájlnév a msolap140. dll, a legújabb verziónál régebbi, és frissíteni kell.
    
    ![Ügyféloldali kódtár részletei](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Nyissa meg a következőt: `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Ha egynél több mappával rendelkezik, válassza a nagyobb számot.
2. Kattintson a jobb gombbal a **Microsoft. AnalysisServices** > **Tulajdonságok** > **részletek**elemre.  

### <a name="adomd"></a>ADOMD

1. Nyissa meg a következőt: `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Ha egynél több mappával rendelkezik, válassza a nagyobb számot.
2. Kattintson a jobb gombbal a **Microsoft. AnalysisServices. AdomdClient** > **Tulajdonságok** > **részletek**elemre.  


## <a name="next-steps"></a>További lépések
[Kapcsolódjon az Excel](analysis-services-connect-excel.md)    
[Kapcsolódás PowerBI-jal](analysis-services-connect-pbi.md)
