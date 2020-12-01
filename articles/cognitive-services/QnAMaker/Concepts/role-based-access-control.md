---
title: Együttműködés másokkal – QnA Maker
description: ''
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: ecf81152605d77e1036c9fbf5d3308af2b1fb988
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96346155"
---
# <a name="collaborate-with-other-authors-and-editors"></a>Együttműködés más szerzőkkel és szerkesztőkkel

Együttműködik más szerzőkkel és szerkesztőkkel az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával, amely a QnA Maker-erőforrásra van helyezve.

## <a name="access-is-provided-on-the-qna-maker-resource"></a>A hozzáférés a QnA Maker erőforráson van megadva

Minden engedélyt a QnA Maker erőforrásra helyezett engedélyek szabályoznak. Ezek az engedélyek az olvasási, írási, közzétételi és teljes hozzáférésre vannak igazítva.

Ez az Azure RBAC szolgáltatás a következőket tartalmazza:
* A Azure Active Directory (HRE) 100%-os visszamenőleges kompatibilitást biztosít a tulajdonosok és a közreműködők kulcs alapú hitelesítésével. A felhasználók a kérelmekben kulcs alapú hitelesítést vagy Azure RBAC-alapú hitelesítést használhatnak.
* A szerzők és szerkesztők gyorsan hozzáadhatók az erőforrás összes tudásbázisához, mivel a vezérlő az erőforrás szintjén van, nem pedig a Tudásbázis szintjén.

## <a name="access-is-provided-by-a-defined-role"></a>A hozzáférést egy meghatározott szerepkör adta meg

[!INCLUDE [Azure RBAC permissions table](../includes/role-based-access-control.md)]

## <a name="authentication-flow"></a>Hitelesítési folyamat

A következő ábrán látható a folyamat, a szerző szemszögéből, a QnA Maker portálra való bejelentkezéshez és a szerzői API-k használatával.

> [!div class="mx-imgBorder"]
> ![A következő ábrán látható a folyamat, a szerző szemszögéből, a QnA Maker portálra való bejelentkezéshez és a szerzői API-k használatával.](../media/qnamaker-how-to-collaborate-knowledge-base/rbac-flow-from-portal-to-service.png)

|Lépések|Leírás|
|--|--|
|1|A portál QnA Maker erőforráshoz tartozó jogkivonatot vásárol.|
|2|A portál meghívja a megfelelő QnA Maker authoring API-t (APIM), amely a tokent a kulcsok helyett átadja.|
|3|QnA Maker API érvényesíti a jogkivonatot.|
|4 |QnA Maker API meghívja a QnAMaker szolgáltatást.|

Ha meg szeretné hívni a [szerzői API-kat](../index.yml), ismerkedjen meg a hitelesítés beállításával.

## <a name="authenticate-by-qna-maker-portal"></a>Hitelesítés QnA Maker portálon

Ha a QnA Maker-portál használatával hoz létre és együttműködik, miután [hozzáadta az erőforráshoz a megfelelő szerepkört egy közreműködőhöz](../index.yml), a QnA Maker-portál kezeli az összes hozzáférési engedélyt.

## <a name="authenticate-by-qna-maker-apis-and-sdks"></a>Hitelesítés QnA Maker API-k és SDK-k használatával

Ha az API-k használatával hoz létre és együttműködik a REST vagy az SDK-k segítségével, [létre kell hoznia egy egyszerű szolgáltatásnevet](../../authentication.md#assign-a-role-to-a-service-principal) a hitelesítés kezeléséhez.

## <a name="next-step"></a>Következő lépés

* Tudásbázis kialakítása [nyelvekhez](../index.yml) és [ügyfélalkalmazások](../index.yml) számára