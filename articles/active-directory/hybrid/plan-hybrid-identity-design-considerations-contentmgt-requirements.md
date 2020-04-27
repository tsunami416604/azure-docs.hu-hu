---
title: Hybrid Identity design – tartalomkezelési követelmények az Azure-ban | Microsoft Docs
description: Betekintést nyújt a vállalat tartalomkezelési követelményeinek meghatározására. Általában, ha egy felhasználó saját eszközével rendelkezik, több hitelesítő adat is tartozhat, amely az általuk használt alkalmazásnak megfelelően váltakozik majd. Fontos megkülönböztetni, hogy milyen tartalmak lettek létrehozva a személyes hitelesítő adatokkal, illetve a vállalati hitelesítő adatok használatával létrehozott tartalommal. A személyazonossági megoldásnak képesnek kell lennie a felhőalapú szolgáltatásokkal való interakcióra, hogy zökkenőmentes felhasználói élményt nyújtson a végfelhasználók számára, miközben gondoskodik az adatok védelméről, és növelheti az adatszivárgás elleni védelmet.
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
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "64918441"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>A Hybrid Identity megoldás tartalomkezelési követelményeinek meghatározása
A vállalat tartalomkezelési követelményeinek megismerése közvetlenül befolyásolhatja a hibrid identitási megoldás használatára vonatkozó döntését. Több eszköz elterjedése és a felhasználók képességeinek a saját eszközei ([BYOD](https://aka.ms/byodcg)) révén a vállalatnak meg kell őriznie a saját adatait, de a felhasználónak is érintetlenül kell tartania a felhasználói adatok védelmét. Általában, ha egy felhasználó saját eszközével rendelkezik, több hitelesítő adat is tartozhat, amely az általuk használt alkalmazásnak megfelelően váltakozik majd. Fontos megkülönböztetni, hogy milyen tartalmak lettek létrehozva a személyes hitelesítő adatokkal, illetve a vállalati hitelesítő adatok használatával létrehozott tartalommal. A személyazonossági megoldásnak képesnek kell lennie a felhőalapú szolgáltatásokkal való interakcióra, hogy zökkenőmentes felhasználói élményt nyújtson a végfelhasználók számára, miközben gondoskodik az adatok védelméről, és növelheti az adatszivárgás elleni védelmet. 

Az Ön személyazonossági megoldását különböző technikai szabályozások használják, amelyek az alábbi ábrán látható módon biztosítanak tartalomkezelést:

![biztonsági vezérlők](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Az Identitáskezelés rendszerének kihasználására szolgáló biztonsági vezérlők**

Általánosságban elmondható, hogy a tartalomkezelési követelmények a következő területeken fogják kihasználni az Identity Management rendszerét:

* Adatvédelem: azonosítja az erőforrást birtokló felhasználót, és alkalmazza a megfelelő vezérlőket az integritás megőrzése érdekében.
* Adatbesorolás: a felhasználó vagy csoport, valamint az objektumhoz való hozzáférés szintjének meghatározása a besorolása alapján. 
* Adatszivárgások elleni védelem: az adatok védelméért felelős biztonsági ellenőrzéseknek a szivárgás elkerülése érdekében az identitás rendszerével kell együttműködni a felhasználó identitásának ellenőrzéséhez. Ez a naplózási nyomvonal szempontjából is fontos.

> [!NOTE]
> Az adatbesorolással kapcsolatos ajánlott eljárásokról és irányelvekről az adatok besorolása [a felhőalapú felkészültségről](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) című témakörben olvashat bővebben.
> 
> 

A hibrid identitás megoldás megtervezése során győződjön meg arról, hogy a szervezet követelményeinek megfelelően a következő kérdések válaszolnak:

* Rendelkezik a vállalata biztonsági ellenőrzésekkel az adatvédelem érvénybe léptetéséhez?
  * Ha igen, a biztonsági vezérlők integrálva lesznek a hibrid identitási megoldással, amelyet el szeretne fogadni?
* A vállalata az adatbesorolást használja?
  * Ha igen, a jelenlegi megoldás képes integrálni a hibrid identitási megoldással, amelyet el fog fogadni?
* A vállalata jelenleg bármilyen megoldást kínál az adatszivárgásra? 
  * Ha igen, a jelenlegi megoldás képes integrálni a hibrid identitási megoldással, amelyet el fog fogadni?
* A vállalatának szüksége van az erőforrásokhoz való hozzáférés naplózására?
  * Ha igen, milyen típusú erőforrásokat?
  * Ha igen, milyen szintű információra van szükség?
  * Ha igen, akkor hol kell lennie a naplónak? A helyszínen vagy a felhőben?
* A vállalatának titkosítania kell a bizalmas adatokat tartalmazó e-maileket (SSNs, hitelkártya-számok stb.)?
* A vállalatának titkosítania kell a külső üzleti partnerekkel megosztott összes dokumentumot/tartalmat?
* A vállalatának bizonyos típusú e-mailekre vonatkozó vállalati házirendeket kell kikényszeríteni (nem kell a választ adni, nem továbbítható)?

> [!NOTE]
> Minden válaszról készítsen feljegyzéseket, és ismerje meg a válaszok indokait. Az [adatvédelmi stratégia meghatározása](plan-hybrid-identity-design-considerations-data-protection-strategy.md) a rendelkezésre álló lehetőségeket, az egyes lehetőségek előnyeit és hátrányait veszi át.  A kérdések megválaszolása után kiválaszthatja, hogy melyik lehetőség felel meg legjobban az üzleti igényeinek.
> 
> 

## <a name="next-steps"></a>További lépések
[A hozzáférés-vezérlésre vonatkozó követelmények meghatározása](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Lásd még:
[Tervezési szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)

