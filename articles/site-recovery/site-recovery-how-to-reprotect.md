---
title: "Lássa el újból védelemmel az Azure-ból a helyszíni helyhez |} Microsoft Docs"
description: "Az Azure virtuális gépek feladatátvétel után a feladat-visszavételt a virtuális gépeket kapcsolja vissza a helyszíni is kezdeményezhető. Útmutató: a feladat-visszavétel előtt lássa el újból védelemmel."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: ruturajd
ms.openlocfilehash: 3644b41c3e3293a263bd9ff996d4e3d26417aeed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="reprotect-from-azure-to-an-on-premises-site"></a>Lássa el újból védelemmel az Azure-ból a helyszíni helyhez



## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti a lássa el újból védelemmel egy helyszíni hely Azure-ból az Azure virtuális gépeket. Kövesse az utasításokat ebben a cikkben, amikor készen áll a sikertelen biztonsági a VMware virtuális gépeket vagy windowsos/Linuxos fizikai kiszolgálók után azok feladatátvételt is a helyszíni hely Azure-ba (lásd: [replikálása VMware virtuális gépek és fizikai kiszolgálók Azure-bA az Azure Site Recovery](site-recovery-failover.md)).

> [!WARNING]
> Feladat-visszavétel nem, miután vagy [áttelepítésével kész](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), a virtuális gép áthelyezése a másik erőforráscsoportba vagy törölni az Azure virtuális géphez. Ha letiltja a virtuális gép védelme, feladat-visszavétel nem.


Miután ismételt védelem befejeződik, és a védett virtuális gépeket replikál, a feladat-visszavételt a virtuális gépeken, hogy azok a helyszíni hely is kezdeményezhető.

> [!NOTE]
> Beállíthatja, hogy csak a védelem-újrabeállítási és a feladat-visszavétel ESXi-állomáson. Feladat-visszavételt a virtuális gép a Hyper-v-gazdagépek, vagy a VMware munkaállomások, illetve bármely más virtualizációs platform nem lehet.

Megjegyzéseit vagy kérdéseit a cikk vagy a végén utáni a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Gyors áttekintését tekintse meg a következő videó bemutatja, hogyan hogy áthelyezze a feladatokat az Azure-ból a helyszíni-helyhez.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> Az Azure-bA a feladatátvétel során a helyszíni hely nem lehet elérni, és ezért a konfigurációs kiszolgáló lehet vagy nem érhető el vagy -leállítás. Védelem-újrabeállítási és a feladat-visszavétel során a helyszíni konfigurációs kiszolgáló fut, és egy csatlakoztatott OK állapotúnak kell lennie.


Állítsa a virtuális gépek előkészítésekor végrehajtása, vagy fontolja meg a következő előfeltételek műveleteket:

* Ha a vCenter-kiszolgáló kezeli a virtuális gépek visszaadják feladataikat kívánt, győződjön meg arról, hogy rendelkezik a [szükséges engedélyek](site-recovery-vmware-to-azure-classic.md) virtuális gépeket a vCenter-kiszolgálók felderítéséhez.

  > [!WARNING]
  > Ha pillanatképeket megtalálható a helyszíni fő célkiszolgáló vagy a virtuális gépet, ismételt védelem sikertelen lesz. Törölheti a pillanatképek a fő célkiszolgáló lássa el újból védelemmel a folytatás előtt. A pillanatképek a virtuális gépen a védelem-újrabeállítási feladat során automatikusan összefésülése megtörténjen.

* Ahhoz, hogy feladat-visszavételt, hozzon létre két további összetevőket:

  * **Folyamatkiszolgáló**: A [folyamatkiszolgáló](site-recovery-vmware-setup-azure-ps-resource-manager.md) adatokat fogad a védett virtuális gép az Azure-ban, és adatokat küld a helyszíni hely. A kis késleltetésű hálózatra azért szükség a folyamatkiszolgáló és a védett virtuális gép között. Így akkor is egy helyszíni folyamatkiszolgáló használata Azure ExpressRoute kapcsolat vagy egy Azure-alapú folyamatkiszolgáló és a VPN-en.
  
  * **Fő célkiszolgáló**: A fő célkiszolgáló kap feladat-visszavétel adatokat. A helyi felügyeleti kiszolgáló, amelyet hozott létre a fő célkiszolgálón, alapértelmezés szerint telepítve van. Azonban nem sikerült visszaírt forgalom mennyiségét, attól függően szükség lehet egy külön fő célkiszolgáló feladat-visszavételi létrehozásához.
    * [A Linux virtuális gép a beavatkozását Linux fő célkiszolgáló](site-recovery-how-to-install-linux-master-target.md).
    * Windows virtuális gépként kell egy Windows fő célkiszolgáló. Később újra felhasználhatja a helyszíni folyamat kiszolgáló és a fő célszámítógépekre.

    A fő célkiszolgáló rendelkezik egyéb szereplő Előfeltételek [közös ellenőrizze az alábbiakat, mielőtt védelem-újrabeállítási a fő célkiszolgáló](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server).

* A konfigurációs kiszolgáló szükségesek helyszíni esetén, a feladat-visszavételt. A feladat-visszavétel során a virtuális gép léteznie kell a konfigurációs kiszolgáló adatbázisában. Ellenkező esetben a feladat-visszavétel nem sikerül. 

> [!IMPORTANT]
> Győződjön meg arról, hogy az egy rendszeresen ütemezett biztonsági mentések a konfigurációs kiszolgáló. Katasztrófa esetén állítsa vissza a kiszolgáló ugyanazon IP-címét, hogy a feladat-visszavétel működik.

* Állítsa be a `disk.EnableUUID=true` beállítása a konfigurációs paraméterek, a fő célkiszolgáló virtuális gép VMware-ben. Ha a sor nem létezik, adja hozzá. Ez a beállítás szükséges, hogy azt a megfelelő csatlakoztatja, adja meg a virtuális gép lemezét (VMDK) való egységes UUID.

* *A fő célkiszolgáló nem használhatja a Storage vmotion szolgáltatások használata*. A sikertelen feladat-visszavétel okozhatnak. A virtuális gép nem indítható el, mert a lemez nem érhetők el azt. A probléma elkerülése érdekében zárja ki a fő célkiszolgálóra a vMotion listájáról.

* Adjon hozzá egy új meghajtót a fő célkiszolgáló: egy adatmegőrzési meghajtó. Adjon hozzá egy új lemezt, és formázza a meghajtót.


### <a name="frequently-asked-questions"></a>Gyakori kérdések

#### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-connection-to-replicate-data-back-to-the-on-premises-site"></a>Miért kell S2S VPN-en vagy ExpressRoute kapcsolat vissza a helyszíni hely replikálja az adatokat?
Mivel az Azure-bA helyszíni replikációs akkor fordulhat elő, az interneten vagy ExpressRoute kapcsolat, amely rendelkezik a nyilvános társviszony-létesítés, az ismételt védelem és a feladat-visszavétel szükséges a helyek (közötti S2S) VPN-en keresztül replikálja az adatokat. Adja meg a hálózati, így az Azure virtuális gép feladatátadása képes elérni (ping) a helyszíni konfigurációs kiszolgáló. A folyamatkiszolgáló az Azure-hálózat virtuális gép feladatátadása is telepíthet. Ez a folyamat-kiszolgáló képes kommunikálni a helyszíni konfigurációs kiszolgáló kell.

#### <a name="when-should-i-install-a-process-server-in-azure"></a>Mikor kell a folyamatkiszolgáló telepítése az Azure-ban?


A virtuális gépek Azure-lássa el újból védelemmel kívánt replikációs adatokat küldeni a folyamatkiszolgáló. A hálózat beállítása, hogy a virtuális gépek Azure-on érhető el a folyamatkiszolgáló.

Az Azure-ban a folyamat-kiszolgáló telepítéséhez, vagy használhatja a meglévő a folyamatkiszolgálót, feladatátvétel során. A fontos és megfontolandó szempont küldenek az Azure virtuális gépek a folyamatkiszolgálóra a késés.

Ha egy ExpressRoute-kapcsolat beállítása, használhatja egy helyszíni folyamatkiszolgáló szeretnék adatokat küldeni, mert a virtuális gép és a folyamatkiszolgáló között késleltetés alacsony.

 ![ExpressRoute architektúra diagramja](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



De ha csak egy S2S VPN-je, ajánlott az Azure-ban a folyamatkiszolgáló telepítése.

 ![VPN-architektúra ábrája](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


Ne feledje, hogy a replikáció az Azure-ból a helyszíni akkor fordulhat elő, csak az S2S VPN vagy a magánhálózati társviszony-létesítés az ExpressRoute-hálózat felett. Győződjön meg arról, hogy elegendő sávszélesség érhető el, hogy hálózati csatornán keresztül.

Azure-alapú folyamat kiszolgáló telepítésével kapcsolatos információkért lásd: [az Azure-ban folyamat kiszolgáló kezeléséhez](site-recovery-vmware-setup-azure-ps-resource-manager.md).

> [!TIP]
> Feladat-visszavétel során az Azure-alapú folyamatkiszolgáló használatát javasoljuk. A replikációs teljesítményt nagyobb, ha a folyamatkiszolgáló a replikáló virtuális gépre (a átvevő gép az Azure-ban) közelebb. Azonban megvalósíthatósági vagy bemutató a igazolása során használhatja a helyszíni folyamatkiszolgáló ExpressRoute együtt a magánhálózati társviszony-létesítés a Koncepció gyorsabb befejezéséhez.

> [!NOTE]
> Az Azure-bA helyszíni replikációs akkor fordulhat elő, csak az interneten vagy ExpressRoute keresztül a nyilvános társviszony-létesítés. Az Azure-ból a helyszíni replikációs akkor fordulhat elő, csak az S2S VPN- vagy ExpressRoute keresztül a magánhálózati társviszony-létesítés


#### <a name="what-ports-should-i-open-on-different-components-so-that-reprotection-can-work"></a>Mely portokat kell nyitható meg a különböző összetevőket, hogy ismételt védelemmel ellátni azt is működjenek?

![A feladatátvétel és a feladat-visszavétel portok](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

#### <a name="which-master-target-server-should-i-use-for-reprotection"></a>Mely fő célkiszolgáló kell használni az ismételt védelem?
A helyszíni fő célkiszolgáló fogadhat adatokat a folyamatkiszolgáló és a helyszíni virtuális gép VMDK majd írásához szükséges. Ha Windows virtuális gépek védelmét, egy Windows fő célkiszolgáló szüksége. Újrahasználhatja a helyszíni folyamat kiszolgáló és a fő cél<!-- !todo component -->. A Linux virtuális gépekhez akkor be kell állítania egy további helyszíni Linuxos fő célkiszolgáló.


A fő célkiszolgáló telepítésével kapcsolatos információkért lásd:

* [Windows fő célkiszolgáló telepítése](site-recovery-vmware-to-azure.md)
* [Linux fő célkiszolgáló telepítése](site-recovery-how-to-install-linux-master-target.md)


#### <a name="what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback"></a>Datastore milyen feladat-visszavétel során a helyszíni ESXi-állomáson támogatottak?

Azure Site Recovery jelenleg csak egy virtuális gép fájlrendszer (virtuálisgép-Fájlrendszereinek) vagy a vSAN-adattároló a visszavétele támogatja. NFS-adattároló nem támogatott. Ez a korlátozás miatt az adattároló kijelölés adjon meg a védelem-újrabeállítási a képernyő NFS datastores esetén üres, vagy jeleníti meg a vSAN-adattárolóban, de a feldolgozás során nem sikerül. Ha azt tervezi, hogy a feladat-visszavételt, hozzon létre egy virtuálisgép-Fájlrendszereinek datastore a helyszínen, és visszaadják feladataikat azt. A feladat-visszavétel, akkor a vmdk-fájl teljes letöltésére.

### <a name="common-things-to-check-after-completing-installation-of-the-master-target-server"></a>Közös ellenőrizze az alábbiakat, a fő célkiszolgáló telepítése után

* Ha a virtuális gép jelen helyszíni a vCenter Server, a fő célkiszolgáló szüksége a helyszíni virtuális gép vmdk-fájl elérésére. Hozzáférés szükséges a virtuális gépek lemezeit a replikált adatokat írni. Győződjön meg arról, hogy a helyszíni virtuális gép adattároló csatlakoztatva van-e a fő célkiszolgáló gazdagépre olvasási/írási hozzáférést.

* Ha a virtuális gép nem található a helyi a vCenter Server, a Site Recovery szolgáltatásban hozzon létre egy új virtuális gép ismételt védelem alatt kell. Ezt a virtuális gépet az ESX-gazdagépen, amelyen hoz létre a fő célkiszolgáló jön létre. Válassza ki, az ESX-gazdagépet, hogy a feladat-visszavétel a virtuális gép jön létre, amelyet a gazdagépen.

* *A fő célkiszolgáló nem használhatja a Storage vmotion szolgáltatások használata*. A sikertelen feladat-visszavétel okozhatnak. A virtuális gép nem indítható el, mert a lemez nem érhetők el azt.

  > [!WARNING]
  > A fő célkiszolgálón a Storage vMotion feladatok ismételt védelem után megy keresztül, ha a védett virtuális gépek lemezeket a fő célkiszolgálóhoz csatlakoztatott telepítse át a vMotion feladat célja. Ezt követően ismét próbálja meg, ha a lemez terméknek sikertelen lesz, mivel a lemezek nem találhatók. A javítást nehéz megtalálni a lemezeket a storage-fiókok. Keresse meg őket manuálisan, és csatolja azokat a virtuális gép kell. Ezt követően a helyszíni virtuális gép rendszerindításra alkalmas.

* Adjon hozzá a meglévő Windows fő célkiszolgáló adatmegőrzési meghajtót. Adjon hozzá egy új lemezt, és formázza a meghajtót. Az adatmegőrzési meghajtó állítsa le a pontok időpont, amikor a virtuális gép replikálja vissza a helyszíni hely szolgál. Az alábbiakban néhány adatmegőrzési meghajtó feltétel. Ezek a feltételek nélkül a meghajtó nem jelenik a fő célkiszolgálón.

   * A kötet nem szolgál más célra, például a replikációs cél.

   * A kötet nem zár módban van.

   * A kötet nincs a gyorsítótár kötetén. A fő célkiszolgáló telepítése nem létezik a köteten található. A folyamat kiszolgáló és a fő célkiszolgáló egyéni telepítési lemez nem jogosult az adatmegőrzési kötet. A folyamatkiszolgáló és fő célkiszolgáló egy olyan köteten vannak telepítve, ha a lemez a fő célkiszolgáló a gyorsítótár kötetén.

   * A kötet fájlrendszerének nincs FAT vagy FAT32.

   * A kötet kapacitása nem nulla.

   * Az alapértelmezett adatmegőrzési kötet a Windows, az R-kötet.

   * Az alapértelmezett adatmegőrzési kötet Linux /mnt/retention.

  > [!IMPORTANT]
  > Egy meglévő folyamat kiszolgálóhoz vagy konfigurációs kiszolgáló vagy terjedő skálán vagy folyamat server/fő cél kiszolgáló használata, adjon hozzá egy új meghajtót kell. Az új meghajtó meg kell felelnie a fenti követelményeknek. Ha az adatmegőrzési meghajtó nincs jelen, nem jelenik meg a legördülő listában a portálon. Után hozzáadhat egy meghajtót a helyszíni fő célkiszolgáló, megjelennek a portálon a kijelölt meghajtó akár 15 percet vesz igénybe. Ha a meghajtó nem jelenik meg 15 perc után is frissítheti a konfigurációs kiszolgáló.

* Virtuális gép Linux átvevő Linux fő kiszolgáló szükséges. Virtuális gép Windows átvevő Windows fő kiszolgáló szükséges.

* A VMware-eszközök telepítése a fő célkiszolgálón. A VMware-eszközök nélkül a fő célkiszolgáló ESXi-állomáson datastores nem észlelhető.

* Engedélyezze a `disk.EnableUUID=true` paraméter a fő célkiszolgáló virtuális gépen a vCenter-tulajdonságok használatával. <!-- !todo Needs link. -->

* A fő célkiszolgáló rendelkeznie kell legalább egy virtuálisgép-Fájlrendszereinek datastore csatolva. Ha nincs, a **Datastore** a védelem-újrabeállítási oldalon bemenet üres lesz, és nem folytathatja a műveletet.

* A fő célkiszolgáló nem lehet pillanatképeket a lemezeken. Ha pillanatképek vannak, ismételt védelem és a feladat-visszavétel nem.

* A fő célkiszolgáló Paravirtual SCSI-vezérlőhöz nem tartozhat. A vezérlő csak egy LSI Logic vezérlő lehet. Egy LSI Logic vezérlőt ismételt védelem sikertelen lesz.

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## <a name="steps-to-reprotect"></a>Lássa el újból védelemmel lépései

> [!NOTE]
> Miután a virtuális gép elindul az Azure-ban, csak bizonyos idő az ügynök regisztrálása a konfigurációs kiszolgálóra (legfeljebb 15 perc). Ebben az időszakban ismételt védelem sikertelen lesz, és a hibaüzenet azt jelzi, hogy az ügynök nincs telepítve. Várjon néhány percet, és próbálkozzon az ismételt védelem újra.



1. A **tároló** > **replikált elemek**, kattintson a jobb gombbal a virtuális gépet, a feladatátvétel megtörtént, és válassza **védelmének újbóli beállításához**. Is kattintson a gépre, és válassza ki **védelmének újbóli beállításához** a parancs gombok közül.
2. A panelen, figyelje meg, hogy a védelem irányát **Azure a helyszíni**, már be van jelölve.
3. A **fő célkiszolgáló** és **Folyamatkiszolgáló**, válassza ki a helyszíni fő célkiszolgáló és a folyamatkiszolgáló.
4. A **Datastore**, válassza ki, amelyhez végre kívánja hajtani a lemezek helyi az adattárral. Ez a beállítás használható, ha a helyszíni virtuális gép törlődik, és új lemezek létrehozásához szükség. A rendszer figyelmen kívül hagyja ezt a beállítást, ha a lemez már létezik, de továbbra is meg kell adnia egy értéket.
5. Válassza ki az adatmegőrzési meghajtó.
6. A feladat-visszavétel házirend automatikusan ki van jelölve.
7. Kattintson a **OK** ismételt védelem megkezdéséhez. Egy feladat elkezdi replikálni a virtuális gépet az Azure-ból a helyszíni hely. A előrehaladásának a a **feladatok** fülre.

Ha azt szeretné, helyreállítása másik helyre (a helyszíni virtuális gép törlésekor), válassza az adatmegőrzési meghajtó és a fő célkiszolgáló konfigurált adattároló. Nem vissza a helyszíni hely, a VMware virtuális gépeket a feladat-visszavétel védelmi tervet a fő célkiszolgáló ugyanazon adattár használják. Új virtuális gép vCenter létrejön.

Ha szeretné helyreállítani a virtuális gépet az Azure-on egy meglévő helyszíni virtuális géphez, olvasási/írási hozzáférést a helyszíni virtuális gép datastores csatlakoztatása a fő célkiszolgáló ESXi-állomáson.
    ![Ismételt védelemmel párbeszédpanel](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

A helyreállítási terv szintjén is állítsa. Replikációs csoport is látható el újra védelemmel csak a helyreállítási tervet. Amikor Ön lássa el újból védelemmel a helyreállítási terv használatával, meg kell adnia az értékek minden védett gép.

> [!NOTE]
> Az azonos fő célkiszolgáló használatával lássa el újból védelemmel egy replikációs csoportot. Ha egy másik fő célkiszolgáló lássa el újból védelemmel egy replikációs csoportot, a kiszolgáló nem adhatók meg a közös pontja időben.

> [!NOTE]
> A helyszíni virtuális gép ismételt védelem ki van kapcsolva. Ez segít a replikáció során az adatok konzisztenciájának biztosítására. Ne kapcsolja be a virtuális gép ismételt védelem befejeződése után.

Miután az ismételt védelem sikeres, a virtuális gép módba lép, egy védett állapotban.

## <a name="next-steps"></a>Következő lépések

Miután a virtuális gép védett állapotba került, [kezdeményezni a feladat-visszavétel](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-fail-back). 

A feladat-visszavételt a virtuális gép az Azure-ban leáll és a helyszíni virtuális gép. Némi állásidőt jelent a alkalmazás várt. Válassza az időpontot a feladat-visszavétel, ha az alkalmazás működését állásidő.

## <a name="common-problems"></a>Gyakori problémák

* Ha egy sablon használatával a virtuális gépek létrehozásakor, győződjön meg arról, hogy minden virtuális gép rendelkezik-e a saját a lemezek UUID azonosítója. Ha a helyszíni virtuális gép UUID ütközik a, a fő célkiszolgálón, mert mindkét ugyanazon sablon alapján létrehozott, az ismételt védelem sikertelen lesz. Telepítsen egy másik fő célkiszolgáló ugyanazon sablonból nem lett létrehozva.

* Ha egy csak olvasható felhasználói vCenter-kiszolgálók automatikus észlelését, és a virtuális gépet véd, védelmi sikeres, és feladatátvételi működik. Ismételt védelem, során feladatátvétel sikertelen lesz, mivel a datastores nem lesz felderítve. A probléma tünete, hogy a datastores ismételt védelem alatt nem jelennek meg. A probléma megoldásához frissítse a vCenter hitelesítőadat egy megfelelő jogosultsággal rendelkező fiók, és próbálja újból elvégezni a feladatot. További információkért lásd: [replikálása VMware virtuális gépek és fizikai kiszolgálók Azure-bA az Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).

* Ha a feladat-visszavételt a Linux virtuális gép, és futtassa a helyszíni, láthatja, hogy a hálózatkezelő csomag el lett távolítva a gép. Az Eltávolítás azért van, mert a hálózatkezelő csomagot el kell távolítani, ha a virtuális gépet helyre lett állítva, az Azure-ban.

* Amikor egy Linux virtuális gép statikus IP-címmel van konfigurálva, és át nem adja a Azure, az IP-cím keletkezik, a DHCP-Kiszolgálótól. Amikor a rendszer átadja a helyszínen, a virtuális gép továbbra is az IP-cím beszerzése a DHCP Protokollt használják. Manuálisan jelentkezzen be a gépet, és állítsa vissza az IP-címet a statikus cím, ha szükséges. A Windows rendszerű virtuális gép újra lekérheti a statikus IP-címet.

* Vagy ESXi 5.5 ingyenes kiadásban, vagy vSphere 6 hipervizor ingyenes kiadás használatakor a feladatátvétel sikerességét, de a feladat-visszavétel nem sikerül. Ahhoz, hogy a feladat-visszavétel, frissíteni vagy program próbalicencre.

* A folyamatkiszolgáló a konfigurációs kiszolgáló nem érhető el, ha a Telnet segítségével ellenőrizze a kapcsolatot a konfigurációs kiszolgálón a 443-as porton. A folyamatkiszolgáló a konfigurációs kiszolgáló pingelése is próbálkozhat. A folyamatkiszolgáló lehet szívverést a konfigurációs kiszolgálóhoz való csatlakozáskor.

* Ha vissza egy másik vCenter az sikertelen lesz, győződjön meg arról, hogy az új vCenter és a fő célkiszolgáló felderített. Egy tipikus tünete, hogy a datastores nem elérhetők és látható a **lássa el újból védelemmel** párbeszédpanel megnyitásához.

* Windows Server 2008 R2 SP1 kiszolgáló védett, mint a helyszíni fizikai kiszolgáló nem sikertelen az Azure-ból a helyszíni-helyhez.

### <a name="common-error-codes"></a>Gyakori hibakódokat

#### <a name="error-code-95226"></a>95226. Hibakód:

*Védelem-újrabeállítási sikertelen volt, mert a virtuális gépet az Azure nem tudta elérni a helyi konfigurációs kiszolgálót.*

Ez akkor fordul elő, amikor 
1. Az Azure virtuális gép nem tudta elérni a helyszíni konfigurációs kiszolgáló és ezért sikertelen nem felderítése és a konfigurációs kiszolgáló regisztrálva. 
2. Az InMage Scout szolgáltatás a való kommunikációhoz és a helyszíni konfigurációs kiszolgáló futnia kell az Azure virtuális gépen nem fut a feladatátvétel utáni.

A probléma megoldásához
1. Gondoskodnia kell arról, hogy az Azure virtuális gép hálózati konfigurálva van, úgy, hogy a virtuális gép kommunikálhat a helyszíni konfigurációs kiszolgáló. Ehhez az szükséges, egy a helyszíni adatközpontját a helyek közötti virtuális magánhálózat beállításához, vagy ExpressRoute kapcsolat konfigurálása a magánhálózati társviszony-létesítés az Azure virtuális gép a virtuális hálózaton. 
2. Ha már rendelkezik egy hálózatot, hogy a virtuális gépet az Azure kommunikálhat a helyszíni konfigurációs kiszolgáló, majd jelentkezzen be a virtuális gépet, és ellenőrizze a "InMage Scout alkalmazásszolgáltatás". Ha azt láthatja, hogy az InMage Scout alkalmazásszolgáltatás nem fut. Indítsa el kézzel a szolgáltatást, és győződjön meg arról, hogy a szolgáltatás indítási típusa automatikusra van beállítva.

### <a name="error-code-78052"></a>78052. Hibakód:
Védelem-újrabeállítási a következő hibaüzenettel meghiúsul: *védelmet a virtuális géphez nem lehetett befejezni.*

Ez akkor fordulhat elő két oknál
1. A virtuális gép, amelyek újbóli védelméhez egy Windows Server 2016. Ez az operációs rendszer curently feladat-visszavétel esetében nem támogatott, de hamarosan elhárítjuk támogatott.
2. Már van egy virtuális gép ugyanazzal a névvel, a fő célkiszolgáló, amelyek nem vissza.

A probléma megoldásához kiválaszthatja, hogy a védelem-újrabeállítási hoz létre a gépet egy másik gazdagépet, ahol a nevek nem ütköznek egy másik fő célkiszolgáló egy másik gazdagépen. A fő célkiszolgáló egy másik gazdagépen, ha nem történik a névütközés vMotion is. Ha a meglévő virtuális gép szétszórt gép, csak átnevezheti azt, hogy az új virtuális gép is létrehozása ugyanazon az ESXi-állomáson.

### <a name="error-code-78093"></a>78093. Hibakód:

*A virtuális gép nem fut, lefagyott állapotban vagy nem érhető el.*

A állítsa vissza a helyszíni virtuális gép keresztül egy sikertelen, a futó Azure virtuális géphez van szükség. Ez azért, hogy a mobilitási szolgáltatás regisztrálja a konfigurációs kiszolgálón a helyi és elindíthatja a replikáló által a folyamat kiszolgálóval való kommunikáció során. Ha a számítógépen helytelen hálózaton vagy nem fut (lefagyott állam vagy -leállítás), majd a konfigurációs kiszolgáló nem érhető el a mobilitási szolgáltatást a virtuális gépen, a védelem-újrabeállítási megkezdéséhez. A virtuális gépet, hogy a kommunikáció hátsó helyszíni elindíthatja indíthatja újra. Az Azure virtuális gép elindítása után a védelem-újrabeállítási feladat újraindításához

### <a name="error-code-8061"></a>8061. Hibakód:

*Az adattároló nem érhető el az ESXi-állomáson.*

Tekintse meg a [fő cél a szükséges előfeltételek](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server) és a [datastores támogatja](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback) feladat-visszavételhez

