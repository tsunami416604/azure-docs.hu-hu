---
title: Integrációs szolgáltatási környezetek kezelése az Azure Logic Apps alkalmazásban
description: Ellenőrizze a hálózati állapotot, és kezelje a logikai alkalmazásokat, kapcsolatokat, egyéni összekötőket és integrációs fiókokat az Azure Logic Apps integrációs szolgáltatási környezetében (ISE).
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: f48106be67763c093a183be01098cab74391752e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284199"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Az integrációs szolgáltatási környezet (ISE) kezelése az Azure Logic Apps alkalmazásban

Ez a cikk bemutatja, hogyan hajthatja végre a felügyeleti feladatokat az [integrációs szolgáltatási környezetben (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)például:

* Kezelje az erőforrásokat, például a logikai alkalmazásokat, a kapcsolatokat, az integrációs fiókokat és az ISE-ben lévő összekötőket.
* Ellenőrizze az ISE hálózati állapotát.
* Adjon hozzá kapacitást, indítsa újra az ISE-t, vagy törölje az ISE-t, kövesse a jelen témakörlépéseit. Ha ezeket az összetevőket hozzá szeretné adni az ISE-hez, olvassa el az [Összetevők hozzáadása az integrációs szolgáltatáskörnyezethez című témakört.](../logic-apps/add-artifacts-integration-service-environment-ise.md)

## <a name="view-your-ise"></a>Az ISE megtekintése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. A portál keresőmezőjébe írja be az "integrációs szolgáltatáskörnyezetek" kifejezést, majd válassza **az Integrációs szolgáltatáskörnyezetek**lehetőséget.

   ![Integrációs szolgáltatási környezetek keresése](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. Az eredmények listájából válassza ki az integrációs szolgáltatási környezetet.

   ![Integrációs szolgáltatáskörnyezet kiválasztása](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Folytassa a következő szakaszokat logikai alkalmazások, kapcsolatok, összekötők vagy integrációs fiókok megkereséséhez az ISE-ben.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>A hálózat állapotának ellenőrzése

Az ISE menü **Beállítások**területén válassza a **Hálózat állapota**lehetőséget. Ez az ablaktábla az alhálózatok állapotát és az egyéb szolgáltatásoktól való kimenő függőségeket jeleníti meg.

![A hálózat állapotának ellenőrzése](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>A logikai alkalmazások kezelése

Megtekintheti és kezelheti az ISE-ben található logikai alkalmazásokat.

1. Az ISE menü **Beállítások**területén válassza a **Logikai alkalmazások**lehetőséget.

   ![Logikai alkalmazások megtekintése](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Ha el szeretné távolítani azokat a logikai alkalmazásokat, amelyekre már nincs szüksége az ISE-ben, jelölje ki ezeket a logikai alkalmazásokat, majd válassza a **Törlés**lehetőséget. A törölni kívánt adatok megerősítéséhez válassza az **Igen**lehetőséget.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>API-kapcsolatok kezelése

Megtekintheti és kezelheti az ISE-ben futó logikai alkalmazások által létrehozott kapcsolatokat.

1. Az ISE menü **Beállítások**területén válassza az **API-kapcsolatok**lehetőséget.

   ![API-kapcsolatok megtekintése](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Ha el szeretné távolítani azokat a kapcsolatokat, amelyekre már nincs szüksége az ISE-ben, jelölje ki ezeket a kapcsolatokat, majd kattintson a **Törlés gombra.** A törölni kívánt adatok megerősítéséhez válassza az **Igen**lehetőséget.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>ISE-összekötők kezelése

Megtekintheti és kezelheti az ISE-ben üzembe helyezett API-összekötőket.

1. Az ISE menü **Beállítások**területén válassza a **Felügyelt összekötők**lehetőséget.

   ![Felügyelt összekötők megtekintése](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Ha el szeretné távolítani azokat az összekötőket, amelyeket nem szeretne elérhetővé tenni az ISE-ben, jelölje ki ezeket az összekötőket, majd kattintson a **Törlés gombra.** A törölni kívánt adatok megerősítéséhez válassza az **Igen**lehetőséget.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Egyéni összekötők kezelése

Megtekintheti és kezelheti az ISE-ben üzembe helyezett egyéni összekötőket.

1. Az ISE menü **Beállítások**területén válassza az **Egyéni összekötők**lehetőséget.

   ![Egyéni összekötők keresése](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Ha olyan egyéni összekötőket szeretne eltávolítani, amelyekre már nincs szüksége az ISE-ben, jelölje ki ezeket az összekötőket, majd kattintson a **Törlés gombra.** A törölni kívánt adatok megerősítéséhez válassza az **Igen**lehetőséget.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Integrációs fiókok kezelése

1. Az ISE menü **Beállítások**területén válassza **az Integrációs fiókok lehetőséget.**

   ![Integrációs fiókok keresése](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Ha már nem szükséges eltávolítani az integrációs fiókokat az ISE-ből, jelölje ki ezeket az integrációs fiókokat, majd kattintson a **Törlés gombra.**

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>ISE-kapacitás hozzáadása

A prémium SZINTŰ ISE alapegység rögzített kapacitással rendelkezik, így ha nagyobb átviteli kapacitásra van szüksége, további méretezési egységeket adhat hozzá a létrehozás során vagy azt követően. A fejlesztői termékváltozat nem tartalmazza a méretezési egységek hozzáadásának képességét.

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az ISE-t.

1. Az ISE használati és teljesítménymutatóinak áttekintéséhez válassza az ISE menü **Áttekintés parancsát.**

   ![Az ISE használatának megtekintése](./media/ise-manage-integration-service-environment/integration-service-environment-usage.png)

1. A **Beállítások csoportban**válassza a **Kiskálázás**lehetőséget. A **Konfigurálás** ablaktáblán válasszon az alábbi lehetőségek közül:

   * [**Kézi lépték:**](#manual-scale)A használni kívánt feldolgozási egységek száma alapján méretezhető.
   * [**Egyéni automatikus skálázás:**](#custom-autoscale)Teljesítménymutatók alapján skálázás különböző feltételek ből való kiválasztással és a feltételek teljesítéséhez vonatkozó küszöbértékek megadásával.

   ![Válassza ki a kívánt méretezési típust](./media/ise-manage-integration-service-environment/select-scale-out-options.png)

<a name="manual-scale"></a>

### <a name="manual-scale"></a>Kézi skála

1. Miután a **Kézi méretezés**lehetőséget, a **További kapacitás**lehetőséget választja, válassza ki a használni kívánt méretezési egységek számát.

   ![Válassza ki a kívánt méretezési típust](./media/ise-manage-integration-service-environment/select-manual-scale-out-units.png)

1. Amikor elkészült, válassza a **Mentés** lehetőséget.

<a name="custom-autoscale"></a>

### <a name="custom-autoscale"></a>Egyéni automatikus skálázás

1. Miután kiválasztotta **az Egyéni automatikus skálázás**, **az automatikus skálázási beállítás neve,** adja meg a beállítást, és szükség esetén válassza ki az Azure erőforráscsoport, ahová a beállítás tartozik.

   ![Adja meg az automatikus skálázási beállítás nevét, és válassza ki az erőforráscsoportot](./media/ise-manage-integration-service-environment/select-custom-autoscale.png)

1. Az **Alapértelmezett** feltétel nél válassza a **Méretezés egy metrika alapján** vagy a Méretezés egy adott **példányszámra**lehetőséget.

   * Ha példányalapú értéket választ, adja meg a feldolgozási egységek számát, amely 0 és 10 között van.

   * Ha a metrikus alapú tanoncok közül választ, kövesse az alábbi lépéseket:

     1. A **Szabályok csoportban** válassza **a Szabály hozzáadása**lehetőséget.

     1. A **Méretezési szabály** ablaktáblán állítsa be a feltételeket és a végrehajtandó műveletet, amikor a szabály aktiválódik.

     1. **A példánykorlátok esetében**adja meg a következő értékeket:

        * **Minimum**: A használandó feldolgozóegységek minimális száma
        * **Maximum**: A felhasználandó feldolgozóegységek maximális száma
        * **Alapértelmezett**: Ha bármilyen probléma merül fel az erőforrás-metrikák olvasása közben, és az aktuális kapacitás az alapértelmezett kapacitás alatt van, az automatikus skálázás a feldolgozási egységek alapértelmezett számára kerül át. Ha azonban az aktuális kapacitás meghaladja az alapértelmezett kapacitást, az automatikus skálázás nem méretezhető be.

1. Másik feltétel hozzáadásához válassza **a Léptékfeltétel hozzáadása lehetőséget.**

1. Ha végzett az automatikus skálázási beállításokkal, mentse a módosításokat.

<a name="restart-ISE"></a>

## <a name="restart-ise"></a>Az ISE újraindítása

Ha módosítja a DNS-kiszolgáló vagy a DNS-kiszolgáló beállításait, újra kell indítania az ISE-t, hogy az ISE feltudja venni ezeket a módosításokat. A prémium szintű termékváltozat ise újraindítása nem eredményez leállást a redundancia és az újrahasznosítás során újrainduló összetevők miatt. Azonban a fejlesztői termékváltozat ISE tapasztalatok állásidő, mert nincs redundancia létezik. További információ: [ISE SKA](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az ISE-t.

1. Az ISE menüben válassza az **Áttekintés parancsot.** Az Áttekintés eszköztár **Újraindítás gombján kattintson az Újraindítás gombra.**

   ![Integrációs szolgáltatáskörnyezet újraindítása](./media/connect-virtual-network-vnet-isolated-environment/restart-integration-service-environment.png)

<a name="delete-ise"></a>

## <a name="delete-ise"></a>ISE törlése

Mielőtt törölne egy ISE-t, amelyre már nincs szüksége, vagy egy ISE-t tartalmazó Azure-erőforráscsoportból, ellenőrizze, hogy nincs-e szabályzata vagy zárolása az Azure erőforráscsoporton, amely ezeket az erőforrásokat tartalmazza, vagy az Azure virtuális hálózatán, mert ezek az elemek blokkolhatják a törlést.

Az ISE törlése után előfordulhat, hogy akár 9 órát kell várnia, mielőtt megpróbálja törölni az Azure virtuális hálózatát vagy alhálózatait.

## <a name="next-steps"></a>További lépések

* [Erőforrások hozzáadása integrációs szolgáltatási környezetekhez](../logic-apps/add-artifacts-integration-service-environment-ise.md)