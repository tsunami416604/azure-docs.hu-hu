---
title: A lekéréses kérelmek áttekintése az Azure statikus Web Apps üzem előtti környezetekben
description: Megtudhatja, hogyan használhatja az üzem előtti környezeteket a pull-kérelmek változásának áttekintésére az Azure statikus Web Apps.
services: static-web-apps
author: sinedied
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: yolasors
ms.openlocfilehash: f8f2e352ae458e3e2825c9701437ea652ba07375
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825667"
---
# <a name="review-pull-requests-in-pre-production-environments-in-azure-static-web-apps-preview"></a>Lekéréses kérelmek áttekintése éles üzem előtti környezetekben az Azure Static Web Apps előzetes verziójában

Ez a cikk bemutatja, hogyan használhatja az üzem előtti környezeteket az [Azure statikus web Appsával](overview.md)üzembe helyezett alkalmazások változásainak áttekintéséhez.

Az üzem előtti (átmeneti) környezet az alkalmazás teljes funkcionalitású, szakaszos verziója, amely az éles környezetben nem elérhető módosításokat is tartalmazza.

Az Azure statikus Web Apps egy GitHub-műveletek munkafolyamatot hoz létre a tárházban. Ha egy lekéréses kérelem jön létre a munkafolyamat által figyelt ág alapján, az üzem előtti környezet épül. Az éles üzem előtti környezet az alkalmazás előállítása, amely lehetővé teszi, hogy az éles környezetben történő továbbítás előtt végezzen értékeléseket.

Több üzem előtti környezet is létezhet egyszerre az Azure statikus Web Apps használatakor. Minden alkalommal, amikor lekéréses kérelmet hoz létre a figyelt ág alapján, a módosításokat egy külön üzem előtti környezetben helyezik üzembe.

Az üzem előtti környezetek használatának számos előnye van. Megteheti például a következőt:

- Tekintse át az éles környezet és az előkészítés közötti vizuális változásokat. Például megtekintheti a frissítéseket a tartalomra és az elrendezésre.
- A csapat változásainak bemutatása.
- Hasonlítsa össze az alkalmazás különböző verzióit.
- A módosítások érvényesítése elfogadási tesztek használatával.
- Az éles környezetben való üzembe helyezés előtt végezze el a józan ész-ellenőrzéseket.

> [!NOTE]
> Az előzetes [verzióban egyszerre legfeljebb egy átmeneti környezet](quotas.md) engedélyezett.

## <a name="prerequisites"></a>Előfeltételek

- Egy meglévő GitHub-tárház, amely az Azure statikus Web Apps van konfigurálva. Ha még nem rendelkezik ilyennel, tekintse [meg az első statikus alkalmazás felépítése](getting-started.md) című témakört.

## <a name="make-a-change"></a>Módosítás létrehozása

Kezdje azzal, hogy megváltoztatja a tárházat. Ezt közvetlenül a GitHubon végezheti el, az alábbi lépésekben látható módon.

1. Navigáljon a projekt adattárához a GitHubon, majd kattintson a **ág** gombra egy új ág létrehozásához.

    :::image type="content" source="./media/review-publish-pull-requests/create-branch.png" alt-text="Új ág létrehozása a GitHub Interface használatával":::]

    Írja be az ág nevét, majd kattintson a **Létrehozás ág**elemre.

1. Lépjen az _alkalmazás_ mappájába, és módosítsa a szöveges tartalmakat. Megváltoztathatja például a címet vagy a bekezdést. Ha megtalálta a szerkeszteni kívánt fájlt, kattintson a **Szerkesztés** gombra a módosítás végrehajtásához.

    :::image type="content" source="./media/review-publish-pull-requests/edit-file.png" alt-text="Új ág létrehozása a GitHub Interface használatával":::

1. A módosítások elvégzése után kattintson a **módosítások** elvégzése gombra, hogy véglegesítse a módosításokat a fiókirodában.

    :::image type="content" source="./media/review-publish-pull-requests/commit-changes.png" alt-text="Új ág létrehozása a GitHub Interface használatával":::

## <a name="create-a-pull-request"></a>Lekéréses kérelem létrehozása

Ezután hozzon létre egy lekéréses kérelmet ebből a változásból.

1. Nyissa meg a projekt **pull-kérelmek** lapját a githubon:

    :::image type="content" source="./media/review-publish-pull-requests/tab.png" alt-text="Új ág létrehozása a GitHub Interface használatával":::

1. Kattintson az ág **& pull-kérésének összevetése** gombra.

1. Igény szerint kitöltheti a módosításokat, majd kattintson a **pull-kérelem létrehozása**lehetőségre.

    :::image type="content" source="./media/review-publish-pull-requests/open.png" alt-text="Új ág létrehozása a GitHub Interface használatával":::

Szükség esetén megtekintheti a véleményezőket, és megjegyzéseket fűzhet hozzájuk.

> [!NOTE]
> Több módosítást is végrehajthat, ha új véglegesítéseket küld az ágra. A lekéréses kérelem automatikusan frissül, hogy tükrözze az összes módosítást.

## <a name="review-changes"></a>Változások áttekintése

A lekéréses kérelem létrehozása után a [GitHub-műveletek](https://github.com/features/actions) üzembe helyezési munkafolyamata fut, és üzembe helyezi a módosításokat egy üzem előtti környezetben.

Miután a munkafolyamat befejezte az alkalmazás létrehozását és üzembe helyezését, a GitHub-robot hozzáadja a lekéréses kérelemhez egy megjegyzést, amely tartalmazza az üzem előtti környezet URL-címét. Erre a hivatkozásra kattintva megtekintheti az előkészített módosításokat.

:::image type="content" source="./media/review-publish-pull-requests/bot-comment.png" alt-text="Új ág létrehozása a GitHub Interface használatával":::

Kattintson a generált URL-címre a módosítások megtekintéséhez.

Ha közelebbről megtekinti az URL-címet, láthatja, hogy a következőhöz hasonló: `https://<SUBDOMAIN-PULL_REQUEST_ID>.<AZURE_REGION>.azurestaticapps.net` .

Egy adott lekéréses kérelem esetében az URL-cím akkor is ugyanaz marad, ha leküldi az új frissítéseket. Amellett, hogy az URL-cím állandó marad, a lekéréses kérelem élettartama megegyezik az üzem előtti környezettel.

## <a name="publish-changes"></a>Változások közzétevése

A módosítások jóváhagyása után a lekéréses kérelem egyesítésével közzéteheti az éles környezetben végzett módosításokat.

Kattintson a **lekéréses kérelem egyesítése**lehetőségre:

:::image type="content" source="./media/review-publish-pull-requests/merge.png" alt-text="Új ág létrehozása a GitHub Interface használatával" ág) számára. Ezután az üzembe helyezési munkafolyamat elindul a követett ág esetében, és a módosítások az alkalmazás újraépítése után élőben jelennek meg.

Az éles környezetben lévő változások ellenőrzéséhez nyissa meg az éles URL-címet a webhely élő verziójának betöltéséhez.

## <a name="limitations"></a>Korlátozások

Az alkalmazás szakaszos verziói jelenleg nyilvánosan elérhetők az URL-címmel, még akkor is, ha a GitHub-adattár privát.

> [!WARNING]
> Ügyeljen arra, hogy a bizalmas tartalmat a szakaszos verzióra tegye közzé, mert az üzem előtti környezetekhez való hozzáférés nem korlátozott.

A statikus Web Apps üzembe helyezett alkalmazások számára elérhető üzem előtti környezetek száma a használt SKU-szinttől függ. Az ingyenes szinten például az éles környezet mellett 1 üzem előtti környezet is lehet.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyéni tartomány beállítása](custom-domain.md)
