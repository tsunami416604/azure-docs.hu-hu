---
title: Az alkalmazás a felhasználói élmény az Azure Active Directoryban elrejtése |} Microsoft Docs
description: Az alkalmazás a felhasználói élmény Azure Active Directory hozzáférési panelek vagy Office 365 kilövők elrejtése módjáról.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2018
ms.author: markvi
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 7da8c60feed7cbb630e5b48653c657cc8fed9e99
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="hide-an-application-from-users-experience-in-azure-active-directory"></a>Az alkalmazás a felhasználói élmény az Azure Active Directoryban elrejtése

Ha olyan alkalmazás, amely nem kívánja megjeleníteni a felhasználói hozzáférés panelek vagy Office 365 kilövők, lehetőség áll rendelkezésre az alkalmazás csempéjére elrejtéséhez.  A következő két lehetőség áll rendelkezésre felhasználó alkalmazás kilövők alkalmazások elrejtése.

- A külső alkalmazás a felhasználók hozzáférési panelek és Office 365 app kilövők elrejtése
- Minden Office 365-alkalmazások a felhasználók hozzáférési panel elrejtése

Elrejti a az alkalmazás felhasználók továbbra is engedélye arra, hogy az alkalmazás, de nem látják őket jelennek meg az alkalmazás kilövők. A vállalati alkalmazások kezelésére a megfelelő engedélyekkel kell rendelkeznie, és a címtár globális rendszergazdának kell lennie.


## <a name="hiding-an-application-from-users-end-user-experiences"></a>Az alkalmazás a felhasználó végfelhasználói élmény elrejtése
A helyzettől függően az alábbi lépések segítségével alkalmazások a hozzáférési panel elrejtése.

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>Hogyan elrejtése a felhasználó hozzáférési panel és Office 365 app kilövők külső alkalmazásából?
Az alábbi lépések segítségével az alkalmazás a felhasználó hozzáférési panel és Office 365 app kilövők elrejtése.

1.  Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2.  Válassza ki **minden szolgáltatás**, adja meg **Azure Active Directory** a szövegmezőbe, majd válassza ki azt a **Enter**.
3.  Az a **Azure Active Directory - *directoryname***  képernyő (Ez azt jelenti, hogy az Azure ad Szolgáltatáshoz képernyőn a kezelt könyvtár), válasszon **vállalati alkalmazások**.
![Vállalati alkalmazások](media/active-directory-coreapps-hide-third-party-app/app1.png)
4.  Az a **vállalati alkalmazások** képernyőn válassza ki **összes alkalmazás**. Kezelheti az alkalmazások listájának megtekintéséhez.
5.  Az a **vállalati alkalmazások – összes alkalmazás** képernyőn, válasszon ki egy alkalmazást.</br>
![Vállalati alkalmazások](media/active-directory-coreapps-hide-third-party-app/app2.png)
6.  Az a ***appname*** (Ez azt jelenti, hogy a képernyő nevű, a kijelölt alkalmazást a címben) képernyőn válassza ki a tulajdonságokat.
7.  Az a  ***appname* -tulajdonságok** képernyőn válassza ki **Igen** a **látható a felhasználók számára?**.
![Vállalati alkalmazások](media/active-directory-coreapps-hide-third-party-app/app3.png)
8.  Válassza ki a **mentése** parancsot.

### <a name="how-do-i-hide-office-365-applications-from-users-access-panel"></a>Hogyan elrejtése a felhasználó hozzáférési panel az Office 365-alkalmazásokhoz?

A következő lépésekkel minden Office 365-alkalmazást a hozzáférési panelen igényelheti elrejtése. Ezek az alkalmazások továbbra is formájában lesz látható az Office 365 portálra.

1.  Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2.  Válassza ki **minden szolgáltatás**, adja meg **Azure Active Directory** a szövegmezőbe, majd válassza ki azt a **Enter**.
3.  Az a **Azure Active Directory - *directoryname***  képernyő (Ez azt jelenti, hogy az Azure ad Szolgáltatáshoz képernyőn a kezelt könyvtár), válasszon **felhasználói beállítások**.
4.  Az a **felhasználói beállítások** képernyőn, a **vállalati alkalmazások** kiválasztása **Igen** a **csak értesülhet az Office 365 portálOffice365-alkalmazások**.

![Vállalati alkalmazások](media/active-directory-coreapps-hide-third-party-app/apps4.png)

## <a name="next-steps"></a>További lépések
* [Összes csoport megtekintéshez](active-directory-groups-view-azure-portal.md)
* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](active-directory-coreapps-assign-user-azure-portal.md)
* [Egy felhasználó vagy csoport-hozzárendelés eltávolítása a vállalati alkalmazások](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Módosítja a nevét, vagy egy vállalati alkalmazás embléma](active-directory-coreapps-change-app-logo-user-azure-portal.md)

