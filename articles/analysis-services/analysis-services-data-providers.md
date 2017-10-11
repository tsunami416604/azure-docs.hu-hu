---
title: "Azure Analysis Services való kapcsolódáshoz szükséges ügyfél-könyvtárak |} Microsoft Docs"
description: "Ismerteti a szükséges ügyfél-alkalmazások és az eszközök csatlakozni az Azure Analysis Services ügyfél könyvtárak"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/15/2017
ms.author: owend
ms.openlocfilehash: a96e7fe671dc051da35168fa7b49ecba53b4c4a5
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Ügyfél-könyvtárakban csatlakozás Azure Analysis Services szolgáltatáshoz

Ügyfél könyvtárak olyan ügyfél-alkalmazások és eszközök Analysis Services-kiszolgálókhoz való kapcsolódáshoz szükséges. 

Analysis Services használata három klienskódtárak segítségével. ADOMD.NET és az Analysis Services Management Objects (AMO), olyan felügyelt klienskódtárak segítségével. Az Analysis Services OLE DB-szolgáltató (MSOLAP DLL) egy natív ügyfél könyvtárban. Általában három telepített egy időben. Az Azure Analysis Services a legújabb verzió szükséges. 

Microsoft ügyfélalkalmazásokat, például a Power BI Desktop és Excel tárakat három ügyfél telepítéséhez. Előfordulhat azonban, attól függően, hogy a verzió vagy frissítési gyakoriságának klienskódtárak nem a legújabb verziók Azure Analysis Services által igényelt. Ugyanez vonatkozik az egyéni alkalmazásokra vagy olyan egyéb felületekre, mint például az AsCmd, a TOM vagy az ADOMD.NET. Ezek az alkalmazások szükség a szalagtárak manuális telepítése. Az ügyfél szalagtárak manuális telepítésre terjeszthető csomag SQL Server szolgáltatáscsomagok szerepelnek. Azonban az ügyfél tárakban vannak társítva, az SQL Server-verzióra, és nem lehet a legújabb.  

Ügyféloldali kódtáraknál ügyfélkapcsolatok Azure Analysis Services-kiszolgálóról egy adatforrás eléréséhez szükséges adatszolgáltatók eltérnek. Adatforrás-kapcsolatok kapcsolatos további információkért lásd: [adatforrás-kapcsolatok](analysis-services-datasource.md).

## <a name="download-the-latest-client-libraries"></a>Töltse le a legfrissebb klienskódtárak segítségével  
Használja a következő klienskódtárak segítségével, ha éles környezetben, és teljesen elérhető és támogatott verziója szükséges.

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Következő lépések
[Csatlakozás az Excel használatával](analysis-services-connect-excel.md)    
[Kapcsolódás PowerBI-jal](analysis-services-connect-pbi.md)
