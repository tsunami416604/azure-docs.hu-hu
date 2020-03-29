---
title: Biztonság az Azure Data Box Heavy számára | Microsoft dokumentumok
description: Ismerteti a biztonsági konvenciókat, irányelveket és szempontokat, és ismerteti, hogyan telepítheti és működtetheti biztonságosan az Azure Data Box Heavy-t.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/08/2019
ms.author: alkohli
ms.openlocfilehash: b7562ab6c7ec9c6234ed3fbb867361307b80a638
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67673118"
---
# <a name="safely-install-and-operate-your-azure-data-box-heavy"></a>Az Azure Data Box Heavy biztonságos telepítése és működtetése

Ez a cikk az Azure Data Box Heavy biztonsági adatait tartalmazza.

![Figyelmeztetés](./media/data-box-heavy-safety/warning-icon.png)
![ikon Olvassa](./media/data-box-heavy-safety/read-safety-and-health-information-icon.png) el a biztonsági közleményt ikon: **Először olvassa el az összes utasítást**

Az Azure Data Box Heavy használata előtt olvassa el a cikkben található összes biztonsági információt. Az utasítások figyelmen kívül hagyása tüzet, áramütést vagy más sérüléseket, illetve a tulajdonságok károsodását okozhatja.

## <a name="safety-icon-conventions"></a>Biztonsági ikon konvenciók
Itt vannak azok az ikonok, amelyeket a Data Box beállításakor és futtatásakor betartandó biztonsági óvintézkedések áttekintése során talál.

| Ikon | Leírás |
|:--- |:--- |
| ![Veszély](./media/data-box-heavy-safety/warning-icon.png) ikon **veszély!** |Olyan veszélyes helyzetet jelöl, amely, ha nem kerülik el, halálhoz vagy súlyos sérüléshez vezet. Ezt a jelszót a legszélsőségesebb helyzetekre kell korlátozni. |
| ![Figyelem](./media/data-box-heavy-safety/warning-icon.png) ikon **FIGYELEM!** |Olyan veszélyes helyzetet jelöl, amely, ha nem kerülik el, halálhoz vagy súlyos sérüléshez vezethet. |
| ![Figyelem](./media/data-box-heavy-safety/warning-icon.png) ikon **VIGYÁZAT!** |Olyan veszélyes helyzetet jelöl, amely, ha nem kerülik el, kisebb vagy közepes sérülést okozhat. |
| ![Értesítésikon](./media/data-box-heavy-safety/notice-icon.png) **NOTICE:** |Fontosnak tekintett, de a veszélyhez kapcsolódó információkat jelöl. |
| ![Áramütés ikon](./media/data-box-heavy-safety/electrical-shock-hazard-icon.png) **Áramütés veszély** |Nagyfeszültségű. |
| ![Nehéz súly](./media/data-box-heavy-safety/heavy-weight-hazard-icon.png) ikon **Nehéz súly** | |
| ![Nincs felhasználó által](./media/data-box-heavy-safety/no-user-serviceable-parts-icon.png) javítható alkatrészikon **Nincs felhasználó által javítható alkatrész** |Ne férjen hozzá, kivéve, ha megfelelően van betanítva. |
| ![Biztonsági nyilatkozat](./media/data-box-heavy-safety/read-safety-and-health-information-icon.png) olvasása ikon: **Először olvassa el az összes utasítást** | |
| ![Tippveszély](./media/data-box-heavy-safety/tip-hazard-icon.png) ikon **Tipp veszély** | |
| ![Túlterhelési hegy](./media/data-box-heavy-safety/overload-tip-hazard-icon.png) veszélyikon **Túlterhelési csúcs veszély** | |
| ![Több áramforrás](./media/data-box-heavy-safety/multiple-power-sources-icon.png) ikon **Több energiaforrás** | |

## <a name="handling-precautions"></a>Kezelési óvintézkedések

![Figyelmeztető](./media/data-box-heavy-safety/warning-icon.png) ikon **figyelmeztetés!**

* A láda mozgatásakor és kezelésekor megfelelő felszerelést (például raklapemelőt) és egyéni védőfelszerelést (például kesztyűt) kell használni.
* A rámpát a mellékelt csavarokkal kell a ládához rögzíteni, mielőtt eltávolítana a készüléket, hogy elkerülje a sérüléseket vagy anyagi károkat.

![Figyelmeztető](./media/data-box-heavy-safety/warning-icon.png) ![ikon](./media/data-box-heavy-safety/tip-hazard-icon.png) Tipp Veszély ikon **tipp veszély veszély**

* Helyezze a berendezést sima, kemény és stabil felületre, hogy elkerülje a lehetséges csúcsot vagy a zúzást.
* Ellenőrizze, hogy a görgők le vannak-e zárva, mielőtt megvizsgálna, bekapcsolna és működtetene a berendezést.

![Figyelmeztetés](./media/data-box-heavy-safety/warning-icon.png) ![ikon](./media/data-box-heavy-safety/electrical-shock-hazard-icon.png)![Elektromos sokk ikon](./media/data-box-heavy-safety/no-user-serviceable-parts-icon.png) Nincs felhasználó által javítható alkatrészek ikon **VIGYÁZ!** 

* Ellenőrizze, hogy a készülék nem *sérült-e* meg. Ha az eszköz háza sérült, kérjen csereterméket a [Microsoft támogatási szolgálatát.](data-box-disk-contact-microsoft-support.md) Ne próbálja meg működtetni a készüléket. 
* A készülék hamisíthatatlan csavarokkal van felszerelve. Ha arra gyanakszik, hogy az eszköz hibásan működik, a [Microsoft támogatási szolgálata](data-box-disk-contact-microsoft-support.md) cserebe kerül. Ne kísérelje meg a készülék szervizelése. 
* A készülék nem tartalmaz felhasználó által javítható alkatrészeket. Veszélyes feszültség, áram és energiaszint van benne. Ne nyissa ki. Az eszköz visszaküldése a Microsoftnak szervizelésre.

![Figyelmeztető](./media/data-box-heavy-safety/warning-icon.png) ![ikon](./media/data-box-heavy-safety/heavy-weight-hazard-icon.png) Nehéz súly ikon **FIGYELEM!** 

* Egy teljesen konfigurált ház súlya akár 326 kg (719 lbs); ne próbálja meg egyedül felemelni.
* Ne próbálja meg felemelni a berendezést megfelelő mechanikai segítség nélkül. Ne feledje, hogy minden olyan kísérlet, hogy szüntesse meg ezt a súlyt okozhat súlyos sérüléseket.
* A berendezés mozgatása és felemelése során megfeleljen a helyi munkahelyi egészségvédelmi és biztonsági követelményeknek.
* A mozgó és emelőberendezésekhez használjon mechanikai segítséget vagy más megfelelő segítséget.


![Figyelmeztetés](./media/data-box-heavy-safety/warning-icon.png) ![ikon Túlterhelés](./media/data-box-heavy-safety/overload-tip-hazard-icon.png) ![imázs veszély ikon Tipp veszély ikon](./media/data-box-heavy-safety/tip-hazard-icon.png)![Nehéz súly ikon](./media/data-box-heavy-safety/heavy-weight-hazard-icon.png) **FIGYELEM!**
* A Data Box Heavy nem használható táblaként vagy munkaterületként. Bármilyen típusú terhelés hozzáadása potenciális veszélyt jelenthet, amely sérüléshez vagy anyagi kárhoz vezethet.
* Az állványra szerelt berendezéseket nem szabad polcként vagy munkatérként használni. Ne helyezze a Data Box Heavy készüléket állványra szerelt berendezésre. Bármilyen típusú terhelés hozzáadása egy hosszabb rackbe szerelt egységhez potenciális csúcsveszélyt okozhat, amely sérüléshez, halálhoz vagy termékkárosodáshoz vezethet.

![Figyelem](./media/data-box-heavy-safety/warning-icon.png) ikon **FIGYELEM!**

* A rendszert úgy tervezték, hogy egy tipikus irodai környezetben működjön. Válasszon olyan webhelyet, amely:

    - Jól szellőző és hőforrásoktól távol, beleértve a közvetlen napfényt és a radiátorokat.
    - Távol a rezgésvagy fizikai sokk forrásoktól.
    - Elektromos eszközök által termelt erős elektromágneses mezőktől elszigetelve.
    - Megfelelően földelt fali aljzatokkal ellátva.
    - Elegendő hely áll rendelkezésre a tápkábel(ek) eléréséhez, mivel ezek szolgálnak a termék fő áramellátásának leválasztására.

* Állítsa be a készüléket olyan munkaterületen, amely lehetővé teszi a megfelelő légáramlást a készülék körül.
* A készüléket a vezetőképességi szennyeződésektől mentes, szabályozott, beltéri helyiségben szerelje fel, és tegye lehetővé a megfelelő légáramlást a készülék körül.
* Tartsa távol a készüléket folyadékforrásoktól és túlzottan párás környezettől.


## <a name="electrical-precautions"></a>Elektromos óvintézkedések

![Figyelmeztetés](./media/data-box-heavy-safety/warning-icon.png) ![ikon](./media/data-box-heavy-safety/electrical-shock-hazard-icon.png) Áramütés ikon **FIGYELEM!**

* Biztosítson biztonságos elektromos földelési csatlakozást a tápkábelhez. A hálózati kábel háromvezetékes földelődugóval (földelőcsaptal) rendelkezik. Ez a csatlakozó csak egy földelt hálózati aljzatba illeszkedik. Ne győzd le a célja a földelő csap.
* Mivel a hálózati kábel dugója a fő leválasztó eszköz, győződjön meg arról, hogy a konnektorok a készülék közelében találhatók, és könnyen hozzáférhetők.
* Húzza ki a hálózati kábelt (a dugó kihúzásával, ne a kábellel), és húzza ki az összes kábelt, ha az alábbi feltételek bármelyike fennáll:

    - A hálózati kábel vagy a csatlakozó kikopik vagy más módon megsérül.
    - Valamit beleöntesz a szerkezet burkolatába.
    - A készülék esőnek vagy felesleges nedvességnek van kitéve.
    - A készülék leesett, és a készülék burkolata megsérült.
    - Azt gyanítja, hogy az eszközt szervizelésre vagy javításra van szüksége.
* Véglegesen húzza ki a készüléket, mielőtt elmozdítaná, vagy ha úgy gondolja, hogy bármilyen módon megsérült.
* Biztosítson megfelelő elektromos túlterhelés elleni védelemmel ellátott áramforrást, amely megfelel az alábbi teljesítményspecifikációknak:

    - Feszültség: 100 V AC-240 V AC
    - Áram: 6 A-tól 10 A-ig, legfeljebb tápkábelenként. Négy tápkábel biztosított.
    - Frekvencia: 50 Hz és 60 Hz között
* Ne kísérelje meg a hálózati tápkábel(ek) módosítását vagy használatát a berendezéshez mellékelt kapcsolókon kívül. A tápkábel(ek)nek a következő feltételeknek kell megfelelniük:
    - A tápkábel elektromos besorolásának nagyobbnak kell lennie, mint a terméken feltüntetett elektromos árambesorolás.
    - A tápkábelnek olyan biztonsági földcsaptal vagy érintkezővel kell rendelkeznie, amely alkalmas a konnektorba.

![Figyelmeztetés](./media/data-box-heavy-safety/warning-icon.png)![ikon](./media/data-box-heavy-safety/electrical-shock-hazard-icon.png) ![Áramütés ikon](./media/data-box-heavy-safety/multiple-power-sources-icon.png) Több áramforrás ikon **FIGYELEM!**  

* Húzza ki az összes hálózati tápkábelt(ek)et, hogy teljesen kivegye a hálózati áramot a készülékből.

![Figyelem](./media/data-box-heavy-safety/warning-icon.png) ikon **VIGYÁZAT!**

* Ez a készülék érmeelemeket tartalmaz. Ne kísérelje meg a készülék szervizelése. A készülék akkumulátorai nem javíthatók a felhasználó számára. 
* **Csak szervizszemélyzet**esetén : Robbanásveszély áll fenn, ha az akkumulátort nem megfelelő típusúra cserélik. A használt elemeket az utasításoknak megfelelően dobja ki.
* Lézerperifériák vagy eszközök vannak jelen. A kockázat vagy a sugárterhelés és/vagy személyi sérülés elkerülése érdekében ne nyissa ki semmilyen lézerperiféria vagy eszköz burkolatát. A lézerperifériák vagy eszközök nem javíthatók. Optikai adó-vevő termékhez csak tanúsított és minősített I. osztályú lézerosztályt használjon.

![Értesítésikon](./media/data-box-heavy-safety/notice-icon.png) **NOTICE:**

A készülék megfelelő működéséhez és a termék károsodásának megelőzéséhez:

* Győződjön meg arról, hogy az első és a hátsó ajtó teljesen nyitva van, miközben a készülék működik.

## <a name="regulatory-information"></a>Szabályozási információk

Ez a szakasz az Azure Data Box Heavy, a DB020 szabályozási modellszám szabályozási adatait tartalmazza.

Ez az eszköz:

- Informatikai berendezésként értékelve,(ITE), amelyet egy tipikus adattéri környezetben való működésre terveztek. A termék más környezetre való alkalmassága további értékelést tehet szükségessé.
- Az NRTL listázott (UL, CSA, ETL stb.) és iec/EN 60950-1 vagy IEC/EN 62368-1 szabványnak megfelelő (CE-jelöléssel ellátott) informatikai berendezésekkel való használatra tervezték.
- Úgy tervezték, hogy működjön a következő környezetben. 
    - Üzemi hőmérséklet: 41°-tól 95°F-ig (5°-35° C)
    - Tárolási hőmérséklet: -40° és 149° F (-40° és 65° C között)
    - Relatív páratartalom: 20% és 85% között (nem kondenzáció) 
    - Működési magasság: Legfeljebb 6560 láb (2000 méterig) tesztelve

Az elektromos áramra vonatkozó minősítéseket a készülékhez mellékelt készülékminősítési címkén tájékolt. 

![Értesítésikon](./media/data-box-heavy-safety/notice-icon.png) **NOTICE:** 

Az eszközön a Microsoft által kifejezetten jóvá nem hagyott módosítások vagy módosítások érvényteleníthetik a felhasználó nak az eszköz üzemeltetésére vonatkozó jogosultságát.

**Kanada és az USA:**

![Értesítésikon](./media/data-box-heavy-safety/notice-icon.png) **NOTICE:** 

Ezt a berendezést az FCC szabályzat 15. Ezeket a határértékeket úgy alakították ki, hogy ésszerű védelmet nyújtsanak a káros interferencia ellen, ha a berendezést kereskedelmi környezetben üzemeltetik. Ez a berendezés rádiófrekvenciás energiát generál, használ és sugározhat, és ha nem a használati utasításnak megfelelően szerelik be és használják, káros interferenciát okozhat a rádiókommunikációban. A berendezés lakóterületen történő működtetése valószínűleg káros interferenciát okoz, amely esetben a felhasználónak saját költségén kell korrigálnia az interferenciát.

Ez az eszköz megfelel az FCC Rules és az Industry Canada licencmentes RSS szabvány(ok) 15. A működésre a következő két feltétel vonatkozik: 

- A készülék nem okozhat káros interferenciát.
- A készüléknek el kell fogadnia minden fogadott interferenciát, beleértve az olyan interferenciát is, amely az eszköz nem kívánt működését okozhatja.

![Kanada](./media/data-box-heavy-safety/canada.png)

CAN ICES-3(A)/NMB-3(A) Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, AMERIKAI.
Egyesült Államok: (800) 426-9400 Kanada: (800) 933-4750

**Európai Unió:**

[Az](mailto:CSI_Compliance@microsoft.com) EU megfelelőségi nyilatkozat másolatának kérelmezése.

![Figyelem](./media/data-box-heavy-safety/warning-icon.png) ikon **FIGYELEM!** 

Ez egy "A" osztályú termék. Háztartási környezetben ez a termék rádióinterferenciát okozhat, amely esetben a felhasználónak megfelelő intézkedéseket kell tennie.

**A hulladékelemek, valamint az elektromos és elektronikus berendezések ártalmatlanítása:**

![Akkumulátorelvezetés ikonja](./media/data-box-heavy-safety/battery-disposal-icon.png)

Ez a szimbólum a terméken, annak akkumulátorain vagy csomagolásán azt jelenti, hogy a terméket és a benne lévő elemeket nem szabad a háztartási hulladékkal együtt megsemmisíteni. Ehelyett az Ön felelőssége, hogy ezt átadja az akkumulátorok, valamint az elektromos és elektronikus berendezések újrahasznosítására szolgáló, megfelelő gyűjtőhelynek. Ez a szelektív gyűjtés és újrahasznosítás segít megőrizni a természeti erőforrásokat, és megelőzni az emberi egészségre és a környezetre gyakorolt esetleges negatív következményeket a veszélyes anyagok elemekben, valamint az elektromos és elektronikus anyagokban való lehetséges jelenléte miatt. amelyeket a nem megfelelő ártalmatlanítás okozhat. Ha többet szeretne tudni arról, hogy hová kell leadni az elemeket, valamint az elektromos és elektronikus hulladékot, kérjük, forduljon a helyi városi/önkormányzati hivatalhoz, a háztartási hulladékkezelő szolgálathoz vagy ahhoz az üzlethez, ahol a terméket vásárolta. Az első és a microsoft.com kapcsolatban az első és a hulladékelőberendezésekkel kapcsolatos további információkért forduljon az *erecycle-hez.\@*

Ez a termék érmecella akkumulátort (ok)ot tartalmaz.

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL Telefonszám: +353 1 295 3826 Faxszám: +353 1 706 4110 

<!--**Japan**

![Japan](./media/data-box-heavy-safety/japan.png)-->

<!--**South Korea**

![South Korea](./media/data-box-heavy-safety/south-korea.png)

-->

Miután áttekintette ezeket a biztonsági közleményeket, beállíthatja és kábelezheti a készüléket.

## <a name="next-steps"></a>További lépések

* [Kábel és csatlakoztassa a Data Box Heavy](data-box-heavy-deploy-set-up.md)


