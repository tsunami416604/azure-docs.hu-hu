---
title: Az Azure VM vész-helyreállítási szolgáltatásának beállítása az Azure Site Recovery szolgáltatással
description: Megismerheti, hogyan állíthatja be az Azure-beli virtuális gépek egy másik Azure-régióba történő vészhelyreállítását az Azure Site Recovery szolgáltatással.
ms.topic: tutorial
ms.date: 1/24/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c5d2bbe920f87421550fadf30a7e7e9d23931bfd
ms.sourcegitcommit: fab450a18a600d72b583ecfbe6c5e53afd43408c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80292484"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Vészhelyreállítás beállítása az Azure-beli virtuális gépekhez

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás hozzájárul a vész-helyreállítási stratégia kezeléséhez és vezényléséhez a replikáció, a feladatátvétel és a feladat-visszavétel a helyszíni gépek és az Azure virtuális gépek (Virtuális gépek).

Ez az oktatóanyag bemutatja, hogyan állíthatja be az Azure-beli virtuális gépek vész-helyreállítási szolgáltatását az egyik Azure-régióból a másikra történő replikálásával. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Recovery Services-tároló létrehozása
> * A célerőforrások beállításainak ellenőrzése.
> * Kimenő hálózati kapcsolat beállítása a virtuális gépekhez
> * Virtuális gép replikálásának engedélyezése

> [!NOTE]
> Ez a cikk a legegyszerűbb beállításokkal ismerteti a vészhelyreállítás üzembe helyezését. Ha szeretne többet megtudni a testreszabott beállításokról, tekintse át a [Cikkeket](azure-to-azure-how-to-enable-replication.md)a Hogyan szakaszban.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Tekintse át [a forgatókönyv-architektúrát és -összetevőket](concepts-azure-to-azure-architecture.md).
- A kezdés előtt tekintse át a [támogatási követelményeket.](site-recovery-support-matrix-azure-to-azure.md)

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A forrásrégió kivételével bármelyik régióban létrehozhat tárolót.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet. Ezután válassza **az INFORMATIKAI & felügyeleti eszközök** > **biztonsági mentését és a helyhelyreállítása t**.
1. A **Név** mezőben adja meg a tárolót azonosító rövid nevet. Ha egynél több előfizetéssel rendelkezik, válassza ki ezek közül a megfelelőt.
1. Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy meglévőt. Válassza ki a kívánt Azure-régiót. A támogatott régiók ellenőrzéséhez tekintse meg a földrajzi elérhetőséget az [Azure Site Recovery díjszabási részletei ben.](https://azure.microsoft.com/pricing/details/site-recovery/)
1. Ha az irányítópultról szeretné elérni a tárolót, válassza a **Rögzítés az irányítópultra** lehetőséget, majd a **Létrehozás lehetőséget.**

   ![Új tároló](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

Az új tároló megjelenik az **Irányítópult****Minden erőforrás** részében, illetve a központi **Recovery Services-tárolók** oldalon.

## <a name="verify-target-resource-settings"></a>A célerőforrások beállításainak ellenőrzése.

Ellenőrizze az Azure-előfizetést a célrégióhoz.

- Ellenőrizze, hogy az Azure-előfizetés lehetővé teszi-e, hogy virtuális gépeket hozzon létre a célrégióban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.
- Győződjön meg arról, hogy az előfizetés elegendő erőforrással rendelkezik a virtuális gép méretéhez, amelyek megfelelnek a forrás virtuális gépek. Site Recovery kiválasztja az azonos méretű, vagy a legközelebbi lehetséges méretű, a cél virtuális gép.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Kimenő hálózati kapcsolat beállítása a virtuális gépekhez

Ahhoz, hogy a Site Recovery a várt módon működjön, módosítania kell a replikálni kívánt virtuális gépek kimenő hálózati kapcsolatát.

> [!NOTE]
> A Site Recovery nem támogatja a hitelesítési proxy használatát a hálózati kapcsolat vezérlésére.

### <a name="outbound-connectivity-for-urls"></a>Kimenő kapcsolat URL-címek esetén

Ha URL-alapú tűzfalproxyt használ a kimenő kapcsolatok szabályozására, engedélyezze a hozzáférést ezekhez az URL-címekhez:

| **Url** | **Részletek** |
| ------- | ----------- |
| `*.blob.core.windows.net` | Lehetővé teszi az adatok írását a virtuális gépről a forrásrégió gyorsítótárjának tárfiókjába. |
| `login.microsoftonline.com` | Hitelesítést és engedélyezést biztosít a Site Recovery szolgáltatás URL-címeihez. |
| `*.hypervrecoverymanager.windowsazure.com` | Lehetővé teszi a virtuális gép és a Site Recovery szolgáltatás közötti kommunikációt. |
| `*.servicebus.windows.net` | Lehetővé teszi a virtuális gép számára a Site Recovery monitorozási és diagnosztikai adatainak írását. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Kimenő kapcsolat az IP-címtartományokhoz

Ha hálózati biztonsági csoportot (NSG) használ, hozzon létre szolgáltatáscímke-alapú NSG-szabályokat az Azure Storage, az Azure Active Directory, a Site Recovery szolgáltatás és a Site Recovery figyeléséhez való hozzáféréshez. [További információ](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags).

## <a name="verify-azure-vm-certificates"></a>Azure-beli virtuális gép tanúsítványainak ellenőrzése

Ellenőrizze, hogy a replikálni kívánt virtuális gépek rendelkeznek-e a legújabb főtanúsítványokkal. Ha nem, a virtuális gép nem regisztrálható a Site Recovery biztonsági korlátozások miatt.

- A Windows rendszerű virtuális gépek esetében az összes új Windows-frissítést telepíteni kell a virtuális gépen, így a megbízható főtanúsítványok mindegyike megtalálható lesz a számítógépen. Leválasztott környezet esetén hajtsa végre a vállalat szabványos Windows Update- és tanúsítványfrissítési folyamatait.
- Linux rendszerű virtuális gépek esetében kövesse a Linux-terjesztőtől származó útmutatást a legújabb megbízható főtanúsítványok és a visszavont tanúsítványok listájának lekéréséhez a virtuális gépen.

## <a name="set-permissions-on-the-account"></a>A fiók engedélyeinek beállítása

Az Azure Site Recovery három beépített szerepkört biztosít a Site Recovery felügyeleti műveleteinek szabályozásához.

- **Site Recovery-közreműködő** – Ez a szerepkör minden olyan engedéllyel rendelkezik, amelyek az Azure Site Recovery-műveletek Recovery Services-tárolókban történő kezeléséhez szükségesek. Az ezzel a szerepkörrel rendelkező felhasználók nem hozhatnak létre, illetve nem törölhetnek Recovery Services-tárolókat, és nem adhatnak hozzáférési jogokat más felhasználóknak. Ez a szerepkör az olyan vész-helyreállítási rendszergazdák számára a leginkább megfelelő, akik az összes alkalmazásra vagy az egész vállalatra vonatkozóan engedélyezhetik és felügyelhetik a vészhelyreállítást.

- **Site Recovery-operátor** – Ez a szerepkör a feladatátvétel és feladat-visszavétel végrehajtásához és felügyeletéhez szükséges engedélyekkel rendelkezik. Az ezzel a szerepkörrel rendelkező felhasználók nem engedélyezhetik vagy tilthatják le a replikációt, nem hozhatnak létre, illetve nem törölhetnek tárolókat, nem regisztrálhatnak új infrastruktúrát, és nem adhatnak hozzáférési jogokat más felhasználóknak. Ez a szerepkör az olyan vész-helyreállítási operátorok számára a leginkább megfelelő, akik le tudják bonyolítani a feladatátvételt a virtuális gépek vagy alkalmazások tekintetében, ha az alkalmazások tulajdonosaitól vagy a rendszergazdáktól erre vonatkozó utasítást kapnak. A katasztrófa utáni megoldás, a vész-helyreállítási operátor újra megvédheti és feladat-visszavétela a virtuális gépek.

- **Site Recovery-olvasó** – Ez a szerepkör a Site Recovery összes felügyeleti műveletének a megtekintésére rendelkezik engedélyekkel. Ez a szerepkör olyan informatikai felügyeleti vezető számára a leginkább megfelelő, aki monitorozhatja az aktuális biztonság állapotát, és támogatási jegyeket hoz létre.

További információ az [Azure RBAC beépített szerepköreiről.](../role-based-access-control/built-in-roles.md)

## <a name="enable-replication-for-a-vm"></a>Virtuális gép replikálásának engedélyezése

A következő szakaszok a replikáció engedélyezésének módját ismertetik.

### <a name="select-the-source"></a>Forrás kiválasztása

A replikáció beállítása, válassza ki a forrást, ahol az Azure virtuális gépek futnak.

1. Nyissa meg a **Recovery Services-tárolókat,** válassza ki a tároló nevét, majd válassza a **+Replicate**lehetőséget.
1. A **forrás**hoz válassza az **Azure**lehetőséget.
1. A **Forrás helye** értékeként válassza ki azt az Azure-régiót, ahol az adott virtuális gépek éppen futnak.
1. Válassza ki azt a **Forrás-előfizetést**, ahol a virtuális gépek futnak. Ez bármelyik előfizetés lehet azon Azure Active Directory-bérlőn belül, ahol a már meglévő Recovery Services-tárolója van.
1. Válassza a **Forrás erőforráscsoportot**, majd a beállítások mentéséhez **kattintson** az OK gombra.

   ![A forrás beállítása](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>A virtuális gépek kiválasztása

A Site Recovery lekéri az előfizetéshez és az erőforráscsoporthoz/felhőszolgáltatáshoz társított virtuális gépek listáját.

1. A **Virtuális gépek** menüben jelölje ki a replikálni kívánt virtuális gépeket.
1. Válassza **az OK gombot.**

### <a name="configure-replication-settings"></a>Replikációs beállítások konfigurálása

A Site Recovery létrehozza a célrégióra vonatkozó alapértelmezett beállításokat és replikációs szabályzatot. Ezeket a beállításokat szükség szerint módosíthatja.

1. Válassza a **Beállítások** lehetőséget a cél- és replikációs beállítások megtekintéséhez.

1. Az alapértelmezett célbeállítások felülbírálásához válassza a **Testreszabás** lehetőséget az **Erőforráscsoport, a Hálózat, a Tárolás és a Rendelkezésre állás**csoport mellett.

   ![Beállítások konfigurálása](./media/azure-to-azure-tutorial-enable-replication/settings.png)

1. A célbeállítások testreszabása a táblázatban összegzett módon.

   | **Beállítás** | **Részletek** |
   | --- | --- |
   | **Cél-előfizetés** | Alapértelmezés szerint a cél-előfizetés megegyezik a forrás-előfizetés. Válassza **a Testreszabás** lehetőséget, ha ugyanabban az Azure Active Directory-bérlőben másik cél-előfizetést szeretne választani. |
   | **Célhely** | A vész-helyreállítási célterület.<br/><br/> Javasoljuk, hogy a célhely és a Site Recovery-tároló helye ugyanaz legyen. |
   | **Cél erőforráscsoport** | Az erőforráscsoport a célrégióban, amely az Azure-beli virtuális gépek feladatátvétel után.<br/><br/> Alapértelmezés szerint a Site Recovery egy új erőforráscsoportot `asr` hoz létre a célrégióban utótaggal. A célerőforrás-csoport helye bármely régió lehet, kivéve azt a régiót, ahol a forrás virtuális gépek vannak tárolva. |
   | **Virtuális hálózat megcélzása** | A virtuális gépek által a feladatátvétel után található hálózat.<br/><br/> Alapértelmezés szerint a Site Recovery egy új virtuális hálózatot (és `asr` alhálózatokat) hoz létre a célrégióban utótaggal. |
   | **Tárfiókok gyorsítótárazása** | A Site Recovery egy tárfiókot használ a forrásrégióban. A forrás virtuális gépekre vonatkozó módosítások ebbe a fiókba érkeznek a célhelyre történő replikáció előtt.<br/><br/> Ha tűzfallal kompatibilis gyorsítótár-tárolófiókot használ, győződjön meg arról, hogy engedélyezi **a Megbízható Microsoft-szolgáltatások engedélyezése szolgáltatást.** [További információ](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions). Győződjön meg arról is, hogy engedélyezi a hozzáférést a forrás virtuális hálózat legalább egy alhálózatához. |
   | **Céltárfiókok (a forrásvirtuális gép nem felügyelt lemezeket használ)** | Alapértelmezés szerint a Site Recovery létrehoz egy új tárfiókot a célrégióban a forrás virtuálisgép-tárfiók tükrözéséhez.<br/><br/> **Engedélyezze a Megbízható Microsoft-szolgáltatások engedélyezése,** ha tűzfal-kompatibilis gyorsítótár-tárolófiókot használ. |
   | **Replika kezelt lemezek (Ha a forrás virtuális gép felügyelt lemezeket használ)** | Alapértelmezés szerint a Site Recovery replikáni kezelt lemezeket hoz létre a célrégióban, hogy tükrözze a forrás virtuális gép felügyelt lemezeit a forrás virtuális gép felügyelt lemezével azonos tárolótípussal (standard vagy prémium szintű). A Lemez típusát csak testreszabhatja. |
   | **Cél elérhetőségi csoportjai** | Alapértelmezés szerint az Azure Site Recovery létrehoz egy új `asr` rendelkezésre állási készlet a célrégióban névvel utótag a virtuális gépek része egy rendelkezésre állási csoport a forrásrégióban. Abban az esetben, ha az Azure Site Recovery által létrehozott rendelkezésre állási készlet már létezik, újra felhasználja. |
   | **Célelérhetőségi zónák** | Alapértelmezés szerint a Site Recovery ugyanazt a zónaszámot rendeli hozzá, mint a célrégió forrásrégiója, ha a célrégió támogatja a rendelkezésre állási zónákat.<br/><br/> Ha a célrégió nem támogatja a rendelkezésre állási zónákat, a célvirtuális gépek alapértelmezés szerint egyetlen példányként vannak konfigurálva.<br/><br/> Válassza **a Testreszabás lehetőséget** a virtuális gépek konfigurálásához a célrégióban lévő rendelkezésre állási csoport részeként.<br/><br/> A replikáció engedélyezése után nem módosíthatja a rendelkezésre állás típusát (egyetlen példány, rendelkezésre állási csoport vagy rendelkezésre állási zóna). A rendelkezésre állás típusának módosításához tiltsa le és engedélyezze a replikációt. |

1. A replikációs házirend beállításainak testreszabásához **válassza** a Testreszabás lehetőséget a **Replikációs házirend**elem mellett, és szükség szerint módosítsa a beállításokat.

   | **Beállítás** | **Részletek** |
   | --- | --- |
   | **Replikációs házirend neve** | Házirend neve. |
   | **Helyreállítási pont megőrzése** | Alapértelmezés szerint a Site Recovery 24 órán keresztül megőrzi a helyreállítási pontokat. 1 és 72 óra közötti értéket konfigurálhat. |
   | **Alkalmazáskonzisztens pillanatfelvétel gyakorisága** | Alapértelmezés szerint a Site Recovery 4 óránként vesz igénybe egy alkalmazáskonzisztens pillanatképet. 1 és 12 óra közötti értéket konfigurálhat.<br/><br/> Az alkalmazáskonzisztens pillanatkép a virtuális gépen belüli alkalmazásadatok időbeli pillanatképe. A kötet árnyékmásolata szolgáltatás (VSS) biztosítja, hogy a virtuális gépen található alkalmazás konzisztens állapotban legyen a pillanatkép készítésekor. |
   | **Replikációs csoport** | Ha az alkalmazásnak több virtuális gép konzisztenciára van szüksége a virtuális gépek között, létrehozhat egy replikációs csoportot a virtuális gépekhez. Alapértelmezés szerint a kijelölt virtuális gépek nem részei semmilyen replikációs csoportnak sem. |

1. A **Testreszabás csoportban**válassza az **Igen** lehetőséget a többvirtuális gép konzisztenciájához, ha virtuális gépeket szeretne hozzáadni egy új vagy meglévő replikációs csoporthoz. Ezután kattintson az **OK** gombra.

   > [!NOTE]
   > - A replikációs csoport összes gépe megosztott összeomláskonzisztens és alkalmazáskonzisztens helyreállítási pontokkal rendelkezik, ha feladatátvételt kap.
   > - A több virtuális gép konzisztenciájának engedélyezése hatással lehet a számítási feladatok teljesítményére (cpu-igényes). Csak akkor használható, ha a gépek ugyanazt a számítási feladatot futtatják, és több gép közötti konzisztenciára van szükség.
   > - Egy replikációs csoportban legfeljebb 16 virtuális gép lehet.
   > - Ha engedélyezte a több virtuális gépre kiterjedő konzisztenciát, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással. Győződjön meg arról, hogy nincs tűzfal blokkolja a belső kommunikációt a virtuális gépek között ezen a porton keresztül.
   > - A replikációs csoportban lévő Linux virtuális gépek esetében győződjön meg arról, hogy a 20004-es porton a kimenő forgalmat manuálisan nyitja meg a Linux-verzióra vonatkozó útmutatásnak megfelelően.

### <a name="configure-encryption-settings"></a>Titkosítási beállítások konfigurálása

Ha a forrás virtuális gép rendelkezik Azure lemeztitkosítás (ADE) engedélyezve van, tekintse át a beállításokat.

1. Ellenőrizze a beállításokat:
   1. **Lemeztitkosítási kulcstárolók**: Alapértelmezés szerint a Site Recovery új kulcstartót hoz `asr` létre a forrás virtuálisgép lemeztitkosítási kulcsain, utótaggal. Ha a kulcstartó már létezik, újra fel használja.
   1. **Kulcstitkosítási kulcstartók:** Alapértelmezés szerint a Site Recovery új kulcstartót hoz létre a célrégióban. A név `asr` utótaggal rendelkezik, és a forrás virtuálisgép kulcsának titkosítási kulcsain alapul. Ha a Site Recovery által létrehozott key vault már létezik, a program újra felhasználja.
1. Egyéni kulcstartók kijelöléséhez válassza a **Testreszabás** lehetőséget.

> [!NOTE]
> Az Azure Site Recovery jelenleg csak a Windows operációs rendszereket futtató és [az Azure AD alkalmazással titkosításra engedélyezett](https://aka.ms/ade-aad-app) Azure virtuális gépeket támogatja.

### <a name="track-replication-status"></a>A replikáció állapotának nyomon követése

A replikáció engedélyezése után nyomon követheti a feladat állapotát.

1. A **Beállítások csoportban**válassza a **Frissítés** lehetőséget a legfrissebb állapot leminősítéséhez.
1. Az előrehaladás és az állapot nyomon követése az alábbiak szerint:
   1. A Védelmi feladat **engedélyezése** a **Beállítások** > **feladatok** > **hely-helyreállítási feladatok ban.**
   1. A **Replikált** > **elemek beállításokban**megtekintheti a virtuális gépek állapotát és a kezdeti replikációelőrehaladását. Válassza ki a virtuális gépet a beállításokba való részletezéshez.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure-beli virtuális gép vészhelyreállítását konfigurálta. Most futtathat egy vész-helyreállítási gyakorlatot annak ellenőrzésére, hogy a feladatátvétel a várt módon működik-e.

> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](azure-to-azure-tutorial-dr-drill.md)
