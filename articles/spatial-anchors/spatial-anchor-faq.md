---
title: Gyakran ismételt kérdések az Azure térbeli Horgonyokkal kapcsolatban | Microsoft Docs
description: Az Azure térbeli horgonyok egy felügyelt felhőalapú szolgáltatás és fejlesztői platform, amely lehetővé teszi az eszközökön, több felhasználón, vegyes valóságon alapuló, HoloLens, iOS és Android rendszerű eszközökön való használatot. Ezek a gyakori kérdések a szolgáltatással kapcsolatos kérdésekkel foglalkoznak a technikai szempontból.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 06936a196813dacfc1fc9d02945bee4119b7eea8
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927495"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Gyakori kérdések az Azure Spatial Anchorsról

Az Azure térbeli horgonyok egy felügyelt felhőalapú szolgáltatás és fejlesztői platform, amely lehetővé teszi a többfelhasználós, térbelian tudatos, vegyes valóságot a HoloLens, iOS és Android rendszerű eszközökön.

További információ: [Azure térbeli horgonyok – áttekintés](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>Azure térbeli horgonyok termékkel kapcsolatos gyakori kérdések

**K: Mely eszközök támogatják az Azure térbeli horgonyokat?**

**V:** Az Azure térbeli horgonyok lehetővé teszik a fejlesztők számára, hogy a ARKit-támogatással rendelkező iOS-eszközökön és ARCore-támogatással rendelkező Android-eszközökön alkalmazásokat HoloLens. iOS-és Android-eszközök esetén a telefonok és a tabletták is elérhetők.

**K: Csatlakoztatni kell a felhőhöz az Azure térbeli horgonyok használatához?**

**V:** Az Azure térbeli horgonyok jelenleg hálózati kapcsolatot igényelnek az internethez. Szívesen fogadjuk megjegyzéseit a [visszajelzési oldalunkon](https://feedback.azure.com/forums/919252-azure-spatial-anchors).

**K: Milyen kapcsolati követelmények vonatkoznak az Azure térbeli horgonyokra?**

**V:** Az Azure térbeli horgonyok Wi-Fi-és mobil szélessávú kapcsolatokkal működnek.

**K: Milyen pontosan tud az Azure térbeli horgonyok megtalálni a horgonyokat?**

**V:** Számos tényező befolyásolja a horgonyok megkeresésének pontosságát – a megvilágítási körülményeket, a környezetben lévő objektumokat, valamint azt a felületet is, amelyen a horgony elhelyezve van. Annak eldöntéséhez, hogy a pontosság megfelel-e az igényeinek, próbálja meg a környezetekben található horgonyokat a használni kívánt környezetben. Ha olyan környezetekben találkozik, ahol a pontosság nem felel meg az igényeinek, lásd: [naplózás és diagnosztika az Azure térbeli horgonyokban](./concepts/logging-diagnostics.md).

**K: Mennyi ideig tart a horgonyok létrehozása és megkeresése?**

**V:** A horgonyok létrehozásához és megkereséséhez szükséges idő számos tényezőtől függ: a hálózati kapcsolattól, az eszköz feldolgozásának és betöltésének, valamint az adott környezettől. Ügyfeleink számos iparágban, például a gyártásban, a kereskedelemben és a játékokban is kiépítik az alkalmazásokat, ami azt jelzi, hogy a szolgáltatás lehetővé teszi a nagyszerű felhasználói élményt a forgatókönyvekhez.

## <a name="privacy-faq"></a>Adatvédelem – gyakori kérdések

**K: Ha az alkalmazás egy térbeli horgonyt helyez el valahol, akkor az összes alkalmazás hozzáfér hozzá?**

**V:** A horgonyok az Azure-fiókkal vannak elkülönítve. Csak azok az alkalmazások férhetnek hozzá a fiókhoz, amelyekhez hozzáférést biztosít a fiókjához.

**K: Milyen információkat továbbítanak és tárolnak a környezettel kapcsolatban az Azure térbeli horgonyok használatakor? Továbbítják és tárolják a környezet képeit?**

**A**: A horgonyok létrehozásakor vagy keresésekor a rendszer a környezet képeit egy származtatott formátumba dolgozza fel. A származtatott formátumot a szolgáltatás továbbítja és tárolja.

Az átláthatóság biztosításához az alábbi képen egy környezet és a származtatott ritka pont felhő látható. A pont felhő a szolgáltatás által továbbított és tárolt környezet geometriai ábrázolását mutatja. A ritka pont felhő minden pontjához az adott pont vizuális jellemzőinek kivonatát továbbítjuk és tároljuk. A kivonat a következőből származik:, de nem tartalmaz képpontos adatokat.

Az Azure térbeli horgonyok megfelelnek az [Azure szolgáltatási szerződés feltételeinek](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9)és a [Microsoft adatvédelmi nyilatkozatának](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409).

![Egy környezet és annak származtatott ritka pontjának](./media/sparse-point-cloud.png)
*1. ábrája: Egy környezet és annak származtatott ritka pontjának felhője*


**K: Van lehetőség diagnosztikai adatok küldésére a Microsoftnak?**

**A**: Igen. Az Azure térbeli horgonyok diagnosztikai móddal rendelkeznek, amelyekkel a fejlesztők választhatnak az Azure térbeli horgonyok API-n keresztül. Ez akkor lehet hasznos, ha például olyan környezetben találkozik, amelyben előre nem hozhatók létre és nem találhatók meg a horgonyok. Megkérhetjük, hogy be tud-e jelenteni egy diagnosztikai jelentést, amely a hibakeresést segítő információkat tartalmaz. További információ: [naplózás és diagnosztika az Azure térbeli horgonyokban](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Rendelkezésre állási és díjszabási GYIK

**K: Biztosítanak SLA-t?**

**V:** A standard szintű Azure-szolgáltatásokhoz a 99,9%-nál nagyobb rendelkezésre állást célozunk meg. Vegye figyelembe, hogy az Azure térbeli horgonyok jelenleg előzetes verzióban érhetők el, ezért az előzetes verziókra vonatkozó [kiegészítő feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) érvényesek.

**K: Közzétehetem az alkalmazásokat az Azure térbeli horgonyok használatával az alkalmazás-áruházakban? Használhatom az Azure térbeli horgonyokat a kritikus fontosságú üzemi helyzetekben?**

**V:** Az Azure térbeli horgonyok jelenleg előzetes verzióban üzemelnek, és ebben az időszakban meghívjuk az [](https://feedback.azure.com/forums/919252-azure-spatial-anchors) alkalmazások fejlesztésére, a termékkel kapcsolatos visszajelzések megtervezésére és az éles környezetekben való üzembe helyezésre.

Az általánosan elérhető (GA) dátumok hamarosan bejelentve lesznek.

**K: Vannak érvényben lévő szabályozási korlátok?**

**A**: Igen, szabályozási korlátok vannak.  Nem várjuk, hogy az alkalmazások tipikus fejlesztése és tesztelése során is megtalálhatja őket. Éles üzembe helyezések esetén készek vagyunk támogatni ügyfeleink nagy léptékű követelményeit. [Lépjen kapcsolatba velünk](mailto:azuremrs@microsoft.com) , hogy beszéljen. Az előzetes verzió ezen fázisában még nem tették közzé a bevezetési és árképzési struktúrát, de hamarosan elvárjuk.

**K: Milyen régiókban érhetők el az Azure térbeli horgonyok?**

**V:** Az Azure-beli, USA 2. keleti régiójában jelenleg is létrehozhat Azure térbeli horgonyokat. Ez azt jelenti, hogy a szolgáltatáshoz tartozó számítási és tárolási kapacitás ebben a régióban van. Ez azt jelentette, hogy az ügyfelek hol találhatók. A jövőben a szolgáltatás regionális elérhetőségét minden elsődleges Azure-régióban ki fogjuk bővíteni.

**K: Számítanak fel díjat az Azure Spatial Anchors használatáért? Kell-e fizetni?**

**V:** A díjszabással kapcsolatos részletekért tekintse meg a [](https://azure.microsoft.com/pricing/details/spatial-anchors/)díjszabási oldalunkat.

## <a name="technical-faqs"></a>Technikai gyakori kérdések

**K: Hogyan működik az Azure térbeli horgony?**

**V:** Az Azure térbeli horgonyok vegyes valóságot/kibővített valóság-követési funkcióktól függenek. Ezek a Szemléző a környezetet a kamerákkal látják el, és nyomon követik az eszközt 6 fokos szabadságon (6DoF), ahogy az a térben halad át.

Az Azure térbeli horgonyok lehetővé teszik, hogy a 6DoF Tracker felépítési blokkként bizonyos hasznos pontokat jelöljön ki a valódi környezetében "horgony" pontként. Előfordulhat például, hogy egy horgony használatával jeleníti meg a tartalmat a valós világ egy adott helyén.

Amikor létrehoz egy horgonyt, az ügyfél-SDK rögzíti a környezeti adatokat, és továbbítja azt a szolgáltatásnak. Ha egy másik eszköz ugyanazon a helyen keresi a horgonyt, a hasonló adatokat továbbítja a szolgáltatásnak. Ezek az adatértékek a korábban tárolt környezeti adatértékekkel egyeznek meg. Ezután a rendszer visszaküldi a horgony pozícióját az eszközhöz viszonyítva az alkalmazásban való használatra.

**K: Hogyan integrálható az Azure térbeli alapjai a ARKit és a ARCore iOS és Android rendszereken?**

**V:** Az Azure térbeli horgonyok a ARKit és a ARCore natív követési képességeit használják. Emellett az iOS-hez és az Androidhoz készült SDK-k olyan funkciókkal is rendelkeznek, mint például a megőrzött horgonyok egy felügyelt felhőalapú szolgáltatásban, és lehetővé teszik az alkalmazások számára, hogy egyszerűen a szolgáltatáshoz csatlakozva megtalálják ezeket a horgonyokat.

**K: Hogyan integrálható az Azure térbeli HoloLens?**

**V:** Az Azure térbeli horgonyok a HoloLens natív követési képességeit használják. Biztosítunk egy Azure térbeli horgonyt SDK-t a HoloLens-on futó alkalmazások létrehozásához. Az SDK integrálható a natív HoloLens képességekkel, és további képességeket biztosít. Ezek a képességek közé tartozik az alkalmazások fejlesztői számára, hogy egy felügyelt felhőalapú szolgáltatásban megmaradjanak a horgonyok, és lehetővé tegyék, hogy alkalmazásai újra megtalálják ezeket a horgonyokat a szolgáltatáshoz való csatlakozással.

**K: Mely platformokon és nyelveken támogatja az Azure térbeli-horgonyokat?**

**V:** A fejlesztők az Azure térbeli Horgonyokkal hozhatnak létre alkalmazásokat az eszközük ismerős eszközeivel és keretrendszerek használatával:

- HoloLens, iOS és Android rendszerek közötti egység
- Swift vagy Objective-C iOS rendszeren
- Java-vagy Android-NDK az Androidon
- C++/WinRT a HoloLens

Kezdje el a [fejlesztést itt](index.yml).

**K: Működik az Unreal?**

**V:** Az Unreal támogatását a későbbiekben is figyelembe kell venni.

**K: Milyen portokat és protokollokat használnak az Azure térbeli horgonyok?**

**V:** Az Azure térbeli horgonyok a 443-as TCP-porton keresztül kommunikálnak titkosított protokoll használatával. A hitelesítéshez [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)használ, amely a HTTPS protokollon keresztül kommunikál a 443-as porton keresztül.
