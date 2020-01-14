---
title: Fizetés a kereskedelmi piactéren | Azure piactér
description: Megtudhatja, hogyan fogadhatja a bevételeit a kereskedelmi piactéren.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 7f539d4165df04ddca198385f9eef4f90ed15189
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933603"
---
# <a name="getting-paid"></a>Fizetés fogadása
Íme néhány fontos információ az ajánlatok, bővítmények és hirdetési bevételek fizetésének fogadásáról.

> [!IMPORTANT]
> Ahhoz, hogy pénzt kapjon a kereskedelmi piactéren elérhető értékesítési ajánlatból, be kell [állítania a kifizetési fiókját, és ki kell töltenie a szükséges adózási űrlapokat](set-up-your-payout-account-tax-forms.md).

## <a name="store-fee"></a>Tárolási díj

Amikor [regisztrál egy fejlesztői fiókra](https://go.microsoft.com/fwlink/p/?LinkID=615100), elfogadja a [Microsoft Azure Marketplace kiadói szerződést](https://go.microsoft.com/fwlink/p/?LinkID=699560). Ez a szerződés az Ön és a Microsoft közötti kapcsolatot mutatja be, mivel a kereskedelmi piactéren értékesítési ajánlatra vonatkozik, beleértve az áruházi díjat is, amelyet a Microsoft minden egyes értékesítésre felszámít.

A díjakat hivatalosan a [Microsoft Azure Marketplace kiadói szerződésben](https://go.microsoft.com/fwlink/p/?LinkID=699560)határozzák meg. Ha kérdése van, mindig tekintse át ezt a dokumentumot.

Az áruházi díjat a kereskedelmi piactér által gyűjtött összes ajánlati értékesítésre, beleértve a bővítményeket is.

## <a name="price-tiers"></a>Árképzési szintek

A kiválasztott árképzési szintek minden olyan országban beállítja az értékesítési árat, amelyben az ajánlat terjesztését választja. További díjszabási funkciókat is használhat, például különböző árakat választhat a különböző piacokon.

Az alkalmazást ingyenesen kínálhatja, vagy kiválaszthat egy árat, amelyet az ügyfeleknek fizetniük kell az ajánlat megvásárlásához. Az árak szintjei a következővel kezdődnek:. 99 USD, további növekményekkel (1,09 USD, 1,19 USD stb.). Az árak szintjei közötti növekmények növekednek, ahogy az ár magasabb lesz.

> [!NOTE] 
> Ezek az árak az ajánlaton belül kínált bővítményekre is érvényesek.

Minden egyes árszint megfelelő értékkel rendelkezik az áruház által kínált összes pénznemben. Ezeket az értékeket arra használjuk, hogy az ajánlatát az egész világon összehasonlítható áron értékesítse. A külföldi árfolyamok változásai miatt azonban a pontos eladások mennyisége némileg eltérhet az egyik pénznemtől a másikig.

Lehetősége van arra is, hogy egy adott piac helyi pénznemében adja meg a választott szabad formájú árat. Ha ezt teszi, a rendszer nem módosítja az árat (még akkor is, ha módosulnak a konverziós arányok), kivéve, ha új árral küldi el a frissítést. 

Ne feledje, hogy a kiválasztott díj olyan értékesítési vagy értéknövelt adót is tartalmazhat, amelyet az ügyfeleknek fizetniük kell. További információért tekintse [meg a fizetős ajánlat adózási adatait](tax-details-paid-transactions.md) .


## <a name="payout-reporting"></a>Kifizetési jelentések

A fizetési adatok részleteit a [partner Center](https://partner.microsoft.com/dashboard) **kifizetési összefoglalásával** érheti el, és letöltheti a jelentéseket. Az itt megjelenő információkkal és a keresett pénz kategorizálásával kapcsolatos további információkért lásd a [kifizetési összefoglalót](payout-summary.md).


## <a name="payout-timeframe"></a>Kifizetés időkerete

A kifizetések havi rendszerességgel történnek (feltéve, hogy a vonatkozó fizetési küszöbérték teljesült, és az alább leírtak szerint nem helyezte el a befizetését). A hónap 15. napján egy adott hónapban esedékes fizetési díj általában elküldhető. Vegye figyelembe, hogy a kifizetések általában 3 – 10 további munkanapot vesznek igénybe, hogy elérjék a kifizetési fiókját. További információ: [fizetési küszöbértékek, metódusok és időkeretek](payment-thresholds-methods-timeframes.md).


##  <a name="payout-hold-status"></a>Kifizetés holdjának állapota

Alapértelmezés szerint a fentiekben leírtak szerint havi rendszerességgel küldjük el a kifizetéseket. Lehetősége van azonban arra is, hogy a kifizetéseit egy megtartott programba helyezi, ami megakadályozza, hogy kifizesse a fiókját. Ha úgy dönt, hogy megtartja a befizetését, továbbra is rögzítjük a beszerzett bevételt, és megadhatjuk a **nyeremények összegzésének**részleteit. Azonban addig nem küldünk fizetést a fiókjába, amíg el nem távolítja a tárolót.

A befizetések tárolásához lépjen a **fejlesztői beállítások menüpontra**. A **kifizetés és az adó**területen a **kifizetési és adózási profil hozzárendelése** szakaszban keresse meg azt a programot, amelyhez a kifizetéseket tárolni szeretné. A programhoz tartozó fizetések tárolásához kattintson a **saját fizetés megtartása** jelölőnégyzetre. Bármikor módosíthatja a kifizetési állapotot, de vegye figyelembe, hogy a döntés a következő havi kifizetésre is hatással van. Ha például a április végét szeretné megtartani, mindenképpen **a március vége előtt állítsa** be a kifizetési Holding állapotát.

Miután beállította a kifizetés állapotát **a be**értékre, a program összes kifizetése megmarad, amíg a csúszkát vissza **nem kapcsolja.** Ha így tesz, a következő havi kifizetési ciklusban is szerepelni fog (ha teljesülnek az érvényes fizetési küszöbértékek). Ha például megtartotta a befizetését, de a júniusi kifizetést szeretné kinyerni, akkor a határidő lejárta előtt mindenképpen **kapcsolja ki** a kifizetési Hold állapotot.

> [!NOTE]
> A **kifizetési Holding-állapot** minden egyes programra külön vonatkozik (Microsoft Store, reklám, Azure Marketplace stb.). Ha az összes programban szeretné kifizetni a befizetéseket, minden egyes programban külön kell fizetnie.


 

 




