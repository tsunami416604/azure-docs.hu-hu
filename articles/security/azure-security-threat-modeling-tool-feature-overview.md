---
title: Microsoft Threat Modeling Tool – Azure |} A Microsoft Docs
description: Ismerje meg az összes funkcióját a Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 8bbc13aa77c50c6ae02ecc67869035e7ec85effa
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359340"
---
# <a name="threat-modeling-tool-feature-overview"></a>Threat Modeling Tool funkcióinak áttekintése

A Threat Modeling Tool segíthet a threat modeling igényeinek. Az eszköz alapszintű bevezetést, lásd: [a Threat Modeling Tool – első lépések](./azure-security-threat-modeling-tool-getting-started.md).

> [!NOTE]
>A Threat Modeling Tool rendszeresen frissül, ezért kérjük, ez az útmutató gyakran, tekintse meg a legújabb funkciókat és fejlesztéseket.

Üres lap megnyitásához válassza **hozzon létre egy modellt**.

![Üres lap](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

Az eszköz jelenleg elérhető funkcióját használja a fenyegetések modellezése hozta létre a csapatunk a [Ismerkedés](./azure-security-threat-modeling-tool-getting-started.md) példa.

![Alapszintű fenyegetések modellezése](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Navigáció

Mielőtt a beépített funkciók tárgyaljuk, tekintsük át a fő összetevőit, az eszközben található.

### <a name="menu-items"></a>Menü elemei

A felhasználói élményt a más Microsoft-termékek hasonló. Tekintsük át a legfelső szintű menüelemeket.

![Menü elemei](./media/azure-security-threat-modeling-tool-feature-overview/menuitems.png)

| Címke                               | Részletek      |
| --------------------------------------- | ------------ |
| **Fájl** | <ul><li>Nyissa meg, mentse és zárja be a fájlokat</li><li>Jelentkezzen be, és jelentkezzen ki a OneDrive-fiókja.</li><li>Ossza meg a hivatkozások (megtekintés és Szerkesztés).</li><li>Információk megtekintése.</li><li>Egy új sablont alkalmazni a meglévő modelleket.</li></ul> |
| **Szerkesztése** | Visszavonása, és ismételje meg a műveleteket, valamint a másolás, beillesztés, és törlése. |
| **Nézet** | <ul><li>Váltás a **elemzési** és **tervezési** nézeteket.</li><li>Nyissa meg a lezárt windows (például rajzsablonokhoz, tulajdonságokhoz és üzenetek).</li><li>Elrendezés alaphelyzetbe állítása az alapértelmezett beállításokat.</li></ul> |
| **Diagram** | Hozzáadása és törlése a diagramok és váltás a lapok diagrammal. |
| **Jelentések** | Hozzon létre a HTML-jelentéseket másokkal való megosztásához. |
| **segítség** | Keresse meg az útmutatók segítséget nyújtanak az eszközzel. |

A következők a legfelső szintű menük billentyűparancsainak a szimbólumokat tartalmazhatja:

| Szimbólum                               | Részletek      |
| --------------------------------------- | ------------ |
| **Nyissa meg a** | Megnyílik egy új fájlt. |
| **Mentés** | Az aktuális fájlba menti. |
| **Tervezés** | Megnyílik a **tervezési** nézet, ahol modelleket hozhat létre. |
| **Elemzés** | Látható a fenyegetések és a hozzájuk tartozó tulajdonságok jönnek létre. |
| **Diagram hozzáadása** | Hozzáad egy új diagram (hasonló új lapokat, az Excelben). |
| **Diagram törlése** | Törli a jelenlegi diagramhoz. |
| **Másolás és Kivágás/beillesztési** | Másolja, és csökkenti radikálisan a elemek illeszti be. |
| **Undo/Redo** | Visszavonja, és a művelet megismétlése. |
| **Nagyítás / kicsinyítés** | A diagram jobb adataikkal közeledik. |
| **Visszajelzés** | Az MSDN-fórum megnyitása. |

### <a name="canvas"></a>Vászon

A vászon az a terület, ahol Ön áthúzása elemeket. Fogd és vidd módja a leggyorsabb és leghatékonyabb modelleket hozhat létre. Kattintson a jobb gombbal is, és elem kijelölése lehetőséget a menüből adja hozzá a általános verziói elemek láthatók szerint:

#### <a name="drop-the-stencil-on-the-canvas"></a>A vásznon a rajzsablonon DROP

![Vászonalapú eldobási](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Válassza ki a rajzsablonon

![Elem tulajdonságai](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Rajzsablonokhoz

A kiválasztott sablon alapján, az összes elérhető rajzsablonokhoz talál. Ha nem találja a megfelelő elemeket, használjon egy másik sablont. Vagy egy sablont, saját igényei szerint módosíthatja. Az alábbiakhoz hasonló kategóriák kombinációját Általánosságban elmondható, találja:

| Rajzsablonon neve                               | Részletek      |
| --------------------------------------- | ------------ |
| **Folyamat** | Alkalmazások, a böngésző beépülő modulok, a szálak, a virtuális gépek |
| **Külső interaktor** | Hitelesítési szolgáltatók, a böngésző, a felhasználók, a webes alkalmazások |
| **Adattár** | Gyorsítótár, storage, konfigurációs fájlok, adatbázisok, beállításjegyzék |
| **Az adatfolyam** | Binary, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, named pipe, RPC/DCOM, SMB, UDP |
| **Szegély/határ megbízhatóság** | A vállalati hálózatok, a internet, a gép, a védőfal, a felhasználói és rendszermag módú |

### <a name="notesmessages"></a>Megjegyzések és üzenetek

| Összetevő                               | Részletek      |
| --------------------------------------- | ------------ |
| **Üzenetek** | Belső eszköz logika, amely értesíti a felhasználókat, amikor nincs hiba, például az elemek között nincs adatáramlás. |
| **Megjegyzések** | Manuális megjegyzések mérnöki csapat a tervezési hozzáadódnak a fájlt, és tekintse át a folyamat. |

### <a name="element-properties"></a>Elem tulajdonságai

A kiválasztott elemek tulajdonságai eltérők. Szereplőkkel bizalmi kapcsolat határain minden más elemet a három általános beállításokat tartalmazza:

| Az elemtulajdonság                               | Részletek      |
| --------------------------------------- | ------------ |
| **Name (Név)** | A folyamatok, tárolók, interactors és folyamatok elnevezéséhez, hogy Ön könnyen elismert hasznos. |
| **Hatókörén kívül** | Kiválasztva, ha az elem van kivehetők a fenyegetés generációs mátrix (nem ajánlott). |
| **Ezért a hatókörén kívül** | Indoklás a mező, hogy a felhasználók hatókörén kívül miért lett kiválasztva. |

Minden elem kategóriához tartozó tulajdonságok módosult. Válassza ki az egyes elemei vizsgálhatja meg az elérhető lehetőségek közül. Vagy megnyithatja a sablont további. Tekintse át a szolgáltatásokat.

## <a name="welcome-screen"></a>Üdvözlőképernyő

Az alkalmazás megnyitásakor megjelenik a **üdvözlő** képernyő.

### <a name="open-a-model"></a>Nyissa meg a modell

A kurzort **egy nyílt modellre** két lehetőség megjelenítéséhez: **Megnyitás erről a számítógépről** és **nyissa meg a onedrive-ról**. Az első lehetőség megnyílik a **fájl megnyitása** képernyő. A második lehetőség végigvezeti a bejelentkezési folyamat a onedrive vállalati verzióhoz. Sikeres hitelesítés után kiválaszthatja a fájlokat és mappákat.

![Modell megnyitása](./media/azure-security-threat-modeling-tool-feature-overview/openmodel.png)

![Nyissa meg a számítógép vagy a onedrive vállalati verzió](./media/azure-security-threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Visszajelzés, javaslat és problémák

Kiválasztásakor **visszajelzés, javaslat és problémák**, akkor nyissa meg az MSDN-fórum az SDL-eszközökhöz. Mások véleményét az eszközről, beleértve a lehetséges megoldások és az új ötletek olvashat.

![Visszajelzés](./media/azure-security-threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>Tervező nézetben

Hozzon létre egy új modell, vagy nyissa meg a **tervezési** megnyílik megtekintéséhez.

### <a name="add-elements"></a>Elemek hozzáadása

A rács két módon adhat hozzá elemeket:

- **Húzza át**: A rács húzza a kívánt elemet. Az elem tulajdonságainak használatával adjon meg további információkat.
- **Kattintson a jobb gombbal**: Kattintson a jobb gombbal a rács bárhol, és válassza ki az elemet a legördülő menüből. A képernyőn megjelenik a kiválasztott elem általános reprezentációját.

### <a name="connect-elements"></a>Csatlakozás elemek

Elemek kétféle módon csatlakoztathatja:

- **Húzza át**: Húzza a kívánt adatfolyam a rácshoz, és mindkét csatlakozzon a megfelelő elemeket.
- **Kattintson a + Shift**: Az első elem (adatok küldése) gombra, nyomja le és tartsa lenyomva a Shift billentyűt és válassza ki a második elem (fogad adatokat). Kattintson a jobb gombbal, és válassza ki **Connect**. Ha egy kétirányú adatfolyamot használja, a sorrendben, nem olyan fontos.

### <a name="properties"></a>Tulajdonságok

 A Tulajdonságok módosítható a rajzsablonokhoz, válassza a rajzsablonon és az információk megjelenítéséhez tölti fel. ennek megfelelően. Az alábbi példa bemutatja a előtt és után egy **adatbázis** rajzsablonon van húzni a diagram:

#### <a name="before"></a>Korábban, mint

![Korábban, mint](./media/azure-security-threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Később, mint

![Később, mint](./media/azure-security-threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Üzenetek

Ha a fenyegetések modellezése létrehozása, és adatfolyam-gyűjteményre csatlakozni elemek elfelejtette, értesítést kap. Az üzenet figyelmen kívül hagyhatja, vagy az utasításokat a probléma megoldásához követheti. 

![Üzenetek](./media/azure-security-threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Megjegyzések

Megjegyzések hozzáadása a diagramhoz, váltani szeretne a **üzenetek** lapról a **megjegyzések** fülre.

## <a name="analysis-view"></a>Elemzés megtekintése

Miután hoz létre a diagramon, válassza ki a **elemzési** szimbólum (Nagyító) váltson át a parancsikonok eszköztáron a **Analysis** megtekintése.

![Elemzés megtekintése](./media/azure-security-threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Generált threat kiválasztása

Amikor kiválaszt egy fenyegetést, három különböző funkciót is használhatja:

| Szolgáltatás                               | Információ      |
| --------------------------------------- | ------------ |
| **Olvasási kijelző** | <p>A fenyegetés olvassa el, így a segítségével nyomon követheti, a korábban áttekintett elemek van megjelölve.</p><p>![Olvasás/olvasatlan kijelző](./media/azure-security-threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Interakció fókusz** | <p>Az ábrán a fenyegetés tartozó kapcsolati ki van emelve.</p><p>![Interakció fókusz](./media/azure-security-threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Veszélyforrások elleni tulajdonságai** | <p>További információ a fenyegetés jelenik meg a **Threat tulajdonságok** ablak.</p><p>![Veszélyforrások elleni tulajdonságai](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Prioritásának módosítása

Minden egyes létrehozott threat prioritását módosíthatja. Különböző színek megkönnyítik a nagy - közép- és az alacsony prioritású fenyegetések azonosítására.

![Prioritásának módosítása](./media/azure-security-threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Veszélyforrások elleni tulajdonságok szerkeszthető mezők

Ahogy az előző képen látható, módosíthatja a az eszköz által létrehozott adatokat. Bizonyos mezők, például az indoklás hozzáadhat adatokat is. Ezek a mezők a sablon alapján jönnek létre. Ha további információra van szüksége az egyes fenyegetési, végezhet módosításokat.

![Veszélyforrások elleni tulajdonságai](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Jelentések

Prioritások és minden egyes létrehozott threat állapotának módosítása után mentse a fájlt, illetve nyomtassa ki a jelentést. Lépjen a **jelentés** > **teljes jelentés létrehozása**. Nevezze el a jelentést, és az alábbi képhez hasonló kell megjelennie:

![Jelentés](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>További lépések

* Közreműködés egy sablont a Közösség számára, lépjen a [GitHub](https://github.com/Microsoft/threat-modeling-templates) lapot. 
* Az eszköz használatának megkezdéséhez lépjen a [letöltése](https://aka.ms/tmtpreview) lapot.
