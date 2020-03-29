---
title: Hibrid identitástervezés – tartalomkezelési követelmények Azure | Microsoft dokumentumok
description: Betekintést nyújt a vállalkozás tartalomkezelési követelményeinek meghatározásába. Általában, ha a felhasználó saját eszközzel rendelkezik, előfordulhat, hogy több hitelesítő adatokat is, amelyek váltakozó szerint az alkalmazást, hogy az általuk használt. Fontos megkülönböztetni, hogy milyen tartalmat hoztak létre a személyes hitelesítő adatokkal szemben a vállalati hitelesítő adatokkal létrehozottakkal szemben. Az identitáskezelési megoldásnak képesnek kell lennie a felhőszolgáltatásokkal való interakcióra, hogy zökkenőmentes élményt nyújtson a végfelhasználónak, miközben biztosítja az adatvédelmüket és növeli az adatszivárgás elleni védelmet.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d970fd133f8c43319e7f1fdb6b3a50c3c05f687
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "64918441"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>A hibrid identitáskezelési megoldás tartalomkezelési követelményeinek meghatározása
A vállalat tartalomkezelési követelményeinek ismertetése közvetlenül befolyásolhatja a hibrid identitásmegoldás használatára vonatkozó döntést. A több eszköz elterjedése és a felhasználók azon képessége, hogy saját eszközeiket hozzák ([BYOD](https://aka.ms/byodcg)), a vállalatnak meg kell védenie saját adatait, de a felhasználók adatvédelmét is érintetlenül kell tartania. Általában, ha a felhasználó saját eszközzel rendelkezik, előfordulhat, hogy több hitelesítő adatokat is, amelyek váltakozó szerint az alkalmazást, hogy az általuk használt. Fontos megkülönböztetni, hogy milyen tartalmat hoztak létre a személyes hitelesítő adatokkal szemben a vállalati hitelesítő adatokkal létrehozottakkal szemben. Az identitáskezelési megoldásnak képesnek kell lennie a felhőszolgáltatásokkal való interakcióra, hogy zökkenőmentes élményt nyújtson a végfelhasználónak, miközben biztosítja az adatvédelmüket és növeli az adatszivárgás elleni védelmet. 

Az Ön személyazonossági megoldását különböző technikai vezérlők használják ki annak érdekében, hogy tartalomkezelést biztosítson az alábbi ábra szerint:

![biztonsági ellenőrzések](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Biztonsági ellenőrzések, amelyek kihasználják az ön identitáskezelő rendszerét**

Általánosságban elmondható, hogy a tartalomkezelési követelmények a következő területeken használják ki az Ön identitáskezelési rendszerét:

* Adatvédelem: az erőforrást birtokló felhasználó azonosítása és a megfelelő vezérlők alkalmazása az integritás fenntartása érdekében.
* Adatbesorolás: azonosítsa a felhasználót vagy csoportot, valamint az objektumhoz való hozzáférés szintjét a besorolása szerint. 
* Adatszivárgás elleni védelem: a szivárgás elkerülése érdekében az adatok védelméért felelős biztonsági ellenőrzéseknek a felhasználó személyazonosságának ellenőrzése érdekében kapcsolatba kell lépniük az identitásrendszerrel. Ez a nyomvonal céljának naplózása szempontjából is fontos.

> [!NOTE]
> Olvassa el [az adatok besorolását a felhőre való felkészültség érdekében,](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) ha további információkat szeretne tudni az ajánlott eljárásokról és az adatbesorolásirányelveiről.
> 
> 

A hibrid identitáskezelési megoldás tervezésekor győződjön meg arról, hogy a szervezet követelményeinek megfelelően válaszol a következő kérdésekre:

* Rendelkezik a vállalat biztonsági ellenőrzéssel az adatvédelem érvényesítéséhez?
  * Ha igen, a biztonsági vezérlők képesek lesznek integrálni a hibrid identitáskezelési megoldás, hogy ön fog elfogadni?
* A vállalat használ adatbesorolást?
  * Ha igen, a jelenlegi megoldás képes integrálni a hibrid identitásmegoldás, hogy fogsz elfogadni?
* Van vállalata jelenleg bármilyen megoldással az adatszivárgásra? 
  * Ha igen, a jelenlegi megoldás képes integrálni a hibrid identitásmegoldás, hogy fogsz elfogadni?
* A vállalatnak naplóznia kell az erőforrásokhoz való hozzáférést?
  * Ha igen, milyen típusú erőforrásokat?
  * Ha igen, milyen szintű információra van szükség?
  * Ha igen, hol kell a naplónak lennie? A helyszínen vagy a felhőben?
* A vállalatnak titkosítania kell a bizalmas adatokat tartalmazó e-maileket (SSN-ek, hitelkártyaszámok stb.)?
* A vállalatnak titkosítania kell a külső üzleti partnerekkel megosztott összes dokumentumot/tartalmat?
* A vállalatnak be kell tartatnia a vállalati irányelveket bizonyos típusú e-mailekre (nem válaszol mindenkinek, nem továbbítja)?

> [!NOTE]
> Minden válaszról készítsen feljegyzéseket, és ismerje meg a válaszok indokait. [Az Adatvédelmi stratégia meghatározása](plan-hybrid-identity-design-considerations-data-protection-strategy.md) az egyes lehetőségek rendelkezésre álló lehetőségeit és előnyeit/hátrányait veszi át.  A kérdések megválaszolásával kiválaszthatja, hogy melyik lehetőség felel meg legjobban üzleti igényeinek.
> 
> 

## <a name="next-steps"></a>További lépések
[A hozzáférés-vezérlésre vonatkozó követelmények meghatározása](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Lásd még:
[Tervezési szempontok – áttekintés](plan-hybrid-identity-design-considerations-overview.md)

