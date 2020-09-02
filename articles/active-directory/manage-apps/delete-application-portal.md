---
title: 'Gyors útmutató: alkalmazás törlése a Azure Active Directory (Azure AD) bérlőből'
description: Ez a rövid útmutató a Azure Portal használatával törli az alkalmazást a Azure Active Directory (Azure AD) bérlőből.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: 659d136695943d846fe57986d4b64a37f0d8f30e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89300119"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>Gyors útmutató: alkalmazás törlése a Azure Active Directory (Azure AD) bérlőből

Ez a rövid útmutató a Azure Portal használatával törli az Azure Active Directory (Azure AD) bérlőhöz hozzáadott alkalmazást.

## <a name="prerequisites"></a>Előfeltételek

Ha törölni szeretne egy alkalmazást az Azure AD-bérlőből, a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A következő szerepkörök egyike: globális rendszergazda, Felhőbeli alkalmazás rendszergazdája, alkalmazás rendszergazdája vagy az egyszerű szolgáltatásnév tulajdonosa.
- Nem kötelező: az [alkalmazások megtekintésének](view-applications-portal.md)befejezése.
- Nem kötelező: az [alkalmazás hozzáadásának](add-application-portal.md)befejezése.
- Nem kötelező: [alkalmazás konfigurálásának](add-application-portal-configure.md)befejezése.
- Nem kötelező: a [felhasználók hozzárendelésének befejezése egy alkalmazáshoz](add-application-portal-assign-users.md).
- Nem kötelező: az [egyszeri bejelentkezés beállításának](add-application-portal-setup-sso.md)befejezése.

>[!IMPORTANT]
>A rövid útmutató lépéseinek teszteléséhez használjon nem éles környezetet.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Alkalmazás törlése az Azure AD-bérlőből

Alkalmazás törlése az Azure AD-bérlőből:

1. Az Azure AD-portálon válassza a **vállalati alkalmazások**lehetőséget. Ezután keresse meg és válassza ki a törölni kívánt alkalmazást. Ebben az esetben törölte az előző rövid útmutatóban hozzáadott **GitHub_test** alkalmazást.
1. A bal oldali ablaktábla **kezelés** szakaszában válassza a **Tulajdonságok**lehetőséget.
1. Válassza a **Törlés**lehetőséget, majd kattintson az **Igen** gombra annak megerősítéséhez, hogy törölni szeretné az alkalmazást az Azure ad-bérlőből.

> [!TIP]
> Az Graph API segítségével automatizálhatja az alkalmazások felügyeletét: az [alkalmazások kezelésének automatizálása Microsoft Graph API-val](https://docs.microsoft.com/graph/application-saml-sso-configure-api).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült ezzel a rövid útmutatóval, érdemes törölni az alkalmazást a tesztelési bérlő tisztításához. Az alkalmazás törlése ebben a rövid útmutatóban szerepelt.

## <a name="next-steps"></a>További lépések

Elvégezte a gyors üzembe helyezési sorozatot! Következő lépésként olvassa el az App Management ajánlott eljárásait ismertető témakört.
> [!div class="nextstepaction"]
> [Alkalmazások kezelése – ajánlott eljárások](application-management-fundamentals.md)
