---
Description: Annak érdekében, hogy pénzt kapjon az ajánlati értékesítésből a kereskedelmi piacon, be kell állítania a kifizetési számláját, és ki kell töltenie a szükséges adózási űrlapokat.
title: A kifizetési számla és az adózási űrlapok beállítása | Azure Piactér
ms.date: 12/10/2019
ms.topic: conceptual
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.openlocfilehash: fb0caa64feb7cb024b684a2e94937aec8893bb5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281306"
---
# <a name="set-up-your-payout-account-and-tax-forms"></a>Kifizetési számla és űrlapok beállítása

Annak érdekében, hogy pénzt kapjon az ajánlati értékesítésből a kereskedelmi piacon, be kell állítania a kifizetési számláját, és ki kell töltenie a szükséges adózási űrlapokat a [Partner Centerben.](https://partner.microsoft.com/dashboard)

Ha csak ingyenes ajánlatokat szeretne felsorolni (és nem tervez alkalmazáson belüli vásárlásokat kínálni vagy a Microsoft Advertising-t használni), akkor nem kell kifizetési fiókot létrehoznia vagy adóűrlapokat kitöltenie. Ha később meggondolja magát, és úgy dönt, hogy ajánlatokat (vagy bővítményeket) szeretne eladni, beállíthatja kifizetési számláját, és akkor kitöltheti az adózási űrlapokat. A kifizetési fiók és az adózási profil befejezéséig nem küldhetsz be fizetős ajánlatokat vagy bővítményeket.

> [!NOTE]
> Bizonyos piacokon a kiadók csak ingyenes ajánlatot nyújthatnak be. Ha a számlája ezen piacok valamelyikén regisztrálva van, akkor nem lesz lehetősége kifizetési számla beállítására.

A fiók beállítása után két dolgot kell tennie ahhoz, hogy ajánlatot (vagy bővítményeket) értékesítsen a kereskedelmi piacon:

- [Töltse ki az adózási űrlapokat](#tax-forms)
- [A kifizetési számla beállítása](#payout-account)

> [!NOTE]
> A rról, hogy hogyan és mikor kell fizetni az ajánlata által leadott pénzért, [lásd: Fizetés](get-paid.md).

## <a name="tax-forms"></a>Adózási űrlapok

### <a name="filling-out-your-tax-forms"></a>Az adóűrlapok kitöltése

Először létre kell hoznia egy adózási profilt, és hozzá kell rendelnie azokhoz a programokhoz, amelyekben részt vesz. A kereskedelmi piactérhez az alábbi lépések végrehajtásával hozhat létre *adózási profilt:*

- Adja meg a lakóhelye és állampolgársága szerinti országot.
- Töltse ki a megfelelő adózási űrlapokat.

Adózási űrlapjait elektronikus úton is kitudja tölteni a Partnerközpontban; a legtöbb esetben nem kell űrlapokat nyomtatnia és postán elküldenie.

> [!IMPORTANT]
> A különböző országok és régiók eltérő adózási követelményekkel rendelkeznek. Az adóként fizetendő összeg pontos összege attól függ, hogy melyik országban és régióban értékesíti az ajánlatot. Tekintse meg a [Microsoft Azure Piactér kiadói szerződését,](https://go.microsoft.com/fwlink/p/?LinkID=699560) hogy megtudja, a Microsoft mely országokban utalja át az Ön nevében az értékesítési és használati adót. Más országokban, attól függően, hogy hol van regisztrálva, előfordulhat, hogy az ajánlat értékesítéseiért és használati adójából közvetlenül a helyi adóhatóságnak kell átutalnia az értékesítési és használati adót. Ezenkívül az ön által kapott alkalmazás-értékesítési bevételek bevételként adókötelesek lehetnek. Javasoljuk, hogy vegye fel a kapcsolatot az országa vagy régiója illetékes hatóságával, amely a legjobban segít meghatározni a kereskedelmi piactéri tevékenységeihez szükséges adózási adatokat.

1. A [Partnerközpontban](https://partner.microsoft.com/dashboard)válassza a Jobb felső sarokban a **Fiókbeállítások** ikont, majd a **Fejlesztői beállítások lehetőséget.**
2. A bal oldali navigációs menüben válassza a **Kifizetés és adózás**lehetőséget, majd a **Kifizetési és adó-hozzárendelések**lehetőséget.

3. Válassza ki azt a program- és eladóazonosító kombinációt, amelyhez adóadatokat szeretne konfigurálni.

4. Ha meglévő adózási profilt szeretne használni, válassza ki azt a legördülő menüből. Ellenkező esetben válassza **az Új profil létrehozása lehetőséget,** és nyomja le a **Küldés gombot.** Az adóprofilok oldalon kerül sor.
5. Az **adózási** adatok szerkesztéséhez kattintson a Szerkesztés gombra.
6. Válassza ki a megfelelő választógombot, és ha a rendszer kéri, válassza ki az országot. Ez a lépés határozza meg azt a Microsoft üzleti entitást, amelyet a rendszer a fiókjában történő kifizetésekhez használ.

7. lépésben megadott beállításoktól függően a rendszer kérni fogja, hogy adja meg az országához szükséges adózási információkat.

> [!NOTE]
> Függetlenül attól, hogy a lakóhely e ország vagy állampolgárság, ki kell töltenie az Egyesült Államok adózási űrlapokat eladni minden ajánlatot vagy add-ons keresztül a kereskedelmi piacon. Azoknak a kiadóknak, akik megfelelnek az Egyesült Államok bizonyos tartózkodási követelményeinek, ki kell tölteniük egy IRS W-9 nyomtatványt. Más kiadók az Egyesült Államokon kívül ki kell töltenie egy IRS W-8 űrlapot. Ezeket az űrlapokat az adózási profil kitöltésekéntöltse ki online.

### <a name="withholding-rates"></a>Forrásadó-ráták

Az adózási űrlapokon elküldött adatok határozzák meg a megfelelő adóelőleg-adót. A forrásadó-ráta csak az Egyesült Államokba történő értékesítésre vonatkozik; a nem amerikai telephelyekre történő értékesítéseket nem kell visszatartani. A visszatartási arány változó, de a legtöbb, az Egyesült Államokon kívül regisztráló megjelenítő esetében az alapértelmezett arány 30%. Lehetősége van arra, hogy csökkentse ezt az arányt, ha az ön országa beleegyezett egy jövedelemadó-szerződésbe az Egyesült Államokkal.

### <a name="tax-treaty-benefits"></a>Adóegyezmény előnyei

Ha az Egyesült Államokon kívül, akkor lehet, hogy kihasználják az adóegyezmény előnyeit. Ezek az előnyök országonként változnak, és lehetővé teszik, hogy csökkentse a kereskedelmi piac által visszatartandó adók összegét. A W-8BEN nyomtatvány II. Javasoljuk, hogy kommunikáljon a megfelelő erőforrásokkal az ön országában vagy régiójában annak meghatározásához, hogy ezek az előnyök vonatkoznak-e Önre.

> [!NOTE]
> Az Egyesült Államok egyéni adófizetői azonosító száma (vagy ITIN) nem szükséges ahhoz, hogy kifizetéseket kapjon a Microsofttól, vagy hogy adóegyezményből származó előnyöket igényeljen.

## <a name="payout-account"></a>Kifizetési számla

A kifizetési számla az a bankszámla, amelyre az értékesítésből származó bevételt küldjük. A Profil lapon megadott összes fizetési számlát megtekintheti.

> [!NOTE]
> Egyes piacokon a PayPal használható a kifizetési számlájához. Ha meg szeretné tudni, hogy a PayPal támogatott-e egy adott piacon, további részletekért olvassa el a [Fizetési küszöbértékek, módszerek és időkeretek,](payment-thresholds-methods-timeframes.md) valamint az alábbi [PayPal-információk című témakört.](#paypal-info)

### <a name="create-a-payment-profile"></a>Fizetési profil létrehozása

1. A [Partnerközpontban](https://partner.microsoft.com/dashboard)válassza a **Beállítások** fogaskerék ikonját a jobb felső sarokban, majd a **Fejlesztői beállítások lehetőséget.**
2. A *Kifizetés és az adófejléc* alatt válassza a Kifizetés és **adózási profil hozzárendelése**lehetőséget.

    > [!NOTE]
    > Mivel ez bizalmas adatok, előfordulhat, hogy a rendszer ismételten be kell jelentkeznie.

3. Válassza ki a konfigurálni kívánt fizetési módot.

4. Jelöljön ki egy meglévő fizetési profilt, vagy kattintson **az Új fizetési profil létrehozása** gombra a kiválasztott fizetési mód új profiljának létrehozásához.

### <a name="create-a-bank-based-payment-profile"></a>Bankalapú fizetési profil létrehozása

Ha úgy döntött, hogy bankszámlát használ a kifizetések fogadására, akkor a következő folyamatot fogja végrehajtani a bankszámla konfigurálásához.

1. A *Bankprofil* lapon adja meg a szükséges információkat a bankjáról.
2. Adja meg bankszámlaadatait.

    > [!NOTE]
    > A fiókadatok megadásához használt mezők csak alfanumerikus karaktereket fogadnak el.

3. Adja meg a kedvezményezett adatait.
4. A *Profil-hozzárendelés* oldalon válassza ki azt a pénznemet, amelyet a kifizetések kiállításakor használni szeretne.

    > [!WARNING]
    > Győződjön meg róla, hogy a bank elfogadja a kiválasztott kifizetési pénznemet.

5. Minden programhoz ki kell választania egy fizetési profilt, amelyen részt vesz, bár ugyanazt a profilt több programhoz is használhatja.

6. A módosítások mentéséhez kattintson a Küldés gombra.

> [!NOTE]
> A Microsoft nak akár 48 óra is eltelhet, amíg érvényesíti a profiljában szereplő adatokat. Ha ez a folyamat *befejeződött, az ellenőrzés állapota* a **Teljes**

A kifizetés sikeressítése érdekében győződjön meg arról, hogy:

- A Partnerközpontban a kifizetési számlájához megadott **számlatulajdonosi névnek** pontosan meg kell egyeznie a bankszámlájához társított névvel. Ha például a bankszámlaneve középső nevet tartalmaz, adjon hozzá egy középső nevet a **fióktulajdonos nevéhez.**
- A kifizetések közvetlenül a Microsofttól kerülnek átutalásra a bankszámlájára USD pénznemben.
- A Partnerközpontban latin betűkkel megadott banki adatokat a program cirill karakterekre fordítja.

### <a name="editing-existing-payment-profiles"></a>Meglévő fizetési profilok szerkesztése

A meglévő fizetési profilokat szerkesztheti, ha módosítania kell vagy ki kell javítania a helytelen adatokat.

1. A [Partnerközpontban](https://partner.microsoft.com/dashboard)válassza a **Beállítások** fogaskerék ikonját a jobb felső sarokban, majd a **Fejlesztői beállítások lehetőséget.**
2. A *Kifizetés és az adófejléc* alatt válassza a **Kifizetési és adózási profilok lehetőséget.**
3. A fizetési profilok az állapotukkal együtt jelennek meg. Keresse meg a szerkesztni kívánt profilt, és kattintson a Jobb szélen található **Szerkesztés** gombra.

> [!IMPORTANT]
> A kifizetési számla módosítása akár egy fizetési ciklussal is késleltetheti a kifizetéseket. Ez a késedelem azért fordul elő, mert ellenőriznünk kell a fiók változását, ugyanúgy, mint amikor először beállította dedikálta a kifizetési számlát. A fiók ellenőrzése után is megkapja a teljes összeg kifizetését; az aktuális fizetési ciklusra esedékes kifizetések hozzáadásra kerülnek a következő fizetési ciklushoz. További információ A [Fizetés szerzése.](get-paid.md)

### <a name="paypal-info"></a>PayPal info

Bizonyos országokban és régiókban fizetési fiókot hozhat létre paypal-adatainak megadásával. Mielőtt azonban a PayPal-t választaná fizetési számlaopcióként:

- Jelölje be [a fizetési küszöbértékeket, módszereket és időkereteket annak megerősítéséhez,](payment-thresholds-methods-timeframes.md) hogy a PayPal támogatott fizetési mód-e az Ön országában vagy régiójában.
- Tekintse át az alábbi gyakori kérdéseket. Az Ön helyzetétől függően előfordulhat, hogy a PayPal nem a legjobb fizetési számla opció az Ön számára, és előnyben részesíthetjük a bankszámlát.

Gyakori kérdések a PayPal fizetési módként való használatával kapcsolatban:

- **Milyen PayPal-beállításokra van szükségem a kifizetések fogadásához?** Győződjön meg arról, hogy paypal-fiókja nem blokkolja az eCheck kifizetéseket. Ezt a beállítást a PayPal fizetési fogadási preferenciái oldalon kezeli. További információ: [PayPal's account setup page.](https://go.microsoft.com/fwlink/p/?linkid=513139)
- **Támogatott az országom/régióm?** Lásd: [Fizetési küszöbértékek, módszerek és időkeretek,](payment-thresholds-methods-timeframes.md) hogy megtudja, hol a PayPal támogatott fizetési mód.
- **A PayPal-fiókomat ugyanabban az országban/régióban kell regisztrálni, mint a Partnerközpont-fiókomat?** Nem. PayPal-fiók beállításakor elfogadhatja az alapértelmezett konfigurációt. Nem lehetnek problémáid más országokkal/régiókkal és pénznemekkel, kivéve, ha bizonyos pénznemekben zároltad a fizetést. Ezt a beállítást a PayPal fizetési fogadási preferenciái oldalon kezeli.
- **El kell fogadnom a PayPal-kifizetéseket manuálisan?** Nem. A PayPal-fiókok alapértelmezés szerint úgy vannak beállítva, hogy a felhasználóktól manuálisan fogadják el a kifizetéseket, ami azt jelenti, hogy ha 30 napon belül nem fogadja el a kifizetést, azt visszaküldjük. Ezt a beállítást úgy módosíthatja, hogy kikapcsolja a "Ask Me"-t a PayPal További beállítások lapján.
- **Milyen pénznemeket támogat a PayPal?** Az aktuális listát lásd a [PayPal támogatási oldalán.](https://developer.paypal.com/docs/classic/api/currency-codes/#paypal)

### <a name="specific-requirements-for-certain-countriesregions"></a>Egyes országokra/régiókra vonatkozó különleges követelmények

Egyes országokban és régiókban a kifizetési számlákra vonatkozó további követelményeket kell betartani. Ha Ön Pakisztánban, Oroszországban vagy Ukrajnában él, vegye figyelembe az alábbi követelményeket.

#### <a name="pakistan"></a>Pakisztán

A Form-R pakisztáni banki szabályozási követelmény. A külföldi pénzeszközök átvételének célját és okát jelzik. Ezért bármikor, amikor jogosult a Microsoft havi kifizetésére, be kell nyújtania egy Form-R-t a bankjához, mielőtt a kifizetést ki lehetne adni a számlájára. A Form-R másolatának beszerzésével kapcsolatos utasításokért forduljon a helyi bankfiókhoz.

Minden hónapban be kell nyújtania egy Form-R-t a bankjához, amikor jogosult a kifizetésre. Ha például az év minden hónapjában kifizetésre számít, 12 alkalommal kell beküldenie egy Form-R-t (havonta egyszer).

Miután a kifizetést benyújtották a bankjának, 30 napja van arra, hogy benyújtsa a Form-R-t. Ha 30 napon belül nem nyújtják be, az összeget visszaküldjük a Microsoftnak.

#### <a name="russia"></a>Oroszország

Ha Ön Oroszországban élő kiadó, előfordulhat, hogy dokumentációt kell benyújtania a bankjának, mielőtt bankja pénzt helyezne el a számláján. Ha jogosult a kifizetésre, a következő dokumentációt e-mailben biztosítjuk Önnek:

- Elfogadási igazolás (AC) – tartalmazza a számlájára átutalt kifizetés összegét.
- [Microsoft Azure Piactér kiadói szerződése](https://go.microsoft.com/fwlink/p/?LinkID=699560) – a kiadói szerződés aláírt másolata, amelyet ellenaláírásra kell kötni.

A kifizetés sikeressítése érdekében győződjön meg arról, hogy:

- A Partnerközpontban a kifizetési számlájához megadott **számlatulajdonosi névnek** pontosan meg kell egyeznie a bankszámlájához társított névvel. Ha például a bankszámlaneve középső nevet tartalmaz, adjon hozzá egy középső nevet a **fióktulajdonos nevéhez.**
- A kifizetések közvetlenül a Microsofttól utalnak át a bankszámlájára Ruble (RUB) pénznemben.
- A Partnerközpontban latin betűkkel megadott banki adatokat a program cirill karakterekre fordítja.
- A kifizetéseket bankszámlára kell fordítani, nem pedig bankkártyára.

#### <a name="ukraine"></a>Ukrajna

Ha Ön Ukrajnában élő kiadó, előfordulhat, hogy dokumentációt kell benyújtania a bankjának, mielőtt bankja pénzt helyezne el a számláján. Ha jogosult a kifizetésre, a következő dokumentációt e-mailben biztosítjuk Önnek:

- Elfogadási igazolás (AC) – tartalmazza a számlájára átutalt kifizetés összegét.
- [Microsoft Azure Piactér kiadói szerződése](https://go.microsoft.com/fwlink/p/?LinkID=699560) – a kiadói szerződés aláírt másolata, amelyet ellenaláírásra kell kötni.
- Módosítási megállapodás (AA) – ezt a dokumentumot a bank felhasználhatja a kifizetési összegek azonosítására.

A Microsoft mindhárom dokumentumot biztosítja az első kifizetési kísérlet megkísérlésekor. Minden későbbi kifizetésesetén csak az AC dokumentumot kapja meg. Őrizze meg az ADA és AA dokumentumokat arra az esetre, ha szüksége lenne rájuk, hogy a jövőben kifizetéseket kapjon a bankjától.

### <a name="create-a-paypal-payment-profile"></a>PayPal fizetési profil létrehozása

Ha úgy döntött, hogy bankszámlát használ a kifizetések fogadására, akkor a következő folyamatot fogja végrehajtani a bankszámla konfigurálásához.

1. A *PayPal* oldalon adja meg a PayPal-fiókjával kapcsolatos szükséges információkat.
2. Adja meg PayPal-fiókjának adatait.

    > [!NOTE]
    > A fiókadatok megadásához használt mezők csak alfanumerikus karaktereket fogadnak el.

3. Adja meg a kedvezményezett adatait.
4. A *Profil-hozzárendelés* oldalon válassza ki azt a pénznemet, amelyet a kifizetések kiállításakor használni szeretne.
5. Minden programhoz ki kell választania egy fizetési profilt, amelyen részt vesz, bár ugyanazt a profilt több programhoz is használhatja.
6. A módosítások mentéséhez kattintson a Küldés gombra.
