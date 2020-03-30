---
title: Az új Azure-szabályzat hatásának kiértékelése
description: Ismerje meg a követendő folyamatot, amikor új szabályzatdefiníciót vezet be az Azure-környezetbe.
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: 562fa2378356ddc1eac48b6ea5c160ebf655d525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74463526"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy"></a>Az új Azure-szabályzat hatásának kiértékelése

Az Azure Policy hatékony eszköz az Azure-erőforrások üzleti szabványoknak megfelelő és megfelelőségi igényeknek megfelelő kezeléséhez. Amikor emberek, folyamatok vagy folyamatok erőforrásokat hoznak létre vagy frissítnek, az Azure Policy áttekinti a kérést. Ha a házirend-definíciós hatás [hozzáfűzése](./effects.md#deny) vagy [DeployIfNotExists,](./effects.md#deployifnotexists)a házirend módosítja a kérést, vagy hozzáadja azt. Ha a házirend-definíciós hatás [Naplózás](./effects.md#audit) vagy [AuditIfNotExists,](./effects.md#auditifnotexists)a házirend tevékenységnapló-bejegyzés létrehozását eredményezi. És ha a házirend-definíciós hatás [Megtagadás](./effects.md#deny), a házirend leállítja a kérelem létrehozását vagy módosítását.

Ezek az eredmények pontosan a kívánt, ha tudja, hogy a házirend helyesen van definiálva. Azonban fontos, hogy érvényesítse az új szabályzat működik a szándéknak, mielőtt lehetővé teszi, hogy módosítsa vagy blokkolja a munkát. Az érvényesítésnek biztosítania kell, hogy csak a tervezett erőforrások legyenek megfelelőek, és az eredmények nem megfelelő erőforrásokat (más néven _hamis pozitív)_ tartalmaznak.

Az új házirend-definíció érvényesítésének ajánlott megközelítése az alábbi lépések végrehajtásával történik:

- Szigorúan határozza meg a házirendet
- A meglévő erőforrások naplózása
- Új vagy frissített erőforráskérelmek naplózása
- A házirend üzembe helyezése az erőforrásokban
- Folyamatos monitorozás

## <a name="tightly-define-your-policy"></a>Szigorúan határozza meg a házirendet

Fontos megérteni, hogy az üzleti szabályzat hogyan valósítva meg szabályzatdefinícióként, és hogyan viszonyul az Azure-erőforrások más Azure-szolgáltatásokhoz. Ez a lépés [a követelmények azonosításával](../tutorials/create-custom-policy-definition.md#identify-requirements) és [az erőforrás tulajdonságainak meghatározásával](../tutorials/create-custom-policy-definition.md#determine-resource-properties)érhető el.
De az is fontos, hogy túllásson az üzletpolitika szűk meghatározásán. A házirend például azt mondja: "Minden virtuális gépnek..."? Mi a helyzet más Azure-szolgáltatások, amelyek a virtuális gépek, például a HDInsight vagy az AKS? A házirend meghatározásakor figyelembe kell vennünk, hogy ez a házirend milyen hatással van a más szolgáltatások által használt erőforrásokra.

Ebből az okból a szabályzatdefiníciók kell a szorosan meghatározott és az erőforrásokés a tulajdonságok kikell értékelni a megfelelőségi lehetséges.

## <a name="audit-existing-resources"></a>Meglévő erőforrások naplózása

Mielőtt új vagy frissített erőforrásokat szeretne kezelni az új házirend-definícióval, a legjobb, ha azt vizsgálja, hogyan értékeli ki a meglévő erőforrások egy korlátozott részhalmazát, például egy teszterőforrás-csoportot. A házirend-hozzárendelésen a_Letiltva_ [kényszerítési mód](./assignment-structure.md#enforcement-mode)
(DoNotEnforce) használatával megakadályozhatja, hogy a [hatás](./effects.md) elindítsa vagy tevékenységnapló-bejegyzéseket hozlétre.

Ez a lépés lehetőséget ad arra, hogy kiértékelje az új szabályzat megfelelőségi eredményeit a meglévő erőforrásokra anélkül, hogy befolyásolna a munkafolyamatot. Ellenőrizze, hogy a megfelelő erőforrások nem megfelelőként vannak-e megjelölve _(hamis pozitív_), és hogy az összes erőforrás, amely várhatóan nem megfelelő, megfelelően van-e megjelölve.
Miután az erőforrások kezdeti részhalmaza a várt módon érvényesít, lassan bővítse ki a kiértékelést az összes meglévő erőforrásra.

A meglévő erőforrások ily módon történő értékelése lehetőséget nyújt a nem megfelelő erőforrások kiújítására az új házirend teljes körű végrehajtása előtt. Ez a törlés történhet manuálisan vagy [szervizelési feladaton](../how-to/remediate-resources.md) keresztül, ha a házirend-definíciós hatás _DeployIfNotExists_.

## <a name="audit-new-or-updated-resources"></a>Új vagy frissített erőforrások naplózása

Miután érvényesítette az új szabályzatdefiníció megfelelően jelentést a meglévő erőforrások, itt az ideje, hogy vizsgálja meg a hatását a szabályzat, amikor az erőforrások létrehozása vagy frissítése. Ha a házirend-definíció támogatja a hatás paraméterezését, használja [a Naplózás](./effects.md#audit)t. Ez a konfiguráció lehetővé teszi az erőforrások létrehozásának és frissítésének figyelését, hogy lássa, az új szabályzatdefiníció elindít-e egy bejegyzést az Azure-tevékenységnaplóban egy olyan erőforráshoz, amely nem megfelelő, anélkül, hogy befolyásolna a meglévő munkát vagy kéréseket.

Javasoljuk, hogy egyszerre frissítse, és hozzon létre új erőforrásokat, amelyek megfelelnek a szabályzat definíciójának, hogy lássa, hogy a _naplózási_ hatás megfelelően aktiválódik, ha a várt. Keresse meg azokat az erőforrás-kérelmeket, amelyeket nem érinthet az új házirend-definíció, amely a _naplózási_ hatást váltja ki.
Ezek az érintett erőforrások egy másik példa a _hamis pozitív,_ és rögzíteni kell a politika meghatározása, mielőtt a teljes végrehajtás.

Abban az esetben, ha a szabályzat definíciója megváltozik a tesztelés ezen szakaszában, javasoljuk, hogy kezdje meg az érvényesítési folyamatot a meglévő erőforrások naplózásával. A házirend-definíció módosítása az új vagy frissített erőforrások _hamis pozitív_ hatosa esetén valószínűleg hatással lesz a meglévő erőforrásokra is.

## <a name="deploy-your-policy-to-resources"></a>A házirend üzembe helyezése az erőforrásokban

Miután befejezte az új házirend-definíció érvényesítését a meglévő erőforrásokkal és az új vagy frissített erőforráskérelmekkel, megkezdi a házirend megvalósításának folyamatát. Javasoljuk, hogy először hozza létre az új házirend-definíció házirend-hozzárendelését az összes erőforrás egy részhalmazához, például egy erőforráscsoporthoz. A kezdeti üzembe helyezés érvényesítése után terjessze ki a szabályzat hatókörét szélesebb és szélesebb szintekre, például előfizetésekre és felügyeleti csoportokra. Ez a bővítés úgy érhető el, hogy eltávolítja a hozzárendelést, és létrehoz egy újat a célhatóköröknél, amíg hozzá nem rendeli az új házirend-definíció által lefedett erőforrások teljes hatóköréhez.

A bevezetés során, ha olyan erőforrások találhatók, amelyekmentesek az új házirend-definíció alól, az alábbi módokon kell kezelni őket:

- A házirend-definíció frissítése egyértelműbbé a nem kívánt hatás csökkentése érdekében
- A házirend-hozzárendelés hatókörének módosítása (új hozzárendelés eltávolításával és létrehozásával)
- Erőforráscsoport hozzáadása a házirend-hozzárendelés kizárási listájához

A hatókör (szint vagy kizárások) bármilyen módosítását teljes mértékben ellenőrizni kell, és közölni kell a biztonsági és megfelelőségi szervezetekkel annak biztosítása érdekében, hogy a lefedettségben ne legyenek hiányosságok.

## <a name="monitor-your-policy-and-compliance"></a>A szabályzat és a megfelelőség figyelése

A házirend-definíció megvalósítása és hozzárendelése nem az utolsó lépés. Folyamatosan figyelje az erőforrások [megfelelőségi](../how-to/get-compliance-data.md) szintjét az új szabályzatdefinícióhoz, és állítsa be a megfelelő [Azure Monitor-riasztásokat és értesítéseket,](../../../azure-monitor/platform/alerts-overview.md) ha nem megfelelő eszközöket azonosítanak. Azt is javasoljuk, hogy értékelje ki a szabályzat definícióját és a kapcsolódó hozzárendelések ütemezett alapon, hogy érvényesítse a szabályzat definíciója megfelel az üzleti szabályzat és a megfelelőségi igényeknek. A házirendeket el kell távolítani, ha már nincs rá szükség. A szabályzatok időről időre frissítésre szorulnak, ahogy az alapul szolgáló Azure-erőforrások fejlődnek, és új tulajdonságokat és képességeket adnak hozzá.

## <a name="next-steps"></a>További lépések

- További információ a [házirend-definíciós struktúráról.](./definition-structure.md)
- További információ a [házirend-hozzárendelési struktúráról.](./assignment-structure.md)
- Ismerje meg, hogyan hozhat [létre programozott házirendeket.](../how-to/programmatically-create.md)
- További információ a [megfelelőségi adatok beszedéséről.](../how-to/get-compliance-data.md)
- További információ a [nem megfelelő erőforrások kiújulásáról.](../how-to/remediate-resources.md)
- Tekintse át, hogy mi a felügyeleti csoport az [Erőforrások rendszerezése az Azure felügyeleti csoportokkal.](../../management-groups/overview.md)