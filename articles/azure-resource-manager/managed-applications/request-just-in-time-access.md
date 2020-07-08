---
title: Igény szerinti hozzáférés igénylése
description: Ismerteti, hogyan Azure Managed Applications a kiadók a felügyelt alkalmazásokhoz való igény szerinti hozzáférést.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 7f475774828bcaecd471e13de994b156041323ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75651383"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Igény szerinti hozzáférés engedélyezése és kérése Azure Managed Applications

Előfordulhat, hogy a felügyelt alkalmazás felhasználói vonakodnak állandó hozzáférést biztosítani a felügyelt erőforráscsoporthoz. Egy Manager-alkalmazás közzétevője számára érdemes lehet, hogy a fogyasztók pontosan tudják, mikor kell hozzáférni a felügyelt erőforrásokhoz. Annak érdekében, hogy a felhasználók nagyobb mértékben szabályozzák a felügyelt erőforrásokhoz való hozzáférést, Azure Managed Applications biztosít egy igény szerinti (JIT) hozzáférés szolgáltatást, amely jelenleg előzetes verzióban érhető el.

A JIT-hozzáférés lehetővé teszi, hogy a hibaelhárítás és a karbantartás érdekében emelt szintű hozzáférést kérjen egy felügyelt alkalmazás erőforrásaihoz. Mindig csak olvasási hozzáférése van az erőforrásokhoz, de egy adott időszakra vonatkozóan nagyobb hozzáférés érhető el.

A hozzáférés biztosításának munkafolyamata a következő:

1. Felvesz egy felügyelt alkalmazást a piactérre, és megadhatja, hogy az JIT-hozzáférés elérhető legyen.

1. Az üzembe helyezés során a fogyasztó a felügyelt alkalmazás adott példányához engedélyezi a JIT-hozzáférést.

1. A telepítés után a felhasználó módosíthatja a JIT-hozzáférés beállításait.

1. A felügyelt erőforrások hibakereséséhez vagy frissítéséhez el kell küldenie a hozzáférési kérést.

1. A fogyasztó jóváhagyja a kérést.

Ez a cikk a-közzétevők által a JIT-hozzáférés engedélyezéséhez és a kérelmek beküldéséhez szükséges műveleteket ismerteti. A JIT hozzáférési kérelmek jóváhagyásával kapcsolatos további tudnivalókért lásd: az igény szerinti [hozzáférés jóváhagyása Azure Managed Applicationsban](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>JIT hozzáférési lépés hozzáadása a felhasználói felülethez

A CreateUiDefinition.jsfájl pontosan az állandó hozzáféréshez létrehozott felhasználói felületi fájlhoz hasonlít, kivéve, ha olyan lépést kell tartalmaznia, amely lehetővé teszi a felhasználók számára a JIT-hozzáférés engedélyezését. Ha többet szeretne megtudni az első felügyelt alkalmazás Azure Marketplace-en való közzétételéről, tekintse meg [a Azure Managed Applications a piactéren](publish-marketplace-app.md).

Az ajánlathoz tartozó JIT-képesség támogatásához adja hozzá a következő tartalmat a CreateUiDefinition.jsfájlhoz:

A "Steps":

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
 
A "kimenetek" elemnél:

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> A JIT-hozzáférés előzetes verzióban érhető el. A JIT-konfiguráció sémája a jövőbeli iterációkban változhat.

## <a name="enable-jit-access"></a>JIT-hozzáférés engedélyezése

Az ajánlat a piactéren való definiálásakor győződjön meg arról, hogy engedélyezte a JIT-hozzáférést.

1. Jelentkezzen be a [Cloud Partner közzétételi portálján](https://cloudpartner.azure.com).

1. Adja meg azokat az értékeket, amelyek alapján közzé szeretné tenni a felügyelt alkalmazást a piactéren. Válassza az **Igen** lehetőséget a **JIT-hozzáférés engedélyezéséhez?**

   ![Igény szerinti hozzáférés engedélyezése](./media/request-just-in-time-access/marketplace-enable.png)

Egy JIT-konfigurációs lépést adott hozzá a felhasználói felületéhez, és engedélyezte a JIT-hozzáférést a Piactéri ajánlatban. Ha a felhasználók telepítik a felügyelt alkalmazást, [bekapcsolhatják az JIT-hozzáférést a példányhoz](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Hozzáférés kérése

Ha hozzá kell férnie a fogyasztó felügyelt erőforrásaihoz, egy adott szerepkörre, időre és időtartamra vonatkozó kérelmet kell küldenie. A felhasználónak jóvá kell hagynia a kérelmet.

JIT hozzáférési kérelem küldése:

1. Válassza ki a hozzáféréshez szükséges felügyelt alkalmazás **JIT-hozzáférését** .

1. Válassza a **jogosult szerepkörök**lehetőséget, majd válassza a művelet oszlopban az **aktiválás** elemet a kívánt szerepkörhöz.

   ![Hozzáférési kérelem aktiválása](./media/request-just-in-time-access/send-request.png)

1. A szerepkör **aktiválása** űrlapon válasszon ki egy kezdési időpontot és időtartamot, amelynek aktív a szerepköre. A kérelem elküldéséhez kattintson az **aktiválás** gombra.

   ![Hozzáférés aktiválása](./media/request-just-in-time-access/activate-access.png) 

1. Tekintse meg az értesítéseket, és ellenőrizze, hogy sikeresen elküldte-e az új JIT-kérést a fogyasztónak.

   ![Értesítés](./media/request-just-in-time-access/in-progress.png)

   Most meg kell várnia, hogy a fogyasztó [jóváhagyja a kérést](approve-just-in-time-access.md#approve-requests).

1. Egy felügyelt alkalmazáshoz tartozó összes JIT-kérelem állapotának megtekintéséhez válassza az **JIT-hozzáférés** és a **kérelmek előzményei**lehetőséget.

   ![Állapot megtekintése](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Ismert problémák

A JIT-hozzáférést kérő fiók résztvevő-AZONOSÍTÓjának explicit módon szerepelnie kell a felügyelt alkalmazás definíciójában. A fiók nem szerepelhet csak a csomagban megadott csoporton keresztül. Ezt a korlátozást egy későbbi kiadásban rögzíti a rendszer.

## <a name="next-steps"></a>További lépések

A JIT-hozzáférésre vonatkozó kérések jóváhagyásáról további információt az igény szerinti [hozzáférés jóváhagyása Azure Managed Applications-ben](approve-just-in-time-access.md)című témakörben talál.
