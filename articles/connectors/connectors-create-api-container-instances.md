---
title: Üzembe helyezése & az Azure Container-példányok kezelése
description: Az Azure Logic Apps használatával automatizálhatja azokat a feladatokat és munkafolyamatokat, amelyek tárolók at hoznak létre és kezelnek az Azure Container-példányokban
services: logic-apps, container-instances
ms.service: logic-apps
ms.suite: integration
author: dlepow
ms.author: danlep
ms.manager: gwallace
ms.reviewer: estfan, macolso
ms.topic: article
tags: connectors
ms.date: 01/14/2020
ms.openlocfilehash: ecb1049d64197f2a60438df7eedfb244907f7327
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046291"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>Azure Container-példányok üzembe helyezése és kezelése az Azure Logic Apps használatával

Az Azure Logic Apps és az Azure Container Instance-összekötő segítségével automatizált feladatokat és munkafolyamatokat állíthat be, amelyek [tárolócsoportokat](../container-instances/container-instances-container-groups.md)telepítenek és kezelnek. A Container Instance összekötő a következő műveleteket támogatja:

* Tárolócsoport létrehozása vagy törlése
* Tárolócsoport tulajdonságainak beszereznie
* Tárolócsoportok listájának beszereznie
* Tárolópéldány naplóinak beszereznie

Ezeket a műveleteket a logikai alkalmazások feladatok, például egy tároló számítási feladatok futtatása a Logic Apps eseményindító válaszul. Más műveletek is használhatják a Container Instance-műveletek kimenetét. 

Ez az összekötő csak műveleteket biztosít, így a logikai alkalmazás elindításához használjon egy külön eseményindítót, például egy **ismétlődési eseményindítót** egy tároló számítási feladat ának normál ütemezés szerint történő futtatásához. Előfordulhat az is, hogy egy tárolócsoport központi telepítését egy esemény, például egy Outlook-e-mail érkezése után kell elindítania. 

Ha most kezdi meg a logikai alkalmazások, tekintse át [az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Alapvető ismeretek [a logikai alkalmazások létrehozásáról,](../logic-apps/quickstart-create-first-logic-app-workflow.md) valamint [a tárolópéldányok létrehozásáról és kezeléséről](../container-instances/container-instances-quickstart.md)

* A logikai alkalmazás, ahol szeretné elérni a tárolópéldányok. Egy művelet használatához indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **Ismétlődés** eseményindítóval.

## <a name="add-a-container-instance-action"></a>Tárolópéldány hozzáadása művelet

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Válasszon elérési utat: 

   * Az utolsó lépésben, ahol műveletet szeretne hozzáadni, válassza az **Új lépés lehetőséget.** 

     – vagy –

   * A lépések között, ahol műveletet szeretne hozzáadni, vigye az egérmutatót a lépések közötti nyíl fölé. 
   Válassza ki a**+** megjelenő pluszjelet ( ), majd válassza **a Művelet hozzáadása**lehetőséget.

1. A keresőmezőbe írja be szűrőként a "tárolópéldány" kifejezést. A műveletek listájában válassza ki a kívánt Azure Container Instance-összekötő műveletet.

1. Adja meg a kapcsolat nevét. 

1. Adja meg a kiválasztott művelethez szükséges részleteket, és folytassa a logikai alkalmazás munkafolyamatának kiépítését.

  Válassza például a **Tárolócsoport létrehozása lehetőséget,** és adja meg egy tárolócsoport és egy vagy több tárolópéldány tulajdonságait a csoportban, ahogy az az alábbi képen látható (részleges részletesség):

  ![Tárolócsoport létrehozása](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő OpenAPI (korábbi Swagger) leírása által leírt eseményindítók, műveletek és korlátok technikai részleteiért tekintse át az összekötő [referencialapját](/connectors/aci/) vagy [a YAML-tárolócsoport hivatkozását.](../container-instances/container-instances-reference-yaml.md)

## <a name="next-steps"></a>További lépések

* Tekintse meg a [minta logikai alkalmazás,](https://github.com/Azure-Samples/aci-logicapps-integration) amely egy tárolót futtat az Azure Container Instances az e-mail vagy a Twitter szöveg hangulatának elemzéséhez

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)