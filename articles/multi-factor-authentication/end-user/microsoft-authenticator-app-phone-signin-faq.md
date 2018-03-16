---
title: "Bejelentkezés – phone Authenticator a Microsoft Azure és a Microsoft-fiókok |} Microsoft Docs"
description: "A telefon használatával jelentkezzen be Microsoft-fiókja helyett adja meg a jelszót. Ebben a cikkben megválaszolunk – gyakori kérdések a szolgáltatásról."
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 86cf6ba2f2ec86a609b4ec60cc143b07d9e542b3
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2018
---
# <a name="sign-in-with-your-phone-not-your-password"></a>Jelentkezzen be a telefon, nem a jelszót

A Microsoft Authenticator alkalmazás segít a fiókok biztonsága érdekében végezzen kétlépéses ellenőrzést, a jelszó megadása után. De tudja, hogy azt is cserélje le a személyes Microsoft-fiókjához tartozó jelszót teljesen?

Ez a szolgáltatás elérhető az iOS és Android-eszközök és személyes Microsoft-fiókkal rendelkező működik.

## <a name="how-it-works"></a>Működés

Sokan a Microsoft Authenticator alkalmazást a kétlépéses ellenőrzéshez bejelentkezéskor használja Microsoft-fiókjával. Írja be a jelszavát, majd nyissa meg az alkalmazás vagy egy értesítés jóváhagyása vagy ellenőrző kód kérése. A telefon bejelentkezhet hagyja ki a jelszót, és tegye meg a személyazonosság igazolását a telefonján. Telefonos bejelentkezés is egy kétlépéses ellenőrzést, akkor továbbra is meg kell adnia egy dolog ismernie és egy annyi teendője van, a személyazonosságát. A telefon még mindig a annyi teendője van, és a telefon PIN-kódot vagy biometrikus kulcs a ismerjük.

## <a name="how-to-get-started"></a>Első lépések

Jelentkezzen be személyes Microsoft-fiókkal és a telefon, kövesse az alábbi lépéseket:

1. Engedélyezze a telefon bejelentkezhet a fiókjához.

  - Ha nem rendelkezik a Microsoft Authenticator alkalmazást még, telepítése és a személyes Microsoft-fiókjával a lépések szerint adja hozzá a [Microsoft Authenticator lap](microsoft-authenticator-app-how-to.md). Újonnan hozzáadott fiókok automatikusan engedélyezve vannak, így Ön visszaigazolásához.

  - Ha már használja a Microsoft Authenticator a kétlépéses ellenőrzéshez, válassza ki az alkalmazás kezdőlapja a fiókot, és válassza ki **telefonos bejelentkezés engedélyezése** a legördülő menüből.

  >[!NOTE]
  >A fiók védelme érdekében kérjük egy PIN-kódot vagy biometrikus zárolást az eszközön. Ha a telefon zárolása feloldva, az alkalmazás jelenik meg, állítsa be a zárolási ahhoz, hogy a telefonos bejelentkezés kéri kérelem.

3. Legtöbb lap, ahol általában írja be a Microsoft-fiókja jelszavát rendelkezik egy hivatkozást, amely szerint **alkalmazást használhat a megnyitáshoz**. Kattintson erre a hivatkozásra a telefon bejelentkezni.

4. Microsoft értesítést küld a mobiltelefonjára. Jelentkezzen be a fiókjába az értesítés jóváhagyása.   

## <a name="faq"></a>GYIK

### <a name="how-is-signing-in-with-my-phone-more-secure-than-typing-a-password"></a>Hogyan van bejelentkezni a biztonságosabb, mint a jelszó beírása telefonomat?  

Ma a legtöbben bejelentkezés webhelyek vagy alkalmazások felhasználónévvel és jelszóval.  Sajnos jelszavak gyakran elvesztése, ellopása, vagy kitalálni a támadók által. Ha beállította a Microsoft Authenticator alkalmazást a bejelentkezéshez, azt a telefonján, amely oldhatja fel a fiók kulcs létrehozása. A Microsoft védeni, ezt a kulcsot, PIN vagy biometrikus már használható a telefonján.  A telefon jelentkezik, amikor ezt a kulcsot a személyazonosság biztonságosan a két tényező – a telefon, magát, és a képességére, a zárolás feloldásához használt. 

A használt kulcs hasonlít a Windows Hello- és a FIDO Alliance UAF specifikációk használt kulcsokat. Az adatok csak akkor életrajza helyileg, a kulcs védelmét, és soha nem küldött, vagy a felhőben tárolt. 
 
### <a name="where-can-i-use-my-phone-to-replace-my-password-and-where-would-i-still-need-the-password"></a>Hol használhatók a telefonszám cserélje le a jelszavam, és ahol volna meg kell-e a jelszó?  

Napjainkban a telefon bejelentkezési szolgáltatás csak működik együtt a webalkalmazások és szolgáltatások, amelyek személyes Microsoft-fiókok, iOS vagy Android-alkalmazásokat, amelyek személyes Microsoft-fiókkal, és egy személyes Microsoft-fiókot használó alkalmazások a Windows 10 szerint vannak kapcsolva. Amikor bejelentkezik a webhelyek vagy alkalmazások közül, ahol általában megadhatja a jelszó lapon kapcsolat van, amely szerint **alkalmazást használhat a megnyitáshoz**. 

Telefonos bejelentkezés nem használható Windows-számítógép, XBOX vagy bármely Microsoft-alkalmazások, például Office-alkalmazások asztali verzióinak jelenleg feloldásához.
 
### <a name="does-this-replace-two-step-verification-should-i-turn-it-off"></a>Vált a kétlépéses ellenőrzést? Kell kikapcsolni a azt?   

Egyes esetekben. Folyamatosan bővítjük telefonos bejelentkezés hatóköre bővíteni, de most még vannak, amelyek nem támogatják a Microsoft-ökoszisztéma helyen. Ezen a helyen továbbra is használjuk kétlépéses ellenőrzés a biztonságos bejelentkezés. Emiatt nem, akkor ne kétlépéses ellenőrzés kikapcsolása a fiókjához.
 
### <a name="okay-if-i-keep-two-step-verification-turned-on-for-my-account-do-i-have-to-approve-two-notifications"></a>Rendben Ha a fiók-e kapcsolva a kétlépéses ellenőrzést megtartásához van két értesítés jóváhagyása?

Nem, nem. Bejelentkezés Microsoft-fiókkal és a telefon számít a kétlépéses ellenőrzést. Helyett írja be a jelszavát, majd egy értesítés jóváhagyása, a személyazonosság igazolását a telefon feloldásához ismerete, és ezután jóváhagyhatja az értesítést. Nem fognak kapni egy második értesítés jóváhagyása.

### <a name="what-if-i-lose-my-phone-or-dont-have-it-with-me-how-can-i-access-my-account"></a>Mi történik, ha elveszti a telefonszám, vagy nincs velem, hogyan lehet érhető-fiókom?  

Mindig kattinthat **használja helyette a jelszó** a bejelentkezési oldalon való visszatéréshez az Ön jelszavát. Ne feledje, hogy használatakor a kétlépéses ellenőrzést, továbbra is szüksége van egy második módszer a bejelentkezés ellenőrzése. Ezért azt javasoljuk, hogy győződjön meg arról, hogy rendelkezik-e további, naprakész biztonsági adatok fiókja. Kezelheti a biztonsági adatait https://account.live.com/proofs/manage.
 
### <a name="how-do-i-stop-using-this-feature-and-go-back-to-entering-my-password"></a>Hogyan állítsa le, ezzel a szolgáltatással és térjen vissza a jelszó beírása?

Kattintson a **használja helyette a jelszó** bejelentkezést követően. Azt a legutóbbi választott megjegyezhető, és kínálnak, amely alapértelmezés szerint a következő bejelentkezéskor. Ha szeretne bejelentkezni a telefonon lépjen vissza, kattintson a **alkalmazást használhat a megnyitáshoz**. 
 
### <a name="can-i-use-the-app-to-sign-in-to-all-my-accounts-with-microsoft"></a>Az alkalmazás használatával jelentkezzen be a Microsoft saját partnerek?   
Ez a funkció jelenleg csak személyes Microsoft fiókokhoz érhető el. 
 
### <a name="can-i-sign-into-my-pc-with-my-phone"></a>Képes vagyok jelentkezzen be a számítógép a telefon?  
A számítógép bejelentkezni a Windows Hello Windows 10 használatát javasoljuk a oldallal, ujjlenyomat vagy PIN-kódot.   
 
### <a name="can-i-sign-in-with-my-windows-phone"></a>I jelentkezhet be a Windows Phone?  
Most azt nem fejleszt ezt a funkciót az a Microsoft Authenticator a Windows Phone. 

## <a name="next-steps"></a>További lépések
Ha még nem töltötte le a Microsoft Authenticator alkalmazást, tekintse meg. Az alkalmazás érhető el [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), és a telefonos bejelentkezés érhető el a Microsoft Authenticator alkalmazást [Android](http://go.microsoft.com/fwlink/?Linkid=825072) és [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Ha kérdései az alkalmazással kapcsolatos általános, vessen egy pillantást a [Microsoft hitelesítő – gyakori kérdések](microsoft-authenticator-app-faq.md)
