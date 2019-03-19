---
title: Azure-beli virtuális gépek másodlagos Azure-régióba történő vészhelyreállításának beállítása az Azure Site Recovery használatával
description: Megismerheti, hogyan állíthatja be az Azure-beli virtuális gépek egy másik Azure-régióba történő vészhelyreállítását az Azure Site Recovery szolgáltatással.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: ff18a14b314b5757629205f4bf0eb134411688ec
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57853114"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Vészhelyreállítás beállítása az Azure virtuális gépek

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás hozzájárul a vész-helyreállítási stratégiát kezelésével és irányításával replikálása, feladatátvétele és feladat-visszavételhez helyszíni gépek és Azure-beli virtuális gépek (VM).

Az oktatóanyag bemutatja, hogyan állítható be a vész-helyreállítási Azure-beli virtuális gépek replikálásával őket egy Azure-régióból a másikba. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Recovery Services-tároló létrehozása
> * A célerőforrások beállításainak ellenőrzése.
> * Virtuális gépek kimenő hozzáférésének beállítása.
> * Virtuális gép replikálásának engedélyezése

> [!NOTE]
> Ez a cikk ismerteti, hogyan telepíthető a legegyszerűbben beállításokkal vész-helyreállítási. Ha szeretné testre szabott beállítások ismertetése, tekintse át a cikkek alapján a [útmutató szakaszban](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](concepts-azure-to-azure-architecture.md).
- Tekintse át a [memóriakonfigurációt](site-recovery-support-matrix-azure-to-azure.md) megkezdése előtt.

## <a name="create-a-vault"></a>Tároló létrehozása

A forrásrégió kivételével bármelyik régióban létrehozhat tárolót.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **Recovery Services** szolgáltatásba.
2. Kattintson a **erőforrás létrehozása** > **kezelőeszközök** > **Backup és Site Recovery**.
3. A **Név** mezőben adja meg a tárolót azonosító rövid nevet. Ha egynél több előfizetéssel rendelkezik, válassza ki ezek közül a megfelelőt.
4. Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy meglévőt. Válassza ki a kívánt Azure-régiót. A támogatott régiók megtekintéséhez olvassa el az [Azure Site Recovery – Díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/) című cikknek a földrajzi elérhetőséggel foglalkozó részét.
5. Ha gyors hozzáférést szeretne a tárolóhoz az irányítópultról, kattintson a **Rögzítés az irányítópulton**, majd a **Létrehozás** gombra.

   ![Új tároló](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   Az új tároló megjelenik az **Irányítópult** **Minden erőforrás** részében, illetve a központi **Recovery Services-tárolók** oldalon.

## <a name="verify-target-resources"></a>Célerőforrások ellenőrzése

1. Győződjön meg arról, hogy az Azure-előfizetés lehetővé teszi a virtuális gépeket hozhat létre a célrégióban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.
2. Ellenőrizze, hogy előfizetése rendelkezik-e elegendő erőforrás a forrás virtuális gépeknek megfelelő Virtuálisgép-méretek támogatják. A Site Recovery választja ki, az azonos méretű, vagy a legközelebbi lehetséges méretét, a cél virtuális gép.

## <a name="configure-outbound-network-connectivity"></a>Kimenő hálózati kapcsolat konfigurálása

Site Recovery számára a várt módon működik meg kell módosítania a replikálni kívánt virtuális gépek kimenő hálózati kapcsolat.

> [!NOTE]
> A Site Recovery nem támogatja hitelesítési proxy használatával hálózati kapcsolatának vezérlésére.


### <a name="outbound-connectivity-for-urls"></a>Kimenő kapcsolat URL-címek esetén

Kimenő kapcsolat szabályozásához URL-alapú tűzfalproxyt használ, ha az URL-címek elérését teszi lehetővé.

| **URL-cím** | **Részletek** |
| ------- | ----------- |
| *.blob.core.windows.net | Lehetővé teszi az adatok írását a virtuális gépről a forrásrégió gyorsítótárjának tárfiókjába. |
| login.microsoftonline.com | Hitelesítést és engedélyezést biztosít a Site Recovery szolgáltatás URL-címeihez. |
| *.hypervrecoverymanager.windowsazure.com | Lehetővé teszi a virtuális gép és a Site Recovery szolgáltatás közötti kommunikációt. |
| *.servicebus.windows.net | Lehetővé teszi a virtuális gép számára a Site Recovery monitorozási és diagnosztikai adatainak írását. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Kimenő kapcsolat az IP-címtartományokhoz

Ha szeretné szabályozni a kimenő kapcsolat URL-ek helyett IP-címeket használ, lehetővé teszi ezeket a címeket az IP-alapú tűzfalak, proxyk vagy NSG-szabályok.

  - [A Microsoft Azure adatközpont IP-tartományai](https://www.microsoft.com/download/details.aspx?id=41653)
  - [A Microsoft Azure adatközpont IP-tartományai Németországban](https://www.microsoft.com/download/details.aspx?id=54770)
  - [A Microsoft Azure adatközpont IP-tartományai Kínában](https://www.microsoft.com/download/details.aspx?id=42064)
  - [Office 365 URL-címek és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Site Recovery-szolgáltatásvégpontok IP-címei](https://aka.ms/site-recovery-public-ips)

Ezzel [parancsfájl](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702) hozhat létre a szükséges NSG-szabályokat.

## <a name="verify-azure-vm-certificates"></a>Azure-beli virtuális gép tanúsítványainak ellenőrzése

Ellenőrizze, hogy van-e a legújabb főtanúsítványok a replikálni kívánt virtuális gépeket. Ha nem a virtuális gép nem tudja regisztrálni a Site Recoveryben, biztonsági okokból.

- A Windows rendszerű virtuális gépek esetében az összes új Windows-frissítést telepíteni kell a virtuális gépen, így a megbízható főtanúsítványok mindegyike megtalálható lesz a számítógépen. Leválasztott környezet esetén hajtsa végre a vállalat szabványos Windows Update- és tanúsítványfrissítési folyamatait.
- Linux rendszerű virtuális gépek esetében kövesse a Linux-terjesztőtől származó útmutatást a legújabb megbízható főtanúsítványok és a visszavont tanúsítványok listájának lekéréséhez a virtuális gépen.

## <a name="set-permissions-on-the-account"></a>A fiók engedélyeinek beállítása

Az Azure Site Recovery három beépített szerepkört biztosít a Site Recovery felügyeleti műveleteinek szabályozásához.

- **Site Recovery-közreműködő** – Ez a szerepkör minden olyan engedéllyel rendelkezik, amelyek az Azure Site Recovery-műveletek Recovery Services-tárolókban történő kezeléséhez szükségesek. Az ezzel a szerepkörrel rendelkező felhasználók nem hozhatnak létre, illetve nem törölhetnek Recovery Services-tárolókat, és nem adhatnak hozzáférési jogokat más felhasználóknak. Ez a szerepkör az olyan vész-helyreállítási rendszergazdák számára a leginkább megfelelő, akik az összes alkalmazásra vagy az egész vállalatra vonatkozóan engedélyezhetik és felügyelhetik a vészhelyreállítást.

- **Site Recovery-operátor** – Ez a szerepkör a feladatátvétel és feladat-visszavétel végrehajtásához és felügyeletéhez szükséges engedélyekkel rendelkezik. Az ezzel a szerepkörrel rendelkező felhasználók nem engedélyezhetik vagy tilthatják le a replikációt, nem hozhatnak létre, illetve nem törölhetnek tárolókat, nem regisztrálhatnak új infrastruktúrát, és nem adhatnak hozzáférési jogokat más felhasználóknak. Ez a szerepkör az olyan vész-helyreállítási operátorok számára a leginkább megfelelő, akik le tudják bonyolítani a feladatátvételt a virtuális gépek vagy alkalmazások tekintetében, ha az alkalmazások tulajdonosaitól vagy a rendszergazdáktól erre vonatkozó utasítást kapnak. A vészhelyzet elhárítása után a vész-helyreállítási operátor gondoskodhat a virtuális gépek újbóli védelmének és feladat-visszavételének biztosításáról.

- **Site Recovery-olvasó** – Ez a szerepkör a Site Recovery összes felügyeleti műveletének a megtekintésére rendelkezik engedélyekkel. Ez a szerepkör olyan informatikai felügyeleti vezető számára a leginkább megfelelő, aki monitorozhatja az aktuális biztonság állapotát, és támogatási jegyeket hoz létre.

Tudjon meg többet [Azure RBAC beépített szerepkörök](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication"></a>A replikáció engedélyezése

### <a name="select-the-source"></a>Forrás kiválasztása

1. A Recovery Services-tárolók között kattintson a tároló nevére, majd a **+Replikálás** elemre.
2. A **Forrás** beállításnál válassza az **Azure** értéket.
3. A **Forrás helye** értékeként válassza ki azt az Azure-régiót, ahol az adott virtuális gépek éppen futnak.
4. Válassza ki azt a **Forrás-előfizetést**, ahol a virtuális gépek futnak. Ez bármelyik előfizetés lehet azon Azure Active Directory-bérlőn belül, ahol a már meglévő Recovery Services-tárolója van.
5. Válassza ki a **forrásként szolgáló erőforráscsoportot**, és kattintson a **OK** a beállítások mentéséhez.

    ![A forrás beállítása](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>A virtuális gépek kiválasztása

A Site Recovery lekéri az előfizetéshez és az erőforráscsoporthoz/felhőszolgáltatáshoz társított virtuális gépek listáját.

1. A **Virtuális gépek** menüben jelölje ki a replikálni kívánt virtuális gépeket.
2. Kattintson az **OK** gombra.

### <a name="configure-replication-settings"></a>Replikációs beállítások konfigurálása

A Site Recovery létrehozza a célrégióra vonatkozó alapértelmezett beállításokat és replikációs szabályzatot. A beállításokat, szükség szerint módosíthatja.

1. A célra és a replikációra vonatkozó beállítások megtekintéséhez kattintson a **Beállítások** elemre.
2. Az alapértelmezett célbeállítások felülbírálásához kattintson **Testreszabás** melletti **erőforráscsoport, hálózat, tárolás és rendelkezésre állási**.

   ![Beállítások konfigurálása](./media/azure-to-azure-tutorial-enable-replication/settings.png)


3. Céloldali beállítások testreszabása, a táblázat foglalja össze.

    **Beállítás** | **Részletek**
    --- | ---
    **Célként megadott előfizetés** | Alapértelmezés szerint a célként megadott előfizetés megegyezik a forrás-előfizetés. Ha egy másik előfizetést szeretne választani ugyanazon az Azure Active Directory-bérlőn belül, kattintson a „Testreszabásra” lehetőségre.
    **Célhely** | A vészhelyreállításhoz használt célrégió.<br/><br/> Javasoljuk, hogy a célhely és a Site Recovery-tároló helye ugyanaz legyen.
    **Céloldali erőforráscsoport** | Az erőforráscsoportot a célrégióban, amely tartalmazza az Azure virtuális gépek a feladatátvételt követően.<br/><br/> Alapértelmezés szerint a Site Recovery létrehoz egy „asr” utótaggal rendelkező új erőforráscsoportot a célrégióban. A céloldali erőforráscsoport helye lehet bármelyik régióban, kivéve a régiót, amelyben a forrás virtuális gépeken üzemelnek.
    **Cél virtuális hálózattal** | A hálózat a célrégióban, hogy virtuális gépek találhatók a feladatátvételt követően.<br/><br/> Alapértelmezés szerint a Site Recovery létrehoz egy „asr” utótaggal rendelkező új virtuális hálózatot (és alhálózatokat) a célrégióban.
    **Gyorsítótárfiókok** | A Site Recovery tárfiókot használ a forrásrégióban. A forrás virtuális gépekre vonatkozó módosítások ebbe a fiókba érkeznek a célhelyre történő replikáció előtt.<br/><br/> Ha a tűzfal engedélyezve gyorsítótárfiók használ, győződjön meg róla, hogy engedélyezze **engedélyezése a Microsoft-szolgáltatások megbízható**. [Részletek](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
    **Cél tárfiókok (a forrás virtuális gép nem felügyelt lemezeket használ)** | Alapértelmezés szerint a Site Recovery létrehoz egy új tárfiókot a célrégióban a forrás virtuális gép tárfiókjának tükrözéséhez.<br/><br/> Engedélyezése **engedélyezése a Microsoft-szolgáltatások megbízható** gyorsítótár tűzfal storage-fiók használata.
    **Replikált felügyelt lemezek (Ha a forrás virtuális gép használja a managed disks)** | Alapértelmezés szerint a Site Recovery replika felügyelt lemezeket a célrégióban a forrás virtuális gép felügyelt lemezeinek azokkal az azonos tártípusban (Standard vagy prémium) tükrözéséhez, mivel a forrásoldali virtuális gép felügyelt lemez hoz létre.
    **Cél rendelkezésre állási csoportok** | Alapértelmezés szerint az Azure Site Recovery létrehoz egy új rendelkezésre állási csoportot a célrégióban a virtuális gépek része egy rendelkezésre állási csoportot a forrásrégióban az "asr" utótaggal rendelkező nevű. Rendelkezésre állási csoport létrehozása az Azure Site Recovery már létezik, a rendszer újra.
    **Cél rendelkezésre állási zónák** | Alapértelmezés szerint a Site Recovery zóna száma azonos a célrégióban a forrás régiójára rendeli, ha a célrégióban támogatja a rendelkezésre állási zónák.<br/><br/> Ha a célrégió nem támogatja a rendelkezésre állási zónák, a cél virtuális gépek alapértelmezés szerint egyetlen példány van konfigurálva.<br/><br/> Kattintson a **Testreszabás** a virtuális gépek konfigurálásához, rendelkezésre állási csoportot a célrégióban részeként.<br/><br/> A rendelkezésre állási típusát (Egypéldányos, rendelkezésre állási csoport vagy a rendelkezésre állási zónában) nem módosíthatja a replikáció engedélyezése után. Tiltsa le majd engedélyezze a rendelkezésre állási típusának módosításához kell.

4. Replikációs házirend beállításainak testre szabhatja, kattintson a **Testreszabás** melletti **replikációs házirend**, és szükség szerint módosítsa a beállításokat.

    **Beállítás** | **Részletek**
    --- | ---
    **Replikációs házirend neve** | A házirend nevét.
    **Helyreállítási pont megőrzése** | Alapértelmezés szerint a Site Recovery 24 óráig tartja a helyreállítási pontok. 1 és 72 óra közötti értéket konfigurálhat.
    **Alkalmazáskonzisztens pillanatkép gyakorisága** | Alapértelmezés szerint a Site Recovery 4 óránként vesz igénybe egy alkalmazáskonzisztens pillanatképet. 1 és 12 óra közötti értéket konfigurálhat.<br/><br/> Az alkalmazáskonzisztens pillanatkép a virtuális Gépen található alkalmazásadatok időponthoz pillanatképet. A kötet árnyékmásolata szolgáltatás (VSS) biztosítja, hogy a virtuális gépen található alkalmazás konzisztens állapotban legyen a pillanatkép készítésekor.
    **Replikációs csoport** | Ha az alkalmazásnak több virtuális gépre kiterjedő konzisztencia virtuális gépen, létrehozhat egy replikációs csoportot a virtuális gépeken. Alapértelmezés szerint a kijelölt virtuális gépek nem részei semmilyen replikációs csoportnak sem.

5. A **Testreszabás**válassza **Igen** a virtuális gépre kiterjedő konzisztencia, ha azt szeretné, hogy virtuális gépeket ad hozzá egy új vagy meglévő replikációs csoporthoz. Ezután kattintson az **OK** gombra. 

    >[!NOTE]
    >- A replikációs csoport összes gép megosztott összeomlás-konzisztens és alkalmazáskonzisztens helyreállítási pontok feladatátvételkor.
    >- Több virtuális gépre kiterjedő konzisztencia engedélyezése hatással lehet a számítási feladatok teljesítményére (azt a CPU-igényes). Akkor kell használni, csak akkor, ha a gépek ugyanazt a számítási feladatot futtat, és több gép közötti konzisztenciára van szükség.
    >- Legfeljebb 16 virtuális gépek egy replikációs csoport rendelkezhet.
    >- Ha engedélyezte a több virtuális gépre kiterjedő konzisztenciát, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással. Győződjön meg arról, hogy nincs-e ezen a porton keresztül a virtuális gépek között a belső kommunikációt blokkoló tűzfal van.
    >- Linux rendszerű virtuális gépek egy replikációs csoportban található győződjön meg arról, megfelelően útmutatást a Linux-verziójának manuális megnyitása a 20004-es porton a kimenő forgalom.



### <a name="configure-encryption-settings"></a>Titkosítási beállítások konfigurálása

Ha a forrásoldali virtuális gép az Azure disk encryption (ADE) engedélyezve van, tekintse át a beállításokat.

1. Ellenőrizze a beállításokat:
    - **Lemez titkosítása kulcstartók**: Alapértelmezés szerint a Site Recovery a forrás virtuális gép lemez titkosítási kulcsokról, egy "asr" utótaggal rendelkező létrehoz egy új kulcstartóba. Ha már létezik a kulcstároló, újra.
    - **A kulcstartók titkosító kulcs**: Alapértelmezés szerint a Site Recovery létrehoz egy új kulcstartót a célrégióban. A név egy "asr" utótaggal rendelkezik, és a forrás virtuális gép fő titkosítási kulcsok alapján. Ha a key vault, a Site Recovery által létrehozott már létezik, akkor újra.

2. Kattintson a **Testreszabás** egyéni kulcstartók kiválasztásához.

>[!NOTE]
>Csak az Azure virtuális gépeken futó Windows operációs rendszerek és [engedélyezett a titkosítás az Azure AD-alkalmazás](https://aka.ms/ade-aad-app) Azure Site Recovery által jelenleg támogatott.
>

### <a name="track-replication-status"></a>A replikáció állapotának nyomon követése

1. Az aktuális állapot megtekintéséhez kattintson a **Beállítások** menü **Frissítés** elemére.
2. Előrehaladási és állapotmeghatározási nyomon a következőképpen:
    - Nyomon követheti a a **engedélyezni a védelmet** feladat látható **beállítások** > **feladatok** > **Site Recovery-feladatok**.
    - A **Beállítások** > **Replikált elemek** pontban tekintheti meg a virtuális gépek és a kezdeti replikáció folyamatának állapotát. A beállítások részleteinek megjelenítéséhez kattintson az adott virtuális gépre.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure-beli virtuális gép vészhelyreállítását konfigurálta. Most ellenőrizze, hogy a feladatátvétel a várt módon működik vészhelyreállítási próbát is kezdeményezhető.

> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](azure-to-azure-tutorial-dr-drill.md)
