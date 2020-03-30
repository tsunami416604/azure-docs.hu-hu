---
title: Igény szerinti hozzáférés igénylése
description: Ez a témakör azt ismerteti, hogy az Azure felügyelt alkalmazások közzétevői hogyan kérnek just-in-time hozzáférést egy felügyelt alkalmazáshoz.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 7f475774828bcaecd471e13de994b156041323ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651383"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Az Azure felügyelt alkalmazásokhoz való azonnali hozzáférés engedélyezése és kérése

A felügyelt alkalmazás fogyasztói vonakodhatnak attól, hogy állandó hozzáférést biztosítsanak a felügyelt erőforráscsoporthoz. Egy kezelőalkalmazás közzétevőjeként célszerű lehet, hogy a fogyasztók pontosan tudják, mikor kell elérnie a felügyelt erőforrásokat. Annak érdekében, hogy a fogyasztók nagyobb ellenőrzést biztosítanak a felügyelt erőforrásokhoz való hozzáférés biztosítása felett, az Azure Managed Applications egy just-in-time (JIT) access nevű szolgáltatást biztosít, amely jelenleg előzetes verzióban érhető el.

JIT-hozzáférés lehetővé teszi, hogy kérjen emelt szintű hozzáférést a felügyelt alkalmazások erőforrásait hibaelhárítás vagy karbantartás. Mindig csak olvasható hozzáféréssel rendelkezik az erőforrásokhoz, de egy adott időszakban nagyobb hozzáféréssel rendelkezhet.

A hozzáférés biztosításához szükséges munkafolyamat a következő:

1. Hozzáad egy felügyelt alkalmazást a piactérhez, és megadhatja, hogy a JIT-hozzáférés elérhető legyen.

1. A központi telepítés során az fogyasztó lehetővé teszi a jit-hozzáférést a felügyelt alkalmazás adott példányához.

1. A telepítés után az fogyasztó módosíthatja a jit-hozzáférés beállításait.

1. A felügyelt erőforrások hibaelhárításához vagy frissítéséhez hozzáférési kérelmet kell küldenie.

1. Az fogyasztó jóváhagyja a kérést.

Ez a cikk a közzétevők által a jit-hozzáférés engedélyezéséhez és a kérelmek elküldése érdekében végrehajtott műveletekre összpontosít. A jit-hozzáférési kérelmek jóváhagyásáról a [Just-in-time access jóváhagyása az Azure felügyelt alkalmazásokban(Jóváhagyása) témakörben](approve-just-in-time-access.md)olvashat.

## <a name="add-jit-access-step-to-ui"></a>JIT-hozzáférési lépés hozzáadása a felhasználói felülethez

A CreateUiDefinition.json fájl pontosan olyan, mint az állandó hozzáféréshez létrehozott felhasználói felületfájl, kivéve, hogy tartalmaznia kell egy olyan lépést, amely lehetővé teszi a fogyasztók számára a jit-hozzáférést. Ha többet szeretne megtudni az első felügyelt alkalmazásajánlat azure piactéren való közzétételéről, olvassa el az Azure felügyelt alkalmazások a piactéren című [témakört.](publish-marketplace-app.md)

Az ajánlat JIT-képességének támogatásához adja hozzá a következő tartalmat a CreateUiDefinition.json fájlhoz:

A "lépések":

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
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> A jit-hozzáférés előzetes verzióban érhető el. A jit-konfiguráció sémája a jövőbeli ismétlésekben változhat.

## <a name="enable-jit-access"></a>JIT-hozzáférés engedélyezése

Amikor meghatározza az ajánlat a piacon, győződjön meg róla, hogy a JIT-hozzáférés engedélyezése.

1. Jelentkezzen be a [Cloud Partner közzétételi portáljára.](https://cloudpartner.azure.com)

1. Adja meg a felügyelt alkalmazás közzétételéhez a piactéren. Válassza az **Igen** lehetőséget a **JIT-hozzáférés engedélyezése mezőben?**

   ![Just-in-time hozzáférés engedélyezése](./media/request-just-in-time-access/marketplace-enable.png)

JIT-konfigurációs lépést adott a felhasználói felülethez, és engedélyezte a jit-hozzáférést a piactéri ajánlatban. Amikor a fogyasztók telepítik a felügyelt alkalmazást, [bekapcsolhatják a jit-hozzáférést a példányukhoz.](approve-just-in-time-access.md#enable-during-deployment)

## <a name="request-access"></a>Hozzáférés kérése

Ha hozzá kell férned az fogyasztó felügyelt erőforrásaihoz, egy adott szerepkörre, időre és időtartamra vonatkozó kérelmet kell küldenie. Az előlegnek jóvá kell hagynia a kérelmet.

JIT-hozzáférési kérelem küldése:

1. Válassza a **JIT Access lehetőséget** a felügyelt alkalmazáshoz, amelyet el kell érnie.

1. Válassza **a Jogosult szerepkörök**lehetőséget, majd a kívánt szerepkör műveletoszlopában válassza az **Aktiválás** lehetőséget.

   ![Hozzáférési kérelem aktiválása](./media/request-just-in-time-access/send-request.png)

1. A **Szerepkör aktiválása** képernyőn válassza ki a szerepkör aktív indításának idejét és időtartamát. A kérelem elküldéséhez válassza az **Aktiválás** gombot.

   ![Hozzáférés aktiválása](./media/request-just-in-time-access/activate-access.png) 

1. Tekintse meg az értesítéseket, és tekintse meg, hogy az új jit-kérelem sikeresen elküldésre került-e a fogyasztónak.

   ![Értesítés](./media/request-just-in-time-access/in-progress.png)

   Most meg kell várnia, amíg a fogyasztó [jóváhagyja a kérést.](approve-just-in-time-access.md#approve-requests)

1. A felügyelt alkalmazás összes JIT-kérelem állapotának megtekintéséhez válassza a **JIT-hozzáférés** és **a kéréselőzmények lehetőséget.**

   ![Állapot megtekintése](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Ismert problémák

A jit-hozzáférést kérő fiók elsődleges azonosítóját explicit módon be kell építeni a felügyelt alkalmazásdefinícióba. A fiók nem csak a csomagban megadott csoporton keresztül vehető fel. Ezt a korlátozást egy későbbi kiadásban rögzítik.

## <a name="next-steps"></a>További lépések

A jit-hozzáférésre vonatkozó kérelmek jóváhagyásáról a [Just-in-time access jóváhagyása az Azure felügyelt alkalmazásokban(Jóváhagyása) témakörben](approve-just-in-time-access.md)olvashat.
