---
title: Szabályzat tervezése kód-munkafolyamatként
description: Megtudhatja, hogyan tervezhet munkafolyamatokat a Azure Policy-definíciók kódként való üzembe helyezéséhez és az erőforrások automatikus ellenőrzéséhez.
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: e3fcb9996266af7e952538c7c92c665929bb9492
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518696"
---
# <a name="design-policy-as-code-workflows"></a>Szabályzat tervezése kód-munkafolyamatként

Ahogy halad a felhő irányításával, érdemes áttérnie az egyes szabályzat-definíciók manuális felügyeletére a Azure Portal vagy a különböző SDK-k segítségével, hogy a vállalatnál könnyebben kezelhető és ismételhető legyen. A felhőben a rendszerek nagy léptékű kezelésének egyik meghatározó megközelítése a következő:

- Infrastruktúra mint kód: a környezeteket definiáló tartalom kezelésének gyakorlata, a Resource Manager-sablonoktól kezdve minden, ami forráskódként Azure Policy definíciókat az Azure-tervezetekhez.
- DevOps: a felhasználók, a folyamatok és a termékek Uniója, amelyek lehetővé teszik az értékek folyamatos teljesítését a végfelhasználók számára.

A szabályzat mint kód az ötletek kombinációja. Lényegében megtarthatja a házirend-definíciókat a verziókövetés és a változtatások megváltozása után, tesztelheti és érvényesítheti a változást. Ez azonban nem lehet olyan mértékben, hogy a szabályzatok a kód-vagy DevOps is betartsák az infrastruktúrát.

Az érvényesítési lépésnek más folyamatos integrációs vagy folyamatos üzembe helyezési munkafolyamatok összetevőjének is kell lennie. Ilyenek például az alkalmazás-környezet vagy a virtuális infrastruktúra üzembe helyezése. A létrehozási és a telepítési folyamat korai összetevőjének Azure Policy érvényesítésével az alkalmazás-és műveleti csapatok felderítik, hogy a módosítások nem jelentenek-e panaszt, jóval azelőtt, hogy túl későn történnek, és éles környezetben próbálnak üzembe helyezni.

## <a name="workflow-overview"></a>A munkafolyamat áttekintése

A szabályzatok javasolt általános munkafolyamata a következő ábrának megfelelően néz ki:

![Szabályzat mint kód munkafolyamat áttekintése](../media/policy-as-code/policy-as-code-workflow.png)

### <a name="create-and-update-policy-definitions"></a>Szabályzat-definíciók létrehozása és frissítése

A szabályzat-definíciók JSON használatával jönnek létre, és a forrás vezérlőelemben tárolódnak. Minden szabályzat saját fájlokat tartalmaz, például a paramétereket, a szabályokat és a környezeti paramétereket, amelyeket ugyanabban a mappában kell tárolni. A következő struktúra ajánlott módszer a házirend-definíciók megtartására a verziókövetés során.

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

Új szabályzat hozzáadásakor vagy egy meglévő frissítésekor a munkafolyamatnak automatikusan frissítenie kell a házirend-definíciót az Azure-ban. Az új vagy frissített szabályzat-definíció tesztelése egy későbbi lépésben történik.

### <a name="create-and-update-initiative-definitions"></a>Kezdeményezési definíciók létrehozása és frissítése

Hasonlóképpen, a kezdeményezéseknek saját JSON-fájljuk és kapcsolódó fájljai is vannak, amelyeket ugyanabban a mappában kell tárolni. A kezdeményezési definícióhoz a házirend-definíciónak már léteznie kell, ezért nem hozható létre vagy nem frissíthető, amíg a szabályzat forrása nem frissült a forrás vezérlőelemben, majd az Azure-ban frissül. A következő struktúra ajánlott módszer a kezdeményezési definíciók megtartására a forrás-vezérlőelemben:

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

A házirend-definíciók, például a meglévő kezdeményezések hozzáadásakor vagy frissítésekor a munkafolyamatnak automatikusan frissítenie kell a kezdeményezési definíciót az Azure-ban. Az új vagy frissített kezdeményezési definíció tesztelése egy későbbi lépésben következik be.

### <a name="test-and-validate-the-updated-definition"></a>A frissített definíció tesztelése és érvényesítése

Miután az Automation megtette az újonnan létrehozott vagy frissített szabályzatot vagy kezdeményezési definíciókat, és elvégezte az Azure-beli objektum frissítését, itt az ideje, hogy tesztelje az elvégzett módosításokat. A szabályzatot vagy a részét képező kezdeményezés (eke) t az éles környezetből legtávolabbi környezetben lévő erőforrásokhoz kell rendelni. Ez a környezet általában _dev_.

A hozzárendelésnek _letiltott_ [enforcementMode](./assignment-structure.md#enforcement-mode) kell használnia, hogy az erőforrások létrehozása és frissítései ne legyenek letiltva, de a meglévő erőforrásokat a rendszer továbbra is naplózza a frissített szabályzat-definíciónak megfelelően. Még a enforcementMode esetében is ajánlott, hogy a hozzárendelési hatókör vagy egy erőforráscsoport, vagy egy olyan előfizetés, amely kifejezetten a házirendek érvényesítésére szolgál.

> [!NOTE]
> Míg a kényszerítési mód hasznos, nem helyettesíti a házirend-definíciók alapos tesztelését különféle feltételek mellett. A házirend-definíciót `PUT` és `PATCH` REST API hívásokkal, a megfelelő és nem megfelelő erőforrásokkal, valamint olyan peremhálózati ügyekkel kell tesztelni, mint az erőforrásból hiányzó tulajdonságok.

A hozzárendelés üzembe helyezését követően a házirend-SDK használatával [lekérheti az új hozzárendelés megfelelőségi adatait](../how-to/get-compliance-data.md) . A házirendek és hozzárendelések teszteléséhez használt környezetnek megfelelő és nem megfelelő erőforrásokkal kell rendelkeznie. A kód helyes egységének teszteléséhez hasonlóan szeretné tesztelni, hogy az erőforrások a várt módon működnek-e, és hogy nem rendelkezik-e hamis pozitív vagy hamis negatív értékekkel. Ha csak a vártnál teszteli és érvényesíti a műveletet, előfordulhat, hogy a szabályzat váratlanul és azonosítatlan hatással van. További információkért lásd: [új Azure-házirend hatásának kiértékelése](./evaluate-impact.md).

### <a name="enable-remediation-tasks"></a>Szervizelési feladatok engedélyezése

Ha a hozzárendelés ellenőrzése megfelel az elvárásoknak, a következő lépés a szervizelés ellenőrzése.
A [deployIfNotExists](./effects.md#deployifnotexists) vagy [módosítást](./effects.md#modify) használó házirendek szervizelési feladatba kapcsolhatók, és nem megfelelő állapotból is kiállíthatók az erőforrások.

Ennek első lépéseként adja meg a szabályzat-definícióban definiált szerepkör-hozzárendelést. Ez a szerepkör-hozzárendelés biztosítja a házirend-hozzárendelési felügyelt identitásnak megfelelő jogosultságokat ahhoz, hogy a szükséges módosításokat elvégezze az erőforrásnak.

Ha a szabályzat-hozzárendelés megfelelő jogosultságokkal rendelkezik, a házirend-SDK használatával aktiválhat egy szervizelési feladatot egy olyan erőforrás-készleten, amelyről ismert, hogy nem megfelelő. A továbblépés előtt három tesztet kell végrehajtani ezekkel a Szervizelési feladatokkal:

- Annak ellenőrzése, hogy a Szervizelési feladat sikeresen befejeződött-e
- A szabályzat kiértékelésének futtatásával láthatja, hogy a szabályzatok megfelelőségi eredményei a várt módon frissülnek.
- Környezeti egység tesztelésének futtatása közvetlenül az erőforrásokon a tulajdonságok megváltozásának ellenőrzéséhez

A frissített szabályzat kiértékelési eredményeinek és a környezetnek a tesztelése közvetlenül megerősíti, hogy a Szervizelési feladatok megváltoztatták a várt eredményt, és hogy a házirend-definíció a várt módon módosította a megfelelőségi változást.

### <a name="update-to-enforced-assignments"></a>A kényszerített hozzárendelések frissítése

Az összes ellenőrzési kapu befejezése után frissítse a hozzárendelést az engedélyezett **enforcementMode** használatára. Ezt a változást kezdetben ugyanabban a környezetben kell elvégezni, mint a termeléstől. Miután a környezet a várt módon működik, a módosítást úgy kell kialakítani, hogy az a következő környezetbe kerüljön, és így tovább, amíg a házirend üzembe nem kerül a termelési erőforrásokra.

## <a name="process-integrated-evaluations"></a>Integrált értékelések feldolgozása

A szabályzatok általános munkafolyamata olyan házirendek és kezdeményezések fejlesztésére és üzembe helyezésére szolgál, amelyek méretezhetők a környezetben. A szabályzat kiértékelése azonban a telepítési folyamat részeként az Azure-ban üzembe helyezett vagy az erőforrásokat létrehozó munkafolyamatok részét képezi, például alkalmazások üzembe helyezése vagy Resource Manager-sablonok futtatása infrastruktúra létrehozásához.

Ezekben az esetekben az alkalmazás vagy az infrastruktúra üzembe helyezését tesztelési előfizetésre vagy erőforráscsoporthoz kell elvégezni, ezért az adott hatókörre vonatkozó házirend-kiértékelést kell végrehajtani az összes meglévő házirend és kezdeményezés ellenőrzésének ellenőrzéséhez. Habár előfordulhat, hogy a **enforcementMode** _le vannak tiltva_ az ilyen környezetben, érdemes korán tudni, ha egy alkalmazás vagy infrastruktúra üzembe helyezése már nem sérti a szabályzat-definíciókat. Ennek a szabályzatnak a kiértékelése ezért a munkafolyamatok egyik lépése, valamint a nem megfelelő erőforrások létrehozására szolgáló sikertelen telepítések.

## <a name="review"></a>Áttekintés

Ez a cikk ismerteti a szabályzatok általános munkafolyamatát, valamint azt is, hogy a szabályzatok kiértékelése más üzembe helyezési munkafolyamatok része legyen. Ezt a munkafolyamatot bármely olyan környezetben felhasználhatja, amely támogatja a parancsfájlokon alapuló lépéseket és az automatizálást az eseményindítók alapján.

## <a name="next-steps"></a>További lépések

- A szabályzat- [definíciós struktúra](./definition-structure.md)megismerése.
- A szabályzat- [hozzárendelési struktúra](./assignment-structure.md)megismerése.
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/getting-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).