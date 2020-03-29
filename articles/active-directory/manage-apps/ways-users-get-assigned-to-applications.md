---
title: Felhasználók hozzárendelése alkalmazásokhoz | Microsoft dokumentumok
description: Ismerje meg, hogyan kapják meg a felhasználók a bérlőben lévő alkalmazásokhoz való hozzárendelést
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b818fe1d8b6bbc9d2d8c5b460b4d71dccdd39366
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825980"
---
# <a name="how-to-assign-users-to-applications"></a>Felhasználók hozzárendelése alkalmazásokhoz

Ez a cikk segít megérteni, hogyan felhasználók kap egy alkalmazás a bérlőben.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Hogyan kaphatfelhasználók egy alkalmazáshoz az Azure AD-ben?

Ahhoz, hogy egy felhasználó hozzáférjen egy alkalmazáshoz, először hozzá kell rendelni őket valamilyen módon. A hozzárendelést elvégezheti a rendszergazda, az üzleti meghatalmazott vagy néha maga a felhasználó is. Az alábbiakban bemutatjuk, hogy a felhasználók hogyan rendelhetők hozzá az alkalmazásokhoz:

1.  A rendszergazda közvetlenül az alkalmazáshoz [rendel egy felhasználót](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

2.  A rendszergazda [hozzárendel egy csoportot,](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) amelynek a felhasználó tagja az alkalmazáshoz, beleértve a következőket:

    * A helyszíni szinkronizálásra kijelölt csoport

    * A felhőben létrehozott statikus biztonsági csoport

    * A felhőben létrehozott [dinamikus biztonsági csoport](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)

    * A felhőben létrehozott Office 365-csoport

    * A [Minden felhasználó](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups) csoport

3.  A rendszergazda lehetővé teszi, hogy [az Önkiszolgáló alkalmazás-hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) lehetővé tegye a felhasználók számára, hogy az [Alkalmazás-hozzáférési panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) alkalmazás hozzáadása **szolgáltatásával** **üzleti jóváhagyás nélkül** vegyenek fel egy alkalmazást

4.  A rendszergazda lehetővé teszi [az Önkiszolgáló alkalmazáshozzáférés engedélyezését,](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) hogy a felhasználó hozzáadjon egy alkalmazást az [Alkalmazás-hozzáférési panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **alkalmazás hozzáadása szolgáltatásával,** de csak a kiválasztott**üzleti jóváhagyók korábbi jóváhagyásához**

5.  A rendszergazda lehetővé teszi, hogy [az Önkiszolgáló csoportkezelés](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) lehetővé tegye a felhasználó számára, hogy **üzleti jóváhagyás nélkül** csatlakozzon egy olyan csoporthoz, amelyhez egy alkalmazás hozzá van rendelve

6.  A rendszergazda lehetővé teszi [az Önkiszolgáló csoportkezelés](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) számára, hogy a felhasználó csatlakozhasson egy olyan csoporthoz, amelyhez egy alkalmazás hozzá van rendelve, de csak **a kiválasztott üzleti jóváhagyók csoport előzetes jóváhagyásával**

7.  A rendszergazda közvetlenül egy felhasználóhoz rendel licencet egy külső alkalmazáshoz, például a [Microsoft Office 365-höz](https://products.office.com/)

8.  A rendszergazda licencet rendel egy olyan csoporthoz, amelynek a felhasználó tagja egy külső alkalmazáshoz, például a [Microsoft Office 365-höz](https://products.office.com/)

9.  A [rendszergazda beleegyezik abba, hogy egy alkalmazást](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) minden felhasználó használja, majd egy felhasználó bejelentkezik az alkalmazásba

10. A felhasználó saját maga [is hozzájárul az alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) az alkalmazásba való bejelentkezéssel

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése az Azure Active Directoryval](what-is-application-management.md)
