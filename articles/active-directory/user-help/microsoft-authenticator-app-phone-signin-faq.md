---
title: A Microsoft Authenticator telefonos bejelentkezés – Azure és a Microsoft-fiókok |} A Microsoft Docs
description: A telefon használatával jelentkezzen be Microsoft-fiókja helyett adja meg a jelszót. Ebben a cikkben megválaszolunk – gyakori kérdések a szolgáltatásról.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: ''
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/12/2017
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 589309c956ebbbebe6c423cfcca117a86c796be3
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060116"
---
# <a name="sign-in-with-your-phone-not-your-password"></a>Bejelentkezés telefonnal, nem kell jelszót

A Microsoft Authenticator alkalmazás segít a kétlépéses ellenőrzés elvégzésével, a jelszó megadása után a fiókok biztonsága. De tudja, hogy azt is cserélje le a személyes Microsoft-fiókja jelszavát teljesen?

Ez a funkció iOS és Android rendszerű eszközökön érhető el, és a személyes Microsoft-fiókokkal működik.

## <a name="how-it-works"></a>Működés

A legtöbb felhasználó használja a Microsoft Authenticator alkalmazást a kétlépéses ellenőrzéshez a Microsoft-fiókkal való bejelentkezéskor. Írja be a jelszavát, majd nyissa meg az alkalmazás értesítéseinek jóváhagyása, vagy ellenőrző kód kérése. A telefonnal bejelentkezni hagyja ki a jelszót, és tegye meg az identitás-ellenőrzést a telefonján. Telefonos bejelentkezés is egy kétlépéses ellenőrzést, akkor továbbra is meg kell adnia egy dolog, tudja, és a egy dolog kell a személyazonosságát. A telefon még mindig a dolog van, és a telefon PIN-kódot vagy biometrikus kulcs ismeri a dolog.

## <a name="how-to-get-started"></a>Első lépések

Jelentkezzen be személyes Microsoft-fiókkal és a telefon, kövesse az alábbi lépéseket:

1. Engedélyezze a telefonnal bejelentkezni a fiókhoz.

  - Ha nem rendelkezik a Microsoft Authenticator alkalmazást, telepítése és a személyes Microsoft-fiókkal, a lépések megfelelően adja hozzá a [Microsoft Authenticator lap](microsoft-authenticator-app-how-to.md). Újonnan hozzáadott fiókok automatikusan engedélyezve vannak, szóval készen lépjen.

  - Ha már használja a Microsoft Authenticator a kétlépéses ellenőrzéshez, válassza ki a fiókját az alkalmazás kezdőlapjáról, és válassza **telefonos bejelentkezés engedélyezése** a legördülő menüből.

  >[!NOTE]
  >Fiókja védelme érdekében szükség van PIN-kódot vagy biometrikus zárolást az eszközön. A telefon zárolása feloldva, ha az alkalmazás egy kérelmet, amely felkéri, állíthatja be a zárolást a telefonos bejelentkezés engedélyezése előtt felugró.

3. Legtöbb oldalt, ahol általában beírnia a Microsoft-fiók jelszavát kell arról, hogy a hivatkozás **inkább alkalmazás**. Kattintson a bejelentkezés telefonnal.

4. A Microsoft értesítést küld a telefonjára. Hagyja jóvá az értesítést, a bejelentkezés a fiókba.   

## <a name="faq"></a>GYIK

### <a name="how-is-signing-in-with-my-phone-more-secure-than-typing-a-password"></a>Hogyan biztonságosabb, mint a jelszó beírása telefonom történő bejelentkezés?  

Ma a legtöbb ember bejelentkezés webhelyek vagy alkalmazások felhasználónév és jelszó használatával.  Sajnos jelszavak rendszer gyakran elvesztése, ellopása, vagy a támadók által kitalálni. Ha beállította a Microsoft Authenticator alkalmazás való bejelentkezéshez, azt a telefonján, amely a fiók zárolásának feloldása kulcs létrehozása. Ennek a kulcsnak a PIN-kód vagy biometrikus, hogy már használja a telefonján védje azt.  Jelentkezik be a telefonjára, ha ezt a kulcsot biztonságosan két tényező – a telefon, magát, és annak zárolását, hogy a személyazonossága igazolásához szolgál. 

A használt kulcs hasonlít a Windows Hello és a FIDO Alliance UAF specifikációk használt kulcsokat. Az adatok csak akkor önéletrajz helyileg, a kulcs védelmét, és soha nem küldött, vagy a felhőben tárolt. 
 
### <a name="where-can-i-use-my-phone-to-replace-my-password-and-where-would-i-still-need-the-password"></a>Hol használható telefonom cserélje le a jelszót, és ha lenne is meg kell a jelszót?  

A telefon bejelentkezési funkció még ma, csak működik a webalkalmazások és szolgáltatások, amelyek személyes Microsoft-fiókok, iOS vagy Android-alkalmazások, amelyek személyes Microsoft-fiókkal és egy személyes Microsoft-fiókot használó alkalmazások a Windows 10-es működteti. Amikor bejelentkezik az egyik ezek a webhelyek vagy alkalmazások, az oldal, ahol Ön általában adja meg a jelszót a kapcsolat van arról, hogy a **inkább alkalmazás**. 

Telefonos bejelentkezés nem használható Windows rendszerű számítógépek, XBOX vagy bármely Microsoft-alkalmazások, például az Office-alkalmazások asztali verzióinak feloldásához jelenleg.
 
### <a name="does-this-replace-two-step-verification-should-i-turn-it-off"></a>Vált, ez a kétlépéses ellenőrzés? Kell azt a kikapcsolása után?   

Egyes esetekben. Telefonos bejelentkezés hatókörének bővítésével dolgozunk, de egyelőre továbbra is vannak, amelyek nem támogatják a Microsoft-ökoszisztéma helyén. Ezen a helyen továbbra is használunk a kétlépéses ellenőrzés a biztonságos bejelentkezéshez. Éppen ezért nem, akkor ne kétlépéses ellenőrzés kikapcsolása a fiókjához.
 
### <a name="okay-if-i-keep-two-step-verification-turned-on-for-my-account-do-i-have-to-approve-two-notifications"></a>Oké Ha a kétlépéses ellenőrzés bekapcsolva a fiókomhoz megtartásához rendelkezem jóváhagyása két értesítések?

Nem, nem történik meg. Microsoft-fiókjába, a telefonos bejelentkezés számít a kétlépéses ellenőrzést. Helyett írja be a jelszót, majd hagyja jóvá az értesítést igazolja az identitását a telefon feloldásához ismerete, valamint majd hagyja jóvá az értesítést. A Microsoft nem küld egy második értesítés jóváhagyása.

### <a name="what-if-i-lose-my-phone-or-dont-have-it-with-me-how-can-i-access-my-account"></a>Mi történik, ha megszakad a telefonra, vagy még nincs velem megosztva, hogyan érhetem el a fiókomat?  

Mindig kattinthat **inkább jelszó** a bejelentkezési lapon váltson vissza az Ön jelszavát. Ne feledje, hogy ha használja a kétlépéses ellenőrzés, Önnek kell elvégeznie, ellenőrizze a bejelentkezést egy második módszer. Ezért erősen javasoljuk, hogy győződjön meg arról, hogy rendelkezik-e további, naprakész biztonsági adatokkal a fiókjában. A biztonsági adatait, kezelheti https://account.live.com/proofs/manage.
 
### <a name="how-do-i-stop-using-this-feature-and-go-back-to-entering-my-password"></a>Hogyan leállítása ezzel a funkcióval és térjen vissza a jelszó beírása?

Kattintson a **inkább jelszó** bejelentkezést követően. Hogy ne felejtse el a legújabb választási lehetőség, és kínálnak, amely alapértelmezés szerint amikor legközelebb bejelentkezik. Ha bármikor visszatérhet a telefonos bejelentkezés, kattintson a szeretné **inkább alkalmazás**. 
 
### <a name="can-i-use-the-app-to-sign-in-to-all-my-accounts-with-microsoft"></a>Az alkalmazás használatával jelentkezzen be az összes saját Microsoft-fiókhoz?   
Ez a funkció jelenleg csak akkor használható a személyes Microsoft-fiókok. 
 
### <a name="can-i-sign-into-my-pc-with-my-phone"></a>I be tud jelentkezni a számítógép a telefonomat?  
A számítógépen bejelentkezik egy Windows Hello a Windows 10-es használatát javasoljuk az arcfelismerés, ujjlenyomattal vagy a PIN-kódot.   
 
### <a name="can-i-sign-in-with-my-windows-phone"></a>E is be lehet jelentkezni a Windows Phone?  
Most hogy nem fejleszt ezt a funkciót a Microsoft Authenticator a Windows Phone. 

## <a name="next-steps"></a>További lépések
Ha még nem töltötte le a Microsoft Authenticator alkalmazást, próbálja ki. Az alkalmazás érhető el az [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), és a telefonos bejelentkezés érhető el a Microsoft Authenticator alkalmazást [Android](http://go.microsoft.com/fwlink/?Linkid=825072) és [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Ha kérdései vannak az alkalmazás általános, vessen egy pillantást a [a Microsoft Authenticator – gyakori kérdések](microsoft-authenticator-app-faq.md)
