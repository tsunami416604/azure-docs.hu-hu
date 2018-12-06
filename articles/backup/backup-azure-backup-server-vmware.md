---
title: Az Azure Backup Server VMware-kiszolgálók biztonsági mentése
description: Az Azure Backup Server használatával biztonsági másolatot egy VMware vCenter vagy ESXi-kiszolgálók Azure-ban vagy a lemez. Ez a cikk több lépés = szerint – részletes útmutató biztonsági mentése (vagy védelme) a VMware számítási feladatokat.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/24/2017
ms.author: adigan
ms.openlocfilehash: e39e5d12610164ca4a1372830cf25ea203fd382c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968834"
---
# <a name="back-up-a-vmware-server-to-azure"></a>VMware-kiszolgáló biztonsági mentése az Azure-bA

Ez a cikk bemutatja, hogyan konfigurálhatja a VMware server számítási feladatok védelme érdekében az Azure Backup Server. Ez a cikk feltételezi, hogy már rendelkezik Azure Backup Server telepítése. Ha nem rendelkezik Azure Backup Server telepítése, lásd: [használata az Azure Backup Server számítási feladatok biztonsági mentésének előkészítése](backup-azure-microsoft-azure-backup.md).

Az Azure Backup Server biztonsági másolatot, vagy védelme, a VMware vCenter Server-verzió 6.5-ös, 6.0-s és a egy 5.5-ös.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>A vCenter-kiszolgáló biztonságos kapcsolat létrehozása

Alapértelmezés szerint az Azure Backup Server mindegyik vCenter-kiszolgáló egy HTTPS-csatornán keresztül kommunikál. Kapcsolja be a biztonságos kommunikációt, azt javasoljuk, hogy az Azure Backup Server telepítése a VMware hitelesítésszolgáltató (CA) tanúsítvány. Ha nem biztonságos kommunikációt is igényelnek, és tiltsa le a HTTPS-követelmény kapni, tekintse meg [letiltása biztonságos kommunikációs protokollt](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol). Az Azure Backup Server és a vCenter-kiszolgáló közötti biztonságos kapcsolat létrehozásához, importálja a megbízható tanúsítvány az Azure Backup Server.

Általában egy böngészőt használ az Azure Backup Server gépre csatlakozni a vCenter-kiszolgáló a vSphere webes ügyfélben keresztül. Az Azure Backup Server böngésző használatával csatlakozni a vCenter-kiszolgáló, először a kapcsolat nem biztonságos. Az alábbi képen a nem biztonságos kapcsolat.

![A VMware-kiszolgáló nem biztonságos kapcsolat – példa](./media/backup-azure-backup-server-vmware/unsecure-url.png)

A probléma megoldásához, és hozzon létre egy biztonságos kapcsolatot, töltse le a megbízható legfelső szintű Hitelesítésszolgáltatói tanúsítványok.

1. Az Azure Backup Server a böngészőben adja meg az URL-címet a vSphere webes ügyfélben. A vSphere webes ügyfél bejelentkezési oldal jelenik meg.

    ![vSphere webes ügyfélben](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

    A rendszergazdák és fejlesztők adatai alján keresse meg a **letöltése megbízható legfelső szintű Hitelesítésszolgáltatói tanúsítványok** hivatkozásra.

    ![Töltse le a megbízható legfelső szintű Hitelesítésszolgáltatói tanúsítványok mutató hivatkozás](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  Ha a vSphere webes ügyfél bejelentkezési oldal nem jelenik meg, ellenőrizze a böngésző proxy beállításait.

2. Kattintson a **letöltése megbízható legfelső szintű Hitelesítésszolgáltatói tanúsítványok**.

    A vCenter-kiszolgáló egy fájlt a helyi számítógép letölti. A fájlnév nevű **letöltése**. A böngésző típusától függően kap egy üzenetet, amely megkérdezi, hogy e megnyitni vagy menteni a fájlt.

    ![Töltse le az üzenetet, amikor a rendszer letölti a tanúsítványokat](./media/backup-azure-backup-server-vmware/download-certs.png)

3. Mentse a fájlt egy olyan helyre, az Azure Backup Server. Amikor menti a fájlt, adja hozzá a .zip fájlnévkiterjesztést.

    A fájl nem egy .zip fájlt, amely tartalmazza az információ a tanúsítványokról. A .zip kiterjesztésű a kibontási eszközt is használhat.

4. Kattintson a jobb gombbal **download.zip**, majd válassza ki **összes kibontása** a tartalma kinyeréséhez.

    A .zip fájl kibontása a nevű tartalmát **tanúsítványok**. Két típusú fájlok jelennek meg a tanúsítványok mappában. A legfelső szintű tanúsítvány fájl kiterjesztése, amely egy számokból álló sorozat például.0 és ikonra.1 kezdődik.

    A CRL-fájl kiterjesztése, amely egy feladatütemezési .r0 vagy .r1 hasonlóan kezdődik. A tanúsítvány CRL-fájlnak kapcsolódik.

    ![Töltse le a fájlt helyileg kibontása ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. Az a **tanúsítványok** mappába, kattintson a jobb gombbal a legfelső szintű tanúsítvány fájlját, majd kattintson a **átnevezése**.

    ![Nevezze át a legfelső szintű tanúsítvány ](./media/backup-azure-backup-server-vmware/rename-cert.png)

    Módosítsa a legfelső szintű tanúsítvány bővítmény .crt. Amikor a rendszer arra kéri, ha biztos abban, hogy meg szeretné változtatni a bővítményt, kattintson a **Igen** vagy **OK**. Ellenkező esetben módosíthatja a fájl kívánt funkció. A legfelső szintű tanúsítvány jelölő ikon a fájlt módosítások ikonra.

6. Kattintson a jobb gombbal a legfelső szintű tanúsítvány és a legördülő menüből válassza ki a **tanúsítvány telepítése**.

    A **Tanúsítványimportáló varázsló** párbeszédpanel jelenik meg.

7. Az a **Tanúsítványimportáló varázsló** párbeszédpanelen jelölje ki **helyi gépen** kell a tanúsítványt, és kattintson a célként **tovább** folytatásához.

    ![Igazolás tárolási célhely beállításai ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

    Ha az kéri, ha azt szeretné, hogy a számítógépen végzett módosítások, kattintson a **Igen** vagy **OK**, az összes módosítást.

8. Az a **tanúsítvány Store** lapon jelölje be **minden tanúsítvány tárolása ebben a tárolóban**, és kattintson a **Tallózás** a tanúsítványtároló kiválasztása.

    ![Tanúsítvány tárolása ebben a egy adott tárolási helyét](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

    A **válassza tanúsítvány Store** párbeszédpanel jelenik meg.

    ![Tároló mappa tanúsítványhierarchiához](./media/backup-azure-backup-server-vmware/cert-store.png)

9. Válassza ki **megbízható legfelső szintű hitelesítésszolgáltatók** a tanúsítványokat, és kattintson a célmappaként **OK**.

    ![Tanúsítvány célmappa](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

    A **megbízható legfelső szintű hitelesítésszolgáltatók** mappa megerősítik, mint a tanúsítványtárolóban. Kattintson a **Tovább** gombra.

    ![Tanúsítvány tároló mappa](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. Az a **tanúsítvány importálása varázsló befejezése** lapon ellenőrizze, hogy a tanúsítvány szerepel-e a kívánt mappába, és kattintson a **Befejezés**.

    ![Ellenőrizze a tanúsítvány nem a megfelelő mappát](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

    Megjelenik egy párbeszédpanel, a sikeres importálás ellenőrzése.

11. Jelentkezzen be a vCenter-kiszolgálóhoz, győződjön meg arról, hogy a kapcsolat nem biztonságos.

  Ha a tanúsítvány importálása nem sikerült, és nem létesíthető biztonságos kapcsolat, tekintse át a VMware vSphere dokumentációt a [kiszolgálótanúsítványok](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html).

  Ha biztonságos határai a szervezeten belül, és nem szeretné bekapcsolni az a HTTPS protokollt, a következő eljárás használatával tiltsa le a biztonságos kommunikációhoz.

### <a name="disable-secure-communication-protocol"></a>Biztonságos kommunikáció protokoll letiltása

A szervezet nem igényel a HTTPS protokollt, ha a következő lépések segítségével tilthatja le a HTTPS. Az alapértelmezett viselkedés letiltásához hozzon létre egy beállításkulcsot, amely figyelmen kívül hagyja az alapértelmezett viselkedést.

1. Másolja és illessze be a következő szöveg egy .txt fájlt.

  ```
  Windows Registry Editor Version 5.00
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
  "IgnoreCertificateValidation"=dword:00000001
  ```

2. Mentse a fájlt az Azure Backup Server számítógéphez. A fájlnév DisableSecureAuthentication.reg használja.

3. Kattintson duplán a fájlra, a beállításjegyzék-bejegyzés aktiválásához.


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>A vcenter Server-szerepkör és a felhasználói fiók létrehozása

A vcenter Server a szerepkör az előre meghatározott jogosultságokat. A vCenter-kiszolgáló rendszergazdája a szerepkörök hoz létre. Az engedélyek hozzárendelése a rendszergazda párokat. a szerepkörrel rendelkező felhasználói fiókok. A szükséges felhasználói hitelesítő adatok biztonsági mentése a vCenter-kiszolgáló számítógép létesíteni, hozzon létre egy szerepkört az adott jogosultságok, és majd a felhasználói fiókot társíthat a szerepkör.

Az Azure Backup Server a felhasználónévvel és jelszóval való hitelesítésre a vCenter-kiszolgáló használ. Az Azure Backup Server ezeket a hitelesítő adatokat használja a biztonsági mentési művelet a hitelesítést.

A vCenter-kiszolgáló szerepkör és a jogosultságok hozzáadása egy biztonsági mentési rendszergazda:

1. Jelentkezzen be a vcenter-kiszolgálóhoz, majd a vCenter-kiszolgáló a **kezelő** panelen, kattintson a **felügyeleti**.

    ![A vCenter Server kezelő panelen rendszergazdai beállítás](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. A **felügyeleti** kiválasztása **szerepkörök**, majd a **szerepkörök** panelen kattintson a Hozzáadás szerepkör ikonra (a + szimbólumra).

    ![Szerepkör hozzáadása](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

    A **szerepkör létrehozása** párbeszédpanel jelenik meg.

    ![Szerepkör létrehozása](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. Az a **szerepkör létrehozása** párbeszédpanel a **szerepkörnév** mezőbe írja be *BackupAdminRole*. A szerepkör neve lehet bármire átnevezhető, de felismerhető a szerepkör célra kell lennie.

4. Válassza ki a vCenter verziójának megfelelő jogosultságokkal, és kattintson a **OK**. A következő táblázat tartalmazza a szükséges jogosultságokkal a vCenter 6.0-s és a egy 5.5-ös vCenter.

  Amikor kiválasztja a jogosultságokat, bontsa ki a szülő és gyermek jogosultságok megtekintése a szülő címke melletti ikonra kattintva. Válassza ki a VirtualMachine jogosultságokat, a szülő-gyermek hierarchia több szinten fel kell. Válassza ki a szülő jogosultság belül az összes gyermek jogosultság nem kell.

  ![Szülő-gyermek jogosultság hierarchiát](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  Miután rákattintott **OK**, az új szerepkör megjelenik a listán, a szerepkörök panelen.

|Vcenter 6.5-ös és a 6.0-s jogosultságokkal| 5.5-ös vcenter-jogosultságok|
|----------------------------------|---------------------------|
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



## <a name="create-a-vcenter-server-user-account-and-permissions"></a>Hozzon létre egy vCenter-kiszolgáló felhasználói fiókot és engedélyek

Miután jogosultságokkal a szerepkör be van állítva, hozzon létre egy felhasználói fiókot. A felhasználói fiók rendelkezik a nevet és jelszót, amely a hitelesítéshez használt hitelesítő adatokat biztosít.

1. Egy felhasználói fiók létrehozásához a vCenter-kiszolgáló **kezelő** panelen, kattintson a **felhasználók és csoportok**.

    ![Felhasználók és csoportok lehetőség](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    A **vCenter-felhasználók és csoportok** panel jelenik meg.

    ![vCenter-felhasználók és csoportok panel](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. Az a **vCenter-felhasználók és csoportok** panelen kattintson a **felhasználók** fülre, majd az add-felhasználók ikon (a + szimbólumra).

    A **új felhasználó** párbeszédpanel jelenik meg.

3. Az a **új felhasználó** párbeszédpanel mezőben, adja meg a felhasználó adatait, majd kattintson **OK**. Ebben az eljárásban a felhasználónév BackupAdmin.

    ![Új felhasználó párbeszédpanel](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

    Az új felhasználói fiók megjelenik a listában.

4. A szerepkör a felhasználói fiók társítása a **kezelő** panelen, kattintson a **globális engedélyek**. Az a **globális engedélyek** panelen kattintson a **kezelés** lapra, és végül kattintson a Hozzáadás elemre (a + szimbólumra).

    ![Globális engedélyek panel](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

    A **globális engedélyek gyökér - engedély hozzáadása** párbeszédpanel jelenik meg.

5. Az a **globális engedélyt gyökér - engedély hozzáadása** párbeszédpanelen kattintson a **Hozzáadás** a felhasználó vagy csoport kiválasztása.

    ![Felhasználó vagy csoport kiválasztása](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

    A **felhasználók vagy csoportok kiválasztása** párbeszédpanel jelenik meg.

6. Az a **felhasználók vagy csoportok kiválasztása** párbeszédpanelen válassza ki **BackupAdmin** majd **Hozzáadás**.

    A **felhasználók**, a *tartomány\felhasználónév* formátum a felhasználói fiók használatos. Használjon egy másik tartományban szeretne, válassza azt a **tartomány** listája.

    ![BackupAdmin felhasználó hozzáadása](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

    Kattintson a **OK** a kiválasztott felhasználók hozzáadása a **engedély hozzáadása** párbeszédpanel bezárásához.

7. Most, hogy a felhasználó azonosítása, rendelje hozzá a felhasználót a szerepkörhöz. A **hozzárendelt szerepkör**, válassza a legördülő listából **BackupAdminRole**, és kattintson a **OK**.

    ![Felhasználó szerepkör hozzárendelése](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  Az a **kezelés** lapján a **globális engedélyek** panelen, az új felhasználói fiók és a kapcsolódó szerepkör szerepelnek a listán.


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>Az Azure Backup Server a vCenter kiszolgáló hitelesítő adatainak létrehozása

Telepítse a VMware-kiszolgáló hozzáadása az Azure Backup Serverhez, előtt [Azure Backup Server 1. frissítés](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Az Azure Backup Server megnyitásához kattintson duplán az ikonra az Azure Backup Server asztalon.

    ![Az Azure Backup Server ikonja](./media/backup-azure-backup-server-vmware/mabs-icon.png)

    Ha nem találja az ikonra az asztalon, nyissa meg az Azure Backup Server telepített alkalmazások listájában. Az Azure Backup Server alkalmazásnév neve Microsoft Azure Backup szolgáltatásban.

2. Az Azure Backup Server konzolon kattintson a **felügyeleti**, kattintson a **éles kiszolgálók**, és az eszközök menüszalagján kattintson a **kezelése VMware**.

    ![Az Azure Backup Server konzol](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

    A **hitelesítő adatok kezelése** párbeszédpanel jelenik meg.

    ![Az Azure Backup Server hitelesítő adatok kezelése párbeszédpanel](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. Az a **hitelesítő adatok kezelése** párbeszédpanelen kattintson a **Hozzáadás** megnyitásához a **hitelesítő adatok hozzáadása** párbeszédpanel bezárásához.

4. Az a **hitelesítő adatok hozzáadása** párbeszédpanelen adja meg egy nevet és az új hitelesítő adat leírását. Majd adja meg a felhasználónevet és jelszót. A név *Contoso Vcenter hitelesítő adat* a hitelesítő adatokat a következő eljárásban azonosítására szolgál. A használt felhasználónévvel és jelszóval, hogy a vCenter-kiszolgálóhoz használt használja. Ha a vCenter-kiszolgáló és az Azure Backup Server nem ugyanabban a tartományban, a **felhasználónév**, adja meg a tartományt.

    ![Az Azure Backup Server hitelesítő adatok hozzáadása párbeszédpanel](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

    Kattintson a **Hozzáadás** új hitelesítő adatok hozzáadása az Azure Backup Server. Az új hitelesítő adat megjelenik a listában a **hitelesítő adatok kezelése** párbeszédpanel bezárásához.

    ![Az Azure Backup Server hitelesítő adatok kezelése párbeszédpanel](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Gombra kattintva zárja be a **hitelesítő adatok kezelése** párbeszédpanelen kattintson a **X** jobb felső sarokban.


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>A vCenter-kiszolgáló hozzáadása az Azure Backup Serverhez

Üzemi kiszolgáló felvétele varázsló segítségével a vCenter-kiszolgáló hozzáadása az Azure Backup Serverhez.

Üzemi kiszolgáló felvétele varázsló megnyitásához kövesse az alábbi lépéseket:

1. Az Azure Backup Server konzolon kattintson a **felügyeleti**, kattintson a **éles kiszolgálók**, és kattintson a **Hozzáadás**.

    ![Nyissa meg az üzemi kiszolgáló felvétele varázsló](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

    A **üzemi kiszolgáló felvétele varázsló** párbeszédpanel jelenik meg.

    ![Üzemi kiszolgáló felvétele varázsló](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. Az a **válassza ki az üzemi kiszolgáló típusának** lapra, jelölje be **VMware-kiszolgálók**, és kattintson a **tovább**.

3. A **kiszolgáló neve vagy IP-címének**, adja meg a teljesen minősített tartománynevét (FQDN) vagy a VMware-kiszolgáló (a gazdagép ESXi-kiszolgáló) IP-címét. Minden ESXi-kiszolgáló eltérő vCenter által felügyelt, ha a vCenter-nevet használhat.

    ![Adja meg a VMware server teljes Tartományneve vagy IP-cím](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. A **SSL-Port**, adja meg a VMware-kiszolgálóval való kommunikációhoz használt port. Az alapértelmezett port van, ha tudja, hogy szükség-e egy másik portot a 443-as portot használja.

5. A **adja meg a hitelesítő adatok**, válassza ki a korábban létrehozott hitelesítő adatokat.

    ![Adja meg a hitelesítő adatok](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Kattintson a **Hozzáadás** a VMware-kiszolgáló hozzáadása a listához, **hozzá VMware-kiszolgálók**, és kattintson a **tovább** Ugrás a varázsló következő lapjára.

    ![VMWare-kiszolgáló és a hitelesítő adat hozzáadása](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Az a **összefoglalás** kattintson **Hozzáadás** a megadott VMware-kiszolgáló hozzáadása az Azure Backup Server.

    ![VMware-kiszolgáló hozzáadása az Azure Backup Serverhez](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  A VMware-kiszolgáló biztonsági mentése egy ügynök nélküli biztonsági mentéshez, és azonnal az új kiszolgáló hozzáadása. A **Befejezés** oldal megjeleníti az eredményeket.

  ![A Befejezés lapon](./media/backup-azure-backup-server-vmware/summary-screen.png)

  Az Azure Backup Serverhez vCenter-kiszolgáló több példányának hozzáadásához ismételje meg az ebben a szakaszban található korábbi lépéseket.

Miután hozzáadta a vCenter-kiszolgáló Azure Backup Server, a következő lépés az hozzon létre egy védelmi csoportot. A védelmi csoport rövid vagy hosszú távú megőrzési különböző részleteit adja meg, és határozza meg, és ahol a alkalmazni a biztonsági mentési szabályzat. A biztonsági mentési szabályzat a ütemezését, mikor készüljön biztonsági másolat, és milyen biztonsági mentését.


## <a name="configure-a-protection-group"></a>Védelmi csoportok beállítása

Ha még nem használta a System Center Data Protection Manager vagy az Azure Backup Server előtt, tekintse meg [lemezes biztonsági mentések tervezése](https://technet.microsoft.com/library/hh758026.aspx) előkészítése a Hardverkörnyezet. Után ellenőrizheti, hogy a megfelelő tárolási, a VMware virtuális gépek hozzáadása az új védelmi csoport létrehozása varázsló használatával.

1. Az Azure Backup Server konzolon kattintson a **védelmi**, és az eszközök menüszalagján kattintson **új** az új védelmi csoport létrehozása varázsló megnyitásához.

    ![Nyissa meg az új védelmi csoport létrehozása varázsló](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

    A **új védelmi csoport létrehozása** varázsló párbeszédpanel jelenik meg.

    ![Új védelmi csoport létrehozása varázsló párbeszédpanelje](./media/backup-azure-backup-server-vmware/protection-wizard.png)

    Kattintson a **tovább** azt mutatja be, hogy a **védelmi csoport típusának kiválasztása** lapot.

2. Az a **válassza ki a védelmi csoport típusának** lapra, jelölje be **kiszolgálók** majd **tovább**. A **jelölje ki a csoporttagokat** lap jelenik meg.

3. Az a **jelölje ki a csoporttagokat** oldal, a választható tagok és a kijelölt tagok jelennek meg. Válassza ki a tagok védelmét, és kattintson a kívánt **tovább**.

    ![Csoporttagok kiválasztása](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

    Egy tag, ha más mappákat vagy a virtuális gépeket tartalmazó mappa kiválasztásakor ezeket a mappákat és virtuális gépek is ki lesz választva. A mappák és a virtuális gépek a szülőmappában mappa védelemmel nevezzük. Egy mappa vagy a virtuális Gépet, törölje a jelölőnégyzet jelölését.

    Ha egy virtuális Gépet, vagy egy mappát, amely tartalmazza a virtuális gép már védett Azure-ba, nem választhat ki a virtuális Gépeket újra. Azt jelenti Miután a virtuális gépek védelme az Azure-ba, már nem biztosítható, amely megakadályozza, hogy ismétlődő helyreállítási pontokat hoz létre egy virtuális gép. Ha azt szeretné, hogy melyik Azure Backup Server-példány már védi a tag, mutasson a védelmet nyújtó kiszolgáló nevének a tag.

4. Az a **adatvédelmi módszer kiválasztása** lap, adja meg a védelmi csoport nevét. Rövid távú védelem (lemezre) és az online védelem ki van jelölve. Ha szeretné használni az online védelem (az Azure-bA), lemezre rövid távú védelmet kell használnia. Kattintson a **tovább** lépjen a rövid távú védelem-tartományt.

    ![Adatvédelmi módszer kiválasztása](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. Az a **rövid távú célok megadása** lapon a **megőrzési**, adja meg a napok, hogy meg szeretné őrizni a helyreállítási pontok száma *tárolt lemezre*. Ha az idő módosítása és a nap, amikor a helyreállítási pontok megnyílik, kattintson a kívánt **módosítás**. A rövid távú helyreállítási pontok teljes biztonsági mentések. Azok nem növekményes biztonsági mentéseket. Ha elégedett a rövid távú célok, kattintson az **tovább**.

    ![Rövid távú célok megadása](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. Az a **tekintse át a lemezfoglalás** lapon ellenőrizze, és ha szükséges, módosíthatja a szabad lemezterületet a virtuális gépek számára. A megadott megőrzési tartomány alapján a javasolt lemezfoglalásokat a **rövid távú célok megadása** lapon, a munkaterhelés típusától és (3. lépésben azonosított) a védett adatok mérete.  

  - **Adatok mérete:** a védelmi csoportban található adatok mérete.
  - **Lemezterület:** az ajánlott mennyiségű lemezterületet a védelmi csoport számára. Ha azt szeretné, a beállítás módosításához, meg kell helyet lefoglalnia, valamivel nagyobb, mint az összeg, amely minden adatforrás növekszik becslései szerint.
  - **Tartozó adatok közös elhelyezése:** bekapcsolja a közös elhelyezést, ha több forrásokat a védelmi leképezhet egyetlen replika- és helyreállítási adatpontok mennyisége. A közös elhelyezés minden számítási feladatok esetében nem támogatott.
  - **Automatikus növekedés:** Ha bekapcsolja ezt a beállítást, ha a védelmi csoportban található adatok megnövelni a kezdeti lefoglalt, System Center Data Protection Manager próbálja meg növelni a 25 %-kal.
  - **Tárolókészlet részletei:** a tárolókészlethez, beleértve a teljes és szabad lemezterülettel állapotát jeleníti meg.

    ![Tekintse át a lemezkiosztást](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

    Ha elégedett a lemezterület-foglalás, kattintson az **tovább**.

7. Az a **replika-létrehozási módszer kiválasztása** csoportjában adja meg, hogyan szeretné létrehozni a kezdeti másolatot, vagy a replikát a védett adatokról az Azure Backup Server.

    Az alapértelmezett érték **automatikusan a hálózaton keresztül** és **most**. Ha használja az alapértelmezett, azt javasoljuk, hogy megad egy csúcsidőn kívüli időpont. Válasszon **később** és a egy napját és időpontját adja meg.

    Nagy mennyiségű adat- vagy kevésbé optimális hálózati körülmények fontolja meg a kapcsolat nélküli az adatok replikálását a cserélhető adathordozó használatával.

    Miután elvégezte a beállításokat, kattintson a **tovább**.

    ![Replika-létrehozási módszer kiválasztása](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. Az a **konzisztencia-ellenőrzési beállítások** lap, adja meg, hogyan és mikor érdemes automatizálni a konzisztencia-ellenőrzéseket. Konzisztencia-ellenőrzéseket is futtathat, amikor a replikaadatok inkonzisztenssé válik, illetve egy beállított ütemezés szerint.

    Ha nem szeretne automatikus konzisztencia-ellenőrzést beállítani, egy manuális ellenőrzést is futtathatja. Az Azure Backup Server konzol a védelem területen kattintson a jobb gombbal a védelmi csoportot, majd **konzisztencia-ellenőrzés végrehajtása**.

    Kattintson a **tovább** áthelyezése a következő lapra.

9. Az a **Online védelem adatainak megadása** lapon, válassza ki a védeni kívánt egy vagy több adatforrást. Válassza ki egyenként a tagokat, vagy kattintson **válassza ki az összes** minden tag kiválasztása. Miután kiválasztotta a tagok, kattintson a **tovább**.

    ![Online védelem adatainak megadása](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. Az a **Online biztonsági mentés ütemezésének megadása** lap, adja meg a helyreállítási pontok generálhatnak a lemezes biztonsági mentés ütemezését. Miután a helyreállítási pont jön létre, a Recovery Services-tárolót az Azure-ban való átvitelig. Ha elégedett az online biztonsági mentés ütemezése, kattintson az **tovább**.

    ![Online biztonsági mentés ütemezésének megadása](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. Az a **Online adatmegőrzési szabály megadása** lap, adja meg, mennyi ideig szeretné megőrizni a biztonsági mentési adatok az Azure-ban. A szabály meghatározása után kattintson a **tovább**.

    ![Online megőrzési szabály megadása](./media/backup-azure-backup-server-vmware/retention-policy.png)

    Nincs nincs határideje, mennyi ideig tárolhatja az adatokat az Azure-ban. Ha a helyreállítási pont adatait az Azure-ban, a csak határértéke, hogy nem rendelkezik a védett példányonkénti több mint 9999 helyreállítási pontot. Ebben a példában a védett példány a VMware-kiszolgáló.

12. Az a **összefoglalás** lapon ellenőrizze a részleteket a védelmi csoport tagjainak és a beállításokat, és kattintson a **csoport létrehozása**.

    ![A védelmi csoport tagja, és a beállítás összegzése](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>További lépések
Ha az Azure Backup Server használatával a VMware számítási feladatainak védelmét, érdekelheti az Azure Backup Server használatával védelme érdekében a [Microsoft Exchange server](./backup-azure-exchange-mabs.md), amely egy [a Microsoft SharePoint-farm](./backup-azure-backup-sharepoint-mabs.md), vagy egy [SQL Server-adatbázis](./backup-azure-sql-mabs.md).

Az ügynök regisztrálása problémáit, a védelmi csoport konfigurálásához, vagy a biztonsági másolat feladatok, témakörben olvashat [hibaelhárítása az Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
