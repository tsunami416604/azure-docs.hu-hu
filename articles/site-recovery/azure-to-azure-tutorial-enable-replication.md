---
title: "Beállítása a vészhelyreállítás Azure virtuális gépek egy másodlagos Azure-régió, az Azure Site Recovery (előzetes verzió)"
description: "Megtudhatja, hogyan állíthat be vészhelyreállítás Azure virtuális gépek egy másik Azure-régió, az Azure Site Recovery szolgáltatással."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/08/2017
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 3db1ead1f1a8b83cc47f53b915ed54bb78db7ab3
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2017
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region-preview"></a>Beállítása a vészhelyreállítás Azure virtuális gépek egy másodlagos Azure-régiót (előzetes verzió)

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás vész-helyreállítási stratégiát infrastruktúrája azzal segíti a kezelése és koordinálása replikációjának, feladatátvételének és feladat-visszavétel a helyszíni gépeket, és az Azure virtuális gépek (VM).

Az oktatóanyag bemutatja, hogyan állíthat be egy másodlagos Azure régióra vész-helyreállítási Azure virtuális gépek. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Recovery Services-tároló létrehozása
> * Ellenőrizze a célként megadott erőforrás-beállítások
> * Virtuális gépek kimenő hozzáférés beállítása
> * A virtuális gépek replikálásának engedélyezése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Győződjön meg arról, hogy tudomásul veszi a [forgatókönyv architektúrája és összetevői](concepts-azure-to-azure-architecture.md).
- Tekintse át a [igényeinek támogatására](site-recovery-support-matrix-azure-to-azure.md) lévő valamennyi összetevőnél.

## <a name="create-a-vault"></a>Tároló létrehozása

A tároló létrehozása minden régióban, kivéve a forrás-régió.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **Recovery Services** szolgáltatásba.
2. Kattintson az **Új** > **Felügyelet és kezelés** > **Backup és Site Recovery** lehetőségre.
3. A **Név** mezőben adja meg a tárolót azonosító rövid nevet. Ha egynél több előfizetéssel rendelkezik, jelölje ki a megfelelő.
4. Hozzon létre egy erőforráscsoportot, vagy válasszon egy meglévőt. Válassza ki a kívánt Azure-régiót. A támogatott régiók megtekintéséhez olvassa el az [Azure Site Recovery – Díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/) című cikknek a földrajzi elérhetőséggel foglalkozó részét.
5. Gyors hozzáférést a tárolóhoz az irányítópultról, kattintson a **rögzítés az irányítópulton** majd **létrehozása**.

   ![Új tároló](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   Az új tároló hozzáadódik a **irányítópult** alatt **összes erőforrás**, majd a fő **Recovery Services-tárolók** lap.

## <a name="verify-target-resources"></a>Ellenőrizze a tároló erőforrásait

1. Győződjön meg arról, hogy az Azure-előfizetéshez lehetővé teszi a virtuális gépek létrehozásához használt vész-helyreállítási cél régióban. A szükséges kvóta engedélyezéséhez forduljon a támogatási szolgálathoz.

2. Ellenőrizze, hogy az előfizetés virtuális gépek támogatásához van szükség, amelyek megfelelnek a forrás virtuális gépeknek elegendő erőforrással rendelkezik. A Site Recovery szerzi méretének vagy a legközelebbi a cél virtuális gép mérete.

## <a name="configure-outbound-network-connectivity"></a>Kimenő hálózati kapcsolat konfigurálása

A Site Recovery számára a várt módon működik kell néhány módosítást a kimenő hálózati kapcsolatot, a replikálni kívánt virtuális gépek.

- A Site Recovery hitelesítési proxypontok vezérlő hálózati kapcsolat nem támogatja.
- Ha egy hitelesítési proxy, nem engedélyezhető.

### <a name="outbound-connectivity-for-urls"></a>Kimenő kapcsolat URL-címek esetén

Ha egy URL-alapú tűzfal proxy vezérlésére kimenő kapcsolatok használata esetén a következő URL-Site Recovery által használt hozzá lehessen férni.

| **URL-CÍME** | **Részletek** |
| ------- | ----------- |
| *.blob.core.windows.net | Lehetővé teszi a virtuális gép lehet írni a gyorsítótár tárfiók a forrás régióban. |
| login.microsoftonline.com | A hitelesítési és engedélyezési biztosít a Site Recovery szolgáltatás URL-címeit. |
| *.hypervrecoverymanager.windowsazure.com | Lehetővé teszi, hogy a virtuális Gépet, a Site Recovery szolgáltatással való kommunikációra. |
| *. servicebus.windows.net | Lehetővé teszi, hogy a VM írása a Site Recovery figyelése és a diagnosztikai adatokat. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Kimenő kapcsolódás az IP-címtartományok

Ha bármely IP-alapú tűzfal, a proxy vagy az NSG-szabályok segítségével szabályozhatja a kimenő kapcsolat, a következő IP-címtartományok kell szerepel az engedélyezési listán. Töltse le a tartományok listáját az alábbi hivatkozások:

  - [A Microsoft Azure Datacenter IP-címtartományok](http://www.microsoft.com/en-us/download/details.aspx?id=41653)
  - [A Windows Azure Datacenter IP-címtartományok Németországban](http://www.microsoft.com/en-us/download/details.aspx?id=54770)
  - [A Windows Azure Datacenter IP-címtartományok Kínában](http://www.microsoft.com/en-us/download/details.aspx?id=42064)
  - [Office 365 URL-címek és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Webhely-helyreállítási szolgáltatási végpont IP-címek](https://aka.ms/site-recovery-public-ips)

A listák segítségével konfigurálhatja a hálózati hozzáférés-vezérlést a hálózaton. Ezzel [parancsfájl](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702) szükséges NSG-szabályok létrehozásához.

## <a name="verify-azure-vm-certificates"></a>Azure virtuális gép tanúsítványok ellenőrzése

Ellenőrizze, hogy a legújabb legfelső szintű tanúsítványokat Windows vagy Linux replikálni kívánt virtuális gépeket. Ha nem a legújabb legfelső szintű tanúsítványok, a virtuális gép nem lehet regisztrálni a Site Recovery szolgáltatásban, kellő biztonsági korlátozások.

- Windows virtuális gépek, a legújabb Windows frissítések telepítése a virtuális Gépen, így a megbízható legfelső szintű tanúsítványok a számítógépen. Leválasztott környezet esetén hajtsa végre a szabványos Windows Update és a szervezet tanúsítvány frissítés folyamatait.

- Linux virtuális gépekhez kövesse az útmutatást, a Linux terjesztő, a virtuális Gépre a legújabb megbízható legfelső szintű tanúsítványok és a visszavont tanúsítványok listájának eléréséhez által biztosított.

## <a name="set-permissions-on-the-account"></a>A fiók engedélyeinek beállítása

Az Azure Site Recovery három beépített szerepkörök, amelyekkel szabályozhatja a Site Recovery felügyeleti műveleteket biztosít.

- **Hely helyreállítási közreműködői** -ezt a szerepkört minden Azure Site Recovery-műveleteket a Recovery Services-tároló kezeléséhez szükséges engedéllyel rendelkezik. Ezzel a szerepkörrel rendelkező felhasználók azonban nem létrehozása vagy Recovery Services-tároló törlése vagy más felhasználók hozzáférési jogosultságokat rendelhet hozzá. Ezt a szerepkört olyan vész helyreállítási rendszergazdák számára, akik engedélyezheti és vészhelyreállítás az alkalmazások vagy a teljes szervezet számára.

- **Helyreállítási operátor hely** -ehhez a szerepkörhöz van engedélye végrehajtására és a feladatátvétel és a feladat-visszavétel operations manager. A felhasználói szerephez nem tudja engedélyezni vagy tiltsa le a replikációt, hozzon létre vagy tárolók törlése, új infrastruktúra regisztrálja, vagy más felhasználók hozzáférési jogosultságokat rendelhet hozzá. A szerepkör a virtuális gépek vagy alkalmazásokat, amikor arra utasította az alkalmazástulajdonosok és a rendszergazdák által a feladatátvétel vész helyreállítási kezelők a legalkalmasabb. POST a katasztrófa feloldását, a vész-Helyreállítási operátor is lássa el újból védelemmel és a feladat-visszavételt a virtuális gépek.

- **Helyreállítási olvasó hely** – Ez a szerepkör jogosult összes Site Recovery felügyeleti műveleteinek megtekintéséhez. Ezt a szerepkört olyan egy informatikai felügyeleti vezető, akik védelmi aktuális állapotának figyelése és támogatási jegyek ablakába.

A további [beépített Azure RBAC-szerepkörök](../active-directory/role-based-access-built-in-roles.md)

## <a name="enable-replication"></a>A replikáció engedélyezése

### <a name="select-the-source"></a>A forrás kiválasztása

1. A Recovery Services-tárolók, kattintson a tároló neve > **+ replikálás**.
2. A **forrás**, jelölje be **Azure - előzetes**.
3. A **adatforrásról**, válasszon ki forrást az Azure-régió, ahol a virtuális gépek futnak.
4. Válassza ki a **Azure virtuális gép üzembe helyezési modellel** virtuális gépek: **erőforrás-kezelő** vagy **klasszikus**.
5. Válassza ki a **forrás-erőforráscsoporton** erőforrás-kezelő virtuális gépekhez, vagy **felhőalapú szolgáltatás** klasszikus virtuális gépekhez.
6. Kattintson az **OK** gombra a beállítások mentéséhez.

### <a name="select-the-vms"></a>Válassza ki a virtuális gépek

A Site Recovery lekéri az előfizetés és az erőforrás/felhőszolgáltatás társított virtuális gépek listáját.

1. A **virtuális gépek**, válassza ki a replikálni kívánt virtuális gépeket.
2. Kattintson az **OK** gombra.

### <a name="configure-replication-settings"></a>Replikációs beállítások konfigurálása

A Site Recovery alapértelmezett beállításait és a cél régió replikációs házirendet hoz létre. Módosíthatja a beállításokat a követelmények alapján.

1. Kattintson a **beállítások** a cél- és a replikációs beállítások megtekintéséhez.
2. Az alapértelmezett tároló beállításainak a felülbírálásához **Testreszabás** melletti **erőforráscsoport, hálózati, tárolási és rendelkezésre állási készletek**.

  ![Beállítások konfigurálása](./media/azure-to-azure-tutorial-enable-replication/settings.png)


- **Célhelye**: vész-helyreállítási cél régióját. Azt javasoljuk, hogy a célhely megegyezik-e a Site Recovery-tároló helyét.

- **Célként megadott erőforráscsoportja**: az erőforráscsoport a cél régióban, amely tárolja az Azure virtuális gépek a feladatátvételt követően. Alapértelmezés szerint a Site Recovery a cél régióban, az "automatikus" utótaggal rendelkező egy új erőforráscsoportot hoz létre.

- **Cél virtuális hálózati**: a cél régióban, hogy virtuális gépek a feladatátvétel után a hálózaton.
  Alapértelmezés szerint a Site Recovery hoz létre egy új virtuális hálózat (és alhálózatok) az "automatikus" utótaggal rendelkező cél régióban.

- **Storage-fiókok gyorsítótár**: a Site Recovery storage-fiókot használ a forrás régióban. Változások a forrás virtuális gépek ezt a fiókot a célhelyre replikáció előtt kell küldeni.

- **Storage-fiókok cél**: alapértelmezés szerint a Site Recovery hoz létre egy új tárfiókot mappába történő tükrözésének a forrás virtuális gép tárfiók cél régióban.

- **Rendelkezésre állási készletek cél**: alapértelmezés szerint a Site Recovery hoz létre egy új rendelkezésre állási csoportban, a "automatikus" utótaggal rendelkező cél régióban. Rendelkezésre állási csoportok csak virtuális gépek részei egy készlet a forrás régióban adhat hozzá.

Az alapértelmezett replikációs házirend-beállítások felülbírálásához **Testreszabás** melletti **replikációs házirend**.  

- **Replikációs házirend neve**: a házirend nevét.

- **Helyreállítási pontok megőrzésének ideje**: alapértelmezés szerint a Site Recovery helyreállítási pontok tartja 24 órán át. Beállíthatja, hogy egy 1 és 72 óra közötti értéket.

- **Alkalmazáskonzisztens pillanatkép gyakorisága**: alapértelmezés szerint a Site Recovery pillanatfelvételt egy alkalmazáskonzisztens 4 óránként. Beállíthatja, hogy bármely érték 1 és 12 óra között. Alkalmazáskonzisztens pillanatkép a virtuális Gépen belül az alkalmazásadatok pont időponthoz kötött pillanatképet. Kötet árnyékmásolata szolgáltatás (VSS) biztosítja, hogy a virtuális Gépen lévő alkalmazás konzisztens állapotban legyenek a pillanatkép készítésének időpontjában.

- **Replikációs csoport**: Ha az alkalmazás virtuális Gépre kiterjedő konzisztencia virtuális gépek között van szüksége, létrehozhat egy replikációs csoport azok számára, virtuális gépek. Alapértelmezés szerint a kijelölt virtuális gépek részei valamelyik replikációs csoport.

  Kattintson a **Testreszabás** melletti **replikációs házirend** , és válassza **Igen** annak a virtuális gépek több virtuális Gépre kiterjedő konzisztencia replikációs csoport része. Hozzon létre egy új replikációs csoportot, vagy adjon meg egy meglévő replikációs csoportot. Válassza ki a virtuális gépeket, a replikációs csoportot, majd kattintson a részeként **OK**.

> [!IMPORTANT]
  A replikációs csoport összes gép lesz megosztva összeomlik miattuk konzisztens és alkalmazáskonzisztens helyreállítási pontokat, ha a feladatátvételt. Lehetővé teszi több virtuális Gépre kiterjedő konzisztencia befolyásolhatja a teljesítményt a munkaterhelés, és csak akkor, ha a gép ugyanaz az alkalmazás fut, és több gép közötti konzisztenciára van szükség.

> [!IMPORTANT]
  Ha engedélyezi a virtuális Gépre kiterjedő konzisztencia, a replikációs csoport gépek kommunikálhatnak egymással 20004 porton keresztül. Ellenőrizze, hogy nem blokkolja-e a belső kommunikáció 20004 porton keresztül a virtuális gépek közötti tűzfal készüléket. Ha azt szeretné, hogy a Linux virtuális gépek a replikációs csoport részeként, győződjön meg arról, a kimenő adatforgalmat a port 20004 manuálisan megnyitott állapotokban az adott Linux verzió útmutatást.

### <a name="track-replication-status"></a>Replikáció állapotának nyomon követése

1. A **beállítások**, kattintson a **frissítése** a legfrissebb állapotának beolvasása.

2. Előrehaladásának nyomon követheti a **engedélyezni a védelmet** feladat **beállítások** > **feladatok** > **Site Recovery-feladatok**.

3. A **beállítások** > **replikált elemek**, megtekintheti az állapotát, a virtuális gépek és a kezdeti replikáció folyamatban van. Kattintson a virtuális gépek a részletekbe menően tárhatják fel annak beállításait.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a vész-helyreállítási egy Azure virtuális gép konfigurálta. Következő lépés, hogy tesztelje a konfigurációt.

> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](azure-to-azure-tutorial-dr-drill.md)
