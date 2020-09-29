---
title: A riasztások mellőzése szabályok használatával elkerülheti a hamis pozitív vagy más nemkívánatos biztonsági riasztásokat a Azure Security Centerban.
description: Ez a cikk azt ismerteti, hogyan használhatók a Azure Security Center letiltási szabályai a nemkívánatos Azure Defender-riasztások elrejtéséhez
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 09/10/2020
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: c4eb30df74e2a8d6748ede987df0b1c41cff0ca3
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448485"
---
# <a name="suppress-alerts-from-azure-defender"></a>Riasztások letiltása az Azure Defenderből

Ez az oldal azt ismerteti, hogyan használhatja a riasztások letiltási szabályait a hamis pozitív vagy más nemkívánatos biztonsági riasztások mellőzéséhez az Azure Defendertől.

## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Előnézet|
|Árképzési|Ingyenes<br>(A legtöbb biztonsági riasztás csak az Azure Defenderrel érhető el)|
|Szükséges szerepkörök és engedélyek:|A **biztonsági rendszergazda** és a **tulajdonos** létrehozhat/törölhet szabályokat.<br>A **biztonsági olvasó** és az **olvasó** megtekintheti a szabályokat.|
|Felhők|![Igen](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Igen](./media/icons/yes-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||


## <a name="what-are-suppression-rules"></a>Mik azok a letiltási szabályok?

A különböző Azure Defender-csomagok a környezet bármely területén észlelnek fenyegetéseket, és biztonsági riasztásokat hoznak.

Ha egyetlen riasztás nem érdekes vagy releváns, akkor manuálisan is elvégezheti. Azt is megteheti, hogy a letiltási szabályok funkció használatával automatikusan elveti a hasonló riasztásokat a jövőben. Általában letiltási szabályt kell használnia a következőkhöz:

- Hamis pozitívként azonosított riasztások letiltása

- A túl gyakran kiváltott riasztások mellőzése

A letiltási szabályok határozzák meg azokat a feltételeket, amelyek esetében a riasztásokat automatikusan el kell utasítani.

> [!CAUTION]
> A biztonsági riasztások letiltása csökkenti az Azure Defender veszélyforrások elleni védelmi hatékonyságát. Körültekintően ellenőrizze az esetleges elnyomási szabályok lehetséges hatását, és figyelje az idő múlásával.

:::image type="content" source="./media/alerts-suppression-rules/create-suppression-rule.gif" alt-text="Riasztási elnyomási szabály létrehozása":::

## <a name="create-a-suppression-rule"></a>Mellőzési szabály létrehozása

A nemkívánatos biztonsági riasztások figyelmen kívül hagyása érdekében többféleképpen hozhat létre szabályokat:

- Ha el szeretné tiltani a riasztásokat a felügyeleti csoport szintjén, használja a Azure Policy
- Ha a riasztásokat az előfizetési szinten szeretné letiltani, a Azure Portal vagy a REST API az alább leírtak szerint használhatja

A tiltási szabályok csak azokat a riasztásokat hagyhatják el, amelyek már aktiválva lettek a kijelölt előfizetéseken.

Szabály létrehozása közvetlenül a Azure Portalban:

1. Security Center biztonsági riasztások oldaláról:

    - Keresse meg azt a riasztást, amelyet nem szeretne többé látni, majd a riasztás három pont (...) menüjéből válassza az **elnyomási szabály létrehozása**elemet:

        [![* * Elnyomási szabály létrehozása * * beállítás](media/alerts-suppression-rules/auto-dismiss-future-option.png)](media/alerts-suppression-rules/auto-dismiss-future-option.png#lightbox)

    - Vagy válassza a **mellőzési szabályok** hivatkozást az oldal tetején, és a letiltási szabályok lapon válassza az **új tiltási szabály létrehozása**elemet:

        ![Új letiltási szabály létrehozása * * gomb](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. Az új letiltási szabály panelen adja meg az új szabály részleteit.
    - A szabály **minden erőforráson** elvetheti a riasztást, így a későbbiekben nem kap ilyen riasztást.     
    - A szabály lemondhatja a riasztást **bizonyos feltételek** esetén – ha egy adott IP-címhez, folyamat nevéhez, felhasználói fiókhoz, Azure-erőforráshoz vagy helyhez kapcsolódik.

    > [!TIP]
    > Ha egy adott riasztásban megnyitotta az új szabály lapot, a rendszer automatikusan konfigurálja a riasztást és az előfizetést az új szabályban. Ha az **új letiltási szabály létrehozása** hivatkozást használta, a kiválasztott előfizetések megfelelnek a portál aktuális szűrőjének.

    [![Mellőzési szabály létrehozási panelje](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)
1. Adja meg a szabály részleteit:
    - **Név** – a szabály neve. A szabályok nevének betűvel vagy számmal kell kezdődnie, 2 és 50 karakter közöttinek kell lennie, és nem tartalmazhat kötőjelet (-) vagy aláhúzást (_) tartalmazó szimbólumot. 
    - **Állapot** -engedélyezve vagy letiltva.
    - **OK** – válasszon egyet a beépített okok közül, vagy a "más" lehetőséget, ha azok nem felelnek meg az igényeinek.
    - **Lejárati dátum** – a szabály befejezési dátuma és időpontja. A szabályok akár hat hónapig is futhatnak.
1. Szükség esetén a **szimulálás** gombbal tesztelheti a szabályt, hogy megtekintse, hány riasztást kellett volna elutasítani, ha ez a szabály aktív volt.
1. Mentse a szabályt. 


## <a name="edit-a-suppression-rules"></a>Mellőzési szabályok szerkesztése

A létrehozott szabályok szerkesztéséhez használja a letiltási szabályok lapot.

1. A Security Center biztonsági riasztásai lapon válassza a **letiltási szabályok** hivatkozást az oldal tetején.
1. Megnyílik a letiltási szabályok lap, amely a kijelölt előfizetésekre vonatkozó összes szabályt megjeleníti.

    [![Mellőzési szabályok listája](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. Egyetlen szabály szerkesztéséhez nyissa meg a szabályhoz tartozó három pontot (...), és válassza a **Szerkesztés**lehetőséget.
1. Végezze el a szükséges módosításokat, majd válassza az **alkalmaz**lehetőséget. 

## <a name="delete-a-suppression-rule"></a>Mellőzési szabály törlése

Egy vagy több létrehozott szabály törléséhez használja a letiltási szabályok lapot.

1. A Security Center biztonsági riasztásai lapon válassza a **letiltási szabályok** hivatkozást az oldal tetején.
1. Megnyílik a letiltási szabályok lap, amely a kijelölt előfizetésekre vonatkozó összes szabályt megjeleníti.
1. Egyetlen szabály törléséhez nyissa meg a szabály három pontot (...), majd válassza a **Törlés**lehetőséget.
1. Több szabály törléséhez jelölje be a törölni kívánt szabályok jelölőnégyzetét, majd válassza a **Törlés**lehetőséget.
    ![Egy vagy több elnyomási szabály törlése](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="view-suppressed-alerts"></a>Letiltott riasztások megtekintése

Az engedélyezett letiltási szabályoknak megfelelő riasztások továbbra is létrejönnek, de az állapotukat a rendszer **elutasítja**. Megtekintheti az állapotot a Azure Portalban, vagy hozzáférhet a Security Center biztonsági riasztásokhoz. 

> [!TIP]
> Az [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) nem hoz létre incidenseket a letiltott riasztásokhoz. Más SIEM-EK esetében a riasztások állapota ("elhagyva") használatával szűrheti a letiltott riasztásokat.

A szabályok által elvetett riasztások megtekintéséhez használja Security Center szűrőjét.

* A Security Center biztonsági riasztások lapján nyissa meg a szűrési beállításokat, **és válassza az elvetett lehetőséget.**  

   [![Elbocsátott riasztások megtekintése](media/alerts-suppression-rules/view-dismissed-alerts.png)](media/alerts-suppression-rules/view-dismissed-alerts.png#lightbox)


## <a name="create-and-manage-suppression-rules-with-the-api"></a>Letiltási szabályok létrehozása és kezelése az API-val

A riasztások letiltási szabályait a Security Center REST API segítségével hozhatja létre, tekintheti meg vagy törölheti. 

A REST API a következő letiltási szabályokra vonatkozó HTTP-metódusok:

- **Put**: letiltási szabály létrehozása vagy frissítése egy adott előfizetésben.

- **Beolvasás**:

    - Egy adott előfizetéshez konfigurált összes szabály listázása. Ez a metódus a vonatkozó szabályok tömbjét adja vissza.

    - Egy adott előfizetéshez tartozó adott szabály részleteinek beolvasása. Ez a metódus egy letiltási szabályt ad vissza.

    - Egy letiltási szabály hatásának szimulálása még a tervezési fázisban. Ez a hívás azonosítja, hogy a meglévő riasztások közül melyik lett elvetve, ha a szabály aktív volt.

- **Törlés**: töröl egy meglévő szabályt (de nem módosítja az általa már elvetett riasztások állapotát).

A részletekért és a használati példákért tekintse meg az [API dokumentációját](https://docs.microsoft.com/rest/api/securitycenter/). 


## <a name="next-steps"></a>Következő lépések

Ez a cikk az Azure Security Center letiltási szabályait ismerteti, amelyek automatikusan elmulasztják a nemkívánatos riasztásokat.

Az Azure Defender biztonsági riasztásokkal kapcsolatos további információkért tekintse meg a következő lapokat:

- [Biztonsági riasztások és a szándék-megölési lánc](alerts-reference.md) – útmutató az Azure Defender által esetlegesen kapott biztonsági riasztásokhoz.