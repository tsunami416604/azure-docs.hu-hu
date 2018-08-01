---
title: Alkalmazás elrejtése a felhasználói élmény az Azure Active Directoryban |} A Microsoft Docs
description: Alkalmazás elrejtése a felhasználói élmény az Azure Active Directory hozzáférési paneljein vagy Office 365 kilövők módja.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/04/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 55f80396df4cbfe7d0a16a6a5066b68aadc0bdd3
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39369344"
---
# <a name="hide-an-application-from-users-experience-in-azure-active-directory"></a>Alkalmazás elrejtése a felhasználói élmény az Azure Active Directoryban

Ha egy alkalmazás, amelyeket nem kíván megjeleníteni a felhasználók hozzáférési paneljein vagy Office 365 kilövők, lehetőség van az alkalmazás csempéjére elrejtéséhez.  A felhasználó alkalmazás kilövők alkalmazások elrejtése a következő két lehetőség érhetők el.

- Egy külső alkalmazás a felhasználók hozzáférési paneljein és az Office 365 alkalmazás kilövők elrejtése
- Minden Office 365-alkalmazások a felhasználók hozzáférési paneljein elrejtése

Elrejti a az alkalmazás felhasználóinak továbbra is engedélye arra, hogy az alkalmazás, de nem látják őket az alkalmazás kilövők jelennek meg. A vállalati alkalmazások kezelésére a megfelelő engedélyekkel kell rendelkeznie, és a címtár globális rendszergazdának kell lennie.


## <a name="hiding-an-application-from-users-end-user-experiences"></a>Az alkalmazás a felhasználó végfelhasználói élményt elrejtése
Az alábbi lépéseket, használhatja a helyzettől függően a hozzáférési panelen alkalmazások elrejtése.

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>Hogyan elrejtése a felhasználó hozzáférési panelen és az Office 365 alkalmazás kilövők egy külső alkalmazás?
Az alábbi lépések segítségével alkalmazás elrejtése a felhasználó hozzáférési panelen és az Office 365 alkalmazás kilövők.

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2.  Válassza ki **minden szolgáltatás**, adja meg **Azure Active Directory** a szövegmezőbe, és válassza ki a **Enter**.
3.  Az a **Azure Active Directory - *directoryname***  képernyő (azt jelenti, az Azure ad Szolgáltatáshoz képernyőn a címtár kezeli), válassza ki **vállalati alkalmazások**.
![Vállalati alkalmazások](./media/hide-application-from-user-portal/app1.png)
4.  Az a **vállalati alkalmazások** képernyőn válassza ki **minden alkalmazás**. Kezelheti az alkalmazások listáját láthatja.
5.  Az a **nagyvállalati alkalmazások – minden alkalmazás** lapon válasszon ki egy alkalmazást.</br>
![Vállalati alkalmazások](./media/hide-application-from-user-portal/app2.png)
6.  Az a ***appname*** képernyő (azaz a képernyő a cím a kijelölt alkalmazás nevét), válassza a tulajdonságok.
7.  Az a  ***appname* -tulajdonságok** képernyőn válassza ki **Igen** a **látható a felhasználók számára?**.
![Vállalati alkalmazások](./media/hide-application-from-user-portal/app3.png)
8.  Válassza ki a **mentése** parancsot.

### <a name="how-do-i-hide-office-365-applications-from-users-access-panel"></a>Hogyan elrejtése a felhasználó hozzáférési panelen Office 365-alkalmazások?

Az alábbi lépések segítségével a hozzáférési panelen az összes Office 365-alkalmazások elrejtése. Ezek az alkalmazások továbbra is meg fognak jelenni az Office 365 portálra.

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2.  Válassza ki **minden szolgáltatás**, adja meg **Azure Active Directory** a szövegmezőbe, és válassza ki a **Enter**.
3.  Az a **Azure Active Directory - *directoryname***  képernyő (azt jelenti, az Azure ad Szolgáltatáshoz képernyőn a címtár kezeli), válassza ki **felhasználói beállítások**.
4.  Az a **felhasználói beállítások** képernyő alatt **vállalati alkalmazások** kiválasztása **Igen** a **csak a tekintse meg az Office 365 portálonOffice365-alkalmazások**.

![Vállalati alkalmazások](./media/hide-application-from-user-portal/apps4.png)

## <a name="next-steps"></a>További lépések
* [Tekintse meg az összes saját csoportok](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](assign-user-or-group-access-portal.md)
* [Egy felhasználó vagy csoport-hozzárendelés eltávolítása a vállalati alkalmazás](remove-user-or-group-access-portal.md)
* [A name vagy a vállalati alkalmazás emblémájának módosítása](change-name-or-logo-portal.md)

