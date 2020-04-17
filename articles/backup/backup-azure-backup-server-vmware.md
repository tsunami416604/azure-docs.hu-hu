---
title: VMware virtuális gépek biztonsági mentése az Azure Backup Server rel
description: Ebből a cikkből megtudhatja, hogyan készíthet idát meg az Azure Backup Server használatával a VMware vCenter/ESXi kiszolgálón futó VMware virtuális gépek biztonsági mentéséhez.
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 92846f9bb9259e55a2c957716676ff42c032b2b5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537406"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>VMware virtuális gépek biztonsági mentése az Azure Backup Server rel

Ez a cikk bemutatja, hogyan készíthet biztonsági másolatot a VMware ESXi gazdagépeken/vCenter Server en futó VMware virtuális gépekről az Azure Backup Server használatával az Azure-ba.

Ez a cikk a következőket ismerteti:

- Hozzon létre egy biztonságos csatornát, hogy az Azure Backup Server képes legyen kommunikálni a VMware-kiszolgálókkal HTTPS-kapcsolaton keresztül.
- Hozzon létre egy VMware-fiókot, amelyet az Azure Backup Server használ a VMware-kiszolgáló eléréséhez.
- Adja hozzá a fiók hitelesítő adatait az Azure Backup hoz.
- Adja hozzá a vCenter vagy ESXi kiszolgálót az Azure Backup Serverhez.
- Állítson be egy védelmi csoportot, amely tartalmazza a biztonsági másolatot készíteni kívánt VMware virtuális gépeket, adja meg a biztonsági mentés beállításait, és ütemezze a biztonsági mentést.

## <a name="before-you-start"></a>Előkészületek

- Ellenőrizze, hogy a vCenter/ESXi olyan verzióját futtatja-e, amely támogatja a biztonsági mentést. Itt olvassa el a támogatási [mátrixot](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix).
- Győződjön meg arról, hogy beállította az Azure Backup Server. Ha még nem, [tedd meg,](backup-azure-microsoft-azure-backup.md) mielőtt elkezdesz. Az Azure Backup Server t a legújabb frissítésekkel kell futtatnia.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Biztonságos kapcsolat létrehozása a vCenter-kiszolgálóval

Alapértelmezés szerint az Azure Backup Server HTTPS-kapcsolaton keresztül kommunikál a VMware-kiszolgálókkal. A HTTPS-kapcsolat beállításához töltse le a VMware hitelesítésszolgáltató (CA) tanúsítványt, és importálja az Azure Backup Server.To set up the HTTPS connection, download the VMware Certificate Authority (CA) certificate, and import it on the Azure Backup Server.

### <a name="before-you-begin"></a>Előkészületek

- Ha nem szeretné használni a HTTPS protokollt, [letilthatja a HTTPS-tanúsítvány érvényesítését az összes VMware-kiszolgálón.](backup-azure-backup-server-vmware.md#disable-https-certificate-validation)
- Általában az Azure Backup Server-gép egy böngészőjéről csatlakozik a vCenter/ESXi kiszolgálóhoz a vSphere webügyfél használatával. Az első alkalommal, amikor ezt teszi, a kapcsolat nem biztonságos, és a következőket jeleníti meg.
- Fontos megérteni, hogy az Azure Backup Server hogyan kezeli a biztonsági mentéseket.
  - Első lépésként az Azure Backup Server biztonsági másolatot készít az adatokról a helyi lemeztárolóba. Az Azure Backup Server egy tárolókészletet, egy lemezkészletet és köteteket használ, amelyeken az Azure Backup Server lemez-helyreállítási pontokat tárol a védett adatokhoz. A tárolókészlet lehet közvetlenül csatlakoztatott tároló (DAS), egy szálas csatorna SAN vagy iSCSI tárolóeszköz vagy SAN. Fontos, hogy a VMware virtuális gép adatainak helyi biztonsági mentéséhez elegendő tárhellyel rendelkezzen.
  - Az Azure Backup Server ezután biztonsági másolatot készít a helyi lemeztárolóról az Azure-ra.
  - [Segítséget kaphat](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) annak kiderítéséhez, hogy mennyi tárhelyre van szüksége. Az információ a DPM, de az Azure Backup Server is használható.

### <a name="set-up-the-certificate"></a>A tanúsítvány beállítása

Hozzon létre egy biztonságos csatornát az alábbiak szerint:

1. Az Azure Backup Server böngészőjében adja meg a vSphere webügyfél URL-címét. Ha a bejelentkezési lap nem jelenik meg, ellenőrizze a kapcsolat és a böngésző proxy beállításait.

    ![vSphere webes ügyfél](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. A vSphere Web Client bejelentkezési lapján kattintson **a Megbízható legfelső szintű hitelesítésszolgáltatói tanúsítványok letöltése**elemre.

    ![Megbízható legfelső szintű hitelesítésszolgáltatói tanúsítvány letöltése](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. A rendszer letölt egy **letöltött** fájlt. A böngészőtől függően megjelenik egy üzenet, amely megkérdezi, hogy megnyitja vagy menti-e a fájlt.

    ![Hitelesítésszolgáltatói tanúsítvány letöltése](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Mentse a fájlt az Azure Backup Server-gépen .zip kiterjesztéssel.

5. Kattintson a jobb gombbal **download.zip** > **Extract All**. A .zip fájl kibontja annak tartalmát a **certs** mappába, amely a következőket tartalmazza:
   - A főtanúsítvány-fájl olyan kiterjesztéssel, amely számozott szekvenciával kezdődik, például .0 és .1.
   - A CRL fájl kiterjesztése kezdődik a sorozat, mint .r0 vagy .r1. A visszavont tanúsítványok listájának fájlja tanúsítványhoz van társítva.

    ![Letöltött tanúsítványok](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. A **certs** mappában kattintson a jobb gombbal a főtanúsítványfájlra > **Átnevezés e.**

    ![Főtanúsítvány átnevezése](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. Módosítsa a főtanúsítvány kiterjesztését .crt-re, és erősítse meg. A fájlikon olyanra változik, amely egy főtanúsítványt jelöl.

8. Kattintson a jobb gombbal a főtanúsítványra, és a legördülő menüben válassza a **Tanúsítvány telepítése parancsot.**

9. A **Tanúsítványimportálás varázslóban**válassza a **Helyi számítógép** lehetőséget a tanúsítvány célhelyeként, majd kattintson a **Tovább**gombra. Ellenőrizze, hogy a rendszer engedélyezi-e a számítógép módosítását.

    ![Varázsló üdvözlője](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. A **Tanúsítványtároló** lapon jelölje **be Az összes tanúsítvány helye a következő tárolóba**lehetőséget, majd a **Tallózás gombra** kattintva válassza ki a tanúsítványtárolót.

    ![Tanúsítvány tárolása](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. A **Tanúsítványtároló kiválasztása csoportban**válassza a **Megbízható legfelső szintű hitelesítésszolgáltatók** lehetőséget a tanúsítványok célmappájaként, majd kattintson az **OK**gombra.

    ![Tanúsítvány célmappája](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. A **Tanúsítványimportálás varázsló befejezésekor**ellenőrizze a mappát, majd kattintson a **Befejezés**gombra.

    ![Annak ellenőrzése, hogy a tanúsítvány a megfelelő mappában van-e](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. A tanúsítvány importálásának megerősítése után jelentkezzen be a vCenter-kiszolgálóra, és ellenőrizze, hogy biztonságos-e a kapcsolat.

### <a name="disable-https-certificate-validation"></a>HTTPS-tanúsítvány-érvényesítés letiltása

Ha biztonságos határok vannak a szervezeten belül, és nem szeretné használni a Https protokollt a VMware-kiszolgálók és az Azure Backup Server-gép között, tiltsa le a HTTPS protokollt az alábbiak szerint:

1. Másolja és illessze be a következő szöveget egy .txt fájlba.

    ```text
    Windows Registry Editor Version 5.00
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
    "IgnoreCertificateValidation"=dword:00000001
    ```

2. Mentse a fájlt az Azure Backup Server gépére **DisableSecureAuthentication.reg**néven.

3. A rendszerleíró bejegyzés aktiválásához kattintson duplán a fájlra.

## <a name="create-a-vmware-role"></a>VMware szerepkör létrehozása

Az Azure Backup Server szüksége van egy felhasználói fiók a v-Center Server/ESXi gazdagép eléréséhez engedéllyel. Hozzon létre egy VMware szerepkört adott jogosultságokkal, majd társítson egy felhasználói fiókot a szerepkörhöz.

1. Jelentkezzen be a vCenter-kiszolgálóra (vagy esxi-állomásra, ha nem használja a vCenter Server kiszolgálót).
2. A **Navigátor** panelen kattintson az **Adminisztráció gombra.**

    ![Felügyelet](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. A **Felügyeleti** > **szerepkörök csoportban**kattintson a szerepkör hozzáadása ikonra (a + szimbólum).

    ![Szerepkör hozzáadása](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. A **Szerepkörszerepkör nevének** > **Role name**létrehozása mezőbe írja be a *BackupAdminRole parancsot.* A szerepkör neve lehet, amit akarsz, de fel kell tudni a szerep céljából.

5. Az alábbi táblázatban jelölje ki a jogosultságokat, majd kattintson az **OK**gombra.  Az új szerepkör megjelenik a **Szerepkörök** panel listájában.
   - A szülőcímke melletti ikonra kattintva bontsa ki a szülőt, és tekintse meg a gyermekjogosultságokat.
   - A VirtualMachine jogosultságok kiválasztásához több szintre kell lépnie a szülő gyermekhierarchiába.
   - Nem kell kiválasztania az összes gyermekjogosultságot egy fölérendelt jogosultságon belül.

    ![Szülő gyermekjogosultság-hierarchia](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Szerepkör-engedélyek

| A vCenter 6.7 felhasználói fiókjának jogosultságai                     | A vCenter 6.5 felhasználói fiókjának jogosultságai                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Adattár-fürt. Datatstore-fürt konfigurálása            | Adattár-fürt. Datatstore-fürt konfigurálása            |
| Datastore.AllocateSpace                                      | Datastore.AllocateSpace                                      |
| Datastore.Tallózás adattár                                   | Datastore.Tallózás adattár                                   |
| Datastore.Low-level fájlműveletek                          | Datastore.Low-level fájlműveletek                          |
| Global.Disable metódusok                                       | Global.Disable metódusok                                       |
| Global.Metódusok engedélyezése                                        | Global.Metódusok engedélyezése                                        |
| Globális.Licencek                                              | Globális.Licencek                                              |
| Global.Log esemény                                             | Global.Log esemény                                             |
| Global.Egyéni attribútumok kezelése                              | Global.Egyéni attribútumok kezelése                              |
| Global.Set egyéni attribútum                                  | Global.Set egyéni attribútum                                  |
| Host.Local műveletek. Virtuális gép létrehozása                | Host.Local műveletek. Virtuális gép létrehozása                |
| Hálózat.Hálózat hozzárendelése                                       | Hálózat.Hálózat hozzárendelése                                       |
| Erőforrás. Virtuális gép hozzárendelése erőforráskészlethez           | Erőforrás. Virtuális gép hozzárendelése erőforráskészlethez           |
| vApp.Add virtuális gép                                     | vApp.Add virtuális gép                                     |
| vApp.Erőforráskészlet hozzárendelése                                    | vApp.Erőforráskészlet hozzárendelése                                    |
| vApp.Unregister                                              | vApp.Unregister                                              |
| VirtualMachine.Configuration . Eszköz hozzáadása vagy eltávolítása          | VirtualMachine.Configuration . Eszköz hozzáadása vagy eltávolítása          |
| Virtuális gép. Configuration.Acquire lemezbérlet            | Virtuális gép. Configuration.Disk címbérlet                     |
| Virtuális gép. Configuration.Add új lemez                   | Virtuális gép. Configuration.Add új lemez                   |
| Virtuális gép. Configuration.Advanced konfiguráció        | Virtuális gép. Configuration.Speciális                       |
| Virtuális gép. Configuration.Totle lemezváltozáskövetés   | Virtuális gép. Configuration.Disk változáskövetés          |
| Virtuális gép. Configuration.Configure állomás USB-eszköz     | Virtuális gép. Configuration.Host USB-eszköz               |
| Virtuális gép. Configuration.Extend virtuális lemez           | Virtuális gép. Configuration.Extend virtuális lemez           |
| Virtuális gép. Configuration.Query nem birtokolt fájlok           | Virtuális gép. Configuration.Query nem birtokolt fájlok           |
| Virtuális gép. Configuration.Change Swapfile elhelyezése     | Virtuális gép. Configuration.Swapfájl elhelyezése            |
| Virtuális gép. Vendég operations.Guest operation program végrehajtása | Virtuális gép. Vendég operations.Guest operation program végrehajtása |
| Virtuális gép. Vendég operations.Guest operations módosítások | Virtuális gép. Vendég operations.Guest operations módosítások |
| Virtuális gép. Vendégműveletek.Vendég műveleti lekérdezések    | Virtuális gép. Vendégműveletek.Vendég műveleti lekérdezések    |
| Virtuális gép . Kölcsönhatás. Eszközkapcsolat             | Virtuális gép . Kölcsönhatás. Eszközkapcsolat             |
| Virtuális gép . Kölcsönhatás. Vendég operációs rendszer kezelése VIX API-val | Virtuális gép . Kölcsönhatás. Vendég operációs rendszer kezelése VIX API-val |
| Virtuális gép . Kölcsönhatás. Kikapcsolás                      | Virtuális gép . Kölcsönhatás. Kikapcsolás                      |
| Virtuális gép . Inventory.Új létrehozása                        | Virtuális gép . Inventory.Új létrehozása                        |
| Virtuális gép . Készlet.Eltávolítás                            | Virtuális gép . Készlet.Eltávolítás                            |
| Virtuális gép . Készlet.Regisztráció                          | Virtuális gép . Készlet.Regisztráció                          |
| Virtuális gép . Provisioning.Lemezhozzáférés engedélyezése             | Virtuális gép . Provisioning.Lemezhozzáférés engedélyezése             |
| Virtuális gép . Provisioning.Fájlhozzáférés engedélyezése             | Virtuális gép . Provisioning.Fájlhozzáférés engedélyezése             |
| Virtuális gép . Provisioning.Read-Only lemezhozzáférés engedélyezése   | Virtuális gép . Provisioning.Read-Only lemezhozzáférés engedélyezése   |
| Virtuális gép . Provisioning.A virtuális gépek letöltésének engedélyezése | Virtuális gép . Provisioning.A virtuális gépek letöltésének engedélyezése |
| Virtuális gép . Pillanatkép-kezelés.  Pillanatkép készítése       | Virtuális gép . Pillanatkép-kezelés.  Pillanatkép készítése       |
| Virtuális gép . Pillanatkép-kezelés. Pillanatkép eltávolítása        | Virtuális gép . Pillanatkép-kezelés. Pillanatkép eltávolítása        |
| Virtuális gép . Pillanatkép-kezelés. Visszaállítás a pillanatképhez     | Virtuális gép . Pillanatkép-kezelés. Visszaállítás a pillanatképhez     |

<br>

| **A vCenter 6.0 felhasználói fiókjának jogosultságai**                | **A vCenter 5.5 felhasználói fiókjának jogosultságai** |
| ---------------------------------------------------------- | ------------------------------------------- |
| Datastore.AllocateSpace                                    | Hálózat.Hozzárendelés                              |
| Global.Egyéni attribútumok kezelése                           | Datastore.AllocateSpace                     |
| Global.Set egyéni attribútum                               | VirtualMachine.Config.ChangeTracking        |
| Host.Local műveletek. Virtuális gép létrehozása              | VirtualMachine.State.RemoveSnapshot         |
| Hálózati.  Hálózat hozzárendelése                                   | VirtualMachine.State.CreateSnapshot         |
| Erőforrás.  Virtuális gép hozzárendelése erőforráskészlethez         | VirtualMachine.Provisioning.DiskRandomRead  |
| Virtuális gép. Configuration.Add új lemez                | VirtualMachine.Interact.PowerOff            |
| Virtuális gép. Configuration.Speciális                    | VirtualMachine.Inventory.Create             |
| Virtuális gép. Configuration.Disk változáskövetés        | VirtualMachine.Config.AddNewDisk            |
| Virtuális gép. Configuration.Host USB-eszköz             | VirtualMachine.Config.HostUSBEszköz         |
| Virtuális gép. Configuration.Query nem birtokolt fájlok         | VirtualMachine.Config.AdvancedConfig        |
| Virtuális gép. Configuration.Swapfájl elhelyezése          | VirtualMachine.Config.SwapElhelyezés         |
| Virtuális gép. Interakció.Kikapcsolás                     | Global.ManageCustomFields mezők                   |
| Virtuális gép. Készlet. Új létrehozása                     |                                             |
| Virtuális gép. Provisioning.Lemezhozzáférés engedélyezése            |                                             |
| Virtuális gép. Kiépítés. Írásvédett lemezhozzáférés engedélyezése |                                             |
| Virtuális gép. Pillanatkép-kezelés. Pillanatkép létrehozása       |                                             |
| Virtuális gép. Pillanatkép-kezelés. Pillanatkép eltávolítása       |                                             |

## <a name="create-a-vmware-account"></a>VMware-fiók létrehozása

1. A VCenter Server **Navigator** panelen kattintson a **Felhasználók és csoportok**elemre. Ha nem használja a vCenter Server, hozza létre a fiókot a megfelelő ESXi gazdagépen.

    ![Felhasználók és csoportok beállítás](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Megjelenik **a vCenter-felhasználók és csoportok** panel.

2. A **vCenter-felhasználók és csoportok** panelen jelölje ki a **Felhasználók** lapot, majd kattintson a Felhasználók hozzáadása ikonra (a + szimbólum).

    ![A vCenter-felhasználók és csoportok panel](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. Az **Új felhasználó** párbeszédpanelen adja meg a felhasználói adatokat > OK **gombra.** Ebben az eljárásban a felhasználónév BackupAdmin.

    ![Új felhasználó párbeszédpanel](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. Ha a felhasználói fiókot a szerepkörhöz szeretné társítani, a **Navigátor** panelen kattintson a **Globális engedélyek gombra.** A **Globális engedélyek** panelen válassza a **Kezelés** lapot, majd kattintson a hozzáadás ikonra (a + szimbólum).

    ![Globális engedélyek panel](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. A **Globális engedélygyökér – Engedély hozzáadása**csoportban kattintson a **Hozzáadás** gombra a felhasználó vagy csoport kiválasztásához.

    ![Felhasználó vagy csoport kiválasztása](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. A **Felhasználók/csoportok kiválasztása**csoportban válassza a BackupAdmin Add **(Biztonságimásolat-rendszergazda** > **hozzáadása) lehetőséget.** A **Felhasználók**párbeszédpanelen a rendszer a *tartomány\felhasználónév* formátumot használja a felhasználói fiókhoz. Ha másik tartományt szeretne használni, válassza ki a **tartomány** listából. A kijelölt felhasználók hozzáadása **párbeszédpanelhez** kattintson az **OK** gombra.

    ![BackupAdmin felhasználó hozzáadása](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. A **Hozzárendelt szerepkör csoportban**a legördülő listában válassza a **BackupAdminRole** > **OK lehetőséget.**

    ![Felhasználó hozzárendelése szerepkörhöz](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

A **Globális engedélyek** panel **Kezelés** lapján az új felhasználói fiók és a társított szerepkör megjelenik a listában.

## <a name="add-the-account-on-azure-backup-server"></a>A fiók hozzáadása az Azure Backup Server en

1. Nyissa meg az Azure Backup Server t. Ha nem találja az ikont az asztalon, nyissa meg a Microsoft Azure Biztonsági mentést az alkalmazások listájából.

    ![Az Azure Backup Server ikonja](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. Az Azure Backup Server konzolon kattintson a **Felügyeleti** >  **kiszolgálók** > **kezelése a VMware kezelése**elemre.

    ![Azure Backup Server konzol](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. A **Hitelesítő adatok kezelése** párbeszédpanelen kattintson a **Hozzáadás**gombra.

    ![Az Azure Backup Server Hitelesítő adatok kezelése párbeszédpanel](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. A **Hitelesítő adatok hozzáadása**mezőbe írja be az új hitelesítő adatok nevét és leírását, és adja meg a VMware-kiszolgálón megadott felhasználónevet és jelszót. A *Contoso Vcenter hitelesítő adatait* a hitelesítő adatok azonosítására használja ebben az eljárásban. Ha a VMware-kiszolgáló és az Azure Backup Server nem ugyanabban a tartományban van, adja meg a tartományt a felhasználónévben.

    ![Az Azure Backup Server Hitelesítő adatok hozzáadása párbeszédpanel](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Az új hitelesítő adatok hozzáadásához kattintson a **Hozzáadás** gombra.

    ![Az Azure Backup Server Hitelesítő adatok kezelése párbeszédpanel](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>A vCenter-kiszolgáló hozzáadása

Adja hozzá a vCenter-kiszolgálót az Azure Backup Server kiszolgálóhoz.

1. Az Azure Backup Server konzolon kattintson a **Felügyeleti** > **éles kiszolgálók** > **hozzáadása elemre.**

    ![Éles kiszolgáló összeadása varázsló megnyitása](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. A **Production Server Addition wizard** > **Select Production Server type (Éles kiszolgáló típusa)** lapon válassza a **VMware-kiszolgálók**lehetőséget, majd kattintson a **Tovább**gombra.

    ![Éles kiszolgáló összeadása varázsló](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. A Számítógépek**kiszolgálónév/IP-cím** **kiválasztása**területen adja meg a VMware-kiszolgáló teljes tartománynevét vagy IP-címét.   Ha az összes ESXi-kiszolgálót ugyanaz a vCenter kezeli, adja meg a vCenter nevét. Ellenkező esetben adja hozzá az ESXi host.otherwise, add hozzá az ESXi host.

    ![VMware-kiszolgáló megadása](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Az **SSL-portban**adja meg a VMware-kiszolgálóval való kommunikációhoz használt portot. A 443 az alapértelmezett port, de módosíthatja, ha a VMware-kiszolgáló egy másik porton figyel.

5. A **Hitelesítő adatok megadása**területen jelölje ki a korábban létrehozott hitelesítő adatokat.

    ![Hitelesítő adatok megadása](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. A **Hozzáadás gombra** kattintva adja hozzá a VMware-kiszolgálót a kiszolgálók listájához. Kattintson a **Tovább** gombra.

    ![VMWare-kiszolgáló és hitelesítő adatok hozzáadása](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Az **Összegzés** lapon kattintson a **Hozzáadás** gombra a VMware-kiszolgáló hozzáadása az Azure Backup Serverhez. Az új kiszolgáló azonnal hozzáadódik, nincs szükség ügynökre a VMware-kiszolgálón.

    ![VMware-kiszolgáló hozzáadása az Azure Backup Server kiszolgálóhoz](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Ellenőrizze a beállításokat a **Befejezés** lapon.

   ![Befejezés oldal](./media/backup-azure-backup-server-vmware/summary-screen.png)

Ha több ESXi-állomással rendelkezik, amelyeket nem a vCenter-kiszolgáló kezel, vagy ha a vCenter Server több példánya van, a kiszolgálók hozzáadásához újra kell futtatnia a varázslót.

## <a name="configure-a-protection-group"></a>Védelmi csoport konfigurálása

VMware virtuális gépek hozzáadása biztonsági mentéshez. A védelmi csoportok több virtuális gépet gyűjtenek össze, és ugyanazokat az adatmegőrzési és biztonsági mentési beállításokat alkalmazzák a csoport összes virtuális gépére.

1. Az Azure Backup Server konzolon kattintson a **Védelem**gombra > **Új parancsra.**

    ![Az Új védelmi csoport létrehozása varázsló megnyitása](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Az **Új védelmi csoport létrehozása** varázsló üdvözlőlapján kattintson a **Tovább**gombra.

    ![Új védelmi csoport létrehozása varázsló párbeszédpanel](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. A **Védelem csoporttípus kiválasztása** lapon válassza a **Kiszolgálók** lehetőséget, majd kattintson a **Tovább**gombra. Megjelenik **a Csoporttagok kijelölése** lap.

1. A **Csoporttagok kiválasztása csoportban**jelölje ki azokat a virtuális gépeket (vagy virtuálisgép-mappákat), amelyekről biztonsági másolatot szeretne tartani. Kattintson a **Tovább** gombra.

    - Ha kijelöl egy mappát, vagy a mappában lévő virtuális gépek vagy mappák is ki vannak jelölve biztonsági mentésre. Törölje a jelet a nem biztonsági másolatot nem tartalmazó mappák ból vagy virtuális gépekről.
1. Ha egy virtuális gépről vagy mappáról már készül biztonsági másolatot, nem jelölheti ki. Ez biztosítja, hogy a virtuális géphez nem jönnek létre ismétlődő helyreállítási pontok.

    ![Csoporttagok kijelölése](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Az **Adatvédelmi módszer kiválasztása** lapon adja meg a védelmi csoport nevét és a védelmi beállításokat. Ha biztonsági másolatot szeretne készíteni az Azure-ról, állítsa be a rövid távú védelmet **a Lemezre,** és engedélyezze az online védelmet. Kattintson a **Tovább** gombra.

    ![Az adatvédelmi módszer kiválasztása](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. A **Rövid távú célok megadása**csoportban adja meg, hogy mennyi ideig szeretné az adatokat lemezre biztonsági másolatot készíteni.
   - A **Megőrzési tartomány ban**adja meg, hogy hány napig kell lemezhelyreállítási pontokat tartani.
   - A **Szinkronizálás gyakorisága**területen adja meg, hogy milyen gyakran kerüljön lemezhelyreállítási pont.
       - Ha nem szeretné beállítani a biztonsági mentési időközt, közvetlenül **a helyreállítási pont előtt** ellenőrizheti, hogy a biztonsági mentés közvetlenül az egyes helyreállítási végpontok ütemezése előtt futjon.
       - A rövid távú biztonsági mentések teljes biztonsági mentések, és nem növekményes.
       - A **Módosítás gombra** kattintva módosíthatja a rövid távú biztonsági mentések időpontját/dátumát.

         ![Rövid távú célok megadása](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. A **Lemezfoglalás áttekintése**területen tekintse át a virtuális gép biztonsági másolatai számára biztosított lemezterületet. a virtuális gépekhez.

   - Az ajánlott lemezfoglalások a megadott megőrzési tartományon, a munkaterhelés típusán és a védett adatok méretén alapulnak. Hajtsa végre a szükséges módosításokat, majd kattintson a **Tovább**gombra.
   - **Adatméret:** A védelmi csoportban lévő adatok mérete.
   - **Lemezterület:** A védelmi csoport számára ajánlott lemezterület. Ha módosítani szeretné ezt a beállítást, az egyes adatforrások becsült mennyiségénél valamivel nagyobb teljes területet kell lefoglalni.
   - **Adatok közös áthelyezése:** Ha bekapcsolja a közös helymeghatározást, a védelemben több adatforrás is leképezhető egyetlen replika- és helyreállításipont-kötetre. A közös helymegosztás nem minden számítási feladatesetén támogatott.
   - **Automatikus növekedés:** Ha bekapcsolja ezt a beállítást, ha a védett csoport ban lévő adatok kinövik a kezdeti lefoglalást, az Azure Backup Server megpróbálja 25 százalékkal növelni a lemez méretét.
   - **A tárolókészlet részletei:** A tárolókészlet állapotát jeleníti meg, beleértve a teljes és a fennmaradó lemezméretet is.

    ![Lemezfoglalás áttekintése](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. A **Replika-létrehozási módszer kiválasztása lapon** adja meg, hogyan szeretné elkészíteni a kezdeti biztonsági mentést, majd kattintson a **Tovább**gombra.
   - Az alapértelmezett beállítás az **Automatikus a hálózaton keresztül** és a **Most**.
   - Ha az alapértelmezett, azt javasoljuk, hogy adjon meg egy csúcsidőn kívüli idő. Válassza **a Később** lehetőséget, és adjon meg egy napot és egy időpontot.
   - Nagy mennyiségű adat vagy az optimálisnál kisebb hálózati feltételek esetén fontolja meg az adatok kapcsolat nélküli replikálását cserélhető adathordozó használatával.

    ![A replika létrehozási módszerének kiválasztása](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. A **Konzisztencia-ellenőrzési beállítások párbeszédpanelen**adja meg, hogyan és mikor szeretné automatizálni a konzisztencia-ellenőrzéseket. Kattintson a **Tovább** gombra.
      - Konzisztencia-ellenőrzéseket futtathat, ha a replikaadatok inkonzisztenssé válnak, vagy egy meghatározott ütemezés szerint.
      - Ha nem szeretné konfigurálni az automatikus konzisztencia-ellenőrzéseket, manuális ellenőrzést futtathat. Ehhez kattintson a jobb gombbal a védelmi csoportra > **Konzisztencia-ellenőrzés végrehajtása parancsra.**

1. Az **Online védelem adatai megadása** lapon jelölje ki azokat a virtuális gépeket vagy virtuálisgép-mappákat, amelyekről biztonsági másolatot szeretne tenni. A tagokat egyenként is kijelölheti, vagy az **összes kijelölése gombra** kattintva kiválaszthatja az összes tagot. Kattintson a **Tovább** gombra.

    ![Online védelmi adatok megadása](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Az **Online biztonsági mentés ütemezésének megadása** lapon adja meg, hogy milyen gyakran szeretne biztonsági másolatot készíteni a helyi tárolóból az Azure-ba.

    - Felhő helyreállítási pontok az adatok hoz létre az ütemezésnek megfelelően. Kattintson a **Tovább** gombra.
    - A helyreállítási pont létrehozása után átkerül a Recovery Services-tárolóba az Azure-ban.

    ![Online biztonsági mentés ütemezésének megadása](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Az **Online adatmegőrzési szabályzat megadása** lapon adja meg, hogy mennyi ideig szeretné megtartani a napi/heti/havi/éves biztonsági mentésből az Azure-ba létrehozott helyreállítási pontokat. majd kattintson a **Tovább**gombra.

    - Nincs időkorlát, hogy mennyi ideig őrizheti meg az adatokat az Azure-ban.
    - Az egyetlen korlát az, hogy nem lehet több, mint 9999 helyreállítási pontok védett példányonként. Ebben a példában a védett példány a VMware-kiszolgáló.

    ![Online adatmegőrzési házirend megadása](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. Az **Összegzés** lapon tekintse át a beállításokat, majd kattintson a **Csoport létrehozása gombra.**

    ![A védelmi csoport tagja és az összegzés beállítása](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6,7

A vSphere 6.7 biztonsági másolatot szeretne tenni a következőkkel:

- TLS 1.2 engedélyezése A DPM-kiszolgálón

>[!NOTE]
>VMWare 6.7-től volt TLS engedélyezve kommunikációs protokoll.

- Állítsa be a beállításkulcsokat az alábbiak szerint:

```text
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001
```

## <a name="next-steps"></a>További lépések

A biztonsági mentések beállításával kapcsolatos hibákelhárítási problémákról az [Azure Backup Server hibaelhárítási útmutatójában](./backup-azure-mabs-troubleshoot.md)talál.
