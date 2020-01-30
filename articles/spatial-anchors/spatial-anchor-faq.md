---
title: Gyakori kérdések
description: Az Azure térbeli horgonyok szolgáltatással kapcsolatos gyakori kérdések.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 563e2da8eba228636b05db2112739fdead4a4aa3
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844887"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Gyakori kérdések az Azure Spatial Anchorsról

Az Azure térbeli horgonyok egy felügyelt felhőalapú szolgáltatás és fejlesztői platform, amely lehetővé teszi a többfelhasználós, térbelian tudatos, vegyes valóságot a HoloLens, iOS és Android rendszerű eszközökön.

További információ: [Azure térbeli horgonyok – áttekintés](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>Azure térbeli horgonyok termékkel kapcsolatos gyakori kérdések

**K: mely eszközök támogatják az Azure térbeli horgonyokat?**

**A:** Az Azure térbeli horgonyok lehetővé teszik a fejlesztők számára, hogy a ARKit-támogatással rendelkező iOS-eszközökön és ARCore-támogatással rendelkező Android-eszközökön alkalmazásokat HoloLens. iOS-és Android-eszközök esetén a telefonok és a tabletták is elérhetők.

**K: csatlakoztatni kell a felhőhöz az Azure térbeli horgonyok használatához?**

**A:** Az Azure térbeli horgonyok jelenleg hálózati kapcsolatot igényelnek az internethez. Szívesen fogadjuk megjegyzéseit a [visszajelzési oldalunkon](https://feedback.azure.com/forums/919252-azure-spatial-anchors).

**K: milyen kapcsolati követelmények vonatkoznak az Azure térbeli horgonyokra?**

**A:** Az Azure térbeli horgonyok Wi-Fi-és mobil szélessávú kapcsolatokkal működnek.

**K: milyen pontosan tud az Azure térbeli horgonyok megtalálni a horgonyokat?**

**A:** Számos tényező befolyásolja a horgonyok megkeresésének pontosságát – a megvilágítási körülményeket, a környezetben lévő objektumokat, valamint azt a felületet is, amelyen a horgony elhelyezve van. Annak eldöntéséhez, hogy a pontosság megfelel-e az igényeinek, próbálja meg a környezetekben található horgonyokat a használni kívánt környezetben. Ha olyan környezetekben találkozik, ahol a pontosság nem felel meg az igényeinek, lásd: [naplózás és diagnosztika az Azure térbeli horgonyokban](./concepts/logging-diagnostics.md).

**K: mennyi ideig tart a horgonyok létrehozása és megkeresése?**

**A:** A horgonyok létrehozásához és megkereséséhez szükséges idő számos tényezőtől függ: a hálózati kapcsolattól, az eszköz feldolgozásának és betöltésének, valamint az adott környezettől. Ügyfeleink számos iparágban, például a gyártásban, a kereskedelemben és a játékokban is kiépítik az alkalmazásokat, ami azt jelzi, hogy a szolgáltatás lehetővé teszi a nagyszerű felhasználói élményt a forgatókönyvekhez.

## <a name="privacy-faq"></a>Adatvédelem – gyakori kérdések

**K: Ha az alkalmazásom egy térbeli horgonyt helyez el valahol, az összes alkalmazás hozzáfér hozzá?**

**A:** A horgonyok az Azure-fiókkal vannak elkülönítve. Csak azok az alkalmazások férhetnek hozzá a fiókhoz, amelyekhez hozzáférést biztosít a fiókjához.

**K: milyen információkat továbbítanak és tárolnak egy adott környezetről az Azure térbeli horgonyok használatakor? Továbbítják és tárolják a környezet képeit?**

**A**: a horgonyok létrehozásakor vagy keresésekor a környezet képeit egy származtatott formátumba dolgozzák fel az eszközön. A származtatott formátumot a szolgáltatás továbbítja és tárolja.

Az átláthatóság biztosításához az alábbi képen egy környezet és a származtatott ritka pont felhő látható. A pont felhő a szolgáltatás által továbbított és tárolt környezet geometriai ábrázolását mutatja. A ritka pont felhő minden pontjához az adott pont vizuális jellemzőinek kivonatát továbbítjuk és tároljuk. A kivonat a következőből származik:, de nem tartalmaz képpontos adatokat.

Az Azure térbeli horgonyok megfelelnek az [Azure szolgáltatási szerződés feltételeinek](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9)és a [Microsoft adatvédelmi nyilatkozatának](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409).

![egy környezetet és annak származtatott ritka pontjának felhőjét](./media/sparse-point-cloud.png)
*1. ábra: egy környezet és a származtatott ritka pont felhője*


**K: van lehetőség arra, hogy diagnosztikai adatokat küldjön a Microsoftnak?**

**V**: Igen. Az Azure térbeli horgonyok diagnosztikai móddal rendelkeznek, amelyekkel a fejlesztők választhatnak az Azure térbeli horgonyok API-n keresztül. Ez akkor lehet hasznos, ha például olyan környezetben találkozik, amelyben előre nem hozhatók létre és nem találhatók meg a horgonyok. Megkérhetjük, hogy be tud-e jelenteni egy diagnosztikai jelentést, amely a hibakeresést segítő információkat tartalmaz. További információ: [naplózás és diagnosztika az Azure térbeli horgonyokban](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Rendelkezésre állási és díjszabási GYIK

**K: biztosítanak SLA-t?**

**A:** A standard szintű Azure-szolgáltatásokhoz a 99,9%-nál nagyobb rendelkezésre állást célozunk meg. Vegye figyelembe, hogy az Azure térbeli horgonyok jelenleg előzetes verzióban érhetők el, ezért az előzetes verziókra vonatkozó [kiegészítő feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) érvényesek.

**K: közzétehetem az alkalmazásokat az Azure térbeli horgonyok használatával az alkalmazás-áruházakban? Használhatom az Azure térbeli horgonyokat a kritikus fontosságú üzemi helyzetekben?**

**A:** Az Azure térbeli horgonyok jelenleg előzetes verzióban üzemelnek, és ebben az időszakban meghívjuk az alkalmazások fejlesztésére, a termékkel kapcsolatos [visszajelzések](https://feedback.azure.com/forums/919252-azure-spatial-anchors) megtervezésére és az éles környezetekben való üzembe helyezésre.

Az általánosan elérhető (GA) dátumok hamarosan bejelentve lesznek.

**K: vannak érvényben lévő szabályozási korlátok?**

Válasz **: igen**, szabályozási korlátok vannak.  Nem várjuk, hogy az alkalmazások tipikus fejlesztése és tesztelése során is megtalálhatja őket. Éles üzembe helyezések esetén készek vagyunk támogatni ügyfeleink nagy léptékű követelményeit. [Lépjen kapcsolatba velünk](mailto:azuremrs@microsoft.com) , hogy beszéljen. Az előzetes verzió ezen fázisában még nem tették közzé a bevezetési és árképzési struktúrát, de hamarosan elvárjuk.

**K: milyen régiókban érhetők el az Azure térbeli adathorgonyok?**

**A:** Az Azure-beli, USA 2. keleti régiójában jelenleg is létrehozhat Azure térbeli horgonyokat. Ez azt jelenti, hogy a szolgáltatáshoz tartozó számítási és tárolási kapacitás ebben a régióban van. Ez azt jelentette, hogy az ügyfelek hol találhatók. A jövőben a szolgáltatás regionális elérhetőségét minden elsődleges Azure-régióban ki fogjuk bővíteni.

**K: az Azure térbeli horgonyok díja? Kell-e fizetni?**

**A:** A díjszabással kapcsolatos részletekért tekintse meg a [díjszabási oldalunkat.](https://azure.microsoft.com/pricing/details/spatial-anchors/)

## <a name="technical-faqs"></a>Technikai gyakori kérdések

**K: Hogyan működik az Azure térbeli horgony?**

**A:** Az Azure térbeli horgonyok vegyes valóságot/kibővített valóság-követési funkcióktól függenek. Ezek a Szemléző a környezetet a kamerákkal látják el, és nyomon követik az eszközt 6 fokos szabadságon (6DoF), ahogy az a térben halad át.

Az Azure térbeli horgonyok lehetővé teszik, hogy a 6DoF Tracker felépítési blokkként bizonyos hasznos pontokat jelöljön ki a valódi környezetében "horgony" pontként. Előfordulhat például, hogy egy horgony használatával jeleníti meg a tartalmat a valós világ egy adott helyén.

Amikor létrehoz egy horgonyt, az ügyfél-SDK rögzíti a környezeti adatokat, és továbbítja azt a szolgáltatásnak. Ha egy másik eszköz ugyanazon a helyen keresi a horgonyt, a hasonló adatokat továbbítja a szolgáltatásnak. Ezek az adatértékek a korábban tárolt környezeti adatértékekkel egyeznek meg. Ezután a rendszer visszaküldi a horgony pozícióját az eszközhöz viszonyítva az alkalmazásban való használatra.

**K: Hogyan integrálható az Azure térbeli alapjai a ARKit és a ARCore iOS és Android rendszereken?**

**A:** Az Azure térbeli horgonyok a ARKit és a ARCore natív követési képességeit használják. Emellett az iOS-hez és az Androidhoz készült SDK-k olyan funkciókkal is rendelkeznek, mint például a megőrzött horgonyok egy felügyelt felhőalapú szolgáltatásban, és lehetővé teszik az alkalmazások számára, hogy egyszerűen a szolgáltatáshoz csatlakozva megtalálják ezeket a horgonyokat.

**K: Hogyan integrálható az Azure térbeli HoloLens?**

**A:** Az Azure térbeli horgonyok a HoloLens natív követési képességeit használják. Biztosítunk egy Azure térbeli horgonyt SDK-t a HoloLens-on futó alkalmazások létrehozásához. Az SDK integrálható a natív HoloLens képességekkel, és további képességeket biztosít. Ezek a képességek közé tartozik az alkalmazások fejlesztői számára, hogy egy felügyelt felhőalapú szolgáltatásban megmaradjanak a horgonyok, és lehetővé tegyék, hogy alkalmazásai újra megtalálják ezeket a horgonyokat a szolgáltatáshoz való csatlakozással.

**K: mely platformok és nyelvek támogatják az Azure térbeli horgonyokat?**

**A:** A fejlesztők az Azure térbeli Horgonyokkal hozhatnak létre alkalmazásokat az eszközük ismerős eszközeivel és keretrendszerek használatával:

- HoloLens, iOS és Android rendszerek közötti egység
- Xamarin iOS és Android rendszereken
- Swift vagy Objective-C iOS rendszeren
- Java-vagy Android-NDK az Androidon
- C++/WinRT a HoloLens

Kezdje el a [fejlesztést itt](index.yml).

**K: működik az Unreal?**

**A:** Az Unreal támogatását a későbbiekben is figyelembe kell venni.

**K: milyen portokat és protokollokat használnak az Azure térbeli horgonyok?**

**A:** Az Azure térbeli horgonyok a 443-as TCP-porton keresztül kommunikálnak titkosított protokoll használatával. A hitelesítéshez [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)használ, amely a HTTPS protokollon keresztül kommunikál a 443-as porton keresztül.
