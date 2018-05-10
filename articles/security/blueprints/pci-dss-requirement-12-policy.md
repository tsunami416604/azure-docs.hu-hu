---
title: Az Azure támogatási feldolgozási tervezetének - házirenddel kapcsolatos követelmények
description: PCI DSS követelmény 12
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: a79d59d8-20e3-4efe-8686-c8f4ed80e220
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 2fb238e9b95180d6156159c87ec008a71943e698
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="policy-requirements-for-pci-dss-compliant-environments"></a>Házirend követelményeinek PCI DSS-kompatibilis környezetben  
## <a name="pci-dss-requirement-12"></a>PCI DSS követelmény 12

**Egy házirendet, amely információ-biztonság személyek karbantartása**

> [!NOTE]
> Ezeket a követelményeket határozzák meg a [Payment Card Industry (PCI) biztonsági szabványok Tanács](https://www.pcisecuritystandards.org/pci_security/) részeként a [PCI adatok biztonsági szabvány (DSS) 3.2-es verziójú](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Tekintse meg a PCI DSS tesztelési eljárások és az egyes követelményekhez útmutatót olvashat.

Egy erős biztonsági szabályzatot állítja be a biztonsági hangjelzés után a teljes entitás és személyzet arról tájékoztatja őket a várt. Az adatok és a védelem feladataik érzékenységi érdemes figyelembe személyzetnek. Követelmény 12 céljából az "személyzet" teljes és Részmunkaidős alkalmazottak, ideiglenes alkalmazottai, alvállalkozói és tanácsadóknak szól, akik "rezidens" a szervezet által a helyszínen vagy a kártya tulajdonosát adatok környezet egyéb hozzáférése vonatkozik.

## <a name="pci-dss-requirement-121"></a>PCI DSS követelmény 12.1

**12.1** jöjjön létre, közzétételéhez, kezelése és terjesztése egy biztonsági házirend.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége létrehozásáért és fenntartásáért az információk biztonsági házirend.|



### <a name="pci-dss-requirement-1211"></a>PCI DSS követelmény 12.1.1-es verziójú

**12.1.1-es verziójú** a biztonsági házirend legalább évente házirend ellenőrzése és frissítése a a környezet változásakor.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége, legalább évente frissítése az információk biztonsági házirendet, vagy ha a kártya tulajdonosát adatok környezet (CDE) módosult.|



## <a name="pci-dss-requirement-122"></a>PCI DSS követelmény 12.2

**12.2** megvalósítása a kockázatbecslés feldolgozni, amelyek:
- Legalább évente, és a környezet (például beszerzési egyesülés, áthelyezés, stb.) jelentős módosításai után történik
- A kritikus eszközöket, veszélyek és biztonsági rések azonosítása
- A kockázat formális, dokumentált analysis eredményez.
- > Kockázatfelmérési módszereit, amelyek példái közé tartoznak, azonban nem csak OKTÁV, ISO 27005 és NIST SP 800-30.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége, amely követelmény 12.2 szereplő összes fenyegetést kockázat értékelési folyamat megvalósítása.|



## <a name="pci-dss-requirement-123"></a>PCI DSS követelmény 12.3

**12.3** kritikus technológiák használati házirendek kifejlesztésére, és adja meg ezek a technológiák megfelelő használatát.

> [!NOTE]
> Példák kritikus technológiák közé tartozik, de nem kizárólag távoli hozzáférési és vezeték nélküli technológiák, laptopok, táblagépek, elektronikus cserélhető adathordozóra, e-mail használati és Internet használati.
Győződjön meg arról, a használati házirendek a következők szükségesek.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége létrehozására és karbantartására Diktálás megfelelő használati, megvalósítása és a kritikus technológiák belül a CDE hitelesítési házirendek.|



### <a name="pci-dss-requirement-1231"></a>PCI DSS követelmény 12.3.1

**12.3.1** jogosult felek explicit jóváhagyás

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége létrehozására és karbantartására Diktálás megfelelő használati, megvalósítása és a kritikus technológiák belül a CDE hitelesítési házirendek.|



### <a name="pci-dss-requirement-1232"></a>PCI DSS követelmény 12.3.2

**12.3.2** hitelesítés használható a technológia

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége létrehozására és karbantartására Diktálás megfelelő használati, megvalósítása és a kritikus technológiák belül a CDE hitelesítési házirendek.|



### <a name="pci-dss-requirement-1233"></a>PCI DSS követelmény 12.3.3-as verziójú

**12.3.3-as verziójú** ilyen eszközöket és a csoporthoz hozzáféréssel rendelkező listája

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége létrehozására és karbantartására Diktálás megfelelő használati, megvalósítása és a kritikus technológiák belül a CDE hitelesítési házirendek.|



### <a name="pci-dss-requirement-1234"></a>PCI DSS követelmény 12.3.4

**12.3.4** pontosan és könnyen határozza meg a tulajdonosa, a kapcsolattartási adatokat és a cél (például címkézés, kódolása és/vagy az eszközök a felvétele a leltárba) módszer

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége létrehozására és karbantartására Diktálás megfelelő használati, megvalósítása és a kritikus technológiák belül a CDE hitelesítési házirendek.|



### <a name="pci-dss-requirement-1235"></a>PCI DSS követelmény 12.3.5

**12.3.5** használati használja a technológia

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége létrehozására és karbantartására Diktálás megfelelő használati, megvalósítása és a kritikus technológiák belül a CDE hitelesítési házirendek.|



### <a name="pci-dss-requirement-1236"></a>PCI DSS követelmény 12.3.6

**12.3.6** elfogadható hálózati helyeket a technológiák

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége felhőalapú virtuális gépek, tárolás és a támogató szolgáltatások elfogadható hálózati helyek meghatározásához.|



### <a name="pci-dss-requirement-1237"></a>PCI DSS követelmény 12.3.7

**12.3.7** vállalat által jóváhagyott szoftverek listájának

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége felhőalapú virtuális gépek, tárolás és a támogató szolgáltatások elfogadható hálózati helyek meghatározásához.|



### <a name="pci-dss-requirement-1238"></a>PCI DSS követelmény 12.3.8

**12.3.8** automatikus bontja a kapcsolatot a távelérés-technológiákhoz munkamenetek egy meghatározott tétlen időszak után

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure a Microsoft vállalati AD munkamenet lock funkciót, amely érvényesíti a munkamenet zárolási elemek adott ideig tartó tétlenség után használja. Hálózati kapcsolatok leállítása 30 perc inaktivitás után van. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége létrehozására és karbantartására Diktálás megfelelő használati, megvalósítása és a kritikus technológiák belül a CDE hitelesítési házirendek.|



### <a name="pci-dss-requirement-1239"></a>PCI DSS követelmény 12.3.9

**12.3.9** szállítók és az üzleti partnerek csak akkor, ha a igényli a szállítók és az üzleti partnerekkel, azonnali inaktiválás után használja a távelérés-technológiák aktiválás

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége létrehozására és karbantartására Diktálás megfelelő használati, megvalósítása és a kritikus technológiák belül a CDE hitelesítési házirendek.|



### <a name="pci-dss-requirement-12310"></a>PCI DSS követelmény 12.3.10

**12.3.10** a távelérés-technológiák keresztül kártya tulajdonosát adatokhoz hozzáférő személyzet letiltása másolása, áthelyezés és kártya tulajdonosát adatokat a helyi merevlemez-meghajtók és elektronikus cserélhető adathordozóra, kivéve explicit módon definiált engedélyezett üzleti igényeknek.
Ha egy engedélyezett üzleti igény, a használati házirendek követelheti meg az adatok védendő vonatkozó PCI DSS követelményeknek megfelelően.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Ügyfelek felelőssége annak biztosítása, hogy személyzet kártya tulajdonosát adatait távelérés-technológiák keresztül érik el tilos a másolása, áthelyezés és kártya tulajdonosát adattárolás a helyi merevlemez-meghajtók és elektronikus cserélhető adathordozóra, kivéve, ha kifejezetten engedélyezi egy adott üzleti igényeknek.|



## <a name="pci-dss-requirement-124"></a>PCI DSS követelmény 12.4

**12.4** győződjön meg arról, hogy a biztonsági házirendet, és eljárások egyértelmű meghatározása a információk biztonsági felelő személyzetnek.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége létrehozására és karbantartására Diktálás megfelelő használati, megvalósítása és a kritikus technológiák belül a CDE hitelesítési házirendek.|



### <a name="pci-dss-requirement-1241"></a>PCI DSS követelmény 12.4.1

**12.4.1** szolgáltatók csak további követelményei: végrehajtó felügyeleti hoz létre a kártya tulajdonosát adatok védelméről és PCI DSS megfelelőségi program felvenni felelős:
- Általános accountability PCI DSS megfelelőségi karbantartásához
- A PCI DSS megfelelőségi program és a kommunikációs okleveles definiálásával vezetőségi 

> [!NOTE]
> Ez a követelmény a legjobb 2018 január 31-ig elteltével követelmény válik.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek, akik a szolgáltatók a PCI megfelelőségi program dokumentálásáért felelősek.|



## <a name="pci-dss-requirement-125"></a>PCI DSS követelmény 12,5

**12,5** legyen egy természetes személyhez, vagy a továbbiakban összevonhatja a következő információk biztonsági felügyeleti feladataikat.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége meghatározása, és rendelje hozzá a információk biztonsági feladataikat az alkalmazottak számára.|



### <a name="pci-dss-requirement-1251"></a>PCI DSS követelmény 12.5.1

**12.5.1** jöjjön létre, és dokumentálása, biztonsági házirendeket és eljárásokat.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége meghatározása, és rendelje hozzá a információk biztonsági feladataikat az alkalmazottak számára.|



### <a name="pci-dss-requirement-1252"></a>PCI DSS követelmény 12.5.2

**12.5.2** figyelő és a biztonsági figyelmeztetések és információk elemzése és ossza ki a megfelelő csoporthoz.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége meghatározása, és rendelje hozzá a információk biztonsági feladataikat az alkalmazottak számára.|



### <a name="pci-dss-requirement-1253"></a>PCI DSS követelmény 12.5.3

**12.5.3** jöjjön létre, és dokumentálása, terjeszteni a biztonsági incidens válasz és eszkalációs eljárások időben és hatékony kezelésére vonatkozó összes helyzetek biztosításához.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége létrehozására és karbantartására Diktálás megfelelő használati, megvalósítása és a kritikus technológiák belül a CDE hitelesítési házirendek.|



### <a name="pci-dss-requirement-1254"></a>PCI DSS követelmény 12.5.4

**12.5.4** felügyelheti a felhasználói fiókok, beleértve az új, törléseket és módosításokat.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége létrehozására és karbantartására Diktálás megfelelő használati, megvalósítása és a kritikus technológiák belül a CDE hitelesítési házirendek.|



### <a name="pci-dss-requirement-1255"></a>PCI DSS követelmény 12.5.5

**12.5.5** megfigyelés és vezérlés összes adatokhoz való hozzáférés.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelőssége létrehozására és karbantartására Diktálás megfelelő használati, megvalósítása és a kritikus technológiák belül a CDE hitelesítési házirendek.|



## <a name="pci-dss-requirement-126"></a>PCI DSS követelmény 12,6

**12,6** megfelelő formális biztonsági tájékoztatási programot személyzetnek fontosságára kártya tulajdonosát adatok biztonsági házirend és eljárások végrehajtására.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Ügyfelek felelőssége létrehozása és a biztonsági kiszolgáló és a CDE hozzáféréssel rendelkező munkatársak körülvevő házirendek karbantartására.|



### <a name="pci-dss-requirement-1261"></a>PCI DSS követelmény 12.6.1

**12.6.1** ismertetni kell a felvételi és legalább évente csoporthoz. 

> [!NOTE]
> Módszerek eltérőek lehetnek attól függően, hogy a személyzet szerepkör és a kártya tulajdonosát adatok szintjét.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Ügyfelek felelőssége annak biztosítása az alkalmazottak valamint információ-biztonság és PCI DSS tájékoztatási betanítása legalább évente.|



### <a name="pci-dss-requirement-1262"></a>PCI DSS követelmény 12.6.2

**12.6.2** csoporthoz, és legalább évente megerősíti, hogy azok Elolvastam és megértettem a biztonsági házirendet, és eljárások igényelnek.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Ügyfelek felelőssége annak biztosítása az alkalmazottak valamint információ-biztonság és PCI DSS tájékoztatási betanítása legalább évente.|



## <a name="pci-dss-requirement-127"></a>PCI DSS követelmény 12.7

**12.7** képernyő lehetséges személyzet előtti belső forrásokból támadások kockázatának minimalizálása érdekében. (Átvilágítást például előző alkalmazási előzmények, a büntetőjogi rekord, a jóváírás előzmények és a hivatkozás ellenőrzi.) 

> [!NOTE]
> Ezek lehetséges csoporthoz, és bizonyos adatokat főként pozíciók például a tároló pénztárosok hozzáférő csak egy száma egyszerre egy tranzakció elősegítése, ezt a követelményt esetén csak egy javaslatot.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Ügyfelek felelősek hozzáféréssel rendelkező munkatársak számára a CDE változni alapos átvilágítást.|



## <a name="pci-dss-requirement-128"></a>PCI DSS követelmény 12.8

**12.8** karbantartása és alkalmazzon házirendet és eljárásokat azzal, akikkel kártya tulajdonosát adatok meg vannak osztva, vagy, amely hatással lehet az adatok biztonsága érdekében kártya tulajdonosát, az alábbiak szerint szolgáltatók kezelése.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelősek PCI-megfelelőséget a szolgáltatók, akikkel kártya tulajdonosát adatok megosztott vagy befolyásolhatják a CDE biztonságát. Az ügyfelek kezelnie kell az összes szolgáltatás listáját biztosít a CDE történik.|



### <a name="pci-dss-requirement-1281"></a>PCI DSS követelmény 12.8.1

**12.8.1** vezet listát az egyes szolgáltatók, beleértve a megadott szolgáltatás leírását.


**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek felelősek PCI-megfelelőséget a szolgáltatók, akikkel kártya tulajdonosát adatok megosztott vagy befolyásolhatják a CDE biztonságát. Az ügyfelek kezelnie kell az összes szolgáltatás listáját biztosít a CDE történik.|



### <a name="pci-dss-requirement-1282"></a>PCI DSS követelmény 12.8.2

**12.8.2** karbantartása, amely tartalmazza az, hogy a szolgáltatók felelősek az adatok biztonsága érdekében kártya tulajdonosát a szolgáltatók rendelkeznek vagy más módon tárolja, feldolgozni vagy továbbítani az ügyfél nevében nyugtázást írásbeli szerződés vagy az azok jelentős hatással lehet az ügyfél kártya tulajdonosát adatok környezet biztonságát. 

> [!NOTE]
> A pontos szövege nyugtázást függ a két fél, biztosítja, hogy a szolgáltatás a részleteit, és a feladatainak felek között. A nyugtázási nem kell a pontos szövege megtalálható ez a követelmény tartalmazza.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Ügyfelek felelőssége írásbeli szerződés fenntartása a kártya tulajdonosát adatok biztonságának fenntartása felelősséget igazolása szolgáltatókkal.|



### <a name="pci-dss-requirement-1283"></a>PCI DSS követelmény 12.8.3

**12.8.3** gondoskodjon arról, hogy egy meglévő folyamat folytatásához, beleértve a megfelelő gondossággal előtt lejáró engagement szolgáltatók.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Ügyfelek felelőssége annak biztosítása, egy meghatározott folyamat folytatásához, beleértve a megfelelő gondossággal előtt lejáró engagement szolgáltatók van.|



### <a name="pci-dss-requirement-1284"></a>PCI DSS követelmény 12.8.4

**12.8.4** szolgáltatók PCI DSS megfelelőségi állapot figyelése évente legalább egy program karbantartása.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek szolgáltatók PCI DSS megfelelőségi állapot figyelése évente legalább egy program karbantartásáért felelős.|



### <a name="pci-dss-requirement-1285"></a>PCI DSS követelmény 12.8.5

**12.8.5** karbantartása mely PCI DSS követelmények minden szolgáltató kezeli, és az entitás kezeli.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Ügyfelek felelőssége másolatának megőrzése a [felelős összegzés mátrix](https://aka.ms/pciblueprintcrm32), amely ismerteti, hogy az ügyfél és a Microsoft Azure felelőssége felelőssége PCI DSS követelmények.|



## <a name="pci-dss-requirement-129"></a>PCI DSS követelmény 12.9

**12.9** szolgáltatók csak további követelményei: szolgáltatók megerősíti, hogy az adatok biztonsága érdekében kártya tulajdonosát a szolgáltató rendelkezik, vagy más módon tárolja a folyamatok, felelős az ügyfelek írásban vagy az ügyfél vagy a mértékben nevében továbbítja az, hogy azok jelentős hatással lehet az ügyfél kártya tulajdonosát adatok környezet biztonsági. 

> [!NOTE]
> A pontos szövege nyugtázást függ a két fél, biztosítja, hogy a szolgáltatás a részleteit, és a feladatainak felek között. A nyugtázási nem kell a pontos szövege megtalálható ez a követelmény tartalmazza.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Ügyfelek, amelyek szolgáltatók megőrzéséhez a PCI-megfelelőséget feladataik igazolása felelősek. |



## <a name="pci-dss-requirement-1210"></a>PCI DSS követelmény 12.10

**12.10** valósítja meg az incidensválasz-csomag. Készüljön azonnal reagálni a rendszer biztonsági szabályok megsértésére.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Ügyfelei felelősek az infravörös tervek és tesztelését, amelyek figyelembe veszi a megosztott touch pontok és az Azure infrastruktúrája kihasználva vevő alkalmazások vevői vezérlőket. Feladata az ügyfél pontos kapcsolattartási adatok megadása az Azure-ba, abban az esetben, ha egy incidenst kell jelenteni kell őket, ami hatással lehet az alkalmazás vagy adatokat.|



### <a name="pci-dss-requirement-12101"></a>PCI DSS követelmény 12.10.1

**12.10.1** rendszer szerződésszegés esetén végrehajtandó incidensválasz-csomag létrehozása. Ellenőrizze, hogy a csomag címeit, legalább a következő:
- Szerepkörök, a felelősségeket és a kommunikáció és az ügyfél stratégiák többek között a fizetési márka minimális értesítési sérülés
- Bizonyos incidensválasz-eljárások
- Üzleti helyreállítási és folytonossági eljárások
- Adatok biztonsági mentési folyamatokat
- A biztonság sérüléseinek jelentési jogi követelmények elemzése
- Lefedettsége és válaszokat az összes kritikus rendszer-összetevő
- Hivatkozás és incidensmegoldásra vonatkozó eljárások a fizetési márka a tartalmazási

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Ügyfelei felelősek az infravörös tervek és tesztelését, amelyek figyelembe veszi a megosztott touch pontok és az Azure infrastruktúrája kihasználva vevő alkalmazások vevői vezérlőket. Feladata az ügyfél pontos kapcsolattartási adatok megadása az Azure-ba, abban az esetben, ha egy incidenst kell jelenteni kell őket, ami hatással lehet az alkalmazás vagy adatokat.|



### <a name="pci-dss-requirement-12102"></a>PCI DSS követelmény 12.10.2

**12.10.2** tekintse át és tesztelje a terv, követelmény 12.10.1, legalább évente szereplő valamennyi eleme.


**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Ügyfelei felelősek az infravörös tervek és tesztelését, amelyek figyelembe veszi a megosztott touch pontok és az Azure infrastruktúrája kihasználva vevő alkalmazások vevői vezérlőket. Feladata az ügyfél pontos kapcsolattartási adatok megadása az Azure-ba, abban az esetben, ha egy incidenst kell jelenteni kell őket, ami hatással lehet az alkalmazás vagy adatokat.|



### <a name="pci-dss-requirement-12103"></a>PCI DSS követelmény 12.10.3

**12.10.3** adott csoporthoz, és elérhető válaszadás a riasztásokra való 24/7 alapon kell kijelölni.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Ügyfelei felelősek az infravörös tervek és tesztelését, amelyek figyelembe veszi a megosztott touch pontok és az Azure infrastruktúrája kihasználva vevő alkalmazások vevői vezérlőket. Feladata az ügyfél pontos kapcsolattartási adatok megadása az Azure-ba, abban az esetben, ha egy incidenst kell jelenteni kell őket, ami hatással lehet az alkalmazás vagy adatokat.|



### <a name="pci-dss-requirement-12104"></a>PCI DSS követelmény 12.10.4

**12.10.4** képzést megfelelő személynek a biztonság megsértésére válasz feladatkörök.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Ügyfelei felelősek az infravörös tervek és tesztelését, amelyek figyelembe veszi a megosztott touch pontok és az Azure infrastruktúrája kihasználva vevő alkalmazások vevői vezérlőket. Feladata az ügyfél pontos kapcsolattartási adatok megadása az Azure-ba, abban az esetben, ha egy incidenst kell jelenteni kell őket, ami hatással lehet az alkalmazás vagy adatokat.|



### <a name="pci-dss-requirement-12105"></a>PCI DSS követelmény 12.10.5

**12.10.5** figyelését, beleértve többek között a behatolás-észlelés, behatolás-megelőzési, tűzfalak, és a fájl-integritási figyelési rendszerek biztonsági riasztások tartalmazzák.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Ügyfelei felelősek az infravörös tervek és tesztelését, amelyek figyelembe veszi a megosztott touch pontok és az Azure infrastruktúrája kihasználva vevő alkalmazások vevői vezérlőket. Feladata az ügyfél pontos kapcsolattartási adatok megadása az Azure-ba, abban az esetben, ha egy incidenst kell jelenteni kell őket, ami hatással lehet az alkalmazás vagy adatokat.|



### <a name="pci-dss-requirement-12106"></a>PCI DSS követelmény 12.10.6

**12.10.6** egy folyamatot, módosításához és a incidensválasz-terv szerzett tapasztalatok alapján fejlődnek és iparági fejlesztésekkel átfogó fejlesztéséhez.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Ügyfelei felelősek az infravörös tervek és tesztelését, amelyek figyelembe veszi a megosztott touch pontok és az Azure infrastruktúrája kihasználva vevő alkalmazások vevői vezérlőket. Feladata az ügyfél pontos kapcsolattartási adatok megadása az Azure-ba, abban az esetben, ha egy incidenst kell jelenteni kell őket, ami hatással lehet az alkalmazás vagy adatokat.|



## <a name="pci-dss-requirement-1211"></a>PCI DSS követelmény 12.11

**12.11** **szolgáltatók csak további követelményei:** hajtsa végre a személyzet biztonsági házirendek és üzemeltetési eljárások következő megerősítéséhez legalább negyedéves értékelést.
Értékelést le kell fednie a következő eljárásokat:
- Ellenőrzi, hogy napi napló
- Tűzfal-szabálykészlet értékelést
- Konfigurációs szabványok alkalmazása az új rendszerek
- Biztonsági riasztásokra való reagálásról
- Módosítsa a felügyeleti folyamatok 

> [!NOTE]
> Ez a követelmény a legjobb 2018 január 31-ig elteltével követelmény válik.


**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek, akik szolgáltatók felelősek az értékelést általában PCI megfelelőségi ellenőrzési teljesítményének folyamatok dokumentálása.|



### <a name="pci-dss-requirement-12111"></a>PCI DSS követelmény 12.11.1

**12.11.1** szolgáltatók csak további követelményei: karbantartása negyedéves felülvizsgálati folyamat dokumentációját:
- Az ellenőrzések eredményeit dokumentálása
- Tekintse át és kijelentkezési a PCI DSS megfelelőségi program személyzet felelős az eredmények 

> [!NOTE]
> Ez a követelmény a legjobb 2018 január 31-ig elteltével követelmény válik.


**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | Az ügyfelek, akik szolgáltatók felelősek az értékelést általában PCI megfelelőségi ellenőrzési teljesítményének folyamatok dokumentálása.|




