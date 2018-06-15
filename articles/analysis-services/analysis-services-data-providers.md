---
title: Azure Analysis Services való kapcsolódáshoz szükséges ügyfél-könyvtárak |} Microsoft Docs
description: Ismerteti a szükséges ügyfél-alkalmazások és az eszközök csatlakozni az Azure Analysis Services ügyfél könyvtárak
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/31/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ad330c5f17b6e151918511916b0aef89bef3a6f7
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699852"
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Ügyfél-könyvtárakban csatlakozás Azure Analysis Services szolgáltatáshoz

Ügyfél könyvtárak olyan ügyfél-alkalmazások és eszközök Analysis Services-kiszolgálókhoz való kapcsolódáshoz szükséges. 

## <a name="download-the-latest-client-libraries-windows-installer"></a>Töltse le a legfrissebb klienskódtárak segítségével (Windows Installer)  

|Letöltés  |Termék verziószáma  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.0.1.492      |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |    15.0.1.492      |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   15.1.0.0    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    15.1.0.0     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO és ADOMD (NuGet-csomagok)

Analysis Services Management Objects (AMO) és ADOMD ügyfélkódtáraival érhetők el a telepítésre alkalmas csomag [NuGet.org](https://www.nuget.org/). Ajánlott áttelepítette NuGet hivatkozások használata a Windows Installer helyett. 

|Csomag  | Termék verziószáma  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    15.1.0.0      |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   15.1.0.0      |

NuGet csomag szerelvények AssemblyVersion hajtsa végre a szemantikai versioning: fő. KISEBB. JAVÍTÁS. NuGet-hivatkozásokat a várt verzió betölteni, még akkor is, ha egy másik verziója található a GAC-ban (MSI-telepítés eredő). JAVÍTÁS minden egyes kiadásához. AMO és ADOMD verziók tárolják a szinkronizálás.

## <a name="understanding-client-libraries"></a>Ügyféloldali kódtáraknál ismertetése

Analysis Services három klienskódtárak segítségével, más néven adatszolgáltatók használják. ADOMD.NET és az Analysis Services Management Objects (AMO) olyan felügyelt klienskódtárak segítségével. Az Analysis Services OLE DB szolgáltató (MSOLAP DLL) egy natív ügyfél könyvtárban. Általában három telepített egy időben. **Az Azure Analysis Services szükséges összes három könyvtárak legújabb verziói**. 

A Microsoft ügyfél alkalmazások, például a Power BI Desktop és az Excel telepítése a három ügyfélcsoport kódtárakat, és frissítse azokat, ha elérhetők új verziók. Attól függően, hogy a verzió vagy frissítési gyakoriságának egyes ügyfél-dokumentumtárak nem lehet az Azure Analysis Services által igényelt legújabb verziók. Ugyanez vonatkozik az egyéni alkalmazásokra vagy olyan egyéb felületekre, mint például az AsCmd, a TOM vagy az ADOMD.NET. Ezeket az alkalmazásokat kell manuálisan vagy programon keresztül telepíti a könyvtárak. Az ügyfél szalagtárak manuális telepítésre terjeszthető csomag SQL Server szolgáltatáscsomagok szerepelnek. Azonban az ügyfél tárakban vannak társítva, az SQL Server-verzióra, és nem lehet a legújabb.  

Ügyféloldali kódtáraknál ügyfélkapcsolatok Azure Analysis Services-kiszolgálóról egy adatforrás eléréséhez szükséges adatszolgáltatók eltérnek. Adatforrás-kapcsolatok kapcsolatos további információkért lásd: [adatforrás-kapcsolatok](analysis-services-datasource.md).

## <a name="client-library-types"></a>Ügyfél dokumentumtár-típus

### <a name="analysis-services-ole-db-provider-msolap"></a>Analysis Services OLE DB-szolgáltató (MSOLAP) 

 Analysis Services OLE DB szolgáltató (MSOLAP) a natív ügyféloldali kódtár az Analysis Services-adatbázis-kapcsolat. Használják közvetve ADOMD.NET és a AMO kapcsolatkérelmeknek delegálása az adatok szolgáltatóhoz. Az OLE DB-szolgáltató Ön is meghívhatja, ha közvetlenül az alkalmazás kódját.  
  
 Az Analysis Services OLE DB-szolgáltató legtöbb eszközök és az Analysis Services-adatbázis eléréséhez használt ügyfél-alkalmazások automatikusan települ. Telepítenie kell az Analysis Services-adatok eléréséhez használt számítógépek.  
  
 OLE DB-szolgáltatókat kapcsolati karakterláncok gyakran meg van adva. Egy Analysis Services-kapcsolati karakterlánc egy másik elnevezéseket segítségével tekintse meg az OLE DB-szolgáltató: MSOLAP. \<verzió > .dll.

### <a name="amo"></a>AMO  

 AMO egy felügyelt ügyféloldali kódtár használt kiszolgálók felügyelete és adatok definíciójával. Eszközök és az ügyfélalkalmazások által használt, és telepítve. Például az SQL Server Management Studio (SSMS) AMO Analysis Services elérésére használja. Használja az AMO kapcsolat általában minimális, amely `“data source=\<servername>”`. A kapcsolat létrejötte után az API használatával az adatbázis-gyűjtemények és a főbb objektum használatát. Az SSDT, mind az SSMS csatlakozhatnak AMO egy Analysis Services-példányon.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET egy Analysis Services-adatok lekérdezése használt felügyelt adatok ügyféloldali kódtárára. Eszközök és az ügyfélalkalmazások által használt, és telepítve. 
  
 -Adatbázishoz szeretne csatlakozni, ha tárakat három kapcsolati karakterlánc tulajdonságainak hasonlóak. Szinte bármilyen kapcsolati karakterlánc használatával ADOMD.NET definiálása [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](https://msdn.microsoft.com/library/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring.aspx) AMO és az Analysis Services OLE DB szolgáltató (MSOLAP) is működik. További tudnivalókért lásd: [kapcsolatikarakterlánc-tulajdonságokat &#40;Analysis Services&#41;](https://docs.microsoft.com/sql/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a> Ügyfél könyvtárverzió megállapítása   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Nyissa meg a következőt: `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`. Ha egynél több mappát, válassza ki a nagyobb számot.
  
2.  Kattintson a jobb gombbal **msolap.dll** > **tulajdonságok** > **részletek**. Ha a fájlnév msolap140.dll, a régebbi, mint a legújabb verzióra, és kell frissíteni.
    
    ![Ügyfél a szalagtár adatait](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Nyissa meg a következőt: `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Ha egynél több mappát, válassza ki a nagyobb számot.
2. Kattintson a jobb gombbal **Microsoft.AnalysisServices** > **tulajdonságok** > **részletek**.  

### <a name="adomd"></a>ADOMD

1. Nyissa meg a következőt: `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Ha egynél több mappát, válassza ki a nagyobb számot.
2. Kattintson a jobb gombbal **Microsoft.AnalysisServices.AdomdClient** > **tulajdonságok** > **részletek**.  


## <a name="next-steps"></a>További lépések
[Csatlakozás az Excel használatával](analysis-services-connect-excel.md)    
[Kapcsolódás PowerBI-jal](analysis-services-connect-pbi.md)
