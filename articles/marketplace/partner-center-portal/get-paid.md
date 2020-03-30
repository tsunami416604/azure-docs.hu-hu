---
title: Első fizetett kereskedelmi piacon | Azure Piactér
description: További információ arról, hogyan kap kifizetéseket a bevételeiért a kereskedelmi piacon.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: 3c75d5ccdd2d632fbc31d1be3faf6cbc6d3fb223
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275832"
---
# <a name="getting-paid"></a>Fizetés fogadása
Íme néhány fontos információ az ajánlatok, bővítmények és hirdetési bevételek kifizetéséről.

> [!IMPORTANT]
> Mielőtt pénzt kapna az ajánlati értékesítésből a kereskedelmi piacon, be kell [állítania a kifizetési számláját, és ki kell töltenie a szükséges adózási űrlapokat.](set-up-your-payout-account-tax-forms.md)

## <a name="store-fee"></a>Bolti díj

Amikor [regisztrál egy fejlesztői fiókra,](https://go.microsoft.com/fwlink/p/?LinkID=615100)elfogadja a [Microsoft Azure Piactér kiadói szerződését.](https://go.microsoft.com/fwlink/p/?LinkID=699560) Ez a szerződés ismerteti az Ön és a Microsoft közötti kapcsolatot, mivel az a kereskedelmi piacon történő értékesítési ajánlatra vonatkozik, beleértve azt az Áruházi díjat is, amelyet a Microsoft minden egyes értékesítésért felszámít.

A díjakat hivatalosan a [Microsoft Azure Piactér kiadói szerződése](https://go.microsoft.com/fwlink/p/?LinkID=699560)határozza meg. Mindig tekintse át a dokumentumot, ha bármilyen kérdése van.

Az Áruház díja a kereskedelmi piactér által beszedett összes ajánlatértékesítésre vonatkozik, beleértve a bővítményeket is.

## <a name="price-tiers"></a>Árszintek

A kiválasztott árcsomag(ok) minden olyan országban beállítja az eladási árat, ahol az ajánlat terjesztését választja. További árképzési funkciókat is használhat, például különböző árakat választhat a különböző piacokhoz.

Ingyenesen felajánlhatja az alkalmazást, vagy választhat egy árat, amelyet az ügyfeleknek kell fizetniük az ajánlat megszerzéséért. Az árszintek 0,99 USD-nél kezdődnek, további növekményekkel (1,09 USD, 1,19 USD és így tovább). Az árszintek közötti lépések az ár növekedésével nőnek.

> [!NOTE] 
> Ezek az árcsomagok az ajánlaton belül kínált bővítményekre is vonatkoznak.

Minden árcsomagnak megfelelő értéke van az Áruház által kínált pénznemekben. Ezeket az értékeket arra használjuk, hogy segítsünk önnek eladni ajánlatát hasonló áron világszerte. A devizaárfolyamok változása miatt azonban a pontos értékesítési összeg kissé eltérhet egyik pénznemben.

Lehetősége van arra is, hogy egy adott piac helyi pénznemében megadjon egy általad választott szabad formájú árat. Ha ezt teszi, az ár nem módosul (még akkor sem, ha a konverziós arány oka megváltozik), hacsak nem küld be új árat. 

Ne feledje, hogy a kiválasztott ár tartalmazhat olyan értékesítést vagy hozzáadottérték-adót, amelyet a vevőknek meg kell fizetniük. További [információ: Adózási adatok a fizetett ajánlatért.](tax-details-paid-transactions.md)


## <a name="payout-reporting"></a>Kifizetési jelentés

A fizetési adatok részleteit és a jelentések letöltését a Partnerközpont **Kifizetési összegzésében** érheti [el.](https://partner.microsoft.com/dashboard) Az itt látható információkról és arról, hogy hogyan kategorizáljuk a keresett pénzt, [lásd: Kifizetési összefoglaló](payout-summary.md).


## <a name="payout-timeframe"></a>Kifizetési időkeret

A kifizetések havonta történnek (feltéve, hogy a vonatkozó fizetési küszöbérték teljesült, és ön nem helyezte el a kifizetést az alábbiakszerint). Az adott hónap 15.-ig általában az adott hónapban esedékes befizetést küldjük el. Ne feledje, hogy a kifizetések általában 3-10 további munkanapot vesznek igénybe a kifizetési számla eléréséhez. További információ: [Fizetési küszöbértékek, módszerek és időkeretek](payment-thresholds-methods-timeframes.md).


##  <a name="payout-hold-status"></a>Kifizetési visszatartás állapota

Alapértelmezés szerint a kifizetéseket havi rendszerességgel küldjük el a fent leírtak szerint. Azonban lehetősége van arra, hogy a kifizetések egy program ot tartásba, amely megakadályozza számunkra, hogy küldjön kifizetéseket a fiókjába. Ha úgy döntesz, hogy a kifizetéseit várakoztatod, továbbra is rögzítjük az elért bevételeket, és a részleteket a **kifizetési összefoglalódban**adjuk meg. A visszatartás eltávolításáig azonban nem küldünk kifizetéseket a fiókjába.

A kifizetések várakoztatásához nyissa meg a **Fejlesztői beállítások lapot.** A **Kifizetés és adózás**csoportban a Kifizetés és **adózási profil hozzárendelése** csoportban keresse meg azt a programot, amelyhez kifizetéseket szeretne tartani. A program kifizetéseinek megtartásához kattintson **a Fizetés visszatartása** jelölőnégyzetre. Bármikor módosíthatja a kifizetési visszatartás állapotát, de ne feledje, hogy döntése hatással lesz a következő havi kifizetésre. Ha például áprilisi kifizetést szeretne tartani, akkor a kifizetési visszatartás állapotát március vége előtt állítsa **Be** állapotra.

Miután beállította a kifizetési várakoztatásállapotát **Be**, a program összes kifizetése várakozik, amíg a csúszkát vissza nem kapcsolja **ki**. Ha így tesz, a következő havi kifizetési ciklusban is szerepelni fog (feltéve, hogy a vonatkozó kifizetési küszöbértékek teljesültek). Ha például a kifizetések várakoztatva vannak, de júniusban szeretnéd a kifizetést, akkor a kifizetési visszatartás állapotát május vége előtt **kapcsold ki.**

> [!NOTE]
> **A kifizetési visszatartás állapota** minden programra külön-külön vonatkozik (Microsoft Store, reklám, Azure Marketplace stb.). Ha az összes programon szeretné tartani a kifizetéseket, akkor minden programon külön-külön kell fizetnie.


 

 




