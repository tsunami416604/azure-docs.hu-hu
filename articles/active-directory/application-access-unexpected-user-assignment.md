---
title: Felhasználók hozzárendelése alkalmazásokhoz hogyan |} Microsoft Docs
description: Hogyan legyenek hozzárendelve felhasználók egy alkalmazás az Ön bérelt szolgáltatásának ismertetése
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 0c6755bc8990c75c696c6df7e95132f2dd97d313
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335162"
---
# <a name="how-to-assign-users-to-applications"></a>Felhasználók hozzárendelése alkalmazásokhoz

Ez a cikk segítenek megérteni, hogyan legyenek hozzárendelve felhasználók egy alkalmazás az Ön bérelt szolgáltatásának.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Hogyan tegye felhasználók legyenek hozzárendelve egy alkalmazás az Azure AD?

A felhasználó csatlakozni egy alkalmazáshoz akkor először kell rendelni az valamilyen módon. Hozzárendelés végzi el a rendszergazda, egy üzleti delegált, vagy bizonyos esetekben a felhasználó saját magukat. Alább ismerteti a felhasználók is legyenek hozzárendelve alkalmazásokhoz:

1.  A rendszergazda [rendel egy felhasználói](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) közvetlenül az alkalmazáshoz

2.  A rendszergazda [hozzárendel egy csoport](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) , hogy a felhasználó tagja az alkalmazásnak, beleértve:

  * Egy csoport, szinkronizált helyszíni

  * A létrehozott felhőalapú statikus biztonsági csoport

  * A [dinamikus biztonsági csoport](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) a felhő létrehozása

  * Egy Office 365-csoport létrehozása a felhőben

  * A [minden felhasználó](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups) csoport

3.  Lehetővé teszi a rendszergazda [önkiszolgáló alkalmazás-hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) a felhasználókat egy alkalmazást, amely engedélyezi a [alkalmazás hozzáférési Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **alkalmazás hozzáadása** szolgáltatás **üzleti jóváhagyása nélkül**

4.  Lehetővé teszi a rendszergazda [önkiszolgáló alkalmazás-hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) a felhasználókat egy alkalmazást, amely engedélyezi a [alkalmazás hozzáférési Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **alkalmazás hozzáadása** szolgáltatást, de csak w**edik előzetes jóváhagyási vállalati jóváhagyó egy kijelölt készletből**

5.  Lehetővé teszi a rendszergazda [önkiszolgáló csoportkezelési](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) csatlakozás a csoporthoz, amely az alkalmazás hozzá van rendelve egy felhasználó **üzleti jóváhagyása nélkül**

6.  Lehetővé teszi a rendszergazda [önkiszolgáló csoportkezelési](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) csatlakozás a csoporthoz, amely az alkalmazás hozzá van rendelve, a, de csak egy felhasználó **vállalati jóváhagyó egy kijelölt készletből előzetes jóváhagyással**

7.  A rendszergazda rendel hozzá licencet a felhasználó közvetlenül az első gyártótól származó alkalmazás, például [Microsoft Office 365](http://products.office.com/)

8.  A rendszergazda rendel hozzá licencet egy csoportot, hogy a felhasználó tagja első fél alkalmazás, például [Microsoft Office 365](http://products.office.com/)

9.  Egy [alkalmazás járul hozzá a rendszergazda](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) minden felhasználó és a felhasználó által használandó jelentkezik be az alkalmazás

10. A felhasználó [járul hozzá az alkalmazás](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) magukat az alkalmazásnak a bejelentkezéssel

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése az Azure Active Directoryval](manage-apps/what-is-application-management.md)
