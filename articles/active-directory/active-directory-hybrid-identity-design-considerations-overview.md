---
title: "Az Azure Active Directory hibrid identitáskezelési elrendezésével kapcsolatos szempontok - áttekintése |} Microsoft Docs"
description: "Áttekintés és a hibrid Identitáskezelés – kialakítási szempontokat útmutató tartalmak térképét"
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: 100509c4-0b83-4207-90c8-549ba8372cf7
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: e2a70f2474298618dd8ee11c583f8f445d7eba7d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Azure Active Directory hibrid identitáskezelés – kialakítási szempontok
Ügyfél-alapú eszközök a vállalati globális vannak proliferating, és felhő alapú szoftver-,--szolgáltatás (SaaS) alkalmazások könnyen fogad el. Ennek eredményeképpen az adatközpontok és felhőszolgáltatások platformon, belső alkalmazás hozzáférést irányítás megőrzéséhez kihívást.  

A Microsoft identity megoldások span a helyszíni és felhőalapú képességek, a hitelesítés és engedélyezés az összes erőforráshoz, függetlenül a hely egyetlen felhasználói azonosítót létrehozása. A hibrid identitás nevezzük. Különböző kialakítási és konfigurációs beállítások a Microsoft-megoldás használata hibrid identitás, és néhány esetben előfordulhat, hogy nehéz lenne meghatározni, melyik kombináció a legjobb a szervezet igényeinek. 

A hibrid Identitáskezelés – kialakítási szempontokat útmutató segítségével megismerheti, hogyan hibrid identitáskezelési megoldás tervezéséhez, amely legjobban megfelel az üzleti és technológiai igényeinek a szervezet számára.  Ez az útmutató részletesen ismerteti azokat a lépéseket és teendőket, amelyeket követve alakítsa ki a szervezet egyedi igényeinek megfelelő hibrid identitáskezelési megoldás. A lépéseket és feladatok ismertetése során az útmutató jelentenek a releváns technológiákat és funkcionális és szolgáltatásminőségi (például a rendelkezésre állási, a méretezhetőséget, a teljesítmény, a kezelhetőségi és a biztonsági) szervezetek számára rendelkezésre álló beállítások funkció szint követelmények. 

Pontosabban a hibrid identitás tervezési szempontok útmutatója céljai a következő kérdések megválaszolása: 

* Milyen kérdéseket kell megválaszolnom ahhoz egy hibrid identitás-specifikus terv a technológiai vagy problématerületnek igényeimnek leginkább megfelelő, és kérje meg?
* Milyen tevékenységek sorozatát kell végrehajtani a technológiai vagy problématerületnek a hibrid identitáskezelési megoldás tervezéséhez? 
* Milyen hibrid identitás technológiai és konfigurációs lehetőségek állnak rendelkezésre a követelmények teljesítéséhez? Mik azok a kereskedelmi-előnyökkel és hátrányokkal járnak ezek a lehetőségek, hogy kiválaszthassam a legjobb lehetőség a vállalati?

## <a name="who-is-this-guide-intended-for"></a>Kinek szól a Ez az útmutató?
 CIO, CITO, fő identitás fejlesztők, vállalati fejlesztők és informatikai fejlesztők felelősek közepes vagy nagy szervezetek hibrid identitáskezelési megoldás.

## <a name="how-can-this-guide-help-you"></a>Hogyan Ez az útmutató segítségével?
Ez az útmutató segítségével megtudhatja, hogyan tervezhet egy hibrid identitáskezelési megoldás, amely képes a felhőalapú identitás rendszer integrálható a jelenlegi helyszíni identitás-megoldás. 

A következő ábrán látható egy példa egy hibrid identitáskezelési megoldás, amely lehetővé teszi a vállalati rendszergazdáknak az aktuális Windows Server Active Directory megoldással a helyszíni integrálható a Microsoft Azure Active Directory engedélyezése a felhasználók számára az egyszeri bejelentkezés (kezelése Egyszeri bejelentkezés) található a felhőben és helyszíni alkalmazások között.

![](./media/hybrid-id-design-considerations/hybridID-example.png)

A fenti ábrán látható egy példa hibrid identitáskezelési megoldás, amely és szolgáltatásokkal való integráció a helyszíni ahhoz, hogy a felhasználó hitelesítési folyamat egyetlen élményt és lehetővé teszi a felhőalapú szolgáltatások kihasználása révén azokat kezelése informatikai erőforrások. Ez lehet egy nagyon gyakori forgatókönyv, de minden szervezet hibrid identitás tervezési várhatóan eltérő követelmények miatt. ábrán az 1. ábrán láthatótól. 

Ez az útmutató azokat a lépéseket és feladatokat, amelyek követésével megtervezheti a szervezet egyedi igényeinek megfelelő hibrid identitáskezelési megoldás. A következő lépések és feladatok ismertetése során az útmutató mutatja be a releváns technológiákat és szolgáltatási lehetőségeket, hogy megfeleljen a működési és a szolgáltatás szolgáltatásminőségi szintre vonatkozó követelményeinek a szervezet.

**Előfeltételek**: rendelkezik némi tapasztalattal a Windows Server, az Active Directory tartományi szolgáltatások és az Azure Active Directory. Ebben a dokumentumban feltételezzük, hogy olyan eszközökre vonatkozóan, hogy a ezek a megoldások hogyan felelnek meg az üzleti igényeinek önállóan vagy egy integrált megoldás részeként.

## <a name="design-considerations-overview"></a>Kialakítási szempontok áttekintése
Ez a dokumentum a lépéseket és feladatokat, amelyek követésével megtervezheti a vonatkozó követelményeknek legjobban megfelelő hibrid identitáskezelési megoldás biztosít. A lépéseket egy rendezett sorozata jelenjenek meg. Kialakítási szempontok a későbbi lépésekben megismert igényelheti módosítása során meghozott döntések a korábbi lépések, azonban későbbi tervezési döntések ütköznek azokkal. Emiatt mindent megtettünk riasztást küld a dokumentum lehetséges tervezési ütközésekre. 

A tervezési, hogy igényeknek leginkább megfelelő annyiszor a dokumentumban ismertetett szempontok alapján szükséges lépéseket iteráció után csak érkeznek. 

| Hibrid Identity szakasz | A témakör listája |
| --- | --- |
| Identitás-követelmények meghatározása |[Az üzleti igények meghatározása](active-directory-hybrid-identity-design-considerations-business-needs.md)<br> [Címtár-szinkronizálás követelmények meghatározása](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [A multi-factor authentication követelmények meghatározása](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [A hibrid identitás bevezetési stratégia meghatározása](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Adatok biztonsági erős identitáskezelési megoldással továbbfejlesztésének tervezése |[Adatvédelmi követelményeinek meghatározása](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [A Tartalomkezelés követelmények meghatározása](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Hozzáférés-vezérlési követelményeinek meghatározása](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Incidensválasz-követelmények meghatározása](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Data protection stratégia meghatározása](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) |
| Hibrid identitás életciklusának tervezése |[Hibrid identitás felügyeleti feladatok meghatározása](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Szinkronizálás kezelése](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Hibrid identitás kezelés bevezetési stratégiájának meghatározása](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="download-this-guide"></a>Ez az útmutató letöltése
A hibrid identitáskezelési elrendezésével kapcsolatos szempontok útmutatója a pdf-es letöltheti a [Technet gallery](https://gallery.technet.microsoft.com/Azure-Hybrid-Identity-b06c8288). 

