---
title: Mi a biztonságos identitás-pontszám? – Az azure Active Directory (nyilvános előzetes verzió) |} A Microsoft Docs
description: Leírja, hogyan használhatja az identitáskezelési biztonságos pontszám javítása az Azure AD-bérlő biztonsági állapotát.
services: active-directory
keywords: identitásbiztonsági pontszám, Azure AD, biztonságos hozzáférés vállalati erőforrásokhoz
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: fundamentals
ms.topic: overview
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2018
ms.author: markvi
ms.reviewer: nigu
ms.custom: it-pro
ms.openlocfilehash: 5a133d0a38852ca3e95e3ae07716c4fbd3c87f5d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53092083"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory-public-preview"></a>Mi az az identitás biztonságos pontszám, az Azure Active Directoryban? (Nyilvános előzetes verzió)

Mennyire biztonságos az Ön Azure AD-bérlője? Ha nem tudja a választ erre a kérdésre, a cikket elolvasva megismerheti, hogyan segítheti az identitásbiztonsági pontszám az identitásvédelem felügyeletében és javításában. 

## <a name="what-is-an-identity-secure-score"></a>Mi az az identitásbiztonsági pontszám?

Az identitásbiztonsági pontszám egy 1 és 248 közötti szám, amely azt tükrözi, biztonsági megoldása mennyire felel meg a Microsoft ajánlott eljárásainak.


![Biztonsági pontszám](./media/identity-secure-score/01.png)



A pontszám az alábbiakban nyújt segítséget:

- Az identitásbiztonsági rendszer objektív felmérése

- Identitásbiztonsági fejlesztések tervezése

- A fejlesztések hatásának felmérése 


A pontszámot és a kapcsolódó információkat az identitásbiztonsági pontszám irányítópultján találhatja meg. Ezen az irányítópulton a következők találhatók:

- Az Ön pontszáma

    ![Biztonsági pontszám](./media/identity-secure-score/02.png)

- Egy összehasonlító grafikon

    ![Biztonsági pontszám](./media/identity-secure-score/03.png)

- Egy trendgrafikon

    ![Biztonsági pontszám](./media/identity-secure-score/04.png)

- Ajánlott identitásbiztonsági eljárások listája. 

    ![Biztonsági pontszám](./media/identity-secure-score/05.png)


A fejlesztési műveletek végrehajtásával a következőket érheti el:

- A biztonsági rendszer és a pontszám javítása.
 
- A Microsoft identitáskezelési funkcióinak jobb kihasználása. 



## <a name="how-do-i-get-my-secure-score"></a>Hogyan tekinthetem meg a saját biztonsági pontszámomat?

Az identitásbiztonsági pontszám az Azure AD összes kiadásában elérhető.

Pontszámát az [Azure AD áttekintési irányítópultján](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore) érheti el.



## <a name="how-does-it-work"></a>Hogyan működik?

Az Azure 48 óránként áttekinti az Ön biztonsági beállításait, és összehasonlítja azokat az ajánlott eljárásokkal. A kiértékelés eredményétől függően a bérlőhöz a rendszer új pontszámot rendel. Ennek értelmében ha Ön módosítja a biztonsági beállításait, akár 48 órába is telhet, mire ezt a pontszáma tükrözni fogja. 

A rendszer az Ön Azure AD-konfigurációjától függően tesz javaslatokat. Ha külső termékek használatával tesz eleget egy ajánlott eljárásnak, azt az adott fejlesztési művelet beállításaiban jelezheti.

![Biztonsági pontszám](./media/identity-secure-score/07.png)


Emellett lehetősége van arra is, hogy egyes javaslatokat figyelmen kívül hagyjon, ha azok nem érvényesek az Ön által használt környezetre. A figyelmen kívül hagyott javaslatok nincsenek hatással a pontszám kiszámítására. 
 
![Biztonsági pontszám](./media/identity-secure-score/06.png)



## <a name="how-does-it-help-me"></a>Miben nyújt segítséget?

A biztonsági pontszám az alábbiakban nyújt segítséget:

- Az identitásbiztonsági rendszer objektív felmérése

- Identitásbiztonsági fejlesztések tervezése

- A fejlesztések hatásának felmérése



## <a name="what-you-should-know"></a>Alapismeretek

### <a name="who-can-use-the-identity-secure-score"></a>Kik használhatják az identitásbiztonság pontszámot?

Az identitásbiztonság pontszámot a következő szerepkörök használhatják:

- Globális rendszergazda
- Biztonsági rendszergazda 
- Biztonsági olvasók 

### <a name="what-does-not-scored-mean"></a>Mit jelent a [Not Scored]?

A [Not Scored] jelzéssel ellátott műveletek olyan műveletek, amelyeket elvégezhet a vállalatában, de a pontszám kiszámításakor a rendszer nem veszi őket figyelembe, mert (még!) nincsenek csatolva az eszközhöz. Így is javíthat a biztonsági beállításain, azonban a rendszer ilyen esetekben egyelőre nem veszi figyelembe ezeket a műveleteket.

### <a name="how-often-is-my-score-updated"></a>Milyen gyakran frissül a pontszám?

A pontszámot a rendszer naponta számítja újra (PST idő szerint kb. 01:00-kor). Ha változtatást eszközöl valamelyik mért műveleten, a pontszám a következő napon automatikusan frissül. Ahhoz, hogy egy változás megjelenjen a pontszámban is, akár 48 órának is el kell telnie.


### <a name="my-score-changed-how-do-i-figure-out-why"></a>Megváltozott a pontszámom. Hogyan tudhatom meg, mi ennek az oka?

A [biztonsági pontszám portáljának](https://securescore.microsoft.com/#!/score) pontszámelemzési oldalán kattintson egy adott nap adatpontjára, és görgessen lejjebb az adott nap befejezett és befejezetlen műveletei között, ahol megtekintheti, mi változott.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Méri-e biztonsági pontszám az illetéktelen behatolások kockázatát?

Röviden: nem. A biztonsági pontszám nem egy átfogó mérőszám, amely az illetéktelen behatolások kockázatát fejezi ki. Ez a számérték inkább azt mutatja, milyen mértékben tett lépéseket azért, hogy az illetéktelen behatolást megakadályozó funkciókat bevezessen. Semmilyen szolgáltatás nem garantálja, hogy a rendszerébe nem tudnak behatolni, és a biztonsági pontszám semmilyen módon nem tekinthető ilyen garanciának.

### <a name="how-should-i-interpret-my-score"></a>Hogyan értelmezendő a pontszám?

Akkor kap pontokat, ha a javasolt módon konfigurálja a biztonsági funkciókat, vagy a biztonsághoz kötődő műveleteket végez (például jelentéseket olvas). Egyes műveletek részleges végrehajtása is pontot ér – ilyen például a többtényezős hitelesítés (MFA) engedélyezése a felhasználók számára. A biztonsági pontszám közvetlenül azt mutatja meg, hogy a Microsoft mely biztonsági szolgáltatásait használja. Ne feledje, hogy a biztonságnak egyensúlyban kell lennie a használhatósággal. Minden biztonsági vezérlő hatással van a felhasználókra is. A kis felhasználói hatású vezérlők elenyésző hatással vannak a felhasználók mindennapi műveleteire.

Pontszáma előzményeit a [biztonsági pontszám portáljának](https://securescore.microsoft.com/#!/score) pontszámelemzési oldalán tekintheti meg. Egy adott dátumot kiválasztva megtekintheti, mely vezérlők voltak engedélyezve az adott napon, és hány pontot kapott ezekre.


### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Hogyan kapcsolódik az identitásbiztonsági pontszám az Office 365 biztonsági pontszámhoz? 

Az [Office 365 biztonsági pontszám](https://docs.microsoft.com/office365/securitycompliance/office-365-secure-score) hamarosan öt különböző pontszám összesítésébe lesz migrálva:

- Identitás

- Adatok

- Eszközök

- Infrastruktúra

- Alkalmazások

Az identitásbiztonsági pontszám az Office 365 biztonsági pontszám identitásra vonatkozó részét jelöli. Ez azt jelenti, hogy az identitásbiztonsági pontszámra és az Office 365 biztonsági pontszámra vonatkozó javaslatok megegyeznek. 


## <a name="next-steps"></a>További lépések

Az Office 365 biztonsági pontszámmal kapcsolatos videó megtekintéséhez kattintson [ide](https://www.youtube.com/watch?v=jzfpDJ9Kg-A).
 
