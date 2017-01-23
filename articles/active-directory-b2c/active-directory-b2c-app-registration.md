---
title: "Azure Active Directory B2C: Alkalmazás regisztrációja | Microsoft Docs"
description: "Hogyan regisztrálhatja alkalmazását Azure Active Directory B2C-vel?"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 0b87ec728891d4f64bb7b2e4b7b4f0f2276b1ffc
ms.openlocfilehash: ca75ddf8d099f411c4ae1af00e922f26e145f939


---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: Az alkalmazás regisztrációja
## <a name="prerequisite"></a>Előfeltétel
A felhasználói regisztrációt és bejelentkezést elfogadó alkalmazás létrehozásához először regisztrálnia kell az alkalmazást egy Azure Active Directory B2C-bérlővel. Hozzon létre egy saját bérlőt az [Azure AD B2C bérlő létrehozása](active-directory-b2c-get-started.md) részben ismertetett lépések segítségével. Miután elvégezte ezeket a lépéseket, a B2C funkciók panelje rögzítésre kerül a kezdőpulton.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>Lépjen a B2C funkciók paneljére
Ha a B2C funkciók panelje rögzítésre kerül a kezdőpulton, azonnal látni fogja a panelt, amint B2C-bérlő globális rendszergazdájaként bejelentkezik az [Azure Portalra](https://portal.azure.com/).

A panelre úgy is beléphet, hogy a **Browse** (Tallózás), majd az **Azure AD B2C** lehetőségre kattint az [Azure Portal](https://portal.azure.com/) bal oldali navigációs ablaktáblájában.

> [!IMPORTANT]
> A B2C bérlő globális rendszergazdájának kell lennie ahhoz, hogy hozzáférhessen a B2C funkciók panelhez. Más bérlők globális rendszergazdái vagy felhasználói nem férhetnek hozzá a panelhez.  A B2C-bérlőre az Azure Portal jobb felső sarkában található bérlőváltó használatával válthat át.
> 
> 

## <a name="register-an-application"></a>Egy alkalmazás regisztrálása
1. Kattintson az Azure Portal B2C funkciók panelén az **Applications** (Alkalmazások) lehetőségre.
2. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
3. Adjon meg az alkalmazáshoz egy olyan **nevet**, amely a felhasználók számára ismerteti az alkalmazást. Adja meg például a „Contoso B2C alkalmazás” nevet.
4. Ha webalapú alkalmazásról van szó, az **Include web app / web API** (Webalkalmazással/webes API-val együtt) váltógombot állítsa a **Yes** (Igen) állásba. A **válasz URL-címek** olyan végpontok, ahol az Azure AD B2C visszalép az alkalmazás által kért jogkivonatokhoz. Adja meg például a következőt: `https://localhost:44321/`. Ha a webalkalmazás az Azure AD B2C által biztonságossá tett webes API-kat is meg fog hívni, ajánlott egy **Aplication Secret** (Alkalmazástitok) létrehozása is a **Generate Key** (Kulcs létrehozása) gombra kattintva.
   
   > [!NOTE]
   > Az **Application Secret** (Alkalmazástitok) fontos biztonsági hitelesítő adat, amelynek megfelelő biztonságáról gondoskodni kell.
   > 
   > 
5. Ha mobilalkalmazásról van szó, az **Include native client** (Natív ügyféllel együtt) váltógombot állítsa a **Yes** (Igen) állásba. Másolja le az automatikusan létrehozott, alapértelmezett **átirányítási URI**-t.
6. Kattintson a **Create** (Létrehozás) gombra az alkalmazás regisztrálásához.
7. Kattintson az imént létrehozott alkalmazásra, és másolja le az alkalmazás globálisan egyedi **ügyfél-azonosítóját**, amelyet később a kódjában fog használni.

> [!IMPORTANT]
> A B2C-funkciók paneljén létrehozott alkalmazásokat ugyanazon a helyen kell kezelni. Ha a B2C-alkalmazásokat a PowerShell vagy egy másik portál használatával szerkeszti, a rendszer nem támogatja többé az alkalmazásokat, és valószínűleg nem fognak működni az Azure AD B2C-vel.
> 
> 

## <a name="build-a-quick-start-application"></a>Első lépések alkalmazás készítése
Miután az Azure AD B2C-vel regisztrált egy alkalmazást, gyors üzembe helyezési oktatóanyagunk segítségével elsajátíthatja a használatát. Az alábbiakban néhány javaslatot olvashat:

[!INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]




<!--HONumber=Jan17_HO1-->


