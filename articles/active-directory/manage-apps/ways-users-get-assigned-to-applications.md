---
title: Felhasználók hozzárendelése az alkalmazások hogyan |} A Microsoft Docs
description: Megismerheti, hogyan lesznek hozzárendelve felhasználók egy alkalmazáshoz, a bérlőben
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
ms.openlocfilehash: 2301c2eee2853617251053713b50f9915962027b
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356643"
---
# <a name="how-to-assign-users-to-applications"></a>Felhasználók hozzárendelése alkalmazások

Ennek a cikknek a segítségével megismerheti, hogyan lesznek hozzárendelve felhasználók egy alkalmazáshoz, a bérlőben.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Hogyan tegye felhasználók lesznek hozzárendelve az alkalmazások az Azure AD?

Egy felhasználó egy alkalmazáshoz akkor először kell rendelni, valamilyen módon. Hozzárendelés végezhet el egy rendszergazda, egy üzleti delegált vagy más néven, a felhasználó saját magukat. Alább leírja, hogyan is lesznek hozzárendelve felhasználók alkalmazásokat:

1.  A rendszergazda [rendel egy felhasználói](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) közvetlenül az alkalmazáshoz

2.  A rendszergazda [hozzárendeli egy csoporthoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) , hogy a felhasználó tagja, az alkalmazáshoz, többek között:

  * Egy csoportot, amely a rendszer szinkronizálta a helyszíni

  * A felhőben létrehozott statikus biztonsági csoport

  * A [dinamikus biztonsági csoport](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) a felhőben létrehozott

  * A felhőben létrehozott Office 365-csoportot

  * A [minden felhasználó](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups) csoport

3.  Lehetővé teszi a rendszergazda [önkiszolgáló alkalmazás-hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) , hogy a felhasználó egy alkalmazást a hozzáadása a [alkalmazás-hozzáférési Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **alkalmazás hozzáadása** funkció **üzleti jóváhagyása nélkül**

4.  Lehetővé teszi a rendszergazda [önkiszolgáló alkalmazás-hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) , hogy a felhasználó egy alkalmazást a hozzáadása a [alkalmazás-hozzáférési Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **alkalmazás hozzáadása** funkciót, de csak w**edik előzetes jóváhagyásra kijelölt munkahelyi jóváhagyónak közül**

5.  Lehetővé teszi a rendszergazda [önkiszolgáló csoportkezelés](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) , hogy a felhasználó, hogy egy alkalmazás hozzá van rendelve egy csoporthoz való csatlakoztatáshoz **üzleti jóváhagyása nélkül**

6.  Lehetővé teszi a rendszergazda [önkiszolgáló csoportkezelés](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) , hogy a felhasználó, hogy egy alkalmazás hozzá van rendelve, de csak egy csoporthoz való csatlakoztatáshoz **közül, kijelölt munkahelyi jóváhagyónak előzetes jóváhagyással**

7.  A rendszergazda rendel a licencet a felhasználó közvetlenül az első gyártótól származó alkalmazás, például [a Microsoft Office 365-höz](http://products.office.com/)

8.  A rendszergazda rendel egy licencet egy csoportot, hogy a felhasználó tagja első gyártótól származó alkalmazás, például [a Microsoft Office 365-höz](http://products.office.com/)

9.  Egy [rendszergazda jóváhagy egy alkalmazást](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) minden felhasználó és a felhasználó által használandó jelentkezik be az alkalmazáshoz

10. A felhasználó [jóváhagy egy alkalmazást](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) magukat, jelentkezzen be az alkalmazás

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése az Azure Active Directoryval](what-is-application-management.md)
