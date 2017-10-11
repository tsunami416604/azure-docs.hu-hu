---
title: "Licenc nélküli használati jelentés |} Microsoft Docs"
description: "A nem licencelt használati jelentés segítségével azonosíthatja a licenc nélküli felhasználók által használt fizetős Azure AD-funkciókat."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 92138f43-9528-4c8a-b834-66a47da476e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: markvi
ms.openlocfilehash: c0b4f455f067e825362bdecc02ea62d7984f0d31
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="unlicensed-usage-report"></a>Licenc nélküli használati jelentés
A nem licencelt használati jelentés segítségével azonosíthatja a licenc nélküli felhasználók által használt fizetős Azure AD-funkciókat. Ez lehetővé teszi, hogy élvezetesebbé tegyük használja a megvásárolt licencek és azonosításához tudja, amikor szükség lehet további licenceket. 

Ebben a jelentésben aktív használati fizetős funkciója az elmúlt 30 napban. 

## <a name="report-structure"></a>A jelentés struktúra
| Oszlop neve | Leírás |
|:--- |:--- |
| Licenc nélküli felhasználók |A felhasználó neve |
| Szolgáltatás |A szolgáltatás neve. Például: a feltételes hozzáférés |
| Alkalmazás érhető el |Az alkalmazást, amely használatban van a szolgáltatás neve. Például: Office 365 SharePoint online-hoz |

> [!NOTE]
> Ha egy felhasználói fiók törölve lett a "Licenc nélküli felhasználók" oszlop tölti fel az ID, például a 1003000090D8B285
> 
> 

## <a name="conditional-access-feature"></a>Feltételes hozzáférési funkciónak
Licenc nélküli felhasználók lesz megjelölve, amikor hozzáférni a feltételes hozzáférési szabályzatot, akkor használható, ha nem rendelkeznek egy Azure AD Premium-licenc egy szolgáltatást. 

Ez vonatkozik az MFA / hely házirendeket, valamint az eszköz házirendek, amelyek az Intune-nal.

## <a name="see-also"></a>Lásd még:
* [Feltételes hozzáférés az Office 365 és az egyéb Azure Active Directory használatával csatlakozó alkalmazások](active-directory-conditional-access.md)
* [Az Azure AD feltételes hozzáférés – első lépések](active-directory-conditional-access-azuread-connected-apps.md) 

