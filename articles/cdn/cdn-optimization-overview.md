---
title: Az Azure CDN tartalomkézbesítési típusú optimalizálása
description: Az Azure CDN tartalomkézbesítési típusú optimalizálása
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2018
ms.author: v-deasim
ms.openlocfilehash: be41678b56fdb57c29d65b6b2a17eccd55e85c74
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47095917"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Az Azure CDN tartalomkézbesítési típusú optimalizálása

Amikor tartalmat egy nagy globális célközönség számára továbbít, rendkívül fontos a a webtartalmak optimalizált továbbításának biztosítása. [Az Azure Content Delivery Network (CDN)](cdn-overview.md) optimalizálhatja a szállítási élmény rendelkezik a tartalom típusa alapján. A tartalom lehet egy webhely, élő stream, videó vagy egy nagy méretű fájlt töltse le. CDN-végpont létrehozásakor megad egy forgatókönyv a **optimalizált** lehetőséget. A választása határozza meg, melyik optimalizálást alkalmazott a CDN-végpontról kézbesített tartalom.

Optimalizálási lehetőségek célja, hogy tartalomkézbesítési teljesítmény növelése érdekében ajánlott eljárásokat viselkedések használja, és jobb forrás offload. A forgatókönyv választási lehetőségek részleges gyorsítótárazás objektum darabolás és a sikertelen újrapróbálkozási szabályzat konfigurációi módosításával befolyásolhatja a teljesítményt. 

Ez a cikk áttekinti a különböző optimalizálási funkciók és kell használnia. Funkcióiról és korlátozásairól további információkért tekintse meg a megfelelő cikkek minden egyes optimalizálás típusa.

> [!NOTE]
> Amikor létrehoz egy CDN-végpont a **optimalizált** beállítások profil jön létre a végpont típusa alapján változhat. Az Azure CDN-szolgáltató a fejlesztés a forgatókönyvtől függően különböző módon vonatkoznak. 

## <a name="provider-options"></a>Szolgáltatói beállítások

**Az Azure CDN Standard a Microsoft** profilok az alábbi optimalizálások támogatja:

* [Általános webes kézbesítés](#general-web-delivery). Az optimalizálás használják a média streamelését, és nagyméretű fájl letöltése.


**Az Azure CDN Standard verizon** és **verizon Azure CDN Premium** profilok az alábbi optimalizálások támogatják:

* [Általános webes kézbesítés](#general-web-delivery). Az optimalizálás használják a média streamelését, és nagyméretű fájl letöltése.

* [Dinamikus helygyorsítás](#dynamic-site-acceleration) 


**Az Azure CDN Akamai Standard** profilok az alábbi optimalizálások támogatják:

* [Általános webes kézbesítés](#general-web-delivery) 

* [Általános médiastreaming](#general-media-streaming)

* [Igény szerinti videó online médialejátszás](#video-on-demand-media-streaming)

* [Nagyméretű fájl letöltése](#large-file-download)

* [Dinamikus helygyorsítás](#dynamic-site-acceleration) 

A Microsoft azt javasolja, hogy a teljesítmény-változatok között jelölje be az optimális a szállítási-szolgáltató különböző szolgáltatók tesztelése.

## <a name="select-and-configure-optimization-types"></a>Válassza ki, és optimalizálás-típusokra konfigurálása

A CDN-végpontot hoz létre, válassza ki az optimalizálás típusa, amely a legjobban illik a forgatókönyv és a végponthoz továbbítására kívánt tartalom típusa. **Általános webes kézbesítés** az alapértelmezett beállítás. Meglévő **Azure CDN Akamai Standard** végpontok csak, a beállítást bármikor frissítheti. Ez a változás nem megszakítás kézbesítési az Azure CDN szolgáltatással. 

1. Az egy **Azure CDN Akamai Standard** profilt, válassza ki a végpont.

    ![Végpont kiválasztása ](./media/cdn-optimization-overview/01_Akamai.png)

2. A beállítások területen válassza ki a **optimalizálási**. Ezt követően válassza ki a típus a **optimalizált** legördülő listából.

    ![Optimalizálás és típus kiválasztása](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Optimalizálás bizonyos forgatókönyvek esetén

A CDN-végpont az egyik ezeket a forgatókönyveket is optimalizálhatja. 

### <a name="general-web-delivery"></a>Általános webes kézbesítés

Általános webes kézbesítés optimalizálása lehetőség leggyakoribb. Az általános webes tartalom optimalizálás, például a weblapok és webes alkalmazások tervezték. Az optimalizálás is használható fájlt, és a videó tölti le.

Egy tipikus webhely statikus és dinamikus tartalom tartalmazza. Statikus tartalom magában foglalja a képek, JavaScript-kódtárak és stíluslapok, amely a gyorsítótárba, és kézbesíti a különböző felhasználókhoz. Dinamikus tartalom adott felhasználó, például a news elemek, amelyek igazodnak a felhasználói profil személyre szabhatja. Dinamikus tartalom, például a bevásárlókocsi vásárlási tartalmát, mert mindegyik felhasználóhoz egyedi nincs gyorsítótárazva. Általános webes kézbesítés optimalizálhatja az egész webhely. 

> [!NOTE]
> Ha használ egy **Azure CDN Akamai Standard** profilt, válassza az optimalizálás típusa, ha az átlagos mérete 10 MB-nál kisebb. Válassza ki a Othewise, ha az átlagos mérete 10 MB-nál nagyobb **nagyméretű fájl letöltése** a a **optimalizált** legördülő listából válassza ki.

### <a name="general-media-streaming"></a>Általános médiastreaming

Ha szeretné a végpontot használja az élő Stream, és rögzített streamelési, válassza az általános adatfolyam-továbbítási optimalizálás típusa.

Időérzékeny, médiatartalmak streamelése azért, mert az ügyfélen, például gyakori videópufferelést, késői csomagokat élményt okozhat. Online médialejátszás optimalizálása csökkenti a médiatartalmak késését, és a smooth streaming élményt nyújt a felhasználók. 

Ebben a forgatókönyvben az Azure media service ügyfelek gyakori. Az Azure media Services szolgáltatást használja, kap egy egyetlen streamvégponton, az élő és igényalapú streaming használható. Az ebben a forgatókönyvben az ügyfelek váltani egy másik végpont, ha az igény szerinti folyamatos átvitel módosítását az élő nem szükséges. Általános online médialejátszás optimalizálása támogatja ezt a forgatókönyvet.

A **Azure CDN Standard a Microsoft**, **Azure CDN Standard verizon**, és **verizon Azure CDN Premium**, használja az általános webes kézbesítési optimalizálás típusa tartalomközvetítés általános media.

Online médialejátszás optimalizálása kapcsolatos további információkért lásd: [online médialejátszás optimalizálása](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Igény szerinti videó online médialejátszás

Online médialejátszás optimalizálása video-on-demand streamelési Videólejátszás javítja. Ha rögzített streamelési végpontok használja, akkor használja ezt a beállítást.

A **Azure CDN Standard a Microsoft**, **Azure CDN Standard verizon**, és **verizon Azure CDN Premium** -profilok használatához az általános webes kézbesítés optimalizálása Írja be a tartalomközvetítés videoszolgáltatás a media.

Online médialejátszás optimalizálása kapcsolatos további információkért lásd: [online médialejátszás optimalizálása](cdn-media-streaming-optimization.md).

> [!NOTE]
> Ha a CDN-végpont elsősorban Videólejátszás szolgál ki, használja az optimalizálás típusa. Az optimalizálás típusa és az általános médiastreaming optimalizálás típusa közötti fő különbség a kapcsolat újrapróbálkozás időkorlátja. Az időtúllépés értéke működjön együtt élő adatfolyam-továbbítási forgatókönyv sokkal rövidebb.
>

### <a name="large-file-download"></a>Nagyméretű fájl letöltése

A **Azure CDN Akamai Standard** profilokat, a 10 MB-nál nagyobb tartalmak letöltések optimalizált nagy fájlt. Ha az átlagos mérete 10 MB-nál kisebb, akkor az általános webes kézbesítés. Ha az átlagos méretű következetesen 10 MB-nál nagyobb, hatékonyabb, hozzon létre egy külön végpontot a nagy méretű fájlok lehet. Belső vezérlőprogram vagy szoftverfrissítések például általában olyan nagy fájlok. Biztosításához 1,8 GB-nál nagyobb fájlok, a nagyméretű fájlok letöltési optimalizálása szükség.

A **Azure CDN Standard a Microsoft**, **Azure CDN Standard verizon**, és **verizon Azure CDN Premium** -profilok használatához az általános webes kézbesítés optimalizálása Írja be, hogy a tartalom a nagyméretű fájl letöltése. Nincs letöltési fájlméret korlátozása nélkül.

Nagyméretű fájlok optimalizálása kapcsolatos további információkért lásd: [nagyméretű fájlok optimalizálása](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Dinamikus helygyorsítás

 Dinamikuswebhely-gyorsítás (DSA) érhető el a **Azure CDN Akamai Standard**, **Azure CDN Standard verizon**, és **verizon Azure CDN Premium** profilok. Az optimalizálás magában foglalja a járulékos díjat kell használni, További információkért lásd: [Content Delivery Network díjszabása](https://azure.microsoft.com/pricing/details/cdn/).

DSA magában foglalja a különféle módszereket, amelyek a késés és a dinamikus tartalom teljesítményét. Ennek technikái a következők: útvonal és hálózati optimalizálás, a TCP-optimalizálás és más. 

Az optimalizálás használatával felgyorsíthatja egy webalkalmazást, amely számos olyan nem gyorsítótárazható válaszokat tartalmazza. Példák a keresési eredmények, a fizetési tranzakciók és a valós idejű adatok. Továbbra is használhatja az alapképességek Azure CDN gyorsítótárazási statikus adatok esetében. 

Dinamikus helygyorsítás kapcsolatos további információkért lásd: [dinamikuswebhely-gyorsítás](cdn-dynamic-site-acceleration.md).



