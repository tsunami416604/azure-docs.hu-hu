---
title: Az Azure Active Directory B2B-együttműködés licencelése útmutató |} A Microsoft Docs
description: A fizetős Azure AD-licenceket az Azure Active Directory B2B együttműködés nem igényel, de Ön is is első díjkötelessé B2B vendégfelhasználó
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0032eb1e6b92abb130521945f64fda3133d9b7fc
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198881"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Útmutató az Azure Active Directory vállalatközi együttműködés licenceléséhez

Az Azure Active Directory (Azure AD) vállalatközi (B2B) együttműködés, meghívhatja a külső felhasználókat (vagy a "vendég felhasználók") használja a fizetős Azure AD-szolgáltatások. Az egyes fizetős Azure AD-licencre, amely rendel egy felhasználóhoz, meghívhatja a legfeljebb 5 vendégfelhasználót a külső felhasználó támogatás alapján.

B2B-vendégként felhasználói licencelés automatikusan kiszámítása és jelentett 1:5 arány alapján. Jelenleg nem lehet megadni a B2B vendégfelhasználókat közvetlenül a Vendég felhasználói licenceket.

Emellett a felhasználók használhatják a Vendég ingyenes további licencelési követelmény az Azure AD-funkciók. Vendég felhasználók férhetnek hozzá az Azure AD-funkciók ingyenes, még akkor is, ha nincsenek a fizetős Azure AD-licenceket. 

## <a name="examples-calculating-guest-user-licenses"></a>Példák: Kiszámítása a Vendég felhasználói licencek
Miután határozhatja meg, hány vendég felhasználók szeretne hozzáférni a fizetős Azure AD szolgáltatásokba, ellenőrizze, hogy elég fizetett Azure AD-licenc szükséges 1:5 arányú vendégfelhasználóknak. Néhány példa:

- Az Azure AD-alkalmazások vagy szolgáltatások 100 vendégfelhasználók meghívása szeretne, és a hozzáférés-kezelés és üzembe helyezés minden vendégfelhasználó hozzárendelni kívánt. Emellett szeretné 50 vendég felhasználók többtényezős hitelesítés és a feltételes hozzáférés szükséges. Ez a kombináció fedezésére 10 alapszintű Azure AD-licenceket, és 10 Azure AD Premium P1-licenc lesz szüksége. Ha a vendég felhasználók az Identity Protection funkcióinak használatát tervezi, szüksége lesz egy-egy 1:5 vendégfelhasználóknak az Azure AD Premium P2 licenccel.
- Azt szeretné, 60 vendégfelhasználók, akik többtényezős hitelesítés, ezért rendelkeznie kell legalább 12 Azure AD Premium P1-licenceket. Az Azure AD Premium P1-licenc, amely lehetővé teszi a licencelési arány 1:5 alatt legfeljebb 50 vendégfelhasználók 10 alkalmazottal rendelkezik. 10 további vendégfelhasználóknak két további prémium P1 szintű licencek vásárlása kell.

## <a name="next-steps"></a>További lépések

Az alábbi forrásanyagokban talál az Azure AD B2B együttműködés:

* [Az Azure Active Directory díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)
* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [Azure Active Directory vállalatközi együttműködés – gyakori kérdések](faq.md)
