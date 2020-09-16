---
title: A felhasználók Azure Active Directory alkalmazásokhoz való hozzárendelésének ismertetése
description: Ismerje meg, hogy a felhasználók hogyan vannak hozzárendelve egy olyan alkalmazáshoz, amely Azure Active Directoryt használ az Identitáskezelés kezeléséhez.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 411e9a6e059075dfc2928ed09c0c604d600fd9be
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604155"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>A felhasználók Azure Active Directory alkalmazásokhoz való hozzárendelésének ismertetése
Ebből a cikkből megtudhatja, hogyan rendelhető hozzá a felhasználók egy alkalmazáshoz a bérlőben.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Hogyan rendelhetők hozzá a felhasználók az Azure AD-alkalmazásokhoz?
Ahhoz, hogy egy felhasználó hozzáférjen egy alkalmazáshoz, előbb hozzá kell rendelnie azt valamilyen módon. A hozzárendelést egy rendszergazda, egy üzleti meghatalmazott vagy esetenként a felhasználó hajthatja végre. Az alábbiakban a felhasználók az alkalmazásokhoz való hozzárendelésének módjait ismertetjük:

*  A rendszergazda közvetlenül [rendel hozzá egy felhasználót](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) az alkalmazáshoz
*  A rendszergazda [olyan csoportot rendel hozzá](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) , amelynek a felhasználó tagja az alkalmazásnak, beleértve a következőket:
    * A helyszíni környezetből szinkronizált csoport
    * A felhőben létrehozott statikus biztonsági csoport
    * A felhőben létrehozott [dinamikus biztonsági csoport](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)
    * A felhőben létrehozott Microsoft 365 csoport
    * A [minden felhasználó](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups) csoport
*  A rendszergazda lehetővé teszi az [önkiszolgáló alkalmazásokhoz való hozzáférést](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) , lehetővé téve a felhasználók számára, hogy a [saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **hozzáadása** funkció használatával **üzleti jóváhagyás nélkül** vegyenek fel alkalmazást
*  A rendszergazda lehetővé teszi az [önkiszolgáló alkalmazásokhoz való hozzáférést](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) , lehetővé téve a felhasználók számára, hogy alkalmazásokat vegyenek fel az [alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **hozzáadása** funkció használatával, de csak a **kiválasztott üzleti jóváhagyók előzetes jóváhagyásával**
*  A rendszergazda lehetővé teszi az [önkiszolgáló csoportok felügyeletét](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) , hogy a felhasználók az **üzleti jóváhagyás nélkül** legyenek hozzárendelve egy olyan csoporthoz, amelyhez az alkalmazás hozzá van rendelve.
*  A rendszergazda lehetővé teszi az [önkiszolgáló csoportok felügyeletét](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) , hogy egy felhasználó olyan csoporthoz csatlakozzon, amelyhez az alkalmazás hozzá van rendelve, de csak a **kiválasztott üzleti jóváhagyók előzetes jóváhagyásával**
*  A rendszergazda közvetlenül az első féltől származó alkalmazáshoz rendel licencet a felhasználóhoz, például [Microsoft 365](https://products.office.com/)
*  A rendszergazdák egy licencet rendelnek egy olyan csoporthoz, amelyhez a felhasználó egy első féltől származó alkalmazás tagja, például [Microsoft 365](https://products.office.com/)
*  Egy [rendszergazda beleegyezett egy alkalmazásba](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) , hogy az összes felhasználó használja, majd a felhasználó bejelentkezik az alkalmazásba
* A felhasználó az alkalmazásba való bejelentkezéssel saját maga [is beleegyezik az alkalmazásba](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)

## <a name="next-steps"></a>További lépések
* [Gyorsindítás sorozat az alkalmazás-kezelésben](view-applications-portal.md)
* [Mi az alkalmazáskezelés?](what-is-application-management.md)
* [Mi az az egyszeri bejelentkezés?](what-is-single-sign-on.md)
