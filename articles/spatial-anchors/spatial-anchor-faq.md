---
title: Azure térbeli horgonyok kapcsolatos gyakori kérdések |} A Microsoft Docs
description: Az Azure térbeli horgonyok egy felügyelt felhőalapú szolgáltatás, és a fejlesztői platform, amely lehetővé teszi az eszközök közötti, a többfelhasználós, vegyes valóságot élmény HoloLens, iOS és Android-eszközök között. Ezek a gyakori kérdések a szolgáltatással kapcsolatos kérdésekre cím technikai szempontból.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: d38f7a22d90c62b245e49e6bdb0a3041c4f1023f
ms.sourcegitcommit: 68ba7916a6ff8dd40f5037e57059733d74374954
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753593"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Azure térbeli horgonyok kapcsolatos gyakori kérdések

Azure térbeli horgonyok egy felügyelt felhőszolgáltatás, amely HoloLens, az iOS és Android-eszközök észlel, amely lehetővé teszi több felhasználó, kijelölése figyelembe vegyes valóság fejlesztői platform.

További információkért lásd: [Azure térbeli kapcsolatok alapjainak áttekintése](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>Az Azure térbeli horgonyok termék – gyakori kérdések

**K: Mely eszközöket támogatja az Azure térbeli horgonyok?**

**VÁLASZ:** Az Azure térbeli horgonyok segítségével a fejlesztők alkalmazásokat készíthet a HoloLens, az iOS-eszközökön ARKit támogatással, és az Android-eszközök ARCore támogatás; iOS és Android rendszerhez készült Ez magában foglalja a telefonokon és táblagépeken egyaránt.

**K: Kell kapcsolódnia kell a felhőben Azure térbeli horgonyok használni?**

**VÁLASZ:** Az Azure térbeli horgonyok jelenleg Internet hálózati kapcsolat szükséges. Szívesen fogadjuk visszajelzését a a [visszajelzési webhelyet](https://feedback.azure.com/forums/919252-azure-spatial-anchors).

**K: Mik az Azure térbeli horgonyok kapcsolati követelményei?**

**VÁLASZ:** Az Azure térbeli horgonyok Wi-Fi és a mobil szélessávú kapcsolat működik.

**K: Pontosan hogyan keresheti meg az Azure térbeli horgonyok horgonyok?**

**VÁLASZ:** Sok tényező befolyásolja a horgonyok--fényviszonyok, a környezetet, és még a felület, amelyen a horgony kerül az objektumok keresése pontosságát. Annak megállapításához, hogy ha pontossága felel meg az igényeinek, próbálja ki a horgonyok a környezetek képviselője tervezi használni őket. Ha a környezetekben, ahol pontossága nem felel meg az igényeinek hibát tapasztal, tekintse meg [naplózás és diagnosztika Azure térbeli horgonyok](./concepts/logging-diagnostics.md).

**K: Mennyi ideig tart hozhat létre, és keresse meg a központi jellegűek?**

**VÁLASZ:** A hozhat létre, és keresse meg a központi jellegűek szükséges idő az--hálózati kapcsolat, az eszköz feldolgozási és terheléselosztást és az adott környezetben számos tényezőtől függ. Gyártási, a kiskereskedelmi és a játékok, amely jelzi, hogy a szolgáltatás lehetővé teszi, hogy azok a forgatókönyvek nagyszerű felhasználói élményt többek között számos iparágban alkalmazásokat fejlesztő ügyfelek van.

## <a name="privacy-faq"></a>Adatvédelem – gyakori kérdések

**K: Ha az alkalmazásom egy térbeli kapcsolati alapot valahol minden alkalmazás rendelkezik hozzáféréssel?**

**VÁLASZ:** Horgonyok legyenek különítve az Azure-fiók használatával. Amelyhez hozzáférést adhat a fiók csak alkalmazások fog tudni hozzáférni horgonyok a fiókon belül.

**K: Továbbított adatok köre és a szolgáltatásban tárolt Azure térbeli horgonyok használatakor egy környezetre vonatkozó információk? A környezet képek továbbított adatok köre és tárolt?**

**A**: Létrehozásakor, vagy horgonyok megkeresése, a környezet képek dolgozza fel az eszközön olyan származtatott formátumra. A származtatott formátumban továbbított és a szolgáltatásban tárolt.

Adja meg az átláthatóság, az alábbi, környezet és a származtatott újraelemzési pont felhő képe. Pont felhőbeli a környezetben, a szolgáltatásban tárolt és továbbított adatok köre geometriai reprezentációja látható. Az egyes újraelemzési pont felhőbeli azt továbbítja, és egy kivonatot az adott pont a vizuális jellemzőinek tárolja. A kivonatoló származik, de nem tartalmaz, képpontos adatokat.

Az Azure térbeli horgonyok betartja a [Azure szolgáltatási szerződés feltételeinek](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9), és a [Microsoft adatvédelmi nyilatkozatát](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409).

![Környezet és a származtatott újraelemzési pont felhőalapú](./media/sparce-point-cloud.png)
*1. ábra: Környezet és a származtatott újraelemzési pont felhő*


**K: Van mód a küldhetők diagnosztikai adatokat a Microsoftnak?**

**A**: Igen. Az Azure térbeli horgonyok rendelkezik egy diagnosztikai üzemmódra, amelyeket a fejlesztők is választható, ha az Azure térbeli horgonyok API-n keresztül. Ez akkor hasznos, például ha egy környezetet, ahol nem hozhat létre, és keresse meg a központi jellegűek kiszámítható módon tudja. Előfordulhat, hogy megkérjük, ha tartalmazó segíti a hibakeresés diagnosztikai jelentést küldhet. További információ: [naplózás és diagnosztika Azure térbeli horgonyok](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Rendelkezésre állás és a díjszabás – gyakori kérdések

**K: Biztosítanak SLA-t?**

**VÁLASZ:** Az Azure-szolgáltatásokat standard szintű, most a cél egy rendelkezésre állási 99,9 %-nál nagyobb. Vegye figyelembe, hogy az Azure térbeli horgonyok jelenleg előzetes verzióban érhető el, és mint ilyen a [előzetes verziókra vonatkozó kiegészítő feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) vonatkoznak.

**K: Közzéteheti a saját alkalmazások használata az Azure térbeli horgonyok alkalmazás-áruházak? Használhatok Azure térbeli horgonyok alapvető fontosságú üzemi forgatókönyvek esetén?**

**VÁLASZ:** Az Azure térbeli horgonyok jelenleg előzetes verzióban érhető el, és ebben az időszakban Felkérjük, hogy fejleszthet alkalmazásokat, [visszajelzést](https://feedback.azure.com/forums/919252-azure-spatial-anchors) a termék, és a tervet az éles környezetekben üzemelő példányok.

Általános elérhető (GA) dátumok hamarosan fogjuk bejelenteni.

**K: Van bármilyen szabályozási korlátok helyen?**
 
**A**: Igen, rendelkezünk szabályozási korlátait.  Várhatóan nem el fog érni őket az alkalmazások fejlesztéséhez és teszteléséhez. Éles környezetekben üzemelő példányok hogy vevőink nagy méretű memóriakonfigurációt készen áll. [Lépjen kapcsolatba velünk](mailto:azuremrs@microsoft.com) vitatni. Ebben a fázisban előzetes verziójának azt még nem tette közzé a rétegezést és díjszabási struktúrája, de várhatóan hamarosan ehhez.

**K: Mely régiókban érhető el az Azure térbeli horgonyok?**

**VÁLASZ:** Létrehozhat egy térbeli horgonyok Azure-fiókot még ma az Azure keleti régiója 2 régiójában. Ez azt jelenti, hogy mind a számítási és tárolási, ez a szolgáltatás működtetésére ebben a régióban. Ugyanakkor nem vonatkoznak korlátozások a, ahol az ügyfelek találhatók. A későbbiekben bővíteni fogjuk a szolgáltatás regionális elérhetősége elsődleges Azure-régióban.

**K: Tegye meg a térbeli horgonyok Azure díjat? Minden eddiginél díja?**

**VÁLASZ:** Előzetes verzió ideje alatt a díjszabással kapcsolatos információk a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/spatial-anchors/).

## <a name="technical-faqs"></a>Gyakori technikai kérdések

**K: Hogyan működik az Azure térbeli horgonyok?**

**VÁLASZ:** Az Azure térbeli horgonyok függ a vegyes valóságon alapuló / kibővített valóság követő. Ezeket követő mennyire fogja világosnak látni a környezet kamera, és nyomon követheti az eszközre a 6-fok-az-szabadon (6DoF), ahogy azt a területet továbbítanak.

Adja meg egy 6DoF tracker építőelemként, Azure térbeli horgonyok lehetővé teszi, hogy a valós környezetben hasznos helyek egyes pontok "forráshorgony" pontként. Előfordulhat, hogy, használhatja például horgonyra megjelenítése egy meghatározott helyen a való életből vett a tartalmat.

Horgonyra hoz létre, amikor az ügyfél-SDK rögzíti a környezeti információk a pont körül, és továbbítja azt a szolgáltatást. Ha egy másik eszköz figyeli a nem az, hogy ugyanaz a hely a horgony, hasonló adatokat a szolgáltatásnak továbbítja. Az adatok egyezik a környezet korábban tárolt adatokon. A horgony az eszköz viszonyított pozícióját vissza a használatra, az alkalmazás elküldi.

**K: Hogyan nem térbeli horgonyok Azure integrálható ARKit és ARCore iOS és Android rendszeren?**

**VÁLASZ:** Az Azure térbeli horgonyok ARKit és ARCore natív követési funkcióit kihasználva képes az. Emellett az SDK-Ink IOS és Android kínálnak képességek között megtalálható például egy felügyelt felhőszolgáltatásban horgonyok megőrzése, és lehetővé teszi az alkalmazások ismét keresse meg ezeket a horgonyok egyszerűen csatlakozik a szolgáltatáshoz.

**K: Hogyan integrálható az Azure térbeli horgonyok HoloLens?**

**VÁLASZ:** HoloLens natív követési funkcióit kihasználva képes az Azure térbeli horgonyok. HoloLens található alkalmazások létrehozására vonatkozó biztosítunk egy Azure térbeli horgonyok SDK-t. Az SDK-t integrálja a natív HoloLens-képességekkel rendelkező, és olyan kiegészítő képességeket biztosít. Ezek a képességek lehetővé teszi az alkalmazásfejlesztők egy felügyelt felhőszolgáltatásban horgonyok megőrizni, és lehetővé teszi az alkalmazások kereséséhez ezen kapcsolatok alapjainak újra csatlakozik a szolgáltatáshoz.

**K: Mely platformok és nyelvek Azure térbeli horgonyok támogatja?**

**VÁLASZ:** A fejlesztők olyan alkalmazásokat hozhatnak létre az Azure térbeli horgonyok ismerős eszközök és keretrendszerek használatával az eszközén:

- HoloLens, az iOS és Android Unity
- A SWIFT vagy Objective-C iOS rendszeren
- A Java vagy az Android az Android Szünettel
- C + +/ a HoloLens WinRT

Ismerkedés a [fejlesztesz](index.yml).

**K: Működik az Unreal?**

**VÁLASZ:** Terveink szerint már Unreal támogatása hamarosan rendelkeznie.

**K: Működik a Xamarin?**

**VÁLASZ:** Igen. Bár a Microsoft nem biztosít egy Xamarin SDK, várhatóan a fejlesztők a térbeli horgonyok Azure Xamarin-alkalmazásokban való integráció révén az Azure térbeli horgonyok API.
