---
title: A lefoglalt kapacitású Blob-tárhely költségeinek optimalizálása - Azure Storage
description: Ismerje meg az Azure Storage számára fenntartott kapacitás megvásárlását a blokkblob és az Azure Data Lake Storage Gen2 erőforrások költségeinek csökkentése érdekében.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e73686629de8481f6a37e5bfafc9b723206b4853
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351035"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>A fenntartott kapacitású Blob Storage költségeinek optimalizálása

Az Azure Storage számára fenntartott kapacitással pénzt takaríthat meg a blobadatok tárolási költségeiből. Az Azure Storage által fenntartott kapacitás kedvezményt biztosít a blokkblobok kapacitására és az Azure Data Lake Storage Gen2 normál tárfiókokban lévő adataira, ha egy vagy három évre szóló foglalást vállal. A foglalás meghatározott mennyiségű tárolókapacitást biztosít a foglalás időtartama alatt.

Az Azure Storage által lefoglalt kapacitás jelentősen csökkentheti a blokkblobok és az Azure Data Lake Storage Gen2 adatainak kapacitásköltségeit. Az elért költségmegtakarítás a foglalás időtartamától, a lefoglalni kívánt teljes kapacitástól, valamint a tárfiókhoz kiválasztott hozzáférési szinttől és redundanciatípusától függ. A fenntartott kapacitás számlázási engedményt biztosít, és nem befolyásolja az Azure Storage-erőforrások állapotát.

Az Azure Storage-foglalás díjszabásával kapcsolatos további információkért tekintse meg a [blokkblobok díjszabását](https://azure.microsoft.com/pricing/details/storage/blobs/) és [az Azure Data Lake Storage Gen 2 díjszabását](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="reservation-terms-for-azure-storage"></a>Az Azure Storage foglalási feltételei

A következő szakaszok ismertetik az Azure Storage-foglalás feltételeit.

### <a name="reservation-capacity"></a>Foglalási kapacitás

Az Azure Storage számára fenntartott kapacitást 100 TB-os és 1 PB/hó egységben vásárolhatja meg egy éves vagy hároméves időtartamra.

### <a name="reservation-scope"></a>Foglalási hatókör

Az Azure Storage számára fenntartott kapacitás egyetlen előfizetéshez vagy több előfizetéshez (megosztott hatókörhöz) érhető el. Ha egyetlen előfizetésre van scoped, a foglalási kedvezmény csak a kiválasztott előfizetésre vonatkozik. Ha több előfizetésre van scoped, a foglalási kedvezmény az ügyfél számlázási környezetében meg oszlik az előfizetések között.

Amikor megvásárolja az Azure Storage számára fenntartott kapacitást, használhatja a foglalást a blokkblobhoz és az Azure Data Lake Storage Gen2 adataihoz is. A foglalás a megvásárolt hatókörön belüli használatra vonatkozik, és nem korlátozódhat egy adott tárfiókra, tárolóra vagy objektumra az előfizetésen belül. A foglalás nem osztható fel több előfizetés között.

Az Azure Storage-foglalás csak az előfizetésben vagy megosztott erőforráscsoportban tárolt adatok mennyiségét fedezi. A foglalás nem tartalmazza a korai törlési, üzemeltetési, sávszélesség- és adatátviteli díjakat. Amint megvásárol egy foglalást, a foglalási attribútumokkal megegyező kapacitásdíjakat a kedvezményes áron számítjuk fel, nem pedig a felosztó-kiosztó díjszabás szerint. Az Azure-foglalásokról a [Mik azok az Azure-foglalások?](/azure/billing/billing-save-compute-costs-reservations)

### <a name="supported-account-types-tiers-and-redundancy-options"></a>Támogatott fióktípusok, rétegek és redundanciabeállítások

Az Azure Storage számára fenntartott kapacitás érhető el a szabványos tárfiókokban lévő erőforrásokhoz, beleértve az általános célú v2 (GPv2) és a Blob storage-fiókokat.

Minden hozzáférési szint (gyakori elérésű, ritka elérésű és archiválási) foglalásesetén támogatott. A hozzáférési szintekről további információt az [Azure Blob storage: gyakori elérésű, ritka elérésű és archív hozzáférési szintek című témakörben talál.](storage-blob-storage-tiers.md)

A redundancia minden típusa támogatott a foglalásokhoz. A redundanciabeállításokról az [Azure Storage redundanciája](../common/storage-redundancy.md)című témakörben talál további információt.

> [!NOTE]
> Az Azure Storage számára fenntartott kapacitás nem érhető el prémium szintű tárfiókok, általános célú v1 (GPv1) tárfiókok, Azure Data Lake Storage Gen1, lapblobok, Azure Queue storage, Azure Table storage vagy Azure Files.  

### <a name="security-requirements-for-purchase"></a>A vásárlásbiztonsági követelményei

Fenntartott kapacitás vásárlása:

- Legalább egy vállalati vagy egyéni **előfizetéstulajdonosi** szerepkörben kell lennie, ha felosztó-ki-felárak vannak.
- Nagyvállalati előfizetések esetén **a Fenntartott példányok hozzáadása** szolgáltatást engedélyezni kell az EA portálon. Ha ez a beállítás le van tiltva, akkor az előfizetésben eA-rendszergazdának kell lennie.
- A felhőszolgáltató (CSP) program esetében csak a rendszergazdai ügynökök vagy az értékesítési ügynökök vásárolhatnak Azure Blob Storage számára fenntartott kapacitást.

## <a name="determine-required-capacity-before-purchase"></a>A vásárlás előtt szükséges kapacitás meghatározása

Amikor azure Storage-foglalást vásárol, ki kell választania a régiót, a hozzáférési szintet és a redundancia lehetőséget a foglaláshoz. A foglalás csak az adott régióban tárolt adatokra, hozzáférési szintre és redundanciaszintre érvényes. Tegyük fel például, hogy a zónaredundáns tárolás (ZRS) használatával vásárol egy foglalást az USA nyugati részén lévő adatokhoz. Nem használhatja ugyanazt a foglalást az usa-keleti adatokhoz, az archív rétegben lévő adatokhoz vagy a georedundáns tárolás (GRS) adataihoz. További igényekre azonban vásárolhat egy másik foglalást.  

A foglalások ma 100 TB vagy 1 PB blokkra állnak rendelkezésre, magasabb kedvezménnyel 1 PB blokkra. Amikor foglalást vásárol az Azure Portalon, a Microsoft javaslatokat tehet önnek a korábbi használat alapján, hogy segítsen meghatározni, hogy melyik foglalást kell megvásárolnia.

## <a name="purchase-azure-storage-reserved-capacity"></a>Az Azure Storage számára fenntartott kapacitás vásárlása

Az Azure Storage számára fenntartott kapacitást az [Azure Portalon](https://portal.azure.com)keresztül vásárolhatja meg. A foglalásért fizethet előre vagy havi részletekben. A havi fizetéssel történő vásárlásról az [Azure-foglalások vásárlása előre vagy havi fizetéssel](/azure/billing/billing-monthly-payments-reservations)című témakörben talál további információt.

A forgatókönyvhöz megfelelő foglalási feltételek azonosításához az [Azure Storage számára fenntartott kapacitáskedvezmény ismertetése](../../cost-management-billing/reservations/understand-storage-charges.md)című témakörben talál segítséget.

A fenntartott kapacitás megvásárlásához kövesse az alábbi lépéseket:

1. Nyissa meg a [Vásárlási foglalások](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) ablaktáblát az Azure Portalon.  
1. Válassza az **Azure Blob Storage** új foglalás megvásárlásához.  
1. Töltse ki a szükséges mezőket az alábbi táblázatban leírtak szerint:

    ![Képernyőkép a lefoglalt kapacitás megvásárlásáról](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |Mező  |Leírás  |
   |---------|---------|
   |**Hatókör**   |  Azt jelzi, hogy hány előfizetés használhatja a foglaláshoz társított számlázási előnyt. Azt is szabályozza, hogy a foglalás hogyan vonatkozik az adott előfizetésekre. <br/><br/> Ha a Megosztott lehetőséget **választja,** a foglalási kedvezmény az Azure Storage-kapacitásra vonatkozik a számlázási környezetbármely előfizetésben. A számlázási környezet az Azure-ra való feliratkozás módjától függ. A vállalati ügyfelek számára a megosztott hatókör a regisztráció, és a regisztráción belüli összes előfizetést tartalmazza. A használatalapú ügyfelek esetében a megosztott hatókör tartalmazza az összes egyéni előfizetést a fiókadminisztrátor által létrehozott használatalapú díjokkal.  <br/><br/>  Ha **az egyetlen előfizetés**t választja, a foglalási kedvezmény az Azure Storage-kapacitásra vonatkozik a kiválasztott előfizetésben. <br/><br/> Ha **az Egyetlen erőforráscsoport**lehetőséget választja, a foglalási engedmény a kiválasztott előfizetésben és az adott előfizetésen belül az Azure Storage-kapacitásra lesz alkalmazva. <br/><br/> A foglalási hatókört a foglalás megvásárlása után módosíthatja.  |
   |**Előfizetés**  | Az Azure Storage-foglaláshoz használt előfizetés. A kiválasztott előfizetés fizetési módja a költségek felszámítására szolgál. Az előfizetésnek a következő típusok egyikének kell lennie: <br/><br/>  Nagyvállalati szerződés (ajánlatszámok: MS-AZR-0017P vagy MS-AZR-0148P): Nagyvállalati előfizetés esetén a díjakat levonják a beléptetés pénzügyi kötelezettségvállalási egyenlegéből, vagy túllépésként számítanak fel. <br/><br/> Egyéni előfizetés felosztó-kiosztó díjszabással (ajánlatszámok: MS-AZR-0003P vagy MS-AZR-0023P): A felosztó-kiosztó díjak at az előfizetés hitelkártyájára vagy számlafizetési módjára számláznak.    |
   | **Régió** | Az a régió, ahol a foglalás érvényben van. |
   | **Hozzáférési szint** | Az a hozzáférési szint, amelyre vonatkozóan a foglalás érvényben van. A lehetőségek közé tartozik *a Forró,* *A Hűvös*vagy *Archív*. A hozzáférési szintekről további információt az [Azure Blob storage: gyakori elérésű, ritka elérésű és archív hozzáférési szintek című témakörben talál.](storage-blob-storage-tiers.md) |
   | **Redundancia** | A foglalás redundancia lehetősége. A lehetőségek közé tartozik *az LRS*, *zrs*, *GRS*és *RA-GZRS*. A redundanciabeállításokról az [Azure Storage redundanciája](../common/storage-redundancy.md)című témakörben talál további információt. |
   | **Számlázási gyakoriság** | Azt jelzi, hogy milyen gyakran számlázzák ki a fiókot a foglalásért. A lehetőségek közé tartozik *a havi* vagy *az előzetes*. |
   | **Méret** | Az a régió, ahol a foglalás érvényben van. |
   |**Kifejezés**  | Egy vagy három éve.   |

1. Miután kiválasztotta a foglalás paramétereit, az Azure Portal megjeleníti a költségeket. A portál on is mutatja az árengedmény százalékos több mint pay-as-you-billing.

1. A **Vásárlás foglalások** ablaktáblán tekintse át a foglalás teljes költségét. A foglalás nevét is megadhatja.

    ![Képernyőkép a foglalás megvásárlásáról](media/storage-blob-reserved-capacity/purchase-reservations.png)

Miután megvásárolta a foglalást, a rendszer automatikusan alkalmazza azt a meglévő Azure Storage-blokkblobokra vagy az Azure Data Lake Storage Gen2 erőforrásaira, amelyek megfelelnek a foglalás feltételeinek. Ha még nem hozott létre Azure Storage-erőforrásokat, a foglalás minden alkalommal érvényes lesz, amikor olyan erőforrást hoz létre, amely megfelel a foglalás feltételeinek. Mindkét esetben a foglalás időtartama közvetlenül a sikeres vásárlás után kezdődik.

## <a name="exchange-or-refund-a-reservation"></a>Foglalás cseréje vagy visszatérítése

A foglalást bizonyos korlátozásokkal kicserélheti vagy visszatérítheti. Ezeket a korlátozásokat a következő szakaszok ismertetik.

Foglalás cseréjéhez vagy visszatérítéséhez keresse meg a foglalás részleteit az Azure Portalon. Válassza **az Exchange** vagy a **Visszatérítés**lehetőséget, és kövesse az utasításokat a támogatási kérelem elküldéséhez. A kérelem feldolgozása után a Microsoft e-mailt küld Önnek a kérelem teljesítésének megerősítésére.

Az Azure-foglalási szabályzatokról az [Azure-foglalások önkiszolgáló cseréje és visszatérítése](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)című témakörben talál további információt.

### <a name="exchange-a-reservation"></a>Foglalás cseréje

A foglalás cseréje lehetővé teszi, hogy a foglalás fel nem használt része alapján arányos visszatérítést kapjon. Ezután alkalmazhatja a visszatérítést egy új Azure Storage-foglalás beszerzési árára.

Nincs korlátozva a cserék száma, amit csinálhatsz. Ezenkívül a cserével kapcsolatban nincs díj. A megvásárolt új foglalásnak legalább nagyobb értékűnek kell lennie, mint az eredeti foglalásból származó arányos egyenlegnek. Az Azure Storage-foglalás csak egy másik Azure Storage-foglalásra váltható, más Azure-szolgáltatásra vonatkozó foglalásra nem.

### <a name="refund-a-reservation"></a>Foglalás visszatérítése

Bármikor lemondhatja az Azure Storage-foglalást. A lemondáskor a foglalás fennmaradó időtartama alapján arányos visszatérítést kap, levonva ebből a 12 százalékos korai felmondási díjat. A maximális visszatérítés évente 50.000 $.

A foglalás azonnali lemondása azonnal megszünteti a foglalást, és a fennmaradó hónapokat visszaküldi a Microsoftnak. A fennmaradó arányos egyenleget, levonva a díjat, visszatérítjük az eredeti vásárlási formába.

## <a name="expiration-of-a-reservation"></a>Foglalás lejárata

Ha egy foglalás lejár, a foglalás keretében használt Azure Storage-kapacitást a fizetős díj díja alapján számlázunk. A foglalások nem újulnak meg automatikusan.

A foglalás lejárta előtt 30 nappal, majd a lejárati dátumon e-mailben értesítést kap. A foglalás által biztosított költségmegtakarítás előnyeinek további kihasználásához újítsa meg azt legkésőbb a lejárati dátumig.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [Mik azok az Azure-foglalások?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [A foglalási kedvezmény alkalmazása az Azure Storage-ra](../../cost-management-billing/reservations/understand-storage-charges.md)
