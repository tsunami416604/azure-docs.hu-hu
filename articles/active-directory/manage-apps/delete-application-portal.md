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
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec0b3ef559abe1c65872d8ecf87f63e6ff3ed4b0
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223907"
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
- Nem kötelező: az [egyszeri bejelentkezés beállításának](add-application-portal-setup-sso.md)befejezése.

>[!IMPORTANT]
>Az ebben a rövid útmutatóban ismertetett lépések teszteléséhez használjon nem termékrendszer-környezetet.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Alkalmazás törlése az Azure AD-bérlőből

Alkalmazás törlése az Azure AD-bérlőből:

1. Az Azure AD-portálon válassza a **vállalati alkalmazások**lehetőséget. Ezután keresse meg és válassza ki a törölni kívánt alkalmazást. Ebben az esetben törölte az előző rövid útmutatóban hozzáadott **GitHub_test** alkalmazást.
1. A bal oldali ablaktábla **kezelés** szakaszában válassza a **Tulajdonságok**lehetőséget.
1. Válassza a **Törlés**lehetőséget, majd kattintson az **Igen** gombra annak megerősítéséhez, hogy törölni szeretné az alkalmazást az Azure ad-bérlőből.


## <a name="next-steps"></a>További lépések

- [Alkalmazások kezelése – ajánlott eljárások](application-management-fundamentals.md)
- [Alkalmazások kezelése – gyakori forgatókönyvek](common-scenarios.md)
- [Az alkalmazások felügyeletének láthatósága és szabályozása](cloud-app-security.md)