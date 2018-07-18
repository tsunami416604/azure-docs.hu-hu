---
title: Mi a mi Ha eszközt az Azure Active Directory feltételes hozzáférés?
description: Ismerje meg, hogyan képes megérteni a feltételes hozzáférési szabályzatokat a hatását a környezetben.
services: active-directory
keywords: feltételes hozzáférés az alkalmazásokhoz, az Azure AD feltételes hozzáférés, biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési szabályzatok
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 34f6efaac00f4aa17ea6a53ab51da69b84591e35
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113028"
---
# <a name="what-is-the-what-if-tool-in-azure-active-directory-conditional-access"></a>Mi a mi Ha eszközt az Azure Active Directory feltételes hozzáférés?

[Feltételes hozzáférés](active-directory-conditional-access-azure-portal.md) egy funkció az Azure Active Directory (Azure AD), amelyek segítségével szabályozhatja, hogy hogyan jogosult felhasználók hozzáférésének a felhőalkalmazásokhoz. Hogyan, hogy mire számíthatnak az űrlap a feltételes hozzáférési szabályzatokat a környezetében? Ez a kérdés megválaszolásához használhatja a **feltételes hozzáférés, mi történik, ha eszköze**.

Ez a cikk bemutatja, hogyan használhatja ezt az eszközt a feltételes hozzáférési szabályzatokat teszteléséhez.

## <a name="what-it-is"></a>Mi ez?

A **feltételes hozzáférés Lehetőségelemzési házirend eszközzel** lehetővé teszi, hogy a feltételes hozzáférési szabályzatokat az adott környezet hatásának megismerése. Helyett a teszt több bejelentkezések kézi végrehajtásával a szabályzatok vezetői Ez az eszköz lehetővé teszi egy szimulált jelentkezzen be egy felhasználó kiértékeléséhez. A szimuláció becslése a hatás bejelentkezési ebben a szabályzatok a, és a szimuláció jelentést hoz létre. A jelentés nem tartalmazza csak alkalmazott feltételes hozzáférési szabályzatok is [klasszikus szabályzatok](active-directory-conditional-access-migration.md#classic-policies) ha vannak ilyenek.    

A mi Ha eszközök azt is lehetővé teszi a gyors határozza meg a szabályzatokban, amelyek egy adott felhasználó a alkalmazni. Használhatja a információkat, például ha a probléma elhárításához van szüksége.  

## <a name="how-it-works"></a>Működés

Az a **feltételes hozzáférés, mi történik, ha eszköze**, először konfigurálja a beállításokat szeretné szimulálni bejelentkezési forgatókönyv. Ezek a beállítások a következők:

- A vizsgálni kívánt felhasználó 

- A felhasználó megpróbálja elérni a felhőalkalmazások

- Melyik való hozzáférés feltételeit, a konfigurálja a felhőalapú alkalmazások történik.
     
A következő lépésben a szimuláció futtatása, amely kiértékeli a beállítások is kezdeményezhető. Csak olyan szabályzatokat, a rendszer-kiértékelés futtatása részét képezik.


Az értékelés befejeződött, amikor az eszköz szabályzat érintett jelentést hoz létre.


> [!NOTE]
> A mi jelenleg, ha az eszköz nem támogatja a beágyazott csoportokat. Ha egy felhasználó tagja egy csoportnak, és a csoport tagja egy másik csoportra, amely használatban van egy feltételes hozzáférési szabályzatot, akkor a Mi történik, ha eszköz nem megfelelően jelennek meg, hogy a szabályzat hatásának a felhasználó számára. 


## <a name="running-the-tool"></a>Az eszköz futtatása

Annak a **mi történik, ha** eszköz a **[feltételes hozzáférés – szabályzatok](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** oldal az Azure Portalon.

Kattintson az eszköztár fölött a szabályzatok listáján, az eszköz indításához **mi történik, ha**.

![mi van, ha](./media/active-directory-conditional-access-whatif/01.png)

Egy értékelés futtatása előtt konfigurálnia kell a beállításokat.

## <a name="settings"></a>Beállítások

Ez a szakasz a szimuláció futtatása az beállításokra vonatkozó információkat biztosít.

![mi van, ha](./media/active-directory-conditional-access-whatif/02.png)


### <a name="user"></a>Felhasználó

Csak kijelölhet egy felhasználót. Ez az egyetlen mező kitöltése kötelező.

### <a name="cloud-apps"></a>Felhőalkalmazások

Ez a beállítás alapértelmezett értéke **az összes felhőalapú alkalmazások**. Az alapértelmezett beállítás az összes rendelkezésre álló házirend-értékelés környezetében hajtja végre. A hatókör, a házirendek adott felhőalkalmazásokat érintő szűkíthető.


### <a name="ip-address"></a>IP-cím

Az IP-cím egy IPv4-címmel referenciaszámítógépnek a [helyfeltétel](active-directory-conditional-access-locations.md). A címet jelöli az internetre irányuló jelentkezzen be a felhasználó által használt eszköz címe. Ellenőrizheti az IP-cím egy eszköz, például ellenőrizheti, hogy [Mi az saját IP-cím](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Eszközplatformok

Ez a beállítás utánozza a [eszköz platformok feltétel](active-directory-conditional-access-conditions.md#device-platforms) és egyenértékű jelöli **minden platformon (beleértve a nem támogatottakat)**. 
### <a name="client-apps"></a>Ügyfélalkalmazások

Ez a beállítás utánozza a [ügyfél alkalmazások feltétel](active-directory-conditional-access-conditions.md#client-apps).
Alapértelmezés szerint a beállítás hatására az összes házirend kellene értékelését **böngésző** vagy **mobilalkalmazások és asztali ügyfelek** vagy külön-külön vagy mindkettő kiválasztva. Azt is észleli házirendeket kikényszerítő **Exchange ActiveSync (EAS)**. Ez a beállítás kiválasztásával szűkítheti:

- **Böngésző** értékelheti ki az összes szabályzat, legalább **böngésző** kiválasztott. 

- **Mobilalkalmazások és asztali ügyfelek** értékelheti ki az összes szabályzat, legalább **mobilalkalmazások és asztali ügyfelek** kiválasztott. 


### <a name="sign-in-risk"></a>Bejelentkezési kockázat

Ez a beállítás utánozza a [bejelentkezési kockázati feltétellel](active-directory-conditional-access-conditions.md#sign-in-risk).   


## <a name="evaluation"></a>Próbaverzió 

Az értékelés indításához kattintson **mi történik, ha**. Az értékelés eredménye nyújt egy jelentést, amely a következőkből áll: 

![mi van, ha](./media/active-directory-conditional-access-whatif/03.png)

- Indikátor e klasszikus szabályzatok vannak jelen a környezetében
- A felhasználói házirendeket
- A felhasználó nem vonatkoznak a szabályzatok


Ha [klasszikus szabályzatok](active-directory-conditional-access-migration.md#classic-policies) találhatók a kiválasztott felhőalapú alkalmazások, a mutatója megjelenik. A kijelző kattintva, a rendszer átirányítja a klasszikus szabályzatok lapon. A klasszikus szabályzatok oldalon a klasszikus szabályzat áttelepítése, vagy csak letiltja azt. Zárja be ezt oldal visszatérhessen a kiértékelésének eredménye.

A házirendekben, amelyek érvényesek a kiválasztott felhasználó listából is megtalálhatja listáját [vezérlők biztosítson](active-directory-conditional-access-controls.md#grant-controls) és [munkamenet](active-directory-conditional-access-controls.md#session-controls) szabályozza a felhasználónak meg kell felelniük.

A házirendekben, amelyek nem érvényesek a felhasználói listán is, és is megkeresheti, ha az okokat, hogy miért ezek a szabályzatok nem vonatkoznak. Minden egyes listázott házirendjének okát jelöli az első feltétel nem teljesült. Egy lehetséges oka az olyan szabályzatot, amely a rendszer nem alkalmazza a letiltott házirend azért, továbbá nem értékeli ki.   



## <a name="next-steps"></a>További lépések

- Ha azt szeretné tudni, hogyan lehet feltételes hozzáférési szabályzat konfigurálása, lásd: [többtényezős hitelesítés megkövetelése az Azure Active Directory feltételes hozzáférés az adott alkalmazások](active-directory-conditional-access-app-based-mfa.md).

- Ha készen áll a környezetre vonatkozó feltételes hozzáférési szabályzatok konfigurálására, tekintse meg a [ajánlott eljárások az Azure Active Directory feltételes hozzáférés](active-directory-conditional-access-best-practices.md). 

- Ha azt szeretné, hogy klasszikus szabályzatok migrálása, lásd: [az Azure Portalon klasszikus szabályzatok Migrálása](active-directory-conditional-access-migration.md)  
