---
title: Szabályzat tervezése kódmunkafolyamatokként
description: Ismerje meg, hogyan tervezhet munkafolyamatokat az Azure Policy-definíciók kódként történő üzembe helyezéséhez és az erőforrások automatikus érvényesítéséhez.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 0914ba6510c9d2ef87d3f83417f97340d42c8bce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74267267"
---
# <a name="design-policy-as-code-workflows"></a>Szabályzat tervezése kódmunkafolyamatokként

A felhőalapú cégirányítási folyamat során érdemes áttérni az Azure Portalon vagy a különböző SDK-kon keresztül az Azure Portalon lévő egyes szabályzatdefiníciók manuális kezeléséről valami kezelhetőbbre és megismételhetőre nagyvállalati szinten. A felhőben a rendszerek nagy léptékű kezelésének két meghatározó megközelítése a következő:

- Infrastruktúra kódként: A környezeteket meghatározó tartalom kezelésének gyakorlata, az Erőforrás-kezelő sablonjaitól kezdve az Azure Policy-definíciókon át az Azure Blueprints-ig, forráskódként.
- DevOps: Az emberek, a folyamatok és a termékek egyesítése, hogy lehetővé tegye az érték folyamatos szállítását a végfelhasználók számára.

A szabályzat mint kód ezeknek az ötleteknek a kombinációja. Lényegében tartsa a szabályzatdefiníciók at forrás-ellenőrzés, és amikor a változás történik, tesztelje és érvényesítse a módosítást. Azonban ez nem lehet a szabályzatok bevonása az infrastruktúra-kód vagy DevOps.

Az ellenőrzési lépésnek más folyamatos integrációs vagy folyamatos üzembe helyezési munkafolyamatok összetevőinek is kell lennie. Ilyenpéldául egy alkalmazáskörnyezet vagy virtuális infrastruktúra telepítése. Azáltal, hogy az Azure Policy érvényesítése a build- és üzembe helyezési folyamat korai összetevője, az alkalmazás és a műveleti csapatok felfedezik, hogy a módosítások nem-revonatkozóak-e, jóval azelőtt, hogy túl késő lenne, és éles környezetben próbálnak üzembe helyezni.

## <a name="workflow-overview"></a>Munkafolyamat – áttekintés

A Szabályzat kódként javasolt általános munkafolyamata a következő diagramhoz hasonlóan néz ki:

![Házirend kódként munkafolyamat – áttekintés](../media/policy-as-code/policy-as-code-workflow.png)

### <a name="create-and-update-policy-definitions"></a>Házirend-definíciók létrehozása és frissítése

A házirend-definíciók JSON használatával jönnek létre, és a forrásvezérlőben tárolódnak. Minden házirend saját fájlkészlettel rendelkezik, például a paraméterek, a szabályok és a környezeti paraméterek, amelyeket ugyanabban a mappában kell tárolni. A következő struktúra ajánlott módja annak, hogy a házirend-definíciók a forrás-ellenőrzés.

```text
.
|
|- policies/  ________________________ # Root folder for policies
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

Új szabályzat hozzáadásakor vagy egy meglévő frissítése kor a munkafolyamat automatikusan frissíti a szabályzat definícióját az Azure-ban. Az új vagy frissített házirend-definíció tesztelése egy későbbi lépésben jelenik meg.

### <a name="create-and-update-initiative-definitions"></a>Kezdeményezésdefiníciók létrehozása és frissítése

Hasonlóképpen, a kezdeményezések saját JSON fájllal és kapcsolódó fájlokkal rendelkeznek, amelyeket ugyanabban a mappában kell tárolni. A kezdeményezés definíciója megköveteli, hogy a szabályzat definíciója már létezzen, így nem hozható létre vagy frissíthető, amíg a szabályzat forrását nem frissítette a forrás-ellenőrzés, majd az Azure-ban. A következő struktúra ajánlott módon tartja a kezdeményezésdefiníciókat a forrásellenőrzésben:

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

A szabályzatdefiníciókhoz hasonlóan egy meglévő kezdeményezés hozzáadásakor vagy frissítésekor a munkafolyamatnak automatikusan frissítenie kell a kezdeményezés definícióját az Azure-ban. Az új vagy frissített kezdeményezésdefiníció tesztelése egy későbbi lépésben következik be.

### <a name="test-and-validate-the-updated-definition"></a>A frissített definíció tesztelése és ellenőrzése

Miután az automatizálás megtette az újonnan létrehozott vagy frissített szabályzat- vagy kezdeményezésdefiníciókat, és az azure-beli objektum frissítése megtörtént, itt az ideje, hogy tesztelje a végrehajtott módosításokat. Ezután a szabályzatot vagy a kezdeményezés(eke)t, amelynek a része, hozzá kell rendelni az éles környezetben lévő erőforrásokhoz. Ez a környezet általában _Dev._

A hozzárendelés kell [használnia enforcementMode](./assignment-structure.md#enforcement-mode) _le tiltott,_ hogy az erőforrások létrehozása és a frissítések nem blokkolja, de a meglévő erőforrások továbbra is auditált a frissített házirend-definíciónak való megfelelés. Még enforcementMode, javasoljuk, hogy a hozzárendelés hatóköre vagy egy erőforráscsoport vagy egy előfizetés, amely kifejezetten a szabályzatok érvényesítése.

> [!NOTE]
> Bár a kényszerítési mód hasznos, ez nem helyettesíti a házirend-definíció konkretizált, különböző körülmények között. A szabályzat definícióját `PUT` kell `PATCH` tesztelni, és a REST API-hívások, megfelelő és nem megfelelő erőforrások, és peremhálózati esetekben, például egy tulajdonság hiányzik az erőforrásból.

A hozzárendelés üzembe helyezése után a Policy SDK segítségével lekéri az új hozzárendelés [megfelelőségi adatait.](../how-to/get-compliance-data.md) A szabályzatok és hozzárendelések teszteléséhez használt környezetnek megfelelő és nem megfelelő erőforrásokkal kell rendelkeznie. Mint egy jó egység teszt a kódot, szeretné tesztelni, hogy az erőforrások a várt módon, és hogy ön is nem hamis-pozitív vagy hamis negatívok. Ha csak a várt módon teszteli és ellenőrzi, előfordulhat, hogy a házirend nem várt és azonosítatlan hatást gyakorol. További információ: [Kiértékelhető egy új Azure-szabályzat hatása.](./evaluate-impact.md)

### <a name="enable-remediation-tasks"></a>Javítási feladatok engedélyezése

Ha a hozzárendelés érvényesítése megfelel az elvárásoknak, a következő lépés a javítás érvényesítése.
A [deployIfNotExists](./effects.md#deployifnotexists) vagy [a módosító](./effects.md#modify) házirendek szervizelési feladattá alakíthatók, és nem megfelelő állapotból kijavíthatják az erőforrásokat.

Ennek első lépése a házirend-hozzárendelés nek a házirend-definícióban meghatározott szerepkör-hozzárendelés megadása. Ez a szerepkör-hozzárendelés elegendő jogosultságot biztosít a házirend-hozzárendelés felügyelt identitásának ahhoz, hogy az erőforrás megfelelővé tegye a szükséges módosításokat.

Ha a házirend-hozzárendelés megfelelő jogokkal rendelkezik, a Policy SDK segítségével indítson el egy szervizelési feladatot egy olyan erőforráskészlet ellen, amelyről ismert, hogy nem megfelelő. A folytatás előtt három vizsgálatot kell elvégezni e helyreigazított feladatok ellen:

- Annak ellenőrzése, hogy a javítási feladat sikeresen befejeződött-e
- Házirend-értékelés futtatása annak érdekében, hogy a házirend-megfelelőségi eredmények a várt módon frissüljenek
- Környezeti egységteszt futtatása az erőforrásokon közvetlenül a tulajdonságaik módosításának ellenőrzéséhez

A frissített házirend-értékelési eredmények és a környezet tesztelése közvetlenül megerősíti, hogy a javítási feladatok megváltoztatták a várt szintet, és hogy a házirend-definíció a megfelelőség imszerint változott.

### <a name="update-to-enforced-assignments"></a>Frissítés kényszerített hozzárendelésekhez

Miután az összes érvényesítési kapu befejeződött, frissítse a hozzárendelést az _engedélyezett_ **kényszerítési mód használatára.** Ezt a változtatást kezdetben ugyanabban a környezetben kell elvégre, távol a termeléstől. Ha a környezet a várt módon működik, a módosítást a következő környezetben kell alkalmazni, és így tovább, amíg a házirend éles erőforrásokra nem kerül üzembe helyezésre.

## <a name="process-integrated-evaluations"></a>Folyamatintegrált értékelések

A szabályzat kódként való általános munkafolyamata a szabályzatok és kezdeményezések nagy léptékű környezetben történő fejlesztésére és alkalmazására szolgál. A házirend-kiértékelésnek azonban a központi telepítési folyamat részét kell, hogy képeznie kell minden olyan munkafolyamat esetében, amely erőforrásokat telepít vagy hoz létre az Azure-ban, például alkalmazások at vagy Erőforrás-kezelő sablonokat futtatva infrastruktúra létrehozásához.

Ezekben az esetekben, miután az alkalmazás vagy az infrastruktúra üzembe helyezése egy teszt-előfizetés vagy erőforráscsoport, házirend-kiértékelést kell végezni, hogy a hatókör ellenőrzése ellenőrzése az összes meglévő szabályzatok és kezdeményezések. Bár lehet, hogy egy ilyen környezetben le van tiltva **enforcementMode** _letiltva,_ célszerű korán tudni, ha egy alkalmazás vagy infrastruktúra-telepítés megsérti a házirend-definíciók korai. Ez a házirend-kiértékelés ezért egy lépés a munkafolyamatokban, és sikertelen üzembe helyezések, amelyek nem megfelelő erőforrásokat hoznak létre.

## <a name="review"></a>Áttekintés

Ez a cikk ismerteti az általános munkafolyamat a szabályzat kódként, és azt is, ahol a házirend-kiértékelés más üzembe helyezési munkafolyamatok részét kell venni. Ez a munkafolyamat bármilyen környezetben használható, amely támogatja a parancsfájlalapú lépéseket és az eseményindítókon alapuló automatizálást.

## <a name="next-steps"></a>További lépések

- További információ a [házirend-definíciós struktúráról.](./definition-structure.md)
- További információ a [házirend-hozzárendelési struktúráról.](./assignment-structure.md)
- Ismerje meg, hogyan hozhat [létre programozott házirendeket.](../how-to/programmatically-create.md)
- További információ a [megfelelőségi adatok beszedéséről.](../how-to/get-compliance-data.md)
- További információ a [nem megfelelő erőforrások kiújulásáról.](../how-to/remediate-resources.md)
- Tekintse át, hogy mi a felügyeleti csoport az [Erőforrások rendszerezése az Azure felügyeleti csoportokkal.](../../management-groups/overview.md)