---
title: Az Azure szabályzatának áttekintése
description: Az Azure Policy az Azure egy szolgáltatása, amelynek használatával szabályzatdefiníciókat hozhat létre, rendelhet hozzá és kezelhet az Azure-környezetben.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: dbdffc7a6f77f3f34ce7937c60eb7a53e5f72590
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961280"
---
# <a name="what-is-azure-policy"></a>Mi az Azure Policy?

Az informatikai szabályozás biztosítja, hogy a szervezet az informatikai lehetőségek hatékony alkalmazásával képes legyen elérni céljait. Ezt az üzleti célok és az informatikai projektek közti átláthatóság megteremtésével éri el.

A cégben jelentős mennyiségű, láthatóan megoldhatatlan informatikai probléma jelentkezik?
A hatékony informatikai szabályozás stratégiai szinten kezeli a kezdeményezések tervezését és a prioritások meghatározását a problémák kezelése és megelőzése érdekében. Itt jön a képbe az Azure Policy.

Az Azure Policy az Azure egy szolgáltatása, amelynek használatával szabályzatokat hozhat létre, rendelhet hozzá és kezelhet. A szabályzatok különböző szabályokat és hatásokat kényszerítenek ki az erőforrásokon, hogy azok megfeleljenek a vállalati szabványoknak és szolgáltatói szerződéseknek. Az Azure Policy ezt az erőforrások kiértékelésével, és az érvényben lévő szabályzatdefinícióknak nem megfelelő erőforrások megkeresésével éri el. Például rendelkezhet egy olyan szabályzattal, amely csak bizonyos méretű virtuálisgép-termékváltozatot engedélyez a környezetben. A szabályzat bevezetését követően a rendszer az erőforrások létrehozása és frissítése alkalmával, valamint a már meglévő erőforrásokon is kiértékeli azt. A dokumentáció későbbi részében részletesen is áttekintjük a szabályzatok az Azure Policy használatával történő létrehozásának és bevezetésének menetét.

> [!IMPORTANT]
> Az Azure Policy megfelelőségértékelése mostantól tarifacsomagtól függetlenül minden hozzárendeléshez elérhető. Ha a hozzárendeléseknél nem láthatók a megfelelőségi adatok, győződjön meg róla, hogy az előfizetés regisztrálva van a Microsoft.PolicyInsights erőforrás-szolgáltatón.

## <a name="how-is-it-different-from-rbac"></a>Mennyiben különbözik ez az RBAC-től?

Van néhány lényeges különbség a szabályzat- és a szerepköralapú hozzáférés-vezérlés (RBAC) közt. Az RBAC a különféle hatókörű felhasználói műveletekre összpontosít. Például a felhasználó hozzá lehet adva a kívánt hatókörben egy erőforráscsoporthoz közreműködő szerepkörben. A szerepkör lehetővé teszi, hogy a felhasználó módosításokat hajtson végre az adott erőforráscsoporton.
A szabályzat az erőforrások tulajdonságaira összpontosít az üzembe helyezés és a már létező erőforrások esetében. A szabályzatokon keresztül például azt szabályozhatja, hogy milyen típusú erőforrások létesíthetőek. Vagy korlátozhatja az erőforrások létesítési helyét. Az RBAC-től eltérően a szabályzatok alapértelmezés szerint megengedőek, és csak explicit módon tiltanak.

### <a name="rbac-permissions-in-azure-policy"></a>RBAC-engedélyek az Azure Policyban

Az Azure Policy az engedélyeket a következő két erőforrás-szolgáltató műveleteiként jeleníti meg:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsight](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Számos beépített szerepkör biztosít különböző szintű engedélyeket az Azure Policy-erőforrásokhoz. Ilyen például a **Biztonsági rendszergazda**, amely felügyelheti a szabályzat-hozzárendeléseket és -definíciókat, de nem tekintheti meg a megfelelőségi információkat, vagy az **Olvasó**, amely olvashatja a szabályzat-hozzárendeléseket és -definíciókat, de nem végezhet módosításokat vagy tekintheti meg a megfelelőségi információkat. Míg a **Tulajdonos** teljes körű jogosultsággal rendelkezik, a **Közreműködő** semmilyen Azure Policy-jogosultsággal nem bír. A Policy megfelelőségi adatainak megtekintésére vonatkozó engedélyek kiosztásához hozzon létre egy [egyéni szerepkört](../../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Szabályzatdefiníció

Az Azure Policyban a szabályzatok létrehozásának és bevezetésének folyamata egy szabályzatdefiníció létrehozásával kezdődik. Mindegyik szabályzatdefiníció feltételekkel rendelkezik, amelyek teljesülése esetén életbe lép. Továbbá rendelkezik egy kiegészítő hatással, amely akkor lép fel, ha a feltételek teljesülnek.

Az Azure Policy tartalmaz néhány beépített szabályzatot, amelyek alapértelmezés szerint a rendelkezésére állnak. Például:

- **Az SQL Server 12.0-s verzió megkövetelése:** Az ehhez a szabályzathoz tartozó feltételek/szabályok biztosítják, hogy mindegyik SQL-kiszolgáló a 12.0-s verziót használja. Letiltja a feltételeknek meg nem felelő kiszolgálókat.
- **Engedélyezett tárfiók-termékváltozatok:** Ennek a szabályzatdefiníciónak a feltételei/szabályai megállapítják, hogy egy adott létesítendő tárfiók a termékváltozat-méretek meghatározott halmazába esik-e. Letiltja a megadott termékváltozat-mérethalmaznak meg nem felelő tárfiókokat.
- **Engedélyezett erőforrástípus:** Ennek a szabályzatdefiníciónak a feltételei/szabályai határozzák meg a cég által üzembe helyezhető erőforrástípusokat. Letilt minden olyan erőforrást, amely nem szerepel ebben az előre meghatározott listában.
- **Engedélyezett helyek**: Ezzel a szabályzattal korlátozható azon helyek köre, amelyeket a cég vagy szervezet megadhat az erőforrások üzembe helyezésekor. Biztosítja a földrajzi megfelelőségi követelmények betartását.
- **Engedélyezett virtuálisgép-termékváltozatok**: Ezzel a szabályzattal megadhatók a virtuális gépek azon termékváltozatai, amelyeket a cég vagy szervezet üzembe helyezhet.
- **Címke és a címke alapértelmezett értékének alkalmazása**: Ez a szabályzat érvényesít egy kötelezően megadandó címkét és a hozzá tartozó alapértelmezett értéket, ha a felhasználó nem adott meg címkét.
- **Címke és címke értékének kényszerítése**: Ez a szabályzat kötelezővé teszi egy címke és a hozzá tartozó érték megadását egy erőforráshoz.
- **Nem engedélyezett erőforrástípusok**: Ezzel a szabályzattal megadhatók mindazon erőforrástípusok, amelyeket a cég vagy szervezet nem helyezhet üzembe.

A szabályzatdefiníciók bevezetéséhez (a beépített és az egyéni definíciók esetében egyaránt) hozzá kell rendelnie azokat. Ezen szabályzatok bármelyike hozzárendelhető az Azure Portalon, a PowerShellben vagy az Azure CLI-n.

Vegye figyelembe, hogy a rendszer nagyjából óránként egyszer értékeli ki újra a szabályzatokat, ezért ha a szabályzat bevezetése (szabályzat-hozzárendelés létrehozása) után módosítja a szabályzatdefiníciót, annak az erőforrásokon való ismételt kiértékelése egy órán belül történik meg.

További, a szabályzatdefiníciók szerkezetéről szóló információkért lásd a [szabályzatdefiníciók szerkezetével](./concepts/definition-structure.md) foglalkozó témakört.

## <a name="policy-assignment"></a>Szabályzat-hozzárendelés

A szabályzat-hozzárendelés egy olyan szabályzatdefiníció, amely egy adott hatókörön belül érvényes. Ez a hatókör bármi lehet egy [felügyeleti csoporttól](../management-groups/overview.md) egy erőforráscsoportig. A *hatókör* kifejezés az összes olyan erőforráscsoportra, előfizetésre vagy felügyeleti csoportra vonatkozik, amelyekhez a szabályzatdefiníció hozzá lett rendelve. A szabályzat-hozzárendeléseket az összes alárendelt erőforrás örökli. Ez azt jelenti, hogy ha egy szabályzat érvényes egy erőforráscsoportra, akkor az adott erőforráscsoporton belüli összes erőforrásra is érvényes lesz. Azt is meg lehet azonban adni, hogy a szabályzat-hozzárendelés egy adott alhatókörre ne vonatkozzon.

Egy előfizetésre vonatkozóan megadhat például egy olyan szabályzatot, amely megakadályozza a hálózati erőforrások létrehozását, de ennek a hatálya alól kivonhat egy, az előfizetésen belüli erőforráscsoportot, amelyet a hálózati infrastruktúra használ. Ehhez a hálózati erőforráscsoporthoz hozzáférést adhat a hálózati erőforrások létrehozásával megbízott felhasználóknak.

Előfordulhat továbbá, hogy szeretne hozzárendelni egy erőforrástípus-alapú engedélyezési szabályzatot a felügyeleti csoport szintjén. Ezután hozzárendelne egy megengedőbb (több erőforrástípust engedélyező) szabályzatot egy alárendelt felügyeleti csoporthoz vagy akár közvetlenül az előfizetésekhez. Ez azonban nem működne, mivel a szabályzat egy explicit tiltási rendszer. A megoldás az, hogy kizárja az alárendelt felügyeleti csoportot vagy az előfizetést a felügyeleti csoport szintjén hozzárendelt szabályzatból, majd hozzárendeli a megengedőbb szabályzatot az alárendelt felügyeleti csoportok vagy az előfizetések szintjén. Lényegében, ha egy szabályzat eredménye egy erőforrás tiltása, akkor az erőforrás engedélyezésének egyetlen módja a tiltó szabályzat módosítása.

További, a szabályzatdefiníciók és -hozzárendelések a portálon keresztül történő beállítására vonatkozó tudnivalókért lásd a [szabályzat-hozzárendelés nem megfelelő erőforrások azonosításának céljából, az Azure környezetben történő létrehozásával](assign-policy-portal.md) foglalkozó cikket. A [PowerShellhez](assign-policy-powershell.md) és az [Azure CLI-hez](assign-policy-azurecli.md) is elérhetők lépések.

## <a name="policy-parameters"></a>Szabályzatparaméterek

A szabályzatparaméterek leegyszerűsítik a szabályzatok kezelését, mert csökkentik a létrehozandó szabályzatdefiníciók számát. A szabályzatdefiníciók létrehozásakor megadhat paramétereket, amelyek általánosabb érvényűvé teszik a definíciót. Ezután az adott szabályzatdefiníciót különböző helyzetekben újra felhasználhatja, ha a szabályzatdefiníció hozzárendelésekor más és más értékeket ad meg. Megadhat például egy adott helykészletet egy előfizetéshez.

A paramétereket a szabályzatdefiníciók létrehozásakor kell megadni/létrehozni. A paraméterek a létrehozásukkor kapnak egy nevet és esetleg egy értéket is. Például megadhat egy paramétert egy *hely* nevű szabályzathoz. Ezután a szabályzat hozzárendelésekor különböző értékeket adhat meg a paraméterhez, például *EastUS* vagy *WestUS*.

További tudnivalókat a szabályzatparaméterekről az [erőforrás-szabályzatok áttekintésének paraméterekkel](./concepts/definition-structure.md#parameters) foglalkozó részében talál.

## <a name="initiative-definition"></a>Kezdeményezési definíció

A kezdeményezési definíciók olyan szabályzatdefiníció-gyűjtemények, amelyek egy átfogó cél teljesülését szolgálják. A kezdeményezési definíciók egyszerűbbé teszik a szabályzatdefiníciók kezelését és hozzárendelését. Ehhez azzal járulnak hozzá, hogy több szabályzatot egyetlen elembe csoportosítanak. Létrehozhat például egy kezdeményezést **Monitorozás engedélyezése az Azure Security Centerben** néven, amelynek az a célja, hogy monitorozza az Azure Security Centerben elérhető összes biztonsági javaslatot.

Egy ilyen kezdeményezés a következő szabályzatdefiníciókat tartalmazhatja:

- **Titkosítatlan SQL Database-adatbázisok monitorozása a Security Centerben** – A titkosítatlan SQL Database-adatbázisok és -kiszolgálók monitorozásához.
- **Operációs rendszer biztonsági réseinek monitorozása a Security Centerben** – Az olyan kiszolgálók monitorozásához, amelyek nem felelnek meg a konfigurált alapértékeknek.
- **Végpontok hiányzó védelmének monitorozása a Security Centerben** – Az olyan kiszolgálók monitorozásához, amelyek nem rendelkeznek telepített végpontvédelmi ügynökkel.

## <a name="initiative-assignment"></a>Kezdeményezési hozzárendelés

A szabályzat-hozzárendelésekhez hasonlóan a kezdeményezési hozzárendelések olyan kezdeményezési definíciók, amelyek egy adott hatókörhöz vannak hozzárendelve. A kezdeményezési hozzárendeléseknek köszönhetően nem kell olyan sok kezdeményezési definíciót létrehozni az egyes hatókörökhöz. Ez a hatókör szintén bármi lehet egy felügyeleti csoporttól egy erőforráscsoportig.

Az előző példában szereplő **Monitorozás engedélyezése az Azure Security Centerben** kezdeményezést több hatókörhöz is hozzá lehet rendelni. Egyszer például hozzárendelhető az **A előfizetéshez**,
illetve külön a **B előfizetéshez**.

## <a name="initiative-parameters"></a>Kezdeményezési paraméterek

A szabályzatparaméterekhez hasonlóan a kezdeményezési paraméterek is a redundancia csökkentésével egyszerűsítik a kezdeményezések kezelését. A kezdeményezési paraméterek gyakorlatilag azon paraméterek listái, amelyeket a szabályzatdefiníciók a kezdeményezésen belül használnak.

Vegyünk példának egy olyan helyzetet, ahol egy kezdeményezési definícióhoz (**C kezdeményezés**) az **A szabályzat** és a **B szabályzat** szabályzatdefiníció tartozik, amelyek mindketten különböző típusú paramétert várnak:

| Szabályzat | Paraméter neve |Paraméter típusa  |Megjegyzés |
|---|---|---|---|
| A szabályzat | allowedLocations | tömb  |Ez a paraméter sztringek listáját várja értékként, mivel a paraméter típusa tömbként lett megadva |
| B szabályzat | allowedSingleLocation |sztring |Ez a paraméter egy szót vár értékként, mivel a paraméter típusa sztringként lett megadva |

Ebben a forgatókönyvben három lehetőség van a **C kezdeményezés** kezdeményezési paramétereinek megadására:

- A kezdeményezésen belüli szabályzatdefiníciók paramétereinek használata. Ebben az esetben az *allowedLocations* és az *allowedSingleLocation* lesz a **C kezdeményezés** kezdeményezési paramétere.
- Értékek megadása a kezdeményezési definíción belüli szabályzatdefiníciók paramétereihez. Ebben az esetben megadhat egy helylistát az **A szabályzat paramétere – allowedLocations** és a **B szabályzat paramétere – allowedSingleLocation** számára. Az értékeket az adott kezdeményezés hozzárendelésekor is megadhatja.
- Adjon meg egy listát mindazon lehetséges *értékekről*, amelyeket használhat a kezdeményezés hozzárendelésekor. A kezdeményezés hozzárendelésekor a kezdeményezésen belüli szabályzatdefiníciók örökölt paraméterei csak olyan értékekkel rendelkezhetnek, amelyek ebben a listában szerepelnek.

Például létrehozhat egy listát, amely a következő lehetséges értékeket tartalmazza egy kezdeményezési definícióhoz: *EastUS*, *WestUS*, *CentralUS*, *WestEurope*. Ha így járt el, akkor a kezdeményezés hozzárendelésekor ezeken kívül nem adhat meg más értékeket, például a *Southeast Asia* értéket, mivel az nem szerepel a listában.

## <a name="maximum-count-of-policy-objects"></a>Policy-objektumok maximális száma

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>Javaslatok a szabályzatok kezeléséhez

A szabályzatdefiníciók és -hozzárendelések létrehozásakor és kezelésekor javasoljuk, hogy vegye figyelembe az alábbi néhány tanácsot és tippet:

- Ha a saját környezetében hoz létre szabályzatdefiníciókat, javasoljuk, hogy kezdjen egy naplózási művelettel a megtagadási művelet helyett, hogy nyomon tudja követni a szabályzatdefiníció hatását a környezet erőforrásaira. Ha vannak olyan futó szkriptjei, amelyek automatikusan vertikálisan felskálázzák az alkalmazásokat, a megtagadás művelet akadályozhatja a működésüket.
- Fontos a szervezeti hierarchiák figyelembe vétele a definíciók és hozzárendelések létrehozásakor. Javasoljuk, hogy a definíciókat magasabb szinteken hozza létre, például felügyeleti csoportok vagy előfizetések szintjén, majd a hozzárendeléseket végezze el a következő alárendelt szinten. Például ha létrehoz egy szabályzatdefiníciót a felügyeleti csoport szintjén, annak a definíciónak a szabályzat-hozzárendelését leszűkítheti a csoporton belül egy előfizetés szintjére.
- Javasoljuk, hogy mindig kezdeményezési definíciókat használjon a szabályzatdefiníciók helyett, még akkor is, ha csak egyetlen szabályzatot tervez használni. Ha van például egy szabályzatdefiníciója (*A szabályzatdef.*), amelyet egy kezdeményezési definíción belül hozott létre (*C kezdeményezési def.*), és később úgy dönt, hogy létrehoz egy másik szabályzatdefiníciót *B szabályzatdef.* néven és az *A szabályzatdef.* definícióéhoz hasonló célokkal, akkor az új definíciót hozzáadhatja az *C kezdeményezési def.* definícióhoz a jobb követhetőség érdekében.
- Vegye figyelembe, hogy miután létrehozott egy kezdeményezési hozzárendelést egy kezdeményezési definícióhoz, a kezdeményezési definícióhoz hozzáadott minden új szabályzatdefiníció automatikusan bekerül az adott kezdeményezési definícióhoz tartozó kezdeményezési hozzárendelésekbe is.
- A kezdeményezési hozzárendelések aktiválásakor a kezdeményezésben lévő összes szabályzat szintén aktiválódik. Azonban ha külön kell végrehajtania valamely szabályzatot, célszerűbb azt nem bevonni a kezdeményezés alá.

## <a name="video-overview"></a>Áttekintő videó

Az Azure Policy alábbi áttekintése a 2018-as buildből származik. A bemutató vagy a videó letöltéséhez lépjen a Channel 9 blog [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) (Az Azure-környezet szabályozása az Azure Policy használatával) című témakörére.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az Azure Policy tudnivalóit és néhány fontosabb fogalmat, folytatásként a következő témaköröket javasoljuk:

- [Szabályzatdefiníció hozzárendelése a portállal](assign-policy-portal.md)
- [Szabályzatdefiníció hozzárendelése az Azure CLI-vel](assign-policy-azurecli.md)
- [Szabályzatdefiníció hozzárendelése a PowerShell-lel](assign-policy-powershell.md)
- A felügyeleti csoportok áttekintéséért lásd [az erőforrások az Azure Felügyeleti csoportok segítségével való rendszerezését](..//management-groups/overview.md) ismertető részt.
- A Channel 9 blog [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) (Az Azure-környezet szabályozása az Azure Policy használatával) című témakörének megtekintése