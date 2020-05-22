---
title: SAP HANA-adatbázis biztonsági mentése az Azure-ba Azure Backup
description: Ebből a cikkből megtudhatja, hogyan készíthet biztonsági mentést egy SAP HANA-adatbázisról az Azure-beli virtuális gépekre a Azure Backup szolgáltatással.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 4183c1eca6b1149c5c61ed77c0ca1101c86f8f4f
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745415"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>SAP HANA-adatbázisok biztonsági mentése Azure-beli virtuális gépeken

SAP HANA adatbázisok olyan kritikus fontosságú számítási feladatok, amelyek alacsony helyreállítási célt (RPO) és hosszú távú adatmegőrzést igényelnek. Az Azure Virtual Machines (VM) rendszeren futó SAP HANA adatbázisok biztonsági mentését [Azure Backup](backup-overview.md)használatával végezheti el.

Ez a cikk az Azure-beli virtuális gépeken futó SAP HANA adatbázisok biztonsági mentését mutatja be egy Azure Backup Recovery Services-tárolón.

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:
> [!div class="checklist"]
>
> * Tároló létrehozása és konfigurálása
> * Adatbázisok felderítése
> * Biztonsági mentések konfigurálása
> * Igény szerinti biztonsági mentési feladatok futtatása

>[!NOTE]
>Az Azure-beli virtuális gépeken futó **SQL Server-kiszolgáló és az Azure** -beli virtuális gépekhez készült SAP HANA Soft delete már előzetes verzióban érhető el.<br>
>Az előzetes verzióra való feliratkozáshoz írjon nekünk a következőt:AskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Előfeltételek

Tekintse át az [előfeltételeket](tutorial-backup-sap-hana-db.md#prerequisites) , valamint azt, hogy az [előzetes regisztrációs parancsfájl mit tartalmaz](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) az adatbázis biztonsági mentéshez való beállításához.

### <a name="establish-network-connectivity"></a>Hálózati kapcsolat létesítése

Az Azure-beli virtuális gépeken futó SAP HANA adatbázisoknak minden művelethez kapcsolódniuk kell a Azure Backup szolgáltatáshoz, az Azure Storage-hoz és a Azure Active Directoryhoz. Ezt privát végpontok használatával vagy a szükséges nyilvános IP-címekhez vagy teljes tartománynevek elérésének engedélyezésével lehet elérni. A szükséges Azure-szolgáltatásokhoz való megfelelő kapcsolódás nem teszi lehetővé az adatbázis-felderítést, a biztonsági mentés konfigurálását, a biztonsági másolatok készítését és az adatok visszaállítását.

A következő táblázat a kapcsolatok létrehozásához használható különböző alternatívákat sorolja fel:

| **Beállítás**                        | **Előnyök**                                               | **Hátrányok**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Privát végpontok                 | Biztonsági másolatok engedélyezése privát IP-címeken a virtuális hálózaton belül  <br><br>   Részletes vezérlés biztosítása a hálózat és a tároló oldalán | Standard magánhálózati végponti [költségek](https://azure.microsoft.com/pricing/details/private-link/) |
| NSG szolgáltatás címkéi                  | A tartomány módosításainak könnyebb kezelése automatikusan történik   <br><br>   Nincs további költség | Csak NSG használható  <br><br>    Hozzáférést biztosít a teljes szolgáltatáshoz |
| Azure Firewall FQDN-Címkék          | Könnyebb kezelhetőség, mert a szükséges teljes tartománynevek automatikusan felügyelhetők | Csak Azure Firewall használható                         |
| Hozzáférés engedélyezése a szolgáltatás teljes tartománynevéhez/IP-címeihez | Nincs további költség   <br><br>  Együttműködik az összes hálózati biztonsági berendezéssel és tűzfallal | Szükség lehet az IP-címek vagy a teljes tartománynevek elérésére   |
| HTTP-proxy használata                 | A virtuális gépekhez való internetes hozzáférés egyetlen pontja                       | További költségek egy virtuális gép futtatásához a proxy szoftverrel         |

A fenti beállítások használatával kapcsolatos további részletekért lásd a következőt:

#### <a name="private-endpoints"></a>Privát végpontok

A privát végpontok lehetővé teszik a biztonságos kapcsolódást a virtuális hálózaton belüli kiszolgálókról a Recovery Services-tárba. A privát végpont egy IP-címet használ a tár VNET. A virtuális hálózaton belüli erőforrásai és a tároló közötti hálózati forgalom a virtuális hálózatra és a Microsoft gerinc hálózatán található privát kapcsolatra is áthalad. Ezzel kiküszöbölhető a nyilvános internetről való kitettség. További információ a Azure Backup privát végpontokról [itt](https://docs.microsoft.com/azure/backup/private-endpoints)olvasható.

#### <a name="nsg-tags"></a>NSG Címkék

Ha hálózati biztonsági csoportokat (NSG) használ, használja a *AzureBackup* szolgáltatás címkéjét, hogy engedélyezze a kimenő hozzáférést Azure Backuphoz. A Azure Backup címkén kívül az *Azure ad* -hez és az *Azure Storage*-hoz hasonló [NSG-szabályok](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) létrehozásával is engedélyeznie kell a csatlakozást a hitelesítéshez és az adatátvitelhez.  A következő lépések azt ismertetik, hogyan hozható létre szabály a Azure Backup címke számára:

1. A **minden szolgáltatás**területen lépjen a **hálózati biztonsági csoportok** elemre, és válassza ki a hálózati biztonsági csoportot.

1. A **Beállítások**területen válassza a **kimenő biztonsági szabályok** lehetőséget.

1. Válassza a **Hozzáadás** lehetőséget. Adja meg az új szabály létrehozásához szükséges összes adatot a [biztonsági szabály beállításai](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings)című témakörben leírtak szerint. Győződjön meg arról, hogy a **cél** a *Service tag* és a **cél szolgáltatás címkéje** *AzureBackup*értékre van állítva.

1. Kattintson a **Hozzáadás** gombra az újonnan létrehozott kimenő biztonsági szabály mentéséhez.

Hasonlóképpen NSG kimenő biztonsági szabályokat hozhat létre az Azure Storage és az Azure AD számára.

#### <a name="azure-firewall-tags"></a>Címkék Azure Firewall

Ha Azure Firewall használ, hozzon létre egy szabályt a *AzureBackup* [Azure Firewall FQDN címke](https://docs.microsoft.com/azure/firewall/fqdn-tags)használatával. Ez lehetővé teszi Azure Backup összes kimenő hozzáférését.

#### <a name="allow-access-to-service-ip-ranges"></a>Hozzáférés engedélyezése a szolgáltatás IP-tartományai számára

Ha a hozzáférési szolgáltatás IP-címeinek engedélyezését választja, tekintse meg az [itt](https://www.microsoft.com/download/confirmation.aspx?id=56519)elérhető JSON-fájl IP-tartományait. Engedélyeznie kell a hozzáférést a Azure Backup, az Azure Storage és a Azure Active Directory megfelelő IP-címekhez.

#### <a name="allow-access-to-service-fqdns"></a>Hozzáférés engedélyezése a szolgáltatás teljes tartománynevéhez

A következő teljes tartományneveket is használhatja a szükséges szolgáltatások elérésének engedélyezéséhez a kiszolgálókon:

| Szolgáltatás    | Elérni kívánt tartománynevek                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | Az 56-es és a 59-es szakaszban található teljes tartománynevek elérésének engedélyezése [a jelen cikk](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) alapján |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>HTTP-proxykiszolgáló használata a forgalom irányításához

Ha egy Azure-beli virtuális gépen futó SAP HANA adatbázisról készít biztonsági másolatot, a virtuális gépen található biztonsági mentési bővítmény a HTTPS API-k használatával küldi el a felügyeleti parancsokat a Azure Backup és az Azure Storage-ba történő adattároláshoz. A biztonsági mentési bővítmény az Azure AD-t is használja a hitelesítéshez. Irányítsa a biztonsági mentési bővítmény forgalmát a három szolgáltatáshoz a HTTP-proxyn keresztül. A fent említett IP-címek és FQDN-k listájának használata a szükséges szolgáltatásokhoz való hozzáférés engedélyezéséhez. A hitelesített proxykiszolgálók nem támogatottak.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Az adatbázisok felderítése

1. A tárolóban **első lépések**kattintson a **biztonsági mentés**elemre. A **hol fut a**számítási feladat? területen válassza **a SAP HANA lehetőséget az Azure-beli virtuális gépen**.
2. Kattintson a **felderítés indítása**gombra. Ez elindítja a nem védett Linux rendszerű virtuális gépek felderítését a tároló régiójában.

   * A felderítést követően a nem védett virtuális gépek a portálon jelennek meg a név és az erőforráscsoport alapján.
   * Ha a virtuális gép nem a várt módon van felsorolva, ellenőrizze, hogy már van-e biztonsági másolat egy tárolóban.
   * Több virtuális gépnek is ugyanaz a neve, de különböző erőforráscsoportok tartoznak hozzájuk.

3. A **Virtual Machines kiválasztása lapon**kattintson a hivatkozásra azon parancsfájl letöltéséhez, amely a Azure Backup szolgáltatás számára engedélyeket biztosít a SAP HANA virtuális gépek adatbázis-felderítéshez való hozzáféréséhez.
4. Futtassa a szkriptet minden olyan virtuális gépen, amely SAP HANA-adatbázisokat üzemeltet, amelyekről biztonsági másolatot szeretne készíteni.
5. Miután futtatta a szkriptet a virtuális gépeken, a **Virtual Machines kiválasztása**területen válassza ki a virtuális gépeket. Ezután kattintson az **adatbázisok felderítése**elemre.
6. Azure Backup a virtuális gépen lévő összes SAP HANA adatbázist felfedi. A felderítés során Azure Backup regisztrálja a virtuális gépet a tárolóban, és telepít egy bővítményt a virtuális gépre. Nincs ügynök telepítve az adatbázison.

    ![SAP HANA adatbázisok felderítése](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása  

Most engedélyezze a biztonsági mentést.

1. A 2. lépésben kattintson a **biztonsági mentés konfigurálása**elemre.

    ![Biztonsági mentés konfigurálása](./media/backup-azure-sap-hana-database/configure-backup.png)
2. Az **elemek kijelölése biztonsági mentéshez**területen jelölje ki az összes védelemmel ellátni kívánt adatbázist > **az OK gombra**.

    ![Válassza ki azokat az elemeket, amelyekről biztonsági másolatot szeretne készíteni](./media/backup-azure-sap-hana-database/select-items.png)
3. A **biztonsági mentési házirendben**  >  **válassza a biztonsági**mentési házirend elemet, hozzon létre egy új biztonsági mentési szabályzatot az adatbázisokhoz az alábbi utasítások szerint.

    ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-sap-hana-database/backup-policy.png)
4. A szabályzat létrehozása után a **biztonsági mentés** menüben kattintson a **biztonsági mentés engedélyezése**elemre.

    ![Biztonsági mentés engedélyezése](./media/backup-azure-sap-hana-database/enable-backup.png)
5. A biztonsági mentési konfiguráció előrehaladásának nyomon követése a portál **értesítések** területén.

### <a name="create-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

A biztonsági mentési szabályzat meghatározza a biztonsági másolatok készítésének idejét, valamint azt, hogy mennyi ideig őrzi meg a rendszer.

* A rendszer a tároló szintjén hozza létre a szabályzatot.
* Több tároló is használhatja ugyanazt a biztonsági mentési szabályzatot, de a biztonsági mentési szabályzatot minden egyes tárba alkalmaznia kell.

>[!NOTE]
>A Azure Backup nem módosítja automatikusan a nyári időmegtakarítást az Azure-beli virtuális gépen futó SAP HANA-adatbázis biztonsági mentésekor.
>
>Szükség szerint módosítsa manuálisan a szabályzatot.

A házirend-beállításokat a következőképpen adhatja meg:

1. A **Házirend neve**mezőben adja meg az új szabályzat nevét.

   ![Adja meg a szabályzat nevét](./media/backup-azure-sap-hana-database/policy-name.png)
2. A **teljes biztonsági mentési szabályzatban**válassza ki a **biztonsági mentés gyakoriságát**, és válassza a **naponta** vagy **hetente**lehetőséget.
   * **Napi**: válassza ki azt az órát és időzónát, amelyben a biztonsági mentési feladatok megkezdődnek.
       * Teljes biztonsági mentést kell futtatnia. Ezt a beállítást nem lehet kikapcsolni.
       * A szabályzat megtekintéséhez kattintson a **teljes biztonsági mentés** elemre.
       * A napi teljes biztonsági mentésekhez nem hozhat létre különbözeti biztonsági másolatokat.
   * **Hetente**: válassza ki a hét azon napját, óráját és időzónáját, amelyben a biztonsági mentési feladatot futtatja.

   ![Biztonsági mentés gyakoriságának kiválasztása](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. A **megőrzési tartomány**területen konfigurálja a teljes biztonsági mentés megőrzési beállításait.
    * Alapértelmezés szerint minden beállítás ki van választva. Törölje az összes olyan megőrzési időtartamra vonatkozó korlátozást, amelyet nem kíván használni, és állítsa be azokat.
    * A minimális megőrzési idő bármilyen típusú biztonsági mentés esetén (teljes/különbözeti/napló) hét nap.
    * A helyreállítási pontok megőrzési időtartam alapján vannak címkézve. Ha például napi teljes biztonsági mentést választ, a naponta csak egy teljes biztonsági mentést indít el.
    * Egy adott nap biztonsági másolata a heti megőrzési időtartam és a beállítás alapján van megcímkézve és megtartva.
    * A havi és az éves adatmegőrzési tartomány hasonló módon viselkedik.

4. A **teljes biztonsági mentési szabályzat** menüben kattintson az **OK** gombra a beállítások elfogadásához.
5. Válasszon különbözeti **biztonsági másolatot** a különbözeti szabályzat hozzáadásához.
6. A **különbözeti biztonsági mentési házirendben**válassza az **Engedélyezés** lehetőséget a gyakoriság és a megőrzési vezérlők megnyitásához.
    * A legtöbb esetben naponta egy különbözeti biztonsági mentést indíthat.
    * A különbözeti biztonsági mentések legfeljebb 180 napig tárolhatók. Ha nagyobb adatmegőrzésre van szüksége, teljes biztonsági mentést kell használnia.

    ![Különbözeti biztonsági mentési szabályzat](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > A növekményes biztonsági mentések jelenleg nem támogatottak.

7. A házirend mentéséhez kattintson az **OK** gombra, és térjen vissza a **biztonsági mentési házirend** fő menüjére.
8. A tranzakciós napló biztonsági mentési szabályzatának hozzáadásához válassza a **napló biztonsági mentése** lehetőséget,
    * A **napló biztonsági mentése**területen válassza az **Engedélyezés**lehetőséget.  Ez nem tiltható le, mert SAP HANA kezeli az összes napló biztonsági mentését.
    * Állítsa be a gyakoriságot és a megőrzési vezérlőket.

    > [!NOTE]
    > A biztonsági másolatok naplózása csak a sikeres teljes biztonsági mentés befejezése után kezdi a folyamatot.

9. A házirend mentéséhez kattintson az **OK** gombra, és térjen vissza a **biztonsági mentési házirend** fő menüjére.
10. Miután befejezte a biztonsági mentési szabályzat definiálását, kattintson **az OK**gombra.

> [!NOTE]
> Minden naplózási biztonsági mentés az előző teljes biztonsági mentéshez van láncolva, amely helyreállítási láncot alkot. Ez a teljes biztonsági mentés a legutóbbi napló biztonsági mentésének lejárta után is megmarad. Ez azt jelentheti, hogy a teljes biztonsági mentést egy további időszakra vonatkozóan kell megőrizni, hogy az összes napló helyreállítható legyen. Tegyük fel, hogy a felhasználó heti teljes biztonsági mentést, napi különbözeti és 2 órás naplókat tartalmaz. Mindegyiket 30 napig őrzi meg a rendszer. A hetente megteltek azonban csak a következő teljes biztonsági mentés elérhetővé tételével, azaz 30 + 7 nap után törlődnek. Tegyük fel, hogy a heti teljes biztonsági mentés november 16-án történik. Az adatmegőrzési szabályzatnak megfelelően a december 16-i-ig meg kell őrizni. Az utolsó napló biztonsági mentése ehhez a teljes, november 22-én a következő beütemezett megtelte előtt történik. Amíg a napló nem érhető el a DEC 22nd-ig, a november 16-i teljes nem törölhető. Így a november 16-i Full megmarad december 22-én.

## <a name="run-an-on-demand-backup"></a>Igény szerinti biztonsági mentés futtatása

A biztonsági mentések a szabályzat ütemezésével összhangban futnak. Az igény szerinti biztonsági mentést az alábbi módon futtathatja:

1. A tároló menüjében kattintson a **biztonsági másolati elemek elemre**.
2. A **biztonsági másolati elemek**területen válassza ki a SAP HANA adatbázist futtató virtuális gépet, majd kattintson a **biztonsági mentés**elemre.
3. A **biztonsági mentés most**a Calendar (naptár) vezérlőelem használatával válassza ki azt az utolsó napot, ameddig a helyreállítási pontot meg kell őrizni. Ezt követően kattintson az **OK** gombra.
4. A portál értesítéseinek figyelése. A feladat előrehaladását a tároló irányítópultján követheti nyomon > **biztonsági mentési feladatok**  >  **folyamatban**vannak. Az adatbázis méretétől függően a kezdeti biztonsági mentés hosszabb időt is igénybe vehet.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>SAP HANA Studio Backup futtatása Azure Backup engedélyezve lévő adatbázison

Ha egy olyan adatbázis helyi biztonsági másolatát kívánja használni, amelyről biztonsági mentés készül Azure Backupsal, tegye a következőket:

1. Várjon, amíg befejeződik az adatbázis teljes vagy naplózott biztonsági mentése. Az állapot ellenõrzése SAP HANA Studio/pilótafülkében.
2. Tiltsa le a naplók biztonsági mentését, és állítsa a biztonsági mentési katalógust a fájlrendszerre a megfelelő adatbázishoz.
3. Ehhez kattintson duplán a **systemdb**-konfiguráció elemre, majd  >  **Configuration**  >  **válassza az adatbázis**  >  **-szűrő (napló)** lehetőséget.
4. A **enable_auto_log_backup** beállítása **nem**értékre.
5. **Log_backup_using_backint** beállítása **hamis**értékre.
6. Igény szerint készítsen teljes biztonsági mentést az adatbázisról.
7. Várjon, amíg befejeződik a teljes biztonsági mentés és a katalógus biztonsági mentése.
8. A korábbi beállítások visszaállítása az Azure-ba:
    * Állítsa **enable_auto_log_backup** a Enable_auto_log_backup **értéket igen**értékre.
    * A **log_backup_using_backint** beállítása **igaz**értékre.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [állíthatja vissza az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokat](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Megtudhatja, hogyan [kezelheti SAP HANA-adatbázisok biztonsági mentését a Azure Backup használatával](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
