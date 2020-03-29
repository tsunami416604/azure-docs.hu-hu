---
title: Többeszközes beszélgetés (előzetes verzió) – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: b3802e66b0ba5a68c898e69ec64b01edce1541c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371358"
---
# <a name="what-is-multi-device-conversation-preview"></a>Mi az a többeszközes beszélgetés (előzetes verzió)?

**A többeszközes beszélgetés** megkönnyíti a beszéd- vagy szöveges beszélgetés létrehozását több ügyfél között, és koordinálja a közöttük küldött üzeneteket.

A **többeszközes beszélgetéssel**a következőkre van szüksége:

- Több ügyfél csatlakoztatása ugyanabba a beszélgetésbe, és kezelheti a köztük lévő üzenetek küldését és fogadását.
- Könnyen átírhatja a hangot az egyes ügyfelektől, és elküldheti az átírást a többieknek, opcionális fordítással.
- Egyszerűen küldhet szöveges üzeneteket az ügyfelek között, opcionális fordítással.

Létrehozhat egy olyan funkciót vagy megoldást, amely számos eszközön működik. Minden eszköz önállóan küldhet üzeneteket (hang- vagy azonnali üzenetek átiratait) az összes többi eszközre.

Míg [**a beszélgetésátírás**](conversation-transcription.md) egyetlen eszközön működik többcsatornás mikrofontömbbel, a **többeszközös beszélgetés** alkalmas több eszközzel rendelkező forgatókönyvekhez, amelyek mindegyike egyetlen mikrofonnal rendelkezik.

>[!IMPORTANT]
> A többeszközes beszélgetés **nem** támogatja a hangfájlok ügyfelek közötti küldését: csak az átírást és/vagy a fordítást.

## <a name="key-features"></a>A legfontosabb jellemzők

- **Valós idejű átírás** – Mindenki kap egy átiratát a beszélgetés, így követni a szöveget valós időben, vagy mentse el későbbre.
- **Valós idejű fordítás** – Több mint 60 [támogatott szövegfordítási nyelvvel](language-support.md#text-languages) a felhasználók lefordíthatják a beszélgetést a kívánt nyelv(ek)re.
- **Olvasható átiratok** - Az átírás és a fordítás könnyen követhető, az írásjelek és a mondat szünetek.
- **Hang- vagy szövegbevitel** – Minden felhasználó a saját eszközén is szólhat vagy gépelhet, attól függően, hogy a résztvevő által választott nyelvhez engedélyezett nyelvi támogatási lehetőségek milyen nyelvi beállításokat biztosít. Kérjük, olvassa el [a nyelvi támogatást](language-support.md#speech-to-text).
- **Üzenettovábbítás** – A többeszközes beszélgetési szolgáltatás az egyik ügyfél által küldött üzeneteket az általuk választott nyelven (nyelvben) továbbítja.
- **Üzenetazonosítás** – A beszélgetés során a felhasználók által kapott minden üzenet az azt küldő felhasználó becenevével lesz megjelölve.

## <a name="use-cases"></a>Használati esetek

### <a name="lightweight-conversations"></a>Könnyű beszélgetések

A beszélgetés létrehozása és az ahhoz való csatlakozás egyszerű. Egy felhasználó fog működni, mint a "fogadó", és hozzon létre egy beszélgetést, amely létrehoz egy véletlenszerű öt betűs beszélgetés kódot és egy QR-kódot. Az összes többi felhasználó csatlakozhat a beszélgetéshez a beszélgetési kód beírásával vagy a QR-kód beolvasásával. 

Mivel a felhasználók a beszélgetési kódon keresztül csatlakoznak, és nem kötelesek megosztani a kapcsolattartási adatokat, egyszerűen hozhat létre gyors, helyszíni beszélgetéseket.

### <a name="inclusive-meetings"></a>Befogadó találkozók

A valós idejű átírás és fordítás segíthet abban, hogy a beszélgetések hozzáférhetők legyenek a különböző nyelveket és/vagy siketeket vagy nagyothallók számára. Minden személy aktívan részt vehet a beszélgetésben, ha az általa preferált nyelvet beszélik, vagy azonnali üzeneteket küldenek.

### <a name="presentations"></a>Bemutatók

A képernyőn és a közönség tagjainak saját eszközein is megadhatfeliratokat bemutatókhoz és előadásokhoz. Miután a közönség csatlakozott a beszélgetési kódhoz, az átiratot a saját nyelvükön, a saját eszközükön láthatják.

> [!NOTE]
> Példát például a [Bemutatófordító](https://www.microsoft.com/translator/apps/presentation-translator/)című PowerPoint-bővítmény ben, amely a többeszközes beszélgetési szolgáltatást használja. A fájlt [innen](https://www.microsoft.com/download/details.aspx?id=55024) töltheti le.

## <a name="how-it-works"></a>Működés

Minden ügyfél a beszédbeszéd SDK-t fogja használni egy beszélgetés létrehozásához vagy az ahhoz való csatlakozáshoz. A beszédközbeni SDK együttműködik a többeszközös beszélgetési szolgáltatással, amely a beszélgetés élettartamát kezeli, beleértve a résztvevők listáját, az egyes ügyfelek választott nyelvét(eke)t és az elküldött üzeneteket.  

Minden ügyfél küldhet hang- vagy csevegőüzeneteket. A szolgáltatás beszédfelismeréssel alakítja át a hangot szöveggé, és azonnali üzeneteket küld a hogy-van. Ha az ügyfelek különböző nyelveket választanak, akkor a szolgáltatás az összes üzenetet az egyes ügyfelek megadott nyelvére fordítja le.

![Többeszközes beszélgetésáttekintő diagram](media/scenarios/multi-device-conversation.png)

## <a name="overview-of-conversation-host-and-participant"></a>Beszélgetés, fogadó és résztvevő áttekintése

A **beszélgetés** olyan munkamenet, amelyhez az egyik felhasználó elindul, hogy a többi résztvevő felhasználó csatlakozzon. Minden ügyfél az ötbetűs **beszélgetési kóddal**csatlakozik a beszélgetéshez.

Minden beszélgetés olyan metaadatokat hoz létre, amelyek a következőket tartalmazzák:
-    A beszélgetés kezdésének és befejeztének időbélyegei
-    A beszélgetés összes résztvevőjének listája, amely tartalmazza az egyes felhasználók által választott becenevet és a beszéd- vagy szövegbevitel elsődleges nyelvét.


A beszélgetésben kétféle felhasználó van: **a gazdagép** és **a résztvevő.**

A **gazdagép** az a felhasználó, aki beszélgetést indít, és aki a beszélgetés adminisztrátoraként működik.
- Minden beszélgetésnek csak egy állomása lehet
- A fogadónak a beszélgetés időtartama alatt kapcsolódnia kell a beszélgetéshez. Ha a házigazda kilép a beszélgetésből, a beszélgetés az összes többi résztvevő számára véget ér.
- A gazdagép néhány további vezérlővel rendelkezik a beszélgetés kezeléséhez: 
    - A beszélgetés zárolása – további résztvevők csatlakozásának megakadályozása
    - Az összes résztvevő elnémítása – annak megakadályozása, hogy a többi résztvevő üzeneteket küldjön a beszélgetésnek, akár beszédből, akár csevegőüzenetekből átírt
    - Az egyes résztvevők elnémítása
    - Az összes résztvevő visszahangosítása
    - Az egyes résztvevők visszahangosítása

A **résztvevő** olyan felhasználó, aki csatlakozik egy beszélgetéshez.
- A résztvevők bármikor elhagyhatják és újra csatlakozhatnak ugyanahhoz a beszélgetéshez anélkül, hogy a többi résztvevő számára befejezenék a beszélgetést.
- A résztvevők nem zárhatják le a beszélgetést, illetve nem némíthatják el/nem hangosíthatják vissza a többieket

> [!NOTE]
> Minden beszélgetéslegfeljebb 100 résztvevővel rendelkezhet, amelyek közül 10 egyszerre beszélhet egy adott időpontban.

## <a name="language-support"></a>Nyelvi támogatás

Beszélgetés létrehozásakor vagy csatlakozásakor minden felhasználónak ki kell választania egy **elsődleges nyelvet:** azt a nyelvet, amelyen beszélni fog, és amelyben azonnali üzeneteket küld, valamint azt a nyelvet, amelyen más felhasználók üzeneteit fogja látni.

Kétféle nyelv létezik: **beszéd-szöveg** és **csak szöveg:**
- Ha a felhasználó a **beszédfelismerési** nyelvet választja elsődleges nyelvként, akkor a beszélgetésben beszéd- és szövegbevitelt is használhat.

- Ha a felhasználó **csak szöveges** nyelvet választ, akkor csak szövegbevitelt és azonnali üzeneteket tud küldeni a beszélgetésben. A csak szöveges nyelvek azok a nyelvek, amelyek et a szövegfordítás támogatott, de a szöveggé való felolvasást nem. Az elérhető nyelveket a nyelvi támogatási oldalon [láthatja.](supported-languages.md)

Az elsődleges nyelvmellett minden résztvevő további nyelveket is megadhat a beszélgetés fordításához.

Az alábbiakban összefoglaljuk, hogy a felhasználó mit tehet egy többeszközes beszélgetésben, a választott elsődleges nyelv alapján.


| Mit tehet a felhasználó a beszélgetésben? | Diktálás | Csak szöveges |
|-----------------------------------|----------------|------|
| Beszédbevitel használata | ✔️ | ❌ |
| Csevegőüzenetek küldése | ✔️ | ✔️ |
| Beszélgetés fordítása | ✔️ | ✔️ |

> [!NOTE]
> Az elérhető beszéd-szöveg és szövegfordítási nyelvek listáját a [támogatott nyelvek című témakörben tetszés szerint.](supported-languages.md)



## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Beszélgetések valós időben fordítása](quickstarts/multi-device-conversation.md)
