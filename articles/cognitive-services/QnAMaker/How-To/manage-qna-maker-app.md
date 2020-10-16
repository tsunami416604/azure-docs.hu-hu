---
title: QnA Maker alkalmazás kezelése – QnA Maker
description: QnA Maker lehetővé teszi, hogy több személy is működjenek együtt a Tudásbázisban. A QnA Maker lehetővé teheti a Tudásbázis aktív tanulással való minőségének javítását. Az egyik áttekintheti, elfogadhatja vagy elutasíthatja a meglévő kérdések eltávolítása vagy módosítása nélkül.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/11/2020
ms.openlocfilehash: 77290d271709db36f9c62e165b0b4070783b3ec6
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92128477"
---
# <a name="manage-qna-maker-app"></a>QnA Maker alkalmazás kezelése

A QnA Maker lehetővé teszi a különböző szerzők és a tartalomkezelési szerkesztők együttműködését azáltal, hogy a közreműködő szerepköre alapján korlátozza a közreműködők hozzáférését.
További információ a [QnA Maker közreműködő hitelesítési fogalmakról](../Concepts/role-based-access-control.md).

A Tudásbázis minőségét úgy is javíthatja, ha alternatív kérdéseket tesz fel az [aktív tanuláson](../Concepts/active-learning-suggestions.md)keresztül. A felhasználói beadványok figyelembe vesznek, és javaslatokként jelennek meg az alternatív kérdések listájában. Lehetősége van arra, hogy alternatív kérdésként adja hozzá ezeket a javaslatokat, vagy utasítsa el őket.

A Tudásbázis nem változik automatikusan. A módosítások érvénybe léptetéséhez el kell fogadnia a javaslatokat. Ezek a javaslatok kérdéseket tesznek fel, de nem módosítják vagy nem távolítják el a meglévő kérdéseket.

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) hozzáadása

QnA Maker lehetővé teszi, hogy több személy is működjenek együtt az összes Tudásbázisban ugyanabban a QnA Maker erőforrásban. Ez a szolgáltatás [Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC)](../../../active-directory/role-based-access-control-configure.md)érhető el.

## <a name="access-at-the-qna-maker-resource-level"></a>Hozzáférés a QnA Maker erőforrás szintjén

QnA Maker szolgáltatásban nem oszthat meg egy adott tudásbázist. Ha részletesebb hozzáférés-vezérlést szeretne, érdemes lehet a tudásbázist különböző QnA Maker erőforrásokon keresztül kiosztania, majd szerepköröket hozzáadnia az egyes erőforrásokhoz.

## <a name="add-a-role-to-a-resource"></a>Szerepkör hozzáadása egy erőforráshoz

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Felhasználói fiók hozzáadása a QnA Maker erőforráshoz

A következő lépések a közreműködő szerepkört használják, de a [szerepkörök](../reference-role-based-access-control.md) bármelyike felvehető a következő lépések használatával

1. Jelentkezzen be az [Azure](https://portal.azure.com/) Portalra, és lépjen a QnA Maker-erőforráshoz.

    ![QnA Maker erőforráslista](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Lépjen a **Access Control (iam)** lapra.

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Válassza a **Hozzáadás** lehetőséget.

    ![QnA Maker IAM Hozzáadás](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Válasszon ki egy szerepkört a következő listából:

    |Szerepkör|
    |--|
    |Tulajdonos|
    |Közreműködő|
    |Cognitive Services QnA Maker olvasó|
    |Cognitive Services QnA Maker szerkesztő|
    |Cognitive Services felhasználó|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker a szerepkör hozzáadása.":::

1. Adja meg a felhasználó e-mail-címét, és kattintson a **Mentés**gombra.

    ![QnA Maker e-mail-cím hozzáadása](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>QnA Maker tudásbázisok megtekintése

Ha az a személy, akivel a QnA Maker szolgáltatását a [QnA Maker portálra](https://qnamaker.ai)osztotta, az adott szolgáltatás összes tudásbázisát láthatja a szerepkörük alapján.

Ha kijelölnek egy tudásbázist, az adott QnA Maker erőforráson aktuális szerepkörük látható a Tudásbázis neve mellett.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="QnA Maker a szerepkör hozzáadása.":::

## <a name="upgrade-runtime-version-to-use-active-learning"></a>Futtatókörnyezet verziójának frissítése az aktív tanulás használatához

Az aktív tanulást a futtatókörnyezet 4.4.0 és újabb verziói támogatják. Ha a Tudásbázis egy korábbi verzión lett létrehozva, [frissítse a futtatókörnyezetet](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) a szolgáltatás használatára.

## <a name="turn-on-active-learning-for-alternate-questions"></a>Alternatív kérdések aktív tanulásának bekapcsolása

Az aktív tanulás alapértelmezés szerint ki van kapcsolva. A javasolt kérdések megtekintéséhez kapcsolja be a következőt:. Az aktív tanulás bekapcsolását követően el kell küldenie az adatokat az ügyfélalkalmazástól a QnA Maker. További információ: [építészeti folyamat a GenerateAnswer és a Train API-k egy robotból való használatához](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. A Tudásbázis közzétételéhez válassza a **Közzététel** lehetőséget. Az aktív tanulási lekérdezések csak a GenerateAnswer API-előrejelzési végpontról lesznek összegyűjtve. A QnA Maker portál teszt ablaktáblájára irányuló lekérdezések nem érintik az aktív tanulást.

1. Az aktív tanulás bekapcsolásához a QnA Maker-portálon nyissa meg a jobb felső sarokban, válassza ki a **nevét**, és lépjen a [**Szolgáltatásbeállítások**](https://www.qnamaker.ai/UserSettings)menüpontra.

    ![Kapcsolja be az aktív tanulás javasolt kérdéseit a szolgáltatás beállításai lapról. Válassza ki a felhasználónevét a jobb felső menüben, majd válassza a Szolgáltatásbeállítások elemet.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Keresse meg a QnA Maker szolgáltatást, majd állítsa be az **aktív tanulást**.

    > [!div class="mx-imgBorder"]
    > [![A szolgáltatás beállításai lapon kapcsolja be az aktív tanulási funkciót. Ha nem tudja váltani a szolgáltatást, előfordulhat, hogy frissítenie kell a szolgáltatást.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)
    > [!Note]
    > Az előző képen megadott pontos verzió csak példaként jelenik meg. A verzió eltérő lehet.
    Az **aktív tanulás** engedélyezése után a Tudásbázis rendszeres időközönként új kérdéseket javasol a felhasználó által benyújtott kérdések alapján. Az **aktív tanulást** letilthatja a beállítás újbóli bekapcsolásával.

## <a name="review-suggested-alternate-questions"></a>Javasolt alternatív kérdések áttekintése

[Tekintse át a további javasolt kérdéseket](improve-knowledge-base.md) az egyes tudásbázisok **szerkesztési** lapján.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudásbázis létrehozása](./manage-knowledge-bases.md)