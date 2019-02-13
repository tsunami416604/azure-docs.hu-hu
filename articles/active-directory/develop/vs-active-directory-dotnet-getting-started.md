---
title: Ismerkedés az Azure AD-t a Visual Studio .NET MVC-projektek
description: Ismerkedés a .NET MVC-projektek az Azure Active Directory használatával, miután csatlakozik, vagy létrehozása a Visual Studio használata az Azure AD-hez kapcsolódó szolgáltatásokkal
services: active-directory
author: ghogen
manager: douge
ms.assetid: 1c8b6a58-5144-4965-a905-625b9ee7b22b
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bbbef3f48eb55c863fdd286113297d79f9b9e02
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208503"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Ismerkedés az Azure Active Directory (az ASP.NET MVC-projektek)

> [!div class="op_single_selector"]
> - [Első lépések](vs-active-directory-dotnet-getting-started.md)
> - [mi történt](vs-active-directory-dotnet-what-happened.md)

Ez a cikk a további útmutatást nyújt az Active Directory egy ASP.NET MVC-projektben keresztül történő hozzáadása után a **Project > csatlakoztatott szolgáltatás** parancsot a Visual Studio. Ha még nem hozzáadott a szolgáltatás a projekthez, bármikor megteheti.

Lásd: [Mi történt az MVC-projektemmel?](vs-active-directory-dotnet-what-happened.md) módosítások a projekthez a csatlakoztatott szolgáltatás hozzáadása során.

## <a name="requiring-authentication-to-access-controllers"></a>Hitelesítés, hozzáférés-vezérlő megkövetelése

A projektben lévő összes voltak adorned együtt a `[Authorize]` attribútum. Ez az attribútum a felhasználónak hitelesíthetők ezek a tartományvezérlők elérése előtt. Ahhoz, hogy a tartományvezérlőt a névtelenül elérhető, távolítsa el ezt az attribútumot a vezérlő. Ha szeretne részletesebben, állítsa be az engedélyeket, az attribútum vonatkoznak minden módszer, amely engedélyezési ahelyett hogy a vezérlő osztályhoz.

## <a name="adding-signin--signout-controls"></a>Bejelentkezés felvétele / kijelentkezés szabályozza

A bejelentkezés és kijelentkezés vezérlők hozzáadása a megtekintése, használhatja a `_LoginPartial.cshtml` a Funkciók hozzáadása a nézetekhez egyik részleges nézetet. Íme egy példa hozzáadja a normál funkciók `_Layout.cshtml` megtekintése. (Vegye figyelembe az utolsó elem az a osztály navigációs sáv összecsukása a div):

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

- [Hitelesítési forgatókönyvek az Azure Active Directory](authentication-scenarios.md)
- [Jelentkezzen be a Microsoft ASP.NET-webalkalmazás hozzáadása](quickstart-v1-aspnet-webapp.md)
