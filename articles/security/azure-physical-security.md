---
title: Azure létesítményekben, a helyi és a fizikai biztonság |} A Microsoft Docs
description: A cikk ismerteti az Azure-adatközpontokhoz, beleértve a fizikai infrastruktúra, biztonsági és megfelelőségi ajánlatok.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 69a54640ae3b4e71d7782712ad1764babab7104a
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170677"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Azure létesítményekben, a helyi és a fizikai biztonság
Azure áll, amelyek egy [globálisan elosztott adatközpont-infrastruktúrába](https://azure.microsoft.com/global-infrastructure/), több ezer olyan online szolgáltatások támogatása, és világszerte több mint 100 rendkívül biztonságos létesítményekben átfedés.

Az infrastruktúra ahhoz, hogy a felhasználók alkalmazásokat közelebb szerte a világon, megőrzi az adatok fizikai tárolási helye, és átfogó megfelelőségi és bővíthetőségét lehetőségek, ügyfelek számára tervezték. Azure 52 régióval rendelkezik világszerte, és amely 140 országban érhető el.

A régió olyan adatközpontok, amely egy nagy és rugalmas hálózaton keresztül kapcsolódnak egymáshoz. A hálózat tartalomterjesztéshez, terheléselosztás, a redundancia és titkosítási alapértelmezés szerint tartalmazza. Minden más felhőszolgáltatónál több globális régióval az Azure lehetőséget kínál rugalmasan üzembe helyezheti az alkalmazást, szüksége lesz rájuk.

Az Azure-régiók régiócsoportokat alkotnak. Egy adott Azure-régiócsoportban a földrajzi határokon belüli összes régió megfelel a régiócsoport adattárolási hellyel, szuverenitással, jogi szabályozással és rugalmassággal kapcsolatos követelményeinek.

A régiócsoportok lehetővé teszik a speciális adattárolási hellyel kapcsolatos és megfelelőségi igényekkel rendelkező ügyfelek számára, hogy adataikat és alkalmazásaikat a lehető legközelebb tárolják. Földrajzi területek számára is hibatűrő teljes régió hiba keresztül kapcsolatukat a dedikált, nagy kapacitású hálózati infrastruktúra állniuk.

A rendelkezésre állási zónák fizikailag elkülönített helyek egy Azure-régióban. Egyes rendelkezésre állási zónában egy vagy több adatközpont független áramellátással, hűtéssel és hálózati található tevődik össze. A rendelkezésre állási zónák lehetővé teszik futtassa az üzletmenet szempontjából kritikus alkalmazások magas rendelkezésre állást és kis késésű replikációt.

Az alábbi ábra bemutatja, hogyan párokat. az Azure globális infrastruktúrája régiók és rendelkezésre állási zónák a magas rendelkezésre állás, vészhelyreállítás és a biztonsági mentés ugyanazon adatok fizikai tárolási helye határán belül.

![Diagram megjelenítése adatok fizikai tárolási helye határ][1]

A földrajzilag elosztott adatközpontok lehetővé teszi, hogy a Microsoft az ügyfelek, hálózati késés csökkentése érdekében, és lehetővé teszik a georedundáns biztonsági mentési és feladatátvételi közelében kell.

## <a name="physical-security"></a>Fizikai biztonság
A Microsoft tervez, létrehozásával és működtetésével adatközpontok úgy, hogy szigorúan szabályozza a fizikai hozzáférést a területek, ahol az adatok tárolása. A Microsoft tisztában van azzal az adatok védelmének fontosságára, és segít az adatközpontok, az adatokat tartalmazó biztonságos elkötelezte magát. Van egy teljes részlegre Microsoft tervezése, létrehozása és működtetése az Azure támogatási fizikai eszközök számára. Ez a csapat befektettek állapota-a-a legújabb fizikai biztonság fenntartása.

A Microsoft a rétegelt megközelítést fizikai biztonság, adatok és az Adatközpont erőforrások fizikai hozzáférjenek jogosulatlan felhasználók kockázatának csökkentése érdekében vesz igénybe. A Microsoft által felügyelt adatközpontok rendelkeznek széles körű biztosított védelmi réteget: a létesítmény határain az épület határain, az épületen belül, és az Adatközpont emelet a jóváhagyási eléréséhez. Fizikai biztonság a következők:

- **Hozzáférési kérelem és jóváhagyásra.** Hozzáférés az Adatközpont érkezés előtt kell igényelnie. Ön számára meg kell adnia egy érvényes üzleti indoklásának látogatásának, például a megfelelőségi és naplózási célokra. Összes kérést, akkor jóvá kell elérhető alapon Microsoft-alkalmazottak számára. Szükséges elérhető alapján teszi lehetővé, az operációs rendszer nélküli helyreállításra minimális adatközpontokban feladatok elvégzéséhez szükséges személyek számát. Microsoft gyűjteménytelepítési engedélyt biztosít, miután egy egyéni csak hozzáféréssel rendelkezik az adatközpontban, szükség esetén diszkrét területéhez a jóváhagyott üzleti indoklás alapján. Engedélyeket csak egy bizonyos ideig, és ezután lejár.

- **Létesítmény a szegélyhálózat-alapú.** Amikor egy adatközpont érkeznek, akkor haladhat végig egy jól definiált hozzáférési pont kell. Magas kerítések készült acélból, betonból és általában minden, a peremhálózati hüvelyk foglalják magukba. Nincsenek kamerák körül az adatközpontok, azok videóit figyelési mindig egy biztonsági csoporttal.

- **Épület be.** A datacenter bejusson van ellátják a szakmai biztonsági tisztviselők, aki átesett szigorú képzési és a háttér-ellenőrzést. Ezek biztonsági tisztviselők is rendszeresen kijelölése az adatközpontban, és mindig kamerák az adatközponton belül a videók figyelheti.

- **Belül az épületben.** Miután megadta az épület, akkor meg kell adnia a biometria keresztül az Adatközpont áthelyezését a kétfaktoros hitelesítés. Ha az identitás érvényesítve van, csak az engedélyezett hozzáférést adatközpont része is megadhatja. Maradhat, ha nem csak az engedélyezett idő időtartamára.

- **Datacenter emelet.** Csak engedélyezett a emelet arra, hogy már jóváhagyott adja meg. Szükségesek a teljes szervezet nélküli észlelési szűrés át. Adatok illetéktelen be- vagy az adatközpontban, a felhasználó tudta nélkül kockázatának csökkentése érdekében, hogy csak engedélyezett eszközök is eljutnak az Adatközpont emelet be. Ezenkívül videokamera figyelő előre és vissza minden kiszolgáló állványra. Amikor kilép a datacenter emelet, akkor újra meg kell adnia révén a teljes szervezet nélküli észlelési szűrés. Hogy az adatközpontban, módosítania kell egy további biztonsági ellenőrzés továbbítása.

A Microsoft bármely Microsoft-létesítményben az induláskor jelvények lemondani a látogatók van szükség.

## <a name="physical-security-reviews"></a>Fizikai biztonság értékelések
Rendszeres időközönként fizikai biztonsági ellenőrzések megfelelően cím az Azure biztonsági követelményeket az adatközpontok biztosítása érdekében a létesítmények gyakorisággal. Az Adatközpont üzemeltetési szolgáltató csoporthoz nem biztosítanak az Azure service management. Munkatársak Azure rendszerek nem tud bejelentkezni, és nem kell fizikai hozzáféréssel Azure helymegosztási hely és a célra.

## <a name="data-bearing-devices"></a>Adatokat tartalmazó eszközök
A Microsoft használja, gyakorlati ajánlott eljárásokat és a egy wiping megoldás, amely [NIST 800-88 megfelelő](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). Merevlemez-meghajtók, amely nem törölhetők egy megsemmisítését folyamat, amely megsemmisít, és rendereli a helyreállítási információk lehetetlen használ. Ez a folyamat megsemmisítését darabokra, zúzására szolgálnak, pulverize vagy hamvasszuk is lehet. Azt határozza meg, az azt jelenti, hogy az eszköz típusa szerint kivezetési. Azt megőrizni a megsemmisítését rekordjait.  

## <a name="equipment-disposal"></a>Berendezés értékesítés
Esetén a rendszer teljes életciklusa a Microsoft üzemeltetési személyzetének hajtsa végre a szigorú adatkezelési és hardver kivezetési eljárások ahhoz, hogy biztosítsa, hogy az Ön adatait tartalmazó hardver nem szeretné elérhetővé tenni nem megbízható feleknek. Merevlemez-meghajtókat, amelyek támogatják ezt a biztonságos törlése megközelítést használunk. Merevlemez-meghajtók, amely nem törölhetők egy megsemmisítését folyamat, amely a meghajtó megsemmisít, és rendereli a helyreállítási információk lehetetlen használ. Ez a folyamat megsemmisítését darabokra, zúzására szolgálnak, pulverize vagy hamvasszuk is lehet. Azt határozza meg, az azt jelenti, hogy az eszköz típusa szerint kivezetési. Azt megőrizni a megsemmisítését rekordjait. Azure-szolgáltatásokhoz jóváhagyott media tárolási és kivezetési szolgáltatásokat használja.

## <a name="compliance"></a>Megfelelőség
Hogy tervezheti meg és kezelheti az Azure-infrastruktúra számos nemzetközi és iparági megfelelőségi szabványok – például az ISO 27001, a HIPAA, a FedRAMP, a SOC 1 és SOC 2 kielégítése érdekében. Emellett megfelelünk országspecifikus szabványokhoz is, például az ausztrál IRAP, az Egyesült Királyságbeli G-Cloud és a szingapúri MTCS. Szigorú külső naplózások, például a British Standards Institute által ellenőrizze a szigorú biztonsági ellenőrzés megszabják ezeknek a szabványoknak való megfelelést.

Megfelelőségi szabvány, amely Azure betartja teljes listáját lásd: a [megfelelőségi ajánlatok](https://www.microsoft.com/trustcenter/compliance/complianceofferings). 

## <a name="next-steps"></a>További lépések
A Microsoft nem biztonságossá tétele az Azure-infrastruktúra kapcsolatos további információkért lásd:

- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure information rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [Az Azure SQL Database biztonsági funkciók](azure-infrastructure-sql.md)
- [Azure éles környezetben való üzemeltetés és a felügyelet](azure-infrastructure-operations.md)
- [Azure-infrastruktúra figyelése](azure-infrastructure-monitoring.md)
- [Az Azure infrastruktúra-integritás](azure-infrastructure-integrity.md)
- [Az Azure vásárlói adatok védelmére](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-physical-security/data-residency-boundary.png
