---
title: Az Azure Active Directory Identity Protection aktív kockázati események bezárása |} A Microsoft Docs
description: Bemutatjuk a Bezárás aktív kockázati események rendelkezik.
services: active-directory
keywords: az Azure active directory identity protection a következőket cloud app discovery szolgáltatást, alkalmazások, biztonság, kockázati, kockázati szint, biztonsági rést, biztonsági házirend kezelése
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50448cc5d4fe6714aa0cd29216209506eccf7a7c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201618"
---
# <a name="how-to-close-active-risk-events"></a>kézikönyv: Az aktív kockázati események bezárása

A [kockázati események](../reports-monitoring/concept-risk-events.md), Azure Active Directory észleli a mutatók esetleg feltört felhasználói fiókok esetében. A rendszergazdák szeretne kapni a lezárt, az összes kockázati események, hogy az érintett felhasználók már nem veszélyben vannak.

Ez a cikk áttekintést, a további lehetőségek gombra kattintva zárja be az aktív kockázati események rendelkezik.

## <a name="options-to-close-risk-events"></a>A beállítások gombra kattintva zárja be a kockázati események 

Egy kockázati esemény állapota vagy **aktív** vagy **lezárt**. Az összes aktív kockázati események járulnak hozzá a felhasználói kockázat szintjének nevű érték kiszámítása. A felhasználói kockázat szintje egy jelző (alacsony, közepes, nagy) a valószínűsége annak, hogy a fiók biztonsága sérült. 

Gombra kattintva zárja be az aktív kockázati események, a következő lehetőségek állnak rendelkezésére:

- Felhasználói kockázati házirendet az új jelszó kérése

- Manuális jelszó-visszaállítás
 
- Az összes kockázati esemény elvetését 

- Zárja be az egyes kockázati események manuális



## <a name="require-password-reset-with-a-user-risk-policy"></a>Felhasználói kockázati házirendet az új jelszó kérése

A [felhasználói kockázati feltételes hozzáférési szabályzat](howto-user-risk-policy.md) konfigurálásával jelszómódosítást kötelezővé teheti a jelszómódosítást, ha egy adott felhasználó kockázati szintje automatikusan észlelhető. 

![Új jelszó létrehozása](./media/howto-close-active-risk-events/13.png)

A jelszó alaphelyzetbe állítása az összes aktív kockázatok események a kapcsolódó felhasználó bezárja, majd az identitás vissza biztosítható biztonságos állapotba. Felhasználói kockázati házirendet az a következő gombra kattintva zárja be aktív kockázati események, mert ez a módszer automatizált előnyben részesített módszere. Nincs nem szükséges az érintett felhasználó és a Súgó a segélyszolgálathoz vagy a rendszergazda közötti interakció.

Azonban a felhasználói kockázati házirend használatával nem mindig alkalmazható. Ez vonatkozik, például a:

- A felhasználók, amely nincs regisztrálva a többtényezős hitelesítés (MFA).
- Aktív kockázati események törölt felhasználók.
- Vizsgálat, amely felfedi, hogy az egy jelentett kockázati esemény a jogosult felhasználó által végrehajtott-e.


## <a name="manual-password-reset"></a>Manuális jelszó-visszaállítás

Ha egy jelszó alaphelyzetbe állításával a felhasználói kockázati házirendet használatával nem egy beállítást, az összes kockázati események zárva a manuális jelszó alaphelyzetbe állítása a felhasználók is igénybe.

![Új jelszó létrehozása](./media/howto-close-active-risk-events/04.png)


A kapcsolódó párbeszédpanel kétféleképpen jelszó alaphelyzetbe állítása itt:

![Új jelszó létrehozása](./media/howto-close-active-risk-events/05.png)


**Ideiglenes jelszó létrehozása** -kódjának létrehozásával egy ideiglenes jelszót, azonnal átvihetők az identitás vissza biztonságos állapotba. Ennél a módszernél az érintett felhasználók implementálására, mert tudja, mit jelent az ideiglenes jelszó szükséges. Például küldhet az új, ideiglenes jelszót a felhasználó másodlagos e-mail cím vagy a felhasználó felettesét. Mert a jelszó ideiglenes kéri a felhasználót, hogy a jelszó módosítása során a következő bejelentkezéskor.


**Új jelszó a felhasználónak** – a felhasználók új jelszót igénylő lehetővé teszi önkiszolgáló helyreállítás nélkül léphet kapcsolatba a támogatási szolgálat vagy rendszergazda. Például egy felhasználói kockázati házirend esetén ez a módszer csak érvényes, a multi-factor Authentication regisztrált felhasználók. A felhasználók számára, hogy nincs regisztrálva a multi-factor Authentication még ez a beállítás nem érhető el.


## <a name="dismiss-all-risk-events"></a>Az összes kockázati esemény elvetését

Ha a jelszó alaphelyzetbe állítása lehetőség nem az Ön számára, is bezárhatja az összes kockázati eseményt. 

![Új jelszó létrehozása](./media/howto-close-active-risk-events/03.png)

Amikor rákattint **az összes esemény elvetését**, az összes esemény bezárása és az érintett felhasználó már nem veszélyben van. Azonban ez a módszer nincs hatással a meglévő jelszót, mert azt nem állapotba hozza a kapcsolódó identitás vissza biztonságos állapotban. A metódus az előnyben részesített használati eset az aktív kockázati események törölt felhasználó. 


## <a name="close-individual-risk-events-manually"></a>Zárja be az egyes kockázati események manuális

Az egyes kockázati események manuális bezárhatja. Kockázati események manuális lezárása, csökkenthető a felhasználói kockázat szintje. Azt jelzi, hogy egy kapcsolódó vizsgálat válaszul manuálisan lezárulnak-általában kockázati események. Például ha kommunikáló felhasználók tárja fel, hogy egy aktív kockázati esemény már nem szükséges. 
 
Kockázati események manuális lezárása, amikor egy kockázati esemény állapotának módosítása a következő műveletek bármelyike érvénybe választhat:

![Műveletek](./media/howto-close-active-risk-events/06.png)

- **Oldja meg** – Ha vizsgálatát, hogy egy kockázati esemény után egy megfelelő korrekciós műveletet identitásvédelem kívül tartott, és úgy gondolja, hogy a kockázati esemény tekintendők-e zárva, jelölje meg az esemény megoldva. Esemény lezárva a kockázati esemény állapotát állítja be, és a kockázati esemény már nem hozzájárul ahhoz, hogy a felhasználói kockázat feloldani.

- **Jelölje meg a hamis pozitív** – néhány esetben Ön egy kockázati esemény vizsgálata és helytelenül lett megjelölve, a kockázatos felderítése. Segíthet az ilyen előfordulások száma csökkenthető a vakriasztások kockázati esemény megjelölése. Ez segít a gépi tanulási algoritmusok hasonló események besorolása a jövőben javítása érdekében. Vakriasztások események állapotát lezárva, és már nem a felhasználói kockázat is hozzájárul.

- **Hagyja figyelmen kívül** – Ha a szervizelés semmit nem tett, de szeretné, el kell távolítani a aktív listából a kockázati esemény egy kockázati esemény figyelmen kívül hagyása jelölheti meg, és be lesz zárva az esemény állapota. Felhasználói kockázat nem jelentenek figyelmen kívül hagyta eseményt. Ez a beállítás csak szokatlan körülmények között nem használható.

- **Aktiválja újra** -(kiválasztásával megoldása, a hamis pozitív vagy figyelmen kívül hagyása) manuálisan lezárt kockázati események aktiválhatók, állítja vissza az esemény állapota aktív. Újraaktivált kockázati események járulnak hozzá a felhasználó kockázati szintű számítás. Nem lehet újraaktiválni (például egy biztonságos jelszó-visszaállítási) szervizelés útján lezárt kockázati események.
  

## <a name="next-steps"></a>További lépések

Az Azure AD Identity Protection áttekintést kaphat, tekintse meg a [áttekintése az Azure AD Identity Protection](overview.md).
