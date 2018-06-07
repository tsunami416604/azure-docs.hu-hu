---
title: Az Azure Analysis Services alias kiszolgálónevekkel |} Microsoft Docs
description: Ismerteti, hogyan történő létrehozásáról és használatáról a kiszolgáló neve az alias.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c563eb97e093924076684dcf40ac3a8fe5f96880
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596753"
---
# <a name="alias-server-names"></a>Másodlagos kiszolgálók nevei

A kiszolgáló neve alias használatával a felhasználók kapcsolódhatnak az Azure Analysis Services-kiszolgáló egy rövidebb a *alias* helyett a kiszolgáló nevét. Egy ügyfélalkalmazás kapcsolódáskor az alias egy végpont használatával van megadva a **hivatkozás: / /** protokoll formátumban. A végpont használata esetén csatlakozhassanak majd a tényleges kiszolgáló nevét adja vissza.

Kiszolgáló aliasneveket jól használhatók:

- Áttelepítése modellek nem befolyásolja a felhasználók kiszolgálók között. 
- Kiszolgáló felhasználóbarát nevek megkönnyíthető a felhasználóknak ne feledje. 
- Közvetlen felhasználók számára a különböző napszakokban különböző kiszolgálókon. 
- Közvetlen felhasználó különböző régiókban példányokhoz földrajzilag közelebb, például az Azure Traffic Manager használata esetén. 

A HTTPS-végpontot, amely adja vissza egy érvényes Azure Analysis Services-kiszolgáló neve alias szolgálhatnak. A végpont támogatnia kell a 443-as porton keresztül HTTPS és a port nem adható meg az URI azonosító.

![A hivatkozási formátumot használó alias](media/analysis-services-alias/aas-alias-browser.png)

Ha egy ügyfél, a másodlagos kiszolgáló nevét is meg kell adni használatával **hivatkozás: / /** protokoll formátumban. Például a Power BI Desktop:

![A Power BI Desktop-kapcsolat](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Alias létrehozása

Hozzon létre egy alias végpontot, használhatja bármelyik módszert, amely egy érvényes Azure Analysis Services-kiszolgáló nevét adja vissza. Például egy fájl az Azure Blob Storage valós kiszolgálót tartalmazó mutató hivatkozás neve, vagy hozzon létre és ASP.NET Web Forms keretrendszerre alkalmazás közzététele.

Ebben a példában a Visual Studio egy ASP.NET webes űrlapalkalmazás jön létre. A mesterlap referencia- és felhasználói vezérlő el lesznek távolítva a Default.aspx lap. A default.aspx fájl tartalma egyszerűen a következő lap direktívájában:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

A Page_Load esemény Default.aspx.cs a Response.Write() metódus használatával adja vissza az Azure Analysis Services-kiszolgáló neve.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Lásd még

[Ügyfél-függvénytárak](analysis-services-data-providers.md)   
[Csatlakoztassa a Power BI Desktop](analysis-services-connect-pbi.md)
