---
title: VMware virtuális gépek újbóli védelme egy helyszíni webhelyen az Azure Site Recovery segítségével
description: Ismerje meg, hogyan védheti újra a VMware virtuális gépeket az Azure Site Recovery azure-beli feladatátvétel után.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 976888f57269cc9fe6107a38e30d78c73eb5c124
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257172"
---
# <a name="reprotect-from-azure-to-on-premises"></a>Ismételt védelem – Azure-ból helyszíni rendszerbe

A helyszíni VMware virtuális gépek vagy fizikai kiszolgálók Azure-ba [való feladatátvétele](site-recovery-failover.md) után az első lépés a helyszíni helyre való visszalépéssorán a feladatátvétel során létrehozott Azure virtuális gépek újbóli védelme. Ez a cikk ennek módját ismerteti. 

## <a name="before-you-begin"></a>Előkészületek

1. A [cikkben](vmware-azure-prepare-failback.md) leírt lépéseket követve előkészítheti az újravédelmet és a feladat-visszavételt, beleértve egy folyamatkiszolgáló és egy helyszíni főcélkiszolgáló beállítását, valamint a helyek közötti VPN vagy expressroute-alapú privát társviszony-létesítés konfigurálását a feladat-visszavételhez.
2. Győződjön meg arról, hogy a helyszíni konfigurációs kiszolgáló fut, és csatlakozik az Azure-hoz. Az Azure-ba való feladatátvétel során előfordulhat, hogy a helyszíni hely nem érhető el, és a konfigurációs kiszolgáló nem érhető el, vagy leáll. A feladat-visszavétel során a virtuális gépnek léteznie kell a konfigurációs kiszolgáló adatbázisában. Ellenkező esetben a feladat-visszavétel sikertelen.
3. Törölje a helyszíni főcélkiszolgálón lévő pillanatképeket. Az újravédelem nem működik, ha vannak pillanatképek.  A virtuális gép pillanatképei automatikusan egyesülnek egy újravédelmi feladat során.
4. Ha a több virtuális gép konzisztenciája érdekében egy replikációs csoportba gyűjtött virtuális gépeket újra védi, győződjön meg arról, hogy mindegyik azonos operációs rendszerrel (Windows vagy Linux) rendelkezik, és győződjön meg arról, hogy a telepített fő célkiszolgáló azonos típusú operációs rendszerrel rendelkezik. A replikációs csoportban lévő összes virtuális gépnek ugyanazt a fő célkiszolgálót kell használnia.
5. Nyissa meg a feladat-visszavételhez [szükséges portokat.](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback)
6. Győződjön meg arról, hogy a vCenter-kiszolgáló csatlakoztatva van a feladat-visszavétel előtt. Ellenkező esetben a lemezek leválasztása és a virtuális géphez való visszacsatolása sikertelen.
7. Ha egy vCenter-kiszolgáló kezeli azokat a virtuális gépeket, amelyeknek a feladat-visszavétele lesz, győződjön meg arról, hogy rendelkezik a szükséges engedélyekkel. Ha csak olvasható felhasználói vCenter-felderítést hajt végre, és védi a virtuális gépeket, a védelem sikeres, és a feladatátvétel működik. Az újravédelem során azonban a feladatátvétel sikertelen, mert az adattárak nem fedezhetők fel, és nem jelennek meg az újravédelem során. A probléma megoldásához frissítse a vCenter hitelesítő adatait a [megfelelő fiókkal/engedélyekkel,](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)majd próbálkozzon újra a feladattal. 
8. Ha egy sablont használt a virtuális gépek létrehozásához, győződjön meg arról, hogy minden virtuális gép saját UUID-val rendelkezik a lemezekhez. Ha a helyszíni virtuális gép UUID ütközik a fő célkiszolgáló UUID, mert mindkettő ugyanabból a sablonból jött létre, az újravédelem sikertelen lesz. Üzembe helyezés egy másik sablonból.
9. Ha nem sikerül visszaegy másik vCenter-kiszolgáló, győződjön meg arról, hogy az új vCenter-kiszolgáló és a fő célkiszolgáló felderítése. Általában, ha nem az adattárak nem érhető el, vagy nem látható a **Védelem újra .**
10. Ellenőrizze a következő eseteket, amelyekben nem lehet visszakapni a feladat-visszavételt:
    - Ha az ESXi 5.5 ingyenes kiadást vagy a vSphere 6 Hypervisor ingyenes kiadást használja. Frissítés másik verzióra.
    - Ha Windows Server 2008 R2 SP1 fizikai kiszolgálóval rendelkezik.
    - A VMware virtuális gépek nem tudnak visszaadni a Hyper-V-nek.
    - [Az áttelepített](migrate-overview.md#what-do-we-mean-by-migration)virtuális gépek .
    - Egy virtuális gép, amely átlett helyezve egy másik erőforráscsoportba.
    - Egy replikát az Azure virtuális gép, amely törölték.
    - Egy replikát erdei Azure virtuális gép, amely nem védett (replikálása a helyszíni helyre).
10. Tekintse át a használható [feladat-visszavételi típusokat](concepts-types-of-failback.md) – az eredeti hely-helyreállítást és az alternatív hely-helyreállítást.


## <a name="enable-reprotection"></a>Újravédelem engedélyezése

Engedélyezze a replikációt. Újra megvédhet bizonyos virtuális gépeket vagy helyreállítási tervet:

- Ha újra megvéd egy helyreállítási tervet, minden védett géphez meg kell adnia az értékeket.
- Ha a virtuális gépek több virtuális gép konzisztenciájának replikációs csoportjához tartoznak, csak helyreállítási tervvel védhetők újra. A replikációs csoportban lévő virtuális gépeknek ugyanazt a fő célkiszolgálót kell használniuk

### <a name="before-you-start"></a>Előkészületek

- Miután egy virtuális gép elindul az Azure-ban a feladatátvétel után, némi időt vesz igénybe az ügynök, hogy regisztráljon vissza a konfigurációs kiszolgálóra (legfeljebb 15 perc). Ez alatt az idő alatt nem lesz képes újravédeni, és egy hibaüzenet azt jelzi, hogy az ügynök nincs telepítve. Ha ez történik, várjon néhány percet, majd védekezz.
- Ha azt szeretné, hogy az Azure virtuális gép egy meglévő helyszíni virtuális gép, csatlakoztassa a helyszíni virtuális gép adattárak olvasási/írási hozzáféréssel a fő célkiszolgáló ESXi-gazdagép.
- Ha azt szeretné, hogy feladatátvételi rendszer egy másik helyre, például ha a helyszíni virtuális gép nem létezik, válassza ki a megőrzési meghajtó és adattár, amelyek a fő célkiszolgálóhoz konfigurálva vannak. Ha a feladat-vissza a helyszíni hely, a VMware virtuális gépek a feladat-visszavételi védelmi terv ben használja ugyanazt az adattára, mint a fő célkiszolgáló. Ezután létrejön egy új virtuális gép a vCenterben.

Engedélyezze az újravédelmet az alábbiak szerint:

1. Válassza a > **Tárolóreplikált elemek lehetőséget.** **Vault** Kattintson a jobb gombbal a feladatátvételt követő virtuális gépre, majd válassza **az Újravédelem**parancsot. Vagy a parancsgombokon jelölje ki a készüléket, majd válassza az **Újravédelem**lehetőséget.
2. Ellenőrizze, hogy az **Azure-helyszíni** védelmi irány van-e kiválasztva.
3. A **Fő célkiszolgáló** és **a folyamatkiszolgáló területen**válassza ki a helyszíni főcélkiszolgálót és a folyamatkiszolgálót.  
4. Az **Adattár ban**válassza ki azt az adattaráni, amelybe a helyszíni lemezeket helyre szeretné állítani. Ez a beállítás akkor használatos, ha a helyszíni virtuális gép törlődik, és új lemezeket kell létrehoznia. Ez a beállítás figyelmen kívül marad, ha a lemezek már léteznek. Továbbra is meg kell adnia egy értéket.
5. Válassza ki a megőrzési meghajtót.
6. A feladat-visszavételi szabályzat automatikusan ki van jelölve.
7. Az újravédelem megkezdéséhez válassza **az OK gombot.**

    ![Újravédelem párbeszédpanel](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Egy feladat elkezdi replikálni az Azure virtuális gép a helyszíni helyen. A **Feladatok** lapon követheti nyomon a folyamat állapotát.
    - Ha az újravédelem sikeres, a virtuális gép védett állapotba lép.
    - A helyszíni virtuális gép ki van kapcsolva az ismételt védelem alatt. Ez elősegíti az adatok konzisztenciáját a replikáció során.
    - Ne kapcsolja be a helyszíni virtuális gép után újravédelem befejeződik.
   

## <a name="next-steps"></a>További lépések

- Ha bármilyen problémát tapasztal, tekintse át a [hibaelhárítási cikket.](vmware-azure-troubleshoot-failback-reprotect.md)
- Az Azure-beli virtuális gépek védelme után [futtathat egy feladat-visszavételt.](vmware-azure-failback.md) A feladat-visszavétel leállítja az Azure virtuális gép, és a helyszíni virtuális gép. Várjon némi állásidőt az alkalmazás, és ennek megfelelően válassza ki a feladat-visszavételi idő.


