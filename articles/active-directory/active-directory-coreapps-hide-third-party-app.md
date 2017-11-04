---
title: "A külső alkalmazás Azure Active Directoryban lévő felhasználói élmény elrejtése |} Microsoft Docs"
description: "A külső alkalmazás Azure Active Directoryban lévő felhasználói élmény elrejtése"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
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
ms.openlocfilehash: 976cbb1341493186b9996d250ebca8f2f3688fdf
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="hide-a-third-party-application-from-users-experience-in-azure-active-directory"></a>A külső alkalmazás Azure Active Directoryban lévő felhasználói élmény elrejtése

Ha egy külső alkalmazás (egy mások, mint a Microsoft által közzétett alkalmazás), hogy nem szeretné, hogy megjeleníti őket a felhasználók hozzáférést panelek vagy Office 365 kilövők, egy lehetőség, hogy az alkalmazás csempéjére elrejtése van. Elrejti a az alkalmazás felhasználók továbbra is engedélye arra, hogy az alkalmazás, de nem látják őket jelennek meg az alkalmazás kilövők. A vállalati alkalmazások kezelésére a megfelelő engedélyekkel kell rendelkeznie, és a címtár globális rendszergazdának kell lennie.

## <a name="hiding-a-third-party-app-from-a-users-experience"></a>A külső alkalmazás a felhasználói élmény a elrejtése
Az alábbi lépések segítségével egy külső gyártótól származó alkalmazást a felhasználó hozzáférési panelre, és Office 365 app kilövők elrejtése

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
