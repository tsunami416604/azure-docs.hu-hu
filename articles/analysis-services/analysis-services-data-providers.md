---
title: Az Azure Analysis Services történő csatlakozáshoz szükséges klienskódtárak |} A Microsoft Docs
description: Ismerteti a szükséges ügyfél-alkalmazások és az eszközök csatlakozni az Azure Analysis Services ügyfélkódtárak
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 334859aecf3a5f7a5678c0af656c6837d5661dac
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270863"
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Kapcsolódás az Azure Analysis Services ügyfélkódtárak

Ügyféloldali függvénytárak is ügyfél alkalmazások és eszközök Analysis Services-kiszolgálókhoz való kapcsolódáshoz szükséges. Microsoft-ügyfélalkalmazások, mint a Power BI Desktop, Excel, az SQL Server Management Studio (SSMS), és az SQL Server Data Tools (SSDT) telepítése mindhárom ügyfélkódtárat, és frissítse azokat rendszeres alkalmazásfrissítéseknél együtt. Bizonyos esetekben szükség lehet a klienskódtárak újabb verzióját telepítse. Egyéni ügyfélalkalmazások számára is szükséges, ügyféloldali függvénytárak telepítve vannak.

## <a name="download-the-latest-client-libraries-windows-installer"></a>Töltse le a legújabb klienskódtárak (Windows Installer)  

|Letöltés  |Termék verziószáma  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.0.15.26    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |    15.0.15.26      |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   15.17.1.0    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    15.17.1.0     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO és ADOMD (NuGet-csomagok)

Analysis Services Management Objects (AMO) és ADOMD ügyféloldali függvénytárak is telepíthető csomagokat az elérhető [NuGet.org](https://www.nuget.org/). Ajánlott NuGet hivatkozások helyett a Windows Installer használatával telepíti át. 

|Csomag  | Termék verziószáma  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    15.17.1     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   15.17.1      |

NuGet-csomag szerelvények AssemblyVersion Szemantikus verziószámozást kövesse: MAJOR.MINOR.PATCH. NuGet-hivatkozásokat a várt verzió betölteni, akkor is, ha van egy másik verziója (MSI telepítése eredő) a GAC-ban. JAVÍTÁS értéke akkor növekszik, egyes kiadásaihoz. AMO és ADOMD-verziók őrzi meg a szinkronizálva.

## <a name="understanding-client-libraries"></a>Ügyfélkódtárak ismertetése

Analysis Services használatához három ügyfél szalagtárakban adatszolgáltatók néven is ismert. ADOMD.NET és az Analysis Services Management Objects (AMO) olyan felügyelt klienskódtárak. Az Analysis Services OLE DB-szolgáltató (MSOLAP DLL) egy natív ügyféloldali kódtár. Általában három telepített egy időben. **Az Azure Analysis Services igényel minden három kódtárak legújabb verzióit**. 

A Microsoft ügyfélalkalmazások, mint a Power BI Desktop és az Excel mindhárom ügyfélkódtárat telepítse, és frissítse azokat, ha elérhetővé válik az új verziók. A verzió vagy a frissítések gyakoriságát, attól függően néhány ügyfélkódtárai nem lehet a legújabb, az Azure Analysis Services használatához szükséges verzió. Ugyanez vonatkozik az egyéni alkalmazásokra vagy olyan egyéb felületekre, mint például az AsCmd, a TOM vagy az ADOMD.NET. Ezekhez az alkalmazásokhoz igényelnek, manuálisan vagy programon keresztül telepíti a kódtárakat. A klienskódtárak manuális telepítéséhez SQL Server funkciócsomagok terjeszthető csomag megtalálhatók. Azonban ezen klienskódtárak vannak társítva, az SQL Server-verzióra, és nem lehet a legújabb.  

Ügyfélkapcsolatok ügyféloldali függvénytárak is adatforrásként az Azure Analysis Services-kiszolgáló eléréséhez szükséges adatszolgáltatóktól származó különböző. Adatforrás-kapcsolatok kapcsolatos további információkért lásd: [adatforrás-kapcsolatok](analysis-services-datasource.md).

## <a name="client-library-types"></a>Ügyféloldali kódtár típusok

### <a name="analysis-services-ole-db-provider-msolap"></a>Analysis Services OLE DB-szolgáltató (MSOLAP) 

 Analysis Services OLE DB szolgáltató (MSOLAP) a natív ügyféloldali kódtár, az Analysis Services-adatbázis-kapcsolat. Használható közvetve ADOMD.NET és AMO, az adatszolgáltató a kapcsolati kérelmek delegálása is. Az OLE DB-szolgáltató közvetlenül az alkalmazás kódja is meghívható.  
  
 Az Analysis Services OLE DB-szolgáltató automatikusan települ a legtöbb eszközök és az Analysis Services-adatbázis eléréséhez használt ügyfél-alkalmazások. Analysis Services-adatok elérésére használt számítógépekre kell telepíteni.  
  
 Kapcsolati karakterláncok OLE DB-szolgáltatók gyakran meg van adva. Egy Analysis Services-kapcsolati sztringet, tekintse meg az OLE DB-szolgáltató különböző elnevezéseket használja: MSOLAP. \<verzió > .dll.

### <a name="amo"></a>AMO  

 AMO egy felügyeleti kiszolgáló és az adatok meghatározása használt felügyelt klienskódtár. Eszközök és az ügyfélalkalmazások által használt, és telepítve. Például az SQL Server Management Studio (SSMS) AMO Analysis Serviceshez való csatlakozáshoz használ. Egy AMO használatával kapcsolat általában minimális, amely `"data source=\<servername>"`. A kapcsolat létrejötte után az API-t használhatja az adatbázis-gyűjtemények és főobjektumok használata. Az SSDT és az SSMS AMO használatával csatlakozhat egy Analysis Services-példányhoz.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET egy felügyelt adatok ügyféloldali kódtár Analysis Services-adatok lekérdezésekhez használja fel. Eszközök és az ügyfélalkalmazások által használt, és telepítve. 
  
 Ha csatlakozik egy adatbázishoz, a kapcsolati karakterlánc tulajdonságai az összes három szalagtárra hasonlóak. Szinte bármilyen kapcsolati karakterlánc használatával tartozó ADOMD.NET meghatározása [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) AMO és az Analysis Services OLE DB szolgáltató (MSOLAP) is működik. További tudnivalókért lásd: [kapcsolati karakterlánc tulajdonságai &#40;Analysis Services&#41;](https://docs.microsoft.com/sql/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a> Szalagtár ügyfélverzió meghatározása   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Nyissa meg a következőt: `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`. Ha egynél több mappát, válassza ki a nagyobb számra.
  
2.  Kattintson a jobb gombbal **msolap.dll** > **tulajdonságok** > **részletek**. Ha a fájlnév msolap140.dll, régebbi, mint a legújabb verzióra, és frissíteni kell.
    
    ![Ügyfél-könyvtár részletei](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Nyissa meg a következőt: `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Ha egynél több mappát, válassza ki a nagyobb számra.
2. Kattintson a jobb gombbal **Microsoft.AnalysisServices** > **tulajdonságok** > **részletek**.  

### <a name="adomd"></a>ADOMD

1. Nyissa meg a következőt: `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Ha egynél több mappát, válassza ki a nagyobb számra.
2. Kattintson a jobb gombbal **Microsoft.AnalysisServices.AdomdClient** > **tulajdonságok** > **részletek**.  


## <a name="next-steps"></a>További lépések
[Kapcsolódás Excellel](analysis-services-connect-excel.md)    
[Kapcsolódás Power BI-jal](analysis-services-connect-pbi.md)
