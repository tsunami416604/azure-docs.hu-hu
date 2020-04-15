---
title: Az erőforrások rendszerezése felügyeleti csoportokkal – Azure Governance
description: Megismerheti a felügyeleti csoportokat és azok használatának módját, valamint a hozzájuk tartozó engedélyek működését.
ms.date: 04/15/2020
ms.topic: overview
ms.openlocfilehash: cc60e4555f0fb2b920b8061fb044ce5dde990d38
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381534"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Erőforrások rendszerezése az Azure Management Groups segítségével

Ha a vállalatnak sok előfizetése van, jól jöhet egy módszer, hogy hatékonyan kezelje az előfizetésekhez való hozzáférést, a szabályzatokat és a megfelelőséget. Az Azure Management Groups előfizetések fölötti hatókörszintet biztosít. Az előfizetéseket „felügyeleti csoportok” nevű tárolókba rendezheti, és az irányítási feltételeket alkalmazhatja a felügyeleti csoportokra. A felügyeleti csoporton belüli összes előfizetés automatikusan örökli a felügyeleti csoportra alkalmazott feltételeket. A felügyeleti csoportok nagy léptékű, nagyvállalati szintű felügyeletet tesznek lehetővé, függetlenül az előfizetése típusától.
A felügyeleti csoporton belüli összes előfizetésnek ugyanazzal az Azure Active Directory-bérlővel kell megbízhatósági kapcsolatban állnia.

Alkalmazhat például olyan szabályzatokat egy felügyeleti csoportra, amelyek korlátozzák a virtuális gépek (VM-ek) létrehozásához elérhető régiókat. A szabályzat minden felügyeleti csoportra, előfizetésre és erőforrásra érvényes lesz a felügyeleti csoporton belül, ha csak az adott régióban engedélyezi virtuális gépek létrehozását.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>A felügyeleti csoportok és előfizetések hierarchiája

A felügyeleti csoportok és előfizetések rugalmas szerkezetének létrehozásával hierarchiába rendezheti erőforrásait az egységes szabályzat- és hozzáféréskezeléshez. Az alábbi ábrán egy példa látható szabályozási hierarchia létrehozására felügyeleti csoportok használatával.

:::image type="content" source="./media/tree.png" alt-text="Példa egy felügyeleti csoport hierarchiafájára" border="false":::

Létrehozhat egy hierarchiát, amelyre szabályzatot alkalmazhat, például a virtuális gépek helyének az USA nyugati régiójára való korlátozását a „Production” csoporton. Ez a szabályzat örökli az összes nagyvállalati szerződés (EA) előfizetések, amelyek leszármazottai az adott felügyeleti csoport, és az előfizetések szerinti összes virtuális gépre vonatkozik. Ezt a biztonsági szabályzatot az erőforrás vagy az előfizetés tulajdonosa nem módosíthatja, ez pedig hatékonyabb kontrollt biztosít.

A felügyeleti csoportok használatának másik esete, amikor egyszerre több előfizetés számára szeretne felhasználói hozzáférést biztosítani. Ha több előfizetést helyez a felügyeleti csoport alá, mindössze egy [szerepköralapú hozzáférés-vezérlési](../../role-based-access-control/overview.md) (RBAC) hozzárendelést kell létrehoznia a felügyeleti csoporthoz, amelytől az összes előfizetés örökli a hozzáférést. Ahelyett, hogy különböző előfizetésekre szkriptelne RBAC-hozzárendeléseket, a felügyeleti csoporton egyetlen hozzárendeléssel biztosíthatja a szükséges hozzáférést a felhasználóknak.

### <a name="important-facts-about-management-groups"></a>A felügyeleti csoportok fontosabb jellemzői

- A címtárak legfeljebb 10 000 felügyeleti csoportot támogatnak.
- A felügyeleticsoport-fák legfeljebb hatszintűek lehetnek.
  - A korlátozásba nem tartozik bele a gyökérszint és az előfizetés szintje.
- Felügyeleti csoportonként vagy előfizetésenként egy szülő támogatott.
- Az egyes felügyeleti csoportok alá számos gyermek tartozhat.
- Az egyes címtárakban minden előfizetés és felügyeleti csoport egyetlen hierarchiában található. Tekintse meg [A gyökérszintű felügyeleti csoport fontosabb jellemzői](#important-facts-about-the-root-management-group) című szakaszt.

## <a name="root-management-group-for-each-directory"></a>Az egyes címtárak gyökérszintű felügyeleti csoportja

Minden címtárhoz tartozik egy legfelső szintű, vagy más néven gyökérszintű felügyeleti csoport. Ez a gyökérszintű felügyeleti csoport úgy épül be a hierarchiába, hogy minden felügyeleti csoport és előfizetés fölött legyen. Ez a gyökérszintű felügyeleti csoport lehetővé teszi globális szabályzatok és RBAC-hozzárendelések címtárszintű alkalmazását. Az [Azure AD globális rendszergazdájának először emelnie kell a jogosultsági szintjét](../../role-based-access-control/elevate-access-global-admin.md), hogy a Felhasználói hozzáférés adminisztrátora szerepkörrel rendelkezzen a gyökérszintű csoport esetében. A jogosultsági szint emelését követően a rendszergazda bármilyen RBAC-szerepkört hozzárendelhet a címtár felhasználóihoz vagy csoportjaihoz a hierarchia kezelése érdekében. Rendszergazdaként hozzárendelheti saját fiókját a gyökérszintű felügyeleti csoport tulajdonosaként.

### <a name="important-facts-about-the-root-management-group"></a>A gyökérszintű felügyeleti csoport fontosabb jellemzői

- A gyökérszintű felügyeleti csoport megjelenített neve alapértelmezés szerint **Bérlői gyökércsoport** lesz. Az azonosító az Azure Active Directory-azonosító lesz.
- A megjelenített név módosításához a fiókjának a Tulajdonos vagy Közreműködő szerepkörrel kell rendelkeznie a gyökérszintű felügyeleti csoportra vonatkozóan. Lásd: [Felügyeleti csoport nevének módosítása](manage.md#change-the-name-of-a-management-group) a felügyeleti csoport nevének frissítéséhez.
- A gyökérszintű felügyeleti csoportot a többi felügyeleti csoporttal szemben nem lehet törölni vagy áthelyezni.  
- A címtár összes előfizetése és felügyeleti csoportja a gyökérszintű felügyeleti csoport alá kerül.
  - A globális felügyelet érdekében a címtár erőforrásai is a gyökérszintű felügyeleti csoport alá kerülnek.
  - Az újonnan létrehozott előfizetések alapértelmezés szerint a gyökérszintű felügyeleti csoporthoz tartoznak.
- A gyökérszintű felügyeleti csoport az összes Azure-ügyfél számára látható, de nem mindegyikük rendelkezik hozzáféréssel a kezeléséhez.
  - Bárki, aki hozzáféréssel rendelkezik egy adott előfizetéshez, láthatja, hogy az hol helyezkedik el a hierarchiában.  
  - Senki nem kap alapértelmezés szerint hozzáférést a gyökérszintű felügyeleti csoporthoz. Kizárólag az Azure AD globális rendszergazdái emelhetik meg jogosultsági szintjüket, hogy hozzáférést kapjanak. Miután hozzáfértek a gyökérfelügyeleti csoporthoz, a globális rendszergazdák bármilyen RBAC szerepkört hozzárendelhetnek más felhasználókhoz a kezeléshez  
    meg.
- Az SDK-ban a gyökérfelügyeleti csoport vagy a "Bérlői gyökér" felügyeleti csoport ként működik.

> [!IMPORTANT]
> A gyökérszintű felügyeleti csoporton végrehajtott felhasználóihozzáférés- és szabályzat-hozzárendelések **a címtárban lévő valamennyi erőforrásra érvényesek lesznek**. Ezért minden ügyfélnek fel kell mérnie, mire van szüksége ebben a hatókörben. A felhasználói hozzáférés és a házirend-hozzárendelések csak ennél a  
> Hatókör.

## <a name="initial-setup-of-management-groups"></a>A felügyeleti csoportok kezdeti beállítása

A felügyeleti csoportok használatának megkezdésekor először egy beállítási folyamat történik. A folyamat első lépéseként létrejön a gyökérszintű felügyeleti csoport a címtárban. A csoport létrehozása után a címtárban található összes meglévő előfizetés a gyökérszintű felügyeleti csoport gyermekeként lesz beállítva.
A folyamat célja, hogy egy adott címtáron belül csak egy felügyeleticsoport-hierarchia legyen. Az egyetlen hierarchia beállítása lehetővé teszi a rendszergazdai ügyfelek számára globális hozzáférések és szabályzatok alkalmazását, amelyeket a címtárat használó többi ügyfél nem tud megkerülni. Minden gyökérszintű hozzárendelés érvényes lesz a teljes hierarchiára, amely tartalmazza az összes felügyeleti csoportot, előfizetést, erőforráscsoportot és az Azure AD-bérlőn belüli erőforrásokat.

## <a name="trouble-seeing-all-subscriptions"></a>Nem látható az összes előfizetés

Néhány címtárban, amelyek az előzetes verzió korai szakaszában (2018. június 25. előtt) kezdtek felügyeleti csoportokat használni, egy probléma jelentkezhet, amelynek következtében a hierarchiában nem található meg minden előfizetés. Az előfizetéseket a hierarchiába helyező eljárás azután lett megvalósítva, hogy egy szerepkör- vagy szabályzat-hozzárendelés végre lett hajtva a címtár gyökérszintű felügyeleti csoportján.

### <a name="how-to-resolve-the-issue"></a>A probléma elhárítása

A probléma megoldására két lehetősége van.

- Az összes szerepkör- és szabályzat-hozzárendelés eltávolítása a gyökérszintű felügyeleti csoportról
  - Ha az összes szerepkör- és szabályzat-hozzárendelést eltávolítja a gyökérszintű felügyeleti csoportról, a szolgáltatás a következő éjszakai ciklus során visszatölti az összes előfizetést a hierarchiába. Ennek a folyamatnak az a célja, hogy a hozzáférések vagy szabályzat-hozzárendelések kiosztása nehogy véletlenül érvényes legyen az összes bérlői előfizetésre.
  - A legjobb mód a folyamat a szolgáltatások működésének befolyásolása nélküli végrehajtására, ha a szerepkör- vagy szabályzat-hozzárendeléseket egy szinttel a gyökérszintű felügyeleti csoport alatt alkalmazza. Ezután eltávolíthatja az összes hozzárendelést a gyökérszintű hatókörből.
- A visszatöltési folyamat elindítása az API közvetlen meghívásával
  - A címtár bármelyik ügyfele meghívhatja a _TenantBackfillStatusRequest_ vagy a _StartTenantBackfillRequest_ API-t. A StartTenantBackfillRequest API a meghívásakor elindítja az összes előfizetés a hierarchiába való átvitelére vonatkozó kezdeti konfigurációs folyamatot. A folyamat azt az eljárást is elindítja, amely az összes új előfizetést a gyökérszintű felügyeleti csoport gyermekeként érvényesíti.
    Ennek az eljárásnak a végrehajtásakor nem szükséges a gyökérszinten módosítani a hozzárendeléseket. Az API meghívásával jóváhagyja, hogy a gyökérszinten jelen lévő szabályzatok és hozzáférési hozzárendelések az összes előfizetésre alkalmazhatók.

Ha kérdése van a visszatöltési folyamatot illetően, lépjen kapcsolatba velünk a következő e-mail-címen: `managementgroups@microsoft.com`
  
## <a name="management-group-access"></a>Hozzáférés a felügyeleti csoportokhoz

Az Azure felügyeleti csoportjai támogatják az [Azure szerepkör-alapú hozzáférés-vezérlést (RBAC)](../../role-based-access-control/overview.md) minden erőforrás-hozzáféréshez és szerepkör-definícióhoz.
Ezeket az engedélyeket a hierarchiában található összes gyermek erőforrás örökli. Bármely RBAC szerepkör hozzárendelhető egy felügyeleti csoporthoz, amely örökli a hierarchiát az erőforrásokhoz. A virtuálisgép-közreműködői RBAC-szerepkör például hozzárendelhető a felügyeleti csoporthoz. Ez a szerepkör nem végez műveletet a felügyeleti csoporton, de a csoport alá tartozó összes virtuális gép örökli.

Az alábbi ábrán a felügyeleti csoportokkal kapcsolatos szerepkörök és támogatott műveletek listája látható.

| RBAC-szerepkör neve             | Létrehozás | Átnevezés | Mozgatni\*\* | Törlés | Hozzáférés hozzárendelése | Szabályzat hozzárendelése | Olvasás  |
|:-------------------------- |:------:|:------:|:--------:|:------:|:-------------:| :------------:|:-----:|
|Tulajdonos                       | X      | X      | X        | X      | X             | X             | X     |
|Közreműködő                 | X      | X      | X        | X      |               |               | X     |
|MG közreműködő\*            | X      | X      | X        | X      |               |               | X     |
|Olvasó                      |        |        |          |        |               |               | X     |
|MG olvasó\*                 |        |        |          |        |               |               | X     |
|Erőforrás-szabályzat közreműködője |        |        |          |        |               | X             |       |
|Felhasználói hozzáférés rendszergazdája   |        |        |          |        | X             | X             |       |

\*: Az MG Közreműködő és az MG Reader csak a felügyeleti csoport hatókörén végezheti ezeket a műveleteket.  
\*\*: A legfelső szintű felügyeleti csoport szerepkör-hozzárendelései nem szükségesek az előfizetés vagy felügyeleti csoport áthelyezéséhez. A hierarchián belüli elemek áthelyezésének részleteiért tekintse meg az [Erőforrások kezelése felügyeleti csoportokkal](manage.md) című szakaszt.

## <a name="custom-rbac-role-definition-and-assignment"></a>Egyéni RBAC szerepkör-definíció és hozzárendelés

A felügyeleti csoportok egyéni RBAC szerepkör-támogatása jelenleg előzetes verzióban érhető el, bizonyos [korlátozásokkal.](#limitations) A felügyeleti csoportok hatóköre a szerepkör-definíció hozzárendelhető hatókörében határozható meg. Az egyéni RBAC-szerepkör hozzárendelhető lesz az adott felügyeleti csoporthoz és az alá tartozó összes felügyeleti csoporthoz, előfizetéshez, erőforráscsoporthoz vagy erőforráshoz. Az egyéni szerepkör ugyanúgy öröklődik lefelé a hierarchián belül, mint a beépített szerepkörök.  

### <a name="example-definition"></a>Példa definíciója

[Az egyéni szerepkör definiálása és létrehozása](../../role-based-access-control/custom-roles.md) nem változik a felügyeleti csoportok felvételével. A teljes elérési út segítségével definiálja a **/providers/Microsoft.Management/managementgroups/{groupId}** felügyeleti csoportot.

A felügyeleti csoport azonosítóját használja, ne a felügyeleti csoport megjelenítendő nevét. Ez a gyakori hiba azért fordul elő, mert mindkettő egyénileg definiált mező egy felügyeleti csoport létrehozásakor.

```json
...
{
  "Name": "MG Test Custom Role",
  "Id": "id", 
  "IsCustom": true,
  "Description": "This role provides members understand custom roles.",
  "Actions": [
    "Microsoft.Management/managementgroups/delete",
    "Microsoft.Management/managementgroups/read",
    "Microsoft.Management/managementgroup/write",
    "Microsoft.Management/managementgroup/subscriptions/delete",
    "Microsoft.Management/managementgroup/subscriptions/write",
    "Microsoft.resources/subscriptions/read",
    "Microsoft.Authorization/policyAssignments/*",
    "Microsoft.Authorization/policyDefinitions/*",
    "Microsoft.Authorization/policySetDefinitions/*",
    "Microsoft.PolicyInsights/*",
    "Microsoft.Authorization/roleAssignments/*",
    "Microsoft.Authorization/roledefinitions/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
        "/providers/microsoft.management/managementGroups/ContosoCorporate"
  ]
}
...
```

### <a name="issues-with-breaking-the-role-definition-and-assignment-hierarchy-path"></a>A szerepkör-definíció és a hozzárendelés-hierarchia elérési útjának megszakadásával kapcsolatos problémák

A szerepkör-definíciók a felügyeleticsoport-hierarchia bármely pontjára hozzárendelhető hatókört jelentenek. A szerepkör-definíció definiálható egy fölérendelt felügyeleti csoporton, míg a tényleges szerepkör-hozzárendelés létezik a gyermek-előfizetés. Mivel kapcsolat van a két elem között, hibaüzenet jelenik meg, amikor megpróbálja elválasztani a hozzárendelést a definíciójától.

Nézzük meg például a hierarchia egy kis részét egy vizualizációhoz.

:::image type="content" source="./media/subtree.png" alt-text="részfa" border="false":::

Tegyük fel, hogy van egy egyéni szerepkör definiálva a Marketing felügyeleti csoportban. Ezt az egyéni szerepkört ezután a két ingyenes próba-előfizetéshez rendeli hozzá.  

Ha megpróbáljuk áthelyezni az egyik ilyen előfizetést, hogy a termelésfelügyeleti csoport gyermekének legyen, ez az áthelyezés megszakítja az előfizetési szerepkör-hozzárendelésről a Marketingfelügyeleti csoport szerepkör-definíciójára való elérési utat. Ebben az esetben hibaüzenet jelenik meg, amely szerint az áthelyezés nem engedélyezett, mivel megszakítja ezt a kapcsolatot.  

Van néhány különböző lehetőség, hogy rögzítse ezt a forgatókönyvet:
- Távolítsa el a szerepkör-hozzárendelést az előfizetésből, mielőtt áthelyezi az előfizetést egy új szülő MG-re.
- Adja hozzá az előfizetést a szerepkör-definíció hozzárendelhető hatóköréhez.
- Módosítsa a hozzárendelhető hatókört a szerepkör-definíción belül. A fenti példában frissítheti a hozzárendelhető hatóköröket a Marketingről a Gyökérfelügyeleti csoportra, hogy a definíciót a hierarchia mindkét ága elérhesse.  
- Hozzon létre egy további egyéni szerepkört, amely a másik ágban lesz definiálva. Ez az új szerepkör megköveteli, hogy a szerepkör-hozzárendelés az előfizetésen is meg kell változtatni.  

### <a name="limitations"></a>Korlátozások  

Vannak korlátozások, amelyek léteznek, ha egyéni szerepköröket használ a felügyeleti csoportokon. 

 - Egy új szerepkör hozzárendelhető hatóköreiben csak egy felügyeleti csoportot határozhat meg. Ez a korlátozás a szerepkör-definíciók és a szerepkör-hozzárendelések kapcsolatának megszakadása esetén érvényben van. Ez akkor fordul elő, ha egy szerepkör-hozzárendeléssel rendelkező előfizetésvagy felügyeleti csoport egy másik szülőre kerül, amely nem rendelkezik a szerepkör-definícióval.  
 - Az RBAC Data Plane-műveletek nem definiálhatók a felügyeleti csoport egyéni szerepköreiben. Ez a korlátozás van érvényben, mivel van egy késési probléma RBAC műveletek frissítése az adatsík erőforrás-szolgáltatók. Ez a késési probléma dolgozik, és ezeket a műveleteket le lesz tiltva a szerepkör-definíció kockázatok csökkentése érdekében.
 - Az Azure Resource Manager nem ellenőrzi a felügyeleti csoport létezését a szerepkör-definíció hozzárendelhető hatókörében. Ha elírás vagy helytelen felügyeleticsoport-azonosító szerepel a listában, a szerepkör-definíció továbbra is létrejön.  

## <a name="moving-management-groups-and-subscriptions"></a>Felügyeleti csoportok és előfizetések áthelyezése 

Egy felügyeleti csoport vagy előfizetés egy másik felügyeleti csoport gyermekének, három szabályt kell kiértékelni igazként.

Ha a költözési műveletet végzi, a következőkre van szüksége: 

- Felügyeleti csoport írási és szerepkör-hozzárendelés írási engedélyei a gyermek-előfizetésvagy felügyeleti csoport.
  - Beépített szerepkör példa **tulajdonos**
- A felügyeleti csoport írási hozzáférése a célszülő felügyeleti csoporthoz.
  - Példa beépített szerepkörre: **Tulajdonos,** **Közreműködő**, **Felügyeleti csoport közreműködője**
- A felügyeleti csoport írási hozzáférése a meglévő szülőfelügyeleti csoporthoz.
  - Példa beépített szerepkörre: **Tulajdonos,** **Közreműködő**, **Felügyeleti csoport közreműködője**

**Kivétel**: Ha a cél vagy a meglévő szülő felügyeleti csoport a legfelső szintű felügyeleti csoport, az engedélykövetelmények nem érvényesek. Mivel a gyökérfelügyeleti csoport az összes új felügyeleti csoport és előfizetés alapértelmezett leszállási helye, nincs szüksége engedélyekre egy elem áthelyezéséhez.

Ha az előfizetés tulajdonosi szerepköre az aktuális felügyeleti csoporttól öröklődik, az áthelyezési célok korlátozottak. Az előfizetést csak egy másik felügyeleti csoportba helyezheti át, ahol a Tulajdonos szerepkör van. Nem helyezheti át egy felügyeleti csoportba, ahol közreműködő, mert elveszítené az előfizetés tulajdonjogát. Ha közvetlenül hozzá van rendelve az előfizetés tulajdonosi szerepköréhez (nem a felügyeleti csoporttól örökölt), áthelyezheti bármelyik felügyeleti csoportba, ahol közreműködő.

## <a name="audit-management-groups-using-activity-logs"></a>Felügyeleti csoportok naplózása tevékenységnaplókkal

A felügyeleti csoportok támogatottak az [Azure-tevékenységnaplóban](../../azure-monitor/platform/platform-logs-overview.md). A felügyeleti csoportokkal kapcsolatos minden eseményre ugyanarról a központi helyről kereshet rá, mint más Azure-erőforrások esetében. Például megtekintheti egy adott felügyeleti csoporthoz tartozó összes szerepkör-hozzárendelés vagy szabályzat-hozzárendelés módosításait.

:::image type="content" source="./media/al-mg.png" alt-text="Tevékenységnaplók felügyeleti csoportokkal" border="false":::

Az Azure Portalon kívüli felügyeleti csoportok lekérdezésekor a felügyeleti csoportok célhatóköre a következőhöz hasonlóan néz ki: **"/ providers/Microsoft.Management/managementGroups/{yourMgID}"**.

## <a name="next-steps"></a>További lépések

A felügyeleti csoportokkal kapcsolatos további tudnivalókért lásd:

- [Felügyeleti csoportok létrehozása az Azure-erőforrások rendszerezéséhez](./create.md)
- [Felügyeleti csoportok módosítása, törlése és kezelése](./manage.md)
- [Felügyeleti csoportok áttekintése az Azure PowerShell Erőforrások moduljában](/powershell/module/az.resources#resources)
- [Felügyeleti csoportok áttekintése a REST API-ban](/rest/api/resources/managementgroups)
- [Felügyeleti csoportok áttekintése az Azure CLI-ben](/cli/azure/account/management-group)
