---
title: Az Azure Analysis Services kiszolgálói aliasnevek |} A Microsoft Docs
description: Ismerteti, hogyan hozhat létre és használhat a kiszolgáló neve az alias.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 66e6b4713591f099769543a75dcddec34f3d2e2b
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188130"
---
# <a name="alias-server-names"></a>Alias kiszolgálók nevei

Kiszolgálónév aliasának használatával a felhasználók kapcsolódhatnak az Azure Analysis Services-kiszolgáló és a egy rövidebb *alias* a kiszolgáló neve helyett. Amikor egy ügyfélalkalmazás csatlakoztat, az alias-végpont használatával van megadva a **hivatkozás: / /** protokoll formátumban. A végpont ahhoz, hogy csatlakozhasson majd a kiszolgáló valódi nevét adja vissza.

Alias kiszolgálók nevei a következők számára hasznos:

- Érintse a felhasználókat a kiszolgálók közötti áttelepítése modellek. 
- Kiszolgáló felhasználóbarát nevek olyan könnyíteni a felhasználóknak ne felejtse el. 
- A nap különböző időpontokban különböző kiszolgálók helyével. 
- Közvetlen felhasználó földrajzilag közelebb, például ha az Azure Traffic Managerrel példányok különböző régiókban. 

Alias alapul szolgálhat bármely HTTPS-végpontot, amely egy érvényes Azure Analysis Services-kiszolgáló nevét adja vissza. A végpont támogatnia kell HTTPS a 443-as porton keresztül, és a port nem adható meg az URI-ban.

![A hivatkozási formátumot használó alias](media/analysis-services-alias/aas-alias-browser.png)

Amikor egy ügyfél, a másodlagos kiszolgáló nevét is meg kell adni használatával **hivatkozás: / /** protokoll formátumban. Ha például a Power BI Desktopban:

![A Power BI Desktop kapcsolat](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Alias létrehozása

Alias végpont létrehozására használhatja bármelyik módszert, amely egy érvényes Azure Analysis Services-kiszolgáló nevét adja vissza. Például egy valós kiszolgálót tartalmazó Azure Blob Storage-fájlra mutató hivatkozást nevével, és a egy ASP.NET Web Forms-alkalmazás közzététele és létrehozása.

Ebben a példában egy ASP.NET Web Forms-alkalmazást a Visual Studióban jön létre. Az fő referencia- és felhasználói vezérlő a Default.aspx oldal törlődnek. Egyszerűen csak a következő lap irányelv Default.aspx tartalma:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

A default.aspx eseményhez Default.aspx.cs az Response.Write() metódust használja az Azure Analysis Services-kiszolgáló nevét adja vissza.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Lásd még

[Ügyfélkódtárak](analysis-services-data-providers.md)   
[Csatlakozás a Power BI Desktopban](analysis-services-connect-pbi.md)
