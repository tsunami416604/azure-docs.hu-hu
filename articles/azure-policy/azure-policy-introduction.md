---
title: "Az Azure Policy áttekintése | Microsoft Docs"
description: "Az Azure Policy az Azure egy szolgáltatása, amelynek használatával szabályzatdefiníciókat hozhat létre, rendelhet hozzá és kezelhet az Azure-környezetben."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders; nini
ms.date: 11/06/2017
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 8ffa4c22c39bcd241b36b55bbcba24ac3f75fff2
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="what-is-azure-policy"></a>Mi az Azure Policy?

Az informatikai szabályozással rendet teremthet az üzleti célok és az informatikai projektek között. A hatékony informatikai szabályozás stratégiai szinten kezeli a kezdeményezések tervezését és a prioritások meghatározását. A cégben jelentős mennyiségű, láthatóan megoldhatatlan informatikai probléma jelentkezik? Szabályzatok bevezetésével hatékonyabban kezelheti és előzheti meg ezeket. Az Azure Policy pedig kifejezetten a szabályzatok bevezetésére szolgál.

Az Azure Policy az Azure egy szolgáltatása, amelynek használatával szabályzatdefiníciókat hozhat létre, rendelhet hozzá és kezelhet. A szabályzatdefiníciók különböző szabályokat és műveleteket kényszerítenek ki az erőforrásokon, így azok megfelelnek a vállalati szabványoknak és szolgáltatói szerződéseknek. Az Azure Policy kiértékeli az erőforrásokat, és megkeresi azokat, amelyek nem felelnek meg az érvényben lévő szabályzatdefinícióknak. Például rendelkezhet egy olyan szabályzattal, amely csak bizonyos típusú virtuális gépeket engedélyez. Egy másik előírhatja, hogy az összes erőforrásnak rendelkeznie kell egy adott címkével. A rendszer aztán az erőforrások létrehozása és frissítése során kiértékeli a szabályzatokat.

## <a name="how-is-it-different-from-rbac"></a>Mennyiben különbözik ez az RBAC-től?

Van néhány lényeges különbség a szabályzat- és a szerepköralapú hozzáférés-vezérlés (RBAC) közt. Az RBAC a különféle hatókörű felhasználói műveletekre összpontosít. Például a felhasználó hozzá lehet adva a kívánt hatókörben egy erőforráscsoporthoz közreműködő szerepkörben. A szerepkör lehetővé teszi, hogy a felhasználó módosításokat hajtson végre az adott erőforráscsoporton. A szabályzat az erőforrások tulajdonságaira összpontosít az üzembe helyezés és a már létező erőforrások esetében. A szabályzatokon keresztül például azt szabályozhatja, hogy milyen típusú erőforrások létesíthetőek. Vagy korlátozhatja az erőforrások létesítési helyét. Az RBAC-től eltérően a szabályzatok alapértelmezés szerint megengedőek, és csak explicit módon tiltanak.

A szabályzatok használatához az RBAC-n keresztül hitelesítenie kell magát. A fióknak kifejezetten rendelkeznie kell a következőkkel:

- `Microsoft.Authorization/policydefinitions/write` engedély a szabályzatok definiálásához.
- `Microsoft.Authorization/policyassignments/write` engedély a szabályzatok hozzárendeléséhez.

Ezeket az engedélyeket a **Közreműködő** szerepkör nem tartalmazza.


## <a name="policy-definition"></a>Szabályzatdefiníció

Mindegyik szabályzatdefiníció feltételekkel rendelkezik, amelyek teljesülése esetén életbe lép. Továbbá rendelkezik egy kiegészítő művelettel, amely akkor lesz végrehajtva, ha a feltételek teljesülnek.

Az Azure Policy tartalmaz néhány beépített szabályzatot, amelyek alapértelmezés szerint a rendelkezésére állnak. Példa:

- **Az SQL Server 12.0-s verzió megkövetelése:** Az ehhez a szabályzathoz tartozó feltételek/szabályok biztosítják, hogy mindegyik SQL-kiszolgáló a 12.0-s verziót használja. A szabályzathoz tartozó művelet letiltja a feltételeknek meg nem felelő kiszolgálókat.
- **Engedélyezett tárfiók-termékváltozatok:** Ennek a szabályzatdefiníciónak a feltételei/szabályai megállapítják, hogy egy adott létesítendő tárfiók a termékváltozat-méretek meghatározott halmazába esik-e. A szabályzathoz tartozó művelet letiltja a megadott termékváltozat-mérethalmaznak meg nem felelő kiszolgálókat.
- **Engedélyezett erőforrástípus:** Ennek a szabályzatdefiníciónak a feltételei/szabályai határozzák meg a cég által üzembe helyezhető erőforrástípusokat. Megtagad minden olyan erőforrást, amely nem szerepel ebben az előre meghatározott listában.
- **Engedélyezett helyek**: Ezzel a szabályzattal korlátozható azon helyek köre, amelyeket a cég vagy szervezet megadhat az erőforrások üzembe helyezésekor. Biztosítja a földrajzi megfelelőségi követelmények betartását.
- **Engedélyezett virtuálisgép-termékváltozatok**: Ezzel a szabályzattal megadhatók a virtuális gépek azon termékváltozatai, amelyeket a cég vagy szervezet üzembe helyezhet.
- **Címke és a címke alapértelmezett értékének alkalmazása**: Ez a szabályzat érvényesít egy kötelezően megadandó címkét és a hozzá tartozó alapértelmezett értéket, ha a felhasználó nem adott meg címkét.
- **Címke és címke értékének kényszerítése**: Ez a szabályzat kötelezővé teszi egy címke és a hozzá tartozó érték megadását egy erőforráshoz.
- **Nem engedélyezett erőforrástípusok**: Ezzel a szabályzattal megadhatók mindazon erőforrástípusok, amelyeket a cég vagy szervezet nem helyezhet üzembe.

Ezen szabályzatok bármelyike hozzárendelhető az Azure Portalon, a PowerShellben vagy az Azure CLI-n.

További, a szabályzatdefiníciók szerkezetéről szóló információkért lásd a [szabályzatdefiníciók szerkezetével](policy-definition.md) foglalkozó témakört.

## <a name="policy-assignment"></a>Szabályzat-hozzárendelés

A szabályzat-hozzárendelés egy olyan szabályzatdefiníció, amely egy adott hatókörön belül érvényes. Ez a hatókör bármi lehet egy felügyeleti csoporttól egy erőforráscsoportig. A *hatókör* kifejezés az összes olyan erőforráscsoportra, előfizetésre vagy felügyeleti csoportra vonatkozik, amelyekhez a szabályzatdefiníció hozzá lett rendelve. A szabályzat-hozzárendeléseket az összes alárendelt erőforrás örökli. Tehát ha egy szabályzat érvényes egy erőforráscsoportra, akkor az adott erőforráscsoporton belüli összes erőforrásra is érvényes lesz. Azt is meg lehet azonban adni, hogy a szabályzat-hozzárendelés egy adott alhatókörre ne vonatkozzon. Egy előfizetésre vonatkozóan megadhat például egy olyan szabályzatot, amely megakadályozza a hálózati erőforrások létrehozását, de ennek a hatálya alól kivonhat egy, az előfizetésen belüli erőforráscsoportot, amelyet a hálózati infrastruktúra használ. Ehhez a hálózati erőforráscsoporthoz hozzáférést adhat a hálózati erőforrások létrehozásával megbízott felhasználóknak.

További, a szabályzatdefiníciók és -hozzárendelések beállítására vonatkozó tudnivalókért lásd a [szabályzat-hozzárendelés nem megfelelő erőforrások azonosításának céljából, az Azure környezetben történő létrehozásával](assign-policy-definition.md) foglalkozó cikket.

## <a name="policy-parameters"></a>Szabályzatparaméterek

A szabályzatparaméterek leegyszerűsítik a szabályzatok kezelését, mert csökkentik a létrehozandó szabályzatdefiníciók számát. A szabályzatdefiníciók létrehozásakor megadhat paramétereket, amelyek általánosabb érvényűvé teszik a definíciót. Ezután az adott szabályzatdefiníciót különböző helyzetekben újra felhasználhatja, ha a szabályzatdefiníció hozzárendelésekor más és más értékeket ad meg. Megadhat például egy adott helykészletet egy előfizetéshez.

A paramétereket a szabályzatdefiníciók létrehozásakor kell megadni/létrehozni. A paraméterek a létrehozásukkor kapnak egy nevet és esetleg egy értéket is. Például megadhat egy paramétert egy *hely* nevű szabályzathoz. Ezután a szabályzat hozzárendelésekor különböző értékeket adhat meg a paraméterhez, például *EastUS* vagy *WestUS*.

<!--
Next link should point to new Concept page for Parameters
-->
További tudnivalókat a szabályzatparaméterekről az [erőforrás-szabályzatok áttekintésének paraméterekkel](policy-definition.md#parameters) foglalkozó részében talál.

## <a name="initiative-definition"></a>Kezdeményezési definíció
A kezdeményezési definíciók olyan szabályzatdefiníció-gyűjtemények, amelyek egy átfogó cél teljesülését szolgálják. A kezdeményezési definíciók egyszerűbbé teszik a szabályzatdefiníciók kezelését és hozzárendelését. Ehhez azzal járulnak hozzá, hogy több szabályzatot egyetlen elembe csoportosítanak. Létrehozhat például egy kezdeményezést **Monitorozás engedélyezése az Azure Security Centerben** néven, amelynek az a célja, hogy monitorozza az Azure Security Centerben elérhető összes biztonsági javaslatot.

Egy ilyen kezdeményezés a következő szabályzatdefiníciókat tartalmazhatja:

1. **Titkosítatlan SQL Database-adatbázisok monitorozása a Security Centerben** – A titkosítatlan SQL Database-adatbázisok és -kiszolgálók monitorozásához.
2. **Operációs rendszer biztonsági réseinek monitorozása a Security Centerben** – Az olyan kiszolgálók monitorozásához, amelyek nem felelnek meg a konfigurált alapértékeknek.
3. **Végpontok hiányzó védelmének monitorozása a Security Centerben** – Az olyan kiszolgálók monitorozásához, amelyek nem rendelkeznek telepített végpontvédelmi ügynökkel.

<!--
For more information about initiative definitions, see Initiative Definitions.+ (instead of linking to this, link out to Concept page on Initiative Definitions)
-->

## <a name="initiative-assignment"></a>Kezdeményezési hozzárendelés
A szabályzat-hozzárendelésekhez hasonlóan a kezdeményezési hozzárendelések olyan kezdeményezési definíciók, amelyek egy adott hatókörhöz vannak hozzárendelve. A kezdeményezési hozzárendeléseknek köszönhetően nem kell olyan sok kezdeményezési definíciót létrehozni az egyes hatókörökhöz. Ez a hatókör szintén bármi lehet egy felügyeleti csoporttól egy erőforráscsoportig.

Az előző példában szereplő **Monitorozás engedélyezése az Azure Security Centerben** kezdeményezést több hatókörhöz is hozzá lehet rendelni. Egyszer például hozzárendelhető az **A előfizetéshez**, illetve külön a **B előfizetéshez**.

## <a name="initiative-parameters"></a>Kezdeményezési paraméterek
A szabályzatparaméterekhez hasonlóan a kezdeményezési paraméterek is a redundancia csökkentésével egyszerűsítik a kezdeményezések kezelését. A kezdeményezési paraméterek gyakorlatilag azon paraméterek listái, amelyeket a szabályzatdefiníciók a kezdeményezésen belül használnak.

Vegyünk példának egy olyan helyzetet, ahol egy kezdeményezési definícióhoz (**C kezdeményezés**) két szabályzatdefiníció tartozik. Mindkét szabályzatdefinícióban egy paraméter van megadva:

| Szabályzat | Paraméter neve |Paraméter típusa  |Megjegyzés |
|---|---|---|---|
| A szabályzat | allowedLocations | tömb  |Ez a paraméter karakterláncok listáját várja értékként, mivel a paraméter típusa tömbként lett megadva |
| B szabályzat | allowedSingleLocation |karakterlánc |Ez a paraméter egy szót vár értékként, mivel a paraméter típusa karakterláncként lett megadva |

Ebben a forgatókönyvben három lehetőség van a **C kezdeményezés** kezdeményezési paramétereinek megadására:

1. A kezdeményezésen belüli szabályzatdefiníciók paramétereinek használata. Ebben az esetben az *allowedLocations* és az *allowedSingleLocation* lesz a **C kezdeményezés** kezdeményezési paramétere.
2. Értékek megadása a kezdeményezési definíción belüli szabályzatdefiníciók paramétereihez. Ebben az esetben megadhat egy helylistát az **A szabályzat paramétere – allowedLocations** és a **B szabályzat paramétere – allowedSingleLocation** számára.
Az értékeket az adott kezdeményezés hozzárendelésekor is megadhatja.
3. Adjon meg egy listát mindazon lehetséges *értékekről*, amelyeket használhat a kezdeményezés hozzárendelésekor. A kezdeményezés hozzárendelésekor a kezdeményezésen belüli szabályzatdefiníciók örökölt paraméterei csak olyan értékekkel rendelkezhetnek, amelyek ebben a listában szerepelnek.

Például létrehozhat egy listát, amely a következő lehetséges értékeket tartalmazza egy kezdeményezési definícióhoz: *EastUS*, *WestUS*, *CentralUS*, *WestEurope*. Ha így járt el, akkor a kezdeményezés hozzárendelésekor ezeken kívül nem adhat meg más értékeket, például a *Southeast Asia* értéket, mivel az nem szerepel a listában.

## <a name="recommendations-for-managing-policies"></a>Javaslatok a szabályzatok kezeléséhez

A szabályzatdefiníciók és -hozzárendelések létrehozásakor és kezelésekor javasoljuk, hogy vegye figyelembe az alábbi néhány tanácsot:

- Ha a saját környezetében hoz létre szabályzatdefiníciókat, javasoljuk, hogy kezdjen egy naplózási művelettel a megtagadási művelet helyett, hogy nyomon tudja követni a szabályzatdefiníció hatását a környezet erőforrásaira. Ha vannak olyan futó szkriptjei, amelyek automatikusan vertikálisan felskálázzák az alkalmazásokat, a megtagadás művelet akadályozhatja a működésüket.
- Fontos a szervezeti hierarchiák figyelembe vétele a definíciók és hozzárendelések létrehozásakor. Javasoljuk, hogy a definíciókat magasabb szinteken hozza létre, például felügyeleti csoportok vagy előfizetések szintjén, majd a hozzárendeléseket végezze el a következő alárendelt szinten. Például ha létrehoz egy szabályzatdefiníciót a felügyeleti csoport szintjén, annak a definíciónak a szabályzat-hozzárendelését leszűkítheti a csoporton belül egy előfizetés szintjére.
- Javasoljuk a standard tarifacsomag használatát, hogy könnyebben átláthassa a környezet megfelelőségi állapotát. További információk az árképzési modelljeinkről és azok tartalmáról: [Díjszabás](https://azure.microsoft.com/pricing/details/azure-policy).
- Javasoljuk, hogy mindig kezdeményezési definíciókat használjon a szabályzatdefiníciók helyett, még akkor is, ha csak egyetlen szabályzatot tervez használni. Ha van például egy szabályzatdefiníciója (*A szabályzatdef.*), amelyet egy kezdeményezési definíción belül hozott létre (*C kezdeményezési def.*), és később úgy dönt, hogy létrehoz egy másik szabályzatdefiníciót *B szabályzatdef.* néven és az *A szabályzatdef.* definícióéhoz hasonló célokkal, akkor az új definíciót hozzáadhatja az *C kezdeményezési def.* definícióhoz a jobb követhetőség érdekében.

   Vegye figyelembe, hogy miután létrehozott egy kezdeményezési hozzárendelést egy kezdeményezési definícióhoz, a kezdeményezési definícióhoz hozzáadott minden új szabályzatdefiníció automatikusan bekerül az adott kezdeményezési definícióhoz tartozó kezdeményezési hozzárendelésekbe is. Azonban ha egy új paramétert ad meg az új szabályzatdefinícióban, akkor frissítenie kell a kezdeményezési definíciókat és hozzárendeléseket is.

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette az Azure Policy tudnivalóit és néhány fontosabb új fogalmat, folytatásként a következő témaköröket javasoljuk:

- [Szabályzatdefiníció hozzárendelése](./assign-policy-definition.md)
- [Szabályzatdefiníció hozzárendelése az Azure CLI-vel](./assign-policy-definition-cli.md)
- [Szabályzatdefiníció hozzárendelése a PowerShell-lel](./assign-policy-definition-ps.md)
