---
title: Erőforrások rendszerezése az Azure Management Groups segítségével – Azure Governance
description: Megismerheti a felügyeleti csoportokat és azok használatának módját, valamint a hozzájuk tartozó engedélyek működését.
author: rthorn17
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.date: 04/17/2019
ms.author: rithorn
ms.topic: overview
ms.openlocfilehash: 157701e826d6a281a60393e1ec270cf061be8214
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699381"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Erőforrások rendszerezése az Azure Management Groups segítségével

Ha a vállalatnak sok előfizetése van, jól jöhet egy módszer, hogy hatékonyan kezelje az előfizetésekhez való hozzáférést, a szabályzatokat és a megfelelőséget. Az Azure Management Groups előfizetések fölötti hatókörszintet biztosít. Az előfizetéseket „felügyeleti csoportok” nevű tárolókba rendezheti, és az irányítási feltételeket alkalmazhatja a felügyeleti csoportokra. A felügyeleti csoporton belüli összes előfizetés automatikusan örökli a felügyeleti csoportra alkalmazott feltételeket. A felügyeleti csoportok nagy léptékű, nagyvállalati szintű felügyeletet tesznek lehetővé, függetlenül az előfizetése típusától.

Alkalmazhat például olyan szabályzatokat egy felügyeleti csoportra, amelyek korlátozzák a virtuális gépek (VM-ek) létrehozásához elérhető régiókat. A szabályzat minden felügyeleti csoportra, előfizetésre és erőforrásra érvényes lesz a felügyeleti csoporton belül, ha csak az adott régióban engedélyezi virtuális gépek létrehozását.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>A felügyeleti csoportok és előfizetések hierarchiája

A felügyeleti csoportok és előfizetések rugalmas szerkezetének létrehozásával hierarchiába rendezheti erőforrásait az egységes szabályzat- és hozzáféréskezeléshez. Az alábbi ábrán egy példa látható szabályozási hierarchia létrehozására felügyeleti csoportok használatával.

![Példa egy felügyeleti csoport hierarchiafájára](./media/tree.png)

Hozzon létre egy hierarchiát, amelyre alkalmazhat egy szabályzatot, például a virtuális gépek helyének az USA nyugati régiójára való korlátozására a „Production” csoporton. Ezt a szabályzatot a felügyeleti csoport alá tartozó mindkét EA-előfizetés örökli, és az előfizetések alá tartozó összes virtuális gépre érvényes lesz. Ezt a biztonsági szabályzatot az erőforrás vagy az előfizetés tulajdonosa nem módosíthatja, ez pedig hatékonyabb kontrollt biztosít.

A felügyeleti csoportok használatának másik esete, amikor egyszerre több előfizetéshez szeretne felhasználói hozzáférést biztosítani. Ha több előfizetést helyez a felügyeleti csoport alá, mindössze egy [szerepköralapú hozzáférés-vezérlési](../../role-based-access-control/overview.md) (RBAC) hozzárendelést kell létrehoznia a felügyeleti csoporthoz, amelytől az összes előfizetés örökli a hozzáférést.
Ahelyett, hogy különböző előfizetésekre szkriptelne RBAC-hozzárendeléseket, a felügyeleti csoporton egyetlen hozzárendeléssel biztosíthatja a szükséges hozzáférést a felhasználóknak.

### <a name="important-facts-about-management-groups"></a>A felügyeleti csoportok fontosabb jellemzői

- A címtárak legfeljebb 10 000 felügyeleti csoportot támogatnak.
- A felügyeleticsoport-fák legfeljebb hatszintűek lehetnek.
  - A korlátozásba nem tartozik bele a gyökérszint és az előfizetés szintje.
- Felügyeleti csoportonként vagy előfizetésenként egy szülő támogatott.
- Az egyes felügyeleti csoportok alá számos gyermek tartozhat.
- Az egyes címtárakban minden előfizetés és felügyeleti csoport egyetlen hierarchiában található. Tekintse meg [A gyökérszintű felügyeleti csoport fontosabb jellemzői](#important-facts-about-the-root-management-group) című szakaszt.

## <a name="root-management-group-for-each-directory"></a>Az egyes címtárak gyökérszintű felügyeleti csoportja

Minden címtárhoz tartozik egy legfelső szintű, vagy más néven gyökérszintű felügyeleti csoport.
Ez a gyökérszintű felügyeleti csoport úgy épül be a hierarchiába, hogy minden felügyeleti csoport és előfizetés fölött legyen. Ez a gyökérszintű felügyeleti csoport lehetővé teszi globális szabályzatok és RBAC-hozzárendelések címtárszintű alkalmazását. Az [Azure AD globális rendszergazdájának először emelnie kell a jogosultsági szintjét](../../role-based-access-control/elevate-access-global-admin.md), hogy a Felhasználói hozzáférés adminisztrátora szerepkörrel rendelkezzen a gyökérszintű csoport esetében. A jogosultsági szint emelését követően a rendszergazda bármilyen RBAC-szerepkört hozzárendelhet a címtár felhasználóihoz vagy csoportjaihoz a hierarchia kezelése érdekében. Rendszergazdaként hozzárendelheti saját fiókját a gyökérszintű felügyeleti csoport tulajdonosaként.

### <a name="important-facts-about-the-root-management-group"></a>A gyökérszintű felügyeleti csoport fontosabb jellemzői

- A gyökérszintű felügyeleti csoport neve és azonosítója alapértelmezés szerint meg van adva. A megjelenített név bármikor módosítható az Azure Portalon. A [név módosításához](manage.md#change-the-name-of-a-management-group) a fiókjának a Tulajdonos vagy Közreműködő szerepkörrel kell rendelkeznie a gyökérszintű felügyeleti csoportra vonatkozóan.
  - A név „Bérlői gyökércsoport” lesz.
  - Az azonosító az Azure Active Directory-azonosító lesz.
- A gyökérszintű felügyeleti csoportot a többi felügyeleti csoporttal szemben nem lehet törölni vagy áthelyezni.  
- A címtár összes előfizetése és felügyeleti csoportja a gyökérszintű felügyeleti csoport alá kerül.
  - A globális felügyelet érdekében a címtár erőforrásai is a gyökérszintű felügyeleti csoport alá kerülnek.
  - Az újonnan létrehozott előfizetések alapértelmezés szerint a gyökérszintű felügyeleti csoporthoz tartoznak.
- A gyökérszintű felügyeleti csoport az összes Azure-ügyfél számára látható, de nem mindegyikük rendelkezik hozzáféréssel a kezeléséhez.
  - Bárki, aki hozzáféréssel rendelkezik egy adott előfizetéshez, láthatja, hogy az hol helyezkedik el a hierarchiában.  
  - Senki nem kap alapértelmezés szerint hozzáférést a gyökérszintű felügyeleti csoporthoz. Kizárólag az Azure AD globális rendszergazdái emelhetik meg jogosultsági szintjüket, hogy hozzáférést kapjanak.  Ezt követően bármilyen RBAC-szerepkört hozzárendelhetnek a címtár felhasználóihoz.  

> [!IMPORTANT]
> A gyökérszintű felügyeleti csoporton végrehajtott felhasználóihozzáférés- és szabályzat-hozzárendelések **a címtárban lévő valamennyi erőforrásra érvényesek lesznek**.
> Ezért minden ügyfélnek fel kell mérnie, mire van szüksége ebben a hatókörben.
> A felhasználói hozzáférések és a szabályzatok hozzárendelései csak ebben a hatókörben lehetnek kötelezőek.  

## <a name="initial-setup-of-management-groups"></a>A felügyeleti csoportok kezdeti beállítása

A felügyeleti csoportok használatának megkezdésekor először egy beállítási folyamat történik. A folyamat első lépéseként létrejön a gyökérszintű felügyeleti csoport a címtárban. A csoport létrehozása után a címtárban található összes meglévő előfizetés a gyökérszintű felügyeleti csoport gyermekeként lesz beállítva. A folyamat célja, hogy egy adott címtáron belül csak egy felügyeleticsoport-hierarchia legyen. Az egyetlen hierarchia beállítása lehetővé teszi a rendszergazdai ügyfelek számára globális hozzáférések és szabályzatok alkalmazását, amelyeket a címtárat használó többi ügyfél nem tud megkerülni. Minden gyökérszintű hozzárendelés érvényes lesz a teljes hierarchiára, amely tartalmazza az összes felügyeleti csoportot, előfizetést, erőforráscsoportot és az Azure AD-bérlőn belüli erőforrásokat.

## <a name="trouble-seeing-all-subscriptions"></a>Nem látható az összes előfizetés

Néhány címtárban, amelyek az előzetes verzió korai szakaszában (2018. június 25. előtt) kezdtek felügyeleti csoportokat használni, egy probléma jelentkezhet, amelynek következtében a hierarchiában nem található meg minden előfizetés. Az előfizetéseket a hierarchiába helyező eljárás azután lett megvalósítva, hogy egy szerepkör- vagy szabályzat-hozzárendelés végre lett hajtva a címtár gyökérszintű felügyeleti csoportján. 

### <a name="how-to-resolve-the-issue"></a>A probléma elhárítása

A probléma megoldására két lehetősége van.

1. Az összes szerepkör- és szabályzat-hozzárendelés eltávolítása a gyökérszintű felügyeleti csoportról
   1. Ha az összes szerepkör- és szabályzat-hozzárendelést eltávolítja a gyökérszintű felügyeleti csoportról, a szolgáltatás a következő éjszakai ciklus során visszatölti az összes előfizetést a hierarchiába.  Ennek a folyamatnak az a célja, hogy a hozzáférések vagy szabályzat-hozzárendelések kiosztása nehogy véletlenül érvényes legyen az összes bérlői előfizetésre.
   1. A legjobb mód a folyamat a szolgáltatások működésének befolyásolása nélküli végrehajtására, ha a szerepkör- vagy szabályzat-hozzárendeléseket egy szinttel a gyökérszintű felügyeleti csoport alatt alkalmazza. Ezután eltávolíthatja az összes hozzárendelést a gyökérszintű hatókörből.
1. A visszatöltési folyamat elindítása az API közvetlen meghívásával
   1. A címtár bármelyik ügyfele meghívhatja a *TenantBackfillStatusRequest* vagy a *StartTenantBackfillRequest* API-t. A StartTenantBackfillRequest API a meghívásakor elindítja az összes előfizetés a hierarchiába való átvitelére vonatkozó kezdeti konfigurációs folyamatot. A folyamat azt az eljárást is elindítja, amely az összes új előfizetést a gyökérszintű felügyeleti csoport gyermekeként érvényesíti. Ennek az eljárásnak a végrehajtásakor nem szükséges a gyökérszinten módosítani a hozzárendeléseket. Az API meghívásával jóváhagyja, hogy a gyökérszinten jelen lévő szabályzatok és hozzáférési hozzárendelések az összes előfizetésre alkalmazhatók.

Ha kérdése van a visszatöltési folyamatot illetően, lépjen kapcsolatba velünk a következő e-mail-címen: managementgroups@microsoft.com  
  
## <a name="management-group-access"></a>Hozzáférés a felügyeleti csoportokhoz

Az Azure felügyeleti csoportjai támogatják az [Azure szerepkör-alapú hozzáférés-vezérlést (RBAC)](../../role-based-access-control/overview.md) minden erőforrás-hozzáféréshez és szerepkör-definícióhoz.
Ezeket az engedélyeket a hierarchiában található összes gyermek erőforrás örökli. Bármilyen beépített RBAC-szerepkör hozzárendelhető a felügyeleti csoporthoz, amely továbbörökíti azt a hierarchiában lejjebb található erőforrásoknak is.
A virtuálisgép-közreműködői RBAC-szerepkör például hozzárendelhető a felügyeleti csoporthoz. Ez a szerepkör nem végez műveletet a felügyeleti csoporton, de a csoport alá tartozó összes virtuális gép örökli.

Az alábbi ábrán a felügyeleti csoportokkal kapcsolatos szerepkörök és támogatott műveletek listája látható.

| RBAC-szerepkör neve             | Létrehozás | Átnevezés | Áthelyezés** | Törlés | Hozzáférés hozzárendelése | Szabályzat hozzárendelése | Olvasás  |
|:-------------------------- |:------:|:------:|:------:|:------:|:-------------:| :------------:|:-----:|
|Tulajdonos                       | X      | X      | X      | X      | X             | X             | X     |
|Közreműködő                 | X      | X      | X      | X      |               |               | X     |
|Felügyeleti csoport közreműködője*             | X      | X      | X      | X      |               |               | X     |
|Olvasó                      |        |        |        |        |               |               | X     |
|Felügyeleti csoport olvasója*                  |        |        |        |        |               |               | X     |
|Erőforrás-szabályzat közreműködője |        |        |        |        |               | X             |       |
|Felhasználói hozzáférés rendszergazdája   |        |        |        |        | X             |               |       |

*: a Felügyeleti csoport közreműködője és a Felügyeleti csoport olvasója szerepkör kizárólag a felügyeleti csoport hatókörén belül engedélyezi az adott művelet végrehajtását a felhasználók számára.  
**: Előfizetések vagy felügyeleti csoportok a gyökérszintű felügyeleti csoportba vagy onnan máshova való áthelyezéséhez nem szükséges, hogy a gyökérszintű felügyeleti csoporthoz tartozzanak szerepkör-hozzárendelések.  A hierarchián belüli elemek áthelyezésének részleteiért tekintse meg az [Erőforrások kezelése felügyeleti csoportokkal](manage.md) című szakaszt.

### <a name="custom-rbac-role-definition-and-assignment"></a>Egyéni RBAC-szerepkördefiníció és -hozzárendelés

Az egyéni RBAC-szerepkörök jelenleg nem támogatottak a felügyeleti csoportok esetében. A funkció aktuális állapotáról a [felügyeleti csoportok visszajelzési fórumán](https://aka.ms/mgfeedback) tájékozódhat.

## <a name="audit-management-groups-using-activity-logs"></a>Felügyeleti csoportok naplózása tevékenységnaplókkal

A felügyeleti csoportok támogatottak az [Azure-tevékenységnaplóban](../../azure-monitor/platform/activity-logs-overview.md). A felügyeleti csoportokkal kapcsolatos minden eseményre ugyanarról a központi helyről kereshet rá, mint más Azure-erőforrások esetében.  Például megtekintheti egy adott felügyeleti csoporthoz tartozó összes szerepkör-hozzárendelés vagy szabályzat-hozzárendelés módosításait.

![Tevékenységnaplók felügyeleti csoportokkal](media/al-mg.png)

Az Azure Portalon kívüli felügyeleti csoportok lekérdezésekor a felügyeleti csoportok célhatóköre a következőhöz hasonlóan néz ki: **"/ providers/Microsoft.Management/managementGroups/{yourMgID}"**.

## <a name="next-steps"></a>További lépések

A felügyeleti csoportokkal kapcsolatos további tudnivalókért lásd:

- [Felügyeleti csoportok létrehozása az Azure-erőforrások rendszerezéséhez](create.md)
- [Felügyeleti csoportok módosítása, törlése és kezelése](manage.md)
- [Felügyeleti csoportok áttekintése az Azure PowerShell Erőforrások moduljában](/powershell/module/az.resources#resources)
- [Felügyeleti csoportok áttekintése a REST API-ban](/rest/api/resources/managementgroups)
- [Felügyeleti csoportok áttekintése az Azure CLI-ben](/cli/azure/account/management-group)