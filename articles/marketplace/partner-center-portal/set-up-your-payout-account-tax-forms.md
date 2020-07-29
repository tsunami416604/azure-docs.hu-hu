---
title: Kifizetési fiók beállítása & adóügyi űrlapok – Microsoft kereskedelmi piactér
Description: Ha pénzt szeretne kapni a kereskedelmi piactéren elérhető ajánlat értékesítéséből, Ismerje meg, hogyan állíthatja be a kifizetési fiókját, és töltse ki a szükséges adózási űrlapokat.
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: marketplace
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 9d75f210ed0547a001ad1dd35f8abfffd31c82a4
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87304207"
---
# <a name="set-up-your-payout-account-and-tax-forms"></a>Kifizetési számla és űrlapok beállítása

Ahhoz, hogy pénzt kapjon a kereskedelmi piactéren elérhető ajánlatokból, be kell állítania a kifizetési fiókját, és ki kell töltenie a [partner Center](https://partner.microsoft.com/dashboard)szükséges adózási űrlapait.

Ha csak az ingyenes ajánlatokat szeretné kilistázni (és nem tervezi az alkalmazáson belüli vásárlást vagy Microsoft Advertising használatát), nem kell kiállítania egy kifizetési fiókot, vagy ki kell töltenie az összes adózási űrlapot. Ha később meggondolja magát, és eldönti, hogy szeretné-e eladni az ajánlatokat (vagy bővítményeket), beállíthatja a kifizetési fiókját, és kitöltheti az adózási űrlapokat. Nem küldhet fizetős ajánlatokat vagy bővítményeket, amíg be nem fejeződik a kifizetési fiók és az adózási profil.

> [!NOTE]
> Bizonyos piacokon a kiadók csak ingyenes ajánlatot küldhetnek el. Ha a fiókja az egyik piacon regisztrálva van, nem lesz lehetősége a kifizetési fiók beállítására.

Miután beállította a fiókját, két dolgot kell tennie, mielőtt az ajánlat (vagy a bővítmények) értékesítését elvégezte a kereskedelmi piactéren:

- [Töltse ki az adózási űrlapokat](#tax-forms)
- [A kifizetési fiók beállítása](#payout-account)

> [!NOTE]
> További információ arról, hogy hogyan és mikor kell fizetnie az Ön által kínált pénzért, lásd: [befizetések](get-paid.md).

## <a name="tax-forms"></a>Adózási űrlapok

### <a name="fill-out-your-tax-forms"></a>Töltse ki az adózási űrlapokat

Először létre kell hoznia egy adózási profilt, és hozzá kell rendelnie azokat a programokhoz, amelyekben részt vesz a programban. A kereskedelmi piactéren a következő lépések végrehajtásával hozhatja létre az *adózási profilt* :

- Adja meg az ország/régió lakóhelyét és állampolgárságát.
- Töltse ki a megfelelő adózási űrlapokat.

A partner Centerben elektronikus formában is elvégezheti és elküldheti az adó-formanyomtatványokat; a legtöbb esetben nem kell űrlapokat kinyomtatnia és e-maileket felvennie.

> [!IMPORTANT]
> A különböző országok és régiók esetében eltérő az adózási követelmények. Az adó összegének pontos összege attól függ, hogy milyen országokban és régiókban vásárolja meg az ajánlatát. Tekintse meg a [Microsoft Azure Marketplace kiadói szerződést](https://go.microsoft.com/fwlink/p/?LinkID=699560) , amely alapján megállapíthatja, hogy mely országokra/régiókra vonatkozik a Microsoft értékesítési és használati adó az Ön nevében. Más országokban/régiókban, attól függően, hogy hol van regisztrálva, előfordulhat, hogy az értékesítési és használati adót közvetlenül a helyi adózási szolgáltatónak kell elvégeznie. Emellett az alkalmazás értékesítésének bevétele a kapott bevételként is adóköteles lehet. Javasoljuk, hogy lépjen kapcsolatba az országa vagy régiója illetékes hatóságával, amely a legjobban segít meghatározni a kereskedelmi Marketplace-tevékenységek megfelelő adózási adatait.

1. A [partner Centerben](https://partner.microsoft.com/dashboard)válassza a **Fiókbeállítások** ikont a jobb felső sarokban, majd válassza a **fejlesztői beállítások**elemet.

2. A bal oldali navigációs menüben válassza a **kifizetés és adó**lehetőséget, majd válassza ki a **kifizetési és adózási hozzárendelések**elemet.

3. Válassza ki azt a program-és eladó-azonosító kombinációt, amelynek az adózási adatait konfigurálni kívánja.

4. Ha meglévő adózási profilt szeretne használni, válassza ki a legördülő menüből. Ellenkező esetben válassza az **új profil létrehozása** elemet, majd kattintson a **Küldés**gombra. Ekkor megnyílik az adó-profilok lap.

5. Az adózási adatok szerkesztéséhez kattintson a **Szerkesztés** gombra.

6. Válassza ki a megfelelő választógombot, és ha a rendszer kéri, válassza ki az országot/régiót. Ez a lépés határozza meg a Microsoft üzleti entitást, amelyet a rendszer a fiókon kifizetéshez használ.

7. A 6. lépésben kiválasztott beállításoktól függően a rendszer kérni fogja, hogy adja meg az országa/régiója számára szükséges adózási adatokat.

   > [!NOTE]
   > A tartózkodási hely vagy az állampolgárság országa/régiója tekintetében ki kell töltenie Egyesült Államoki adózási űrlapokat, hogy bármely ajánlatot vagy bővítményt a kereskedelmi piactéren értékesítsen. Azok a közzétevők, akik megfelelnek bizonyos Egyesült Államok tartózkodási követelményeknek, ki kell tölteniük egy IRS-t (9). A Egyesült Államokon kívüli többi közzétevőnek ki kell töltenie egy IRS-8 űrlapot. Ezeket az űrlapokat online is kitöltheti az adózási profil befejezése után.

### <a name="withholding-rates"></a>Visszatartási díjak

Az adózási űrlapokon beküldött adatok meghatározzák a megfelelő adó-visszatartási arányt. A visszatartási arány csak az Egyesült Államokba helyezett értékesítésekre vonatkozik; a nem USA-beli helyszínekre irányuló értékesítések nem tartoznak a forrásadó alá. A visszatartási arányok eltérőek, de a legtöbb közzétevőnél a Egyesült Államokon kívül regisztrálva az alapértelmezett érték 30%. Ha az országa/régiója egyetért a jövedelemadó-szerződéssel, akkor csökkentheti ezt a mértéket, ha a Egyesült Államok.

### <a name="tax-treaty-benefits"></a>Adózási szerződés előnyei

Ha a Egyesült Államokon kívül esik, lehetősége lesz az adózási szerződés előnyeinek kihasználására. Ezek az előnyök országonként vagy régiónként eltérőek, és lehetővé tehetik, hogy csökkentse a kereskedelmi piactér által visszaadott adók mennyiségét. Az adózási szerződés előnyeit a 8BEN űrlap II. részének befejezésével veheti igénybe. Javasoljuk, hogy az Ön országában vagy régiójában lévő megfelelő erőforrásokkal kommunikáljon annak megállapításához, hogy ezek az előnyök Önre vonatkoznak-e.

> [!NOTE]
> Egy Egyesült Államok egyedi adófizető-azonosító szám (vagy ITIN) nem szükséges a Microsofttól érkező fizetések fogadására, vagy az adó-szerződésből származó előnyök igénylésére.

## <a name="payout-account"></a>Kifizetési fiók

A kifizetési fiók az a bankszámla, amelyre a bevételeket küldjük. A profil lapon megadott összes fizetési fiókot megtekintheti.

> [!NOTE]
> Bizonyos piacokon a PayPal a kifizetési fiókjához is használható. Annak megállapításához, hogy a PayPal támogatott-e egy adott piacon, további részletekért tekintse meg a [fizetési küszöbértékeket, a metódusokat és a határidők](payment-thresholds-methods-timeframes.md) és a [PayPal-információk](#paypal-info) című szakaszt.

### <a name="create-a-payment-profile"></a>Fizetési profil létrehozása

1. A [partner Centerben](https://partner.microsoft.com/dashboard)válassza a **Beállítások** fogaskerék ikont a jobb felső sarokban, majd válassza a **fejlesztői beállítások**lehetőséget.
2. A *kifizetés és az adó* fejléc alatt válassza a **kifizetés és az adó profil hozzárendelése**lehetőséget.

    > [!NOTE]
    > Mivel ez bizalmas információ, a rendszer kérheti, hogy jelentkezzen be újra.

3. Válassza ki a konfigurálni kívánt fizetési módot.

4. Válasszon ki egy meglévő fizetési profilt, vagy kattintson az **új fizetési profil létrehozása** lehetőségre új profil létrehozásához a választott fizetési módhoz.

### <a name="create-a-bank-based-payment-profile"></a>Banki alapú fizetési profil létrehozása

Ha úgy döntött, hogy bankszámlát használ a kifizetések fogadásához, a következő eljárást kell végrehajtania a bankszámla konfigurálásához.

1. A *Bank Profile* lapon adja meg a bankhoz szükséges adatokat.
2. Adja meg a bankszámlájához tartozó adatokat.

    > [!NOTE]
    > A fiókadatok megadásához használt mezők csak alfanumerikus karaktereket fogadnak el.

3. Adja meg a kedvezményezett adatait.
4. A profil- *hozzárendelés* lapon válassza ki azt a pénznemet, amelyet a kifizetések kiválasztásakor használni szeretne.

    > [!WARNING]
    > Győződjön meg arról, hogy a Bank elfogadja a kiválasztott kifizetési pénznemet.

5. Minden programban részt vevő programhoz ki kell választania egy fizetési profilt, de több program esetében ugyanazt a profilt használhatja.

6. A módosítások mentéséhez kattintson a Submit (küldés) gombra.

   > [!NOTE]
   > A Microsoft akár 48 órát is igénybe vehet a profilban lévő információk érvényesítéséhez. Ha ez a folyamat befejeződött, **a teljes** *ellenőrzés állapota* megjelenik

Győződjön meg arról, hogy a kifizetés sikeres volt, ügyeljen rá, hogy:

- A partner Centerben a kifizetési fiókjához megadott **fiók tulajdonosának** pontosan meg kell egyeznie a bankszámlával társított névvel. Ha például a bankszámla neve középső nevet tartalmaz, adjon hozzá egy középső nevet a **fiók tulajdonosának nevéhez**.
- A kifizetések közvetlenül a Microsofttól a bankszámlájára lesznek továbbítva USD pénznemben.
- A partner Centerben megadott banki adatok latin betűvel vannak lefordítva a cirill karakterekre.

### <a name="editing-existing-payment-profiles"></a>Meglévő fizetési profilok szerkesztése

Ha módosítani vagy javítani szeretné a helytelen adatokat, módosíthatja a meglévő fizetési profilokat.

1. A [partner Centerben](https://partner.microsoft.com/dashboard)válassza a **Beállítások** fogaskerék ikont a jobb felső sarokban, majd válassza a **fejlesztői beállítások**lehetőséget.

2. A *kifizetés és az adó* fejléc alatt válassza a **kifizetési és adózási profilok**lehetőséget.

3. A fizetési profilok az állapotukkal együtt lesznek felsorolva. Keresse meg a szerkeszteni kívánt profilt, és kattintson a jobb szélen található **Szerkesztés** gombra.

> [!IMPORTANT]
> A kifizetési fiók módosítása akár egy fizetési ciklusban is késleltetheti a befizetését. Ez a késleltetés azért fordul elő, mert ellenőrizni kell a fiók változását, ugyanúgy, ahogy a kifizetési fiók első beállításakor. A fiók ellenőrzése után továbbra is a teljes összegért kell fizetnie. a rendszer a jelenlegi fizetési ciklus miatt esedékes összes kifizetést hozzáadja a következőhöz. További információért lásd: [befizetések](get-paid.md) .

### <a name="paypal-info"></a>PayPal-információ

Az országok és régiók kiválasztása lapon fizetési fiókot hozhat létre a PayPal-adatok beírásával. A PayPal fizetési fiókként való kiválasztása előtt azonban a következő lehetőségek közül választhat:

- A [fizetési küszöbértékek, módszerek és időkeretek](payment-thresholds-methods-timeframes.md) ellenőrzése annak megerősítéséhez, hogy a PayPal támogatott fizetési mód-e az Ön országában vagy régiójában.
- Tekintse át az alábbi gyakori kérdéseket. Előfordulhat, hogy a PayPal nem a legjobb fizetési fiók lehetőséget választja, és előfordulhat, hogy egy bankszámla is előnyben részesített.

Gyakori kérdések a PayPal fizetési módszerként való használatáról:

- **Milyen PayPal-beállításokra van szükségem a fizetések fogadásához?** Győződjön meg arról, hogy a PayPal-fiókja nem blokkolja a eCheck befizetését. Ezt a beállítást a PayPal fizetési fogadási beállítások lapján kezelheti. További információért lásd a [PayPal fiókjának beállítása lapot](https://go.microsoft.com/fwlink/p/?linkid=513139) .

- **Támogatott az országom/régióm?** Tekintse meg a [fizetési küszöbértékeket, metódusokat és időkereteket](payment-thresholds-methods-timeframes.md) , hogy megtudja, hol található a PayPal egy támogatott fizetési mód.

- **Meg kell-e regisztrálni a PayPal-fiókomat ugyanabban az országban/régióban, mint a partner Center-fiókom?** Nem. A PayPal-fiók beállításakor elfogadhatja az alapértelmezett konfigurációt. A többi országgal/régióval és pénznemmel kapcsolatos problémával nem rendelkezhet, kivéve, ha letiltotta a fizetést bizonyos pénznemekben. Ezt a beállítást a PayPal fizetési fogadási beállítások lapján kezelheti.

- **Manuálisan kell PayPal-fizetést fogadni?** Nem. A PayPal-fiókok alapértelmezés szerint úgy vannak beállítva, hogy a felhasználók számára manuálisan fogadják el a befizetéseket, ami azt jelenti, hogy 30 napon belül nem fogadja el a befizetést. Ezt a beállítást megváltoztathatja a PayPal további beállítások lapján a "Kérdezzen meg" lehetőség kikapcsolásával.

- **Milyen pénznemeket támogat a PayPal?** Tekintse [meg a PayPal támogatási oldalát](https://developer.paypal.com/docs/classic/api/currency-codes/#paypal) az aktuális listához

### <a name="specific-requirements-for-certain-countriesregions"></a>Bizonyos országokban/régiókban meghatározott követelmények

Egyes országokban és régiókban a kifizetési fiókok további követelményeit kell követni. Ha Ön Pakisztán, Oroszország vagy Ukrajna lakosa, vegye figyelembe az alábbi követelményeket.

#### <a name="pakistan"></a>Pakisztán

Az űrlap-R egy pakisztáni banki szabályozási követelmény. A szolgáltatás a források külföldről való fogadásának célját és okát jelzi. Ezért bármikor, ha Ön jogosult a Microsoft havi befizetésére, a kifizetés a fiókba való kiadása előtt be kell nyújtania egy Form-R-t a bankba. Az űrlap-R másolatának beszerzésével kapcsolatos útmutatásért forduljon a helyi bank ágában.

Minden hónapban be kell nyújtania egy űrlap-R-t a bankba, hogy Ön jogosult legyen a kifizetésre. Ha például az év minden hónapjában várhatóan fizetést szeretne kapni, akkor az R 12 alkalommal kell elküldenie egy űrlapot (havonta egyszer).

Miután elküldte a nyereményt a banknak, 30 napja van egy Form-R beküldéséhez. Ha 30 napon belül nem küldi el a rendszer, a rendszer visszaküldi a forrásokat a Microsoftnak.

#### <a name="russia"></a>Oroszország

Ha Ön olyan közzétevő, aki Oroszországban él, előfordulhat, hogy a bankja számára meg kell adnia a dokumentációt, mielőtt a bank befizeti az összeget a fiókjába. Ha jogosult a fizetésre, a következő e-mail-üzenetben biztosítjuk Önnek az alábbi dokumentációt:

- Elfogadási tanúsítvány (AC) – a fiókba átutalt kifizetés mennyiségét tartalmazza.
- [Microsoft Azure Marketplace kiadói szerződés](https://go.microsoft.com/fwlink/p/?LinkID=699560) – a kiadói szerződés aláírt másolata, amely alá kell írni a számlálót.

Győződjön meg arról, hogy a kifizetés sikeres volt, ügyeljen rá, hogy:

- A partner Centerben a kifizetési fiókjához megadott **fiók tulajdonosának** pontosan meg kell egyeznie a bankszámlával társított névvel. Ha például a bankszámla neve középső nevet tartalmaz, adjon hozzá egy középső nevet a **fiók tulajdonosának nevéhez**.
- A kifizetések közvetlenül a Microsofttól a bankszámlájára kerülnek a rubel (RUB) pénznemben.
- A partner Centerben megadott banki adatok latin betűvel vannak lefordítva a cirill karakterekre.
- A kifizetéseket bankszámlára kell tenni, és nem kell bankkártyához adni.

#### <a name="ukraine"></a>Ukrajna

Ha Ön olyan közzétevő, aki Ukrajnában él, előfordulhat, hogy meg kell adnia egy dokumentációt a bank számára, mielőtt a bank befizeti az összeget a fiókjába. Ha jogosult a fizetésre, a következő e-mail-üzenetben biztosítjuk Önnek az alábbi dokumentációt:

- Elfogadási tanúsítvány (AC) – a fiókba átutalt kifizetés mennyiségét tartalmazza.
- [Microsoft Azure Marketplace kiadói szerződés](https://go.microsoft.com/fwlink/p/?LinkID=699560) – a kiadói szerződés aláírt másolata, amely alá kell írni a számlálót.
- Módosítási szerződés (AA) – ezt a dokumentumot a bankja használhatja a kifizetési alapok azonosításához.

A Microsoft mindhárom dokumentumot az első kifizetésének kísérlete után biztosítja. Minden további kifizetés esetében csak az AC-dokumentumot fogja kapni. Őrizze meg az ADA-t és az AA-dokumentumokat arra az esetre, ha szüksége van rájuk a jövőbeli kifizetések fogadásához a banktól.

### <a name="create-a-paypal-payment-profile"></a>PayPal-fizetési profil létrehozása

Ha úgy döntött, hogy bankszámlát használ a kifizetések fogadásához, a következő eljárást kell végrehajtania a bankszámla konfigurálásához.

1. A *PayPal* lapon adja meg a szükséges adatokat a PayPal-Fiókjával kapcsolatban.
2. Adja meg a PayPal-fiókjának adatait.

    > [!NOTE]
    > A fiókadatok megadásához használt mezők csak alfanumerikus karaktereket fogadnak el.

3. Adja meg a kedvezményezett adatait.
4. A profil- *hozzárendelés* lapon válassza ki azt a pénznemet, amelyet a kifizetések kiválasztásakor használni szeretne.
5. Minden programban részt vevő programhoz ki kell választania egy fizetési profilt, de több program esetében ugyanazt a profilt használhatja.
6. A módosítások mentéséhez kattintson a Submit (küldés) gombra.

## <a name="next-steps"></a>További lépések

[A kereskedelmi piactér számlázása](billing-details.md)