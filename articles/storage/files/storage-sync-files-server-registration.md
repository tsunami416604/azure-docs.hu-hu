---
title: Az Azure File Sync használatával regisztrált kiszolgálók kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan lehet regisztrálni, és egy Windows Server az Azure File Sync Társzinkronizálási szolgáltatás törlése.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 468bd70682b1b36e906d32cd7bde58c78bdbb376
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521999"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Az Azure File Sync használatával regisztrált kiszolgálók kezelése
Az Azure File Sync lehetővé teszi a vállalat Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. Ezt nem átalakításával keletkező a Windows-kiszolgálók az Azure-fájlmegosztás gyors gyorsítótáraivá. A Windows Server rendszeren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl. SMB, NFS vagy FTPS), és annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A következő cikk azt ábrázolja, hogyan regisztrálja, és a Storage Sync Service a kiszolgáló kezeléséhez. Lásd: [üzembe helyezése az Azure File Sync](storage-sync-files-deployment-guide.md) üzembe helyezése az Azure File Sync-teljes körű tájékoztatást.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Regisztráció és a kiszolgáló regisztrációját az Társzinkronizálási szolgáltatás
A kiszolgáló regisztrálása az Azure File Sync használatával hoz létre a Windows Server és Azure közötti megbízhatósági kapcsolat. Ez a kapcsolat majd létrehozásához használható *kiszolgálói végpontot* a kiszolgálón, amely képviseli az Azure-fájlmegosztások szinkronizálandó meghatározott mappákról (más néven egy *felhőbeli végpont*). 

### <a name="prerequisites"></a>Előfeltételek
A Társzinkronizálási szolgáltatás regisztrálja a kiszolgálót, elő kell készítenie a kiszolgáló és az a szükséges előfeltételek:

* A kiszolgáló Windows Server támogatott verziójának kell futnia. További információkért lásd: [a Windows Server támogatott verziója](storage-sync-files-planning.md#supported-versions-of-windows-server).
* Győződjön meg arról, hogy a Társzinkronizálási szolgáltatás van telepítve. A Storage Sync Service telepítésének módjáról további információkért lásd: [üzembe helyezése az Azure File Sync](storage-sync-files-deployment-guide.md).
* Győződjön meg arról, hogy a kiszolgáló csatlakozik-e az internethez, hogy az Azure érhető el.
* Tiltsa le az Internet Explorer – fokozott biztonsági beállításai a rendszergazdák számára a Server Manager felhasználói felületén.
    
    ![A Kiszolgálókezelő felhasználói felület az Internet Explorer – fokozott biztonsági beállításai vannak kiemelve](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Győződjön meg arról, hogy az AzureRM PowerShell-modul telepítve van-e a kiszolgálón. Ha a kiszolgáló egy feladatátvevő fürt tagja, a fürt minden csomópontján kell az AzureRM-modul. Az AzureRM-modul telepítésével kapcsolatos további részletek találhatók a [Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

    > [!Note]  
    > A kiszolgáló regisztrálása vagy regisztrációjának törlése az AzureRM PowerShell-modul legújabb verziójának használatát javasoljuk. Ha az AzureRM-csomag korábban már telepítve van ezen a kiszolgálón (5.* pedig az ezen a kiszolgálón a PowerShell-verzió vagy újabb), használhatja a `Update-Module` parancsmagot, hogy ez a csomag frissítése. 
* Ha hálózati proxykiszolgálót a környezetében, a Proxybeállítások konfigurálása a kiszolgálón a szinkronizálási ügynök vehető igénybe.
    1. Határozza meg a proxykiszolgáló IP-cím és port számát
    2. Módosítsa a két fájlt:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Adja hozzá a sorokat (alatt ez a szakasz) 1. ábra /System.ServiceModel 127.0.0.1:8888 helyes IP-címet (cserélje le a 127.0.0.1) és a megfelelő port számát (cserélje le a 8888) módosítása a fenti két fájl alapján:
    4. Állítsa be a WinHTTP-proxybeállítások parancssor segítségével:
        * A proxy megjelenítése: netsh winhttp proxy megjelenítése
        * Állítsa be a proxy: netsh winhttp proxy 127.0.0.1:8888 beállítása
        * A proxy alaphelyzetbe állítása: netsh winhttp proxy alaphelyzetbe állítása
        * Ha a telepítő az ügynök telepítése után, majd indítsa újra a szinkronizálási ügynök: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Kiszolgáló regisztrálása a Társzinkronizálási szolgáltatás
A kiszolgáló használhatók legyenek, mint egy *kiszolgálói végpont* a az Azure File Sync *szinkronizálási csoport*, regisztrálni kell az egy *Társzinkronizálási szolgáltatás*. A kiszolgáló csak regisztrálhatók egy Társzinkronizálási szolgáltatást egyszerre.

#### <a name="install-the-azure-file-sync-agent"></a>Az Azure File Sync ügynök telepítése
1. [Töltse le az Azure File Sync ügynök](https://go.microsoft.com/fwlink/?linkid=858257).
2. Indítsa el az Azure File Sync ügynök telepítőjét.
    
    ![Az Azure File Sync ügynök telepítőjének első oldalán](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Győződjön meg arról, hogy lehetővé teszi az Azure File Sync ügynök, a Microsoft Update használata frissítések. Nagyon fontos, mert a kritikus fontosságú biztonsági javításokat tartalmaz, és a Microsoft Update-en keresztül fejlesztést tartalmaz a kiszolgáló csomag tartalmazza a szükséges.

    ![Győződjön meg arról, a Microsoft Update ablaktáblán, az Azure File Sync ügynök telepítőjének engedélyezve van a Microsoft Update](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Ha a kiszolgáló nem korábban regisztrálva lett, a kiszolgáló regisztrálása felhasználói felületén jelenik meg azonnal a telepítés befejezése után.

> [!Important]  
> Ha a kiszolgáló egy feladatátvevő fürt tagja, az Azure File Sync ügynök a fürt minden csomópontján telepíteni kell.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Regisztrálja a kiszolgálót, a kiszolgáló regisztrálása felhasználói felület használatával
> [!Important]  
> Cloud Solution Provider (CSP) előfizetések nem használható a kiszolgáló regisztrálása felhasználói felületén. Ehelyett használja a PowerShell (ebben a szakaszban).

1. Ha a kiszolgáló regisztrálása felhasználói felület nem indult el közvetlenül az Azure File Sync ügynök a telepítés befejezése után, amely segítségével elindítható manuálisan végrehajtásával `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Kattintson a *bejelentkezési* az Azure-előfizetése eléréséhez. 

    ![A kiszolgáló regisztrációs felhasználói felület párbeszédpanel megnyitása](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Válassza ki a megfelelő előfizetés, erőforráscsoport és a Storage Sync Service a párbeszédpanelről.

    ![Tárolási szinkronizálási szolgáltatás adatai](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. Előzetes verzióban elérhető egy további bejelentkezési szükséges a folyamat befejezéséhez. 

    ![Jelentkezzen be a párbeszédpanelt](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Ha a kiszolgáló egy feladatátvevő fürt tagja, mindegyik kiszolgálón kell futtatni a kiszolgáló regisztrálása. Az Azure Portalon megtekintheti a regisztrált kiszolgálókat, az Azure File Sync automatikusan felismeri a csomópontok egy feladatátvevő fürt összes tagja és összevonja azokat megfelelően.

#### <a name="register-the-server-with-powershell"></a>Regisztrálja a kiszolgálót a PowerShell-lel
Kiszolgáló regisztrálása a PowerShell használatával is elvégezheti. Ez a kiszolgáló regisztrálása a Cloud Solution Provider (CSP) előfizetésekhez az egyetlen támogatott módja:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzureRmStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>A Storage Sync Service a kiszolgáló regisztrációjának törlése
Nincsenek számos lépést, a kiszolgáló regisztrációját az Társzinkronizálási szolgáltatás szükséges. Vessünk egy pillantást a kiszolgáló megfelelően kiszolgálóregisztráció.

> [!Warning]  
> Ne kísérelje meg regisztrációjának törlése és a egy kiszolgáló regisztrálása vagy eltávolításával és újbóli létrehozása a kiszolgálói végpontot, kivéve, ha explicit módon tulajdonosaitól egy Microsoft-mérnök szinkronizálási, felhőbeli rétegezés vagy bármilyen más szempont az Azure File Sync hibáinak elhárítása. A kiszolgáló regisztrációjának törlése és eltávolítása a kiszolgálói végpontot egy destruktív művelet, és a kiszolgáló-végponttal rendelkező köteteken rétegzett fájlok nem "újra létrehozza" az Azure-fájlmegosztás a helyükre után a regisztrált kiszolgáló és a kiszolgálói végpontot újból, és emiatt szinkronban hibákat. Azt is vegye figyelembe, előfordulhat, hogy a rétegzett fájlokhoz, amely a kiszolgálói végpont névtér kívüliek véglegesen elvesztek. Előfordulhat, hogy létezik a rétegzett fájlok kiszolgálón belüli végpontok felhőrétegzési még akkor is, ha még sosem engedélyezték.

#### <a name="optional-recall-all-tiered-data"></a>(Nem kötelező) Összes rétegzett adat visszahívása
Ha szeretné a jelenleg elérhető legyen az Azure File Sync (azaz nem egy éles környezetben, nem a tesztelési, környezet) eltávolítása után számítógépen rétegzett fájlok, ne felejtse el minden egyes kiszolgálói végpontot tartalmazó köteten lévő összes fájlt. Tiltsa le a felhőbeli rétegezés az összes kiszolgálói végpontot, és futtassa a következő PowerShell-parancsmagot:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Ha a helyi kötetet, a kiszolgálói végpontot üzemeltető nincs elég szabad hely összes rétegzett adat előhívja a `Invoke-StorageSyncFileRecall` parancsmag sikertelen lesz.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Kiszolgáló eltávolítása az összes szinkronizálási csoportot
A Storage Sync Service a kiszolgáló regisztrációjának törlése, mielőtt az összes kiszolgálói végpontot, az adott kiszolgálón el kell távolítani. Ez az Azure Portalon teheti meg:

1. Keresse meg a Storage Sync Service, ahol a kiszolgáló regisztrálva van-e.
2. A Storage Sync Service az összes szinkronizálási csoportból távolítsa el a kiszolgáló összes kiszolgálói végpontot. A kattintson a jobb gombbal a megfelelő kiszolgálói végpontot, a szinkronizálási csoport panelen kell elvégezni.

    ![Kiszolgálói végpont eltávolítása a szinkronizálási csoport](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Ez egy egyszerű PowerShell-parancsprogrammal is elvégezhető:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Connect-AzureRmAccount
Login-AzureRmStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

#### <a name="unregister-the-server"></a>A kiszolgáló regisztrációjának törlése
Most, hogy minden adat rendelkezik lett idézni, és a kiszolgáló összes szinkronizálási csoportot eltávolították, a kiszolgáló regisztrációjának törlése lehet. 

1. Az Azure Portalon lépjen a *regisztrált kiszolgálókat* a Storage Sync Service szakaszában.
2. Kattintson a jobb gombbal a kiszolgáló regisztrációjának törlése, és kattintson a "Kiszolgáló regisztrációjának törlése".

    ![Kiszolgáló regisztrációjának törlése](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Biztosítása az Azure File Sync jóban a szomszédokkal. a helyi adatközpontban 
Mivel az Azure File Sync ritkán lesz a saját adatközpontjában futó egyetlen szolgáltatás, előfordulhat, hogy szeretné korlátozni a hálózati és storage-használata az Azure File Sync.

> [!Important]  
> Túl alacsony korlátok beállítása az Azure File Sync szinkronizálási és visszaírási teljesítményét negatív hatással lesz.

### <a name="set-azure-file-sync-network-limits"></a>Az Azure File Sync hálózati vonatkozó korlátok beállítása
Képes szabályozni a a hálózati kihasználtság az Azure File Sync használatával a `StorageSyncNetworkLimit` parancsmagok. 

Például létrehozhat egy új szabályozási korlát, győződjön meg arról, hogy az Azure File Sync nem használja a több mint 10 MB/s közötti 9: 00 és 17: 00 (17:00 óra) a munkahét során: 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

A korlát a következő parancsmag használatával tekintheti meg:

```PowerShell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Hálózati korlátozások eltávolításához használja `Remove-StorageSyncNetworkLimit`. Például a következő parancs eltávolítja az összes hálózati korlátai:

```PowerShell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>A Windows Server tárolási QoS használata 
Az Azure File Sync egy Windows Server virtualization gazdagépen futó virtuális gépen jöhet szóba, ha a tárolási szolgáltatásminőség (tárolási szolgáltatásminőség) segítségével tárolási i/o-használat szabályozását. A tárolási QoS-házirend állítható, a maximális (vagy a korlátot, például hogyan tartatja be StorageSyncNetwork korlát feletti) vagy a minimális (vagy a foglalás). Legalább egy maximális helyett beállítása lehetővé teszi, hogy a folyamatosan rendelkezésre álló tárolási sávszélességet használja, ha más munkaterhelések nem használják az Azure File Sync. További információkért lásd: [tárolási szolgáltatásminőség](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Lásd még
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md) 
- [Az Azure File Sync hibaelhárítása](storage-sync-files-troubleshoot.md)