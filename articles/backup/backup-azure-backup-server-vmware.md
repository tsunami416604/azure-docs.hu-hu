---
title: VMware virtuális gépek biztonsági mentése a Azure Backup Server
description: Ebből a cikkből megtudhatja, hogyan használhatja a Azure Backup Servert a VMware vCenter/ESXi-kiszolgálón futó VMware virtuális gépek biztonsági mentésére.
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 92846f9bb9259e55a2c957716676ff42c032b2b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537406"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>VMware virtuális gépek biztonsági mentése a Azure Backup Server

Ez a cikk azt ismerteti, hogyan lehet biztonsági másolatot készíteni a VMware ESXi gazdagépeken/vCenter Servereken futó VMware virtuális gépekről az Azure-ban Azure Backup Server használatával.

Ez a cikk a következőket ismerteti:

- Hozzon létre egy biztonságos csatornát, hogy Azure Backup Server képes legyen kommunikálni a VMware-kiszolgálókkal HTTPS-kapcsolaton keresztül.
- Állítson be egy VMware-fiókot, amelyet Azure Backup Server használ a VMware-kiszolgáló eléréséhez.
- Adja hozzá a fiók hitelesítő adatait Azure Backuphoz.
- Adja hozzá a vCenter-vagy ESXi-kiszolgálót a Azure Backup Serverhoz.
- Állítson be egy védelmi csoportot, amely tartalmazza azokat a VMware virtuális gépeket, amelyekről biztonsági másolatot szeretne készíteni, adja meg a biztonsági mentési beállításokat, és ütemezze a biztonsági mentést.

## <a name="before-you-start"></a>Előkészületek

- Győződjön meg arról, hogy a vCenter/ESXi olyan verzióját futtatja, amely támogatja a biztonsági mentést. Tekintse meg a támogatási mátrixot [itt](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix).
- Győződjön meg arról, hogy beállította Azure Backup Server. Ha még nem tette meg, a Kezdés előtt [végezze el a](backup-azure-microsoft-azure-backup.md) következőt:. A legújabb frissítésekkel Azure Backup Servert kell futtatnia.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Biztonságos kapcsolatok létrehozása a vCenter Server

Alapértelmezés szerint a Azure Backup Server HTTPS-kapcsolaton keresztül kommunikál a VMware-kiszolgálókkal. A HTTPS-kapcsolat beállításához töltse le a VMware hitelesítésszolgáltató (CA) tanúsítványát, és importálja azt a Azure Backup Server.

### <a name="before-you-begin"></a>Előkészületek

- Ha nem szeretné a HTTPS-t használni, [Tiltsa le a https-tanúsítvány érvényesítését az összes VMware-kiszolgálón](backup-azure-backup-server-vmware.md#disable-https-certificate-validation).
- Általában a vSphere webes ügyfélprogram használatával kapcsolódhat a vCenter/ESXi-kiszolgálóhoz a Azure Backup Server gép böngészőjében. Első alkalommal a kapcsolat nem biztonságos, és a következőt fogja megjeleníteni.
- Fontos megérteni, hogyan kezeli a Azure Backup Server a biztonsági mentéseket.
  - Első lépésként Azure Backup Server az adatbiztonsági mentést a helyi lemezes tárhelyre. Azure Backup Server egy tárolót használ, amely lemezeket és köteteket tartalmaz, amelyeken Azure Backup Server tárolja a lemez helyreállítási pontjait a védett adatbázisokhoz. A Storage-készlet lehet közvetlenül csatlakoztatott tároló (DAS), Fibre Channel SAN vagy iSCSI tárolóeszköz vagy SAN. Fontos, hogy elegendő tárterületet biztosítson a VMware VM-adatai helyi biztonsági mentéséhez.
  - Azure Backup Server ezután biztonsági másolatot készít a helyi lemezes tárterületről az Azure-ba.
  - [Segítséget kaphat](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) a szükséges tárhely mennyiségének meghatározásához. Az információ a DPM, de Azure Backup Server is használható.

### <a name="set-up-the-certificate"></a>A tanúsítvány beállítása

A következőképpen állíthatja be a biztonságos csatornát:

1. A Azure Backup Server böngészőjében adja meg a vSphere webes ügyfél URL-címét. Ha a bejelentkezési oldal nem jelenik meg, ellenőrizze a kapcsolatok és a böngésző proxybeállításait.

    ![vSphere webes ügyfél](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. A vSphere webes ügyfél bejelentkezés lapján kattintson a **megbízható legfelső szintű hitelesítésszolgáltatói tanúsítványok letöltése**elemre.

    ![Megbízható legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány letöltése](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. A **Letöltés** nevű fájl le van töltve. A böngészőtől függően megjelenik egy üzenet, amely megkérdezi, hogy meg kell-e nyitni vagy menteni szeretné a fájlt.

    ![HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány letöltése](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Mentse a fájlt a Azure Backup Server gépre. zip kiterjesztéssel.

5. Kattintson a jobb gombbal a **download. zip** > **kibontása**elemre. A. zip fájl kibontja a tartalmát a **tanúsítványok** mappába, amely a következőket tartalmazza:
   - A főtanúsítvány fájlja egy olyan bővítménnyel, amely egy számú, például. 0 és. 1 típusú sorszámmal kezdődik.
   - A CRL-fájl kiterjesztése olyan, mint. R0 vagy. R1. A CRL-fájl egy tanúsítvánnyal van társítva.

    ![Letöltött tanúsítványok](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. A **tanúsítványok** mappában kattintson a jobb gombbal a főtanúsítvány fájl > **Átnevezés**elemre.

    ![Főtanúsítvány átnevezése](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. Módosítsa a főtanúsítvány bővítményét. CRT-re, és erősítse meg. A fájl ikon egy főtanúsítványt jelképező egyikre változik.

8. Kattintson a jobb gombbal a főtanúsítványra, majd az előugró menüben válassza a **tanúsítvány telepítése**lehetőséget.

9. A **tanúsítvány importálása varázslóban**válassza a **helyi gép** lehetőséget a tanúsítvány célhelye, majd kattintson a **tovább**gombra. Győződjön meg arról, hogy a rendszer megkérdezi, hogy szeretné-e engedélyezni a számítógép módosításait.

    ![Varázsló – Üdvözöljük](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. A **tanúsítványtároló** lapon válassza a **minden tanúsítvány tárolása a következő tárolóban**lehetőséget, majd kattintson a **Tallózás** gombra a tanúsítványtároló kiválasztásához.

    ![Tanúsítvány tárolása](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. A **tanúsítványtároló kiválasztása**területen válassza ki a **megbízható legfelső szintű hitelesítésszolgáltatók** mappát a tanúsítványok célmappájában, majd kattintson **az OK**gombra.

    ![Tanúsítvány célmappája](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. A **tanúsítvány importálása varázsló befejezése lapon**ellenőrizze a mappát, majd kattintson a **Befejezés**gombra.

    ![A tanúsítvány ellenőrzése a megfelelő mappában van](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. A tanúsítvány importálásának megerősítése után jelentkezzen be a vCenter Serverba, és ellenőrizze, hogy a kapcsolódás biztonságos-e.

### <a name="disable-https-certificate-validation"></a>HTTPS-tanúsítvány ellenőrzésének letiltása

Ha a szervezeten belül biztonságos határok vannak, és nem szeretné a VMware-kiszolgálók és a Azure Backup Server gép közötti HTTPS protokollt használni, tiltsa le a HTTPS-t a következőképpen:

1. Másolja és illessze be az alábbi szöveget egy. txt fájlba.

    ```text
    Windows Registry Editor Version 5.00
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
    "IgnoreCertificateValidation"=dword:00000001
    ```

2. Mentse a fájlt a Azure Backup Server gépen a **DisableSecureAuthentication. reg**néven.

3. A beállításjegyzék-bejegyzés aktiválásához kattintson duplán a fájlra.

## <a name="create-a-vmware-role"></a>VMware-szerepkör létrehozása

A Azure Backup Server egy olyan felhasználói fiókra van szüksége, amely jogosult a v-Center Server/ESXi-gazdagép elérésére. Hozzon létre egy VMware-szerepkört meghatározott jogosultságokkal, majd társítson egy felhasználói fiókot a szerepkörhöz.

1. Jelentkezzen be a vCenter Serverba (vagy ESXi-gazdagépre, ha nem használja a vCenter Server).
2. A **navigátor** panelen kattintson az **Adminisztráció**elemre.

    ![Felügyelet](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. Az **adminisztrációs** > **szerepkörök**területen kattintson a szerepkör hozzáadása ikonra (a + szimbólum).

    ![Szerepkör hozzáadása](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. A **szerepkör** > -szerepkör létrehozása mezőben adja meg a *BackupAdminRole***nevet**. A szerepkör neve lehet bármilyen hasonló, de felismerhetőnek kell lennie a szerepkör céljához.

5. Válassza ki az alábbi táblázatban összefoglalt jogosultságokat, majd kattintson az **OK**gombra.  Az új szerepkör megjelenik a **szerepkörök** panel listájában.
   - A szülő címke melletti ikonra kattintva bontsa ki a szülőt, és tekintse meg a gyermek jogosultságokat.
   - A VirtualMachine jogosultságok kiválasztásához több szintet kell megadnia a szülő gyermek hierarchiába.
   - Nem kell kijelölnie az összes gyermek jogosultságot a szülő jogosultságon belül.

    ![Szülő gyermek jogosultsági hierarchia](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Szerepkör-engedélyek

| Jogosultságok a vCenter 6,7 felhasználói fiókhoz                     | Jogosultságok a vCenter 6,5 felhasználói fiókhoz                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Adattár-fürt. Datatstore-fürt konfigurálása            | Adattár-fürt. Datatstore-fürt konfigurálása            |
| Adattár. AllocateSpace                                      | Adattár. AllocateSpace                                      |
| Adattár. Tallózás az adattárban                                   | Adattár. Tallózás az adattárban                                   |
| Adattár. alacsony szintű fájl műveletei                          | Adattár. alacsony szintű fájl műveletei                          |
| Globális. disable metódusok                                       | Globális. disable metódusok                                       |
| Globális. Enable metódusok                                        | Globális. Enable metódusok                                        |
| Global. licenses                                              | Global. licenses                                              |
| Globális. log esemény                                             | Globális. log esemény                                             |
| Globális. egyéni attribútumok kezelése                              | Globális. egyéni attribútumok kezelése                              |
| Globális. egyéni attribútum beállítása                                  | Globális. egyéni attribútum beállítása                                  |
| Gazdagép. local művelet. Virtuális gép létrehozása                | Gazdagép. local művelet. Virtuális gép létrehozása                |
| Hálózat. hálózati hozzárendelés                                       | Hálózat. hálózati hozzárendelés                                       |
| Erőforrás. Virtuális gép erőforrás-készlethez rendelése           | Erőforrás. Virtuális gép erőforrás-készlethez rendelése           |
| vApp. virtuális gép hozzáadása                                     | vApp. virtuális gép hozzáadása                                     |
| vApp. erőforrás-készlet társítása                                    | vApp. erőforrás-készlet társítása                                    |
| vApp. Regisztráció törlése                                              | vApp. Regisztráció törlése                                              |
| VirtualMachine. Configuration. Eszköz hozzáadása vagy eltávolítása          | VirtualMachine. Configuration. Eszköz hozzáadása vagy eltávolítása          |
| Virtuális gép. Konfiguráció. a lemez bérletének beolvasása            | Virtuális gép. Konfiguráció. Disk bérlet                     |
| Virtuális gép. Konfiguráció. új lemez hozzáadása                   | Virtuális gép. Konfiguráció. új lemez hozzáadása                   |
| Virtuális gép. Konfiguráció. Speciális konfiguráció        | Virtuális gép. Konfigurálás. speciális                       |
| Virtuális gép. Konfiguráció. lemez változás-követésének váltása   | Virtuális gép. Konfiguráció. lemez változásának követése          |
| Virtuális gép. Konfigurálás. gazdagép USB-eszközének konfigurálása     | Virtuális gép. Configuration. Host USB-eszköz               |
| Virtuális gép. Konfiguráció. virtuális lemez kiterjesztése           | Virtuális gép. Konfiguráció. virtuális lemez kiterjesztése           |
| Virtuális gép. Konfiguráció. a nem tulajdonában lévő fájlok lekérdezése           | Virtuális gép. Konfiguráció. a nem tulajdonában lévő fájlok lekérdezése           |
| Virtuális gép. Configuration. Change swapfile elhelyezése     | Virtuális gép. Configuration. swapfile elhelyezése            |
| Virtuális gép. Guest Operations. Guest Operation program végrehajtása | Virtuális gép. Guest Operations. Guest Operation program végrehajtása |
| Virtuális gép. Vendég műveletek. vendég művelet módosításai | Virtuális gép. Vendég műveletek. vendég művelet módosításai |
| Virtuális gép. Vendég műveletek. vendég műveleti lekérdezések    | Virtuális gép. Vendég műveletek. vendég műveleti lekérdezések    |
| Virtuális gép. Kölcsönhatás. Eszköz csatlakoztatása             | Virtuális gép. Kölcsönhatás. Eszköz csatlakoztatása             |
| Virtuális gép. Kölcsönhatás. Vendég operációs rendszer felügyelete a VIX API-val | Virtuális gép. Kölcsönhatás. Vendég operációs rendszer felügyelete a VIX API-val |
| Virtuális gép. Kölcsönhatás. Kikapcsolás                      | Virtuális gép. Kölcsönhatás. Kikapcsolás                      |
| Virtuális gép. Leltár. új létrehozása                        | Virtuális gép. Leltár. új létrehozása                        |
| Virtuális gép. Leltár. Eltávolítás                            | Virtuális gép. Leltár. Eltávolítás                            |
| Virtuális gép. Leltár. regisztráció                          | Virtuális gép. Leltár. regisztráció                          |
| Virtuális gép. Kiépítés. lemezes hozzáférés engedélyezése             | Virtuális gép. Kiépítés. lemezes hozzáférés engedélyezése             |
| Virtuális gép. Kiépítés. fájlok hozzáférésének engedélyezése             | Virtuális gép. Kiépítés. fájlok hozzáférésének engedélyezése             |
| Virtuális gép. Kiépítés. írásvédett lemezes hozzáférés engedélyezése   | Virtuális gép. Kiépítés. írásvédett lemezes hozzáférés engedélyezése   |
| Virtuális gép. Üzembe helyezés. virtuális gép letöltésének engedélyezése | Virtuális gép. Üzembe helyezés. virtuális gép letöltésének engedélyezése |
| Virtuális gép. Pillanatképek kezelése.  Pillanatkép készítése       | Virtuális gép. Pillanatképek kezelése.  Pillanatkép készítése       |
| Virtuális gép. Pillanatképek kezelése. Pillanatkép eltávolítása        | Virtuális gép. Pillanatképek kezelése. Pillanatkép eltávolítása        |
| Virtuális gép. Pillanatképek kezelése. Helyreállítás pillanatképre     | Virtuális gép. Pillanatképek kezelése. Helyreállítás pillanatképre     |

<br>

| **Jogosultságok a vCenter 6,0 felhasználói fiókhoz**                | **Jogosultságok a vCenter 5,5 felhasználói fiókhoz** |
| ---------------------------------------------------------- | ------------------------------------------- |
| Adattár. AllocateSpace                                    | Network. assign                              |
| Globális. egyéni attribútumok kezelése                           | Adattár. AllocateSpace                     |
| Globális. egyéni attribútum beállítása                               | VirtualMachine. config. változáskövetési        |
| Gazdagép. local művelet. Virtuális gép létrehozása              | VirtualMachine. State. RemoveSnapshot         |
| Hálózati.  Hálózat kiosztása                                   | VirtualMachine. State. CreateSnapshot         |
| Erőforrás.  Virtuális gép erőforrás-készlethez rendelése         | VirtualMachine. kiépítés. DiskRandomRead  |
| Virtuális gép. Konfiguráció. új lemez hozzáadása                | VirtualMachine. Interact. erő            |
| Virtuális gép. Konfigurálás. speciális                    | VirtualMachine. Inventory. Create             |
| Virtuális gép. Konfiguráció. lemez változásának követése        | VirtualMachine. config. AddNewDisk            |
| Virtuális gép. Configuration. Host USB-eszköz             | VirtualMachine. config. HostUSBDevice         |
| Virtuális gép. Konfiguráció. a nem tulajdonában lévő fájlok lekérdezése         | VirtualMachine. config. AdvancedConfig        |
| Virtuális gép. Configuration. swapfile elhelyezése          | VirtualMachine. config. SwapPlacement         |
| Virtuális gép. Interakció. kikapcsolás                     | Global. ManageCustomFields                   |
| Virtuális gép. Hardverleltár. Új létrehozása                     |                                             |
| Virtuális gép. Kiépítés. lemezes hozzáférés engedélyezése            |                                             |
| Virtuális gép. Kiépítési. Írásvédett lemezes hozzáférés engedélyezése |                                             |
| Virtuális gép. Pillanatképek kezelése. Pillanatkép létrehozása       |                                             |
| Virtuális gép. Pillanatképek kezelése. Pillanatkép eltávolítása       |                                             |

## <a name="create-a-vmware-account"></a>VMware-fiók létrehozása

1. VCenter Server- **navigátor** panelen kattintson a **felhasználók és csoportok**elemre. Ha nem használja a vCenter Server, hozza létre a fiókot a megfelelő ESXi-gazdagépen.

    ![Felhasználók és csoportok beállítás](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Megjelenik a **vCenter-felhasználók és-csoportok** panel.

2. A **vCenter-felhasználók és-csoportok** panelen válassza a **felhasználók** lapot, majd kattintson a felhasználók hozzáadása ikonra (a + szimbólum).

    ![vCenter-felhasználók és-csoportok panel](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. Az **új felhasználó** párbeszédpanelen adja hozzá a felhasználói adatokat > **OK gombra**. Ebben az eljárásban a Felhasználónév a BackupAdmin.

    ![Új felhasználó párbeszédpanel](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. Ahhoz, hogy a felhasználói fiókot társítsa a szerepkörhöz, a **navigátor** panelen kattintson a **globális engedélyek**elemre. A **globális engedélyek** panelen válassza a **kezelés** fület, majd kattintson a Hozzáadás ikonra (a + szimbólum).

    ![Globális engedélyek panel](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. A **globális engedély root-Add engedély**elemnél kattintson a **Hozzáadás** gombra a felhasználó vagy csoport kiválasztásához.

    ![Felhasználó vagy csoport kiválasztása](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. A **felhasználók/csoportok kiválasztása lapon**válassza a **BackupAdmin** > **Hozzáadás**lehetőséget. A **felhasználók**a felhasználói fiókhoz a *tartomány \ Felhasználónév* formátumot használják. Ha másik tartományt szeretne használni, válassza ki a **tartományt a tartomány** listából. Kattintson az **OK** gombra a kijelölt felhasználók hozzáadásához az **engedély hozzáadása** párbeszédpanelen.

    ![BackupAdmin-felhasználó hozzáadása](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. A **hozzárendelt szerepkör**listából válassza a legördülő lista **BackupAdminRole** > **OK**elemét.

    ![Felhasználó társítása szerepkörhöz](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

A **globális engedélyek** panel **kezelés** lapján az új felhasználói fiók és a társított szerepkör megjelenik a listában.

## <a name="add-the-account-on-azure-backup-server"></a>Fiók hozzáadása Azure Backup Server

1. Nyissa meg Azure Backup Server. Ha nem találja az ikont az asztalon, nyissa meg Microsoft Azure Backup az alkalmazások listából.

    ![Azure Backup Server ikon](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. A Azure Backup Server-konzolon kattintson a **felügyeleti** >  **üzemi kiszolgálók** > **kezelése VMware**elemre.

    ![Azure Backup Server konzol](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. A **hitelesítő adatok kezelése** párbeszédpanelen kattintson a **Hozzáadás**gombra.

    ![Azure Backup Server hitelesítő adatok kezelése párbeszédpanel](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. A **hitelesítő adatok hozzáadása**lapon adja meg az új hitelesítő adat nevét és leírását, majd adja meg a VMware-kiszolgálón megadott felhasználónevet és jelszót. A *contoso vCenter hitelesítő adatai* a hitelesítő adatok azonosítására szolgálnak ebben az eljárásban. Ha a VMware-kiszolgáló és a Azure Backup Server nem ugyanabban a tartományban van, adja meg a tartományt a felhasználónévben.

    ![Azure Backup Server hitelesítő adat hozzáadása párbeszédpanel](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Az új hitelesítő adat hozzáadásához kattintson a **Hozzáadás** gombra.

    ![Azure Backup Server hitelesítő adatok kezelése párbeszédpanel](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>A vCenter Server hozzáadása

Adja hozzá a vCenter Servert a Azure Backup Serverhoz.

1. A Azure Backup Server-konzolon kattintson a **felügyeleti** > **üzemi kiszolgálók** > **Hozzáadás**elemre.

    ![Az üzemi kiszolgáló hozzáadása varázsló megnyitása](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. Az **üzemi kiszolgáló hozzáadása varázslóban** > **válassza ki a termelési kiszolgáló típusa** lapot, válassza ki a **VMware-kiszolgálók**elemet, majd kattintson a **tovább**gombra.

    ![Üzemi kiszolgáló hozzáadása varázsló](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. A **számítógépek**  **kiszolgáló nevének/IP-címének**kiválasztása lapon adja meg a VMware-kiszolgáló teljes tartománynevét vagy IP-címét. Ha az ESXi-kiszolgálók mindegyikét ugyanazzal a vCenter felügyeli, adja meg a vCenter nevét. Ellenkező esetben adja hozzá az ESXi-gazdagépet.

    ![VMware-kiszolgáló meghatározása](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Az **SSL-port**mezőben adja meg a VMware-kiszolgálóval való kommunikációhoz használt portot. 443 az alapértelmezett port, de megváltoztathatja, ha a VMware-kiszolgáló egy másik porton figyel.

5. A **hitelesítő adatok megadása**területen válassza ki a korábban létrehozott hitelesítő adatokat.

    ![Hitelesítő adat megadása](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. A **Hozzáadás** gombra kattintva vegye fel a VMware-kiszolgálót a kiszolgálók listára. Kattintson a **Tovább** gombra.

    ![VMWare-kiszolgáló és hitelesítő adat hozzáadása](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Az **Összefoglalás** lapon kattintson a **Hozzáadás** gombra a VMware-kiszolgáló Azure Backup Serverhoz való hozzáadásához. Az új kiszolgáló hozzáadása azonnal megtörténik, nincs szükség ügynökre a VMware-kiszolgálón.

    ![VMware-kiszolgáló hozzáadása a Azure Backup Serverhoz](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Ellenőrizze a beállításokat a **Befejezés** lapon.

   ![Befejezés lap](./media/backup-azure-backup-server-vmware/summary-screen.png)

Ha több ESXi-gazdagépe nem a vCenter-kiszolgáló által felügyelt, vagy ha vCenter Server több példánya van, újra kell futtatnia a varázslót a kiszolgálók hozzáadásához.

## <a name="configure-a-protection-group"></a>Védelmi csoport konfigurálása

VMware virtuális gépek hozzáadása a biztonsági mentéshez. A védelmi csoportok több virtuális gépet gyűjtenek, és ugyanazokat az adatmegőrzési és biztonsági mentési beállításokat alkalmazzák a csoport összes virtuális gépére.

1. A Azure Backup Server-konzolon kattintson a **védelem**elemre, > **új**elemre.

    ![Az új védelmi csoport létrehozása varázsló megnyitása](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Az **új védelmi csoport létrehozása** varázsló kezdőlapján kattintson a **tovább**gombra.

    ![Új védelmi csoport létrehozása varázsló párbeszédpanel](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. A **védelmi csoport típusának kiválasztása** lapon válassza a **kiszolgálók** elemet, majd kattintson a **tovább**gombra. Megjelenik a **csoporttagok kiválasztása** lap.

1. A **csoporttagok kiválasztása**területen válassza ki azokat a virtuális gépeket (vagy virtuálisgép-mappákat), amelyekről biztonsági másolatot szeretne készíteni. Kattintson a **Tovább** gombra.

    - Ha kijelöl egy mappát, vagy a mappában található virtuális gépek vagy mappák is ki vannak választva a biztonsági mentéshez. Törölheti azokat a mappákat vagy virtuális gépeket, amelyekről nem kíván biztonsági másolatot készíteni.
1. Ha már folyamatban van egy virtuális gép vagy mappa biztonsági mentése, azt nem lehet kijelölni. Ez biztosítja, hogy a rendszer duplikált helyreállítási pontokat hozzon létre egy virtuális géphez.

    ![Csoporttagok kiválasztása](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Az **adatvédelmi módszer kiválasztása** lapon adja meg a védelmi csoport nevét, valamint a védelmi beállításokat. Az Azure-ba történő biztonsági mentéshez állítsa be a rövid távú védelmet a **lemezre** , és engedélyezze az online védelmet. Kattintson a **Tovább** gombra.

    ![Az adatvédelmi módszer kiválasztása](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. A **rövid távú célok megadása**területen határozza meg, hogy mennyi ideig szeretné megőrizni a lemezre történő biztonsági mentést.
   - A **megőrzési tartomány**mezőben határozza meg, hogy hány nap elteltével kell megőrizni a lemezes helyreállítási pontokat.
   - A **szinkronizálás gyakorisága**mezőben határozza meg, hogy milyen gyakran történjen lemezes helyreállítási pont.
       - Ha nem szeretné beállítani a biztonsági mentés időközét, akkor **közvetlenül egy helyreállítási pont előtt** is megtekintheti, hogy a biztonsági mentés az egyes helyreállítási pontok ütemezése előtt fusson.
       - A rövid távú biztonsági mentések teljes biztonsági mentést biztosítanak, és nem növekményes.
       - A rövid távú biztonsági mentések időpontjának és dátumának módosításához kattintson a **módosítás** gombra.

         ![Rövid távú célok megadása](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. A **lemez kiosztásának áttekintése**lapon tekintse át a virtuális gépek biztonsági másolatainak rendelkezésre álló lemezterületet. a virtuális gépek esetében.

   - Az ajánlott lemez-foglalások a megadott megőrzési időtartamon, a munkaterhelés típusától és a védett adatok méretén alapulnak. Végezze el a szükséges módosításokat, majd kattintson a **tovább**gombra.
   - **Adatméret:** A védelmi csoportban lévő adatméret.
   - **Lemezterület:** A védelmi csoport számára ajánlott lemezterület. Ha módosítani kívánja ezt a beállítást, akkor az egyes adatforrások által becsült mennyiségnél valamivel nagyobb teljes területet le kell foglalni.
   - **Az adatkeresés:** Ha bekapcsolja a közös elhelyezést, a védelemben több adatforrás is leképezhető egyetlen replika és egy helyreállítási pont kötetre. Az egyhelyes elhelyezés nem támogatott az összes munkaterhelésnél.
   - **Automatikus növekedés:** Ha bekapcsolja ezt a beállítást, ha a védett csoportban lévő adatmennyiség meghaladja a kezdeti foglalást, a Azure Backup Server 25 százalékkal próbálkozik a lemez méretének növelésével.
   - **Storage-készlet részletei:** Megjeleníti a tárterület állapotát, beleértve a teljes és a fennmaradó lemez méretét.

    ![Lemez kiosztásának áttekintése](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. A **replika-létrehozási módszer kiválasztása** lapon adja meg, hogyan szeretné elkészíteni a kezdeti biztonsági mentést, majd kattintson a **tovább**gombra.
   - Az alapértelmezett érték **automatikusan a hálózaton keresztül** történik, és **most**.
   - Ha az alapértelmezett beállítást használja, azt javasoljuk, hogy válasszon ki egy off-Peak időpontot. Válassza a **később** lehetőséget, és adjon meg egy napot és egy időpontot.
   - Nagy mennyiségű vagy kevésbé optimális hálózati feltételnél érdemes lehet a cserélhető adathordozó használatával offline módon replikálni az adatfájlokat.

    ![Replika-létrehozási módszer kiválasztása](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. A **konzisztencia-ellenőrzés beállításainál**válassza ki, hogyan és mikor szeretné automatizálni a konzisztencia-ellenőrzéseket. Kattintson a **Tovább** gombra.
      - A konzisztencia-ellenőrzéseket futtathatja, ha a replika adatai inkonzisztensek, vagy egy meghatározott ütemterv alapján.
      - Ha nem szeretne automatikus konzisztencia-ellenőrzést beállítani, akkor manuális ellenőrzést is futtathat. Ehhez kattintson a jobb gombbal a védelmi csoportra > **konzisztencia-ellenőrzés végrehajtása**parancsra.

1. Az **online védelmi adatkapcsolat megadása** oldalon válassza ki azokat a virtuális gépeket vagy virtuálisgép-mappákat, amelyekről biztonsági másolatot szeretne készíteni. A tagokat egyenként is kiválaszthatja, vagy az **összes kijelölése** lehetőségre kattintva kiválaszthatja az összes tagot. Kattintson a **Tovább** gombra.

    ![Online védelmi adatértékek meghatározása](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Az **online biztonsági mentés** időpontjának megadása oldalon határozza meg, hogy milyen gyakran szeretné biztonsági mentést készíteni a helyi tárolóból az Azure-ba.

    - Az adatfelhőbeli helyreállítási pontok az ütemterv szerint lesznek létrehozva. Kattintson a **Tovább** gombra.
    - A helyreállítási pont létrehozása után a rendszer átviszi a Recovery Services-tárolóba az Azure-ban.

    ![Online biztonsági mentési ütemterv megadása](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Az **online adatmegőrzési szabály meghatározása** lapon adja meg, hogy mennyi ideig szeretné megőrizni a napi/heti/havi/éves biztonsági másolatokből az Azure-ba létrehozott helyreállítási pontokat. Ezután kattintson a **tovább**gombra.

    - Nincs időkorlát arra vonatkozóan, hogy mennyi ideig tarthat az Azure-beli adatmegőrzés.
    - Az egyetlen korlát, hogy a védett példányok esetében nem lehet több, mint 9999 helyreállítási pont. Ebben a példában a védett példány a VMware-kiszolgáló.

    ![Online adatmegőrzési szabály meghatározása](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. Az **Összefoglalás** lapon tekintse át a beállításokat, majd kattintson a **csoport létrehozása**elemre.

    ![Védelmi csoport tagja és a beállítás összegzése](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6,7

A 6,7-es vSphere biztonsági mentéséhez tegye a következőket:

- A TLS 1,2 engedélyezése a DPM-kiszolgálón

>[!NOTE]
>A VMWare 6,7-es verziójában a TLS engedélyezve volt a kommunikációs protokollként.

- A beállításkulcsokat a következőképpen állíthatja be:

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

A biztonsági másolatok beállítása során felmerülő problémák elhárításához tekintse át a [Azure Backup Server hibaelhárítási útmutatóját](./backup-azure-mabs-troubleshoot.md).
