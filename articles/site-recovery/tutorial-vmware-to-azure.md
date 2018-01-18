---
title: "Vész-helyreállítási Azure beállítása a helyszíni VMware virtuális gépek az Azure Site Recovery szolgáltatással |} Microsoft Docs"
description: "Megtudhatja, hogyan állíthatja be az Azure-bA vész-helyreállítási helyszíni VMware virtuális gépek az Azure Site Recovery szolgáltatással."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/15/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 8acc8deff8b635c97e8722d65a728aebf0e49bb3
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
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

Állítsa be a forrás környezetében, egyetlen, magas rendelkezésre állású, a helyi számítógépen, a gazdagép helyi Site Recovery-összetevőkkel van szükség. Összetevők közé tartoznak a konfigurációs kiszolgáló, a folyamatkiszolgáló és a fő célkiszolgálón.

- A konfigurációs kiszolgáló koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- A folyamatkiszolgáló egy replikációs átjáróként működik. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az Azure Storage-nak. Szeretne replikálni, virtuális gépeken is telepíti a mobilitási szolgáltatást a folyamatkiszolgáló és a helyszíni VMware virtuális gépek automatikus felderítést hajt végre.
- A fő célkiszolgáló az Azure-ból a feladat-visszavétel során kezeli a replikációs adatokat.

A konfigurációs kiszolgáló, a magas rendelkezésre állású VMware virtuális gépként beállításához előkészített OVF sablon letöltése, és importálja a sablont a VMware virtuális gép létrehozása. Miután beállította a konfigurációs kiszolgáló, akkor regisztrálja a tárolóban lévő állapottal. A regisztrációt követően a Site Recovery a helyszíni VMware virtuális gépek deríti fel.

### <a name="download-the-vm-template"></a>A Virtuálisgép-sablon letöltése

1. A tárolóban, Ugrás **infrastruktúra előkészítése** > **forrás**.
2. A **forrás előkészítése**, kattintson a **+ konfigurációs kiszolgáló**.
3. A **kiszolgáló hozzáadása**, ellenőrizze, hogy **VMware konfigurációs kiszolgáló** megjelenik **kiszolgálótípus**.
4. Töltse le a nyitott virtualizációs formátum (OVF) sablont a konfigurációs kiszolgáló.

  > [!TIP]
  A konfigurációs kiszolgáló sablon legújabb verziója letölthető közvetlenül [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

## <a name="import-the-template-in-vmware"></a>A VMware-sablon importálása

1. Jelentkezzen be a VMware vCenter-kiszolgáló vagy vSphere ESXi-állomáson, a VMWare vSphere Client használatával.
2. Az a **fájl** menüjében válassza **OVF-sablon telepítése**, azt a OVF-sablon központi telepítése varázsló elindításához.  

     ![OVF-sablon](./media/tutorial-vmware-to-azure/vcenter-wizard.png)

3. A **forrás kiválasztása**, adja meg a letöltött OVF helyét.
4. A **tekintse át a részletek**, kattintson a **következő**.
5. A **név és a mappa kiválasztása**, és **válassza konfigurációs**, fogadja el az alapértelmezett beállításokat.
6. A **válassza ki a tárolási**, amely a legjobb teljesítmény érdekében válasszon **vastag rendelkezés különösen nullázni** a **jelölje be a virtuális lemez formátum**.
4. A varázsló utasításait a többi fogadja el az alapértelmezett beállításokat.
5. A **kész befejezni**:
  - A virtuális Gépet, az alapértelmezett beállításokkal, válassza ki **kapcsolja be a telepítést követően** > **Befejezés**.
  - Ha hozzá szeretne adni egy hálózati illesztővel, törölje a jelet **kapcsolja be a telepítést követően**, majd válassza ki **Befejezés**. Alapértelmezés szerint a konfigurációs kiszolgáló sablon az egyetlen hálózati adapter van telepítve, de a telepítés után is hozzáadhat további hálózati adapterek.

  
## <a name="add-an-additional-adapter"></a>Vegyen fel egy további adaptert

Egy olyan további hálózati adapter hozzáadása a konfigurációs kiszolgáló, tegye, mielőtt regisztrálja a kiszolgálót a tárolóban lévő állapottal. Hozzáadás a felesleges adapterek regisztrálás után nem támogatott.

1. A vSphere Client készletben, kattintson a jobb gombbal a virtuális Gépet, és válassza ki **beállításainak szerkesztése**.
2. A **hardver**, kattintson a **Hozzáadás** > **Ethernet-Adapter**. Ezután kattintson a **Next** (Tovább) gombra.
3. Válassza ki és hálózatiadapter-típust, és a hálózaton. 
4. Csatlakozás a virtuális hálózati Adaptert a virtuális gép bekapcsolása, válassza ki **teljesítménnyel csatlakozását a**. Kattintson a **következő** > **Befejezés**, és kattintson a **OK**.


## <a name="register-the-configuration-server"></a>Regisztrálja a konfigurációs kiszolgálót. 

1. A VMWare vSphere ügyfél konzolról bekapcsolása a virtuális Gépet.
2. A virtuális Gépet egy Windows Server 2016 telepítésének tapasztalatokat indul el. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
3. A telepítés befejezése után jelentkezzen be rendszergazdaként, a virtuális Gépet.
4. Az első alkalommal jelentkezik be, az Azure Site Recovery Configuration Tool indít.
5. Adjon meg egy nevet, amellyel a konfigurációs kiszolgáló regisztrálása a Site Recovery szolgáltatással. Ezután kattintson a **Next** (Tovább) gombra.
6. Az eszköz ellenőrzi, hogy a virtuális gép Azure csatlakozhat. A kapcsolat létrejötte után kattintson **bejelentkezés**, hogy jelentkezzen be az Azure-előfizetéshez. A hitelesítő adatokat a tárolóba, ahol a konfigurációs kiszolgáló regisztrálni kívánt hozzáféréssel kell rendelkeznie.
7. Az eszköz bizonyos konfigurációs feladatokat hajtja végre, és majd újraindul.
8. A gép újra bejelentkezni. A konfigurációs kiszolgáló kezelése varázsló automatikusan elindul.

### <a name="configure-settings-and-connect-to-vmware"></a>Beállítások konfigurálása, és kapcsolódjon a VMware

1. A konfigurációs kiszolgáló kezelése varázsló > **kapcsolat beállítása**, válassza ki a hálózati Adaptert, amelyek megkapják a replikációs forgalmat. Ezután kattintson a **Save** (Mentés) gombra. Ez a beállítás nem módosítható, miután van konfigurálva.
2. A **válassza ki a Recovery Services-tároló**, válassza ki az Azure-előfizetéshez, és a megfelelő erőforráscsoport és a tároló.
3. A **harmadik féltől származó szoftverek telepítése**fogadja el a licenc agreeemtn, és kattintson a **töltse le és telepítse**, MySQL-kiszolgáló telepítéséhez.
4. Kattintson a **telepíteni a VMware PowerLCI**. Győződjön meg arról, hogy az összes böngészőablakot be van zárva, mielőtt ezt megtehetné. Kattintson a **Folytatás**
5. A **ellenőrzése készülék konfigurációs**, Előfeltételek a rendszer ellenőrzi a folytatás előtt.
6. A **vCenter-kiszolgáló vagy vSphere ESXi kiszolgáló konfigurálása**, adja meg az FQDN vagy IP-címet a vCenter-kiszolgáló vagy vSphere-gazdagép, mely virtuális gépeken a replikálni kívánt találhatók. Adja meg a portot, amelyet a kiszolgáló figyel, és egy rövid nevet használt a VMware-kiszolgáló újraregisztrálásával a felhőben.
7. Adja meg a konfigurációs kiszolgáló által a VMware-kiszolgálóhoz való csatlakozáshoz használandó hitelesítő adatokat. A Site Recovery automatikusan észlelni a VMware virtuális gépek, amelyek érhető(k) el replikálásra ezeket a hitelesítő adatokat használja. Kattintson a **Hozzáadás**, és kattintson a **Folytatás**.
8. A **virtuális gép hitelesítő adatok beállítása**, adja meg a felhasználónevet és jelszót, hogy a mobilitási szolgáltatás automatikusan telepítse a gépeken, ha engedélyezve van a replikáció történik. Windows-alapú gépek a fiók helyi rendszergazdai jogosultságokkal a replikálni kívánt gépeken kell. Linux adja meg a rendszergazdafiók részletei.
9. Kattintson a **véglegesítő konfigurációs** és elvégezheti a regisztrálást. 
10. Az Azure portálon, a regisztráció befejezését követően ellenőrizze, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepel a **forrás** lap a tárolóban lévő állapottal. Kattintson a **OK** tároló beállításainak konfigurálása.


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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](site-recovery-test-failover-to-azure.md)
