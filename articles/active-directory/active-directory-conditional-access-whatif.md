---
title: "Az Azure Active Directory, mi történik, ha eszköz feltételes hozzáférés – előzetes verzió |} Microsoft Docs"
description: "Ismerje meg, hogyan tesztelheti az Azure Active Directory feltételes hozzáférési házirendek konfigurációját."
services: active-directory
keywords: "alkalmazások, a feltételes hozzáférés az Azure ad-vel, a biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési házirendekkel a feltételes hozzáférés"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/21/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: de6b3dcd77132154e583d7333983d6745c4aa3bd
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="azure-active-directory-conditional-access-what-if-tool---preview"></a>Az Azure Active Directory, mi történik, ha eszköz feltételes hozzáférés – előzetes

[Feltételes hozzáférés](active-directory-conditional-access-azure-portal.md) egy Azure Active Directory (Azure AD), amelyek segítségével szabályozhatja, hogy hogyan funkció engedélyezett a felhasználók hozzáférést a felhőalapú alkalmazásokat. Honnan tudhatja mi történik az űrlap a feltételes hozzáférési szabályzatokat a környezetében? Ez a kérdés megválaszolásához használhatja a **feltételes hozzáférés mi történik, ha eszköz**.

Ez a cikk azt ismerteti, hogyan használhatja az eszköz a feltételes hozzáférési házirendek tesztelésére.

## <a name="what-it-is"></a>Mi ez?

A **feltételes hozzáférés, milyen if házirend eszközzel** teszi lehetővé a saját környezete a feltételes hozzáférési házirendek hatásának megismerése. Befolyásoló tényezők a házirendeket több bejelentkezések manuálisan elvégzésével teszt helyett az eszköz lehetővé teszi egy szimulált bejelentkezés felhasználói kiértékeléséhez. A szimuláció becslése bejelentkezés Ez hatással legyen a házirendek és a szimuláció jelentést hoz létre. A jelentés nem csak listájában alkalmazott feltételes hozzáférési házirendek is [klasszikus házirendek](active-directory-conditional-access-migration.md#classic-policies) ha vannak ilyenek.    

A mi Ha az eszközök is módszert kínál a gyorsan határozzák meg a házirendekben, amelyek egy adott felhasználóra vonatkoznak. Használhatja az adatokat, például ha a probléma elhárítása érdekében van szüksége.  

## <a name="how-it-works"></a>Működés

Az a **feltételes hozzáférés mi történik, ha eszköz**, először konfigurálnia kell a beállításokat szeretné szimulálni bejelentkezési forgatókönyv. Ezek a beállítások a következők:

- A vizsgálni kívánt felhasználó 

- A felhőalapú alkalmazásokat a felhasználó akkor próbálja meg elérni a

- Mely való hozzáférés feltételeit, a felhő konfigurálása alkalmazások történik.
     
A következő lépésben futtassa a szimuláció, amely kiértékeli a beállítások is kezdeményezhető. Csak azok a házirendekben, amelyek engedélyezve vannak a Futtatás próbaverzióként részét képezik.


Amikor befejezte a kiértékelése, az eszköz jelentését állítja elő a érintett házirendek.


## <a name="running-the-tool"></a>Az eszköz futtatása

Megtalálhatja az **mi történik, ha** eszköz a  **[feltételes hozzáférés - házirendek](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)**  oldal az Azure portálon.

Indítsa el az eszközt, az eszköztár fölött a házirendek listájában, kattintson a **mi történik, ha**.

![mi van, ha](./media/active-directory-conditional-access-whatif/01.png)

Próbaverzióként futtatása előtt konfigurálnia kell a beállításokat.

## <a name="settings"></a>Beállítások

Ez a szakasz a szimuláció futtatása beállításokkal kapcsolatos információkat nyújt.

![mi van, ha](./media/active-directory-conditional-access-whatif/02.png)


### <a name="user"></a>Felhasználó

Csak kijelölhet egy felhasználót. Ez az egyetlen mezőt kötelező kitölteni.

### <a name="cloud-apps"></a>Felhőalkalmazások

Ez a beállítás alapértelmezés szerint **összes felhőalapú alkalmazások**. Az alapértelmezett beállítás az összes rendelkezésre álló házirend próbaverzióként környezetében hajt végre. A házirendek adott felhőalkalmazások érintő hatókör megadásával szűkíthető.


### <a name="ip-address"></a>IP-cím

Az IP-cím nem egy egyetlen IPv4-cím, hogy utánozzák a [hely feltétel](active-directory-conditional-access-azure-portal.md#locations). A cím internetre irányuló jelentkezhet be a felhasználó által használt eszköz címe jelöli. Ellenőrizheti az IP-cím egy eszköz, például máshová [Mi az az IP-cím](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Eszközplatformok

Ez a beállítás utánozza a [eszköz platformok feltétel](active-directory-conditional-access-azure-portal.md#device-platforms) , pedig a egyenértékű **minden platform (beleértve a nem támogatott)**. 
### <a name="client-apps"></a>Ügyfélalkalmazások

Ez a beállítás utánozza a [ügyfél alkalmazások feltétel](active-directory-conditional-access-azure-portal.md#client-apps).
Alapértelmezés szerint ez a beállítás hatására az rendelkező összes házirend értékelését **böngésző** vagy **mobilalkalmazások és asztali ügyfelek** vagy külön-külön vagy mindkettő kiválasztva. Azt is észleli, amely kötelezővé házirendek **Exchange ActiveSync (EAS)**. Megadásával szűkíthető, ez a beállítás kiválasztásával:

- **Böngésző** , legalább az összes házirendek kiértékelése **böngésző** kijelölt. 

- **Mobilalkalmazások és asztali ügyfelek** , legalább az összes házirendek kiértékelése **mobilalkalmazások és asztali ügyfelek** kijelölt. 


### <a name="sign-in-risk"></a>Bejelentkezési kockázat

Ez a beállítás utánozza a [bejelentkezési kockázat feltétel](active-directory-conditional-access-azure-portal.md#sign-in-risk).   


## <a name="evaluation"></a>Próbaverzió 

Egy értékelési indításához kattintson **mi történik, ha**. A kiértékelés eredménye lehetővé teszi egy jelentést, amely áll: 

![mi van, ha](./media/active-directory-conditional-access-whatif/03.png)

- Azt jelzi hogy klasszikus házirendek szerepel-e a környezetben
- A felhasználói házirendeket
- A felhasználó nem vonatkozó házirendek


Ha [klasszikus házirendek](active-directory-conditional-access-migration.md#classic-policies) a kijelölt felhőalapú alkalmazásokhoz, azt jelzi hogy áll rendelkezésre. A kijelző kattint, ekkor megnyílik a klasszikus házirendek lapon. A klasszikus házirendek lapon klasszikus házirend áttelepítése, vagy csak tiltsa le azt. Ezen a lapon bezárásával a kiértékelésének eredménye térhet vissza.

A házirendekben, amelyek érvényesek a kiválasztott felhasználó listája is megkeresheti listája [vezérlők biztosítani](active-directory-conditional-access-controls.md#grant-controls) és [munkamenet](active-directory-conditional-access-controls.md#session-controls) meg kell felelnie a felhasználó a vezérlő.

A házirendekben, amelyek nem érvényesek a felhasználói listájában el, és is megtalálhatja a miért ezek a házirendek alkalmazása nem lehetnek. Minden felsorolt házirend OK jelenti az első feltétel, amely nem teljesült. Egy olyan házirendet, amely nem alkalmazható oka lehet egy letiltott házirend értékelése nem további miatt.   



## <a name="next-steps"></a>További lépések

- Ha meg szeretné ismerni a feltételes hozzáférési házirend konfigurálása tudnivalókat [Ismerkedés a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal-get-started.md).

- Ha készen áll a környezet feltételes hozzáférési házirend-beállításokkal, tekintse meg a [ajánlott eljárások a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-best-practices.md). 

- Ha szeretne áttelepíteni a klasszikus házirendek, lásd: [klasszikus szabályzatokkal az Azure portálon áttelepítése](active-directory-conditional-access-migration.md)  
