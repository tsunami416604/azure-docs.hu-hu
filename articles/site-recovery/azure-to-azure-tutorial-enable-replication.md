---
title: Azure-beli virtuális gép vész-helyreállításának beállítása Azure Site Recovery
description: Megismerheti, hogyan állíthatja be az Azure-beli virtuális gépek egy másik Azure-régióba történő vészhelyreállítását az Azure Site Recovery szolgáltatással.
ms.topic: tutorial
ms.date: 1/24/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 979b390f65363b43f33ce2f09d26844c3cc1a2e8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79238915"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Vész-helyreállítás beállítása Azure-beli virtuális gépekhez

A [Azure site Recovery](site-recovery-overview.md) szolgáltatás a helyi gépek és az Azure-beli virtuális gépek (VM-EK) replikációjának, feladatátvételének és feladat-visszavételének kezelésével és koordinálásával járul hozzá a vész-helyreállítási stratégiához.

Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be az Azure-beli virtuális gépek vész-helyreállítását azáltal, hogy egy Azure-régióból egy másikba replikálja őket. Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Recovery Services-tároló létrehozása
> * A célerőforrások beállításainak ellenőrzése.
> * Kimenő hálózati kapcsolat beállítása virtuális gépekhez
> * Virtuális gép replikálásának engedélyezése

> [!NOTE]
> Ez a cikk a vész-helyreállítás a legegyszerűbb beállításokkal történő üzembe helyezésére vonatkozó utasításokat tartalmazza. Ha szeretne többet megtudni a testreszabott beállításokról, tekintse át a cikkek [című szakaszt](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Tekintse át [a forgatókönyv-architektúrát és -összetevőket](concepts-azure-to-azure-architecture.md).
- Mielőtt elkezdené, tekintse át a [támogatási követelményeket](site-recovery-support-matrix-azure-to-azure.md) .

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A forrásrégió kivételével bármelyik régióban létrehozhat tárolót.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. A Azure Portal menüben vagy a **Kezdőlap** lapon válassza az **erőforrás létrehozása**lehetőséget. Ezután válassza ki **& felügyeleti eszközöket** > **a biztonsági mentést és a site Recovery**.
1. A **Név** mezőben adja meg a tárolót azonosító rövid nevet. Ha egynél több előfizetéssel rendelkezik, válassza ki ezek közül a megfelelőt.
1. Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy meglévőt. Válassza ki a kívánt Azure-régiót. A támogatott régiók megtekintéséhez olvassa el az [Azure Site Recovery – Díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/) című cikknek a földrajzi elérhetőséggel foglalkozó részét.
1. Ha a tárolót az irányítópultról szeretné elérni, válassza a **rögzítés az irányítópulton** lehetőséget, majd válassza a **Létrehozás**lehetőséget.

   ![Új tároló](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

Az új tároló megjelenik az **Irányítópult** **Minden erőforrás** részében, illetve a központi **Recovery Services-tárolók** oldalon.

## <a name="verify-target-resource-settings"></a>A célerőforrások beállításainak ellenőrzése.

Keresse meg az Azure-előfizetését a megcélzott régióban.

- Ellenőrizze, hogy az Azure-előfizetése lehetővé teszi-e a virtuális gépek létrehozását a céltartományban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.
- Győződjön meg arról, hogy az előfizetése elegendő erőforrással rendelkezik a forrás virtuális gépeknek megfelelő virtuálisgép-méretek támogatásához. Site Recovery a célként megadott virtuális gép méretének vagy a legközelebbi lehetséges méretnek a megválasztása.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Kimenő hálózati kapcsolat beállítása virtuális gépekhez

Ahhoz, hogy a Site Recovery a várt módon működjön, módosítania kell a kimenő hálózati kapcsolatot a replikálni kívánt virtuális gépekről.

> [!NOTE]
> A Site Recovery nem támogatja a hitelesítési proxy használatát a hálózati kapcsolat vezérléséhez.

### <a name="outbound-connectivity-for-urls"></a>Kimenő kapcsolat URL-címek esetén

Ha URL-alapú tűzfal-proxyt használ a kimenő kapcsolatok vezérléséhez, engedélyezze az alábbi URL-címek elérését:

| **URL-cím** | **Részletek** |
| ------- | ----------- |
| `*.blob.core.windows.net` | Lehetővé teszi az adatok írását a virtuális gépről a forrásrégió gyorsítótárjának tárfiókjába. |
| `login.microsoftonline.com` | Hitelesítést és engedélyezést biztosít a Site Recovery szolgáltatás URL-címeihez. |
| `*.hypervrecoverymanager.windowsazure.com` | Lehetővé teszi a virtuális gép és a Site Recovery szolgáltatás közötti kommunikációt. |
| `*.servicebus.windows.net` | Lehetővé teszi a virtuális gép számára a Site Recovery monitorozási és diagnosztikai adatainak írását. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Kimenő kapcsolat az IP-címtartományokhoz

Ha hálózati biztonsági csoportot (NSG) használ, hozzon létre az Azure Storage-hoz, Azure Active Directoryhoz, Site Recovery szolgáltatáshoz és Site Recovery figyeléshez való hozzáféréshez használható, szolgáltatás-címkézési szabályokat. [További információk](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges).

## <a name="verify-azure-vm-certificates"></a>Azure-beli virtuális gép tanúsítványainak ellenőrzése

Győződjön meg arról, hogy a replikálni kívánt virtuális gépek rendelkeznek a legfelső szintű tanúsítványokkal. Ha nem, a virtuális gép nem regisztrálható Site Recovery biztonsági korlátozások miatt.

- A Windows rendszerű virtuális gépek esetében az összes új Windows-frissítést telepíteni kell a virtuális gépen, így a megbízható főtanúsítványok mindegyike megtalálható lesz a számítógépen. Leválasztott környezet esetén hajtsa végre a vállalat szabványos Windows Update- és tanúsítványfrissítési folyamatait.
- Linux rendszerű virtuális gépek esetében kövesse a Linux-terjesztőtől származó útmutatást a legújabb megbízható főtanúsítványok és a visszavont tanúsítványok listájának lekéréséhez a virtuális gépen.

## <a name="set-permissions-on-the-account"></a>A fiók engedélyeinek beállítása

Az Azure Site Recovery három beépített szerepkört biztosít a Site Recovery felügyeleti műveleteinek szabályozásához.

- **Site Recovery-közreműködő** – Ez a szerepkör minden olyan engedéllyel rendelkezik, amelyek az Azure Site Recovery-műveletek Recovery Services-tárolókban történő kezeléséhez szükségesek. Az ezzel a szerepkörrel rendelkező felhasználók nem hozhatnak létre, illetve nem törölhetnek Recovery Services-tárolókat, és nem adhatnak hozzáférési jogokat más felhasználóknak. Ez a szerepkör az olyan vész-helyreállítási rendszergazdák számára a leginkább megfelelő, akik az összes alkalmazásra vagy az egész vállalatra vonatkozóan engedélyezhetik és felügyelhetik a vészhelyreállítást.

- **Site Recovery-operátor** – Ez a szerepkör a feladatátvétel és feladat-visszavétel végrehajtásához és felügyeletéhez szükséges engedélyekkel rendelkezik. Az ezzel a szerepkörrel rendelkező felhasználók nem engedélyezhetik vagy tilthatják le a replikációt, nem hozhatnak létre, illetve nem törölhetnek tárolókat, nem regisztrálhatnak új infrastruktúrát, és nem adhatnak hozzáférési jogokat más felhasználóknak. Ez a szerepkör az olyan vész-helyreállítási operátorok számára a leginkább megfelelő, akik le tudják bonyolítani a feladatátvételt a virtuális gépek vagy alkalmazások tekintetében, ha az alkalmazások tulajdonosaitól vagy a rendszergazdáktól erre vonatkozó utasítást kapnak. A katasztrófa feloldása után a vész-helyreállítási operátor újra tudja védetté tenni a virtuális gépeket, és feladat-visszavételt hajt végre.

- **Site Recovery-olvasó** – Ez a szerepkör a Site Recovery összes felügyeleti műveletének a megtekintésére rendelkezik engedélyekkel. Ez a szerepkör olyan informatikai felügyeleti vezető számára a leginkább megfelelő, aki monitorozhatja az aktuális biztonság állapotát, és támogatási jegyeket hoz létre.

További információ az [Azure RBAC beépített szerepköreiről](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication-for-a-vm"></a>Virtuális gép replikálásának engedélyezése

A következő szakaszok ismertetik a replikáció engedélyezésének módját.

### <a name="select-the-source"></a>Forrás kiválasztása

A replikáció beállításának megkezdéséhez válassza ki azt a forrást, ahol az Azure-beli virtuális gépek futnak.

1. Lépjen **Recovery Services**tárolók elemre, válassza ki a tároló nevét, majd válassza a **+ replikálás**lehetőséget.
1. A **forrás**beállításnál válassza az **Azure**lehetőséget.
1. A **Forrás helye** értékeként válassza ki azt az Azure-régiót, ahol az adott virtuális gépek éppen futnak.
1. Válassza ki azt a **Forrás-előfizetést**, ahol a virtuális gépek futnak. Ez bármelyik előfizetés lehet azon Azure Active Directory-bérlőn belül, ahol a már meglévő Recovery Services-tárolója van.
1. Válassza ki a **forrás erőforráscsoportot**, majd kattintson az **OK** gombra a beállítások mentéséhez.

   ![A forrás beállítása](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>A virtuális gépek kiválasztása

A Site Recovery lekéri az előfizetéshez és az erőforráscsoporthoz/felhőszolgáltatáshoz társított virtuális gépek listáját.

1. A **Virtuális gépek** menüben jelölje ki a replikálni kívánt virtuális gépeket.
1. Kattintson az **OK** gombra.

### <a name="configure-replication-settings"></a>Replikációs beállítások konfigurálása

A Site Recovery létrehozza a célrégióra vonatkozó alapértelmezett beállításokat és replikációs szabályzatot. Ezeket a beállításokat igény szerint módosíthatja.

1. A cél és a replikálás beállításainak megtekintéséhez válassza a **Beállítások** lehetőséget.

1. Az alapértelmezett cél beállításainak felülbírálásához válassza az **erőforráscsoport, a hálózat, a tárterület és a rendelkezésre állás**melletti **Testreszabás** lehetőséget.

   ![Beállítások konfigurálása](./media/azure-to-azure-tutorial-enable-replication/settings.png)

1. Szabja testre a célhely beállításait a táblázatban foglaltak szerint.

   | **Beállítás** | **Részletek** |
   | --- | --- |
   | **Cél-előfizetés** | Alapértelmezés szerint a cél-előfizetés megegyezik a forrás-előfizetéssel. Válassza a **Testreszabás** lehetőséget, ha egy másik cél előfizetést szeretne kiválasztani ugyanazon a Azure Active Directory bérlőn belül. |
   | **Célhely** | A vész-helyreállításhoz használt cél régió.<br/><br/> Javasoljuk, hogy a célhely és a Site Recovery-tároló helye ugyanaz legyen. |
   | **Cél erőforráscsoport** | Az Azure-beli virtuális gépeket a feladatátvételt követően tároló erőforráscsoport.<br/><br/> Alapértelmezés szerint a Site Recovery egy `asr` utótaggal létrehoz egy új erőforráscsoportot a célként megadott régióban. A cél erőforráscsoport helye bármely régió lehet, kivéve azt a régiót, amelyben a forrásként szolgáló virtuális gépek futnak. |
   | **Célként megadott virtuális hálózat** | A célként megadott régióban található, a virtuális gépeket a feladatátvételt követően tároló hálózat.<br/><br/> Alapértelmezés szerint a Site Recovery egy új virtuális hálózatot (és alhálózatokat) hoz létre a célként megadott régióban egy `asr` utótaggal. |
   | **Gyorsítótár-tárolási fiókok** | Site Recovery egy Storage-fiókot használ a forrás régióban. A forrás virtuális gépekre vonatkozó módosítások ebbe a fiókba érkeznek a célhelyre történő replikáció előtt.<br/><br/> Ha tűzfallal védett gyorsítótáras Storage-fiókot használ, győződjön meg arról, hogy engedélyezi a **megbízható Microsoft-szolgáltatások engedélyezését**. [További információk](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions). Győződjön meg arról is, hogy engedélyezi a hozzáférést a forrás vnet legalább egy alhálózatához. |
   | **Cél Storage-fiókok (a forrásoldali virtuális gép nem felügyelt lemezeket használ)** | Alapértelmezés szerint a Site Recovery új Storage-fiókot hoz létre a célként megadott régióban a forrásként szolgáló virtuális gép tárolási fiókjának tükrözéséhez.<br/><br/> Engedélyezze a **megbízható Microsoft-szolgáltatások engedélyezését** , ha tűzfallal védett gyorsítótáras Storage-fiókot használ. |
   | **Replika által felügyelt lemezek (ha a forrás virtuális gép felügyelt lemezeket használ)** | Alapértelmezés szerint a Site Recovery replika felügyelt lemezeket hoz létre a célhelyen a forrásként szolgáló virtuális gép felügyelt lemezeit ugyanazzal a tárolási típussal (standard vagy prémium), mint a forrás virtuális gép felügyelt lemezével. Csak a lemez típusát lehet testreszabni. |
   | **Cél rendelkezésre állási csoportok** | Alapértelmezés szerint a Azure Site Recovery egy új rendelkezésre állási készletet hoz létre a célként megadott régióban, amelynek a neve `asr` utótaggal rendelkezik a rendelkezésre állási csoportnak a forrás régiójában. Ha a Azure Site Recovery által létrehozott rendelkezésre állási csoport már létezik, az újra használatban van. |
   | **Cél rendelkezésre állási zónák** | Alapértelmezés szerint a Site Recovery ugyanazt a zónát rendeli hozzá, mint a célként megadott régióban lévő forrástartomány, ha a cél régió támogatja a rendelkezésre állási zónákat.<br/><br/> Ha a célként megadott régió nem támogatja a rendelkezésre állási zónák használatát, a célként megadott virtuális gépek alapértelmezés szerint önálló példányként vannak konfigurálva.<br/><br/> Válassza a **Testreszabás** lehetőséget, hogy a virtuális gépeket egy rendelkezésre állási csoport részeként konfigurálja a célként megadott régióban.<br/><br/> A replikáció engedélyezése után nem módosíthatja a rendelkezésre állási típust (egy példány, rendelkezésre állási csoport vagy rendelkezésre állási zóna). A rendelkezésre állási típus módosításához tiltsa le és engedélyezze a replikálást. |

1. A replikációs házirend beállításainak testreszabásához válassza a **replikációs házirend**melletti **Testreszabás** lehetőséget, és szükség szerint módosítsa a beállításokat.

   | **Beállítás** | **Részletek** |
   | --- | --- |
   | **Replikációs házirend neve** | A szabályzat neve. |
   | **Helyreállítási pont megőrzése** | Alapértelmezés szerint a Site Recovery 24 órán keresztül tartja a helyreállítási pontokat. 1 és 72 óra közötti értéket konfigurálhat. |
   | **Alkalmazás-konzisztens pillanatkép gyakorisága** | Alapértelmezés szerint a Site Recovery 4 óránként végrehajtja az alkalmazás-konzisztens pillanatképet. 1 és 12 óra közötti értéket konfigurálhat.<br/><br/> Az alkalmazás-konzisztens pillanatkép a virtuális gépen belüli alkalmazásadatok időponthoz tartozó pillanatképe. A kötet árnyékmásolata szolgáltatás (VSS) biztosítja, hogy a virtuális gépen található alkalmazás konzisztens állapotban legyen a pillanatkép készítésekor. |
   | **Replikációs csoport** | Ha az alkalmazás több virtuális gépre kiterjedő konzisztenciát igényel a virtuális gépek között, létrehozhat egy replikációs csoportot ezekhez a virtuális gépekhez. Alapértelmezés szerint a kijelölt virtuális gépek nem részei semmilyen replikációs csoportnak sem. |

1. A **Testreszabás**területen válassza az **Igen** lehetőséget a több virtuális gépre kiterjedő konzisztencia esetén, ha új vagy meglévő replikációs csoportba kívánja hozzáadni a virtuális gépeket. Ezután kattintson az **OK** gombra.

   > [!NOTE]
   > - A replikálási csoportban lévő összes gép megosztott összeomlási konzisztens és alkalmazás-konzisztens helyreállítási pontokat tartalmaz a feladatátvétel során.
   > - A több virtuális gépre kiterjedő konzisztencia engedélyezése hatással lehet a munkaterhelés teljesítményére (ez a CPU-igényes). Csak akkor használható, ha a gépek ugyanazt a számítási feladatot futtatják, és több gépen is konzisztencia szükséges.
   > - Egy replikációs csoportban legfeljebb 16 virtuális gép lehet.
   > - Ha engedélyezte a több virtuális gépre kiterjedő konzisztenciát, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással. Győződjön meg arról, hogy nincs tűzfal blokkolja a virtuális gépek közötti belső kommunikációt a porton keresztül.
   > - Egy replikációs csoportban lévő Linux rendszerű virtuális gépek esetén győződjön meg arról, hogy az 20004-as porton a kimenő forgalom manuálisan nyílik meg a Linux-verzióra vonatkozó útmutatásnak megfelelően.

### <a name="configure-encryption-settings"></a>Titkosítási beállítások konfigurálása

Ha a forrásoldali virtuális gépnek engedélyezve van az Azure Disk Encryption (ADE), tekintse át a beállításokat.

1. Ellenőrizze a beállításokat:
   1. **Lemezes titkosítási kulcstartók**: alapértelmezés szerint a site Recovery létrehoz egy új kulcstárolót a forrásként szolgáló virtuális gép lemezének titkosítási kulcsain egy `asr` utótaggal. Ha a kulcstartó már létezik, a rendszer újra felhasználja.
   1. **Key encryption Key vaultok**: alapértelmezés szerint a site Recovery egy új kulcstartót hoz létre a céltartományban. A névnek `asr` utótagja van, és a forrás virtuálisgép-kulcs titkosítási kulcsain alapul. Ha a Site Recovery által létrehozott Key Vault már létezik, a rendszer újra felhasználja.
1. Válassza a **Testreszabás** lehetőséget az egyéni kulcstartók kiválasztásához.

> [!NOTE]
> Azure Site Recovery jelenleg csak a Windows operációs rendszert futtató és [Az Azure ad-alkalmazással való titkosításra engedélyezett](https://aka.ms/ade-aad-app) Azure-beli virtuális gépeket támogatja.

### <a name="track-replication-status"></a>A replikáció állapotának nyomon követése

A replikáció engedélyezése után nyomon követheti a feladatok állapotát.

1. A **Beállítások**területen válassza a **frissítés** lehetőséget a legutóbbi állapot lekéréséhez.
1. Nyomon követheti a folyamat előrehaladását és állapotát a következőképpen:
   1. A **védelem engedélyezése** feladat előrehaladásának nyomon követése a **beállítások** > **feladatok** > **site Recovery feladatokban**.
   1. A **Beállítások** > **Replikált elemek** pontban tekintheti meg a virtuális gépek és a kezdeti replikáció folyamatának állapotát. Válassza ki a virtuális gépet a beállítások részletezéséhez.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure-beli virtuális gép vészhelyreállítását konfigurálta. Mostantól a vész-helyreállítási gyakorlat futtatásával ellenőrizhető, hogy a feladatátvétel a várt módon működik-e.

> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](azure-to-azure-tutorial-dr-drill.md)
