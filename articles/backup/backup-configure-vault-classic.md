---
title: "Készítsen biztonsági másolatot a Windows server vagy az Azure-ba (klasszikus modell) munkaállomás |} Microsoft Docs"
description: "Windows Server-kiszolgálók biztonsági mentési vagy ügyfelek számára, hogy a mentési tárolóban az Azure-ban. Nyissa meg a fájlok és mappák a mentési tárolóba védelméhez az Azure Backup szolgáltatás ügynökének használatával alapjai keresztül."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "mentési tároló; Készítsen biztonsági másolatot a Windows server; biztonsági mentési Időablakok;"
ms.assetid: 3b543bfd-8978-4f11-816a-0498fe14a8ba
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: a8daa6a4655b72936b6299c0fa5b80459ffa5da3
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="back-up-a-windows-server-or-workstation-to-azure-using-the-classic-portal"></a>Biztonsági másolatot készíthet a Windows server és a munkaállomás Azure klasszikus portál használatával
> [!div class="op_single_selector"]
> * [Klasszikus portál](backup-configure-vault-classic.md)
> * [Azure Portal](backup-configure-vault.md)
>
>

Ez a cikk az Azure-bA az eljárásokat, kövesse a környezet előkészítése és a biztonsági másolatot készíthet a Windows server (és munkaállomások) ismerteti. A biztonsági mentési megoldások telepítésének szempontjai is magában foglalja. Ha szeretné használni az Azure biztonsági mentés közben először, ebben a cikkben gyorsan végigvezeti a folyamatot.

Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: Resource Manager és klasszikus. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

## <a name="before-you-start"></a>Előkészületek
Biztonsági mentése a kiszolgáló vagy az ügyfél az Azure-ba, az Azure-fiók szükséges. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) néhány percig.

## <a name="create-a-backup-vault"></a>Backup-tároló létrehozása
Fájlok és mappák biztonsági mentése egy kiszolgáló vagy az ügyfél, hozzon létre egy mentési tárolót, hol szeretné tárolni az adatokat a földrajzi régióban kell.

> [!IMPORTANT]
> 2017 márciusától már nem hozhat létre Backup-tárolókat a klasszikus portálon.
>
> A biztonsági mentési tárakról mostantól lehetőség van helyreállítási tárakra váltani. A részletekről bővebben az [Váltás biztonsági mentési tárolóról Recovery Services-tárolóra](backup-azure-upgrade-backup-to-recovery-services.md) című cikkben olvashat. A Microsoft azt javasolja, hogy a biztonsági mentési tárolóról váltson Recovery Services-tárolóra.<br/> **2017. október 15-től** a PowerShell a továbbiakban nem használható Backup-tárolók létrehozására. <br/> **2017. november 1-től kezdődően**:
>- A rendszer automatikusan elvégzi valamennyi megmaradó biztonsági mentési tároló átváltását Recovery Services-tárolókra.
>- A klasszikus portálon nem lehet majd hozzáférni a biztonsági másolati adatokhoz. Helyette az Azure Portal segítségével férhet hozzá a Recovery Services-tárolókban található biztonsági mentési adatokhoz.
>


## <a name="download-the-vault-credential-file"></a>Töltse le a tárolói hitelesítő adatok fájlját
A helyszíni gépre kell hitelesíteni a mentési tárolóban előtt az adatok biztonsági másolatát is az Azure-bA. A hitelesítési sorrendekben *hitelesítő adatokat tároló*. A tárolói hitelesítő adatok fájlját letöltődik a klasszikus portálon egy biztonságos csatornán keresztül. A tanúsítvány titkos kulcsa nem maradnak a portálon vagy a szolgáltatás.

### <a name="to-download-the-vault-credential-file-to-a-local-machine"></a>Töltse le a tárolói hitelesítő adatok fájlját egy helyi számítógép számára
1. A bal oldali navigációs ablaktáblán kattintson **Recovery Services**, majd válassza ki a mentési tároló, amely létrehozta.

    ![IR befejezve](./media/backup-configure-vault-classic/rs-left-nav.png)
2. Kattintson a gyors üzembe helyezés lap **letöltési tárolói hitelesítő adatokat**.

   A klasszikus portálon, a tároló nevét és az aktuális dátumot együttes használatával hoz létre a tároló hitelesítő adatait. A tároló hitelesítési adatait tartalmazó fájlt csak a regisztrációs munkamenet során használt, és 48 óra múlva lejár.

   A tároló hitelesítő adatait tartalmazó fájlt a portálról letölthető.
3. Kattintson a **mentése** letölteni a tároló hitelesítő adatait tartalmazó fájlt a helyi fiók a Letöltések mappába. Igény szerint kiválaszthatja **Mentés másként** a a **mentése** menüre kattintva adjon meg egy helyet a tárolói hitelesítő adatok fájlját.

   > [!NOTE]
   > Ellenőrizze, hogy a tároló hitelesítő adatait tartalmazó fájlt van egy helyre mentik el, hogy a számítógép elérhető. Ha egy fájl megosztás vagy kiszolgálói üzenetblokk tárolva van, győződjön meg arról, hogy rendelkezik-e az engedélyeket az eléréséhez.
   >
   >

## <a name="download-install-and-register-the-backup-agent"></a>Letöltése, telepítése és regisztrálása a Backup szolgáltatás ügynöke
Miután a mentési tároló létrehozása, és töltse le a tárolói hitelesítő adatok fájlját, egy ügynököt telepíteni kell a Windows-alapú gépek minden egyes.

### <a name="to-download-install-and-register-the-agent"></a>Letöltéséhez, telepítéséhez, és az ügynök regisztrálása
1. Kattintson a **Recovery Services**, majd válassza ki a mentési tároló, amelyet szeretne regisztrálni egy kiszolgálóval.
2. A gyors kezdés lapon kattintson az ügynök **ügynök Windows vagy a System Center Data Protection Manager vagy a Windows ügyfél**. Ezután kattintson a **Save** (Mentés) gombra.

    ![Ügynök mentése](./media/backup-configure-vault-classic/agent.png)
3. Miután letöltötte a MARSagentinstaller.exe fájlt, kattintson **futtatása** (vagy kettős kattintással **MARSAgentInstaller.exe** a mentett helyről).
4. Válassza ki a telepítési mappa és a gyorsítótár mappája, amelyek szükségesek az ügynök, és kattintson a **következő**. A gyorsítótár helyét adja meg, hogy a biztonsági mentési adatok legalább 5 százalékának megfelelő szabad helynek kell lennie.
5. Továbbra is az interneten keresztül az alapértelmezett proxybeállítások csatlakozni.             Ha proxykiszolgálót segítségével csatlakozni az internethez, a proxykonfiguráció lapon válassza ki a **egyéni proxybeállításainak használata** jelölje be a jelölőnégyzetet, és írja be a proxy-kiszolgáló adatait. Ha egy hitelesített proxykiszolgálót használ, írja be a felhasználói nevet és jelszót adatait, és kattintson **következő**.
6. Kattintson a **telepítése** az ügynök telepítésére. A Backup szolgáltatás ügynökének telepíti a .NET-keretrendszer 4.5 és Windows PowerShell (Ha még nincs telepítve) a telepítés befejezéséhez.
7. Az ügynök telepítése után kattintson **továbblépni a regisztrációra** a munkafolyamat folytatásához.
8. A tárolót azonosító lapon keresse meg és jelölje ki a korábban letöltött tárolói hitelesítő adatok fájlját.

    Csak 48 órán át, a portál letöltése után a tárolói hitelesítő adatok fájlját esetén érvényes. Ha hibát észlel a ezen a lapon (például "tárolói hitelesítő adatok megadott fájlja lejárt"), jelentkezzen be a portálra, és töltse le újból a tároló hitelesítő adatait tartalmazó fájlt.

    Győződjön meg arról, hogy a tároló hitelesítő adatait tartalmazó fájlt érhető el egy olyan helyre, a telepítő alkalmazás elérhető legyen. Ha a hozzáféréssel kapcsolatos hibákat tapasztal, másolja a tároló hitelesítő adatait tartalmazó fájlt egy ideiglenes helyre ugyanazon a számítógépen, és próbálja megismételni a műveletet.

    Ha a tároló hitelesítő adat hiba, például "érvénytelen megadott tárolói hitelesítő adatok", a fájl sérült, vagy nem nem rendelkezik a legújabb hitelesítő adatokat a helyreállítási szolgáltatáshoz tartozó. Próbálja megismételni a műveletet, miután egy új tárolói hitelesítő adatok fájlját letöltése a portálról. Ez a hiba akkor is előfordulhat, ha a felhasználó kattint a **letöltési tárolóhitelesítő adatokat** beállítás többször gyors egymásutánban. Ebben az esetben csak az utolsó tárolói hitelesítő adatok fájlját esetén érvényes.
9. A titkosítási beállítás lapon egy jelszót létrehozni, vagy adjon meg egy jelszót (minimum 16 karakter). Ne felejtse el a hozzáférési kód mentse egy biztonságos helyre.
10. Kattintson a **Befejezés** gombra. A kiszolgáló regisztrálása varázsló regisztrálja a kiszolgáló biztonsági mentése.

    > [!WARNING]
    > Ha elveszíti vagy elfelejti a jelszót, a Microsoft nem helyreállíthatja a biztonsági mentési adatokat. Ön a tulajdonosa a titkosítási jelszót, és a Microsoft nem látnak bele a jelszót, amelyet használhat. Mentse a fájlt egy biztonságos helyre, mert lesz szükség a helyreállítási művelet során.
    >
    >

11. A titkosítási kulcs beállítása után hagyja a **indítsa el a Microsoft Azure Recovery Services Agent** jelölőnégyzetet, és kattintson a **Bezárás**.

## <a name="complete-the-initial-backup"></a>Végezze el a kezdeti biztonsági másolatot
A kezdeti biztonsági mentésbe két fő feladat tartozik:

* A biztonsági mentési ütemezés létrehozása
* Fájlok és mappák biztonsági mentése az első alkalommal

A biztonsági mentési házirendet a kezdeti biztonsági mentés befejezése után is használhatja, ha az adatok helyreállítása kell biztonsági mentési pontok hoz létre. A biztonsági mentési házirend ezt az Ön által meghatározott ütemezés szerint végzi.

### <a name="to-schedule-the-backup"></a>A biztonsági mentés ütemezése
1. Nyissa meg a Microsoft Azure Backup szolgáltatás ügynöke. (Az nyílik meg automatikusan, ha a **indítsa el a Microsoft Azure Recovery Services Agent** jelölőnégyzet be van jelölve, a kiszolgáló regisztrálása varázsló bezárásakor.) A megkereséséhez keressen rá a gépen a **Microsoft Azure Backup** kifejezésre.

    ![Indítsa el az Azure Backup szolgáltatás ügynöke](./media/backup-configure-vault-classic/snap-in-search.png)
2. Kattintson a Backup szolgáltatás ügynökének **biztonsági mentés ütemezése**.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-configure-vault-classic/schedule-backup-close.png)
3. A Biztonsági mentés ütemezése varázsló Első lépések oldalán kattintson a **Tovább** gombra.
4. Az Elemek kijelölése biztonsági mentéshez oldalon kattintson az **Elemek hozzáadása** lehetőségre.
5. Jelölje ki azokat a fájlokat és mappákat, amelyekről biztonsági másolatot szeretne készíteni, majd kattintson az **OK** gombra.
6. Kattintson a **Tovább** gombra.
7. A **Biztonsági mentés ütemezésének megadása** lapon határozza meg a **biztonsági mentés ütemezését**, és kattintson a **Tovább** gombra.

    Napi (legfeljebb napi háromszori) vagy heti biztonsági mentéseket ütemezhet.

    ![Windows Server biztonsági mentés elemei](./media/backup-configure-vault-classic/specify-backup-schedule-close.png)

   > [!NOTE]
   > A biztonsági mentés ütemezésének meghatározásával kapcsolatos további információért tekintse meg a [Use Azure Backup to replace your tape infrastructure](backup-azure-backup-cloud-as-tape.md) (Az Azure Backup használata a szalagos infrastruktúra lecseréléséhez) című cikket.
   >
   >

8. A **Megőrzési házirend kiválasztása** oldalon válassza ki a biztonsági másolat **megőrzési házirendjét**.

    A megőrzési házirend határozza meg a biztonsági másolat tárolásának időtartamát. Ahelyett, hogy mindegyik biztonsági mentési ponthoz „lapos házirendet” határozna meg, különböző megőrzési házirendeket határozhat meg a biztonsági másolat készítésének ideje alapján. Igényei szerint módosíthatja a napi, heti, havi és évi megőrzési házirendeket.
9. A Kezdeti biztonsági mentés típusának kiválasztása oldalon válassza ki a kezdeti biztonsági mentés típusát. Hagyja bejelölve az **Automatikusan a hálózaton keresztül** beállítást, majd kattintson a **Tovább** gombra.

    Automatikusan készíthet biztonsági másolatot a hálózaton keresztül, vagy offline készíthet biztonsági másolatot. Ezen cikk többi része az automatikus biztonsági mentés folyamatát írja le. Ha offline biztonsági mentést szeretne végezni, további információért tekintse meg az [Offline backup workflow in Azure Backup](backup-azure-backup-import-export.md) (Offline biztonsági mentési munkafolyamat az Azure Backupban) című cikket.
10. A Jóváhagyás lapon ellenőrizze az információkat, majd kattintson a **Befejezés** gombra.
11. Miután a varázsló befejezte a biztonsági mentési ütemezés létrehozását, kattintson a **Bezárás** gombra.

### <a name="enable-network-throttling-optional"></a>(Választható)-szabályozás engedélyezése
A Backup szolgáltatás ügynökének biztosít a hálózati sávszélesség-szabályozás. Szabályozza, hogyan adatátvitel során használt hálózati sávszélesség-szabályozás. Ez a vezérlő akkor lehet hasznos, ha biztonsági kell során az adatokat munkaidő, de nem szeretné a biztonsági mentési folyamat zavarja a más internetes forgalmat. Sávszélesség-szabályozás biztonsági mentése és visszaállítása tevékenységek vonatkozik.

**Hálózati sávszélesség-szabályozás engedélyezése**

1. Kattintson a Backup szolgáltatás ügynökének **tulajdonságainak módosítása**.

    ![Tulajdonságainak módosítása](./media/backup-configure-vault-classic/change-properties.png)
2. Az a **sávszélesség-szabályozási** lapon jelölje be a **engedélyezi az internetes sávszélesség szabályozásának a biztonsági mentési műveleteknél** jelölőnégyzetet.

    ![Hálózati sávszélesség-szabályozás](./media/backup-configure-vault-classic/throttling-dialog.png)
3. Miután engedélyezte a sávszélesség-szabályozás, adja meg az engedélyezett sávszélesség vonatkozó biztonsági mentési adatátvitel során **időpontokat a munkaidőhöz** és **munkaidőn kívüli**.

    A sávszélesség értékek 512 kilobit / másodperc (Kbps) kezdődik, és folytathatja a legfeljebb 1,023 megabájt / másodperc (MBps). Is kijelölni a kezdő és a Befejezés **időpontokat a munkaidőhöz**, és a hét melyik napjain figyelembe vett munkanapok. Óra között útmutatóul szolgálnak a kijelölt munkahelyi kívül óra munkaórákon kívüli időre.
4. Kattintson az **OK** gombra.

### <a name="to-back-up-now"></a>Biztonsági mentése
1. Kattintson a Backup szolgáltatás ügynökének **biztonsági másolat készítése most** befejeződik, a kezdeti összehangolása a hálózaton keresztül.

    ![Windows Server biztonsági másolat készítése](./media/backup-configure-vault-classic/backup-now.png)
2. A Jóváhagyás lapon tekintse át azokat a beállításokat, amelyeket a Biztonsági másolat készítése varázsló a gép biztonsági mentéséhez fog használni. Ezután kattintson a **Biztonsági mentés** gombra.
3. A varázsló bezárásához kattintson a **Bezárás** gombra. Ha ezt a biztonsági mentési folyamat befejezése előtt teszi, a varázsló továbbra is fut a háttérben.

A kezdeti biztonsági mentés befejezése után a **Feladat befejezve** állapot jelenik meg a biztonsági mentési konzolon.

![IR befejezve](./media/backup-configure-vault-classic/ircomplete.png)

## <a name="next-steps"></a>Következő lépések
* Regisztráljon egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/).

Virtuális gépek vagy más munkaterhelések biztonsági mentésével kapcsolatos további információkért lásd:

* [Készítsen biztonsági másolatot IaaS virtuális gépeket](backup-azure-vms-prepare.md)
* [-Munkaterhelések biztonsági mentése az Azure-ba, a Microsoft Azure Backup Server](backup-azure-microsoft-azure-backup.md)
* [-Munkaterhelések biztonsági mentése az Azure-ba, a dpm-mel](backup-azure-dpm-introduction.md)
