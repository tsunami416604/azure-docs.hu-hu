<properties 
    pageTitle="開始使用 Azure Active Directory 和 Visual Studio 已連接服務 (MVC 專案)" 
    description="使用 Visual Studio 已連接服務連接 Azure AD 或建立 Azure AD 後，如何在 MVC 專案中開始使用 Azure Active Directory" 
    services="active-directory" 
    documentationCenter="" 
    authors="TomArcher" 
    manager="douge" 
    editor="tglee"/>

<tags 
    ms.service="active-directory" 
    ms.workload="web" 
    ms.tgt_pltfrm="vs-getting-started" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/03/2015" 
    ms.author="tarcher"/>


# 開始使用 Azure Active Directory 和 Visual Studio 已連接服務 (MVC 專案)

> [AZURE.SELECTOR]
> - [開始使用](vs-active-directory-dotnet-getting-started.md)
> - [發生了什麼事](vs-active-directory-dotnet-what-happened.md)

## 存取控制器之前需要驗證

專案中的所有控制器都加上 **Authorize** 屬性做裝飾。 此屬性要求使用者必須經過驗證才能存取這些控制器。 若要允許以匿名方式存取控制器，請從控制器中移除此屬性。 如果您要以更精確地設定權限，請將此屬性套用至每一個需要授權的方法，而非套用至控制器類別。

## 加入 SignIn / SignOut 控制項

若要新增到檢視的登入/登出控制項，您可以使用 **_LoginPartial.cshtml** 將功能加入您的檢視之一部分的檢視。 以下是範例的功能加入至標準 **_Layout.cshtml** 檢視。 (請注意 div 中具有類別 navbar-collapse 的最後一個元素)：

<pre>
            
    <!DOCTYPE html>
    &lt;html&gt;
    &lt;head&gt;
        <meta charset="utf-8" />
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       &lt;title&gt;@ViewBag.Title - My ASP.NET Application</title>
       @Styles.Render("~/Content/css")
       @Scripts.Render("~/bundles/modernizr")
   </head>
   &lt;body&gt;
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
                       &lt;li&gt;@Html.ActionLink("Home", "Index", "Home")</li>
                       &lt;li&gt;@Html.ActionLink("About", "About", "Home")</li>
                       &lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")</li>
                   </ul> 
                    
            <span style="background-color:yellow">@Html.Partial("_LoginPartial")</span>
             
                </div>
           </div>
       </div>
       <div class="container body-content">
           @RenderBody()
           <hr />
           &lt;footer&gt;
               &lt;p&gt;&copy; @DateTime.Now.Year - My ASP.NET Application</p>
           </footer>
       </div>
       @Scripts.Render("~/bundles/jquery")
       @Scripts.Render("~/bundles/bootstrap")
       @RenderSection("scripts", required: false)
   </body>
   </html>
          </pre>

[深入了解 Azure Active Directory](http://azure.microsoft.com/services/active-directory/)





