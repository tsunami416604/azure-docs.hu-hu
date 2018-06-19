---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: f1dc23729f32a7a9535b887acf638cf5464c24bd
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "36206115"
---
## <a name="register-your-application"></a>Alkalmazás regisztrálása

Regisztrálja az alkalmazást, és adja hozzá az alkalmazás regisztrációs adatokat a megoldás, két lehetőség közül választhat:

### <a name="option-1-express-mode"></a>1. lehetőség: Expressz mód

Az alkalmazás a következőképpen gyorsan regisztrálhatja:

1. Az alkalmazás regisztrálása a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)
2.  Adjon meg egy nevet az alkalmazás és az e-maileket
3.  Győződjön meg arról, hogy az interaktív telepítés beállítást
4.  Kövesse az utasításokat az alkalmazás átirányítási URL-cím hozzáadása

### <a name="option-2-advanced-mode"></a>2. lehetőség: Speciális módban

Az alkalmazás regisztrálása és az alkalmazás regisztrációs adatokat hozzáadni a megoldás, tegye a következőket:

1. Lépjen a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app) alkalmazás regisztrálása
2. Adjon meg egy nevet az alkalmazás és az e-maileket 
3. Győződjön meg arról, hogy az interaktív telepítés beállítás nincs bejelölve
4. Kattintson a `Add Platform`, majd jelölje be `Web`
5. Lépjen vissza a Visual Studio és a Megoldáskezelőben, válassza ki a projektet, és tekintse meg a Tulajdonságok ablak (Ha nem látja a Tulajdonságok ablak az F4)
6. Módosítsa az SSL engedélyezve `True`
7. Kattintson jobb gombbal a projektre a Visual Studio, majd válassza a **tulajdonságok**, és a **webes** fülre. Az a *kiszolgálók* szakaszban módosítsa a *projekt URL-címe* kell lennie az SSL URL-cím
8. Másolja az SSL URL-címet, és vegye fel az URL-cím az átirányítási URL-t átirányítási URL-t a regisztrációs portál közül:<br/><br/>![Projekt tulajdonságai](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
9. Adja hozzá a következő `web.config` a legfelső szintű mappában található a szakasz `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

10. Cserélje le `ClientId` az imént regisztrált alkalmazás azonosítójával
11. Cserélje le `redirectUri` a SSL URL-címet a projekt

