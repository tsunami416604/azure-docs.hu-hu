---
title: Az Azure Active Directory hibrid identitástervezési szempontjai – áttekintés | Microsoft dokumentumok
description: Útmutató a hibrid identitástervezéssel kapcsolatos szempontok áttekintéséről és tartalomtérképéről
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 100509c4-0b83-4207-90c8-549ba8372cf7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7f8dd49f3668b8f68753681123a04d21edac46c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381463"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Azure Active Directory hibrid identitáskezelés – kialakítási szempontok
A fogyasztói alapú eszközök a vállalati világot is elszaporodják, és a felhőalapú szoftverszolgáltatásként (SaaS) alkalmazások könnyen alkalmazhatók. Ennek eredményeképpen a felhasználók alkalmazás-hozzáférésének karbantartása a belső adatközpontok és a felhőplatformok között kihívást jelent.  

A Microsoft identitáskezelési megoldásai kiterjednek a helyszíni és felhőalapú képességekre is, így egyetlen felhasználói identitás jön létre hitelesítés és engedélyezés céljából az összes erőforráshoz, helytől függetlenül. Ez a fogalom az úgynevezett hibrid identitás. A Microsoft-megoldások használatával a hibrid identitáshoz különböző tervezési és konfigurációs lehetőségek állnak rendelkezésre, és bizonyos esetekben nehéz lehet meghatározni, hogy melyik kombináció felel meg a legjobban a szervezet igényeinek. 

Ez a hibrid identitástervezési szempontokútmutató segít megérteni, hogyan tervezheti meg a szervezet üzleti és technológiai igényeinek leginkább megfelelő hibrid identitáskezelési megoldást.  Ez az útmutató a szervezet egyedi követelményeinek megfelelő hibrid identitáskezelési megoldás megtervezéséhez követendő lépések és feladatok sorozatát ismerteti. A lépések és feladatok során az útmutató bemutatja a szervezetek számára elérhető megfelelő technológiákat és funkciókat a funkcionális és szolgáltatásminőség (például a rendelkezésre állás, a méretezhetőség, a teljesítmény, a kezelhetőség és a biztonság) szintjének való megfeleltetés érdekében. Követelmények. 

Pontosabban, a hibrid identitás tervezési szempontok útmutató célja, hogy válaszoljon a következő kérdésekre: 

* Milyen kérdéseket kell feltennem és megválaszolnom ahhoz, hogy hibrid identitásspecifikus kialakítást hajthassak egy olyan technológiához vagy problématartományhoz, amely a legjobban megfelel az igényeimnek?
* Milyen tevékenységsorozatot kell végrehajtani, hogy tervezzen egy hibrid identitásmegoldás-megoldás a technológia vagy a probléma tartomány? 
* Milyen hibrid identitástechnológia és konfigurációs lehetőségek állnak rendelkezésre a követelmények teljesítéséhez? Milyen kompromisszumok vannak a lehetőségek között, hogy kiválaszthassam a vállalkozásom számára legmegfelelőbb megoldást?

## <a name="who-is-this-guide-intended-for"></a>Kinek szól ez az útmutató?
 CIO, CITO, chief Identity Architects, Enterprise Architects és IT Architects felelős tervezése hibrid identitásmegoldás i közepes vagy nagy szervezetek számára.

## <a name="how-can-this-guide-help-you"></a>Miben segít ez az útmutató?
Ez az útmutató segítségével megtudhatja, hogyan tervezhet egy hibrid identitáskezelési megoldást, amely képes integrálni egy felhőalapú identitáskezelő rendszert a jelenlegi helyszíni identitáskezelési megoldással. 

Az alábbi ábra egy hibrid identitáskezelési megoldást mutat be, amely lehetővé teszi, hogy az informatikai rendszergazdák a helyszíni Windows Server Active Directory-megoldásukat integrálják a Microsoft Azure Active Directoryval, hogy a felhasználók használhassák az egyszeri bejelentkezést ( Egyszeri bejelentkezés) a felhőben és a helyszíni alkalmazásokban.

![Példa](media/plan-hybrid-identity-design-considerations/hybridID-example.png)

A fenti ábra egy olyan hibrid identitáskezelési megoldás példája, amely a felhőszolgáltatásokat a helyszíni funkciókkal való integrációra használja fel annak érdekében, hogy egyetlen felhasználói élményt nyújtson a végfelhasználói hitelesítési folyamatnak, és megkönnyítse az informatikai szolgáltatások kezelését Erőforrások. Bár ez a példa lehet egy gyakori forgatókönyv, minden szervezet hibrid identitás terv valószínűleg eltér a példa az 1. 

Ez az útmutató egy sor olyan lépést és feladatot tartalmaz, amelyek végrehajtásához olyan hibrid identitáskezelési megoldást tervezhet, amely megfelel a szervezet egyedi követelményeinek. A következő lépések és feladatok során az útmutató bemutatja a szervezet funkcionális és szolgáltatásminőségi követelményeinek teljesítéséhez rendelkezésre álló megfelelő technológiákat és funkciókat.

**Feltételezések**: Van némi tapasztalata a Windows Server, az Active Directory tartományi szolgáltatások és az Azure Active Directory. Ebben a dokumentumban feltételezzük, hogy azt keresi, hogy ezek a megoldások hogyan elégülhetnek meg az üzleti igényeknek önmagukban vagy integrált megoldásban.

## <a name="design-considerations-overview"></a>Tervezési szempontok – áttekintés
Ez a dokumentum olyan lépésekkel és feladatokkal rendelkezik, amelyek végrehajtásához olyan hibrid identitáskezelési megoldást tervezhet, amely a legjobban megfelel az igényeinek. Az egyes lépések a megfelelő sorrendben követik egymást. A későbbi lépésekben ismertetett tervezési szempontok miatt azonban módosítania kell a korábbi lépésekben hozott döntéseket, azonban az ütköző tervezési döntések miatt. Minden kísérlet arra irányul, hogy figyelmeztesse Önt a dokumentum ban esetleges tervezési ütközések. 

Csak akkor jut el a tervhez, amely a legjobban megfelel az Ön követelményeinek, miután a lépések en annyiszor halad, ahányszor szükséges ahhoz, hogy az összes szempontot beépítse a dokumentumba. 

| Hibrid identitásfázis | Témakörlista |
| --- | --- |
| Identitáshoz kapcsolódó követelmények meghatározása |[Határozza meg az üzleti igényeket](plan-hybrid-identity-design-considerations-business-needs.md)<br> [Címtár-szinkronizálási követelmények meghatározása](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Többtényezős hitelesítési követelmények meghatározása](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Hibrid identitásbevezetési stratégia definiálása](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Terv az adatbiztonság megerősítésére az erős identitásmegoldás révén |[Az adatvédelmi követelmények meghatározása](plan-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [A tartalomkezeléshez kapcsolódó követelmények meghatározása](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [A hozzáférés-vezérlésre vonatkozó követelmények meghatározása](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Az incidensmegoldáshoz kapcsolódó követelmények meghatározása](plan-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Adatvédelmi stratégia meghatározása](plan-hybrid-identity-design-considerations-data-protection-strategy.md) |
| A hibrid identitáskezelés életciklusának megtervezése |[Hibrid identitáskezelési feladatok meghatározása](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Szinkronizálás kezelés](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Hibrid identitáskezelési bevezetési stratégia meghatározása](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="next-steps"></a>Következő lépések
[Identitáshoz kapcsolódó követelmények meghatározása](plan-hybrid-identity-design-considerations-business-needs.md)

