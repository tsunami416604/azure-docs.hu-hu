---
title: Az Azure Active Directory B2B-együttműködés licencelése útmutató |} A Microsoft Docs
description: A fizetős Azure AD-licenceket az Azure Active Directory B2B együttműködés nem igényel, de Ön is is első díjkötelessé B2B vendégfelhasználó
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: mal
ms.openlocfilehash: d80794511f334cd6dc5af418e24fc774b7d8728f
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867510"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Útmutató az Azure Active Directory vállalatközi együttműködés licenceléséhez

Az Azure Active Directory (Azure AD) vállalatközi (B2B) együttműködés, meghívhatja a külső felhasználókat (vagy a "vendég felhasználók") használja a fizetős Azure AD-szolgáltatások. Az egyes fizetős Azure AD-licencre, amely rendel egy felhasználóhoz, meghívhatja a legfeljebb 5 vendégfelhasználót a külső felhasználó támogatás alapján.

Vendégfelhasználó olyan személy, aki nem tagja a szervezet vagy a szervezet valamely leányvállalata valamelyikét. Vendégfelhasználók határozzák meg a szervezete számára, hogy a kapcsolat nem által a bejelentkezéshez használt hitelesítő adatokkal. Valójában a vendégfelhasználó jelentkezhetnek be vagy egy külső azonosító vagy a vállalat tulajdonában hitelesítő adatokkal.

Az alábbi *nem* vendég felhasználók:
- Az alkalmazottak, helyszíni alvállalkozók vagy megbízottai
- Alkalmazottai, alvállalkozói helyszíni vagy megbízottai, a Leányvállalataink

B2B-vendégként felhasználói licencelés automatikusan kiszámítása és jelentett 1:5 arány alapján. Jelenleg nem lehet megadni a B2B vendégfelhasználókat közvetlenül a Vendég felhasználói licenceket.

Bizonyos esetekben, ahol a vendégfelhasználó jelentett nem külső felhasználó 1:5 juttatás használatával. Ha a Vendég felhasználó már rendelkezik egy fizetős Azure AD-licencre a felhasználó saját szervezet, a felhasználó nem használja a B2B-vendégként felhasználói licencek egyikét. Emellett a felhasználók használhatják a Vendég ingyenes további licencelési követelmény az Azure AD-funkciók. Vendég felhasználók férhetnek hozzá az Azure AD-funkciók ingyenes, még akkor is, ha nincsenek a fizetős Azure AD-licenceket. 

## <a name="examples-calculating-guest-user-licenses"></a>Példák: A Vendég felhasználói licencek kiszámítása
Miután határozhatja meg, hány vendég felhasználók szeretne hozzáférni a fizetős Azure AD szolgáltatásokba, ellenőrizze, hogy elég fizetett Azure AD-licenc szükséges 1:5 arányú vendégfelhasználóknak. Néhány példa:

- Az Azure AD-alkalmazások vagy szolgáltatások 100 vendégfelhasználók meghívása szeretne, és a hozzáférés-kezelés és üzembe helyezés minden vendégfelhasználó hozzárendelni kívánt. Emellett szeretné 50 vendég felhasználók többtényezős hitelesítés és a feltételes hozzáférés szükséges. Ez a kombináció fedezésére 10 alapszintű Azure AD-licenceket, és 10 Azure AD Premium P1-licenc lesz szüksége. Ha a vendég felhasználók az Identity Protection funkcióinak használatát tervezi, szüksége lesz egy-egy 1:5 vendégfelhasználóknak az Azure AD Premium P2 licenccel.
- Azt szeretné, 60 vendégfelhasználók, akik többtényezős hitelesítés, ezért rendelkeznie kell legalább 12 Azure AD Premium P1-licenceket. Az Azure AD Premium P1-licenc, amely lehetővé teszi a licencelési arány 1:5 alatt legfeljebb 50 vendégfelhasználók 10 alkalmazottal rendelkezik. 10 további vendégfelhasználóknak két további prémium P1 szintű licencek vásárlása kell.

## <a name="using-the-b2b-collaboration-api-to-invite-users-from-your-affiliates"></a>A Társvállalatok felhasználókat meghívni a B2B-együttműködés API használatával

Definíció szerint B2B vendégfelhasználó egy külső felhasználót, hogy meghívja a használatára a fizetős Azure AD-alkalmazások és szolgáltatások. Egy alkalmazott, helyszíni nyertes vagy a vállalat vagy a Társvállalatok egyik helyszíni ügynök nem jogosultak a B2B-együttműködés, még akkor is, ha a B2B-funkciói használhatók. Néhány példa: 
- Szeretné meghívni egy felhasználót, aki a szervezet egy alkalmazott külső hitelesítő adatokat (például társadalombiztosítási azonosító) használatával. Ez a forgatókönyv nem felel meg a licencelési követelményeket, és nem engedélyezett. Külső hitelesítő adatok ne egy alkalmazott egy külső felhasználót.  
- A szervezet valamely leányvállalata egyik felhasználó meghívása a B2B-API-k használatával szeretné. Bár ebben a forgatókönyvben a B2B API-k segítségével a felhasználó meghívása, azt a B2B-együttműködés nem tekinthető. A licencelési követelményeket, nem kompatibilis, mert egy felhasználó a által nyújtott nem külső felhasználó. 

Mindkét forgatókönyv esetében, a jobb megoldás, hogy a felhasználókat tagokként meghívása a B2B API-val (invitedUserType tag =) és a egy Azure AD-licencet rendelhet hozzájuk. 

## <a name="next-steps"></a>További lépések

Az alábbi forrásanyagokban talál az Azure AD B2B együttműködés:

* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [Az Azure Active Directory B2B-együttműködés – gyakori kérdések (GYIK)](faq.md)
