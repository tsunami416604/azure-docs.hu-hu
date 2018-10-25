---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 15db2192703971a8056df34343c427db11c8411a
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988507"
---
## <a name="register-your-application"></a>Alkalmazás regisztrálása

Regisztrálja az alkalmazást, és adja hozzá az alkalmazás regisztrációs adatok a megoldáshoz, két lehetősége van:

### <a name="option-1-express-mode"></a>1. lehetőség: Expressz mód

Az alkalmazás gyorsan regisztrálhatja az alábbiak szerint:

1. Regisztrálja az alkalmazást a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure).
2. Adjon meg egy nevet az alkalmazás és az e-maileket.
3. Győződjön meg arról, hogy az interaktív telepítés esetén alkalmazott beállítás be van jelölve.
4. Kövesse az utasításokat egy átirányítási URL-cím hozzáadása az alkalmazáshoz.

### <a name="option-2-advanced-mode"></a>2. lehetőség: Speciális módban

Az alkalmazása regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való hozzáadásához tegye a következőket:

1. Az alkalmazás regisztrálásához lépjen a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app).
2. Adjon meg egy nevet az alkalmazás és az e-maileket.
3. Ellenőrizze, hogy az interaktív telepítés a beállítás nincs bejelölve
4. Válassza ki `Add Platform`, majd válassza ki `Web`.
5. Lépjen vissza a Visual Studio és a Megoldáskezelőben, válassza ki a projektet, és tekintse meg a Tulajdonságok ablak (Ha nem látja a Tulajdonságok ablakban, az F4)
6. Az SSL engedélyezve van módosítsa `True`.
7. Kattintson a jobb gombbal a projektre a Visual Studióban, majd kattintson a **tulajdonságok**, és a **webes** fülre. Az a *kiszolgálók* szakaszban módosítsa a *projekt URL-címe* kell az SSL URL-címet.
8. Másolja az SSL URL-címet, és adja hozzá az URL-cím átirányítási URL-címeket a regisztrációs portál listájában átirányítási URL-címek listájához:<br/><br/>![Projekt tulajdonságai](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
9. Adja hozzá a következő a `web.config` a legfelső szintű mappában található a szakasz `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

10. Cserélje le `ClientId` az imént regisztrált alkalmazás azonosítójával.
11. Cserélje le `redirectUri` a projekt SSL URL-címével.
