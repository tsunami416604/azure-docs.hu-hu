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
ms.openlocfilehash: 02d12538fce5ccc3905ea1170fc1a8324309004f
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85955998"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>Gyors útmutató: alkalmazás törlése a Azure Active Directory (Azure AD) bérlőből

Ez a rövid útmutató a Azure Portal használatával törli az Azure AD-bérlőhöz hozzáadott alkalmazást.

## <a name="prerequisites"></a>Előfeltételek

Ha törölni szeretne egy alkalmazást az Azure AD-bérlőből, a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A következő szerepkörök egyike: globális rendszergazda, Felhőbeli alkalmazás rendszergazdája, alkalmazás rendszergazdája vagy az egyszerű szolgáltatásnév tulajdonosa.
- (Nem kötelező: [az alkalmazások megtekintésének](view-applications-portal.md)befejezése).
- (Nem kötelező: [alkalmazás hozzáadása](add-application-portal.md)).
- (Nem kötelező: [alkalmazás konfigurálásának](add-application-portal-configure.md)befejezése).
- (Nem kötelező: az [egyszeri bejelentkezés beállításának](add-application-portal-setup-sso.md)befejezése).

>[!IMPORTANT]
>Az ebben a rövid útmutatóban ismertetett lépések teszteléséhez nem éles környezet használatát javasoljuk.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Alkalmazás törlése az Azure AD-bérlőből

Alkalmazás törlése az Azure AD-bérlőből:

1. Az Azure AD-portálon válassza a **vállalati alkalmazások** elemet, majd keresse meg és válassza ki a törölni kívánt alkalmazást. Ebben az esetben töröljük az előző rövid útmutatóban hozzáadott GitHub_test alkalmazást.
2. A navigáció kezelés szakaszában válassza a **Tulajdonságok**lehetőséget.
3. Válassza a Törlés lehetőséget, majd kattintson az Igen gombra annak megerősítéséhez, hogy törölni szeretné az alkalmazást az Azure AD-bérlőből.


## <a name="next-steps"></a>További lépések

- [Alkalmazások kezelése – ajánlott eljárások](application-management-fundamentals.md)
- [Alkalmazások kezelése – gyakori forgatókönyvek](common-scenarios.md)
- [Az alkalmazások felügyeletének láthatósága és szabályozása](cloud-app-security.md)