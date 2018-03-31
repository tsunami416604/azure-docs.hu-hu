---
title: Ismerkedés az Azure AD-t a Visual Studio .NET MVC-projektek |} Microsoft Docs
description: Ismerkedés az Azure Active Directoryval .NET MVC projekt egy Visual Studio használatával Azure AD létrehozása vagy csatlakoztatása után kapcsolódó szolgáltatások
services: active-directory
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: 1c8b6a58-5144-4965-a905-625b9ee7b22b
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: 5ad919be46cf771f50b646207090c19de5248eb6
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Ismerkedés az Azure Active Directory (ASP.NET MVC-projektek)

> [!div class="op_single_selector"]
> - [Első lépések](vs-active-directory-dotnet-getting-started.md)
> - [mi történt](vs-active-directory-dotnet-what-happened.md)

Ez a cikk útmutatást további Active Directory egy ASP.NET MVC projekt keresztül történő hozzáadása után a **Project > kapcsolódó szolgáltatások** a Visual Studio parancsot. Ha a szolgáltatás már nem felvételét a projekthez, bármikor is megteheti.

Lásd: [Mi történt a MVC projekt?](vs-active-directory-dotnet-what-happened.md) amikor a csatlakoztatott szolgáltatás hozzáadása a projekthez végzett módosítások.

## <a name="requiring-authentication-to-access-controllers"></a>Hozzáférés-vezérlők hitelesítést igénylő

A projekt lévő összes rendelkező volt adorned a `[Authorize]` attribútum. Ez az attribútum a felhasználónak hitelesíteni ezeket a tartományvezérlőket elérése előtt. Ahhoz, hogy a tartományvezérlőt a névtelenül elérhető, távolítsa el ezt az attribútumot a vezérlő. Ha szeretne egy részletesebb szinten adja meg az engedélyeket, az attribútumot minden módszer, amely helyett telepítené azt a vezérlő osztályhoz engedélyezési alkalmazza.

## <a name="adding-signin--signout-controls"></a>Bejelentkezés felvétele / SignOut szabályozza

A bejelentkezés/SignOut vezérlők hozzáadása a megtekintése, használhatja a `_LoginPartial.cshtml` részleges nézetre, és a Funkciók hozzáadása a nézetek közül. Példa a normál hozzáadott funkcióinak `_Layout.cshtml` nézet. (Megjegyzés: az utolsó elem az a osztály navigációs sávja összecsukása div):

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

- [Az Azure Active Directory hitelesítési forgatókönyvei](active-directory-authentication-scenarios.md)
- [Bejelentkezés a Microsoft ASP.NET webalkalmazás hozzáadása](guidedsetups/active-directory-aspnetwebapp-v1.md)
