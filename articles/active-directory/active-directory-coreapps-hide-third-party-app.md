---
title: "Az alkalmazás a felhasználói élmény az Azure Active Directoryban elrejtése |} Microsoft Docs"
description: "Az alkalmazás a felhasználói élmény az Azure Active Directoryban elrejtése"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: billmath
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 667fdd45bc9eb1f01ce3883006bb29274478cb83
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="hide-an-application-from-users-experience-in-azure-active-directory"></a>Az alkalmazás a felhasználói élmény az Azure Active Directoryban elrejtése

Ha nem kívánja megjeleníteni a felhasználói hozzáférés panelek vagy Office 365 kilövők alkalmazást, nincs egy lehetőség, hogy az alkalmazás csempéjére elrejtése. Ez a beállítás csak érhető el külső alkalmazások (nem a Microsoft által közzétett alkalmazások). Elrejti a az alkalmazás felhasználók továbbra is engedélye arra, hogy az alkalmazás, de nem látják őket jelennek meg az alkalmazás kilövők. A vállalati alkalmazások kezelésére a megfelelő engedélyekkel kell rendelkeznie, és a címtár globális rendszergazdának kell lennie. 

## <a name="hiding-an-application-from-users-end-user-experiences"></a>Az alkalmazás a felhasználó végfelhasználói élmény elrejtése
Az alábbi lépések segítségével az alkalmazás a felhasználó hozzáférési panel és Office 365 app kilövők elrejtése

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>Hogyan elrejtése a felhasználó hozzáférési panel és Office 365 app kilövők külső alkalmazásából?

1.  Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2.  Válassza ki **további szolgáltatások**, adja meg **Azure Active Directory** a szövegmezőbe, majd válassza ki azt a **Enter**.
3.  Az a **Azure Active Directory - *directoryname***  képernyő (Ez azt jelenti, hogy az Azure ad Szolgáltatáshoz képernyőn a kezelt könyvtár), válasszon **vállalati alkalmazások**.
![Vállalati alkalmazások](media/active-directory-coreapps-hide-third-party-app/app1.png)
4.  Az a **vállalati alkalmazások** képernyőn válassza ki **összes alkalmazás**. Kezelheti az alkalmazások listájának megtekintéséhez.
5.  Az a **vállalati alkalmazások – összes alkalmazás** képernyőn, válasszon ki egy alkalmazást.</br>
![Vállalati alkalmazások](media/active-directory-coreapps-hide-third-party-app/app2.png)
6.  Az a ***appname*** (Ez azt jelenti, hogy a képernyő nevű, a kijelölt alkalmazást a címben) képernyőn válassza ki a tulajdonságokat.
7.  Az a  ***appname* -tulajdonságok** képernyőn válassza ki **Igen** a **látható a felhasználók számára?**.
![Vállalati alkalmazások](media/active-directory-coreapps-hide-third-party-app/app3.png)
8.  Válassza ki a **mentése** parancsot.

## <a name="next-steps"></a>Következő lépések
* [Összes csoport megtekintéshez](active-directory-groups-view-azure-portal.md)
* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](active-directory-coreapps-assign-user-azure-portal.md)
* [Egy felhasználó vagy csoport-hozzárendelés eltávolítása a vállalati alkalmazások](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Módosítja a nevét, vagy egy vállalati alkalmazás embléma](active-directory-coreapps-change-app-logo-user-azure-portal.md)
