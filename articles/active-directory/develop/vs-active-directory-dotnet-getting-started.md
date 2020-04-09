---
title: Az Azure AD első lépései a .NET MVC projektekben | Azure
description: Az Azure Active Directory használatának első lépései a .NET MVC-projektekben, miután csatlakozott az Azure AD-hez, vagy létrehozta a Visual Studio csatlakoztatott szolgáltatásait
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: fe408e965c528db1d82b73ee7b20bbe3b3933657
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886126"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Az Azure Active Directory –ASP.NET MVC-projektek – első lépések

> [!div class="op_single_selector"]
> - [Bevezetés](vs-active-directory-dotnet-getting-started.md)
> - [mi történt](vs-active-directory-dotnet-what-happened.md)

Ez a cikk további útmutatást nyújt, miután hozzáadta az Active Directoryt egy ASP.NET MVC-projekthez a Visual Studio **Project > Connected Services** parancsán keresztül. Ha még nem adta hozzá a szolgáltatást a projekthez, ezt bármikor megteheti.

Lásd: [Mi történt az MVC-projekttel a](vs-active-directory-dotnet-what-happened.md) csatlakoztatott szolgáltatás hozzáadásakor végrehajtott módosításokért.

## <a name="requiring-authentication-to-access-controllers"></a>Hitelesítés megkövetelése a vezérlők eléréséhez

A projekt összes vezérlőjét az `[Authorize]` attribútum díszítette. Ehhez az attribútumhoz a felhasználó hitelesítésére van szükség, mielőtt elérne ezekhez a vezérlőkhöz. A vezérlő névtelen elérésének engedélyezéséhez távolítsa el ezt az attribútumot a vezérlőből. Ha az engedélyeket részletesebb szinten szeretné beállítani, alkalmazza az attribútumot minden olyan metódusra, amely hez engedélyezés szükséges, ahelyett, hogy a vezérlőosztályra alkalmazna.

## <a name="adding-signin--signout-controls"></a>Bejelentkezési/ kijelentkezési vezérlők hozzáadása

A SignIn/SignOut vezérlők megtekintéséhez a `_LoginPartial.cshtml` részleges nézetben adhat hozzá funkciót az egyik nézethez. Íme egy példa a szabványos `_Layout.cshtml` nézethez hozzáadott funkciókra. (Megjegyzés: az utolsó elem a div osztály navbar-összeomlás):

```html
<!DOCTYPE html>
 <html>
 <head>
     <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@ViewBag.Title - My ASP.NET Application</title>
    @Styles.Render("~/Content/css")
    @Scripts.Render("~/bundles/modernizr")
</head>
<body>
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
                    <li>@Html.ActionLink("About", "About", "Home")</li>
                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
                </ul>
                @Html.Partial("_LoginPartial")
            </div>
        </div>
    </div>
    <div class="container body-content">
        @RenderBody() 
        <hr />
        <footer>
            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>
        </footer>
    </div>
    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

## <a name="next-steps"></a>További lépések

- [Az Azure Active Directory hitelesítési forgatókönyvei](authentication-scenarios.md)
- [Bejelentkezés hozzáadása a Microsofttal egy ASP.NET webalkalmazáshoz](quickstart-v2-aspnet-webapp.md)
