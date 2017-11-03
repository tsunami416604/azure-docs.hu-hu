---
title: "Készítsen biztonsági másolatot a VMware Server az Azure Backup Server |} Microsoft Docs"
description: "Azure Backup Server használatával egy VMware vCenter/ESXi-kiszolgálók biztonsági mentését Azure vagy lemezre. Ez a cikk ismerteti a lépés = biztonsági mentése (vagy védelmének)-lépésre utasítást a VMware-munkaterhelések."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
ms.assetid: 6b131caf-de85-4eba-b8e6-d8a04545cd9d
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/24/2017
ms.author: markgal;
ms.openlocfilehash: ad331dffb7c31d12290f4223967c568e4535fe3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-a-vmware-server-to-azure"></a>Készítsen biztonsági másolatot egy VMware-kiszolgálóról az Azure-bA

Ez a cikk ismerteti, hogyan konfigurálhatja az Azure Backup Server VMware kiszolgáló-munkaterhelések védelme érdekében. Ez a cikk feltételezi, hogy már rendelkezik Azure Backup Server telepítve. Ha még nem rendelkezik telepített Azure Backup Server, lásd: [Felkészülés az Azure Backup Server használatával-munkaterhelések biztonsági mentése](backup-azure-microsoft-azure-backup.md).

Az Azure Backup Server készítsen biztonsági másolatot, vagy védelme, a VMware vCenter Server verziója 6.5, 6.0 és 5.5.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>A vCenter-kiszolgáló biztonságos kapcsolat létrehozása

Alapértelmezés szerint Azure Backup Server minden vCenter-kiszolgáló egy HTTPS-csatornán keresztül kommunikál. Kapcsolja be a biztonságos kommunikáció, azt javasoljuk, hogy az Azure Backup Server telepítése a VMware tanúsítvány hitelesítésszolgáltatói (CA) tanúsítvány. Ha nem feltétlenül szükséges a biztonságos kommunikációt, és inkább tiltsa le a HTTPS-követelmény, lásd: [letiltása biztonságos kommunikációs protokollt](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol). Azure Backup Server és a vCenter-kiszolgáló közötti biztonságos kapcsolat létrehozásához, importálja a megbízható tanúsítványt Azure biztonsági mentési kiszolgálón.

Általában egy böngészőt használ az Azure Backup Server számítógépen a vCenter-kiszolgáló a vSphere webes ügyfél keresztül csatlakozni. Az első használatakor az Azure Backup Server browser csatlakozni a vCenter-kiszolgáló, a kapcsolat nem biztonságos. A következő kép bemutatja a biztonságos kapcsolat.

![VMware-kiszolgáló nem biztonságos kapcsolatot – példa](./media/backup-azure-backup-server-vmware/unsecure-url.png)

Hárítsa el a problémát, és a biztonságos kapcsolat létrehozásához, töltse le a megbízható legfelső szintű Hitelesítésszolgáltatói tanúsítványokat.

1. Azure biztonsági mentési kiszolgálón a böngészőben adja meg az URL-címet a vSphere webes ügyfél. A vSphere webes ügyfél bejelentkezési oldal jelenik meg.

    ![a vSphere webügyfél](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

    Keresse meg az adatokat a rendszergazdák és fejlesztők alján a **letöltése megbízható legfelső szintű Hitelesítésszolgáltatói tanúsítványokat** hivatkozásra.

    ![Töltse le a megbízható legfelső szintű Hitelesítésszolgáltatói tanúsítványok csatolása](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  Ha nem látja a vSphere webes ügyfél bejelentkezési oldalra, ellenőrizze a böngésző proxybeállításait.

2. Kattintson a **letöltése megbízható legfelső szintű Hitelesítésszolgáltatói tanúsítványokat**.

    A vCenter Server letölti egy fájlt a helyi számítógépen. A fájl neve **letöltése**. Attól függően, hogy a böngésző kap egy üzenetet, amely megkérdezi, hogy a fájl megnyitása vagy mentése-e.

    ![üzenet letöltése a tanúsítványok lesznek letöltve](./media/backup-azure-backup-server-vmware/download-certs.png)

3. Mentse a fájlt az Azure Backup Server helyére. Amikor a mentse a fájlt, adja hozzá a .zip fájlnévkiterjesztést.

    A fájl nem egy .zip fájlt, amely a tanúsítványokkal kapcsolatos információkat tartalmaz. A .zip kiterjesztésű a kibontási eszközök is használhatja.

4. Kattintson a jobb gombbal **download.zip**, majd válassza ki **összes kibontása** tartalmának.

    A zip-fájlt bontja ki a tartalmát egy nevű mappát a **Tanúsítványos**. Két típusú fájlokat jelennek meg a tanúsítványok mappában. A főtanúsítvány-fájl kiterjesztése például.0 és ikonra.1 számozott sorozatát kezdődik.
    
    A tanúsítvány-visszavonási listát fájl kiterjesztése, amely egy sorozatban a hasonlóan .r0 vagy .r1 kezdődik. A CRL-fájlnak társítva egy tanúsítványt.

    ![Töltse le a fájlt helyileg kibontása ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. Az a **Tanúsítványos** mappába, kattintson a jobb gombbal a főtanúsítvány-fájlt, majd kattintson a **átnevezése**.

    ![Nevezze át a legfelső szintű tanúsítvány ](./media/backup-azure-backup-server-vmware/rename-cert.png)

    Módosítsa a legfelső szintű tanúsítvány bővítmény .crt. Még ha biztos abban, hogy a kiterjesztés módosítani szeretné, kattintson a kérdésnél **Igen** vagy **OK**. Ellenkező esetben a fájl tervezett függvény változtatható meg. A fájl ikon a legfelső szintű tanúsítvány jelképező ikont.

6. Kattintson a jobb gombbal a legfelső szintű tanúsítványt, és válassza ki a legördülő menüből **tanúsítvány telepítése**.

    A **Tanúsítványimportáló varázsló** párbeszédpanel jelenik meg.

7. Az a **Tanúsítványimportáló varázsló** párbeszédpanelen jelölje ki **helyi számítógép** a tanúsítványt, és kattintson a célként **tovább** a folytatáshoz.

    ![Tanúsítvány tárolási cél beállításai ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

    Ha a rendszer megkérdezi, ha szeretné engedélyezni a számítógép módosításait, kattintson a **Igen** vagy **OK**, a változásokat.

8. Az a **tanúsítványtároló** lapon jelölje be **minden tanúsítvány tárolása ebben a tárolóban**, és kattintson a **Tallózás** a tanúsítványtároló kiválasztása.

    ![Bizonyos tárolási helyet a tanúsítvány tárolása](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

    A **tanúsítványtároló kiválasztása** párbeszédpanel jelenik meg.

    ![Tanúsítványhierarchia tároló mappa](./media/backup-azure-backup-server-vmware/cert-store.png)

9. Válassza ki **megbízható legfelső szintű hitelesítésszolgáltatók** a tanúsítványokat, és kattintson a célmappaként **OK**.

    ![Tanúsítvány célmappájának](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

    A **megbízható legfelső szintű hitelesítésszolgáltatók** mappa rendszer meggyőződött róla, mint a tanúsítványtárolóból. Kattintson a **Tovább** gombra.

    ![Tanúsítvány tároló mappa](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. Az a **a Tanúsítványimportáló varázsló befejezése** lapon győződjön meg arról, hogy a tanúsítványt a kívánt mappa, és kattintson a **Befejezés**.

    ![Ellenőrizze a tanúsítvány megtalálható-e a megfelelő mappát](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

    Megjelenik egy párbeszédpanel, a sikeres Tanúsítványimportáló ellenőrzése.

11. Jelentkezzen be a vCenter-kiszolgálóhoz annak ellenőrzéséhez, hogy a kapcsolat biztonságos.

  Ha a tanúsítvány importálása nem sikeres, és nem tud biztonságos kapcsolatot, tekintse át a VMware vSphere dokumentációt a [kiszolgálótanúsítványok](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html).

  Ha biztonságos határai vannak a szervezeten belül, és nem szeretné bekapcsolni a HTTPS-protokollal, a következő eljárás használatával tiltsa le a biztonságos kommunikációhoz.

### <a name="disable-secure-communication-protocol"></a>Tiltsa le a biztonságos kommunikációs protokollja

Ha a szervezet nem igényli a HTTPS protokollt, a következő lépések használatával tiltsa le a HTTPS. Az alapértelmezett viselkedés letiltásához hozzon létre egy beállításkulcsot, amely figyelmen kívül hagyja az alapértelmezett viselkedés.

1. Másolja és illessze be a következő szöveget a .txt fájlt.

  ```
  Windows Registry Editor Version 5.00
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
  "IgnoreCertificateValidation"=dword:00000001
  ```

2. Mentse a fájlt az Azure Backup Server számítógéphez. A fájlnév DisableSecureAuthentication.reg használja.

3. Kattintson duplán a fájlra a beállításjegyzék-bejegyzés aktiválására.


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>A vcenter Server szerepkör és a felhasználói fiók létrehozása

A vcenter Server a szerepkör az előre meghatározott jogosultságokat. A vCenter-kiszolgáló rendszergazdája a szerepkörök hoz létre. Engedélyeket rendelhet a rendszergazda szerepkörrel rendelkező felhasználói fiókok párokat. A szükséges felhasználói hitelesítő adatok biztonsági mentése a vCenter-kiszolgáló számítógép létrehozásához szerepkör létrehozása adott jogosultságokkal, és majd a felhasználói fiók társítása a szerepkör.

Az Azure Backup Server felhasználónév és jelszó használatával hitelesítik magukat a vCenter-kiszolgálóhoz. Az Azure Backup Server ezeket a hitelesítő adatokat használ az összes biztonsági mentési műveletek hitelesítésként.

A vCenter-kiszolgáló szerepkör és a jogosultságok hozzáadása egy biztonsági mentési rendszergazda:

1. Jelentkezzen be a vCenter-kiszolgálóhoz, majd a vCenter-kiszolgáló **Navigator** panelen, kattintson a **felügyeleti**.

    ![A vCenter Server Navigator panelen rendszergazdai beállítás](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. A **felügyeleti** kiválasztása **szerepkörök**, majd a a **szerepkörök** panelen kattintson a Hozzáadás szerepkör ikon (a + szimbólumra).

    ![Szerepkör hozzáadása](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

    A **szerepkör létrehozása** párbeszédpanel jelenik meg.

    ![Szerepkör létrehozása](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. Az a **szerepkör létrehozása** párbeszédpanel a **szerepkörnév** adja meg a *BackupAdminRole*. A szerepkör neve lehet bármilyen, de a szerepkör célra felismerhető kell lennie.

4. Válassza ki a vCenter-verziójának megfelelő jogosultságokkal, és kattintson a **OK**. A következő táblázatból azonosíthatja a vCenter 6.0 és vCenter 5.5 a szükséges jogosultságokkal.

  Ha bejelöli a jogosultságokkal, kattintson a bontsa ki a szülő és gyermek jogosultságok megtekintése a szülő címke melletti ikonra. Jelölje be a VirtualMachine jogosultságokat, szüksége több szintet használja fel a szülő-gyermek hierarchia. Válassza ki a szülő jogosultság belül az összes alárendelt jogosultság nem kell.

  ![Szülő-gyermek jogosultság hierarchiát](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  Miután rákattintott **OK**, az új szerepkört a szerepkörök panelen listájában jelenik meg.

|Vcenter 6.0 jogosultságokkal| Vcenter 5.5 jogosultságokkal|
|--------------------------|---------------------------|
|Datastore.AllocateSpace   | Datastore.AllocateSpace|
|Global.ManageCustomFields | Global.ManageCustomerFields|
|Global.SetCustomFields    |   |
|Host.Local.CreateVM       | Network.Assign |
|Network.Assign            |  |
|Resource.AssignVMToPool   |  |
|VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   |
|VirtualMachine.Config.AdvanceConfig| VirtualMachine.Config.AdvancedConfig|
|VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking |
|VirtualMachine.Config.HostUSBDevice||
|VirtualMachine.Config.QueryUnownedFiles|    |
|VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement |
|VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff |
|VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create |
|VirtualMachine.Provisioning.DiskRandomAccess| |
|VirtualMachine.Provisioning.DiskRandomRead|VirtualMachine.Provisioning.DiskRandomRead |
|VirtualMachine.State.CreateSnapshot| VirtualMachine.State.CreateSnapshot|
|VirtualMachine.State.RemoveSnapshot|VirtualMachine.State.RemoveSnapshot |
</br>



## <a name="create-a-vcenter-server-user-account-and-permissions"></a>A vCenter Server felhasználói fiókjával és engedélyeivel létrehozása

Jogosultságokkal a szerepkör telepítése után, a felhasználói fiók létrehozása. A felhasználói fiók rendelkezik a nevet és jelszót, amely a hitelesítéshez használt hitelesítő adatait.

1. Egy felhasználói fiókot létrehozni a vCenter-kiszolgáló **Navigator** panelen, kattintson a **felhasználók és csoportok**.

    ![Felhasználók és csoportok beállítás](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    A **vCenter felhasználók és csoportok** panel jelenik meg.

    ![vCenter-felhasználók és csoportok panelen](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. Az a **vCenter felhasználók és csoportok** panelen, jelölje be a **felhasználók** lapot, és végül kattintson a Hozzáadás felhasználók elemre (a + szimbólumra).

    A **új felhasználó** párbeszédpanel jelenik meg.

3. Az a **új felhasználó** párbeszédpanel mezőbe, adja hozzá a felhasználó adatait, majd kattintson **OK**. Ebben az eljárásban a felhasználónév BackupAdmin.

    ![Új felhasználó párbeszédpanel](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

    Az új felhasználói fiók szerepel a listán.

4. A felhasználói fiókhoz társítja a szerepkör a a **Navigator** panelen, kattintson a **globális engedélyek**. Az a **globális engedélyek** panelen, jelölje be a **kezelése** lapot, és végül kattintson a Hozzáadás elemre (a + szimbólumra).

    ![Globális engedélyek panel](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

    A **globális engedélyek gyökér - engedély hozzáadása** párbeszédpanel jelenik meg.

5. Az a **globális engedély gyökér - engedély hozzáadása** párbeszédpanel, kattintson a **Hozzáadás** a felhasználó vagy csoport kiválasztása.

    ![Felhasználó vagy csoport kiválasztása](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

    A **felhasználók vagy csoportok kiválasztása** párbeszédpanel jelenik meg.

6. Az a **felhasználók vagy csoportok kiválasztása** párbeszédpanelen válassza ki **BackupAdmin** majd **hozzáadása**.

    A **felhasználók**, a *tartomány\felhasználónév* formátum a felhasználói fiók kerül használatra. Ha egy másik tartományban használni kívánt, válassza ki azt a **tartomány** listája.

    ![BackupAdmin felhasználó hozzáadása](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

    Kattintson a **OK** a kijelölt felhasználók hozzáadása a **hozzáadása engedély** párbeszédpanel megnyitásához.

7. Most, hogy a felhasználó állapította meg, rendelje hozzá a felhasználót a szerepkörhöz. A **hozzárendelt szerepkör**, válassza ki a legördülő listáról, **BackupAdminRole**, és kattintson a **OK**.

    ![Rendelje hozzá a felhasználói szerepkörhöz](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  Az a **kezelése** lapra a **globális engedélyek** panel, az új felhasználói fiók és a kapcsolódó szerepkör szerepelnek a listán.


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>VCenter Server-felhasználó hitelesítő adatai Azure Backup Server létrehozása

A VMware-kiszolgáló Azure Backup-kiszolgálóra veszi fel, telepíti az [1. frissítés az Azure Backup Server](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Azure Backup Server megnyitásához kattintson duplán az Azure Backup Server asztalon ikonra.

    ![Az Azure Backup Server ikonja](./media/backup-azure-backup-server-vmware/mabs-icon.png)

    Ha nem találja az ikonra az asztalon, Azure Backup Server megnyitása a telepített alkalmazások listájában. Az Azure Backup Server alkalmazásnév Microsoft Azure Backup szolgáltatás neve.

2. Az Azure Backup Server konzolon kattintson **felügyeleti**, kattintson a **az üzemi kiszolgálók**, és az eszközök menüszalagján kattintson **kezelése VMware**.

    ![Az Azure Backup Server konzol](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

    A **hitelesítő adatok kezelése** párbeszédpanel jelenik meg.

    ![Az Azure Backup Server hitelesítő adatok kezelése párbeszédpanel](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. Az a **hitelesítő adatok kezelése** párbeszédpanel, kattintson a **Hozzáadás** megnyitásához a **adja hozzá a hitelesítő adatok** párbeszédpanel.

4. A a **adja hozzá a hitelesítő adatok** párbeszédpanelen adja meg egy nevet és leírást az új hitelesítő adat. Adja meg a felhasználónevet és jelszót. A név *Contoso Vcenter hitelesítőadat* alapján határozza meg a hitelesítő adatokat a következő eljárásban. Használja ugyanazt a felhasználónevet és jelszót, amely a vCenter Server szolgál. Ha a vCenter-kiszolgáló és az Azure biztonsági mentés kiszolgáló nem ugyanabban a tartományban, **felhasználónév**, adja meg a tartományt.

    ![Az Azure Backup Server hozzáadása hitelesítő párbeszédpanel](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

    Kattintson a **Hozzáadás** kiszolgálóhoz történő hozzáadásakor az új hitelesítő adatok Azure biztonsági mentés. Az új hitelesítő adat megjelenik a listában a **hitelesítő adatok kezelése** párbeszédpanel megnyitásához.
    
    ![Az Azure Backup Server hitelesítő adatok kezelése párbeszédpanel](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Bezárja a **hitelesítő adatok kezelése** párbeszédpanel, kattintson a **X** jobb felső sarokban.


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>A vCenter-kiszolgáló hozzáadása az Azure Backup Server

Az üzemi kiszolgáló hozzáadása varázsló segítségével a vCenter-kiszolgáló hozzáadása az Azure Backup Server.

Az üzemi kiszolgáló hozzáadása varázsló megnyitásához kövesse az alábbi lépéseket:

1. Az Azure Backup Server konzolon kattintson **felügyeleti**, kattintson a **az üzemi kiszolgálók**, és kattintson a **Hozzáadás**.

    ![Nyissa meg az üzemi kiszolgáló hozzáadása varázsló](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

    A **üzemi kiszolgáló hozzáadása varázsló** párbeszédpanel jelenik meg.

    ![Az üzemi kiszolgáló hozzáadása varázsló](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. A a **válassza ki az üzemi kiszolgáló típusa** lapon, hogy melyik **VMware Server**, és kattintson a **következő**.

3. A **kiszolgáló neve vagy IP-címet**, adja meg a teljesen minősített tartománynevét (FQDN) vagy a VMware-kiszolgáló IP-címét. Ha az összes ESXi-kiszolgálókat kezeli-e az azonos vCenter, használhatja a vCenter neve.

    ![Adja meg a VMware server FQDN vagy IP-címe](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. A **SSL-Port**, adja meg azt a portot, a VMware-kiszolgálóval való kommunikációhoz használt. Az alapértelmezett port van, csak ha tudja, hogy szükség-e egy másik portra 443-as portot használja.

5. A **adja meg a hitelesítő adatok**, válassza ki a korábban létrehozott hitelesítő adatokat.

    ![Hitelesítő adatainak megadása](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Kattintson a **Hozzáadás** a VMware-kiszolgáló hozzáadása a listája **hozzá VMware-kiszolgálók**, és kattintson a **következő** Ugrás a varázsló következő lapjára.

    ![VMWare-kiszolgáló és a hitelesítő adatok hozzáadása](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Az a **összegzés** kattintson **Hozzáadás** a megadott VMware-kiszolgáló hozzáadása az Azure Backup Server.

    ![VMware-kiszolgáló hozzáadása az Azure Backup Server](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  A VMware server biztonsági másolat egy ügynök nélküli biztonsági mentést, és az új kiszolgáló azonnal hozzá van adva. A **Befejezés** oldal megjeleníti az eredményeket.

  ![Befejezés lapra](./media/backup-azure-backup-server-vmware/summary-screen.png)

  A vCenter Server több példánya Azure Backup Server hozzáadásához ismételje az előző lépést, ebben a szakaszban.

Miután hozzáadta a vCenter-kiszolgáló Azure Backup Server, a következő lépésre egy védelmi csoportot létrehozni. A védelmi csoport rövid és hosszú távú megőrzési különböző részleteit határozza meg, és amelyen határozza meg, és alkalmazza a biztonsági mentési házirendet. A biztonsági mentési házirend, amikor biztonsági mentések fordulnak elő, és milyen biztonsági mentése az ütemezését.


## <a name="configure-a-protection-group"></a>A védelmi csoportok beállítása

Ha még nem használta a System Center Data Protection Manager vagy az Azure Backup Server előtt, tekintse meg [lemezes biztonsági mentések tervezése](https://technet.microsoft.com/library/hh758026.aspx) készíti elő a Hardverkörnyezet. Után ellenőrizze, hogy rendelkezik-e megfelelő tárolására, vegye fel a VMware virtuális gépeket az új védelmi csoport létrehozása varázsló segítségével.

1. Kattintson az Azure Backup Server konzolon **védelmi**, és az eszközök menüszalagján kattintson **új** az új védelmi csoport létrehozása varázsló megnyitásához.

    ![Nyissa meg az új védelmi csoport létrehozása varázsló](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

    A **új védelmi csoport létrehozása** varázsló párbeszédpanel jelenik meg.

    ![Új védelmi csoport létrehozása varázsló párbeszédpanelje](./media/backup-azure-backup-server-vmware/protection-wizard.png)

    Kattintson a **következő** ahhoz, hogy értékről a **védelmi csoport típusának kiválasztása** lap.

2. A a **válassza ki a védelmi csoport típusának** lapon, hogy melyik **kiszolgálók** majd **tovább**. A **csoporttagok kiválasztása** lap jelenik meg.

3. Az a **csoporttagok kiválasztása** lap, a rendelkezésre álló tagok és a kijelölt tagok jelennek meg. Válassza ki, amelyet szeretne védeni, és kattintson a tagok **következő**.

    ![Csoporttagok kiválasztása](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

    Felhasználót, ha más mappákban vagy a virtuális gépeket tartalmazó mappa kiválasztásakor ezen mappák és a virtuális gépek is ki lesz választva. A mappák és a virtuális gépek által a szülőmappa neve mappa szintű védelmet. Egy mappa vagy a virtuális gép, törölje a jelölőnégyzet jelölését.

    Ha egy virtuális Gépet, vagy egy mappát, amely tartalmazza a virtuális gépek Azure-bA már védett, nem választhat ki ezt a virtuális Gépet újra. Ez azt jelenti, hogy a virtuális gépek védelme az Azure-ba, után már nem biztosítható újra, amely megakadályozza, hogy ismétlődő helyreállítási pontokat hoz létre egy virtuális gép. Ha azt szeretné, hogy melyik Azure Backup Server-példány már védett tag, mutasson a tag jelenítse meg a védelmet nyújtó kiszolgáló nevét.

4. Az a **adatvédelmi módszer kiválasztása** lapján adja meg a védelmi csoport nevét. Rövid távú védelem (lemez) és az online védelem van kiválasztva. Ha szeretné használni az online védelem (az Azure-bA), lemezre rövid távú védelmet kell használnia. Kattintson a **következő** folytatja a rövid távú védelem tartománynak.

    ![Adatvédelmi módszer kiválasztása](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. Az a **rövid távú célok megadása** lap, a **megőrzési időtartam**, adja meg az, hogy hány napig szeretné megőrizni a helyreállítási pontok *tárolt lemezre*. Ha az időt és a nap, ha a helyreállítási pontokat készít, kattintson a kívánt **módosítás**. A rövid távú helyreállítási pontok teljes biztonsági mentés. Nincsenek növekményes biztonsági mentést. Ha elégedett a rövid távú célok, kattintson **következő**.

    ![Rövid távú célok megadása](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. Az a **tekintse át a lemezfoglalás** lapon ellenőrizze, és szükség esetén módosítsa a szabad lemezterület a virtuális gépekhez. A megadott megőrzési időtartam alapuló a javasolt lemezfoglalásokat a **rövid távú célok megadása** lapon, a munkaterhelés típusától és (a 3. lépésben azonosított) a védett adatok mérete.  

  - **Adatok mérete:** a védelmi csoportban lévő adatok méretétől.
  - **Szabad lemezterület:** az ajánlott mennyiségű lemezterületet a védelmi csoporthoz. Ha azt szeretné, a beállítás módosításával, az egyes adatforrások növekedésének becslései összeg valamivel nagyobb helyet kell lefoglalni.
  - **Tartozó adatok közös elhelyezése:** bekapcsolja a közös elhelyezést, ha több adatforrások védelméhez leképezheti egyetlen replika- és helyreállítási adatpontok mennyisége. Közös elhelyezés nem minden munkaterhelésnél támogatott.
  - **Méretének automatikus növelése:** Ha bekapcsolja ezt a beállítást, ha a védelmi csoportban lévő adatok meghaladja a kezdeti foglalás, a System Center Data Protection Manager megpróbálja 25 százalékkal a lemez mérete nagyobb.
  - **Tárolókészlet részletei:** a tárolókészlethez, beleértve a teljes és szabad lemezterülettel állapotát jeleníti meg.

    ![Tekintse át a lemezkiosztást](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

    Ha elégedett a lemezterület-foglalás, kattintson **következő**.

7. Az a **replika-létrehozási módszer kiválasztása** csoportjában adja meg, hogyan szeretné létrehozni a kezdeti másolatot, vagy a replikát a védett adatokról az Azure Backup Server.

    Az alapértelmezett érték **automatikusan a hálózaton keresztül** és **most**. Használja az alapértelmezett, azt javasoljuk, hogy megadja a csúcsidőn kívüli időpontot. Válasszon **később** , és adja meg a napját és időpontját.

    Nagy mennyiségű adatok vagy kevésbé optimális hálózati körülmények esetén érdemes megfontolni adatreplikálás a kapcsolat nélküli cserélhető adathordozó használatával.

    Miután elvégezte a választott, kattintson a **következő**.

    ![A replika-létrehozási módszer kiválasztása](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. Az a **konzisztencia-ellenőrzési beállítások** lapra, adja meg, hogyan és mikor érdemes automatizálni a konzisztencia-ellenőrzést. Konzisztencia-ellenőrzéseket is futtathatja, ha a replikaadatok inkonzisztenssé válik, vagy adott ütemezés szerinti.

    Ha nem szeretné konfigurálni az automatikus konzisztencia-ellenőrzési, egy manuális ellenőrzést is futtathatja. A védelem található, az Azure Backup Server konzol, kattintson a jobb gombbal a védelmi csoportot, és válassza ki **konzisztencia-ellenőrzés**.

    Kattintson a **következő** áthelyezése a következő oldalra.

9. Az a **Online védelem adatainak megadása** lapon, válassza ki a védeni kívánt egy vagy több adatforrást. Válassza ki egyenként a tagokat, vagy kattintson a **kijelölése az összes** összes tag kiválasztását. A tagok kiválasztása után kattintson **következő**.

    ![Online védelem adatainak megadása](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. Az a **Online biztonsági mentés ütemezésének megadása** lapján adja meg a helyreállítási pontok generálása a lemezes biztonsági mentés ütemezését. A helyreállítási pont jön létre, miután az Azure Recovery Services-tároló kerül át. Ha elégedett az online biztonsági mentési ütemezést, kattintson **következő**.

    ![Adja meg az online biztonsági mentés ütemezése](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. Az a **Online adatmegőrzési szabály megadása** lapon, az azt jelzi, hogy mennyi ideig szeretné megőrizni a biztonsági mentési adatok az Azure-ban. Miután a házirend be van állítva, kattintson **következő**.

    ![Online megőrzési szabály megadása](./media/backup-azure-backup-server-vmware/retention-policy.png)

    Nincs nincs határideje, mennyi ideig tárolhatja az adatokat az Azure-ban. Ha a helyreállítási pont adatait az Azure-ban tárolja, a csak határértéke, hogy rendelkezik-e nem védett példányonként legfeljebb 9999 helyreállítási pontokat. Ebben a példában a védett példány a VMware server.

12. Az a **összegzés** lapon tekintse át a részletes adatait a védelmi csoport tagjainak és a beállításokat, és kattintson a **csoport létrehozása**.

    ![Védelmi csoport tagja és beállítás összegzése](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>Következő lépések
Azure Backup Server használatával VMware munkaterhelések védelmét, ha szeretné Azure Backup Server védelme is egy [Microsoft Exchange server](./backup-azure-exchange-mabs.md), egy [Microsoft SharePoint-farm](./backup-azure-backup-sharepoint-mabs.md), vagy egy [SQL Server-adatbázis](./backup-azure-sql-mabs.md).

Az ügynök regisztrálása problémáit, a védelmi csoport konfigurálásához, vagy biztonsági mentése feladat, kapcsolatban információk [hibaelhárítása Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
