---
title: "Tiltsa le az Azure Active Directory tartományi szolgáltatások |} Microsoft Docs"
description: "Tiltsa le az Azure Active Directory tartományi szolgáltatások az Azure portál használatával"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: maheshu
ms.openlocfilehash: f61f6df85e47bec32e147990d956a4409429a60c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Tiltsa le az Azure Active Directory tartományi szolgáltatások az Azure portál használatával
Ez a cikk bemutatja, hogyan tiltsa le az Azure Active Directory (AD) tartományi szolgáltatásokban az Azure AD-címtár az Azure-portál használatával.

> [!WARNING]
> **Törlése végleges, és nem vonható vissza.**
> Folytassa a figyelmeztetés! Ha törli a felügyelt tartományra:
  * A felügyelt tartomány számára tartományvezérlők rendszer leépíti és a virtuális hálózat eltávolítva.
  * A felügyelt tartományra lévő adatok véglegesen törlődik. Ez magában foglalja az egyéni szervezeti egységek, csoportházirend-objektumok, egyéni DNS-rekordokat, szolgáltatásnevekről, a felügyelt tartományra létrehozott csoportosan felügyelt szolgáltatásfiókokat stb.
  * A felügyelt tartományhoz csatlakozó gépek elveszíti a megbízhatósági kapcsolatban a tartományhoz, és a tartományhoz nem csatlakozó kell.
  * Az érintett számítógépekre AD vállalati hitelesítő adatokkal nem tud bejelentkezni. Ehelyett használjon a helyi rendszergazdai hitelesítő adatokat a géphez.
A felügyelt tartomány törlése nem törli az Azure AD-címtár vagy ellenkező esetben a directory kedvezőtlen hatással.
>

Hajtsa végre az alábbi lépéseket az Azure AD tartományi szolgáltatások által kezelt tartomány törlése:
1. Keresse meg a [Azure AD tartományi szolgáltatások kiterjesztése](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) az Azure portálon.
2. Kattintson a felügyelt tartomány nevét.

    ![Válassza ki a tartomány törlése](./media/getting-started/domain-services-delete-select-domain.png)

3. Az a **áttekintése** lapján kattintson a **törlése** gombra.

    ![Tartomány törlése](./media/getting-started/domain-services-delete-domain.png)

4. A törlés megerősítéséhez írja be a felügyelt tartomány DNS-tartomány nevét. Kattintson a **törlése** gombra kattint, amikor elkészült.

    ![Tartomány megerősítés törlése](./media/getting-started/domain-services-delete-domain-confirm.png)

A felügyelt tartományra körülbelül 15 – 20 perc törlődik.

Érdemes lehet [visszajelzés megosztása](active-directory-ds-contact-us.md) és segítsen megérteni a szolgáltatások segítene úgy döntött, hogy Azure AD tartományi szolgáltatásokat a jövőben. Ezzel a visszajelzéssel segít nekünk a szolgáltatást, hogy jobban megfeleljenek az igények és a használati esetek fejleszteni.
