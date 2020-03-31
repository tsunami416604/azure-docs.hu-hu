---
title: Az Azure szabályzatának áttekintése
description: Az Azure Policy az Azure egy szolgáltatása, amelynek használatával szabályzatdefiníciókat hozhat létre, rendelhet hozzá és kezelhet az Azure-környezetben.
ms.date: 11/25/2019
ms.topic: overview
ms.custom: fasttrack-edit
ms.openlocfilehash: e886f37a8d7f1395b5c831e81e600ecc6e2dd20f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241527"
---
# <a name="what-is-azure-policy"></a>Mi az Azure Policy?

Cégirányítási ellenőrzi, hogy a szervezet képes elérni a céljait az informatikai hatékony és eredményes használatával. Megfelel ennek az igénynek azáltal, hogy egyértelművé teszi az üzleti célokat és az informatikai projekteket.

A cégben jelentős mennyiségű, láthatóan megoldhatatlan informatikai probléma jelentkezik? A hatékony informatikai szabályozás stratégiai szinten kezeli a kezdeményezések tervezését és a prioritások meghatározását a problémák kezelése és megelőzése érdekében. Ez a stratégiai igény az, ahol az Azure Policy érkezik.

Az Azure Policy az Azure egy szolgáltatása, amelynek használatával szabályzatokat hozhat létre, rendelhet hozzá és kezelhet. A szabályzatok különböző szabályokat és hatásokat kényszerítenek ki az erőforrásokon, hogy azok megfeleljenek a vállalati szabványoknak és szolgáltatói szerződéseknek. Az Azure Policy úgy tesz eleget ezeknek az elvárásoknak, hogy kiértékeli, megfelelnek-e az erőforrások a hozzájuk rendelt szabályzatoknak. Az Azure Policy által tárolt összes adat inkultett készleten van titkosítva.

Például rendelkezhet egy olyan szabályzattal, amely csak bizonyos méretű virtuálisgép-termékváltozatot engedélyez a környezetben. Ennek a szabályzatnak a bevezetése után a rendszer kiértékeli, hogy az új és meglévő erőforrások megfelelnek-e annak. A megfelelő típusú szabályzattal biztosítható a meglévő erőforrások megfelelősége. A dokumentáció későbbi részében további részleteket olvashat arról, hogyan hozhat létre és valósíthat meg szabályzatokat az Azure Policy használatával.

> [!IMPORTANT]
> Az Azure Policy megfelelőségértékelése mostantól tarifacsomagtól függetlenül minden hozzárendeléshez elérhető. Ha a hozzárendeléseknél nem láthatók a megfelelőségi adatok, győződjön meg róla, hogy az előfizetés regisztrálva van a Microsoft.PolicyInsights erőforrás-szolgáltatón.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-is-it-different-from-rbac"></a>Mennyiben különbözik ez az RBAC-től?

Van néhány fő különbség az Azure-szabályzat és a szerepköralapú hozzáférés-vezérlés (RBAC) között. Az RBAC a különféle hatókörű felhasználói műveletekre összpontosít. Előfordulhat, hogy hozzáadódik egy erőforráscsoport közreműködői szerepköréhez, így módosíthatja az adott erőforráscsoportot. Az Azure Policy az üzembe helyezés során az erőforrás-tulajdonságokra és a már meglévő erőforrásokra összpontosít. Az Azure Policy szabályozza az erőforrások típusához vagy helyéhez hasonló tulajdonságokat. Az RBAC-kal ellentétben az Azure Policy egy alapértelmezett engedélyezési és explicit megtagadási rendszer.

### <a name="rbac-permissions-in-azure-policy"></a>RBAC-engedélyek az Azure Policyban

Az Azure Policy több engedéllyel (más néven művelettel) rendelkezik két erőforrás-szolgáltatóban:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Számos beépített szerepkör oszt ki engedélyeket Azure Policy-erőforrásoknak. Az **erőforrás-házirend közreműködői** szerepkör tartalmazza a legtöbb Azure-szabályzati műveletet. **A tulajdonosnak** teljes jogai vannak. A **Közreműködő** és **a Reader** egyaránt használhatja az összes olvasási Azure-szabályzat-műveletet, de a **közreműködő** is elindíthatja a szervizelést.

Ha egyik beépített szerepkör sem tartalmazza a szükséges engedélyeket, hozzon létre egy [egyéni szerepkört](../../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Szabályzatdefiníció

Az Azure Policyban a szabályzatok létrehozásának és bevezetésének folyamata egy szabályzatdefiníció létrehozásával kezdődik. Minden házirend-definíciónak vannak olyan feltételei, amelyek mellett a végrehajtás feltételei vannak. És van egy meghatározott hatása, hogy történik, ha a feltételek teljesülnek.

Az Azure Policy számos beépített szabályzatot kínálunk, amelyek alapértelmezés szerint elérhetők. Példa:

- **Engedélyezett tárfiók-termékváltozatok:** Azt határozza meg, hogy egy üzembe helyezett tárfiók termékváltozat-méretekkészletén belül van-e. Hatása az, hogy megtagadja az összes olyan tárfiókok, amelyek nem tartják be a megadott termékváltozat-méretek készletét.
- **Engedélyezett erőforrástípus:** Meghatározza a telepíthető erőforrástípusokat. Hatása, hogy megtagadja az összes erőforrást, amely nem része ennek a definiált listának.
- **Engedélyezett helyek:** Korlátozza az új erőforrások rendelkezésre álló helyeit. Biztosítja a földrajzi megfelelőségi követelmények betartását.
- **Engedélyezett virtuálisgép-skus:** A virtuálisgép-skus-ok üzembe helyezhető készletét adja meg.
- **Címke hozzáadása az erőforrásokhoz:** A szükséges címkét és annak alapértelmezett értékét alkalmazza, ha a központi telepítési kérelem nem adja meg.
- **Címke kényszerítése és értéke:** A szükséges címke és annak értéke kényszerítése egy erőforráshoz.
- **Nem engedélyezett erőforrástípusok:** Megakadályozza az erőforrástípusok listájának üzembe helyezését.

Ezek a házirend-definíciók (beépített és egyéni definíciók) megvalósításához hozzá kell rendelnie őket. Ezen szabályzatok bármelyike hozzárendelhető az Azure Portalon, a PowerShellben vagy az Azure CLI-n.

A házirendek kiértékelése számos különböző művelettel történik, például házirend-hozzárendeléssel vagy házirend-frissítéssel. A teljes listát a [Házirend-értékelési eseményindítók című témakörben található.](./how-to/get-compliance-data.md#evaluation-triggers)

További, a szabályzatdefiníciók szerkezetéről szóló információkért lásd a [szabályzatdefiníciók szerkezetével](./concepts/definition-structure.md) foglalkozó témakört.

## <a name="policy-assignment"></a>Szabályzat-hozzárendelés

A szabályzat-hozzárendelés egy olyan szabályzatdefiníció, amely egy adott hatókörön belül érvényes. Ez a hatókör egy [felügyeleti csoporttól](../management-groups/overview.md) az egyes erőforrásokig terjedhet. A *hatókör* kifejezés az összes olyan erőforrásra, erőforráscsoportra, előfizetésre vagy felügyeleti csoportra vonatkozik, amelyhez a házirend-definíció hozzá van rendelve. A szabályzat-hozzárendeléseket az összes alárendelt erőforrás örökli. Ez a kialakítás azt jelenti, hogy az erőforráscsoportra alkalmazott házirendaz adott erőforráscsoport erőforrásaira is vonatkozik. Azt is meg lehet azonban adni, hogy a szabályzat-hozzárendelés egy adott alhatókörre ne vonatkozzon.

Egy előfizetésre vonatkozóan megadhat például egy olyan szabályzatot, amely megakadályozza a hálózati erőforrások létrehozását, Kizárhat egy erőforráscsoportot az előfizetésben, amely hálózati infrastruktúrára szolgál. Ezután hozzáférést biztosít ehhez a hálózati erőforráscsoporthoz azoknak a felhasználóknak, akikben megbízik a hálózati erőforrások létrehozásában.

Egy másik példában előfordulhat, hogy a felügyeleti csoport szintjén szeretne hozzárendelni egy erőforrástípus engedélyezési listaházirendet. Ezután hozzárendelne egy megengedőbb (több erőforrástípust engedélyező) szabályzatot egy alárendelt felügyeleti csoporthoz vagy akár közvetlenül az előfizetésekhez. Ez azonban nem működne, mivel a szabályzat egy explicit tiltási rendszer. A megoldás az, hogy kizárja az alárendelt felügyeleti csoportot vagy az előfizetést a felügyeleti csoport szintjén hozzárendelt szabályzatból, majd hozzárendeli a megengedőbb szabályzatot az alárendelt felügyeleti csoportok vagy az előfizetések szintjén. Ha bármely házirend azt eredményezi, hogy egy erőforrás tanusítása megtörténik, akkor az erőforrás engedélyezésének egyetlen módja a megtagadási házirend módosítása.

További, a szabályzatdefiníciók és -hozzárendelések a portálon keresztül történő beállítására vonatkozó tudnivalókért lásd a [szabályzat-hozzárendelés nem megfelelő erőforrások azonosításának céljából, az Azure környezetben történő létrehozásával](assign-policy-portal.md) foglalkozó cikket. A [PowerShellhez](assign-policy-powershell.md) és az [Azure CLI-hez](assign-policy-azurecli.md) is elérhetők lépések.

## <a name="policy-parameters"></a>Szabályzatparaméterek

A szabályzatparaméterek leegyszerűsítik a szabályzatok kezelését, mert csökkentik a létrehozandó szabályzatdefiníciók számát. A szabályzatdefiníciók létrehozásakor megadhat paramétereket, amelyek általánosabb érvényűvé teszik a definíciót. Ezután az adott szabályzatdefiníciót különböző helyzetekben újra felhasználhatja, ha a szabályzatdefiníció hozzárendelésekor más és más értékeket ad meg. Megadhat például egy adott helykészletet egy előfizetéshez.

A paraméterek a házirend-definíció létrehozásakor kerülnek definiálásra. A paraméterek a definiálásukkor kapnak egy nevet és esetleg egy értéket is. Például megadhat egy paramétert egy *hely* nevű szabályzathoz. Ezután a szabályzat hozzárendelésekor különböző értékeket adhat meg a paraméterhez, például *EastUS* vagy *WestUS*.

A házirend-paraméterekről további információt a [Definícióstruktúra - Paraméterek](./concepts/definition-structure.md#parameters)című témakörben talál.

## <a name="initiative-definition"></a>Kezdeményezési definíció

A kezdeményezési definíciók olyan szabályzatdefiníció-gyűjtemények, amelyek egy átfogó cél teljesülését szolgálják. A kezdeményezési definíciók egyszerűbbé teszik a szabályzatdefiníciók kezelését és hozzárendelését. Ehhez azzal járulnak hozzá, hogy több szabályzatot egyetlen elembe csoportosítanak. Létrehozhat például egy kezdeményezést **Monitorozás engedélyezése az Azure Security Centerben** néven, amelynek az a célja, hogy monitorozza az Azure Security Centerben elérhető összes biztonsági javaslatot.

> [!NOTE]
> Az SDK, például az Azure CLI és az Azure PowerShell, **a policyset** nevű tulajdonságok at és paramétereket használ a kezdeményezésekhez.

Egy ilyen kezdeményezés a következő szabályzatdefiníciókat tartalmazhatja:

- **Titkosítatlan SQL Database-adatbázisok monitorozása a Security Centerben** – A titkosítatlan SQL Database-adatbázisok és -kiszolgálók monitorozásához.
- **Az operációs rendszer biztonsági réseinek figyelése a Security Centerben** – Olyan kiszolgálók figyelése, amelyek nem felelnek meg a konfigurált alapkonfigurációnak.
- **Végpontok hiányzó védelmének monitorozása a Security Centerben** – Az olyan kiszolgálók monitorozásához, amelyek nem rendelkeznek telepített végpontvédelmi ügynökkel.

## <a name="initiative-assignment"></a>Kezdeményezési hozzárendelés

A szabályzat-hozzárendelésekhez hasonlóan a kezdeményezési hozzárendelések olyan kezdeményezési definíciók, amelyek egy adott hatókörhöz vannak hozzárendelve. A kezdeményezési hozzárendeléseknek köszönhetően nem kell olyan sok kezdeményezési definíciót létrehozni az egyes hatókörökhöz. Ez a hatókör egy felügyeleti csoporttól az egyes erőforrásokig is terjedhet.

Minden egyes kezdeményezés különböző hatókörökhöz rendelhető. Egy kezdeményezés rendelhető mind **az előfizetéshezA** és **"B" előfizetéshez.**

## <a name="initiative-parameters"></a>Kezdeményezési paraméterek

A szabályzatparaméterekhez hasonlóan a kezdeményezési paraméterek is a redundancia csökkentésével egyszerűsítik a kezdeményezések kezelését. A kezdeményezési paraméterek olyan paraméterek, amelyeket a kezdeményezésen belüli házirend-definíciók használnak.

Vegyünk példának egy olyan helyzetet, ahol egy kezdeményezési definícióhoz (**C kezdeményezés**) az **A szabályzat** és a **B szabályzat** szabályzatdefiníció tartozik, amelyek mindketten különböző típusú paramétert várnak:

| Szabályzat | Paraméter neve |Paraméter típusa  |Megjegyzés |
|---|---|---|---|
| A szabályzat | allowedLocations | tömb  |Ez a paraméter sztringek listáját várja értékként, mivel a paraméter típusa tömbként lett megadva |
| B szabályzat | allowedSingleLocation |sztring |Ez a paraméter egy szót vár értékként, mivel a paraméter típusa sztringként lett megadva |

Ebben a forgatókönyvben három lehetőség van a **C kezdeményezés** kezdeményezési paramétereinek megadására:

- A kezdeményezésen belüli szabályzatdefiníciók paramétereinek használata. Ebben az esetben az *allowedLocations* és az *allowedSingleLocation* lesz a **C kezdeményezés** kezdeményezési paramétere.
- Értékek megadása a kezdeményezési definíción belüli szabályzatdefiníciók paramétereihez. Ebben a példában megadhat egy helylistát a **policyA paraméteréhez – az allowedLocations** és **a policyB paramétere – az allowedSingleLocation**. Az értékeket az adott kezdeményezés hozzárendelésekor is megadhatja.
- Adjon meg egy listát mindazon lehetséges *értékekről*, amelyeket használhat a kezdeményezés hozzárendelésekor. A kezdeményezés hozzárendelésekor a kezdeményezésen belüli szabályzatdefiníciók örökölt paraméterei csak olyan értékekkel rendelkezhetnek, amelyek ebben a listában szerepelnek.

Amikor értékbeállításokat hoz létre egy kezdeményezésdefinícióban, nem tud más értéket megadni a kezdeményezés-hozzárendelés során, mert az nem része a listának.

## <a name="maximum-count-of-azure-policy-objects"></a>Az Azure Policy-objektumok maximális száma

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>Javaslatok a szabályzatok kezeléséhez

Íme néhány tipp és tipp, amelyeket szem előtt kell tartania:

- Kezdje egy naplózási hatás helyett a megtagadási hatás nyomon követésére hatása a házirend-definíció a környezetben lévő erőforrások. Ha már rendelkezik az alkalmazások automatikus skálázására már meglévő parancsfájlokkal, a megtagadási hatás beállítása akadályozhatja a már meglévő automatizálási feladatokat.

- A definíciók és hozzárendelések létrehozásakor vegye figyelembe a szervezeti hierarchiákat. Azt javasoljuk, hogy definíciók létrehozása magasabb szinteken, például a felügyeleti csoport vagy az előfizetési szinten. Ezután hozza létre a hozzárendelést a következő gyermekszinten. Ha egy felügyeleti csoportban hoz létre definíciót, a hozzárendelés hatóköre az adott felügyeleti csoporton belüli előfizetésre vagy erőforráscsoportra is levihető.

- Azt javasoljuk, hogy még egyetlen házirend-definícióesetén is hozzon létre és osszon hozzá kezdeményezésdefiníciókat.
  Például, van *politikadefiníciós politikaDefA,* és hozza létre a kezdeményezés *definíciós kezdeményezésDefC*. Ha később egy másik házirend-definíciót hoz létre a *policyDefB* számára a *policyDefA-hoz*hasonló célokkal, hozzáadhatja azt *az initiativeDefC* csoportban, és együtt nyomon követheti őket.

- Miután létrehozott egy kezdeményezés-hozzárendelést, a kezdeményezéshez hozzáadott házirend-definíciók is a kezdeményezések hozzárendelésének részévé válnak.

- A kezdeményezési hozzárendelés kiértékelésekor a program a kezdeményezésen belüli összes szabályzatot is értékeli.
  Ha egyénileg kell értékelnie egy házirendet, jobb, ha nem tartalmazza azt egy kezdeményezésben.

## <a name="video-overview"></a>Videó – áttekintés

Az Azure Policy alábbi áttekintése a 2018-as buildből származik. Diák vagy videó letöltés, látogasson [el az Azure-környezet szabályozása az Azure-szabályzat](https://channel9.msdn.com/events/Build/2018/THR2030) a Channel 9-en.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az Azure Policy tudnivalóit és néhány fontosabb fogalmat, folytatásként a következő témaköröket javasoljuk:

- [Házirend-definíció hozzárendelése a portál használatával](./assign-policy-portal.md).
- [Rendeljen hozzá egy szabályzatdefiníciót az Azure CLI használatával.](./assign-policy-azurecli.md)
- [Házirend-definíció hozzárendelése a PowerShell használatával.](./assign-policy-powershell.md)
