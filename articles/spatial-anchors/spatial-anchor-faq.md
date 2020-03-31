---
title: Gyakori kérdések
description: Gyakori kérdések az Azure Spatial Anchors szolgáltatásról.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 563e2da8eba228636b05db2112739fdead4a4aa3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76844887"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Gyakori kérdések az Azure Spatial Anchors-ról

Az Azure Spatial Anchors egy felügyelt felhőszolgáltatás és fejlesztői platform, amely lehetővé teszi a többfelhasználós, térbelileg tudatos vegyes valóságélményeket a HoloLens, az iOS és az Android eszközökön.

További információ: [Azure Spatial Anchors overview](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>Azure Spatial Anchors termék – gyakori kérdések

**K: Mely eszközöket támogatja az Azure Spatial Anchors?**

**A.** Az Azure Spatial Anchors lehetővé teszi a fejlesztők számára, hogy alkalmazásokat építsenek a HoloLensen, az ARKit-támogatással rendelkező iOS-eszközökön és az ARCore-támogatással rendelkező Android-eszközökön; iOS és Android esetén ez magában foglalja a telefonokat és a táblagépeket is.

**K: Az Azure Spatial Anchors használatához csatlakozni kell a felhőhöz?**

**A.** Az Azure Spatial Anchors jelenleg internetkapcsolatra van szükség. Üdvözöljük észrevételeit a [visszajelzés oldalon](https://feedback.azure.com/forums/919252-azure-spatial-anchors).

**K: Mik az Azure Spatial Anchors kapcsolódási követelményei?**

**A.** Az Azure Spatial Anchors Wi-Fi és mobil szélessávú kapcsolatokkal működik.

**K: Milyen pontosan tudja az Azure Spatial Anchors horgonyok?**

**A.** Számos tényező befolyásolja a horgonyok helytmegtalálásának pontosságát - a megvilágítási feltételeket, a környezetben lévő tárgyakat, és még azt a felületet is, amelyre a horgonyt helyezik. Annak megállapításához, hogy a pontosság megfelel-e az igényeinek, próbálja ki a horgonyokat olyan környezetekben, amelyek képviselik, hogy hol kívánja használni őket. Ha olyan környezeteket tapasztal, ahol a pontosság nem felel meg az igényeinek, olvassa [el a Naplózás és diagnosztika az Azure Spatial Anchors alkalmazásban című témakört.](./concepts/logging-diagnostics.md)

**K: Mennyi ideig tart a horgonyok létrehozása és megkeresése?**

**A.** A horgonyok létrehozásához és megkereséséhez szükséges idő számos tényezőtől függ : a hálózati kapcsolattól, az eszköz feldolgozásától és terhelésétől, valamint az adott környezettől. Ügyfeleink számos iparágban építenek alkalmazásokat, beleértve a gyártást, a kiskereskedelmet és a játékot, jelezve, hogy a szolgáltatás nagyszerű felhasználói élményt tesz lehetővé a forgatókönyveikhez.

## <a name="privacy-faq"></a>Adatvédelmi gyakori kérdések

**K: Amikor az alkalmazásom elhelyez egy térbeli horgonyt valahol, minden alkalmazás hozzáférhet hozzá?**

**A.** A horgonyokat az Azure-fiók elkülöníti. Csak azok az alkalmazások férhetnek hozzá a fiókon belüli horgonyokhoz, amelyekhez hozzáférést biztosít a fiókjához.

**K: Milyen információkat egy környezetben a szolgáltatás továbbítása és tárolása a szolgáltatás használata esetén az Azure Spatial Anchors? Továbbítják és tárolják a környezetről készült képeket?**

**A**: Horgonyok létrehozásakor vagy helytkeresésekor a környezetről készült képeket az eszközön származtatott formátumban dolgozzák fel. Ezt a származtatott formátumot a szolgáltatás továbbítja és tárolja.

Az átláthatóság biztosítása érdekében az alábbiakban egy környezet és a származtatott ritka pontfelhő képe látható. A pontfelhő a szolgáltatáson továbbított és tárolt környezet geometriai ábrázolását mutatja. A ritka pontfelhő minden egyes pontjához továbbítjuk és tároljuk az adott pont vizuális jellemzőinek kivonatát. A kivonat képpontadatokból származik, de nem tartalmaz.

Az Azure Spatial Anchors betartja az [Azure szolgáltatási szerződési feltételeit](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9)és a Microsoft [adatvédelmi nyilatkozatát.](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409)

![Egy környezet és annak származtatott](./media/sparse-point-cloud.png)
ritkapont-felhője*1.*


**K: Van mód arra, hogy diagnosztikai adatokat küldjek a Microsoftnak?**

**A:** Igen. Az Azure Spatial Anchors rendelkezik egy diagnosztikai móddal, amelyet a fejlesztők az Azure Spatial Anchors API-n keresztül választhatnak. Ez például akkor hasznos, ha olyan környezettel találkozik, ahol nem tud előre látható módon horgonyokat létrehozni és megtalálni. Megkérdezhetjük, hogy beküldhet-e diagnosztikai jelentést, amely olyan információkat tartalmaz, amelyek segítenek a hibakeresésben. További információ: [Naplózás és diagnosztika az Azure Spatial Anchors](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Elérhetőségi és árképzési gyakori kérdések

**K: Megad egy SLA-t?**

**A.** Az Azure-szolgáltatások standard szolgáltatásaszerint 99,9%-nál nagyobb rendelkezésre állást célozunk meg. Vegye figyelembe, hogy az Azure Spatial Anchors jelenleg előzetes verzióban van, és mint ilyen, az [előzetes kiegészítő feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) érvényesek.

**K: Közzétehetem az alkalmazásaimat az Azure Spatial Anchors használatával az alkalmazásáruházakban? Használhatom az Azure Spatial Anchors-t az alapvető fontosságú éles forgatókönyvekhez?**

**A.** Az Azure Spatial Anchors jelenleg előzetes verzióban van, és ez idő alatt felkérjük, hogy dolgozzon ki alkalmazásokat, [adjon visszajelzést](https://feedback.azure.com/forums/919252-azure-spatial-anchors) a termékről, és tervezze meg az éles környezetben való üzemelő példányokat.

Általános elérhetőség (GA) dátumok lesz jelentették be nemsokára.

**K: Vannak-e fojtási korlátok?**

**A**: Igen, vannak fojtási korlátaink.  Nem számítunk arra, hogy a tipikus alkalmazásfejlesztéshez és -teszteléshez is megütheti őket. Az éles környezetben történő üzembe helyezésekhez készen állunk arra, hogy támogassuk ügyfeleink magas szintű követelményeit. [Lépjen kapcsolatba velünk,](mailto:azuremrs@microsoft.com) hogy megvitassák. Az Előzetes verzió ezen fázisában még nem tettük közzé a rétegezési és árképzési struktúránkat, de erre hamarosan számítunk.

**K: Milyen régiókban érhető el az Azure Spatial Anchors?**

**A.** Még ma létrehozhat egy Azure Spatial Anchors-fiókot az Usa keleti régiójában. Ez azt jelenti, hogy a szolgáltatás számítási és tárolási teljesítménye is ebben a régióban található. Igaz, nincs korlátozás, ahol az ügyfelek találhatók. A jövőben a szolgáltatás regionális rendelkezésre állását az összes elsődleges Azure-régióra bővítjük.

**K: Az Azure Spatial Anchors díjat számítja fel? Fogsz valaha is vádat?**

**A.** Az előzetes verzió során az árakról az ároldalról az [ároldalunkon talál részleteket.](https://azure.microsoft.com/pricing/details/spatial-anchors/)

## <a name="technical-faqs"></a>Technikai GYIK

**K: Hogyan működik az Azure Spatial Anchors?**

**A.** Az Azure Spatial Anchors a vegyes valóságtól/ kiterjesztett valóságkövetőktől függ. Ezek a nyomkövetők kamerákkal érzékelik a környezetet, és 6 szabadságfával (6DoF) követik nyomon a készüléket, ahogy áthalad a térben.

Mivel az Azure Spatial Anchors egy 6DoF-követőt biztosít, lehetővé teszi, hogy bizonyos érdekes helyeket jelöljön ki a valós környezetben "horgony" pontként. Használhat például egy horgonyt a valós világ egy adott helyén való tartalom megjelenítéséhez.

Amikor létrehoz egy horgonyt, az ügyfél SDK rögzíti a környezeti információkat az adott pont körül, és továbbítja azokat a szolgáltatásnak. Ha egy másik eszköz ugyanebben a térben keresi a horgonyt, hasonló adatok továbbítódnak a szolgáltatásnak. Ezeket az adatokat a rendszer összehasonlítja a korábban tárolt környezeti adatokkal. A horgony helyzete az eszközhöz viszonyítva ezután visszakerül az alkalmazásban való használatra.

**K: Hogyan integrálható az Azure Spatial Anchors az ARKit-tal és az ARCore-ral iOS és Android rendszeren?**

**A.** Az Azure Spatial Anchors kihasználja az ARKit és az ARCore natív követési képességeit. Emellett az iOS-hez és Androidhoz készült SDK-k olyan képességeket kínálnak, mint például a felügyelt felhőszolgáltatásban továbbra is fennálló horgonyok, és lehetővé teszik az alkalmazások számára, hogy újra megtalálják ezeket a horgonyokat a szolgáltatáshoz való egyszerű csatlakozással.

**K: Hogyan integrálható az Azure Spatial Anchors a HoloLens-szel?**

**A.** Az Azure Spatial Anchors kihasználja a HoloLens natív nyomon követési képességeit. A HoloLensen alkalmazások készítéséhez egy Azure Spatial Anchors SDK-t biztosítunk. Az SDK integrálható a natív HoloLens-képességekkel, és további képességeket biztosít. Ezek a képességek közé tartozik, hogy az alkalmazásfejlesztők megőrizhetik a horgonyokat egy felügyelt felhőszolgáltatásban, és lehetővé teszik az alkalmazások számára, hogy újra megtalálják ezeket a horgonyokat a szolgáltatáshoz való csatlakozással.

**K: Mely platformokat és nyelveket támogatja az Azure Spatial Anchors?**

**A.** A fejlesztők az Azure Spatial Anchors alkalmazásokkal készíthetnek alkalmazásokat az eszközükhöz használt eszközök és keretrendszerek használatával:

- Egység a HoloLens, az iOS és az Android rendszerében
- Xamarin iOS és Android rendszeren
- Swift vagy Objective-C iOS rendszeren
- Java vagy az Android NDK Android
- C++/WinRT a HoloLensen

A fejlesztés sel [itt ismerkedik.](index.yml)

**K: Működik az Unreal-nál?**

**A.** Az Unreal támogatását a jövőben figyelembe vesszük.

**K: Milyen portokat és protokollokat használ az Azure Spatial Anchors?**

**A.** Az Azure Spatial Anchors a 443-as TCP-porton keresztül kommunikál egy titkosított protokoll használatával. A hitelesítéshez [az Azure Active Directoryt](https://docs.microsoft.com/azure/active-directory/)használja, amely https-porton keresztül kommunikál a 443-as porton keresztül.
