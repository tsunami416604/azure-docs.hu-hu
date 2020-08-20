---
title: Felhasználók társítása alkalmazásokhoz | Microsoft Docs
description: Ismerje meg, hogy a felhasználók hogyan rendelhetők hozzá egy alkalmazáshoz a bérlőben
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 516bffa7057f8fee3b8e38d46f3b2da905880044
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88639936"
---
# <a name="how-to-assign-users-to-applications"></a>Felhasználók társítása alkalmazásokhoz

Ebből a cikkből megtudhatja, hogyan rendelhető hozzá a felhasználók egy alkalmazáshoz a bérlőben.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Hogyan rendelhetők hozzá a felhasználók az Azure AD-alkalmazásokhoz?

Ahhoz, hogy egy felhasználó hozzáférjen egy alkalmazáshoz, előbb hozzá kell rendelnie azt valamilyen módon. A hozzárendelést egy rendszergazda, egy üzleti meghatalmazott vagy esetenként a felhasználó hajthatja végre. Az alábbiakban a felhasználók az alkalmazásokhoz való hozzárendelésének módjait ismertetjük:

1.  A rendszergazda közvetlenül [rendel hozzá egy felhasználót](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) az alkalmazáshoz

2.  A rendszergazda [olyan csoportot rendel hozzá](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) , amelynek a felhasználó tagja az alkalmazásnak, beleértve a következőket:

    * A helyszíni környezetből szinkronizált csoport

    * A felhőben létrehozott statikus biztonsági csoport

    * A felhőben létrehozott [dinamikus biztonsági csoport](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)

    * A felhőben létrehozott Office 365-csoport

    * A [minden felhasználó](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups) csoport

3.  A rendszergazda lehetővé teszi az [önkiszolgáló alkalmazásokhoz való hozzáférést](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) , lehetővé téve a felhasználók számára, hogy a [saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **hozzáadása** funkció használatával **üzleti jóváhagyás nélkül** vegyenek fel alkalmazást

4.  A rendszergazda lehetővé teszi az [önkiszolgáló alkalmazásokhoz való hozzáférést](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) , lehetővé téve a felhasználók számára, hogy alkalmazásokat vegyenek fel az [alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **hozzáadása** funkció használatával, de csak**a kiválasztott üzleti jóváhagyók előzetes jóváhagyása**

5.  A rendszergazda lehetővé teszi az [önkiszolgáló csoportok felügyeletét](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) , hogy a felhasználók az **üzleti jóváhagyás nélkül** legyenek hozzárendelve egy olyan csoporthoz, amelyhez az alkalmazás hozzá van rendelve.

6.  A rendszergazda lehetővé teszi az [önkiszolgáló csoportok felügyeletét](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) , hogy egy felhasználó olyan csoporthoz csatlakozzon, amelyhez az alkalmazás hozzá van rendelve, de csak a **kiválasztott üzleti jóváhagyók előzetes jóváhagyásával**

7.  A rendszergazda közvetlenül az első féltől származó alkalmazáshoz rendel licencet a felhasználóhoz, például [Microsoft Office 365](https://products.office.com/)

8.  A rendszergazda olyan csoporthoz rendeli a licencet, amelyhez a felhasználó egy első féltől származó alkalmazás tagja, például [Microsoft Office 365](https://products.office.com/)

9.  Egy [rendszergazda beleegyezett egy alkalmazásba](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) , hogy az összes felhasználó használja, majd a felhasználó bejelentkezik az alkalmazásba

10. A felhasználó az alkalmazásba való bejelentkezéssel saját maga [is beleegyezik az alkalmazásba](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)

## <a name="next-steps"></a>Következő lépések
[Alkalmazások kezelése Azure Active Directory](what-is-application-management.md)
