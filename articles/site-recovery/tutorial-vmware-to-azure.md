---
title: "Vész-helyreállítási Azure beállítása a helyszíni VMware virtuális gépek az Azure Site Recovery szolgáltatással |} Microsoft Docs"
description: "Megtudhatja, hogyan állíthatja be az Azure-bA vész-helyreállítási helyszíni VMware virtuális gépek az Azure Site Recovery szolgáltatással."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5810ff908d48fc4ff742d734e7c2457fdfe8cb03
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Helyszíni VMware virtuális gépek Azure-bA vész-helyreállítási beállítása

Az oktatóanyag bemutatja, hogyan állíthat be az Azure-bA vész-helyreállítási helyszíni VMware virtuális gépek Windows rendszerű. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adja meg a forrásként és a célkiszolgálón.
> * A forrás replikációs környezet beállítása, beleértve a helyszíni Site Recovery-összetevőkhöz, és a cél replikálási környezetet.
> * Replikációs házirend létrehozása
> * A virtuális gépek replikálásának engedélyezése

Ez az egy sorozat harmadik oktatóanyaga. Ez az oktatóanyag feltételezi, hogy már végrehajtotta a feladatokat a az előző oktatóanyagok:

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. [Helyszíni VMware előkészítése](tutorial-prepare-on-premises-vmware.md)

Mielőtt elkezdené, érdemes [tekintse át a architektúra](concepts-vmware-to-azure-architecture.md) vész-helyreállítási forgatókönyv esetében.


## <a name="select-a-replication-goal"></a>Replikációs cél

1. A **Recovery Services-tárolók**, kattintson a tároló nevére **ContosoVMVault**.
2. A **bevezetés**, kattintson a helyreállítás. Kattintson a **infrastruktúra előkészítése**.
3. A **védelmi cél** > **hol vannak a található gép**, jelölje be **helyszíni**.
4. A ** hol szeretné replikálni a gépeket, jelölje ki **az Azure-bA**.
5. A **a virtuális gép**, jelölje be **Igen, amelyen a VMware vSphere Hipervizorra**. Ezután kattintson az **OK** gombra.

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Állítsa be a forrás környezetében, töltse le a Site Recovery az egységes telepítő fájlját. Futtatja a telepítőt a helyi Site Recovery-összetevők telepítéséhez, VMware-kiszolgáló regisztrálása a tárolóban és a helyszíni virtuális gépek felderítése.

### <a name="verify-on-premises-site-recovery-requirements"></a>A helyszíni Site Recovery követelményeinek ellenőrzése

Egyetlen, magas rendelkezésre állású, a helyszíni VMware virtuális gép gazdagép helyszíni Site Recovery összetevők kell. Összetevők közé tartoznak a konfigurációs kiszolgáló, a folyamatkiszolgáló és a fő célkiszolgálón.

- A konfigurációs kiszolgáló koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- A folyamatkiszolgáló egy replikációs átjáróként működik. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az Azure Storage-nak. Szeretne replikálni, virtuális gépeken is telepíti a mobilitási szolgáltatást a folyamatkiszolgáló és a helyszíni VMware virtuális gépek automatikus felderítést hajt végre.
- A fő célkiszolgáló az Azure-ból a feladat-visszavétel során kezeli a replikációs adatokat.

A virtuális gép az alábbi követelményeknek kell megfelelnie.

| **Követelmény** | **Részletek** |
|-----------------|-------------|
| Processzormagok száma| 8 |
| RAM | 12 GB |
| Lemezek száma | 3 - operációsrendszer-lemez, a folyamat kiszolgáló gyorsítótár lemez vagy az adatmegőrzési meghajtó (a feladat-visszavétel) |
| – Lemez szabad területe (Folyamatkiszolgálói gyorsítótár) | 600 GB |
| Lemez szabad területe (adatmegőrzési lemez) | 600 GB |
| Operációs rendszer verziója | Windows Server 2012 R2 |
| Operációs rendszer területi beállítása | Angol (en-us) |
| VMware vSphere PowerCLI verziója | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") |
| Windows Server-szerepkörök | Ezek a szerepkörök nem engedélyezi: Active Directory tartományi szolgáltatásokban, az Internet Information Services, a Hyper-V |
| A hálózati adapter típusa | VMXNET3 |
| IP-cím típusa | Statikus |
| Portok | 443 (vezérlőcsatorna-vezénylés)<br/>9443 (Adatátvitel)|

Továbbá: 
- Győződjön meg arról, hogy a virtuális Gépen a rendszeróra szinkronizálva van-e a kiszolgálót. Idő 15 percen belül kell szinkronizálni. Ha az értéke nagyobb, a telepítés meghiúsul.
a telepítés meghiúsul.
- Győződjön meg arról, hogy a konfigurációs kiszolgáló VM hozzáférhet az URL-címek:

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- Győződjön meg arról, hogy IP-címeken alapuló tűzfalszabályok szabályok engedélyezik-e a kommunikációt az Azure-bA.
    - Engedélyezi a [Azure datacenter IP-címtartományok](https://www.microsoft.com/download/confirmation.aspx?id=41653)port 443-as (HTTPS) és port 9443 (adatreplikáció).
    - Az IP-címtartományok az előfizetés az Azure-régió, valamint a (hozzáférés-vezérlés és identitás kezelésre szolgáló) USA nyugati régiója engedélyezése.


### <a name="download-the-site-recovery-unified-setup-file"></a>Töltse le a Site Recovery egyesített telepítő

1. A tárolóban lévő > **infrastruktúra előkészítése**, kattintson a **forrás**.
1. A **forrás előkészítése**, kattintson a **+ konfigurációs kiszolgáló**.
2. A **kiszolgáló hozzáadása**, ellenőrizze, hogy **konfigurációs kiszolgáló** megjelenik **kiszolgálótípus**.
3. Töltse le a Site Recovery az egységes telepítő telepítőfájlját.
4. Töltse le a tárolóregisztrációs kulcsot. Ez szükséges az egységes telepítő futtatásakor. A kulcs a generálásától számított öt napig érvényes.

   ![A forrás beállítása](./media/tutorial-vmware-to-azure/source-settings.png)

### <a name="set-up-the-configuration-server"></a>A konfigurációs kiszolgáló beállítása

1. Futtassa az egyesített telepítő fájlját.
2. A **előkészületek**, jelölje be **a konfigurációs kiszolgáló és a folyamatkiszolgáló telepítése** kattintson **következő**.

3. A **harmadik féltől származó szoftverek licenc**, kattintson a **elfogadom** töltse le és telepítse a MySQL, majd kattintson a **következő**.

4. A **Regisztráció** területen válassza ki a kulcstartóból letöltött regisztrációs kulcsot.

5. Az **Internetbeállítások** területen adja meg, hogy a konfigurációs kiszolgálón futó Provider hogyan csatlakozzon az Azure Site Recoveryhez az interneten keresztül.

   - Ha szeretné csatlakoztatni a jelenleg be van állítva a számítógépen, válassza a proxy **csatlakozás az Azure Site Recovery proxykiszolgálóval**.
   - Ha azt szeretné, hogy a Provider közvetlenül kapcsolódjon, válassza ki a **Csatlakozás közvetlenül az Azure Site Recovery proxykiszolgáló nélkül**.
   - Ha a meglévő proxy hitelesítést igényel, vagy ha egyéni proxyt a szolgáltatói kapcsolat, válassza ki a használni kívánt **kapcsolódás egyéni proxybeállításokkal**, és adja meg a címet, a port és a hitelesítő adatokat.

   ![Tűzfal](./media/tutorial-vmware-to-azure/combined-wiz4.png)

6. Az **Előfeltételek ellenőrzése** területen a telepítő ellenőrzi, hogy a telepítés végrehajtható-e. Ha megjelenik egy figyelmeztetés a **globális időszinkron ellenőrzéséről**, ellenőrizze, hogy a rendszeróra ideje (a **Dátum és idő** beállítások) megegyeznek-e az időzónával.

   ![Előfeltételek](./media/tutorial-vmware-to-azure/combined-wiz5.png)

7. A **MySQL-konfiguráció** területen hozza létre a telepített MySQL-kiszolgálópéldányra való bejelentkezéshez szükséges hitelesítő adatokat.

8. A **környezet részletei**, jelölje be **Igen** VMware virtuális gépek védelméhez. A telepítő ellenőrzi, hogy telepítve van-e a PowerCLI 6.0.

9. A **Telepítés helye** területen válassza ki, hová szeretné telepíteni a bináris fájlokat, és hol kívánja tárolni a gyorsítótárat. A kiválasztott meghajtón legalább 5 GB szabad lemezterületre van szükség, de javasoljuk, hogy a gyorsítótárazáshoz használt lemezen legyen legalább 600 GB szabad hely.

10. A **Hálózat kiválasztása** területen adja meg a figyelőt (hálózati adaptert és SSL-portot), amelyen keresztül a konfigurációs kiszolgáló küldi és fogadja a replikált adatokat. A 9443-as port a replikációs forgalom küldésére és fogadására használt alapértelmezett port, ez azonban a környezeti követelményektől függően módosítható. Azt is megnyithatja kell levezényelni a replikálási műveletek használt 443-as porton. Ne használja a 443-as porton és-replikációs forgalom fogadására.

11. Az **Összefoglalás** területen ellenőrizze az adatokat, majd kattintson a **Telepítés** gombra. A telepítő telepíti a konfigurációs kiszolgáló, és regisztrálja azt az Azure Site Recovery szolgáltatásban.

    ![Összefoglalás](./media/tutorial-vmware-to-azure/combined-wiz10.png)

    A telepítés után a rendszer létrehoz egy hozzáférési kódot. Erre szüksége lesz, amikor engedélyezi a replikálást, ezért másolja le, és tárolja biztonságos helyen. A kiszolgáló megjelenik a **beállítások** > **kiszolgálók** ablaktábla a tárolóban lévő állapottal.

### <a name="configure-automatic-discovery"></a>Automatikus felderítés konfigurálása

Virtuális gépek felderítése, a konfigurációs kiszolgáló kapcsolódnia kell a helyszíni VMware-kiszolgálók. Ez az oktatóanyag céljából vegye fel a vCenter-kiszolgáló vagy vSphere-gazdagép, egy olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik a kiszolgálón. Ehhez a fiókhoz létrehozott a [az oktatóanyag előző](tutorial-prepare-on-premises-vmware.md). 

Vegye fel a fiókot:

1. A virtuális gép konfigurációs kiszolgálón indítsa el a **CSPSConfigtool.exe**. A parancsikonja elérhető az asztalon, az alkalmazás pedig a *telepítési hely*\home\svsystems\bin mappában található.

2. Kattintson a **Fiókok kezelése** > **Fiók hozzáadása** elemre.

   ![Fiók hozzáadása](./media/tutorial-vmware-to-azure/credentials1.png)

3. A **Fiókadatok** területen adja hozzá az automatikus felderítéshez használandó fiókot.

   ![Részletek](./media/tutorial-vmware-to-azure/credentials2.png)

A VMware-kiszolgáló hozzáadása:

1. Nyissa meg a [Azure-portálon](https://portal.azure.com) , majd kattintson a **összes erőforrás**.
2. Kattintson a helyreállítási szolgáltatás tárolójából nevű a **ContosoVMVault**.
3. Kattintson a **helyreállítási hely** > **infrastruktúra előkészítése** > **forrás**
4. Válassza ki **+ vCenter**, egy vCenter-kiszolgáló vagy vSphere ESXi gazdagéphez való csatlakozásra.
5. A **vCenter hozzáadása**, adja meg a kiszolgáló rövid nevét. Ezt követően adja meg az IP-cím vagy teljes Tartománynevét.
6. Hagyja a értékre a 443-as portot, kivéve, ha a VMware-kiszolgálók a kérelmeket egy másik port figyelésére.
7. Válassza ki a kiszolgálóhoz való csatlakozáshoz használandó fiókot. Kattintson az **OK** gombra.

A Site Recovery VMware-kiszolgálók, a megadott beállítások csatlakozik, és felderíti a virtuális gépek.

> [!NOTE]
> Akár 15 vagy még több percbe is beletelhet, amíg a fiók neve megjelenik a portálon. Az azonnali frissítéshez kattintson a **Konfigurációs kiszolgálók** > ***kiszolgáló neve*** > **Kiszolgáló frissítése** elemre.

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki, és ellenőrizze a tároló erőforrásait.

1. Kattintson az **Infrastruktúra előkészítése** > **Cél** elemre, majd válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg-e a cél telepítési modell a Resource Manager-alapú, vagy a klasszikus.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

   ![Cél](./media/tutorial-vmware-to-azure/storage-network.png)

## <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

1. Nyissa meg a [Azure-portálon](https://portal.azure.com) , majd kattintson a **összes erőforrás**.
2. Kattintson a helyreállítási szolgáltatás tárolójából nevű a **ContosoVMVault**.
3. A replikációs házirend létrehozásához kattintson a **Site Recovery-infrastruktúra** > **replikációs házirendek** > **+ replikációs házirend**.
4. A **replikációs házirend létrehozása**, megadhatja a házirend nevét **VMwareRepPolicy**.
5. A **helyreállítási Időkorlát küszöbértéke**, használja az alapértelmezett 60 perc. Az értéke határozza meg, milyen gyakran helyreállítási pontot hoz létre. Riasztás akkor jön létre, ha a folyamatos replikálásra túllépi ezt a korlátozást.
6. A **helyreállításipont-megőrzést**, használja az alapértelmezett 24 órás mennyi az adatmegőrzési időtartam nem az egyes helyreállítási pontok. Ebben az oktatóanyagban válassza azt 72 óra. A replikált virtuális gépek bármelyik pontra ablakban állíthatók helyre.
7. A **alkalmazáskonzisztens pillanatkép gyakorisága**, 60 perc az alapértelmezett használni, hogy vannak-e létre alkalmazáskonzisztens pillanatképek gyakorisága. Kattintson a **OK** a szabályzat létrehozásához.

   ![Szabályzat](./media/tutorial-vmware-to-azure/replication-policy.png)

A házirend automatikusan tartozik a konfigurációs kiszolgáló. Alapértelmezés szerint a megfelelő házirend automatikusan létrejön a feladat-visszavételre. Például, ha a replikációs házirend **rep-házirend** akkor rendszer a feladat-visszavétel házirend **rep-házirend-feladat-visszavétel**. Ez a házirend nem használ, amíg nem indítja el a feladat-visszavétel az Azure-ból.

## <a name="enable-replication"></a>A replikáció engedélyezése

A Site Recovery a mobilitási szolgáltatást telepíti, ha a virtuális gépek engedélyezve van a replikáció. Képes 15 percet vesz igénybe, vagy már érvényesíti a módosítások életbe és jelenik meg a portálon.

Replikáció engedélyezése az alábbiak szerint:

1. Kattintson a **alkalmazás replikálása** > **forrás**.
2. A **forrás**, válassza ki a konfigurációs kiszolgálót.
3. A **számítógép típusú**, jelölje be **virtuális gépek**.
4. A **vCenter vagy vSphere Hipervizorra**jelölje be a vCenter-kiszolgáló, amely kezeli a vSphere-gazdagép, vagy jelölje ki a gazdagépet.
5. Válassza ki a folyamat (konfigurációs kiszolgáló). IThen kattintson **OK**.
6. A **cél**, válassza ki az előfizetés és az erőforráscsoport, amelyben a nem sikerült létrehozni a virtuális gépek keresztül szeretné. A (klasszikus vagy erőforrás-kezelés), az Azure-ban használni kívánt telepítési modell kiválasztása a feladatait átadó virtuális gépeket.
7. Válassza ki az adatok replikálásához használni kívánt Azure-tárfiókot.
8. Válassza ki azt az Azure-hálózatot, valamint alhálózatot, amelyhez a feladatátvételt követően felálló Azure virtuális gépek csatlakozni fognak.
9. Ha a megadott hálózati beállításokat az összes védelemre kijelölt gépre szeretné alkalmazni, válassza a **Beállítás most a kijelölt gépekhez** lehetőséget. Ha az egyes gépeknél külön-külön szeretné beállítani az Azure-hálózatot, kattintson a **Beállítás később** elemre.
10. A **Virtuális gépek** > **Virtuális gépek kijelölése** menüben kattintással jelölje ki a replikálni kívánt virtuális gépeket. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Ezután kattintson az **OK** gombra.
11. A **tulajdonságok** > **tulajdonságainak konfigurálása**, válassza ki a fiókot, amelyek használják a folyamatkiszolgáló automatikusan telepíteni a mobilitási szolgáltatás a számítógépen.
12. A **replikációs beállítások** > **replikáció beállításainak konfigurálása**, győződjön meg arról, hogy a megfelelő replikációs házirend van-e kiválasztva.
13. Kattintson a **engedélyezze a replikálást**.

Előrehaladásának nyomon követheti a **Védelemengedélyezési** feladat **beállítások** > **feladatok** > **Site Recovery-feladatok**. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.

Figyelheti a virtuális gépeket ad hozzá, ellenőrizheti a legutóbb felfedezett a virtuális gépek **konfigurációs kiszolgálók**
> **Az utolsó Kapcsolatfelvétel**. Az ütemezett felderítési várakozás nélkül adja hozzá a virtuális gépeket, jelölje ki a konfigurációs kiszolgálót (ne kattintson azt), és kattintson **frissítése**.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](site-recovery-test-failover-to-azure.md)
