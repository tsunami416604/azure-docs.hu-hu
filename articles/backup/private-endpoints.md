---
title: Privát végpontok
description: Megtudhatja, hogyan hozhat létre privát végpontokat a Azure Backuphoz, és hogy a saját végpontok használata hogyan segít megőrizni az erőforrások biztonságát.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: 9158ad23bf05bf52f879afb1f1d25d2f4ba42cfb
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84323637"
---
# <a name="private-endpoints-for-azure-backup"></a>Azure Backup magánhálózati végpontok

Azure Backup lehetővé teszi az adatok biztonságos biztonsági mentését és visszaállítását a Recovery Services-tárolóból [privát végpontok](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)használatával. A privát végpontok egy vagy több magánhálózati IP-címet használnak a VNet, és így hatékonyan helyezik üzembe a szolgáltatást a VNet.

Ez a cikk segít megérteni a Azure Backup magánhálózati végpontok létrehozásának folyamatát, valamint azokat a forgatókönyveket, amelyekben a privát végpontok használata hozzájárul az erőforrások biztonságának fenntartásához.

## <a name="before-you-start"></a>Előkészületek

- A magánhálózati végpontok csak az új Recovery Services-tárolók számára hozhatók létre (amelyek nem rendelkeznek a tárolóhoz regisztrált elemekkel). Ezért létre kell hozni a privát végpontokat, mielőtt a tár bármely elemét védetté tenné.
- Egy virtuális hálózat több Recovery Services-tárolóhoz is tartalmazhat privát végpontokat. Emellett egy Recovery Services-tár több virtuális hálózatban is rendelkezhet saját végpontokkal. Azonban a tárolóhoz létrehozható privát végpontok maximális száma 12.
- Miután létrehozta a tárolóhoz egy privát végpontot, a rendszer zárolja a tárolót. Nem lesz elérhető (biztonsági mentéshez és visszaállításhoz) a hálózatokon kívülről is, amelyek a tárolóhoz privát végpontot tartalmaznak. Ha a tár összes privát végpontja törlődik, a tároló minden hálózatról elérhető lesz.
- A Recovery Services-tárolót (mindkettő) Azure Backup és Azure Site Recovery is használja, ez a cikk a privát végpontok használatát ismerteti csak Azure Backup esetén.
- Azure Active Directory jelenleg nem támogatja a privát végpontokat. A Azure Active Directory működéséhez szükséges IP-címeket és teljes tartományneveket engedélyezni kell a biztonságos hálózatról, amikor az Azure-beli virtuális gépeken található adatbázisok biztonsági mentését hajtja végre, és a MARS-ügynök használatával készít biztonsági mentést. NSG-címkéket és Azure Firewall címkéket is használhat az Azure AD-hez való hozzáférés engedélyezéséhez.
- A hálózati házirendekkel rendelkező virtuális hálózatok magánhálózati végpontok esetén nem támogatottak. A folytatás előtt le kell tiltania a hálózati házirendeket.
- A Recovery Services erőforrás-szolgáltatót újra regisztrálnia kell az előfizetéssel, ha a regisztrációja előtt az 2020. május 1. előtt regisztrálta. A szolgáltató újbóli regisztrálásához nyissa meg az előfizetését a Azure Portalban, navigáljon az **erőforrás-szolgáltatóhoz** a bal oldali navigációs sávon, majd válassza a **Microsoft. recoveryservices szolgáltatónál** elemet, és kattintson az **újbóli regisztrálás**gombra.

## <a name="recommended-and-supported-scenarios"></a>Ajánlott és támogatott forgatókönyvek

Habár a privát végpontok engedélyezve vannak a tárolóhoz, az SQL-és SAP HANA-munkaterhelések biztonsági mentésére és helyreállítására szolgálnak az Azure-beli virtuális gépeken és a MARS-ügynök biztonsági mentésében. A tárolót használhatja más számítási feladatok biztonsági mentéséhez is (a saját végpontokat azonban nem szükséges). Az SQL és a SAP HANA számítási feladatainak és a MARS-ügynök használatával történő biztonsági mentésének kiegészítéseként az Azure-beli virtuális gépek biztonsági mentése esetén a rendszer a fájlok helyreállítását is elvégzi. További információkért tekintse meg a következő táblázatot:

| Munkaterhelések biztonsági mentése az Azure-beli virtuális gépen (SQL, SAP HANA), biztonsági mentés a MARS-ügynök használatával | A privát végpontok használata ajánlott a biztonsági mentéshez és a visszaállításhoz anélkül, hogy engedélyezni kellene a-t a virtuális hálózatok Azure Backup vagy Azure Storage-beli IP-címeinek/teljes tartománynevének listázásához. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Azure-beli virtuális gép biztonsági mentése**                                         | A virtuális gép biztonsági mentése nem igényli, hogy bármely IP-cím vagy teljes tartománynév számára engedélyezze a hozzáférést. Így nincs szükség privát végpontokra a lemezek biztonsági mentéséhez és visszaállításához.  <br><br>   A privát végpontokat tartalmazó tárolóból származó fájlok helyreállítása azonban olyan virtuális hálózatokra korlátozódik, amelyek a tárolóhoz privát végpontot tartalmaznak. <br><br>    A ACL'ed nem felügyelt lemezek használata esetén győződjön meg arról, hogy a lemezeket tartalmazó Storage-fiók lehetővé teszi a **megbízható Microsoft-szolgáltatások** elérését, ha az ACL'ed. |
| **Azure Files biztonsági mentés**                                      | Azure Files biztonsági mentéseket a helyi Storage-fiók tárolja. Így nincs szükség privát végpontokra a biztonsági mentéshez és a visszaállításhoz. |

## <a name="creating-and-using-private-endpoints-for-backup"></a>Privát végpontok létrehozása és használata a biztonsági mentéshez

Ez a szakasz a virtuális hálózatokon belüli Azure Backup magánhálózati végpontok létrehozásával és használatával kapcsolatos lépésekkel foglalkozik.

>[!IMPORTANT]
> Erősen ajánlott, hogy kövesse a jelen dokumentumban említett lépéseket. Ennek elmulasztása miatt előfordulhat, hogy a tár nem kompatibilis a privát végpontok használatára, és egy új tárolóval újra kell indítania a folyamatot.

>[!NOTE]
> Lehetséges, hogy a Azure Portal-élmény bizonyos elemei jelenleg nem érhetők el. Tekintse meg az ilyen helyzetekben felhasználható alternatív tapasztalatokat, amíg a teljes rendelkezésre állást el nem végzi a régiójában.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

[Ebből a szakaszból](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) megtudhatja, hogyan hozhat létre tárolót az Azure Resource Manager-ügyfél használatával. Ezzel létrehoz egy tárolót, amelynek felügyelt identitása már engedélyezve van. További információ a Recovery Services-tárolóról [.](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)

## <a name="enable-managed-identity-for-your-vault"></a>Felügyelt identitás engedélyezése a tárolóhoz

A felügyelt identitások lehetővé teszik, hogy a tároló privát végpontokat hozzon létre és használjon. Ez a szakasz a tár felügyelt identitásának engedélyezését tárgyalja.

1. Nyissa meg a Recovery Services > **identitását**.

    ![Identitás állapotának módosítása a következőre](./media/private-endpoints/identity-status-on.png)

1. Módosítsa az **állapotot** **a be** értékre, majd kattintson a **Mentés**gombra.

1. A rendszer létrehoz egy **objektumazonosító-azonosítót** , amely a tár felügyelt identitása.

    >[!NOTE]
    >Ha engedélyezve van, a felügyelt identitás nem tiltható le (akár átmenetileg is). A felügyelt identitás letiltása inkonzisztens viselkedést eredményezhet.

## <a name="dns-changes"></a>DNS-változások

A privát végpontok használatához saját DNS zónák szükségesek ahhoz, hogy a biztonsági mentési bővítmény feloldja a privát IP-címek magánhálózati kapcsolatának teljes tartománynevét. Összességében három magánhálózati DNS-zónára van szükség. Habár a zónák közül kettőt létre kell hozni kötelezően, a harmadik lehet úgy, hogy integrálva van a privát végponttal (a privát végpont létrehozásakor), vagy külön lehet létrehozni.

Használhatja az egyéni DNS-kiszolgálókat is. Az egyéni DNS-kiszolgálók használatával kapcsolatos részletekért tekintse meg az [Egyéni DNS-kiszolgálók DNS-módosításait](#dns-changes-for-custom-dns-servers) .

### <a name="creating-mandatory-dns-zones"></a>Kötelező DNS-zónák létrehozása

Két kötelező DNS-zónát kell létrehoznia:

- `privatelink.blob.core.windows.net`(biztonsági mentés/visszaállítás céljából)
- `privatelink.queue.core.windows.net`(a szolgáltatással való kommunikációhoz)

1. Keresse meg **saját DNS zónát** a **minden szolgáltatás** keresési sávján, és válassza ki **saját DNS zónát** a legördülő listából.

    ![saját DNS zóna kiválasztása](./media/private-endpoints/private-dns-zone.png)

1. Az **saját DNS zóna** ablaktáblán kattintson a **+ Hozzáadás** gombra az új zóna létrehozásának megkezdéséhez.

1. A **saját DNS-zóna létrehozása** panelen adja meg a szükséges adatokat. Az előfizetésnek meg kell egyeznie a privát végpont létrehozásának helyétől.

    A zónákat a következőképpen kell elnevezni:

    - `privatelink.blob.core.windows.net`
    - `privatelink.queue.core.windows.net`

    | **Zóna**                           | **Szolgáltatás** | **Előfizetés és erőforráscsoport (RG) részletei**                  |
    | ---------------------------------- | ----------- | ------------------------------------------------------------ |
    | `privatelink.blob.core.windows.net`  | Blob        | **Előfizetés**: ugyanaz, mint ahol a privát végpontot létre kell hozni **RG**: vagy a VNET vagy a privát végponthoz tartozó RG. |
    | `privatelink.queue.core.windows.net` | Várólista       | **RG**: vagy a VNET vagy a privát végponthoz tartozó RG |

    ![saját DNS zóna létrehozása](./media/private-endpoints/create-private-dns-zone.png)

1. Ha elkészült, folytassa a DNS-zóna áttekintésével és létrehozásával.

### <a name="optional-dns-zone"></a>Opcionális DNS-zóna

Az ügyfelek dönthetnek úgy, hogy a magánhálózati végpontokat a Azure Backup magánhálózati DNS-zónákkal integrálják (lásd a privát végpontok létrehozásáról szóló szakaszt) a szolgáltatásokkal való kommunikációhoz. Ha nem kívánja integrálni a magánhálózati DNS-zónával, dönthet úgy, hogy saját DNS-kiszolgálót használ, vagy külön saját DNS-zónát hoz létre. Ez az előző szakaszban tárgyalt két kötelező magánhálózati DNS-zónán kívül történik.

Ha önálló, saját DNS-zónát szeretne létrehozni az Azure-ban, ugyanezt a lépéseket használhatja a kötelező DNS-zónák létrehozásához. Az elnevezési és az előfizetés részletei a következőkben vannak elosztva:

| **Zóna**                                                     | **Szolgáltatás** | **Az előfizetés és az erőforráscsoport részletei**                  |
| ------------------------------------------------------------ | ----------- | ------------------------------------------------------------ |
| `privatelink.<geo>.backup.windowsazure.com`  <br><br>   **Megjegyzés**: a *geo* itt a régiókódra hivatkozik. Például: *wcus* és *ne* az USA nyugati középső régiójában és Észak-Európában. | Backup      | **Előfizetés**: ugyanaz, mint ahol a privát végpontot létre kell hozni **RG**: az előfizetésen belüli bármely RG |

Tekintse át [ezt a listát](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx) a régiókódokhoz.

Az URL-címek elnevezési konvenciói a nemzeti régiókban:

- [Kína](https://docs.microsoft.com/azure/china/resources-developer-guide#check-endpoints-in-azure)
- [Németország](https://docs.microsoft.com/azure/germany/germany-developer-guide#endpoint-mapping)
- [US Gov](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide)

### <a name="linking-private-dns-zones-with-your-virtual-network"></a>Privát DNS-zónák összekapcsolása a virtuális hálózattal

A fent létrehozott DNS-zónákat most ahhoz a virtuális hálózathoz kell kapcsolni, ahol a biztonsági mentésre kerülő kiszolgálók találhatók. Ezt az összes létrehozott DNS-zóna esetében el kell végezni.

1. Nyissa meg a DNS-zónát (amelyet az előző lépésben hozott létre), és navigáljon a bal oldali sávban található **virtuális hálózati kapcsolatokhoz** . Kattintson a **+ Hozzáadás** gombra
1. Adja meg a szükséges adatokat. Az **előfizetés** és a **virtuális hálózat** mezőket meg kell adni annak a virtuális hálózatnak a megfelelő részleteivel, ahol a kiszolgálók léteznek. A többi mezőnek a következőképpen kell maradnia:.

    ![Virtuális hálózati kapcsolat hozzáadása](./media/private-endpoints/add-virtual-network-link.png)

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>Engedélyek megadása a tárolónak a szükséges privát végpontok létrehozásához

A Azure Backuphoz szükséges privát végpontok létrehozásához a tárolónak (a tár felügyelt identitásának) a következő erőforráscsoportok engedélyekkel kell rendelkeznie:

- A célként megadott VNet tartalmazó erőforráscsoport
- Az erőforráscsoport, amelyben létre kell hozni a privát végpontokat
- Az saját DNS zónákat tartalmazó erőforráscsoport

Javasoljuk, hogy adja meg a **közreműködői** szerepkört a három erőforráscsoport számára a tárolóhoz (felügyelt identitás). A következő lépések azt írják le, hogyan kell ezt megtenni egy adott erőforráscsoport esetében (ezt a három erőforráscsoporthoz kell elvégezni):

1. Nyissa meg az erőforráscsoportot, és navigáljon a **Access Control (iam)** elemre a bal oldali sávon.
1. Ha **Access Control**, lépjen a **szerepkör-hozzárendelés hozzáadása**elemre.

    ![Szerepkör-hozzárendelés hozzáadása](./media/private-endpoints/add-role-assignment.png)

1. A **szerepkör-hozzárendelés hozzáadása** panelen válassza a **közreműködő** **szerepkört**, és használja a tároló **nevét** a **rendszerbiztonsági tag**néven. Válassza ki a tárolót, és kattintson a **Mentés** gombra, ha elkészült.

    ![Szerepkör és rendszerbiztonsági tag kiválasztása](./media/private-endpoints/choose-role-and-principal.png)

Az engedélyek részletesebb kezeléséhez tekintse meg a [szerepkörök és engedélyek manuális létrehozása](#create-roles-and-permissions-manually)című témakört.

## <a name="creating-and-approving-private-endpoints-for-azure-backup"></a>Azure Backup magánhálózati végpontok létrehozása és jóváhagyása

### <a name="creating-private-endpoints-for-backup"></a>Privát végpontok létrehozása a biztonsági mentéshez

Ez a szakasz azt ismerteti, hogyan hozható létre saját végpont a tárolóhoz.

1. A keresési sávban keresse meg és válassza ki a **privát hivatkozás**lehetőséget. Ekkor megjelenik a **privát kapcsolati központ**.

    ![Privát hivatkozás keresése](./media/private-endpoints/search-for-private-link.png)

1. A bal oldali navigációs sávon kattintson a **privát végpontok**elemre. Egyszer a **privát végpontok** ablaktáblán kattintson a **+ Hozzáadás** elemre a saját tárolóhoz tartozó privát végpont létrehozásának megkezdéséhez.

    ![Privát végpont hozzáadása a privát kapcsolati központban](./media/private-endpoints/add-private-endpoint.png)

1. Ha a **privát végpont létrehozása** folyamatban van, meg kell adnia a privát végponti kapcsolat létrehozásához szükséges adatokat.

    1. **Alapismeretek**: adja meg a privát végpontok alapszintű részleteit. A régiónak meg kell egyeznie a tárolóval és az erőforrással.

        ![Adja meg az alapszintű részleteket](./media/private-endpoints/basic-details.png)

    1. **Erőforrás**: ezen a lapon megemlítheti azt a Pásti-erőforrást, amelyhez létre kívánja hozni a kapcsolatát. Válassza ki a **Microsoft. recoveryservices szolgáltatónál/Vaults** elemet a kívánt előfizetés erőforrástípus alapján. Ha elkészült, válassza ki a Recovery Services-tároló nevét az **erőforrás** -és **AzureBackup** célként megadott **alerőforrásként**.

        ![Töltse ki az Erőforrás lapot](./media/private-endpoints/resource-tab.png)

    1. **Konfiguráció**: a konfiguráció területen válassza ki azt a virtuális hálózatot és alhálózatot, ahol létre szeretné hozni a privát végpontot. Ez lenne a vnet, ahol a virtuális gép megtalálható. Dönthet úgy, hogy **integrálja a privát végpontot** egy privát DNS-zónával. Másik lehetőségként használhatja az egyéni DNS-kiszolgálót, vagy létrehozhat egy magánhálózati DNS-zónát is.

        ![Kitöltés a konfiguráció lapon](./media/private-endpoints/configuration-tab.png)

    1. Igény szerint hozzáadhat **címkéket** a privát végponthoz.

    1. A részletek megadása után folytassa a **felülvizsgálat + létrehozás** gombra kattintva. Az ellenőrzés befejeződése után kattintson a **Létrehozás** gombra a privát végpont létrehozásához.

## <a name="approving-private-endpoints"></a>Privát végpontok jóváhagyása

Ha a privát végpontot létrehozó felhasználó egyben a Recovery Services-tároló tulajdonosa is, a fent létrehozott privát végpont automatikusan jóvá lesz hagyva. Ellenkező esetben a tároló tulajdonosának jóvá kell hagynia a privát végpontot, mielőtt használni tudná. Ez a szakasz a privát végpontok manuális jóváhagyását ismerteti a Azure Portalon keresztül.

Lásd: [privát végpontok manuális jóváhagyása a Azure Resource Manager ügyfél használatával](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) a Azure Resource Manager ügyfél használatára a privát végpontok jóváhagyásához.

1. A Recovery Services-tárolóban navigáljon a bal oldali sávban található **privát végponti kapcsolatokhoz** .
1. Válassza ki a jóváhagyni kívánt privát végpont-kapcsolatokat.
1. Válassza a **jóváhagyás** lehetőséget a felső sávon. Az **elutasítás** vagy az **Eltávolítás** lehetőséget is választhatja, ha el kívánja utasítani vagy törölni szeretné a végponti kapcsolatokat.

    ![Privát végpontok jóváhagyása](./media/private-endpoints/approve-private-endpoints.png)

## <a name="adding-dns-records"></a>DNS-rekordok hozzáadása

>[!NOTE]
> Ez a lépés nem kötelező, ha integrált DNS-zónát használ. Ha azonban létrehozta saját Azure saját DNS-zónáját, vagy egyéni magánhálózati DNS-zónát használ, győződjön meg arról, hogy az ebben a szakaszban leírt módon szerepelnek a bejegyzések.

Miután létrehozta a választható magánhálózati DNS-zónát és a tárolóhoz tartozó magánhálózati végpontokat, DNS-rekordokat kell hozzáadnia a DNS-zónához. Ezt manuálisan vagy PowerShell-parancsfájl használatával is elvégezheti. Ezt csak a biztonsági mentési DNS-zónára kell elvégezni, a blobok és a várólisták esetében a rendszer automatikusan frissíti azokat.

### <a name="add-records-manually"></a>Rekordok manuális hozzáadása

Ehhez a privát végponton lévő összes FQDN-hez bejegyzéseket kell készítenie a saját DNS zónába.

1. Nyissa meg a **saját DNS-zónáját** , és navigáljon a bal oldali sávban található **Áttekintés** lehetőségre. Ha van, kattintson a **+** rekordhalmaz gombra a rekordok hozzáadásának megkezdéséhez.

    ![Válassza a + rekordhalmaz elemet a rekordok hozzáadásához](./media/private-endpoints/select-record-set.png)

1. A megnyíló **rekordazonosító hozzáadása** panelen adjon hozzá egy BEJEGYZÉST minden FQDN és magánhálózati IP-cím **típusú** rekordként. A teljes tartománynevek és IP-címek listája a privát végpontról szerezhető be (az **Áttekintés**alatt). Ahogy az alábbi példában is látható, a magánhálózati végpont első teljes tartományneve hozzá lesz adva a saját DNS-zónában lévő rekordhoz.

    ![Teljes tartománynevek és IP-címek listája](./media/private-endpoints/list-of-fqdn-and-ip.png)

    ![Rekordhalmaz hozzáadása](./media/private-endpoints/add-record-set.png)

### <a name="add-records-using-powershell-script"></a>Rekordok hozzáadása PowerShell-parancsfájl használatával

1. Indítsa el a **Cloud Shell** a Azure Portalban, és válassza a **fájl feltöltése** lehetőséget a PowerShell ablakban.

    ![Válassza a fájl feltöltése a PowerShell-ablakban lehetőséget.](./media/private-endpoints/upload-file-in-powershell.png)

1. Töltse fel a következő szkriptet: [DnsZoneCreation](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/dnszonerecordcreation.ps1)

1. Nyissa meg a saját mappáját (például: `cd /home/user` )

1. Futtassa a következő parancsfájlt:

    ```azurepowershell
    ./dnszonerecordcreation.ps1 -Subscription <SubscriptionId> -VaultPEName <VaultPE Name> -VaultPEResourceGroup <Vault PE RG> -DNSResourceGroup <Private DNS RG> -Privatezone <privatednszone>
    ```

    Ezek a paraméterek:

    - **előfizetés**: az az előfizetés, amelyben az erőforrások (a tároló magánhálózati végpontja és a saját DNS-zóna) található.
    - **vaultPEName**: a tárolóhoz létrehozott privát végpont neve
    - **vaultPEResourceGroup**: a tár privát végpontját tartalmazó erőforráscsoport
    - **dnsResourceGroup**: a magánhálózati DNS-zónákat tartalmazó erőforráscsoport
    - **Privatezone**: a magánhálózati DNS-zóna neve

## <a name="using-private-endpoints-for-backup"></a>Privát végpontok használata biztonsági mentéshez

Miután jóváhagyta a VNet-tárolóhoz létrehozott privát végpontokat, megkezdheti a biztonsági mentések és a visszaállítások elvégzését.

>[!IMPORTANT]
>A folytatás előtt győződjön meg arról, hogy sikeresen végrehajtotta az összes fent említett lépést a dokumentumban. A betöltéshez el kell végeznie az alábbi ellenőrzőlista lépéseit:
>
>1. Létrehozott egy (új) Recovery Services-tárolót
>1. A tár engedélyezése a rendszerhez rendelt felügyelt identitás használatára
>1. Három saját DNS zóna létrehozása (kettő, ha integrált DNS-zónát használ a biztonsági mentéshez)
>1. saját DNS zónák összekapcsolása az Azure-Virtual Network
>1. A tár felügyelt identitásához hozzárendelt megfelelő engedélyek
>1. Létrehozott egy privát végpontot a tárolóhoz
>1. Jóváhagyta a privát végpontot (ha nem engedélyezett automatikusan)
>1. A kötelező DNS-rekordok hozzáadása a saját DNS-zónához a biztonsági mentéshez (csak akkor érvényes, ha nem használ integrált magánhálózati DNS-zónát)

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-sap-hana"></a>Munkaterhelések biztonsági mentése és visszaállítása az Azure-beli virtuális gépen (SQL, SAP HANA)

Miután létrehozta és jóváhagyta a privát végpontot, a privát végpont használatához nincs szükség további módosításokra az ügyfél oldalán. A biztonságos hálózatról a tárolóra irányuló összes kommunikációt és adatátvitelt a privát végponton keresztül hajtja végre a rendszer.
Ha azonban egy kiszolgáló (SQL/SAP HANA) regisztrálása után eltávolít egy privát végpontot a tárolóhoz, akkor újra regisztrálnia kell a tárolót a tárolóban. Nem kell leállítania a védelmet.

### <a name="backup-and-restore-through-mars-agent"></a>Biztonsági mentés és visszaállítás a MARS-ügynökön keresztül

Ha a MARS-ügynököt használja a helyszíni erőforrások biztonsági mentésére, győződjön meg arról, hogy a helyszíni hálózat (a biztonsági mentéshez szükséges erőforrásokat tartalmazza) az Azure-VNet van társítva, amely a tárolóhoz saját végpontot tartalmaz, így használhatja azt. Ezután továbbra is telepítheti a MARS-ügynököt, és konfigurálhatja a biztonsági mentést az itt leírtak szerint. Gondoskodnia kell azonban arról, hogy a biztonsági mentés minden kommunikációja csak a csak a hálózaton keresztül történjen.

Ha azonban a MARS-ügynök regisztrálása után eltávolítja a tárolóhoz tartozó magánhálózati végpontokat, újra regisztrálnia kell a tárolót a tárolóban. Nem kell leállítania a védelmet.

## <a name="additional-topics"></a>További témakörök

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Recovery Services-tároló létrehozása az Azure Resource Manager ügyfél használatával

Létrehozhatja a Recovery Services-tárolót, és engedélyezheti a felügyelt identitását (a felügyelt identitás engedélyezése szükséges, ahogy azt később látni fogjuk) a Azure Resource Manager-ügyfél használatával. Ehhez az alábbi módon kell megosztva egy mintát:

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

A fenti JSON-fájlnak a következő tartalommal kell rendelkeznie:

Kérelem JSON:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

Válasz JSON:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>A példában a Azure Resource Manager ügyfélen létrehozott tár már létre van hozva egy rendszer által hozzárendelt felügyelt identitással.

### <a name="managing-permissions-on-resource-groups"></a>Erőforráscsoportok engedélyeinek kezelése

A tár felügyelt identitásának a következő engedélyekkel kell rendelkeznie az erőforráscsoport és a virtuális hálózat között, ahol a magánhálózati végpontok létre lesznek hozva:

- `Microsoft.Network/privateEndpoints/*`Ez szükséges ahhoz, hogy a szifilisz az erőforráscsoport privát végpontján legyen végrehajtva. Hozzá kell rendelni az erőforráscsoporthoz.
- `Microsoft.Network/virtualNetworks/subnets/join/action`Erre azért van szükség, mert a virtuális hálózaton a magánhálózati IP-cím csatlakozik a privát végponthoz.
- `Microsoft.Network/networkInterfaces/read`Erre azért van szükség az erőforráscsoporthoz, hogy megkapja a magánhálózati végponthoz létrehozott hálózati adaptert.
- Saját DNS zóna közreműködői szerepköre ez a szerepkör már létezik, és használható a biztosításához és az engedélyek megadásához `Microsoft.Network/privateDnsZones/A/*` `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` .

A szükséges engedélyekkel rendelkező szerepkörök létrehozásához az alábbi módszerek egyikét használhatja:

#### <a name="create-roles-and-permissions-manually"></a>Szerepkörök és engedélyek manuális létrehozása

Hozza létre a következő JSON-fájlokat, és használja a szakasz végén található PowerShell-parancsot a szerepkörök létrehozásához:

PrivateEndpointContributorRoleDef. JSON

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

NetworkInterfaceReaderRoleDef. JSON

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

PrivateEndpointSubnetContributorRoleDef. JSON

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>Parancsfájl használata

1. Indítsa el a **Cloud Shell** a Azure Portalban, és válassza a **fájl feltöltése** lehetőséget a PowerShell ablakban.

    ![Válassza a fájl feltöltése a PowerShell-ablakban lehetőséget.](./media/private-endpoints/upload-file-in-powershell.png)

1. Töltse fel a következő szkriptet: [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. Nyissa meg a saját mappáját (például: `cd /home/user` )

1. Futtassa a következő parancsfájlt:

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    Ezek a paraméterek:

    - **előfizetés**: * * olyan SubscriptionId, amelyhez a tárolóhoz tartozó magánhálózati végpontot kell létrehozni, valamint azt az alhálózatot, ahol a tár magánhálózati végpontja csatolva lesz

    - **vaultPEResourceGroup**: az erőforráscsoport, amelyben létrejön a tár magánhálózati végpontja

    - **vaultPESubnetResourceGroup**: annak az alhálózatnak az erőforráscsoport, amelyhez a magánhálózati végpont csatlakozni fog

    - **vaultMsiName**: a tár MSI-fájljának neve, amely ugyanaz, mint a **VaultName**

1. Végezze el a hitelesítést, és a szkript a fent megadott előfizetés kontextusát fogja elvégezni. Létrehozza a megfelelő szerepköröket, ha hiányoznak a bérlőből, és hozzárendeli a szerepköröket a tároló MSI-hez.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Privát végpontok létrehozása Azure PowerShell használatával

#### <a name="auto-approved-private-endpoints"></a>Automatikusan jóváhagyott privát végpontok

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  
  
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Privát végpontok manuális jóváhagyása az Azure Resource Manager ügyfél használatával

1. A **GetVault** használatával szerezze be a privát végponthoz tartozó MAGÁNHÁLÓZATI kapcsolatok azonosítóját.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    Ez visszaküldi a magánhálózati végponti kapcsolatok AZONOSÍTÓját. A kapcsolat neve a következő módon kérhető le a kapcsolati azonosító első részének használatával:

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. Kérje le a **privát végponti kapcsolat azonosítóját** (és a **magánhálózati végpont nevét**, ahol szükséges) a válaszból, és cserélje le a következő JSON-és Azure Resource Manager URI-ra, majd próbálja meg módosítani az állapotot "jóváhagyott/visszautasított/leválasztott" értékre, ahogyan az alábbi példában látható:

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

### <a name="dns-changes-for-custom-dns-servers"></a>DNS-változások az egyéni DNS-kiszolgálókon

#### <a name="create-dns-zones-for-custom-dns-servers"></a>DNS-zónák létrehozása egyéni DNS-kiszolgálókhoz

Létre kell hoznia három privát DNS-zónát, és csatolnia kell őket a virtuális hálózathoz.

| **Zóna**                                                     | **Szolgáltatás** |
| ------------------------------------------------------------ | ----------- |
| `privatelink.<geo>.backup.windowsazure.com`      | Backup      |
| `privatelink.blob.core.windows.net`                            | Blob        |
| `privatelink.queue.core.windows.net`                           | Várólista       |

>[!NOTE]
>A fenti szövegben a *geo* a régiókódra hivatkozik. Például: *wcus* és *ne* az USA nyugati középső régiójában és Észak-Európában.

Tekintse át [ezt a listát](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx) a régiókódokhoz.

#### <a name="adding-dns-records-for-custom-dns-servers"></a>DNS-rekordok hozzáadása az egyéni DNS-kiszolgálókhoz

Ehhez a privát végponton lévő összes FQDN-hez bejegyzéseket kell készítenie a saját DNS zónába.

Érdemes megjegyezni, hogy a biztonsági mentéshez, a Blobhoz és a Queue szolgáltatáshoz létrehozott privát végpontokat fogjuk használni.

- A tárolóhoz tartozó magánhálózati végpont a magánhálózati végpont létrehozásakor megadott nevet használja.
- A blob-és üzenetsor-szolgáltatásokhoz tartozó magánhálózati végpontok előre vannak meghatározva a tárolóhoz tartozó névvel.

Az alábbi képen például a *pee2epe*nevű privát végponti kapcsolatban létrehozott három privát végpont látható:

![Három privát végpont a privát végponti kapcsolatok számára](./media/private-endpoints/three-private-endpoints.png)

A biztonsági mentési szolgáltatás DNS-zónája ( `privatelink.<geo>.backup.windowsazure.com` ):

1. Navigáljon a privát végponthoz a biztonsági mentéshez a **Private link Centerben**. Az áttekintő lap felsorolja a privát végpont teljes tartománynevét és magánhálózati IP-címeit.

1. Adjon hozzá egy bejegyzést minden FQDN és magánhálózati IP-cím típusú rekordként.

    ![Bejegyzés hozzáadása az egyes FQDN-és magánhálózati IP-címekhez](./media/private-endpoints/add-entry-for-each-fqdn-and-ip.png)

A Blob service DNS-zónája ( `privatelink.blob.core.windows.net` ):

1. Navigáljon a privát végponthoz a Blobhoz a **Private link Centerben**. Az áttekintő lap felsorolja a privát végpont teljes tartománynevét és magánhálózati IP-címeit.

1. Adjon hozzá egy bejegyzést a teljes tartománynév és a magánhálózati IP-cím típusú rekordként.

    ![Adja hozzá a teljes tartománynév és a magánhálózati IP-cím bejegyzést a Blob service](./media/private-endpoints/add-type-a-record-for-blob.png)

A Queue szolgáltatás DNS-zónája ( `privatelink.queue.core.windows.net` ):

1. Navigáljon a privát végponthoz a **privát kapcsolati központban**. Az áttekintő lap felsorolja a privát végpont teljes tartománynevét és magánhálózati IP-címeit.

1. Adjon hozzá egy bejegyzést a teljes tartománynév és a magánhálózati IP-cím típusú rekordként.

    ![Adja hozzá a teljes tartománynév és a magánhálózati IP-cím bejegyzést a Queue szolgáltatás](./media/private-endpoints/add-type-a-record-for-queue.png)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

K. Létrehozhatok privát végpontot egy meglévő Backup-tárolóhoz?<br>
A. Nem, a saját végpontok csak az új biztonsági mentési tárolók számára hozhatók létre. Így a tár nem rendelkezhetett olyan elemmel, amely védett volt. Valójában a privát végpontok létrehozása előtt nem lehet a tárolóhoz tartozó elemeket védelemmel ellátni.

K. Megpróbáltam védelemmel ellátni egy elemet a tárolóban, de nem sikerült, és a tár továbbra sem tartalmaz védett elemeket. Létrehozhatok privát végpontokat ehhez a tárolóhoz?<br>
A. Nem, a tár nem rendelkezhet semmilyen, a múltbeli elemek elleni védelemmel kapcsolatos kísérlettel.

K. Van egy olyan tároló, amely privát végpontokat használ a biztonsági mentéshez és a visszaállításhoz. Később is Hozzáadhatok vagy eltávolíthatok privát végpontokat ehhez a tárolóhoz, még akkor is, ha a biztonsági másolati elemek védettek?<br>
A. Igen. Ha már létrehozott privát végpontokat egy tárolóhoz, és védett biztonsági másolati elemeket hoz létre, akkor később szükség szerint hozzáadhat vagy eltávolíthat privát végpontokat.

K. Használható-e a Azure Backup privát végpontja a Azure Site Recoveryhoz is?<br>
A. Nem, a biztonsági mentéshez használt magánhálózati végpont csak Azure Backup használható. Ha a szolgáltatás támogatja, létre kell hoznia egy új privát végpontot a Azure Site Recoveryhoz.

K. Kihagytam a cikkben szereplő lépések egyikét, és folytattam az adatforrások védelme érdekében. Továbbra is használhatok privát végpontokat?<br>
A. Nem követi a cikkben szereplő lépéseket, és az elemek védelemmel való ellátása azt eredményezheti, hogy a tár nem tudja használni a privát végpontokat. Ezért javasoljuk, hogy tekintse meg ezt az ellenőrzőlistát, mielőtt továbblép az elemek védelemmel.

K. Használhatom a saját DNS-kiszolgálót az Azure saját DNS-zóna vagy egy integrált magánhálózati DNS-zóna használata helyett?<br>
A. Igen, használhatja a saját DNS-kiszolgálóit. Azonban győződjön meg arról, hogy az összes szükséges DNS-rekord hozzá van adva az ebben a részben javasolt módon.

K. Kell-e további lépéseket végrehajtani a kiszolgálón, miután követtem a jelen cikkben leírt eljárást?<br>
A. A cikkben részletezett folyamat után nem kell további munkát végeznie a privát végpontok használatához a biztonsági mentéshez és a visszaállításhoz.

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Backup összes biztonsági szolgáltatásáról](security-overview.md)
