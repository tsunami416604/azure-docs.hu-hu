---
title: "Azure Analysis Services való kapcsolódáshoz szükséges ügyfél-könyvtárak |} Microsoft Docs"
description: "Ismerteti a szükséges ügyfél-alkalmazások és az eszközök csatlakozni az Azure Analysis Services ügyfél könyvtárak"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/14/2017
ms.author: owend
ms.openlocfilehash: 870d430d1926859894f452e0af812d794272a9e6
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Ügyfél-könyvtárakban csatlakozás Azure Analysis Services szolgáltatáshoz

Ügyfél könyvtárak olyan ügyfél-alkalmazások és eszközök Analysis Services-kiszolgálókhoz való kapcsolódáshoz szükséges. 

## <a name="download-the-latest-client-libraries"></a>Töltse le a legfrissebb klienskódtárak segítségével  

|Letöltés  |Verzió  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    14.0.801.241      |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |    14.0.801.241      |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   14.0.800.117      |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    14.0.801.241      |

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

 AMO egy felügyelt ügyféloldali kódtár használt kiszolgálók felügyelete és adatok definíciójával. Eszközök és az ügyfélalkalmazások által használt, és telepítve. Például az SQL Server Management Studio (SSMS) AMO Analysis Services elérésére használja.  
  
 Használja az AMO kapcsolat általában minimális, amely `“data source=\<servername>”`. A kapcsolat létrejötte után az API használatával az adatbázis-gyűjtemények és a főbb objektum használatát. Az SSDT, mind az SSMS csatlakozhatnak AMO egy Analysis Services-példányon.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET egy Analysis Services-adatok lekérdezése használt felügyelt adatok ügyféloldali kódtárára. Eszközök és az ügyfélalkalmazások által használt, és telepítve. 
  
 -Adatbázishoz szeretne csatlakozni, ha tárakat három kapcsolati karakterlánc tulajdonságainak hasonlóak. Szinte bármilyen kapcsolati karakterlánc használatával ADOMD.NET definiálása [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](https://msdn.microsoft.com/library/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring.aspx) AMO és az Analysis Services OLE DB szolgáltató (MSOLAP) is működik. További tudnivalókért lásd: [kapcsolatikarakterlánc-tulajdonságokat &#40; Analysis Services &#41; ](https://docs.microsoft.com/sql/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a>Ügyfél könyvtárverzió megállapítása   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Nyissa meg a következőt: `C:\Program Files\Microsoft Analysis Services\AS OLEDB\140`. Ha egynél több mappát, válassza ki a nagyobb számot.
  
2.  Kattintson a jobb gombbal **msolap140.dll** > **tulajdonságok** > **részletek**.  
    
    ![Ügyfél a szalagtár adatait](media/analysis-services-data-providers/aas-msolap-details.png)
  
### <a name="amo"></a>AMO

1. Nyissa meg a következőt: `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\v4.0_14.0.0.0__89845dcd8080cc91`.
2. Kattintson a jobb gombbal **Microsoft.AnalysisServices** > **tulajdonságok** > **részletek**.  

### <a name="adomd"></a>ADOMD

1. Nyissa meg a következőt: `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\v4.0_14.0.0.0__89845dcd8080cc91`.
2. Kattintson a jobb gombbal **Microsoft.AnalysisServices.AdomdClient** > **tulajdonságok** > **részletek**.  


## <a name="next-steps"></a>Következő lépések
[Csatlakozás az Excel használatával](analysis-services-connect-excel.md)    
[Kapcsolódás PowerBI-jal](analysis-services-connect-pbi.md)
