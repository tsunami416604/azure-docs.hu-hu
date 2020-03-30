---
title: Azure Analysis Services aliaskiszolgáló-nevek | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre Azure Analysis Services-kiszolgálónév-aliasok. A felhasználók ezután a kiszolgálónév helyett rövidebb aliasnévvel csatlakozhatnak a kiszolgálóhoz.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5e7017fad90e32cb8c4b952987fe248e463e4d03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572296"
---
# <a name="alias-server-names"></a>Aliaskiszolgáló-nevek

A kiszolgálónév-alias használatával a felhasználók a kiszolgáló név helyett rövidebb *aliassal* csatlakozhatnak az Azure Analysis Services-kiszolgálóhoz. Ügyfélalkalmazásból való csatlakozáskor az alias végpontként van megadva a **link://** protokollformátum használatával. A végpont ezután a valódi kiszolgáló nevét adja vissza a csatlakozáshoz.

Az aliaskiszolgáló-nevek a következőkhez jók:

- Modellek áttelepítése kiszolgálók között a felhasználók befolyásolása nélkül. 
- A barátságos kiszolgálóneveket a felhasználók könnyebben megjegyezik. 
- Közvetlen felhasználók különböző szerverek különböző napszakokban. 
- Különböző régiókban lévő felhasználókat irányítson földrajzilag közelebb álló példányokra, például az Azure Traffic Manager használatakor. 

Bármely HTTPS-végpont, amely érvényes Azure Analysis Services-kiszolgálónevet ad vissza, aliasként szolgálhat. A végpontnak támogatnia kell a HTTPS-t a 443-as porton keresztül, és a portot nem szabad megadni az URI-ban.

![Alias hivatkozásformátumhasználatával](media/analysis-services-alias/aas-alias-browser.png)

Ügyfélről való csatlakozáskor az aliaskiszolgáló neve **link://** protokollformátumban kerül beírásra. Például a Power BI Desktopban:

![Power BI Desktop-kapcsolat](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Alias létrehozása

Alias-végpont létrehozásához bármilyen módszert használhat, amely érvényes Azure Analysis Services-kiszolgálónevet ad vissza. Például egy hivatkozást egy fájlt az Azure Blob Storage tartalmazza a valódi kiszolgáló nevét, vagy hozzon létre és tegyen közzé egy ASP.NET webűrlap-alkalmazás.

Ebben a példában egy ASP.NET webűrlap-alkalmazás jön létre a Visual Studióban. A mesteroldal hivatkozása és a felhasználói vezérlő törlődik a Default.aspx oldalról. A Default.aspx fájl tartalma egyszerűen a következő oldalirányelv:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

A Page_Load esemény Default.aspx.cs a Response.Write() metódust használja az Azure Analysis Services kiszolgálónevének adására.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Lásd még

[Ügyféltárak](analysis-services-data-providers.md)   
[Csatlakozás a Power BI Desktopból](analysis-services-connect-pbi.md)
