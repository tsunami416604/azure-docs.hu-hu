---
title: Az Azure létesítményekben, a helyszíni és a fizikai biztonság |} Microsoft Docs
description: A cikk ismerteti az Azure adatközpontjaiban, beleértve a fizikai infrastruktúra, biztonsági és megfelelőségi ajánlatokat.
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
ms.openlocfilehash: a6a9b1d6e12dabb09cde684c34481b4b3442c1b8
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102274"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Az Azure létesítményekben, a helyszíni és a fizikai biztonság
A Microsoft cloud magában foglalja a [globálisan elosztott adatközpont infrastruktúrájában](https://azure.microsoft.com/global-infrastructure/) online szolgáltatások ezer támogatása, és több mint 100 magas átfedés biztonságos létesítményekben világszerte.

Az infrastruktúra készült alkalmazások közelebb a felhasználók számára a világ megőrzi az adatokat rezidens, és az ügyfelek átfogó megfelelőség és a rugalmasság beállításai az ajánlat kapcsolja. Azure 52 régiók világszerte rendelkezik, és 140 országok áll rendelkezésre.

A régió olyan adatközpontok összekapcsolt egy rugalmas és nagy a hálózaton keresztül. A hálózaton olyan terjessze a tartalmakat, az terheléselosztás, a redundancia és a titkosítás alapértelmezés szerint. A minden más felhőszolgáltatónál több globális régióval rendelkező Azure rugalmassága révén az ügyfelek az igényeiknek megfelelő helyen helyezhetik üzembe alkalmazásaikat.

Az Azure-régiók régiócsoportokat alkotnak. Egy adott Azure-régiócsoportban a földrajzi határokon belüli összes régió megfelel a régiócsoport adattárolási hellyel, szuverenitással, jogi szabályozással és rugalmassággal kapcsolatos követelményeinek.

A régiócsoportok lehetővé teszik a speciális adattárolási hellyel kapcsolatos és megfelelőségi igényekkel rendelkező ügyfelek számára, hogy adataikat és alkalmazásaikat a lehető legközelebb tárolják. Földrajzi hibatűrő állnia teljes régió sikertelen a dedikált nagy kapacitású hálózati infrastruktúra a kapcsolaton keresztül.

A rendelkezésre állási zónák fizikailag elkülönített helyek egy adott Azure-régión belül. Minden rendelkezésre állási zónában egy vagy több független áramellátással, hűtéssel és hálózattal rendelkező adatközpont található. A rendelkezésre állási zónák lehetővé teszik az ügyfelek számára, hogy kritikus fontosságú alkalmazásaikat magas rendelkezésre állást és kis késésű replikációt biztosítva futtassák.

Az alábbi ábra bemutatja, hogyan az Azure-globális infrastruktúra párokat régió és a rendelkezésre állási zónák a magas rendelkezésre állás, a vész-helyreállítási és a biztonsági mentés ugyanazon adatok rezidens határon belül.

![Adatok rezidens határ][1]

Egy nagy földrajzi elosztott erőforrásigényét adatközpontok lehetővé teszi, hogy a Microsoft közeli ahhoz, hogy a hálózati késés csökkentésére, és lehetővé teszik a georedundáns biztonsági mentési és feladatátvételi ügyfelek.

## <a name="physical-security"></a>Fizikai biztonság
Microsoft tervez, épít fel és adatközpontok oly módon, hogy feltétlenül szabályozza a fizikai hozzáférést a felhasználói adatokat tároló területek működik. A Microsoft tisztában van azzal a felhasználói adatok védelmének fontosságára, és segíti az adatközpontokban a adatokat tartalmazó biztonságos elkötelezettek. A Microsoft számára tervezési, kiépítési és a fizikai eszközök Azure támogató operációs van a teljes osztályok. A csapat befektettek-az-a-legkorszerűbb fizikai biztonság fenntartásának.

A Microsoft a réteges megközelítésének jogosulatlan felhasználók hozzáférjenek fizikai adatok és az Adatközpont-erőforrásaikat kockázatának csökkentéséhez fizikai biztonsági vesz igénybe. A Microsoft által felügyelt adatközpontok védelmi kiterjedt réteggel rendelkeznek: a létesítmény határain az épület határain, az épület belül, és a datacenter emelet a jóváhagyás eléréséhez. Fizikai biztonsági réteg alkalmazása a következők:

- Hozzáférési kérelem és a jóváhagyási – előtt az Adatközpont érkező kell igényelnie. Most számára meg kell adnia egy érvényes üzleti indoklásának a helyezést, például a megfelelőségi és naplózási célokra. Összes kérelmet a Microsoft alkalmazottai által jóváhagyott szükséges hozzáférés alapú. Szükséges elérhető alapján segít, hogy az operációs rendszer minimális adatközpontjaiban a feladatok elvégzéséhez szükséges személyek számát. Ha az engedélyt, egy adott csak hozzáfér az adatközpontban, a jóváhagyott üzleti indoklásának alapján diszkrét területére. Engedélyeket csak egy bizonyos ideig, és az engedélyezett idő elteltével lejár.

- Intézmény szegélyhálózati - megérkezik egy adatközpontban, a is szükséges egy jól meghatározott hozzáférési ponton keresztül. Általában magas kerítések acélból és tényleges névtérkiszolgálóit is, a peremhálózati minden hüvelyk. Nincsenek kamerák körül az adatközpontokban a videók 24/7 és 365 nap év figyelési egy biztonsági csoporttal.

- Épület be – a datacenter be van ellátják szakemberek biztonsági tisztviselő, aki átesett szigorú képzési és a háttér-ellenőrzést. A biztonsági tisztviselő is rendszeresen kijelölése az adatközpontban, is figyelik a videók kamerák belül a datacenter 24/7 és év során.

- Épület - belül az épület megadása után át kell adnia biometria keresztül az Adatközpont áthelyezését a kéttényezős hitelesítés. Ha a identitásának érvényességéről, megadhatja az adatközponthoz, amely jóváhagyta a hozzáférést, az a része. Megtudhatja, hogy csak az az időtartam, a jóváhagyott.

- Datacenter emelet – akkor engedélyezett a emelet be, hogy most jóváhagyott adja meg. Szükségesek felelt meg a teljes szervezet nélküli észlelési szűrés. Az adatok illetéktelen be- vagy az Adatközpont nélkül a Tudásbázis a kockázatok csökkentése érdekében csak az engedélyezett eszközök is ne a datacenter emelet be. Emellett videó kamerák figyelő az első és vissza minden kiszolgáló állványra. Teljes törzs nélküli észlelési fájlszűrési ismétlődik a datacenter emelet kilép. Hogy az adatközpontban, módosítania kell egy további biztonsági ellenőrzés továbbítása.

A Microsoft-létesítményben induláskor jelvények lemondani látogatók szükségesek.

## <a name="physical-security-reviews"></a>Ellenőrzi, hogy fizikai biztonság
A létesítmények fizikai biztonsági értékelést rendszeresen végez annak megfelelő biztosítása az adatközpontokban cím a Microsoft Azure biztonsági követelményeinek. Az Adatközpont üzemeltetési szolgáltató csoporthoz nem szerepelnek a Microsoft Azure service management. Személyzet nem rendelkezik Azure rendszerek elérését, vagy az Azure közös elhelyezés helyiségben és célra való fizikai hozzáférés.

## <a name="data-bearing-devices"></a>Adatokat tartalmazó eszközök
A Microsoft ajánlott ajánlott eljárások és egy wiping megoldás, amely használja [NIST 800-88 megfelelő](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). A merevlemez-meghajtók, amely nem törölhetők egy megsemmisítése folyamat, amely megsemmisít azt, és ez a beállítás a helyreállítási információk lehetetlen használatos. Megsemmisítése folyamat esetleg darabokra, zúzására szolgálnak, pulverize, vagy hamvasszuk. A megfelelő módon történő elhelyezésének az eszköz típusa határozza meg. Rögzíti a bontási megmaradnak.  

## <a name="equipment-disposal"></a>Berendezések kivezetési
A Microsoft Azure ezen elv megvalósítja az ügyfeleik nevében. Esetén a rendszer záró életciklusa a Microsoft műveleti személyzet kövesse a szigorú adatkezeléssel és hardver kivezetési eljárások ahhoz, hogy biztosítsa, hogy felhasználói adatokat tartalmazó hardver nem legyen elérhető a nem megbízható entitások számára. A biztonságos törlése megközelítés (keresztül merevlemez-meghajtó belső vezérlőprogramját) esetén, amelyek támogatják ezt követi. A merevlemez-meghajtók, amely nem törölhetők egy megsemmisítése folyamat, amely megsemmisít azt, és ez a beállítás a helyreállítási információk lehetetlen használatos. Megsemmisítése folyamat esetleg darabokra, zúzására szolgálnak, pulverize, vagy hamvasszuk. A megfelelő módon történő elhelyezésének az eszköz típusa határozza meg. Rögzíti a bontási megmaradnak. Minden Microsoft Azure-szolgáltatások jóváhagyott media tárolási és kivezetési szolgáltatások használhatók.

## <a name="compliance"></a>Megfelelőség
Az Azure-infrastruktúra kialakításának és megfelelő nemzetközi és az iparág-specifikus megfelelőségi követelményeket, például ISO 27001, a HIPAA, a FedRAMP, a SOC 1 és a SOC 2 széles körét kezeli. Ország-specifikus szabványok is teljesül, beleértve a Ausztrália IRAP, UK G-felhő és szingapúri MTCS. Különböző külső szervezetek – többek között a British Standards Institute – mélyreható vizsgálatok keretében ellenőrzik, hogy az Azure megfelel-e a szabványok által előírt szigorú biztonsági szabályozásnak.

Tekintse meg a [megfelelőségi ajánlatok](https://www.microsoft.com/trustcenter/compliance/complianceofferings) az Azure által elfogadott megfelelőségi követelményeket teljes listáját.

## <a name="next-steps"></a>További lépések
Microsoft funkciója az Azure-infrastruktúra védelméhez kapcsolatos további információkért lásd:

- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure információk rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [A Microsoft Azure SQL Database biztonsági funkciói](azure-infrastructure-sql.md)
- [Az Azure éles műveletek és kezelése](azure-infrastructure-operations.md)
- [Azure-infrastruktúra megfigyelése](azure-infrastructure-monitoring.md)
- [Azure-infrastruktúra integritása](azure-infrastructure-integrity.md)
- [Az Azure-ban felhasználói adatok védelme](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-physical-security/data-residency-boundary.png
