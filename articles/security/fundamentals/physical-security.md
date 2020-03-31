---
title: Az Azure-adatközpontok fizikai biztonsága – Microsoft Azure | Microsoft dokumentumok
description: A cikk ismerteti, hogy a Microsoft mit tesz az Azure-adatközpontok védelme érdekében, beleértve a fizikai infrastruktúrát, a biztonságot és a megfelelőségi ajánlatokat.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 634f89b3123902d981ad07dd1404315387104322
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726729"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Az Azure létesítményei, helyiségei és fizikai biztonsága
Ez a cikk ismerteti, hogy a Microsoft mit tesz az Azure-infrastruktúra védelme érdekében.

## <a name="datacenter-infrastructure"></a>Adatközpont-infrastruktúra
Az Azure egy [globálisan elosztott adatközpont-infrastruktúrából](https://azure.microsoft.com/global-infrastructure/)áll, amely több ezer online szolgáltatást támogat, és világszerte több mint 100 rendkívül biztonságos létesítményt ölel fel.

Az infrastruktúrát úgy alakították ki, hogy az alkalmazásokat világszerte közelebb hozza a felhasználókhoz, megőrizve az adatok tárolási helyét, és átfogó megfelelőségi és rugalmassági lehetőségeket kínálva az ügyfelek számára. Az Azure világszerte 52 régióval rendelkezik, és 140 országban/régióban érhető el.

A régió olyan adatközpontok készlete, amelyek egy masszív és rugalmas hálózaton keresztül kapcsolódnak össze. A hálózat magában foglalja a tartalomelosztást, a terheléselosztást, a redundanciát és a titkosítást. A bármely más felhőszolgáltatónál több globális régióval rendelkező Azure rugalmasságot biztosít az alkalmazások ott történő üzembe helyezéséhez, ahol szükség van rájuk.

Az Azure-régiók földrajzi területek szerint vannak rendezve. Az Azure-földrajzi biztosítja, hogy az adatok tartózkodási, szuverenitási, megfelelőségi és rugalmassági követelmények tiszteletben tartása földrajzi határokon belül.

A régiócsoportok lehetővé teszik, hogy a speciális adattárolási és megfelelőségi igényekkel rendelkező ügyfelek földrajzilag közel tárolhassák adataikat és alkalmazásaikat. A földrajzi területek hibatűrőek, hogy ellenálljanak a teljes régióhibának a dedikált, nagy kapacitású hálózati infrastruktúrához való csatlakozásuk révén.

A rendelkezésre állási zónák fizikailag különálló helyek egy Azure-régióban. Az egyes rendelkezésre állási zónák egy vagy több, önálló áramellátással, hűtéssel, és hálózattal rendelkező adatközpontból állnak. A rendelkezésre állási zónák lehetővé teszik a kritikus fontosságú alkalmazások futtatását magas rendelkezésre állású és alacsony késleltetésű replikációval.

Az alábbi ábra bemutatja, hogy az Azure globális infrastruktúra párok régió és a rendelkezésre állási zónák ugyanazon az adattárolási határon belül a magas rendelkezésre állás, a vész-helyreállítási és biztonsági mentés.

![Adatrezidensségi határt bemutató diagram](./media/physical-security/data-residency-boundary.png)

A földrajzilag elosztott adatközpontok lehetővé teszik a Microsoft számára, hogy közel legyen az ügyfelekhez, csökkentse a hálózati késést, és lehetővé tegye a georedundáns biztonsági mentést és a feladatátvételt.

## <a name="physical-security"></a>Fizikai biztonság
A Microsoft úgy tervez, épít és működtet adatközpontokat, hogy szigorúan szabályozza az adatok tárolására alkalmas területekhez való fizikai hozzáférést. A Microsoft tisztában van az adatok védelmének fontosságával, és elkötelezett az adatokat tartalmazó adatközpontok védelme mellett. A Microsoftnál egy teljes részleg foglalkozik az Azure-t támogató fizikai létesítmények tervezésével, építésével és működtetésével. Ez a csapat a legkorszerűbb fizikai biztonság fenntartásába fektet.

A Microsoft a fizikai biztonság réteges megközelítését alkalmazza, hogy csökkentse annak kockázatát, hogy illetéktelen felhasználók fizikai hozzáféréssel férjenek hozzá az adatokhoz és az adatközpont erőforrásaihoz. A Microsoft által kezelt adatközpontok kiterjedt védelmi rétegekkel rendelkeznek: hozzáférés-jóváhagyás a létesítmény kerületén, az épület kerületén, az épületen belül és az adatközpont padlóján. A fizikai biztonság rétegei a következők:

- **Hozzáférési kérelem és jóváhagyás.** Az adatközpontba való megérkezés előtt hozzáférést kell kérnie. A látogatáshoz érvényes üzleti indoklást kell megadnia, például megfelelőségi vagy naplózási célokat. Minden kérelmet a Microsoft alkalmazottai a szükséges hozzáférés alapján hagynak jóvá. A szükséges hozzáférés alapja segít megőrizni a feladatok elvégzéséhez szükséges személyek száma az adatközpontokban, hogy a lehető legkisebb legyen. Miután a Microsoft engedélyt adott, az egyén csak a jóváhagyott üzleti indoklás alapján férhet hozzá a szükséges adatközpont különálló területéhez. Az engedélyek egy bizonyos időtartamra korlátozódnak, majd lejárnak.

- **A létesítmény környéke.** Amikor megérkezik egy adatközpontba, egy jól definiált hozzáférési ponton kell keresztülmennie. Jellemzően, magas kerítések acélból és betonból magában foglalja minden négyzetcentiméterét a kerületét. Az adatközpontok körül kamerák vannak, és egy biztonsági csapat mindig figyeli a videóikat.

- **Épület bejárata.** Az adatközpont bejárata professzionális biztonsági tisztviselőkkel van eljárva, akik szigorú képzésen és háttérellenőrzésen estek át. Ezek a biztonsági tisztviselők is rendszeresen járőröznek az adatközpontban, és folyamatosan figyelik az adatközponton belüli kamerák videóit.

- **Az épületen belül.** Miután belépett az épületbe, át kell adnia a kétfaktoros hitelesítést biometriával, hogy tovább haladjon az adatközpontban. Ha az identitás érvényesítve van, megadhatja csak az adatközpontnak azt a részét, amelyhez jóváhagyott hozzáférést engedélyezett. Csak a jóváhagyott idő alatt tartózkodhat ott.

- **Az adatközpont emeletén.** Csak arra a padlóra léphetsz, ahhoz, hogy beléphessen. Meg kell felelnie a teljes test fém detektálás szűrés. Annak érdekében, hogy csökkentsék annak kockázatát, hogy a jogosulatlan adatok a tudomásunk nélkül belépjenek vagy elhagyják az adatközpontot, csak a jóváhagyott eszközök léphetnek be az adatközpont padlójára. Ezenkívül a videokamerák minden szerverállvány elejét és hátulját figyelik. Amikor kilép az adatközpont padlójáról, ismét át kell haladnia a teljes testfém detektálási szűrésen. Az adatközpont elhagyásához át kell haladnia egy további biztonsági ellenőrzésen.

A Microsoft megköveteli a látogatóktól, hogy a Microsoft bármely létesítményéből való távozáskor adják át a jelvényeket.

## <a name="physical-security-reviews"></a>Fizikai biztonsági felülvizsgálatok
Rendszeres időközönként fizikai biztonsági felülvizsgálatokat végzünk a létesítményekről, hogy az adatközpontok megfelelően megfeleljenek az Azure biztonsági követelményeinek. Az adatközpont-tárhelyszolgáltató személyzete nem biztosít Azure-szolgáltatáskezelést. A személyzet nem tud bejelentkezni az Azure-rendszerekbe, és nem rendelkezik fizikai hozzáféréssel az Azure helymegosztási helyiséghez és ketrecekhez.

## <a name="data-bearing-devices"></a>Adatcsapágy-eszközök
A Microsoft a legjobb gyakorlati eljárásokat és a [NIST 800-88 szabványnak megfelelő](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01)törlési megoldást használja. A merevlemezek, hogy nem lehet törölni, használjuk a megsemmisítési folyamat, amely elpusztítja azt, és teszi a helyreállítási információ lehetetlen. Ez a megsemmisítési folyamat lehet szétesik, foszlány, porrá, vagy hamvasztás. Az elidegenítés módját az eszköz típusa szerint határozzuk meg. Nyilvántartást vezetünk a pusztításról.  

## <a name="equipment-disposal"></a>Berendezések ártalmatlanítása
A rendszer életciklusának végét követően a Microsoft operatív személyzete szigorú adatkezelési és hardverártalmatlanítási eljárásokat követ annak biztosítása érdekében, hogy az ön adatait tartalmazó hardverek ne legyenek a nem megbízható felek rendelkezésére. Biztonságos törlési megközelítést alkalmazunk az azt támogató merevlemezekhez. A merevlemezek, hogy nem lehet törölni, az általunk használt megsemmisítési folyamat, amely elpusztítja a meghajtót, és teszi a helyreállítási információ lehetetlen. Ez a megsemmisítési folyamat lehet szétesik, foszlány, porrá, vagy hamvasztás. Az elidegenítés módját az eszköz típusa szerint határozzuk meg. Nyilvántartást vezetünk a pusztításról. Minden Azure-szolgáltatás jóváhagyott médiatárolási és -ártalmatlanítási szolgáltatásokat használ.

## <a name="compliance"></a>Megfelelőség
Az Azure-infrastruktúrát úgy tervezzük és kezeljük, hogy megfeleljen a nemzetközi és iparág-specifikus megfelelőségi szabványok széles körének, például az ISO 27001, a HIPAA, a FedRAMP, a SOC 1 és az SOC 2 szabványnak. Ország- vagy régióspecifikus szabványoknak is megfelelünk, beleértve az Ausztráliai IRAP- t, az Egyesült KirályságG-Felhőjét és a szingapúri MTCS-t. Szigorú harmadik fél által végzett ellenőrzések, mint például a British Standards Institute, ellenőrizze, hogy betartja a szigorú biztonsági ellenőrzések ezek a szabványok megbízást.

Az Azure által betartott megfelelőségi szabványok teljes listáját a [Megfelelőségi ajánlatok című témakörben](https://www.microsoft.com/trustcenter/compliance/complianceofferings)található.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni arról, hogy a Microsoft mit tesz az Azure-infrastruktúra védelme érdekében, olvassa el a következő témakört:

- [Az Azure-infrastruktúra rendelkezésre állása](infrastructure-availability.md)
- [Az Azure információs rendszer összetevői és határai](infrastructure-components.md)
- [Azure hálózati architektúra](infrastructure-network.md)
- [Azure éles hálózat](production-network.md)
- [Az Azure SQL Database biztonsági szolgáltatásai](infrastructure-sql.md)
- [Az Azure éles üzemei és kezelése](infrastructure-operations.md)
- [Az Azure-infrastruktúra figyelése](infrastructure-monitoring.md)
- [Az Azure infrastruktúra integritása](infrastructure-integrity.md)
- [Az Azure-beli ügyfelek adatainak védelme](protection-customer-data.md)


