---
title: Riasztások beállítása
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogyan engedélyezheti a riasztásokat az Azure-beli digitális Twins-mérőszámokban.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: ded2f54379e60e8e3fc26d9c2166930a6f953078
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854861"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>Azure Digital Twins hibaelhárítása: riasztások

Az Azure Digital Twins a szolgáltatási példány [metrikáit](troubleshoot-metrics.md) gyűjti, amelyek információkat biztosítanak az erőforrások állapotáról. Ezeket a mérőszámokat felhasználhatja az Azure Digital Twins szolgáltatás általános állapotának felmérésére és a hozzájuk kapcsolódó erőforrásokra.

A **riasztások** proaktívan értesítik Önt, ha fontos feltételek találhatók a metrikák adataiban. Lehetővé teszik a problémák azonosítását és megcímzését, mielőtt a felhasználók a rendszerértesítéseket. A riasztásokról a [*Microsoft Azure riasztások áttekintése című*](../azure-monitor/platform/alerts-overview.md)témakörben olvashat bővebben.

## <a name="turn-on-alerts"></a>Riasztások bekapcsolása

Itt láthatja, hogyan engedélyezheti a riasztásokat az Azure Digital Twins-példányon:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az Azure Digital Twins-példányhoz. A nevét a portál keresési sávjába írja be. 

2. Válassza a **riasztások** lehetőséget a menüből, majd az **+ új riasztási szabályt**.

3. A *riasztási szabály létrehozása* lapon a következő utasításokat követve határozhatja meg a feltételeket, az aktiválható műveleteket és a riasztás részleteit.     
    * A **hatókör** részleteinek automatikusan ki kell töltenie a példány adatait
    * A riasztási eseményindítók és válaszok testreszabásához meg kell határoznia a **feltételt** és a **műveleti csoport** részleteit
    * A **riasztási szabály részletei** szakaszban adja meg a _riasztási szabály nevét_, _leírását (nem kötelező)_. Ha azt szeretné, hogy a riasztás a létrehozása után azonnal aktívvá váljon, jelölje be a _riasztási szabály engedélyezése a létrehozáskor_ jelölőnégyzetet.
    * A hozzáadott feltételek és műveletek alapján ki kell választania a _riasztási szabály mentése az erőforráscsoport_ és a _Súlyosság_ lehetőséget a megfelelő legördülő listából.

4. A riasztási szabály létrehozásához kattintson a _riasztási szabály létrehozása_ gombra.

:::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="Képernyőfelvétel: a riasztási szabály létrehozása lap a hatókör, a feltétel és a műveleti csoport fejezeteivel" lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

:::image type="content" source="media/troubleshoot-alerts/alert-rule-details.png" alt-text="A riasztási szabály létrehozásához tartozó riasztási szabály részletei szakaszt bemutató képernyőkép" lightbox="media/troubleshoot-alerts/alert-rule-details.png":::

A mezők kitöltését bemutató útmutató a [*Microsoft Azure riasztások áttekintése*](../azure-monitor/platform/alerts-overview.md)című témakörben található. Az alábbiakban néhány példát láthat arra, hogyan fog kinézni a lépések az Azure Digital Twins szolgáltatásban.

Íme egy részlet a *Select Condition (feltétel kiválasztása* ) folyamatból, amely bemutatja, hogy milyen típusú riasztások érhetők el az Azure digitális Twins szolgáltatásban. Ezen az oldalon szűrheti a jel típusát, és kiválaszthatja a listából a kívánt jeleket.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="Képernyőfelvétel: az első a jel logikai beállítása oldal. A jel típusa mezőben a mérőszámok vagy a tevékenységek naplóinak kiválasztására, valamint a kiválasztható mérőszámok listájára van lehetőség.":::

A jel kiválasztása után a rendszer kérni fogja a riasztás logikájának konfigurálását. Szűrheti a dimenziót, megadhatja a riasztás küszöbértékét, és beállíthatja a feltétel ellenőrzésének gyakoriságát. Íme egy példa arra, hogy mikor kell beállítani a riasztást, ha az átlagos útválasztási hiba arányának mérőszáma 5% fölé esik.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="A második, a jel logikai oldalának konfigurálását bemutató képernyőkép. A lapon láthatók a metrikák előzményei, egy olyan dimenzióval szűrhetők, mint a Event Grid műveletek, valamint egy szakasz a riasztási logika meghatározásához, például az "átlag nagyobb, mint 5"":::
 
A riasztások beállítása után a rendszer megjeleníti a példány *riasztások* lapját.
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="Képernyőfelvétel: a riasztások oldal és a hozzáadandó gomb. Egy riasztás van konfigurálva" lightbox="media/troubleshoot-alerts/alerts-post.png":::


## <a name="next-steps"></a>További lépések

* A Azure Monitor riasztásokkal kapcsolatos további információkért lásd: [*Microsoft Azure riasztások áttekintése*](../azure-monitor/platform/alerts-overview.md).
* További információ az Azure digitális Twins metrikáinak használatáról [*: a metrikák megtekintése Azure Monitorsal*](troubleshoot-metrics.md).
* Ha szeretné megtudni, hogyan engedélyezheti a diagnosztikai naplózást a metrikák számára, tekintse meg a [*Hibaelhárítás: diagnosztika beállítása*](troubleshoot-diagnostics.md)című témakört.
