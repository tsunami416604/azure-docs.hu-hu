---
title: Csoportok hozzárendelése az Azure AD alkalmazásaiban |} Microsoft Docs
description: How Azure-alkalmazások csoport-hozzárendelés végrehajtásához.
services: active-directory
documentationcenter: ''
author: kgremban
manager: mtillman
editor: ''
ms.assetid: 29b5ba89-a1c7-4f1f-a294-248a40106617
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
robots: noindex
ms.openlocfilehash: 471099c58a7fc1dfd520e693873a76f64c0fbf0a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
ms.locfileid: "26597144"
---
# <a name="assign-azure-active-directory-groups-to-an-application"></a>Az alkalmazás Azure Active Directory-csoportok hozzárendelése
Mielőtt a felhasználók és csoportok rendelhet egy alkalmazás, felhasználó-hozzárendelés követelheti meg. Kötelező felhasználói kiosztása, lásd: a [igénylő felhasználó hozzárendelés](active-directory-applications-guiding-developers-requiring-user-assignment.md) cikk.

Ez a cikk feltételezi, hogy már létrehozott csoportok az active Directory, az alkalmazás használ.

## <a name="assigning-groups-to-an-application"></a>Csoportok hozzárendelése az alkalmazáshoz
1. Jelentkezzen be rendszergazdai fiókkal az Azure portálon.
2. Kattintson a **összes elemet** a főmenü elemére.
3. Adja meg az alkalmazás használja.
4. Kattintson a **alkalmazások** fülre.
5. Az ebben a könyvtárban társított alkalmazások listájából válassza ki az alkalmazást.
6. Kattintson a **felhasználók és csoportok** fülre.
7. Az active Directoryban a csoportlista szűréséhez használatával a **csoportok** legördülő listából.
8. Válassza ki azt a csoportot.
9. Kattintson a **hozzárendelése**.
10. Kattintson a **Igen** megjelenésekor.

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]
