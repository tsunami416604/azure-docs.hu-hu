---
title: "A Site Recovery feladatátvételi |} Microsoft Docs"
description: "Az Azure Site Recovery koordinálja a replikálása, feladatátvétele és helyreállítási virtuális gépek és fizikai kiszolgálók. További információk a feladatátvétel az Azure vagy egy másodlagos adatközpontba."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/09/2018
ms.author: ponatara
ms.openlocfilehash: f7a60cd82508629ad3cf46882564aa68995ba3e6
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="failover-in-site-recovery"></a>Feladatátvétel a Site Recoveryben
Ez a cikk ismerteti, hogyan feladatátvételi virtuális gépek és fizikai kiszolgálók Site Recovery által védett.

## <a name="prerequisites"></a>Előfeltételek
1. Előtt végezzen feladatátvételi, tegye a [feladatátvételi teszt](site-recovery-test-failover-to-azure.md) annak érdekében, hogy minden a várt módon működik.
1. [Készítse elő a hálózati](site-recovery-network-design.md) előtt végezzen feladatátvételi célként megadott helyen.  

A következő táblázat használatával kapcsolatban az Azure Site Recovery feladatátvételi megoldásait. Ezek a beállítások másik feladatátvevő forgatókönyvek is fel vannak sorolva.

| Forgatókönyv | Alkalmazás helyreállítási követelményeknek | A Hyper-V munkafolyamata | VMware munkafolyamata
|---|--|--|--|
|Tervezett feladatátvétel miatt egy jövőbeli datacenter állásidő| Az alkalmazás egy tervezett tevékenység végrehajtásakor nulla adatveszteséget| Hyper-V, az automatikus rendszer-Helyreállítás replikálja az adatokat a felhasználó által megadott másolási gyakorisággal. Tervezett feladatátvétel gyakoriságát és replikálja a végleges módosítások előtt a rendszer feladatátvételt kezdeményez szolgál. <br/> <br/> 1.    Tervezze meg a karbantartási időszak szerint az üzleti változáskezelési folyamatot. <br/><br/> 2 értesítheti a felhasználókat a jövőbeli állásidő. <br/><br/> 3. Állítsa offline állapotba a felhasználók számára is elérhető alkalmazás.<br/><br/>4 kezdeményezze a tervezett feladatátvételt az ASR-portál használatával. A helyszíni virtuális gép automatikusan leállítási.<br/><br/>A hatékony adatvesztés = 0 <br/><br/>A helyreállítási pontok a napló egy felhasználóhoz, aki szeretné a régebbi helyreállítási pont használatára is találhatók egy adatmegőrzési időtartam. (Hyper-V adatmegőrzési 24 óra).| VMware ASR replikálja az adatokat, és folyamatosan CDP. Feladatátvevő feladatátvételi lehetőséget biztosít a felhasználók a legújabb adatokkal (ideértve a feladás egy vagy több alkalmazás leállítási)<br/><br/> 1. Tervezze meg a karbantartási időszak szerint a változáskezelési folyamatot <br/><br/>2 értesítheti a felhasználókat a várható állásidő <br/><br/>3.    Állítsa offline állapotba a felhasználók számára is elérhető alkalmazás. <br/><br/>4.  Indítsa el egy tervezett feladatátvételt a legutóbbi pontnak ASR portál használatával, miután az alkalmazás offline állapotban. Használja a "Nem tervezett feladatátvétel" beállítást a portálon, és válassza ki a feladatátvételt a legutóbbi pontnak. A helyszíni virtuális gép automatikusan leállítási.<br/><br/>A hatékony adatvesztés = 0 <br/><br/>A helyreállítási pontok megőrzési ablakban napló egy korábbi helyreállítási pontra használni kívánó ügyfél valósul meg. (VMware adatmegőrzési 72 óra).
|Feladatátvétel miatt egy nem tervezett datacenter állásidő (természetes vagy informatikai katasztrófa) | Az alkalmazás minimális adatvesztéssel | 1. kezdeményezzen a szervezet BCP terv <br/><br/>2. Indítsa el a nem tervezett feladatátvételt az ASR portálról a legújabb vagy egy pontot, a megőrzési időszak (napló) használatával.| 1. Kezdeményezzen a szervezet BCP terv. <br/><br/>2.  Indítsa el a nem tervezett feladatátvételt az ASR portálról a legújabb vagy egy pontot, a megőrzési időszak (napló) használatával.


## <a name="run-a-failover"></a>Feladatátvétel futtatása
Ez az eljárás ismerteti a feladatátvétel futtatása egy [helyreállítási terv](site-recovery-create-recovery-plans.md). Másik megoldásként futtathatja a feladatátvételt egyetlen virtuális gép vagy fizikai kiszolgáló a **replikált elemek** lap


![Feladatátvétel](./media/site-recovery-failover/Failover.png)

1. Válassza ki **helyreállítási tervek** > *recoveryplan_name*. Kattintson a **feladatátvételi**
2. Az a **feladatátvételi** képernyőn válassza ki a **helyreállítási pont** így. Az alábbi lehetőségek egyikét használhatja:
    1.  **Legújabb** (alapértelmezett): Ez a beállítás elindítja a feladatot, amely el lett küldve a Site Recovery szolgáltatás összes adatot feldolgozásával. Az adatok feldolgozását az egyes virtuális gépek helyreállítási pont létrehozása. Ez a helyreállítási pont a feladatátvétel során a virtuális gép használja. Ezt a lehetőséget biztosít a virtuális gép létrehozása után feladatátvétel van az adatok a feladatátvétel kiváltásakor a Site Recovery szolgáltatás replikálta a legalacsonyabb helyreállítási Időkorlát (helyreállítási időkorlát).
    1.  **Legújabb feldolgozott**: Ez a beállítás átadja a feladatokat az összes virtuális gép a legutóbbi helyreállítási pontot, amely a Site Recovery szolgáltatás feldolgozása már megtörtént a helyreállítási terv. A virtuális gép feladatátvételi tesztje során, a legutóbbi feldolgozott helyreállítási pontot időbélyegzőjét is látható. Akkor használatos, ha a helyreállítási terv feladatátvételi, nyissa meg az egyes virtuális géphez, és nézze meg **legújabb helyreállítási pontok** csempén ezt az információt. Nem töltött idő, akkor a feldolgozatlan adatok feldolgozására, ezt a lehetőséget biztosít egy alacsony RTO (helyreállítási idő célkitűzése) feladatátvételi beállítás.
    1.  **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás átadja a feladatokat az összes virtuális gép a legutóbbi alkalmazáskonzisztens helyreállítási pontnak, amely a Site Recovery szolgáltatás feldolgozása már megtörtént a helyreállítási terv. A virtuális gép feladatátvételi tesztje során, a legújabb alkalmazáskonzisztens helyreállítási pont időbélyegzőjét is látható. Akkor használatos, ha a helyreállítási terv feladatátvételi, nyissa meg az egyes virtuális géphez, és nézze meg **legújabb helyreállítási pontok** csempén ezt az információt.
    1.  **Legújabb virtuális Gépre kiterjedő feldolgozott**: Ez a beállítás csak érhető el helyreállítási tervek, amelyeken legalább egy virtuális gép több virtuális Gépre kiterjedő konzisztencia. Virtuális gépek részei egy replikációs csoport feladatátvételt a legutóbbi közös virtuális Gépre kiterjedő konzisztens helyreállítási pontot. Más virtuális gépek feladatátvétel a legújabb feldolgozott helyreállítási pontnak.  
    1.  **Legújabb virtuális Gépre kiterjedő alkalmazáskonzisztens**: Ez a beállítás csak érhető el, amelyek rendelkeznek legalább egy virtuális gép több virtuális Gépre kiterjedő konzisztencia ON helyreállítási tervek. Virtuális gépek részei egy replikációs csoport feladatátvételt a legutóbbi közös virtuális Gépre kiterjedő alkalmazáskonzisztens helyreállítási pont. Más virtuális gépek feladatátvétele, a legutóbbi alkalmazáskonzisztens helyreállítási pontnak.
    1.  **Egyéni**: Ha egy virtuális gép feladatátvételi tesztje során, akkor használhatja ezt a beállítást, így egy adott helyreállítási pontot.

    > [!NOTE]
    > Egy helyreállítási pontot választhat csak esetén érhető el az Azure-bA feladatátvétele.
    >
    >


1. Ha egyes virtuális gépek szerepelnek a helyreállítási tervben feladatátvételt volt az előző és a virtuális gépek aktívak a forrás- és a célként megadott helyen, most használhatja **irányának módosítása** beállítás döntse el, hogy a irányát, amelyben a feladatátvétel történjen.
1. Ha a feladat-visszavétele keresztül az Azure-ba, és a felhő (csak akkor, ha a védett Hyper-v virtuális gépek VMM-kiszolgálóról vonatkozik) engedélyezett az adattitkosítás a **titkosítási kulcs** válassza ki a tanúsítványt, ha engedélyezte az adattitkosítást a VMM-kiszolgálón a telepítés során ki.
1. Válassza ki **leállítási gépek a feladatátvétel megkezdése előtt** Ha azt szeretné, hogy a hely helyreállításával lehet engedélyezi ezt a forrás virtuális gépek leállítása a feladatátvétel elindítása előtt. Feladatátvételi továbbra is fennáll, akkor is, ha a leállítási sikertelen lesz.  

    > [!NOTE]
    > Ha a védett Hyper-v virtuális gépek, leállítási is lehetőséget megpróbálja van még nem lettek elküldve a szolgáltatás ahhoz, hogy kiváltsa a feladatátvétel a helyszíni adatok szinkronizálása.
    >
    >

1. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon. Akkor is, ha hiba történik, nem tervezett feladatátvétel során, a helyreállítási terv fut, amíg nem fejeződik be.
1. A feladatátvétel után ellenőrizze a virtuális gép naplózási-e az azt. Ha szeretne a virtuális gép egy másik helyreállítási pontra váltani, akkor is használhatja **helyreállítási pont módosítása** lehetőséget.
1. Ha elégedett a feladatátviteli virtuális géppel, **véglegesítheti** a feladatátvételt. **Véglegesítési törli az összes elérhető helyreállítási pontok megtekintéséhez a szolgáltatással** és **helyreállítási pont módosítása** beállítás már nem érhető el.

## <a name="planned-failover"></a>Tervezett feladatátvétel
Virtuális gépek/fizikai kiszolgálók is támogatja a Site Recovery használatával védett **tervezett feladatátvétel**. Tervezett feladatátvétel beállítás nulla adatok elvesztését feladatátvételi. Egy tervezett feladatátvételt akkor váltódik ki, ha először a forrás virtuális gépek leállítási, a legújabb adatok szinkronizálódnak és a feladatátvétel aktiválódik, majd.

> [!NOTE]
> A Hyper-v virtuális gépek egy helyszíni hely helyszíni egy másik helyre a feladatátvételi térjen vissza a helyszíni elsődleges helyet kell először **névkeresési-replikálás** a virtuális gép biztonsági elsődleges helyhez, majd indítás, feladatátvétel. Ha az elsődleges virtuális gép nem áll rendelkezésre, majd elindítása előtt, hogy **névkeresési-replikálás** vissza kell állítani a virtuális gép egy biztonsági másolatból.   
>
>
## <a name="failover-job"></a>Feladatátvételi feladatban

![Feladatátvétel](./media/site-recovery-failover/FailoverJob.png)

Amikor elindul a feladatátvétel, magában foglalja a következő lépéseket:

1. Előfeltételek ellenőrzése: Ez a lépés biztosítja, hogy a feladatátvétel szükséges összes feltétel teljesül-e
1. Feladatátvétel: Ebben a lépésben fel az adatokat, így készen, hogy egy Azure virtuális gépek hozhatók létre belőle. Ha úgy döntött, **legújabb** helyreállítási pont ebben a lépésben létrehoz egy helyreállítási pontot, amely a szolgáltatás el lett küldve az adatokból.
1. Kezdete: Ebben a lépésben létrehoz az előző lépésben feldolgozott adatokat használó Azure virtuális géphez.

> [!WARNING]
> **Ne szakítsa meg az egy folyamatban lévő feladatátvételi**: elindítani a feladatátvételt, mielőtt a virtuális gép replikálása leállt. Ha Ön **Mégse** egy folyamatban lévő feladat, a feladatátvétel leállítja, de a virtuális gép nem fog elindulni replikálásához. Nem lehet újból elindítani a replikációt.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Feladatátvétel az Azure-bA igénybe vett idő

Bizonyos esetekben virtuális gépeinek feladatátvételi egy extra köztes lépés, amely általában körülbelül 8 – 10 perc befejezéséhez szükséges. A következő esetekben feladatátvételi szükséges idő nagyobb, mint a szokásos lesz:

* VMware virtuális gépek használata a mobilitási szolgáltatás régebbi, mint 9.8 verzió
* Fizikai kiszolgálók
* VMware Linux virtuális gépek
* Fizikai kiszolgálóként védett Hyper-V virtuális gépek
* VMware virtuális gépek nincsenek jelen, ahol következő illesztőprogramok rendszerindító illesztőprogramok
    * storvsc
    * VMBus
    * storflt
    * Intelide
    * ATAPI
* VMware virtuális gépek, amelyek nem rendelkeznek a DHCP-szolgáltatás engedélyezve van, függetlenül attól, hogy használják DHCP vagy statikus IP-címek

Minden egyéb esetben a köztes lépés nem szükséges, a feladatátvételi idő azonban alacsonyabb.





## <a name="using-scripts-in-failover"></a>A feladatátvevő parancsfájlok használata
Előfordulhat, hogy automatizálni kívánt bizonyos műveleteket a feladatátvétel során. Parancsfájlokat vagy [Azure automation-forgatókönyveket](site-recovery-runbook-automation.md) a [helyreállítási tervek](site-recovery-create-recovery-plans.md) ehhez.

## <a name="post-failover-considerations"></a>POST feladatátvételi szempontokat részletező cikket
Vegye figyelembe a következőket érdemes feladatátvétel utáni:
### <a name="retaining-drive-letter-after-failover"></a>Feladatátvétel után meghajtóbetűjelet megőrzése
A meghajtóbetűjelet, a virtuális gépek a feladatátvételt követően megőrzéséhez beállíthatja a **TÁROLÓHÁLÓZATI szabályzatát** a virtuális gép **OnlineAll**. [További információk](https://support.microsoft.com/en-us/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure).



## <a name="next-steps"></a>További lépések

> [!WARNING]
> Miután a virtuális gépek átvette, és a helyszíni adatközpont érhető el, akkor [ **lássa el újból védelemmel** ](vmware-azure-reprotect.md) VMware virtuális gépek biztonsági a helyszíni adatközpont.

Használjon [ **tervezett feladatátvétel** ](hyper-v-azure-failback.md) kapcsoló használatával **feladat-visszavétel** Hyper-v virtuális gépek vissza a helyszíni az Azure-ból.

Ha nem sikerült keresztül a Hyper-v virtuális gép másik helyszíni VMM-kiszolgáló által kezelt adatközpont és az elsődleges adatközpont érhető el, majd **a visszirányú replikálás** beállítással indítsa el a replikációt az elsődleges adatközpont.
