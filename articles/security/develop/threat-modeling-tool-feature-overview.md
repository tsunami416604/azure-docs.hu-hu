---
title: A Microsoft Threat Modeling Tool szolgáltatásának áttekintése – Azure
description: Ismerje meg a Fenyegetésmodellezés eszközösszes elérhető funkcióját
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 08/17/2017
ms.openlocfilehash: 88eea0eb2bceb0f6322381a94d4339cbdfa662c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552117"
---
# <a name="threat-modeling-tool-feature-overview"></a>Fenyegetésmodellező eszköz – szolgáltatás – áttekintés

A fenyegetésmodellezési eszköz segíthet a fenyegetésmodellezési igények kielégítésében. Az eszköz alapvető bemutatását a [Fenyegetésmodellező eszköz első lépései című témakörben tésiterületen.](threat-modeling-tool-getting-started.md)

> [!NOTE]
>A Fenyegetésmodellezési eszköz gyakran frissül, ezért gyakran ellenőrizze ezt az útmutatót a legújabb funkciók és fejlesztések megtekintéséhez.

Üres lap megnyitásához válassza **a Modell létrehozása**lehetőséget.

![Üres oldal](./media/threat-modeling-tool-feature-overview/tmtstart.png)

Az eszközben jelenleg elérhető funkciók megtekintéséhez használja a csapatunk által létrehozott fenyegetésmodellt az [Első lépések](threat-modeling-tool-getting-started.md) példában.

![Alapvető fenyegetésmodell](./media/threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Navigáció

Mielőtt megbeszélnénk a beépített funkciókat, nézzük át az eszközben található fő összetevőket.

### <a name="menu-items"></a>Menüelemek

A felhasználói élmény hasonló más Microsoft-termékekhez. Tekintsük át a legfelső szintű menüpontokat.

![Menüelemek](./media/threat-modeling-tool-feature-overview/menuitems.png)

| Címke                               | Részletek      |
| --------------------------------------- | ------------ |
| **Fájl** | <ul><li>Fájlok megnyitása, mentése és bezárása</li><li>Jelentkezzen be és jelentkezzen ki a OneDrive-fiókokból.</li><li>Hivatkozások megosztása (megtekintés és szerkesztés).</li><li>Fájladatok megtekintése.</li><li>Új sablon alkalmazása meglévő modellekre.</li></ul> |
| **Szerkesztés** | A műveletek visszavonása és ismétlése, valamint másolás, beillesztés és törlés. |
| **Nézet** | <ul><li>Váltás **az Elemzés** és **a Tervezési** nézetek között.</li><li>Zárt ablakok at nyithat meg (például rajzsablonokat, elemtulajdonságokat és üzeneteket).</li><li>Az elrendezés visszaállítása az alapértelmezett beállításokra.</li></ul> |
| **Ábra** | Diagramok hozzáadása és törlése, valamint a diagramok lapjainak áthelyezése. |
| **Jelentések** | Html-jelentések et hozhat létre, amelyeket megoszthat másokkal. |
| **Súgó** | Útmutatók keresése az eszköz használatához. |

A szimbólumok a legfelső szintű menük parancsikonjai:

| Szimbólum                               | Részletek      |
| --------------------------------------- | ------------ |
| **Megnyitás** | Új fájl megnyitása. |
| **Mentés** | Az aktuális fájl mentése. |
| **Tervezés** | Megnyitja a **Tervező** nézetet, ahol modelleket hozhat létre. |
| **Elemzés** | A létrehozott fenyegetéseket és azok tulajdonságait jeleníti meg. |
| **Diagram hozzáadása** | Új diagramot ad hozzá (hasonlóan az Excel új lapjaihoz). |
| **Diagram törlése** | Az aktuális diagram törlése. |
| **Másolás/kivágás/beillesztés** | Másolatok, vágások és beillesztések elemek. |
| **Visszavonás/ismétlés** | Visszavonja és újravégrehajtja a műveleteket. |
| **Nagyítás/kicsinyítés** | A jobb nézet érdekében nagyítja és kicsinyíti a diagramot. |
| **Visszajelzés** | Megnyitja az MSDN Fórumot. |

### <a name="canvas"></a>Vászon

A vászon az a hely, ahová az elemeket áthúzza. Drag and drop a leggyorsabb és leghatékonyabb módja annak, hogy modelleket. A jobb gombbal kattintva és a menü elemeinek kiválasztásával az elemek általános verzióit is hozzáadhatja, akövetkezők nek megfelelően:

#### <a name="drop-the-stencil-on-the-canvas"></a>Dobd el a rajzsablont a vászonra

![Vászon leejtése](./media/threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>A rajzsablon kijelölése

![Elem tulajdonságai](./media/threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Rajzsablonok

A kiválasztott sablon alapján megtalálhatja az összes használható rajzsablont. Ha nem találja a megfelelő elemeket, használjon másik sablont. Vagy módosíthatja a sablont, hogy megfeleljen az Ön igényeinek. Általában az alábbi kategóriák kombinációját találja:

| Rajzsablon neve                               | Részletek      |
| --------------------------------------- | ------------ |
| **Folyamat** | Alkalmazások, böngésző plug-inek, szálak, virtuális gépek |
| **Külső interakciós** | Hitelesítésszolgáltatók, böngészők, felhasználók, webalkalmazások |
| **Adattár** | Gyorsítótár, tárolás, konfigurációs fájlok, adatbázisok, rendszerleíró adatbázis |
| **Adatfolyam** | Bináris, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, named pipe, RPC/DCOM, SMB, UDP |
| **Megbízhatósági vonal/határhatár** | Vállalati hálózatok, internet, gép, homokozó, felhasználó/kernel mód |

### <a name="notesmessages"></a>Megjegyzések/üzenetek

| Összetevő                               | Részletek      |
| --------------------------------------- | ------------ |
| **Üzenetek** | Belső eszközlogika, amely figyelmezteti a felhasználókat, ha hiba történik, például nincs adatfolyam az elemek között. |
| **Megjegyzések** | A tervezési és ellenőrzési folyamat során a mérnöki csapatok manuális megjegyzéseket adnak a fájlhoz. |

### <a name="element-properties"></a>Elem tulajdonságai

Az elemek tulajdonságai a kijelölt elemektől függően változnak. A megbízhatósági határokon kívül az összes többi elem három általános kijelölést tartalmaz:

| Elem tulajdonság                               | Részletek      |
| --------------------------------------- | ------------ |
| **Név** | Hasznos a folyamatok, üzletek, interactors és folyamatok elnevezéséhez, hogy könnyen felismerhetők legyenek. |
| **Hatókörön kívül** | Ha be van jelölve, az elem kikerül a fenyegetés-generálási mátrixból (nem ajánlott). |
| **A hatályon kívül eső ok** | Indoklás mező, amely tudatja a felhasználókkal, hogy miért lett kiválasztva hatókörön kívül. |

A tulajdonságok az egyes elemkategóriákban módosulnak. A rendelkezésre álló beállítások vizsgálatához jelölje ki az egyes elemeket. Vagy megnyithatja a sablont, hogy többet tudjon meg. Tekintsük át a funkciókat.

## <a name="welcome-screen"></a>Üdvözlőképernyő

Az alkalmazás megnyitásakor megjelenik az **üdvözlőképernyő.**

### <a name="open-a-model"></a>Modell megnyitása

Vigye az egérmutatót **a Modell megnyitása elemre** két lehetőség megnyitásához: **Megnyitás erről a számítógépről** és **megnyitás a OneDrive-ról**. Az első lehetőség megnyitja a **Fájl megnyitása** képernyőt. A második lehetőség végigvezeti a OneDrive-ra való bejelentkezésfolyamatán. A sikeres hitelesítés után mappákat és fájlokat jelölhet ki.

![Nyitott modell](./media/threat-modeling-tool-feature-overview/openmodel.png)

![Megnyitás számítógépről vagy OneDrive-ról](./media/threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Visszajelzések, javaslatok és problémák

Ha a Visszajelzés, javaslatok és problémák lehetőséget **választja,** keresse fel az MSDN SDL-eszközök fórumát. Elolvashatja, hogy mások mit mondanak az eszközről, beleértve a megoldásokat és az új ötleteket.

![Visszajelzés](./media/threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>Tervező nézet

Új modell megnyitásakor vagy létrehozásakor megnyílik a **Tervező** nézet.

### <a name="add-elements"></a>Elemek hozzáadása

Kétféleképpen adhat hozzá elemeket a rácshoz:

- **Húzás:** Húzza a kívánt elemet a rácsra. Ezután az elem tulajdonságaival további információkat adhat meg.
- **Kattintson a jobb gombbal:** Kattintson a jobb gombbal a rács tetszőleges pontjára, és válasszon elemeket a legördülő menüből. A képernyőn megjelenik a kijelölt elem általános ábrázolása.

### <a name="connect-elements"></a>Elemek csatlakoztatása

Az elemeket kétféleképpen kapcsolhatja össze:

- **Húzás:** Húzza a kívánt adatfolyamot a rácsra, és csatlakoztassa mindkét végét a megfelelő elemekhez.
- **Kattintson a + Shift**gombra : Kattintson az első elemre (adatok küldése), tartsa lenyomva a Shift billentyűt, majd jelölje ki a második elemet (adatok fogadása). Kattintson a jobb gombbal, és válassza a **Csatlakozás parancsot.** Ha kétirányú adatfolyamot használ, a sorrend nem olyan fontos.

### <a name="properties"></a>Tulajdonságok

 A rajzsablonokon módosítható tulajdonságok megtekintéséhez jelölje ki a rajzsablont, és ennek megfelelően az információ feltöltése. A következő példa az **adatbázis-rajzsablon** diagramra való húzása előtt és után látható:

#### <a name="before"></a>Előtte

![Előtte](./media/threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Utána

![Utána](./media/threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Üzenetek

Ha létrehoz egy fenyegetésmodellt, és elfelejti az adatfolyamokat az elemekhez kapcsolni, értesítést kap. Figyelmen kívül hagyhatja az üzenetet, vagy kövesse az utasításokat a probléma megoldásához. 

![Üzenetek](./media/threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Megjegyzések

Ha jegyzeteket szeretne hozzáadni a diagramhoz, váltson az **Üzenetek** lapról a **Jegyzetek** lapra.

## <a name="analysis-view"></a>Elemző nézet

A diagram létrehozása után válassza az **Elemzés** szimbólumot (a nagyítót) a billentyűparancsok eszköztárán az **Elemzés** nézetre való váltáshoz.

![Elemző nézet](./media/threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Létrehozott fenyegetés kiválasztása

Fenyegetés kiválasztásakor három különböző függvényt használhat:

| Szolgáltatás                               | Információ      |
| --------------------------------------- | ------------ |
| **Olvasásjelző** | <p>A fenyegetés olvasottként van megjelölve, amely segít nyomon követni az áttekintett elemeket.</p><p>![Olvasási/olvasatlan jelző](./media/threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Interakció fókusza** | <p>A fenyegetéshez tartozó diagramon kiemelt interakció van kiemelve.</p><p>![Interakció fókusza](./media/threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Fenyegetés tulajdonságai** | <p>A fenyegetéssel kapcsolatos további információk a **Fenyegetés tulajdonságai** ablakban jelennek meg.</p><p>![Fenyegetés tulajdonságai](./media/threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Prioritás módosítása

Módosíthatja az egyes létrehozott fenyegetések prioritási szintjét. A különböző színek megkönnyítik a magas, közepes és alacsony prioritású fenyegetések azonosítását.

![Prioritás módosítása](./media/threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Fenyegetéstulajdonságai szerkeszthető mezők

Az előző képen látható módon módosíthatja az eszköz által létrehozott információkat. Bizonyos mezőkhöz, például az indokláshoz is hozzáadhat adatokat. Ezeket a mezőket a sablon hozza létre. Ha további információra van szüksége az egyes fenyegetések, módosíthatja.

![Fenyegetés tulajdonságai](./media/threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Jelentések

Miután befejezte a prioritások módosítását és az egyes létrehozott fenyegetések állapotának frissítését, mentheti a fájlt és/vagy kinyomtathatja a jelentést. Nyissa meg a **Jelentés** > **létrehozása a teljes jelentéshez**című a főt. Nevezze el a jelentést, és az alábbihoz hasonló képet kell látnia:

![Jelentés](./media/threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>További lépések

- Küldje el kérdéseit, észrevételeit tmtextsupport@microsoft.comés aggályait a alkalmazásnak. **[A](https://aka.ms/threatmodelingtool)** kezdéshez töltse le a Fenyegetésmodellezési eszközt.
- Ahhoz, hogy hozzájáruljon egy sablont a közösség, látogasson el a [GitHub](https://github.com/Microsoft/threat-modeling-templates) oldalon.