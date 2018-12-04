---
title: Azure-beli virtuális gépek másodlagos Azure-régióba történő vészhelyreállításának beállítása az Azure Site Recovery használatával
description: Megismerheti, hogyan állíthatja be az Azure-beli virtuális gépek egy másik Azure-régióba történő vészhelyreállítását az Azure Site Recovery szolgáltatással.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 0aa7b7f3558bab7f3553527e03c44d71dd5a87ac
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52833142"
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region"></a>Azure-beli virtuális gépek másodlagos Azure-régióba történő vészhelyreállításának beállítása

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás hozzájárul a vész-helyreállítási stratégiát kezelésével és irányításával replikálása, feladatátvétele és feladat-visszavételhez helyszíni gépek és Azure-beli virtuális gépek (VM).

Az oktatóanyag bemutatja, hogyan állíthat be vészhelyreállítást egy másodlagos Azure-régióba az Azure-beli virtuális gépek számára. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Recovery Services-tároló létrehozása
> * A célerőforrások beállításainak ellenőrzése.
> * Virtuális gépek kimenő hozzáférésének beállítása.
> * Virtuális gép replikálásának engedélyezése

> [!NOTE]
> Ez a cikk ismerteti, hogyan telepíthető a legegyszerűbben beállításokkal vész-helyreállítási. Ha szeretné testre szabott beállítások ismertetése, tekintse át a cikkek alapján a [útmutató szakaszban](azure-to-azure-how-to-enable-replication.md). O

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

  - [A Microsoft Azure adatközpont IP-tartományai](https://www.microsoft.com/en-us/download/details.aspx?id=41653)
  - [A Microsoft Azure adatközpont IP-tartományai Németországban](https://www.microsoft.com/en-us/download/details.aspx?id=54770)
  - [A Microsoft Azure adatközpont IP-tartományai Kínában](https://www.microsoft.com/en-us/download/details.aspx?id=42064)
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

Tudjon meg többet [Azure RBAC beépített szerepkörök](../role-based-access-control/built-in-roles.md)

## <a name="enable-replication"></a>A replikáció engedélyezése

### <a name="select-the-source"></a>Forrás kiválasztása

1. A Recovery Services-tárolók között kattintson a tároló nevére, majd a **+Replikálás** elemre.
2. A **Forrás** beállításnál válassza az **Azure** értéket.
3. A **Forrás helye** értékeként válassza ki azt az Azure-régiót, ahol az adott virtuális gépek éppen futnak.
4. Válassza ki azt a **Forrás-előfizetést**, ahol a virtuális gépek futnak. Ez bármelyik előfizetés lehet azon Azure Active Directory-bérlőn belül, ahol a már meglévő Recovery Services-tárolója van.
5. A Resource Managert használó virtuális gépek esetében válassza ki **a forrásként szolgáló erőforráscsoportot**, a klasszikus virtuális gépek esetében pedig a **felhőszolgáltatást**.
6. Kattintson az **OK** gombra a beállítások mentéséhez.

### <a name="select-the-vms"></a>A virtuális gépek kiválasztása

A Site Recovery lekéri az előfizetéshez és az erőforráscsoporthoz/felhőszolgáltatáshoz társított virtuális gépek listáját.

1. A **Virtuális gépek** menüben jelölje ki a replikálni kívánt virtuális gépeket.
2. Kattintson az **OK** gombra.

### <a name="configure-replication-settings"></a>Replikációs beállítások konfigurálása

A Site Recovery létrehozza a célrégióra vonatkozó alapértelmezett beállításokat és replikációs szabályzatot. A beállításokat, szükség szerint módosíthatja.

1. A célra és a replikációra vonatkozó beállítások megtekintéséhez kattintson a **Beállítások** elemre.
2. Az alapértelmezett célbeállítások felülbírálásához kattintson **Testreszabás** melletti **erőforráscsoport, hálózat, tárolás és rendelkezésre állási**.

  ![Beállítások konfigurálása](./media/azure-to-azure-tutorial-enable-replication/settings.png)


3. Testre szabhatja a célként megadott beállításokat az alábbiak szerint:

    - **Célelőfizetés**: A vészhelyreállításhoz használt célelőfizetés. Alapértelmezés szerint a célelőfizetés megegyezik a forrás-előfizetéssel. Ha egy másik előfizetést szeretne választani ugyanazon az Azure Active Directory-bérlőn belül, kattintson a „Testreszabásra” lehetőségre.
    - **Célhely**: A vészhelyreállításhoz használt célrégió. Javasoljuk, hogy a célhely és a Site Recovery-tároló helye ugyanaz legyen.
    - **Cél erőforráscsoport**: A célrégió azon erőforráscsoportja, ahol a feladatátvételt követően az Azure-beli virtuális gépek találhatók. Alapértelmezés szerint a Site Recovery létrehoz egy „asr” utótaggal rendelkező új erőforráscsoportot a célrégióban. A cél erőforráscsoport helye bármelyik régió lehet, kivéve azt a régiót, amelyben a forrás virtuális gépeket üzemeltetik.
    - **Cél virtuális hálózat**: Az a hálózat a célrégióban, ahol a virtuális gépek találhatók a feladatátvétel után.
      Alapértelmezés szerint a Site Recovery létrehoz egy „asr” utótaggal rendelkező új virtuális hálózatot (és alhálózatokat) a célrégióban.
    - **Gyorsítótár tárfiókjai**: A Site Recovery tárfiókot használ a forrásrégióban. A forrás virtuális gépekre vonatkozó módosítások ebbe a fiókba érkeznek a célhelyre történő replikáció előtt.
      >[!NOTE]
      >Ha engedélyezve van a tűzfal tárfiókot használ, ügyeljen arra, hogy "A megbízható Microsoft-szolgáltatások engedélyezése". [Részletek](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
      >

    - **Cél tárfiókok (ha a forrás virtuális gép nem használ felügyelt lemezeket)**: Alapértelmezés szerint a Site Recovery új tárfiókot hoz létre a célrégióban a forrás virtuális gép tárfiókjának tükrözéséhez.
      >[!NOTE]
      >Ha engedélyezve van a tűzfal-forrás vagy cél tárfiókot használ, ügyeljen arra, hogy "A megbízható Microsoft-szolgáltatások engedélyezése". [Részletek](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
      >

    - **Replika felügyelt lemezek (ha a forrás virtuális gép felügyelt lemezeket használ)**: Alapértelmezés szerint a Site Recovery replika felügyelt lemezeket hoz létre a célrégióban a forrás virtuális gép felügyelt lemezeinek azokkal azonos tártípusban (standard vagy prémium) való tükrözéséhez.
    - **Cél rendelkezésre állási csoportok**: alapértelmezés szerint az Azure Site Recovery létrehoz egy új rendelkezésre állási csoportot a célrégióban a virtuális gépek része egy rendelkezésre állási csoportot a forrásrégióban az "asr" utótaggal rendelkező nevű. Rendelkezésre állási csoport létrehozása az Azure Site Recovery már létezik, a rendszer újra.
    - **Cél rendelkezésre állási zónák**: alapértelmezés szerint a Site Recovery zóna száma azonos a célrégióban a forrás régiójára rendeli, ha a célrégióban támogatja a rendelkezésre állási zónák. 

    A célrégió nem támogatja a rendelkezésre állási zónák használatát, ha a cél virtuális gépek alapértelmezés szerint egyetlen példány van konfigurálva. Ha szükséges, az ilyen virtuális gépek rendelkezésre állási csoportot a célrégióban kell "Testreszabás" gombra kattintva konfigurálhatja.

    >[!NOTE]
    >A rendelkezésre állási típusa – egyetlen példány, a rendelkezésre állási csoport vagy a rendelkezésre állási zónában, a replikáció engedélyezése után nem módosítható. Tiltsa le majd engedélyezze a rendelkezésre állási típusának módosításához kell.
    >

4. Replikációs házirend beállításainak testre szabhatja, kattintson a **Testreszabás** melletti **replikációs házirend**, és szükség szerint a következő beállításokat módosíthatja:

    - **Replikációs szabályzat neve**: A szabályzat neve.
    - **Helyreállítási pont megőrzése**: Alapértelmezés szerint a Site Recovery 24 óráig őrzi meg a helyreállítási pontokat. 1 és 72 óra közötti értéket konfigurálhat.
    - **Alkalmazáskonzisztens pillanatkép gyakorisága**: Alapértelmezés szerint a Site Recovery 4 óránként készít egy alkalmazáskonzisztens pillanatképet. 1 és 12 óra közötti értéket konfigurálhat. Az alkalmazáskonzisztens pillanatkép a virtuális gépen található alkalmazásadatok időponthoz kötött pillanatképe. A kötet árnyékmásolata szolgáltatás (VSS) biztosítja, hogy a virtuális gépen található alkalmazás konzisztens állapotban legyen a pillanatkép készítésekor.
    - **Replikációs csoport**: Ha az alkalmazásnak több virtuális gépre kiterjedően konzisztensnek kell lennie, létrehozhat egy replikációs csoport az érintett virtuális gépek számára. Alapértelmezés szerint a kijelölt virtuális gépek nem részei semmilyen replikációs csoportnak sem.

5. A **Testreszabás**válassza **Igen** a virtuális gépre kiterjedő konzisztencia, ha azt szeretné, hogy virtuális gépeket ad hozzá egy új vagy meglévő replikációs csoporthoz. hogy a virtuális gépek egy replikációs csoport része. Ezután kattintson az **OK** gombra.

    - A feladatátvételkor a replikációs csoportba tartozó összes gép megosztott összeomlás-konzisztens és alkalmazáskonzisztens helyreállítási pontokkal rendelkezik majd. A több virtuális gépre kiterjedő konzisztencia hatással lehet a számítási feladatok teljesítményére, és csak akkor javasolt a használata, ha a gépeken ugyanaz a számítási feladat fut, és több gép közötti konzisztenciára van szükség.
    - Ha engedélyezte a több virtuális gépre kiterjedő konzisztenciát, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással. Ellenőrizze, hogy nem blokkolja-e tűzfal a virtuális gépek között a 20004-es porton keresztül zajló belső kommunikációt. Ha Linux rendszerű virtuális gépeket szeretne egy replikációs csoport részévé tenni, győződjön meg arról, hogy a 20004-es port esetében a kimenő adatforgalom manuális megnyitása megtörtént-e az adott Linux-verzió útmutatásainak megfelelően.

### <a name="configure-encryption-settings"></a>Titkosítási beállítások konfigurálása

Ha a forrás virtuális gép az Azure disk encryption (ADE) engedélyezve van, a titkosítási beállítások láthatók:

1. Tekintse át a titkosítási beállításokat.
    - **Lemeztitkosítási kulcstárolók**: Alapértelmezés szerint az Azure Site Recovery a forrás VM lemeztitkosítási kulcsai alapján létrehoz a célrégióban egy új kulcstárolót, amelynek a neve az „asr” utótaggal rendelkezik. Ha az Azure Site Recovery által létrehozott kulcstároló már létezik, a rendszer azt használja újra.
    - **Kulcstitkosítási kulcstároló**: Alapértelmezés szerint az Azure Site Recovery a forrás VM kulcstitkosítási kulcsai alapján létrehoz a célrégióban egy új kulcstárolót, amelynek a neve az „asr” utótaggal rendelkezik. Ha az Azure Site Recovery által létrehozott kulcstároló már létezik, a rendszer azt használja újra.

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
