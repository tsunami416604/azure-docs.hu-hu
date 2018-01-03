---
title: "Eszköz - Azure modellezési Microsoft fenyegetés |} Microsoft Docs"
description: "További tudnivalók a fenyegetések modellezése eszköz elérhető összes szolgáltatást"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 76e49fc0e680acbc2b7b7c62b69fbf72d6690acf
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="threat-modeling-tool-feature-overview"></a>Fenyegetések modellezése eszköz funkcióinak áttekintése

A fenyegetések modellezése eszköz segítségével a veszéllyel modellezési igényeinek. Az eszköz alapszintű bevezetést, lásd: [Ismerkedjen meg a fenyegetések modellezése eszköz](./azure-security-threat-modeling-tool-getting-started.md).

> [!NOTE]
>A fenyegetések modellezése eszköz gyakran frissíteni, így a ennek az útmutatónak gyakran tekintse meg a legújabb szolgáltatásait és fejlesztéseit.

Egy üres lap megnyitásához válassza ki a **hozzon létre egy modellt**.

![Üres lap](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

Az eszköz jelenleg elérhető szolgáltatások megtekintéséhez használja a hozta létre a csapat a fenyegetések modellezése a [Ismerkedés](./azure-security-threat-modeling-tool-getting-started.md) példa.

![Alapszintű fenyegetések modellezése](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Navigációs

A beépített szolgáltatásai arról lesz szó, mielőtt tekintsük át, az eszköz megtalálható a fő összetevőket.

### <a name="menu-items"></a>Menüelemek

A felhasználói élmény hasonlít más Microsoft-termékeket. Tekintsük át, a legfelső szintű menüelemeket.

![Menüelemek](./media/azure-security-threat-modeling-tool-feature-overview/menuitems.png)

| Címke                               | Részletek      |
| --------------------------------------- | ------------ |
| **Fájl** | <ul><li>Nyissa meg, mentse és zárja be a fájlok</li><li>Jelentkezzen be, és jelentkezzen ki a OneDrive-fiókokat.</li><li>Táblázat hivatkozásaival megnyitható (megtekintés és Szerkesztés).</li><li>Fájl adatainak megtekintése.</li><li>Új sablon alkalmazása a meglévő modellt.</li></ul> |
| **Szerkesztése** | Visszavonási és visszaállítási műveleteket, valamint a másolás, illessze be, és törölni. |
| **Nézet** | <ul><li>Váltás a **elemzés** és **tervezési** nézetek.</li><li>Nyissa meg a lezárt windows (például rajzsablonok, tulajdonságokhoz és üzenetek).</li><li>Alapértelmezett beállításokra állította vissza elrendezés.</li></ul> |
| **Diagram** | Hozzáadása és törlése a diagramok és váltás a lapok diagramjainak. |
| **Jelentések** | Megosztja másokkal a HTML-jelentések létrehozása. |
| **segítség** | Az eszköz használatához nyújtanak segítséget az útmutatók található. |

A jelek a következők a legfelső szintű menük billentyűparancsainak:

| Szimbólum                               | Részletek      |
| --------------------------------------- | ------------ |
| **Nyissa meg** | Megnyit egy új fájlt. |
| **Mentése** | Menti az aktuális fájl. |
| **Tervezési** | Megnyitja a **tervezési** nézet, amellyel modellek hozható létre. |
| **Elemzés** | Azt mutatja be a fenyegetések és azok tulajdonságaival jönnek létre. |
| **Diagram hozzáadása** | Hozzáad egy új diagram (új lapokat, az Excel programban hasonló). |
| **Diagram törlése** | Törli a jelenlegi diagramhoz. |
| **Másolás, Kivágás és beillesztési** | Másolja, kivágása és illeszti be az elemeket. |
| **Visszavonási vagy visszaállítási** | Visszavonja, és ismét végrehajtja a műveleteket. |
| **Nagyítás / kicsinyítés** | A diagram jobb mindkét nagyít. |
| **Visszajelzés** | Megnyitja az MSDN-fórumon. |

### <a name="canvas"></a>Vászonra

A vászonra a hely, ahol Ön áthúzása elemek. Fogd és vidd módszer a leggyorsabb és leghatékonyabb módja modellek létrehozásához. Kattintson a jobb gombbal is, és válassza ki az elemeket a látható elemek, általános verziói felvenni kívánt menüből:

#### <a name="drop-the-stencil-on-the-canvas"></a>Helyezze át a rajzsablon a a vászonra

![Vászonra eldobási](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Válassza ki a rajzsablon

![Elem tulajdonságai](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Rajzsablonok

A sablon alapján, az összes elérhető rajzsablonok található. Ha nem találja a megfelelő elemeket, használjon egy másik sablont. Vagy a sablon szükség szerint módosíthatja. Általában kategóriák hasonlóan ezek kombinációja található:

| Rajzsablon neve                               | Részletek      |
| --------------------------------------- | ------------ |
| **Folyamat** | Alkalmazások, a böngésző beépülő modulok, a szálak, a virtuális gépek |
| **Külső interaktor** | Hitelesítésszolgáltatók, a böngészők, a felhasználók, a webes alkalmazások |
| **Adattár** | Gyorsítótár, a tárolási, a konfigurációs fájlok, a adatbázisok, a beállításjegyzék |
| **Adatfolyam** | Bináris, ALPC, HTTP, HTTPS vagy a TLS/SSL, IOCTL, IPSec, nevesített cső, RPC/DCOM, SMB, UDP |
| **Szegély/határ megbízhatóság** | Vállalati hálózatokhoz, a internet, a gép, a védőfal, a felhasználó és a kernel mód |

### <a name="notesmessages"></a>Megjegyzések/üzenetek

| Összetevő                               | Részletek      |
| --------------------------------------- | ------------ |
| **Üzenetek** | Belső eszköz logika, amely értesíti a felhasználókat, ha nem sikerül, például nem adatfolyamok elemek között. |
| **Megjegyzések** | Manuális megjegyzések mérnöki csapat a tervezési hozzáadódnak a fájlt, és tekintse át a folyamat. |

### <a name="element-properties"></a>Elem tulajdonságai

Kijelölt elemek elem tulajdonságai eltérők. Bizalmi kapcsolat határain kívül más elemek három általános beállításokat tartalmazza:

| Elem tulajdonság                               | Részletek      |
| --------------------------------------- | ------------ |
| **Name (Név)** | Akkor hasznos, ha a folyamatok, tárolók, interactors és adatfolyamok elnevezési, így most könnyen felismerhető. |
| **Hatókörén kívül** | Ha választja, az elem kívül a fenyegetés generációs mátrix (nem ajánlott) szükséges. |
| **Ezért a hatókörén kívül** | Választotta ki, hogy miért Hatókörön kívüli felhasználók indoklás mező. |

Tulajdonságok elem kategóriákban módosult. Válassza ki az elérhető lehetőségek a vizsgálandó egyes elemei. Vagy megnyithatja a sablon további. Tekintsük át, a szolgáltatások.

## <a name="welcome-screen"></a>Üdvözlőképernyő

Az alkalmazás megnyitásakor látni a **üdvözlő** képernyő.

### <a name="open-a-model"></a>Nyissa meg a modell

Vigye **nyissa meg A modell** hogy láthatóvá váljon a két lehetőség közül választhat: **nyissa meg a számítógépen** és **nyissa meg a onedrive vállalati verzió**. Az első lehetőség nyílik a **fájl megnyitása** képernyő. A második lehetőség végigvezeti Önt a bejelentkezési folyamat a onedrive vállalati verzióhoz. Sikeres hitelesítést követően kiválaszthatja a fájlokat és mappákat.

![Modell megnyitása](./media/azure-security-threat-modeling-tool-feature-overview/openmodel.png)

![Nyissa meg a számítógép vagy a onedrive vállalati verzió](./media/azure-security-threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Visszajelzés, a javaslatok és a problémák

Ha bejelöli **visszajelzést, a javaslatok és a problémák**, SDL eszközök az MSDN-fórumon Ugrás. Mi mások véleményét az eszközről, beleértve a lehetséges megoldások és új ötleteket érheti el.

![Visszajelzés](./media/azure-security-threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>Tervező nézetben

Hozzon létre egy új modell, vagy nyissa meg a **tervezési** megnyílik megtekintése.

### <a name="add-elements"></a>Elemek hozzáadása

A rács két módon adhat elemeket:

- **Adatértékmezők áthúzása**: a rács húzza a kívánt elemet. Az elem tulajdonságainak használatával további információkkal.
- **Kattintson a jobb gombbal**: kattintson a jobb gombbal a rács bárhol, és jelölje ki az elemet a legördülő menüből. A képernyőn megjelenik a kiválasztott elem általános megjelenítése.

### <a name="connect-elements"></a>Csatlakozás elemei

Elemek kétféle módon csatlakoztathatja:

- **Adatértékmezők áthúzása**: a kívánt adatfolyamblokk húzza a rács, és mindkét ends végpontjához kapcsolódjon a megfelelő elemeket.
- **Kattintson a + Shift**: kattintson az első elem (adatok küldése), nyomja le az ENTER és tartsa lenyomva a Shift billentyűt, és válassza a második (az adatok fogadása) elemet. Kattintson a jobb gombbal, és válassza ki **Connect**. Ha kétirányú az adatfolyam használata esetén sorrendje nem olyan fontos.

### <a name="properties"></a>Tulajdonságok

 A tulajdonságokat, amelyeket módosítható az a rajzsablonok, jelölje be a rajzsablon és az információk megjelenítéséhez tölti fel. ennek megfelelően. A következő példa bemutatja előtt és után egy **adatbázis** rajzsablon van húzott a diagram:

#### <a name="before"></a>Korábban, mint

![Korábban, mint](./media/azure-security-threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Később, mint

![Később, mint](./media/azure-security-threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Üzenetek

Ha a fenyegetések modellezése elfelejti elemek adatfolyamok csatlakozni, értesítést kap. Az üzenetet figyelmen kívül hagyhatja, vagy hajtsa végre az utasításokat a probléma elhárítása érdekében. 

![Üzenetek](./media/azure-security-threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Megjegyzések

Megjegyzések hozzáadása a diagramhoz, váltani a **üzenetek** beállítást a **megjegyzések** fülre.

## <a name="analysis-view"></a>Elemző nézet

A diagram létrehozása után válassza ki a **elemzés** szimbólum (Nagyító) a parancsikonok eszköztár váltson át a **Analysis** nézet.

![Elemző nézet](./media/azure-security-threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Generált fenyegetés kiválasztása

Fenyegetést kiválasztásakor, három különböző funkciókat használhatja:

| Szolgáltatás                               | Információ      |
| --------------------------------------- | ------------ |
| **Olvasási kijelző** | <p>A fenyegetés Olvasás, amely segítségével nyomon követheti, akkor tekintse át a elemek van megjelölve.</p><p>![Olvasás/olvasatlan kijelző](./media/azure-security-threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Interakció fókusz** | <p>A diagram, amelyek fenyegetést tartozik a beavatkozás ki van jelölve.</p><p>![Interakció fókusz](./media/azure-security-threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Fenyegetés-tulajdonságok** | <p>További információt a fenyegetés megjelenik a **fenyegetés tulajdonságok** ablak.</p><p>![Fenyegetés-tulajdonságok](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Prioritás módosítása

Módosíthatja az egyes létrehozott fenyegetésekre a prioritási szintet. Különböző színek megkönnyítik a nagy - közép- és alacsony prioritású fenyegetések azonosítására.

![Prioritás módosítása](./media/azure-security-threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Fenyegetés-tulajdonságok szerkeszthető mezők

Az előző ábrán látható, módosíthatja az adatokat az eszköz állítja elő. Információ is bizonyos mezőkön, például a indoklás adhat hozzá. Ezeket a mezőket a sablon által generált. Ha további információt szeretne az egyes fenyegetésekre, végezhet módosításokat.

![Fenyegetés-tulajdonságok](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Jelentések

Prioritásokat és minden egyes létrehozott fenyegetés állapotának módosítása után mentse a fájlt, vagy nyomtassa ki a jelentést. Ugrás a **jelentés** > **teljes jelentés létrehozása**. Nevezze el a jelentést, és más hasonló, a következő lemezképhez kell megjelennie:

![Jelentés](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>További lépések

* Egy sablon függ a Közösség számára, keresse fel a [GitHub](https://github.com/Microsoft/threat-modeling-templates) lap. 
* Ismerkedés az eszközzel, keresse fel a [letöltése](https://aka.ms/tmtpreview) lap.
