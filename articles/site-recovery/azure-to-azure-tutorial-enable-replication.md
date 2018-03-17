---
title: "Azure-beli virtuális gépek másodlagos Azure-régióba történő vészhelyreállításának beállítása az Azure Site Recovery használatával (előzetes verzió)"
description: "Megismerheti, hogyan állíthatja be az Azure-beli virtuális gépek egy másik Azure-régióba történő vészhelyreállítását az Azure Site Recovery szolgáltatással."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/16/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 47d9a1e8aecde8ba0f01034f1d172c3fbd87ccfe
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region-preview"></a>Azure-beli virtuális gépek másodlagos Azure-régióba történő vészhelyreállításának beállítása (előzetes verzió)

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás a helyszíni számítógépek és az Azure-beli virtuális gépek replikálásának, feladatátvételének és feladat-visszavételének kezelésével és irányításával járul hozzá a vészhelyreállítási stratégia megvalósításához.

Az oktatóanyag bemutatja, hogyan állíthat be vészhelyreállítást egy másodlagos Azure-régióba az Azure-beli virtuális gépek számára. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Recovery Services-tároló létrehozása
> * A célerőforrások beállításainak ellenőrzése.
> * Virtuális gépek kimenő hozzáférésének beállítása.
> * Virtuális gép replikálásának engedélyezése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](concepts-azure-to-azure-architecture.md).
- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](site-recovery-support-matrix-azure-to-azure.md).

## <a name="create-a-vault"></a>Tároló létrehozása

A forrásrégió kivételével bármelyik régióban létrehozhat tárolót.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **Recovery Services** szolgáltatásba.
2. Kattintson az **Erőforrás létrehozása** > **Figyelés + felügyelet** > **Backup és Site Recovery** lehetőségre.
3. A **Név** mezőben adja meg a tárolót azonosító rövid nevet. Ha egynél több előfizetéssel rendelkezik, válassza ki ezek közül a megfelelőt.
4. Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy meglévőt. Válassza ki a kívánt Azure-régiót. A támogatott régiók megtekintéséhez olvassa el az [Azure Site Recovery – Díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/) című cikknek a földrajzi elérhetőséggel foglalkozó részét.
5. Ha gyors hozzáférést szeretne a tárolóhoz az irányítópultról, kattintson a **Rögzítés az irányítópulton**, majd a **Létrehozás** gombra.

   ![Új tároló](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   Az új tároló megjelenik az **Irányítópult** **Minden erőforrás** részében, illetve a központi **Recovery Services-tárolók** oldalon.

## <a name="verify-target-resources"></a>Célerőforrások ellenőrzése

1. Győződjön meg arról, hogy Azure-előfizetése lehetővé teszi-e virtuális gépek létrehozását a vészhelyreállításhoz használni kívánt célrégióban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

2. Ellenőrizze, hogy előfizetése rendelkezik-e a megfelelő erőforrásokkal a forrásként szolgáló virtuális gépeknek megfelelő méretű virtuális gépek támogatásához. A cél virtuális gép esetében a Site Recovery a méretben legközelebb állót fogja választani.

## <a name="configure-outbound-network-connectivity"></a>Kimenő hálózati kapcsolat konfigurálása

A Site Recovery megfelelő működése érdekében végre kell hajtani néhány módosítást a kimenő hálózati kapcsolatot illetően a replikálni kívánt virtuális gépeken.

- A Site Recovery nem támogatja hitelesítési proxy használatát a hálózati kapcsolatok szabályozásához.
- Ha rendelkezik hitelesítési proxyval, a replikáció nem engedélyezhető.

### <a name="outbound-connectivity-for-urls"></a>Kimenő kapcsolat URL-címek esetén

Ha URL-alapú tűzfalproxyt használ a kimenő kapcsolat szabályozásához, biztosítania kell a hozzáférést a Site Recovery által használt alábbi URL-címekhez.

| **URL-cím** | **Részletek** |
| ------- | ----------- |
| *.blob.core.windows.net | Lehetővé teszi az adatok írását a virtuális gépről a forrásrégió gyorsítótárjának tárfiókjába. |
| login.microsoftonline.com | Hitelesítést és engedélyezést biztosít a Site Recovery szolgáltatás URL-címeihez. |
| *.hypervrecoverymanager.windowsazure.com | Lehetővé teszi a virtuális gép és a Site Recovery szolgáltatás közötti kommunikációt. |
| *.servicebus.windows.net | Lehetővé teszi a virtuális gép számára a Site Recovery monitorozási és diagnosztikai adatainak írását. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Kimenő kapcsolat az IP-címtartományokhoz

Ha a kimenő kapcsolatot bármilyen IP-alapú tűzfal, proxy vagy NSG-szabályok szabályozzák, a következő IP-címtartományoknak szerepelnie kell az engedélyezési listán. A tartományok listája az alábbi hivatkozásokat követve tölthető le:

  - [A Microsoft Azure adatközpont IP-tartományai](http://www.microsoft.com/en-us/download/details.aspx?id=41653)
  - [A Microsoft Azure adatközpont IP-tartományai Németországban](http://www.microsoft.com/en-us/download/details.aspx?id=54770)
  - [A Microsoft Azure adatközpont IP-tartományai Kínában](http://www.microsoft.com/en-us/download/details.aspx?id=42064)
  - [Office 365 URL-címek és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Site Recovery-szolgáltatásvégpontok IP-címei](https://aka.ms/site-recovery-public-ips)

A listák segítségével konfigurálhatja a hálózati hozzáférés-vezérlést a hálózaton. Ezzel a [szkripttel](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702) hozhatja létre a szükséges NSG-szabályokat.

## <a name="verify-azure-vm-certificates"></a>Azure-beli virtuális gép tanúsítványainak ellenőrzése

Ellenőrizze, hogy a legújabb főtanúsítványok mindegyike megtalálható-e a replikálni kívánt Windows vagy Linux rendszerű virtuális gépeken. Ha a legújabb főtanúsítványok nem találhatók, a virtuális gépet biztonsági okokból nem lehet regisztrálni a Site Recovery szolgáltatásban.

- A Windows rendszerű virtuális gépek esetében az összes új Windows-frissítést telepíteni kell a virtuális gépen, így a megbízható főtanúsítványok mindegyike megtalálható lesz a számítógépen. Leválasztott környezet esetén hajtsa végre a vállalat szabványos Windows Update- és tanúsítványfrissítési folyamatait.

- Linux rendszerű virtuális gépek esetében kövesse a Linux-terjesztőtől származó útmutatást a legújabb megbízható főtanúsítványok és a visszavont tanúsítványok listájának lekéréséhez a virtuális gépen.

## <a name="set-permissions-on-the-account"></a>A fiók engedélyeinek beállítása

Az Azure Site Recovery három beépített szerepkört biztosít a Site Recovery felügyeleti műveleteinek szabályozásához.

- **Site Recovery-közreműködő** – Ez a szerepkör minden olyan engedéllyel rendelkezik, amelyek az Azure Site Recovery-műveletek Recovery Services-tárolókban történő kezeléséhez szükségesek. Az ezzel a szerepkörrel rendelkező felhasználók nem hozhatnak létre, illetve nem törölhetnek Recovery Services-tárolókat, és nem adhatnak hozzáférési jogokat más felhasználóknak. Ez a szerepkör az olyan vész-helyreállítási rendszergazdák számára a leginkább megfelelő, akik az összes alkalmazásra vagy az egész vállalatra vonatkozóan engedélyezhetik és felügyelhetik a vészhelyreállítást.

- **Site Recovery-operátor** – Ez a szerepkör a feladatátvétel és feladat-visszavétel végrehajtásához és felügyeletéhez szükséges engedélyekkel rendelkezik. Az ezzel a szerepkörrel rendelkező felhasználók nem engedélyezhetik vagy tilthatják le a replikációt, nem hozhatnak létre, illetve nem törölhetnek tárolókat, nem regisztrálhatnak új infrastruktúrát, és nem adhatnak hozzáférési jogokat más felhasználóknak. Ez a szerepkör az olyan vész-helyreállítási operátorok számára a leginkább megfelelő, akik le tudják bonyolítani a feladatátvételt a virtuális gépek vagy alkalmazások tekintetében, ha az alkalmazások tulajdonosaitól vagy a rendszergazdáktól erre vonatkozó utasítást kapnak. A vészhelyzet elhárítása után a vész-helyreállítási operátor gondoskodhat a virtuális gépek újbóli védelmének és feladat-visszavételének biztosításáról.

- **Site Recovery-olvasó** – Ez a szerepkör a Site Recovery összes felügyeleti műveletének a megtekintésére rendelkezik engedélyekkel. Ez a szerepkör olyan informatikai felügyeleti vezető számára a leginkább megfelelő, aki monitorozhatja az aktuális biztonság állapotát, és támogatási jegyeket hoz létre.

További információ az [Azure beépített RBAC-szerepköreivel](../active-directory/role-based-access-built-in-roles.md) kapcsolatban.

## <a name="enable-replication"></a>A replikáció engedélyezése

### <a name="select-the-source"></a>Forrás kiválasztása

1. A Recovery Services-tárolók között kattintson a tároló nevére, majd a **+Replikálás** elemre.
2. A **Forrás** esetében válassza az **Azure – ELŐZETES VERZIÓ** lehetőséget.
3. A **Forrás helye** értékeként válassza ki azt az Azure-régiót, ahol az adott virtuális gépek éppen futnak.
4. Válassza ki **az Azure-beli virtuális gép üzembe helyezési modelljét** a virtuális gépek számára: **Resource Manager-alapú** vagy **Klasszikus**.
5. A Resource Managert használó virtuális gépek esetében válassza ki **a forrásként szolgáló erőforráscsoportot**, a klasszikus virtuális gépek esetében pedig a **felhőszolgáltatást**.
6. Kattintson az **OK** gombra a beállítások mentéséhez.

### <a name="select-the-vms"></a>A virtuális gépek kiválasztása

A Site Recovery lekéri az előfizetéshez és az erőforráscsoporthoz/felhőszolgáltatáshoz társított virtuális gépek listáját.

1. A **Virtuális gépek** menüben jelölje ki a replikálni kívánt virtuális gépeket.
2. Kattintson az **OK** gombra.

### <a name="configure-replication-settings"></a>Replikációs beállítások konfigurálása

A Site Recovery létrehozza a célrégióra vonatkozó alapértelmezett beállításokat és replikációs szabályzatot. A beállításokat igény szerint módosíthatja.

1. A célra és a replikációra vonatkozó beállítások megtekintéséhez kattintson a **Beállítások** elemre.
2. Az alapértelmezett célbeállítások felülbírálásához kattintson a **Testreszabás** elemre az **Erőforráscsoport, hálózat, tárolás és rendelkezésre állási készletek** mellett.

  ![Beállítások konfigurálása](./media/azure-to-azure-tutorial-enable-replication/settings.png)


- **Célhely**: A vészhelyreállításhoz használt célrégió. Javasoljuk, hogy a célhely és a Site Recovery-tároló helye ugyanaz legyen.

- **Cél erőforráscsoport**: A célrégió azon erőforráscsoportja, ahol a feladatátvételt követően az Azure-beli virtuális gépek találhatók. Alapértelmezés szerint a Site Recovery létrehoz egy „asr” utótaggal rendelkező új erőforráscsoportot a célrégióban.

- **Cél virtuális hálózat**: Az a hálózat a célrégióban, ahol a virtuális gépek találhatók a feladatátvétel után.
  Alapértelmezés szerint a Site Recovery létrehoz egy „asr” utótaggal rendelkező új virtuális hálózatot (és alhálózatokat) a célrégióban.

- **Gyorsítótár tárfiókjai**: A Site Recovery tárfiókot használ a forrásrégióban. A forrás virtuális gépekre vonatkozó módosítások ebbe a fiókba érkeznek a célhelyre történő replikáció előtt.

- **Cél tárfiókok**: Alapértelmezés szerint a Site Recovery új tárfiókot hoz létre a célrégióban a forrás virtuális gép tárfiókjának tükrözéséhez.

- **Cél rendelkezésre állási csoportok**: Alapértelmezés szerint a Site Recovery létrehoz egy új rendelkezésre állási csoportot a célrégióban az „asr” utótaggal. Rendelkezésre állási csoportot csak vehet fel, ha a virtuális gépek egy csoport részei a forrásrégióban.

Az alapértelmezett replikációsszabályzat-beállítások felülbírálásához kattintson a **Replikációs szabályzat** melletti **Testreszabás** elemre.  

- **Replikációs szabályzat neve**: A szabályzat neve.

- **Helyreállítási pont megőrzése**: Alapértelmezés szerint a Site Recovery 24 óráig őrzi meg a helyreállítási pontokat. 1 és 72 óra közötti értéket konfigurálhat.

- **Alkalmazáskonzisztens pillanatkép gyakorisága**: Alapértelmezés szerint a Site Recovery 4 óránként készít egy alkalmazáskonzisztens pillanatképet. 1 és 12 óra közötti értéket konfigurálhat. Az alkalmazáskonzisztens pillanatkép a virtuális gépen található alkalmazásadatok időponthoz kötött pillanatképe. A kötet árnyékmásolata szolgáltatás (VSS) biztosítja, hogy a virtuális gépen található alkalmazás konzisztens állapotban legyen a pillanatkép készítésekor.

- **Replikációs csoport**: Ha az alkalmazásnak több virtuális gépre kiterjedően konzisztensnek kell lennie, létrehozhat egy replikációs csoport az érintett virtuális gépek számára. Alapértelmezés szerint a kijelölt virtuális gépek nem részei semmilyen replikációs csoportnak sem.

  Kattintson a **Replikációs szabályzat** melletti **Testreszabás** elemre, majd a több virtuális gépre kiterjedő konzisztencia esetében az **Igen** lehetőségre, hogy a virtuális gépek egy replikációs csoportba kerüljenek. Hozzon létre egy új replikációs csoportot, vagy használjon egy már meglévőt. Jelölje ki a replikációs csoport részévé tenni kívánt virtuális gépeket, majd kattintson az **OK** gombra.

> [!IMPORTANT]
  A feladatátvételkor a replikációs csoportba tartozó összes gép megosztott összeomlás-konzisztens és alkalmazáskonzisztens helyreállítási pontokkal rendelkezik majd. A több virtuális gépre kiterjedő konzisztencia hatással lehet a számítási feladatok teljesítményére, és csak akkor javasolt a használata, ha a gépeken ugyanaz a számítási feladat fut, és több gép közötti konzisztenciára van szükség.

> [!IMPORTANT]
  Ha engedélyezte a több virtuális gépre kiterjedő konzisztenciát, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással. Ellenőrizze, hogy nem blokkolja-e tűzfal a virtuális gépek között a 20004-es porton keresztül zajló belső kommunikációt. Ha Linux rendszerű virtuális gépeket szeretne egy replikációs csoport részévé tenni, győződjön meg arról, hogy a 20004-es port esetében a kimenő adatforgalom manuális megnyitása megtörtént-e az adott Linux-verzió útmutatásainak megfelelően.

### <a name="track-replication-status"></a>A replikáció állapotának nyomon követése

1. Az aktuális állapot megtekintéséhez kattintson a **Beállítások** menü **Frissítés** elemére.

2. A **Védelem engedélyezése** feladat előrehaladását a **Beállítások**>**Feladatok**>**Site Recovery-feladatok** menüpontban követheti nyomon.

3. A **Beállítások** > **Replikált elemek** pontban tekintheti meg a virtuális gépek és a kezdeti replikáció folyamatának állapotát. A beállítások részleteinek megjelenítéséhez kattintson az adott virtuális gépre.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure-beli virtuális gép vészhelyreállítását konfigurálta. A következő lépés a konfiguráció tesztelése.

> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](azure-to-azure-tutorial-dr-drill.md)
