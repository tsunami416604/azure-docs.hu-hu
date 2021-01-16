---
title: Gördülékeny felhőalapú Azure-partneri megoldások kezelése
description: Ez a cikk bemutatja, hogyan kell egy Fluent felhőt a Azure Portal. Egyszeri bejelentkezés beállítása, egy Fluent szervezet törlése és támogatás kérése.
ms.service: partner-services
ms.topic: conceptual
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 2d13c183f0b3891fa92b5e2a6534acbf8102e032
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253504"
---
# <a name="manage-the-confluent-cloud-resource"></a>Az Fluent felhő erőforrásának kezelése

Ez a cikk azt ismerteti, hogyan kezelheti Apache Kafka-példányát az Azure-hoz készült Fluent-felhőben. Bemutatja, hogyan állíthat be egyszeri bejelentkezést (SSO), hogyan törölhet egy Fluent szervezetet, és hozzon létre egy támogatási kérést.

## <a name="single-sign-on"></a>Egyszeri bejelentkezés

A bérlői rendszergazda a saját szervezete számára is importálhatja az SSO-t a katalógus alkalmazásba. Ez a lépés nem kötelező. Az alkalmazások importálásával kapcsolatos információkért tekintse meg a rövid útmutató [: alkalmazás hozzáadása a Azure Active Directory (Azure ad) bérlőhöz](../../active-directory/manage-apps/add-application-portal.md)című témakört. Ha a bérlői rendszergazda importálja az alkalmazást, a felhasználóknak nem kell explicit módon beleegyezniük ahhoz, hogy engedélyezzék a hozzáférést a felhőalapú portálhoz.

Az egyszeri bejelentkezés engedélyezéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon a Fluent Felhőbeli erőforrás példányának **áttekintéséhez** .
1. Válassza ki azt a hivatkozást, amely az **Fluent felhővel való felügyeletre** vonatkozik.

   :::image type="content" source="media/sso-link.png" alt-text="A portálon történő egyszeri bejelentkezés.":::

1. Ha a bérlői rendszergazda nem importálta a katalógus-alkalmazást az egyszeri bejelentkezéshez, adja meg az engedélyeket és a jóváhagyást. Erre a lépésre csak akkor van szükség, amikor először fér hozzá a ( **z) és** a (z) közötti kapcsolathoz.
1. Válasszon ki egy Azure AD-fiókot az egyszeri bejelentkezéshez az Fluent felhő portálon.
1. A beleegyezikség megadása után a rendszer átirányítja a felhőalapú portálra.

## <a name="set-up-cluster"></a>Fürt beállítása

A fürt beállításával kapcsolatos további információkért lásd: [fürt létrehozása a felhőben – Fluent – gördülékeny dokumentáció](https://docs.confluent.io/cloud/current/clusters/create-cluster.html).

## <a name="delete-confluent-organization"></a>A Fluent szervezet törlése

Ha már nincs szüksége a Fluent Felhőbeli erőforrásra, törölje az erőforrást az Azure-ban, és a felhőben is.

Az erőforrások törlése az Azure-ban:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki az **összes erőforrást** , és szűrje _az Fluent felhő_ erőforrásának vagy az **erőforrástípus-típusnak** a **neve** alapján. Vagy a Azure Portalban keresse meg az erőforrás nevét.
1. Az erőforrás **áttekintésében** válassza a **Törlés** lehetőséget.

    :::image type="content" source="media/delete-resources-icon.png" alt-text="Erőforrás-törlési ikon.":::

1. A törlés megerősítéséhez írja be az erőforrás nevét, és válassza a **Törlés** lehetőséget.

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="A rendszer felszólítja az erőforrás törlésének megerősítésére.":::

Ha szeretné törölni az erőforrást az Fluent-felhőben, tekintse meg a következő témakört: a többtényezős felhőalapú [környezetek – Fluent dokumentáció](https://docs.confluent.io/current/cloud/using/environments.html) és a [felhő alapjai – Fluent-dokumentáció](https://docs.confluent.io/current/cloud/using/cloud-basics.html).

A fürt és a fürtben lévő összes érték véglegesen törlődik. Ha az Ön szerződése adatmegőrzési záradékot tartalmaz, akkor a rendszer a [Szolgáltatási feltételekben](https://www.confluent.io/confluent-cloud-tos)megadott időszakban tartja az adatait.

A rendszer a fürt törlésének időpontjáig arányosan értékeli a használatot. Miután a fürt véglegesen törölve lett, a-e-mailben megerősítő üzenetet küld.

## <a name="get-support"></a>Támogatás kérése

Ha támogatási kérést szeretne beküldeni, lépjen kapcsolatba a megfelelő [támogatással](https://support.confluent.io) , vagy küldjön el egy kérést a portálon az alább látható módon.

> [!NOTE]
> A felhasználók első alkalommal alaphelyzetbe állíthatják a jelszót, mielőtt bejelentkeznek a Fluent támogatási portálra. Ha nincs olyan fiókja, amely az Fluent felhővel rendelkezik, küldjön további segítséget e-mailben `cloud-support@confluent.io` .

Az alábbi lépéseket követve küldhet be kérelmet az erőforrásból:

1. A Azure Portal válassza ki a Fluent szervezetet.
1. Válassza az **új támogatási kérés** lehetőséget a képernyő bal oldalán található menüből.
1. Ha támogatási kérést szeretne létrehozni, válassza ki az **Fluent-portálra** mutató hivatkozást.

    :::image type="content" source="media/support-request.png" alt-text="Hozzon létre egy támogatási kérést.":::

## <a name="next-steps"></a>További lépések

A hibaelhárítással kapcsolatos segítségért tekintse meg a következő témakört: [a felhőalapú megoldások Apache Kafka hibaelhárítása](troubleshoot.md).
