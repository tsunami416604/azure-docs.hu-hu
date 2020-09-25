---
title: 'Rövid útmutató: a Microsoft IDENTIFY platform-alkalmazás fiókjainak módosítása | Azure'
description: Ebben a rövid útmutatóban a Microsoft Identity platformmal regisztrált alkalmazást konfigurálhatja, hogy megváltoztassa, kik vagy milyen fiókok férhessenek hozzá az alkalmazáshoz.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: d143bde9c22bc726f00b5c209d1b7fbc131905b0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258013"
---
# <a name="quickstart-modify-the-accounts-supported-by-an-application"></a>Gyors útmutató: alkalmazás által támogatott fiókok módosítása

Az alkalmazások Microsoft Identity Platformon való regisztrálásakor esetenként az a cél, hogy az alkalmazást csak a szervezethez tartozó felhasználók érhessék el. Előfordulhat az is, hogy az alkalmazást olyan felhasználók számára is szeretné elérhetővé tenni, akik külső szervezetekhez tartoznak, vagy nem feltétlenül tartoznak valamely szervezethez (a személyes fiókok).

Ebből a rövid útmutatóból megtudhatja, hogyan változtassa meg alkalmazása beállításait annak módosításához, hogy kik, illetve mely érhetik el az alkalmazást.

## <a name="prerequisites"></a>Előfeltételek

* A rövid útmutató befejezése [: alkalmazás regisztrálása a Microsoft Identity platformon](quickstart-register-app.md)

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Bejelentkezés az Azure Portalra és az alkalmazás kiválasztása

Mielőtt konfigurálhatná az alkalmazást, végre kell hajtania az alábbi lépéseket:

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatást, majd válassza a **Alkalmazásregisztrációk**lehetőséget.
1. Keresse meg és jelölje ki a konfigurálni kívánt alkalmazást. Miután kijelölte az alkalmazást, az alkalmazás **Áttekintés** lapja vagy regisztrációs főoldala jelenik meg.
1. A lépéseket követve [módosítsa az alkalmazásregisztrációt különböző fiókok támogatásának engedélyezéséhez](#change-the-application-registration-to-support-different-accounts).
1. Ha egyoldalas alkalmazása van, [engedélyezze az OAuth 2.0 implicit engedély használatát](#enable-oauth-20-implicit-grant-for-single-page-applications).

## <a name="change-the-application-registration-to-support-different-accounts"></a>Alkalmazásregisztráció módosítása különböző fiókok támogatásának engedélyezéséhez

Ha olyan alkalmazást ír, amelyet a szervezeten kívüli ügyfelei és partnerei számára is elérhetővé szeretne tenni, frissítenie kell az alkalmazás definícióját az Azure Portalon.

> [!IMPORTANT]
> Az Azure AD megköveteli, hogy a több-bérlős alkalmazások alkalmazásazonosító URI-ja (App ID URI) globálisan egyedi legyen. Az alkalmazásazonosító URI egy módszer, amellyel az alkalmazás a protokollüzenetekben azonosítható. Egybérlős alkalmazás esetében az alkalmazásazonosító URI-nak csak a bérlőn belül kell egyedinek lennie. Több-bérlős alkalmazás esetében azonban globálisan egyedinek kell lennie, hogy az Azure AD megtalálja az alkalmazást a különböző bérlők közt. A globális egyediség azzal kényszeríthető ki, hogy a rendszer megköveteli, hogy az alkalmazásazonosító URI egy olyan egy gazdanévvel rendelkezzen, amely egyezik az Azure AD-bérlő egy ellenőrzött tartományával. Ha például a bérlő neve contoso.onmicrosoft.com, érvényes alkalmazásazonosító URI lehet a `https://contoso.onmicrosoft.com/myapp`. Ha a bérlő a contoso.com ellenőrzött tartománnyal rendelkezik, az érvényes alkalmazásazonosító URI ugyanígy a `https://contoso.com/myapp` lehet. Ha az alkalmazásazonosító URI nem ezt a mintát követi, az alkalmazások több-bérlősként való konfigurálása meghiúsul.

### <a name="to-change-who-can-access-your-application"></a>Az alkalmazást elérők körének módosítása

1. Az alkalmazás **Áttekintés** lapján válassza a **Hitelesítés** szakaszt, és módosítsa a **Támogatott fióktípusok** alatt megadott értéket.
    * Válassza a **Csak az ebben a címtárban található fiókok** lehetőséget, ha üzletági alkalmazást készít. Ez a lehetőség nem érhető el, ha nem regisztrálta az alkalmazást valamely címtárban.
    * Válassza a **Tetszőleges szervezeti címtárban található fiókok** lehetőséget, ha szeretne minden üzleti és az oktatási ügyfelet megcélozni.
    * Válassza a **Tetszőleges szervezeti címtárban található fiókok és személyes Microsoft-fiókok** lehetőséget, ha a lehető legszélesebb ügyfélkört szeretné megcélozni.
1. Kattintson a **Mentés** gombra.

## <a name="enable-oauth-20-implicit-grant-for-single-page-applications"></a>OAuth 2.0 implicit engedély használatának engedélyezése egyoldalas alkalmazásoknál

Az egyoldalas alkalmazások (SPA-k) általában egy böngészőben futó JavaScript-intenzív előtérrel rendelkeznek, amely az alkalmazás webes API-s hátterét hívja meg az üzleti logika végrehajtatására. Az Azure AD-ben üzemeltetett egyoldalas alkalmazások esetében az OAuth 2.0 implicit engedélyezés használatával hitelesítheti a felhasználókat az Azure AD-ben, és szerezheti be a jogkivonatot, amelynek használatával biztosíthatja a hívásokat az alkalmazás JavaScript-ügyfeléről a háttér webes API-jára.

Miután a felhasználó megadta a hozzájárulást, ugyanez a hitelesítési protokoll használható a jogkivonatok beszerzéséhez az ügyfél és az alkalmazáshoz konfigurált egyéb webes API erőforrások közötti hívások biztosításához. Ha többet szeretne megtudni az implicit engedélyezésről és annak alkalmazhatóságáról az egyes alkalmazás-forgatókönyvekben, ismerkedjen meg az Azure AD [1.0](../azuread-dev/v1-oauth2-implicit-grant-flow.md)-s és [v2.0](v2-oauth2-implicit-grant-flow.md)-s verziójára vonatkozó OAuth 2.0 implicit engedélyezési folyamattal.

Az OAuth 2.0 implicit engedély alapértelmezés szerint le van tiltva az alkalmazásokon. Ha engedélyezni szeretné az OAuth 2.0 implicit engedély használatát az alkalmazásánál, kövesse az alábbi lépéseket.

### <a name="to-enable-oauth-20-implicit-grant"></a>Az OAuth 2.0 implicit engedélyezési folyamat engedélyezése

1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatást, majd válassza a **Alkalmazásregisztrációk**lehetőséget.
1. Keresse meg és jelölje ki a konfigurálni kívánt alkalmazást. Miután kijelölte az alkalmazást, az alkalmazás **Áttekintés** lapja vagy regisztrációs főoldala jelenik meg.
1. Az alkalmazás **Áttekintés** lapján válassza a **Hitelesítés** szakaszt.
1. A **Speciális beállítások** alatt keresse meg au **Implicit engedély** szakaszt.
1. Jelölje ki az **Azonosító-jogkivonatok** vagy a **Hozzáférési jogkivonatok** lehetőséget, vagy mindkettőt.
1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Védjegyzési útmutató az alkalmazásokhoz](howto-add-branding-in-azure-ad-apps.md)
