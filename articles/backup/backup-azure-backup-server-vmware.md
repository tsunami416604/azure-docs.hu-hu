---
title: VMware virtuális gépek az Azure Backup Server biztonsági mentése
description: Az Azure Backup Server használatával a VMware vCenter vagy ESXi-kiszolgálón fut, a VMware virtuális gépek biztonsági mentése.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: raynew
ms.openlocfilehash: 6fb95d43bff79ca91988549114daa91e5d41f358
ms.sourcegitcommit: 5e4ca656baf3c7d370ab3c0fbad0278aa2c9f1e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319363"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>VMware virtuális gépek az Azure Backup Server biztonsági mentése

Ez a cikk bemutatja, hogyan készíthet biztonsági másolatot a VMware virtuális gépek, VMware ESXi-gazdagépek/vCenter Server futó Azure-bA az Azure Backup Server használatával.

Ez a cikk azt ismerteti, hogyan lehet:

- Biztonságos csatorna beállítása, hogy az Azure Backup Server VMware-kiszolgálókhoz HTTPS-kapcsolaton keresztül kommunikálhassanak.
- Állítsa be a VMware-fiókot, amely az Azure Backup Server használja a VMware-kiszolgáló eléréséhez.
- Adja hozzá a fiók hitelesítő adatait az Azure Backup szolgáltatásban.
- A vCenter vagy ESXi-kiszolgáló hozzáadása az Azure Backup Server.
- Állítsa be egy védelmi csoportot, amely tartalmazza a VMware virtuális gépeket szeretne biztonsági másolatot készíteni, adja meg a biztonsági mentés beállításait és a biztonsági mentés ütemezése.

## <a name="before-you-start"></a>Előkészületek
- Győződjön meg arról, hogy a vCenter/ESXi backup - 6.5-ös, 6.0-s és a egy 5.5-ös verziók által támogatott verzióját futtatja-e.
- Győződjön meg arról, hogy beállította az Azure Backup Server. Ha még nem, [ehhez](backup-azure-microsoft-azure-backup.md) megkezdése előtt. Kell futtatni az Azure Backup Server a legújabb frissítésekkel.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>A vCenter-kiszolgáló biztonságos kapcsolat létrehozása

Alapértelmezés szerint az Azure Backup Server kommunikál a VMware-kiszolgálók HTTPS-kapcsolaton keresztül. A HTTPS-kapcsolat beállításához, a VMware hitelesítésszolgáltató (CA) tanúsítvány letöltéséhez, és importálja az Azure Backup Server.


### <a name="before-you-start"></a>Előkészületek

- Ha nem szeretné HTTPS protokoll használatára is [tiltsa le az alapértelmezett beállítás](backup-azure-backup-server-vmware.md).
- Általában csatlakoztat egy böngészőből az Azure Backup Server gépet, a vCenter/ESXi-kiszolgálóhoz, a vSphere webes ügyfélben használatával. Először ezt megteheti a kapcsolat nem biztonságos, és a következőképpen fog megjelenni.
- Fontos tudni, hogyan kezeli az Azure Backup Server a biztonsági mentéseket.
    - Első lépésként az Azure Backup Server biztonsági másolatot készít adatokat a helyi lemezes tárterületet. Az Azure Backup Server használ a tárolókészlethez, a lemezeket és köteteket, amelyeken az Azure Backup Server tárolja a védett adatokhoz tartozó lemez-helyreállítási pontok. A tárolókészlet közvetlenül csatlakoztatott tárolóval (DAS), a fiber channel SAN, vagy iSCSI-tárolóeszköz vagy TÁROLÓHÁLÓZAT is lehet. Fontos győződjön meg arról, hogy elegendő tárhely helyi biztonsági a VMware virtuális gép adatait.
    - Az Azure Backup Server majd biztonsági másolatot készít a helyi lemezes tárhely az Azure-bA.
    - [Segítség kérése](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) döntse el, hogy mekkora tárolási helyek, akkor van szükség. Információk a DPM azonban túl használható az Azure Backup Server.

### <a name="set-up-the-certificate"></a>A tanúsítvány beállítása

Az alábbiak szerint állíthatja biztonságos csatorna:

1. Az Azure Backup Server a böngészőben adja meg a vSphere webes ügyfél URL-címe. Ha a bejelentkezési oldal nem jelenik meg, ellenőrizze a kapcsolatot és a böngésző proxybeállítások.

    ![vSphere webes ügyfélben](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. A vSphere webes ügyfél bejelentkezési oldalon kattintson a **letöltése megbízható legfelső szintű Hitelesítésszolgáltatói tanúsítványok**.

    ![Megbízható legfelső szintű Hitelesítésszolgáltatói tanúsítvány letöltése](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Nevű fájl **letöltése** töltődik le. A böngésző típusától függően kap egy üzenetet, amely megkérdezi, hogy e megnyitni vagy menteni a fájlt.

    ![Hitelesítésszolgáltatói tanúsítvány letöltése](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Mentse a fájlt az Azure Backup Server gépen .zip kiterjesztésű.

5. Kattintson a jobb gombbal **download.zip** > **az összes kibontása**. A zip-fájlt a tartalmát a kinyeri a **tanúsítványok** mappába, amely tartalmazza:
   - A legfelső szintű.0 és ikonra.1 például egy számozott feladatütemezési kezdődő kiterjesztésű tanúsítványfájlt.
   - A CRL-fájl kiterjesztése, amely egy feladatütemezési .r0 vagy .r1 hasonlóan kezdődik. A tanúsítvány CRL-fájlnak kapcsolódik.

     ![A letöltött tanúsítvány](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. Az a **tanúsítványok** mappát, kattintson a jobb gombbal a legfelső szintű tanúsítványfájl > **átnevezése**.

    ![Nevezze át a legfelső szintű tanúsítvány](./media/backup-azure-backup-server-vmware/rename-cert.png)

6. Állítsa a legfelső szintű tanúsítvány bővítmény .crt, majd erősítse meg. A fájl ikon módosul, hogy a legfelső szintű tanúsítvány jelöli.

7. Kattintson a jobb gombbal a legfelső szintű tanúsítvány és a legördülő menüből válassza ki a **tanúsítvány telepítése**.

8. A **Tanúsítványimportáló varázsló**válassza **helyi gép** kell a tanúsítványt, és kattintson a célként **tovább**. Győződjön meg arról, ha azt kéri, hogy szeretné-e a számítógép-engedélyezése.

    ![Üdvözli a varázsló](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)


9. Az a **tanúsítvány Store** lapon jelölje be **minden tanúsítvány tárolása ebben a tárolóban**, és kattintson a **Tallózás** a tanúsítványtároló kiválasztása.

     ![Tanúsítványtároló](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

10. A **válassza tanúsítvány Store**, jelölje be **megbízható legfelső szintű hitelesítésszolgáltatók** a tanúsítványokat, és kattintson a célmappaként **OK**.

    ![Tanúsítvány célmappa](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

11. A **tanúsítvány importálása varázsló befejezése**, ellenőrizze a mappát, és kattintson **Befejezés**.

    ![Ellenőrizze a tanúsítvány nem a megfelelő mappát](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)


12. A tanúsítvány importálása ellenőrzése után jelentkezzen be a vcenter-kiszolgáló győződjön meg arról, hogy a kapcsolat nem biztonságos.




### <a name="disable-default-https"></a>Alapértelmezett HTTPS letiltása

Ha biztonságos határai a szervezeten belül, és nem szeretné használni a HTTPS protokollt közötti, VMware-kiszolgálók és az Azure Backup Server gépet, tiltsa le a HTTPS a következő: u
1. Másolja és illessze be a következő szöveg egy .txt fájlt.

      ```
      Windows Registry Editor Version 5.00
      [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
      "IgnoreCertificateValidation"=dword:00000001
      ```

2. Mentse a fájlt az Azure Backup Server gépen nevű **DisableSecureAuthentication.reg**.

3. Kattintson duplán a fájlra, a beállításjegyzék-bejegyzés aktiválásához.


## <a name="create-a-vmware-role"></a>VMware-szerepkör létrehozása

Az Azure Backup Server van szüksége a Vcenter-kiszolgáló elérésére jogosult felhasználói fiók/ESXi-gazdagépen. Hozzon létre egy VMware szerepkört adott jogosultságokkal, és majd egy felhasználói fiókot társíthat a szerepkört.

1. Jelentkezzen be a vCenter-kiszolgáló (vagy az ESXi-állomáson, ha nem használ vCenter-kiszolgáló).
2. Az a **kezelő** panelen, kattintson a **felügyeleti**.

    ![Adminisztráció](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. A **felügyeleti** > **szerepkörök**, kattintson a Hozzáadás szerepkör ikonra (a + szimbólumra).

    ![Szerepkör hozzáadása](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)


4. A **szerepkör létrehozása** > **szerepkörnév**, adja meg *BackupAdminRole*. A szerepkör neve lehet bármire átnevezhető, de felismerhető a szerepkör célra kell lennie.

5. Válassza ki a jogosultságokat, az alábbi táblázat foglalja össze, és kattintson a **OK**.  Az új szerepkör megjelenik a listán szereplő a **szerepkörök** panel.
   - Bontsa ki a szülő és gyermek jogosultságok megtekintése a szülő címke melletti ikonra kattintva.
   - Válassza ki a VirtualMachine jogosultságokat, a szülő-gyermek hierarchia több szinten fel kell.
   - Válassza ki a szülő jogosultság belül az összes gyermek jogosultság nem kell.

     ![Szülő-gyermek jogosultság hierarchiát](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Szerepkör-engedélyek
**6.5/6.0** | **5.5**
--- | ---
Datastore.AllocateSpace | Datastore.AllocateSpace
Global.ManageCustomFields | Global.ManageCustomFields
Global.SetCustomField |
Host.Local.CreateVM | Network.Assign
Network.Assign |
Resource.AssignVMToPool |
VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   
VirtualMachine.Config.AdvancedConfig| VirtualMachine.Config.AdvancedConfig
VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking
VirtualMachine.Config.HostUSBDevice |
VirtualMachine.Config.QueryUnownedFiles |
VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement
VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff
VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create
VirtualMachine.Provisioning.DiskRandomAccess |
VirtualMachine.Provisioning.DiskRandomRead | VirtualMachine.Provisioning.DiskRandomRead
VirtualMachine.State.CreateSnapshot | VirtualMachine.State.CreateSnapshot
VirtualMachine.State.RemoveSnapshot | VirtualMachine.State.RemoveSnapshot




## <a name="create-a-vmware-account"></a>Hozzon létre egy VMware-fiókot

1. A vCenter Server **kezelő** panelen, kattintson a **felhasználók és csoportok**. Ha nem használ vCenter-kiszolgálóhoz, hozzon létre a fiók a megfelelő ESXi-gazdagépen.

    ![Felhasználók és csoportok lehetőség](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    A **vCenter-felhasználók és csoportok** panelen jelennek meg.


2. Az a **vCenter-felhasználók és csoportok** panelen kattintson a **felhasználók** fülre, majd az add-felhasználók ikon (a + szimbólumra).

     ![vCenter-felhasználók és csoportok panel](./media/backup-azure-backup-server-vmware/usersandgroups.png)


3. A **új felhasználó** párbeszédpanelen adjon hozzá a felhasználói adatok > **OK**. Ebben az eljárásban a felhasználónév BackupAdmin.

    ![Új felhasználó párbeszédpanel](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)


4. A szerepkör a felhasználói fiók társítása a **kezelő** panelen, kattintson a **globális engedélyek**. Az a **globális engedélyek** panelen kattintson a **kezelés** lapra, és végül kattintson a Hozzáadás elemre (a + szimbólumra).

    ![Globális engedélyek panel](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)


5. A **globális engedélyt gyökér - engedély hozzáadása**, kattintson a **Hozzáadás** a felhasználó vagy csoport kiválasztása.

    ![Felhasználó vagy csoport kiválasztása](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. A **felhasználók vagy csoportok kiválasztása**, válassza a **BackupAdmin** > **Hozzáadás**. A **felhasználók**, a *tartomány\felhasználónév* formátum a felhasználói fiók használatos. Használjon egy másik tartományban szeretne, válassza azt a **tartomány** listája. Kattintson a **OK** a kiválasztott felhasználók hozzáadása a **engedély hozzáadása** párbeszédpanel bezárásához.

    ![BackupAdmin felhasználó hozzáadása](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)


7.  A **hozzárendelt szerepkör**, válassza a legördülő listából **BackupAdminRole** > **OK**.

    ![Felhasználó szerepkör hozzárendelése](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)


Az a **kezelés** lapján a **globális engedélyek** panelen, az új felhasználói fiók és a kapcsolódó szerepkör szerepelnek a listán.


## <a name="add-the-account-on-azure-backup-server"></a>Adja hozzá a fiókot az Azure Backup Server


1. Nyissa meg az Azure Backup Server. Ha nem találja az ikonra az asztalon, nyissa meg a Microsoft Azure Backup a listáról.

    ![Az Azure Backup Server ikonja](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. Az Azure Backup Server konzolon kattintson a **felügyeleti** >  **éles kiszolgálók** > **kezelése VMware**.

    ![Az Azure Backup Server konzol](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)


3. Az a **hitelesítő adatok kezelése** párbeszédpanelen kattintson a **Hozzáadás**.

    ![Az Azure Backup Server hitelesítő adatok kezelése párbeszédpanel](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. A **hitelesítő adatok hozzáadása** , adjon meg egy nevet és az új hitelesítő adat leírását, és adja meg a felhasználónevet és jelszót a VMware-kiszolgálón megadott. A név *Contoso Vcenter credential* ebben az eljárásban a hitelesítő adatok azonosítására szolgál. Ha a VMware-kiszolgáló és az Azure Backup Server nem ugyanabban a tartományban találhatók, adja meg a tartományt a felhasználó nevében.

    ![Az Azure Backup Server hitelesítő adatok hozzáadása párbeszédpanel](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Kattintson a **Hozzáadás** hozzáadni az új hitelesítő adat.

    ![Az Azure Backup Server hitelesítő adatok kezelése párbeszédpanel](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)


## <a name="add-the-vcenter-server"></a>A vCenter-kiszolgáló hozzáadása

A vCenter-kiszolgáló hozzáadása az Azure Backup Server.


1. Az Azure Backup Server konzolon kattintson a **felügyeleti** > **éles kiszolgálók** > **Hozzáadás**.

    ![Nyissa meg az üzemi kiszolgáló felvétele varázsló](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)


2. A **üzemi kiszolgáló felvétele varázsló** > **válassza ki az üzemi kiszolgáló típusának** lapra, jelölje be **VMware-kiszolgálók**, és kattintson a **következő**.

     ![Üzemi kiszolgáló felvétele varázsló](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. A **számítógépek kiválasztása****kiszolgáló neve vagy IP-címének**, adja meg a VMware-kiszolgáló teljes Tartománynevét vagy IP-címét. Ha minden ESXi-kiszolgáló eltérő vCenter által felügyelt, adja meg a vCenter nevét. Ellenkező esetben adja hozzá az ESXi-gazdagépen.

    ![Adja meg a VMware-kiszolgáló](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. A **SSL-Port**, adja meg a VMware-kiszolgálóval való kommunikációhoz használt port. 443-as az alapértelmezett porton, de módosíthatja azt, ha a VMware-kiszolgáló egy másik porton figyel.

5. A **adja meg a hitelesítő adatok**, válassza ki a korábban létrehozott hitelesítő adatokat.

    ![Adja meg a hitelesítő adatok](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Kattintson a **Hozzáadás** a VMware-kiszolgáló hozzáadása a kiszolgálók listájához. Ezután kattintson a **Next** (Tovább) gombra.

    ![VMWare-kiszolgáló és a hitelesítő adat hozzáadása](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Az a **összefoglalás** kattintson **Hozzáadás** a VMware-kiszolgáló hozzáadása az Azure Backup Server. Az új kiszolgáló nincs ügynök a VMware-kiszolgálón van szükség, azonnal kerül.

    ![VMware-kiszolgáló hozzáadása az Azure Backup Serverhez](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. A beállítások ellenőrzése a **Befejezés** lapot.

   ![A Befejezés lapon](./media/backup-azure-backup-server-vmware/summary-screen.png)

Ha nem a vCenter-kiszolgáló által felügyelt több ESXi-gazdagép rendelkezik, vagy ha a vCenter-kiszolgáló több példányát, futtassa újra a varázslót, a kiszolgálók hozzáadása szeretné.




## <a name="configure-a-protection-group"></a>Védelmi csoportok beállítása

Adja hozzá a VMware virtuális gépek biztonsági mentésének. Védelmi csoportok gyűjtse össze a több virtuális gépet, és a alkalmazni az azonos adatok megőrzése és a biztonsági mentési beállítások a csoportban lévő összes virtuális gép.


1. Az Azure Backup Server konzolon kattintson a **védelmi**, > **új**.

    ![Nyissa meg az új védelmi csoport létrehozása varázsló](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Az a **új védelmi csoport létrehozása** varázsló kezdőlapján kattintson **tovább**.

    ![Új védelmi csoport létrehozása varázsló párbeszédpanelje](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Az a **válassza ki a védelmi csoport típusának** lapra, jelölje be **kiszolgálók** majd **tovább**. A **jelölje ki a csoporttagokat** lap jelenik meg.

1. A **jelölje ki a csoporttagokat** > Válassza ki a virtuális gépeket (vagy virtuális gép mappák), amelyet szeretne készíteni. Ezután kattintson a **Next** (Tovább) gombra.

    - Amikor kijelöl egy mappát vagy virtuális gépek vagy a mappán belül mappákat is kiválasztva a biztonsági mentéshez. Mappák vagy virtuális gépek biztonsági mentése nem kívánja jelölését.
1. Ha egy virtuális gép vagy a mappa már készül biztonsági másolat, nem jelölhető ki. Ez biztosítja, hogy ismétlődő helyreállítási pontok nem hoz létre egy virtuális Gépet. .

     ![Csoporttagok kiválasztása](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)


1. A **adatvédelmi módszer kiválasztása** területén adjon meg egy nevet a védelmi csoport, és a védelmi beállításokat. Vissza Azure-ban, állítsa a rövid távú védelemhez **lemez** és az online védelem. Ezután kattintson a **Next** (Tovább) gombra.

    ![Adatvédelmi módszer kiválasztása](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. A **rövid távú célok megadása**, adja meg, hogy mennyi ideig szeretné megőrizni a adatok biztonsági mentése lemezre.
   - A **megőrzési**, adja meg, hány napig lemez-helyreállítási pontok kell tárolni.
   - A **szinkronizálási gyakoriság**, adja meg, hogy milyen gyakran lemez-helyreállítási pontok.
       - Ha nem szeretné beállítani a biztonsági mentés időköz ellenőrizheti **közvetlenül egy helyreállítási pont előtt** , hogy egy biztonsági mentése fut, minden egyes helyreállítási pontok ütemezése előtt.
       - Rövid távú biztonsági mentések teljes biztonsági mentések, és nem fokozatos.
       - Kattintson a **módosítás** módosítása az időpontok és dátumok rövid távú biztonsági mentések esetén.

     ![Rövid távú célok megadása](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. A **tekintse át a lemezfoglalás**, ellenőrizze a megadott virtuális gép biztonsági mentésének lemezterületet. a virtuális gépek.

   - A javasolt lemezfoglalásokat a megadott megőrzési tartomány, a munkaterhelés típusától és a védett adatok méretének alapulnak. Végezze el a szükséges módosításokat, és kattintson a **tovább**.
   - **Adatok mérete:** A védelmi csoportban található adatok méretét.
   - **Lemezterület:** Az ajánlott mennyiségű lemezterületet a védelmi csoport számára. Ha azt szeretné, a beállítás módosításához, meg kell helyet lefoglalnia, valamivel nagyobb, mint az összeg, amely minden adatforrás növekszik becslései szerint.
   - **A tartozó adatok közös elhelyezése:** Ha bekapcsolja a közös elhelyezést, több adatforrást a védelmi leképezheti egyetlen replika- és helyreállításipont-köteten. A közös elhelyezés minden számítási feladatok esetében nem támogatott.
   - **Automatikus növekedés:** Ha bekapcsolja ezt a beállítást, ha a védelmi csoportban található adatok megnövelni a kezdeti lefoglalt, az Azure Backup Server próbálja meg a 25 %-kal növelni.
   - **Tárolókészlet részletei:** A tárolókészlethez, beleértve a teljes és szabad lemezterülettel állapotát jeleníti meg.

     ![Tekintse át a lemezkiosztást](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. A **replika-létrehozási módszer kiválasztása** csoportjában adja meg, hogyan szeretné a kezdeti biztonsági mentés, és kattintson a **tovább**.
   - Az alapértelmezett érték **automatikusan a hálózaton keresztül** és **most**.
   - Ha használja az alapértelmezett, azt javasoljuk, hogy megad egy csúcsidőn kívüli időpont. Válasszon **később** és a egy napját és időpontját adja meg.
   - Nagy mennyiségű adat- vagy kevésbé optimális hálózati körülmények fontolja meg a kapcsolat nélküli az adatok replikálását a cserélhető adathordozó használatával.

     ![Replika-létrehozási módszer kiválasztása](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. A **konzisztencia-ellenőrzési beállítások**, adja meg, hogyan és mikor érdemes automatizálni a konzisztencia-ellenőrzéseket. Ezután kattintson a **Next** (Tovább) gombra.
      - Konzisztencia-ellenőrzéseket is futtathat, amikor a replikaadatok inkonzisztenssé válik, illetve egy beállított ütemezés szerint.
      - Ha nem szeretne automatikus konzisztencia-ellenőrzést beállítani, egy manuális ellenőrzést is futtathatja. Ehhez kattintson a jobb gombbal a védelmi csoport > **konzisztencia-ellenőrzés végrehajtása**.

1. A **Online védelem adatainak megadása** lapra, jelölje be a virtuális gépek vagy a virtuális gép biztonsági mentéséhez használni kívánt mappákat. Válassza ki egyenként a tagokat, vagy kattintson **válassza ki az összes** minden tag kiválasztása. Ezután kattintson a **Next** (Tovább) gombra.

      ![Online védelem adatainak megadása](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Az a **Online biztonsági mentési ütemezés megadása** csoportjában adja meg, hogy milyen gyakran kívánja a biztonsági másolatokat a helyi tárolóból az Azure-bA.

    - Felhőbeli helyreállítási pontok az adatok jön létre, az ütemezés szerint. Ezután kattintson a **Next** (Tovább) gombra.
    - Miután a helyreállítási pont jön létre, a Recovery Services-tárolót az Azure-ban való átvitelig.

      ![Online biztonsági mentés ütemezésének megadása](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Az a **Online adatmegőrzési szabály megadása** lap, adja meg, mennyi ideig szeretné megőrizni a az Azure-bA a napi/heti/havi/évi biztonsági mentésekből létrehozott helyreállítási pontokat. Kattintson a **tovább**.

    - Nincs nincs határideje, mennyi ideig tárolhatja az adatokat az Azure-ban.
    - A csak határértéke, hogy nem rendelkezik a védett példányonkénti több mint 9999 helyreállítási pontot. Ebben a példában a védett példány a VMware-kiszolgáló.

      ![Online megőrzési szabály megadása](./media/backup-azure-backup-server-vmware/retention-policy.png)


1. Az a **összefoglalás** lapon tekintse át a beállításokat, és kattintson a **csoport létrehozása**.

     ![A védelmi csoport tagja, és a beállítás összegzése](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>A VMWare vSphere 6.7

A biztonsági mentési vSphere 6.7 hajtsa végre a következő:

- Engedélyezze a TLS 1.2-es DPM-kiszolgálón
  >[!Note]
  >VMWare 6.7 és újabb verziók esetében volt engedélyezett TLS kommunikációs protokollként.

- Állítsa be a beállításkulcsokat a következő:  

  Windows beállításjegyzék-szerkesztő, 5.00

  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\. NETFramework\v2.0.50727] "SystemDefaultTlsVersions" = dword: 00000001 "SchUseStrongCrypto" = dword: 00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\. NETFramework\v2.0.50727] "SystemDefaultTlsVersions" = dword: 00000001 "SchUseStrongCrypto" = dword: 00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\. NETFramework\v4.0.30319] "SystemDefaultTlsVersions" = dword: 00000001 s "SchUseStrongCrypto" = dword: 00000001


## <a name="next-steps"></a>További lépések

Problémák elhárítása a biztonsági mentések beállítása során, tekintse át a [hibaelhárítási útmutató Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
