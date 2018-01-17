---
title: "Licenc nélküli használati jelentés |} Microsoft Docs"
description: "A nem licencelt használati jelentés segítségével azonosíthatja a licenc nélküli felhasználók által használt fizetős Azure AD-funkciókat."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 92138f43-9528-4c8a-b834-66a47da476e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.openlocfilehash: 8bcd814ee7b7bfc158e62ad26e6cc23781f5352d
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
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

## <a name="see-also"></a>Lásd még
* [Feltételes hozzáférés az Office 365 és az egyéb Azure Active Directory használatával csatlakozó alkalmazások](active-directory-conditional-access-azure-portal.md)
* [Az Azure AD feltételes hozzáférés – első lépések](active-directory-conditional-access-azure-portal-get-started.md) 

