---
title: Riasztási munkafolyamatok felgyorsítása
description: A riasztások és incidensek munkafolyamatainak javítása.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 14d7a0de1cd29b8c07f90c759a4d423d7186fdb9
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839676"
---
# <a name="accelerate-alert-workflows"></a>Riasztási munkafolyamatok felgyorsítása

Ez a cikk a riasztási munkafolyamatok felgyorsítását mutatja be a riasztási megjegyzések, a riasztási csoportok és az egyéni riasztási szabályok használatával az Azure Defender for IoT.  Ezek az eszközök segítenek a következőket:

- A hálózatban észlelt nagy mennyiségű riasztási esemény elemzése és kezelése.

- Meghatározott hálózati tevékenységek kimutatása és kezelése.

## <a name="accelerate-incident-workflows-by-using-alert-comments"></a>Incidens-munkafolyamatok felgyorsítása a riasztási megjegyzésekkel

A riasztási megjegyzésekkel segítheti a kommunikációt a magánszemélyek és a csapatok között a riasztási esemény vizsgálata során.

:::image type="content" source="media/how-to-work-with-alerts-sensor/suspicion-of malicious-activity-screen.png" alt-text="A rosszindulatú tevékenységeket bemutató képernyőkép.":::

A javításhoz használjon riasztási megjegyzéseket:

- **Munkafolyamat lépései**: riasztások enyhítésének lépései.

- **Munkafolyamat követése**: értesítés arról, hogy a lépések végrehajtása megtörtént.

- **Munkafolyamat-útmutató**: javaslatok, információk vagy figyelmeztetések megadása az eseményről.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-comment-screen.png" alt-text="A riasztási megjegyzéseket bemutató képernyőkép.":::

Az elérhető lehetőségek listája megjelenik az egyes riasztásokban. A felhasználók kijelölhetnek egy vagy több üzenetet.

Riasztási megjegyzések hozzáadása:

1. Az oldalsó menüben válassza a **Rendszerbeállítások** elemet.

2. A **Rendszerbeállítás** ablakban válassza a **riasztási megjegyzések** elemet.

3. A **Megjegyzések hozzáadása** mezőben adja meg a megjegyzés szövegét. Legfeljebb 50 karaktert használhat. A vesszők nem megengedettek.

4. Válassza a **Hozzáadás** elemet.

## <a name="accelerate-incident-workflows-by-using-alert-groups"></a>Incidens-munkafolyamatok felgyorsítása riasztási csoportok használatával

A riasztási csoportok lehetővé teszik a SOC-csapatok számára, hogy az SIEM-megoldásokban megtekintsék és szűrhetik a riasztásokat, majd a vállalati biztonsági szabályzatok és üzleti prioritások alapján Az új észlelésekkel kapcsolatos riasztások például egy felderítési csoportban vannak rendszerezve. Ez a csoport olyan riasztásokat tartalmaz, amelyek az új eszközök, új VLAN-ok, új felhasználói fiókok, új MAC-címek és egyebek észlelésével foglalkoznak.

A riasztási csoportok akkor lépnek életbe, amikor a következő partneri megoldások továbbítási szabályait hozza létre:

  - Syslog-kiszolgálók

  - QRadar

  - ArcSight

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule.png" alt-text="A továbbítási szabály létrehozásának képernyőképe.":::

A kapcsolódó riasztási csoport a partner kimeneti megoldásaiban jelenik meg. 

### <a name="requirements"></a>Követelmények

A riasztási csoport a támogatott partneri megoldásokban fog megjelenni a következő előtagokkal:

  - **Cat** for QRadar, ArcSight, syslog CEF, syslog LEEF

  - **Riasztási csoport** a syslog szöveges üzeneteihez

  - Syslog-objektumok **alert_group**

Ezeket a mezőket a partneri megoldásban kell konfigurálni a riasztási csoport nevének megjelenítéséhez. Ha nincs riasztás társítva a riasztási csoporthoz, a partner megoldás mezőjében a **na** érték jelenik meg.

### <a name="default-alert-groups"></a>Alapértelmezett riasztási csoportok

A következő riasztási csoportok automatikusan definiálva vannak:
|  |  |  |
|--|--|--|
| Rendellenes kommunikációs viselkedés | Egyéni riasztások | Távelérés |
| Rendellenes HTTP-kommunikáció viselkedése | Felderítés | Parancsok újraindítása és leállítása |
| Hitelesítés | Belső vezérlőprogram módosítása | Vizsgálat |
| Jogosulatlan kommunikációs viselkedés | Nem engedélyezett parancsok | Érzékelő forgalma |
| Sávszélesség-rendellenességek | Internet-hozzáférés | Kártevők gyanúja |
| Puffer túlcsordulása | Művelet-meghibásodások | Rosszindulatú tevékenység gyanúja |
| Sikertelen parancsok | Működési problémák |  |
| Konfigurációs változások | Programozási |  |

A riasztási csoportok előre definiálva vannak. A riasztási csoportokkal kapcsolatos riasztásokkal és az egyéni riasztási csoportok létrehozásával kapcsolatos részletekért forduljon [Microsoft ügyfélszolgálatahoz](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="customize-alert-rules"></a>Riasztási szabályok testreszabása

Egyéni riasztási szabályokat adhat meg az egyes érzékelők által észlelhető információk alapján. Definiáljon például egy olyan szabályt, amely arra utasítja az érzékelőt, hogy a forrás IP-címe, a cél IP-címe vagy a parancs (egy protokollon belül) alapján aktiválja a riasztást. Ha az érzékelő észleli a szabályban meghatározott forgalmat, egy riasztás vagy esemény jön létre.

A riasztási üzenet azt jelzi, hogy egy felhasználó által definiált szabály aktiválta a riasztást.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Képernyőkép, amely testreszabott riasztásokat jelenít meg.":::

Egyéni riasztási szabály létrehozása:

1. Válassza az **egyéni riasztások** lehetőséget az érzékelő oldalsó menüjében.
1. A szabály létrehozásához válassza a pluszjelet ( **+** ).

   :::image type="content" source="media/how-to-work-with-alerts-sensor/user-defined-rule.png" alt-text="A felhasználó által definiált szabályt megjelenítő képernyőkép.":::

1. Adja meg a szabály nevét.
1. Válasszon ki egy kategóriát vagy protokollt a **Kategóriák** ablaktáblán.
1. Definiáljon egy adott forrás-és cél IP-címet vagy MAC-címet, vagy válasszon címet.
1. Feltétel hozzáadása. A feltételek és tulajdonságaik listája minden kategória esetében egyedi. Minden egyes riasztáshoz kijelölhet egynél több feltételt is.
1. Jelezze, hogy a szabály **riasztást** vagy **eseményt** indít el.
1. Súlyossági szint társítása a riasztáshoz.
1. Jelezze, hogy a riasztás tartalmaz-e PCAP fájlt.
1. Válassza a **Mentés** lehetőséget.

A szabály hozzá lesz adva a **testreszabott riasztási szabályok** listájához, ahol áttekintheti az alapszintű szabályok paramétereit, a szabály utolsó indításának időpontját és egyebeket. A szabály a listából is engedélyezhető és letiltható.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="A felhasználó által hozzáadott testreszabott szabály képernyőképe.":::

### <a name="see-also"></a>További információ

[A riasztásokban megadott információk megtekintése](how-to-view-information-provided-in-alerts.md)

[A riasztási esemény kezelése](how-to-manage-the-alert-event.md)
