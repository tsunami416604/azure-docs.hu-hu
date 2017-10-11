---
title: "A Hyper-V replikálás az Azure Site Recovery egy másodlagos hely replikációs házirend beállítása |} Microsoft Docs"
description: "Ismerteti, hogyan állíthatja be az Azure Site Recovery VMM másodlagos hely Hyper-V virtuális gép replikációs házirend."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5d9b79cf-89f2-4af9-ac8e-3a32ad8c6c4d
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: a3ecc555e049914d7bffdddce7b0522f09362d24
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="step-8-set-up-a-replication-policy"></a>8. lépés: A replikációs házirend beállítása

Konfigurálása után [hálózatleképezés](vmm-to-vmm-walkthrough-network-mapping.md), ez a cikk használja a Hyper-V virtuális gép (VM) replikáció egy másodlagos helyre, egy replikációs házirendnek használatával [Azure Site Recovery](site-recovery-overview.md).

A cikk elolvasása után felmerülő megjegyzéseit alul vagy az [Azure Recovery Services fórumban](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti közzé.


## <a name="before-you-start"></a>Előkészületek

- Amikor egy replikációs házirendet hoz létre, a házirend az összes állomást ugyanazt az operációs rendszert kell rendelkeznie. A VMM-felhő csak Windows Server különböző verzióit futtató Hyper-V-gazdagépek, de ebben az esetben szüksége több replikációs házirend.
- A kezdeti replikálás offline végezheti el.

## <a name="configure-replication-settings"></a>Replikációs beállítások konfigurálása

1. Új replikációs szabályzat létrehozásához kattintson az **Infrastruktúra előkészítése** > **Replikációs beállítások** > **+Létrehozás és társítás** elemre.

    ![Network (Hálózat)](./media/vmm-to-vmm-walkthrough-replication/gs-replication.png)
2. A **Házirend létrehozása és társítása** beállításnál adja meg a szabályzat nevét. A forrás és cél típusnak kell lennie **Hyper-V**.
3. A **Hyper-V gazdagép verziószámánál**, válassza ki, melyik operációs rendszer fut az állomáson.
4. A **hitelesítési típus** és **hitelesítési portot**, adja meg, hogy az elsődleges és a helyreállítási Hyper-V gazdakiszolgálók közötti forgalom hitelesítése. Válassza ki **tanúsítvány** kivéve, ha Kerberos-környezetben működő rendelkezik. Az Azure Site Recovery automatikusan HTTPS-hitelesítési tanúsítványok konfigurálja. Nem kell manuálisan semmit. Alapértelmezés szerint a Windows tűzfalat a Hyper-V gazdakiszolgálók port 8083 és 8084 (a tanúsítványok) fognak megnyílni. Ha **Kerberos**, a Kerberos jegyet a gazdakiszolgálók a kölcsönös hitelesítéshez használható. Vegye figyelembe, hogy ez a beállítás csak a Windows Server 2012 R2 rendszeren futó Hyper-V gazdakiszolgálók szükséges.
5. A **Másolás gyakorisága** elemmel meghatározhatja, hogy milyen gyakran szeretné replikálni a módosult adatokat a kezdeti replikációt követően (ez lehet 30 másodperc, 5 perc vagy 15 perc).
6. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy hány órás legyen az egyes helyreállítási pontok adatmegőrzési időtartama. A védelemmel ellátott gépeket az időtartamon belüli bármelyik pontra visszaállíthatja.
7. A **alkalmazáskonzisztens pillanatkép gyakorisága**, adja meg, hogy milyen gyakran (1 – 12 órába) helyreállítási pontokat tartalmazó alkalmazáskonzisztens pillanatképeket jönnek létre. A Hyper-V két különböző pillanatképtípust használ: az egyik a standard pillanatkép, amely a virtuális gép egészét lefedő növekményes pillanatképet, a másik pedig az alkalmazáskonzisztens pillanatkép, amely a virtuális gépen futó alkalmazások adatairól készült, időponthoz kötött pillanatképet jelent. Az alkalmazáskonzisztens pillanatképek a kötet árnyékmásolása szolgáltatás (VSS) segítségével garantálják, hogy az alkalmazások konzisztens állapotban legyenek a pillanatkép készítésének időpontjában. Ha engedélyezi az alkalmazáskonzisztens pillanatképeket, az hatással a forrás virtuális gépeken futó alkalmazások teljesítményére. Ügyeljen rá, hogy az itt megadott érték kisebb legyen a további beállított helyreállítási pontok számánál.
8. A **adatátvitel tömörítésének**, adja meg, hogy van-e a replikált adatok tömörített.
9. Válassza ki **replika virtuális gép törlése**adja meg, hogy a replika virtuális gépet törölni kell a forrás virtuális gép védelmének letiltásakor. Ha a forrás virtuális gép el van távolítva a Site Recovery konzolján védelmének letiltásakor engedélyezi ezt a beállítást, a Site Recovery beállításait a VMM el lesznek távolítva a VMM-konzolon, és a replika törlése.
10. A **kezdeti replikációs módszer**, ha a hálózaton keresztül, replikál adja meg, hogy a kezdeti replikáció elindítása, vagy átütemezheti azt. Ha a hálózati sávszélesség, érdemes ütemezni, amikor kevesen használják az kívül. Ezután kattintson az **OK** gombra.

     ![Replikációs szabályzat](./media/vmm-to-vmm-walkthrough-replication/gs-replication2.png)
11. Az újonnan létrehozott szabályzatokat a rendszer automatikusan társítja a VMM-felhővel. A **replikációs házirend**, kattintson a **OK**. További VMM-felhőket (és a virtuális gépek őket) társíthatja a replikációs házirendet a **replikációs** > szabályzat neve > **VMM-felhő társítása**.

     ![Replikációs szabályzat](./media/vmm-to-vmm-walkthrough-replication/policy-associate.png)



## <a name="prepare-for-offline-initial-replication"></a>Offline kezdeti replikálás előkészítése

A kezdeti másolat offline replikációt teheti meg. Előkészítheti a következőképpen:

* A forráskiszolgálón adjon meg egy elérési helyet, ahonnan az adatok exportálása akkor kerül sor. Rendelje hozzá a teljes hozzáférés NTFS és megosztási engedélyek az Exportálás elérési úton a VMM szolgáltatást. A célkiszolgálón adjon meg egy elérési helyet, ahonnan az adatok importálását történik. Rendelje hozzá az importálási útvonalat ugyanazokkal az engedélyekkel.
* Ha az importálási vagy exportálási elérési út megosztása, rendelje hozzá a távoli számítógépen, amelyen a megosztott megtalálható a VMM szolgáltatás fiókja rendszergazda, a kiemelt felhasználó, a Nyomtatófelelősök vagy a kiszolgáló operátor csoporttagság.
* A futtató fiókokat vegyen fel a gazdagépet, az importálási és exportálási útvonalakra, rendelje hozzá az olvasási és írási engedéllyel a futtató fiókokat a VMM-ben való használata.
* Az importálási és exportálási megosztások nem kell található egy Hyper-V gazdagép-kiszolgálón, amely minden olyan számítógépen, mert visszacsatolási konfiguráció nem támogatott a Hyper-v.
* Az Active Directory minden Hyper-v gazdagép-kiszolgálón szeretné védeni, akkor engedélyezze és konfigurálja a virtuális gépeket tartalmazó által korlátozott delegálás hogy bízzon meg a távoli számítógépeken, amelyeken az importálási és exportálási útvonalak találhatók, az alábbiak szerint:
  1. A tartományvezérlőn nyissa meg a **Active Directory – felhasználók és számítógépek**.
  2. A konzolfán kattintson **tartománynév** > **számítógépek**.
  3. Kattintson a jobb gombbal a Hyper-V gazdagép-kiszolgálónév > **tulajdonságok**.
  4. Az a **delegálás** lapra, majd **a számítógépen csak a megadott szolgáltatások delegálhatók**.
  5. Kattintson a **bármely hitelesítési protokoll**.
  6. Kattintson a **hozzáadása** > **felhasználók és számítógépek**.
  7. Írja be a nevét, a számítógép, amelyen az Exportálás elérési útja > **OK**. Választható szolgáltatások közül, tartsa lenyomva a CTRL billentyűt, és kattintson a **cifs** > **OK**. Ismételje meg a számítógép, amelyen az importálási útvonalat nevét. Ismételje meg a Hyper-V-gazdagép további kiszolgálókat a megfelelő.



## <a name="next-steps"></a>Következő lépések

Ugrás a [9. lépés: engedélyezze a replikálást](vmm-to-vmm-walkthrough-enable-replication.md).
