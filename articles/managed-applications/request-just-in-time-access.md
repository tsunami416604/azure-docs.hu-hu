---
title: Engedélyezze és just-in-time-hozzáférés kérése az Azure által felügyelt alkalmazások
description: Ismerteti, hogyan kiadók az Azure Managed Applications leegyszerűsödött igény a felügyelt alkalmazásokhoz.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: ea933f5382cb42c01de523326b094c1813401132
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481774"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Engedélyezze és just-in-time-hozzáférés kérése az Azure által felügyelt alkalmazások

Előfordulhat, hogy a felügyelt alkalmazás felhasználóinak védhetőek való állandó hozzáférést adni a felügyelt erőforráscsoporthoz. Közzétevő-kezelő alkalmazás előfordulhat, hogy inkább, hogy a fogyasztók ismeri pontosan számíthat a felügyelt erőforrások eléréséhez. Biztosítson a fogyasztóknak nagyobb mértékben vezérelheti a felügyelt erőforrások, Azure által felügyelt alkalmazások való hozzáférést nyújt a szolgáltatás jelenleg előzetes verzióban – igény (szerinti JIT) hozzáférési.

Igény szerinti elérése lehetővé teszi egy felügyelt alkalmazás erőforrásai hibaelhárítása vagy karbantartása az emelt szintű hozzáférés kérése. Mindig az erőforrásokhoz való csak olvasási hozzáféréssel rendelkezik, de egy adott időtartamra már nagyobb elérheti.

A munkafolyamat hozzáférés biztosításához a következő:

1. Adjon hozzá egy felügyelt alkalmazást, a piactéren, és adja meg, hogy JIT hozzáférési érhető el.

1. A fogyasztó központi telepítése során lehetővé teszi a igény szerinti hozzáférést, hogy a felügyelt alkalmazás példánya esetében.

1. Az üzembe helyezést követően a fogyasztó a JIT hozzáférési beállításait módosíthatja.

1. A kezelt erőforrások frissítése vagy hibák elhárítása kell küldése az vonatkozó hozzáférési kérést.

1. A fogyasztó jóváhagyja a kérést.

Ez a cikk a kiadók igény szerinti elérésének lehetővé tétele és a kérelmek elküldése műveletek összpontosít. JIT hozzáférési kérelmek jóváhagyása kapcsolatos további információkért lásd: [just-in-time-hozzáférés az Azure felügyelt alkalmazások jóváhagyása](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Igény szerinti hozzáférés lépés hozzáadása a felhasználói felület

A CreateUiDefinition.json fájl megegyezik a a felhasználói felület fájlt hoz létre állandó hozzáférést, kivéve a lépést, amely lehetővé teszi, hogy a fogyasztók igény szerinti hozzáférés engedélyezéséhez meg kell adni. Az ajánlat az Azure Marketplace első felügyelt alkalmazás közzététele kapcsolatos további információkért lásd: [Azure felügyelt alkalmazások a Marketplace-en](publish-marketplace-app.md).

Igény szerinti képesség támogatásához az ajánlatban, adja hozzá az CreateUiDefinition.json fájlhoz az alábbi tartalommal:

A "lépések"szakaszban:

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```
 
A "kimenetek":

```json
"jitAccessPolicy": "[parse(concat('{\"jitAccessEnabled\":', string(steps('jitConfiguration').jitConfigurationControl.jitEnabled), ',\"jitApprovalMode\":\"', steps('jitConfiguration').jitConfigurationControl.jitApprovalMode, '\",\"maximumJitAccessDuration\":\"', steps('jitConfiguration').jitConfigurationControl.maxAccessDuration, '\",\"jitApprovers\":', string(steps('jitConfiguration').jitConfigurationControl.approvers), '}'))]"
```

> [!NOTE]
> Igény szerinti hozzáférést az előzetes verzióban. Igény szerinti konfigurációs sémáját a jövőbeli ismétlések sikerült módosítani.

## <a name="enable-jit-access"></a>Igény szerinti hozzáférés engedélyezése

Az ajánlatot a piactéren meghatározásakor ellenőrizze, hogy engedélyezi a JIT hozzáférési.

1. Jelentkezzen be a [Cloud Partner közzétételi portál](https://cloudpartner.azure.com).

1. Adja meg a felügyelt alkalmazás közzététele a piactéren. Válassza ki **Igen** a **igény szerinti hozzáférés engedélyezéséhez?**

   ![Igény szerinti elérésének engedélyezése](./media/request-just-in-time-access/marketplace-enable.png)

Hozzá kell adni egy igény szerinti konfigurációs lépés a felhasználói felület, és a marketplace kínálatának az igény szerinti hozzáférés engedélyezve van. Amikor a felhasználók a felügyelt alkalmazás üzembe helyezéséhez a következőkre [kapcsolja be az áttelepített példány JIT hozzáférési](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Hozzáférés kérése

A fogyasztó kezelt erőforrások eléréséhez szükséges, ha egy adott szerepkörrel, ideje és időtartama kérelmet küld. A fogyasztó, majd jóvá kell hagynia a kérelmet.

A JIT hozzáférési kérelem küldése:

1. Válassza ki **JIT hozzáférési** hozzá kell férnie a felügyelt alkalmazás.

1. Válassza ki **jogosult szerepkörök**, és válassza ki **aktiválás** művelet oszlopában a kívánt szerepkört.

   ![A hozzáférési kérés aktiválása](./media/request-just-in-time-access/send-request.png)

1. Az a **szerepkör aktiválása** alkotnak, jelölje be a kezdő és a szerepkör lesz aktív időtartam. Válassza ki **aktiválás** a kérelem elküldéséhez.

   ![Hozzáférés aktiválása](./media/request-just-in-time-access/activate-access.png) 

1. Tekintse meg az értesítések megtekintéséhez, hogy az igény szerinti új kérelem sikeresen elküldte-e a fogyasztó számára.

   ![Értesítés](./media/request-just-in-time-access/in-progress.png)

   Most meg kell várnia a fogyasztó [jóváhagyja a kérelmét](approve-just-in-time-access.md#approve-requests).

1. Az összes felügyelt alkalmazás igény szerinti kérelem állapotának megtekintéséhez válassza **JIT hozzáférési** és **kérelem előzményei**.

   ![Állapot megtekintése](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Ismert problémák

A résztvevő-azonosító az igény szerinti hozzáférést kér a fiók explicit módon kell szerepelnie a felügyelt alkalmazás definícióját. A fiók csak tartalmaz egy csoportot, amely a csomagban megadott keresztül nem lehet. Ez a korlátozás egy későbbi kiadásban lesz kijavítva.

## <a name="next-steps"></a>További lépések

JIT hozzáférési kérelem jóváhagyásáról kapcsolatos további információkért lásd: [just-in-time-hozzáférés az Azure felügyelt alkalmazások jóváhagyása](approve-just-in-time-access.md).
