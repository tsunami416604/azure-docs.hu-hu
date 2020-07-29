---
title: 'Gyors útmutató: alkalmazás hozzáadása a Azure Active Directory (Azure AD) bérlőhöz'
description: Ebben a rövid útmutatóban egy katalógusbeli alkalmazást adhat hozzá az Azure Portalon az Azure Active Directory- (Azure AD-) bérlőhöz.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79d80a5b94a753b43715c1ccd8fc2059ff84cfca
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338027"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-azure-ad-tenant"></a>Gyors útmutató: alkalmazás hozzáadása a Azure Active Directory (Azure AD) bérlőhöz

Az Azure Active Directory (Azure AD) rendelkezik egy katalógussal, amely több ezer előre integrált alkalmazást tartalmaz. A szervezet által használt alkalmazások többsége valószínűleg már szerepel a katalógusban.

Miután hozzáadta az alkalmazást az Azure AD-bérlőhöz, a következőket teheti:

- Konfigurálja az alkalmazás tulajdonságait.
- Az alkalmazás felhasználói hozzáférésének kezelése feltételes hozzáférési házirenddel.
- Konfigurálja az egyszeri bejelentkezést, hogy a felhasználók Azure AD-beli hitelesítő adataikkal be tudják jelentkezni az alkalmazásba.

## <a name="prerequisites"></a>Előfeltételek

Ha alkalmazást szeretne hozzáadni az Azure AD-bérlőhöz, a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A következő szerepkörök egyike: globális rendszergazda, Felhőbeli alkalmazás rendszergazdája, alkalmazás rendszergazdája vagy az egyszerű szolgáltatásnév tulajdonosa.
- (Nem kötelező: [az alkalmazások megtekintésének](view-applications-portal.md)befejezése).

>[!IMPORTANT]
>Az ebben a rövid útmutatóban ismertetett lépések teszteléséhez nem éles környezet használatát javasoljuk.

## <a name="add-an-app-to-your-azure-ad-tenant"></a>Alkalmazás hozzáadása az Azure AD-bérlőhöz

Alkalmazás hozzáadása az Azure AD-bérlőhöz:

1. A [Azure Portal](https://portal.azure.com)a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.
2. A **Azure Active Directory** ablaktáblán válassza a **vállalati alkalmazások**lehetőséget. Megnyílik a **minden alkalmazás** panel, és az Azure ad-bérlőben található alkalmazások véletlenszerű mintája jelenik meg.
3. A **vállalati alkalmazások** ablaktáblán válassza az **új alkalmazás**lehetőséget. 
    ![Válassza az új alkalmazás lehetőséget, ha egy katalógus-alkalmazást szeretne hozzáadni a bérlőhöz](media/add-application-portal/new-application.png)
4. Váltson az új katalógus előzetes verziójára: az **alkalmazás hozzáadása lap**tetején található szalagcímen válassza ki azt a hivatkozást, amely az **új és továbbfejlesztett alkalmazás-katalógus kipróbálásához kattintson ide**.
5. Megnyílik a **Tallózás Azure ad-katalógus (előzetes verzió)** panel, amelyen a Felhőbeli platformok, a helyszíni alkalmazások és a Kiemelt alkalmazások csempéi láthatók. A **Kiemelt alkalmazások** szakaszban felsorolt alkalmazások rendelkeznek ikonokkal, amelyek azt jelzik, hogy támogatják-e az összevont egyszeri bejelentkezést (SSO) és a létesítést.
    ![Alkalmazás keresése név vagy kategória szerint](media/add-application-portal/browse-gallery.png)
6. Böngészhet a katalógusban a hozzáadni kívánt alkalmazáshoz, vagy megkeresheti az alkalmazást úgy, hogy beírja a nevét a keresőmezőbe. Ezután válassza ki az alkalmazást az eredmények közül. Az űrlapon szerkesztheti az alkalmazás nevét, hogy megfeleljen a szervezet igényeinek. Ebben a példában a GitHubot választottuk, és a nevet a **GitHub-test**értékre módosították.
    ![Bemutatja, hogyan adhat hozzá egy alkalmazást a katalógusból](media/add-application-portal/create-application.png)
    >[!TIP]
    >Ha a keresett alkalmazás nem szerepel a katalógusban, akkor rákattinthat a **saját alkalmazás létrehozása** elemre, majd az **alkalmazással kapcsolatos teendők** lehetőségre. a gyűjteményben válassza a **bármely más alkalmazás integrálása**elemet. A Microsoft már dolgozott számos alkalmazás-fejlesztővel, hogy előre konfigurálja őket az Azure AD-vel való együttműködéshez. Ezek azok az alkalmazások, amelyek megjelennek a katalógusban. Ha azonban a hozzáadni kívánt alkalmazás nem szerepel a listáján, létrehozhat egy új, általános, alkalmazást, és konfigurálhatja saját maga, vagy az azt létrehozó fejlesztő útmutatásával.
7. Válassza a **Létrehozás** lehetőséget. Megjelenik az első lépések lap, amelyen a beállításokkal konfigurálhatja az alkalmazást a vállalat számára.

Elkészült egy alkalmazás hozzáadásával. A következő rövid útmutató bemutatja, hogyan módosíthatja az emblémát, és szerkesztheti az alkalmazás egyéb tulajdonságait.

## <a name="next-steps"></a>További lépések

- [Alkalmazás konfigurálása](add-application-portal-configure.md)
- [Egyszeri bejelentkezés beállítása](add-application-portal-setup-sso.md)
- [Alkalmazás törlése](delete-application-portal.md)