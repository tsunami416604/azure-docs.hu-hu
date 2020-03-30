---
title: Regisztrált kiszolgálók kezelése az Azure File Sync segítségével | Microsoft dokumentumok
description: Megtudhatja, hogyan regisztrálhat és vonhat le windows servert egy Azure File Sync Storage Sync Service szolgáltatással.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2656716560b981481273c3032fc0c7b1a06be8a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255092"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Regisztrált kiszolgálók kezelése az Azure File Sync segítségével
Az Azure File Sync lehetővé teszi a vállalat Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. Ezt úgy éri el, hogy a Windows-kiszolgálókat az Azure-fájlmegosztás gyors gyorsítótárává alakítja. A Windows Server rendszeren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl. SMB, NFS vagy FTPS), és annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A következő cikk bemutatja, hogyan regisztrálhat és kezelhet kiszolgálót egy tárolószinkronizálási szolgáltatással. Az Azure File Sync központi [telepítéséről](storage-sync-files-deployment-guide.md) további információt talál az Azure File Sync végpontok között történő központi telepítéséről.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Kiszolgáló regisztrálása/regisztrációjának megszüntetése a Storage Sync Szolgáltatással
Kiszolgáló regisztrálása az Azure File Sync használatával bizalmi kapcsolatot hoz létre a Windows Server és az Azure között. Ez a kapcsolat ezután *kiszolgálói végpontok* létrehozására használható a kiszolgálón, amelyek olyan mappákat jelölnek, amelyeket egy Azure-fájlmegosztással (más néven felhővégpontokkal) kell *szinkronizálni.* 

### <a name="prerequisites"></a>Előfeltételek
Ahhoz, hogy kiszolgálót regisztrálhasson egy storage-szinkronizálási szolgáltatással, először először elő kell készítenie a kiszolgálót a szükséges előfeltételekkel:

* A kiszolgálónak a Windows Server támogatott verzióját kell futtatnia. További információ: [Azure File Sync rendszerkövetelmények és az interoperabilitás](storage-sync-files-planning.md#windows-file-server-considerations).
* Győződjön meg arról, hogy egy tárolószinkronizálási szolgáltatás telepítve van. A Storage Sync Service üzembe helyezéséről az [Azure File Sync telepítése](storage-sync-files-deployment-guide.md)című témakörben talál további információt.
* Győződjön meg arról, hogy a kiszolgáló csatlakozik az internethez, és hogy az Azure elérhető.
* Tiltsa le az IE fokozott biztonsági konfigurációját a rendszergazdák számára a Kiszolgálókezelő felhasználói felületén.
    
    ![A Kiszolgálókezelő felhasználói felülete kiemelt, kiemelt IE-biztonsági beállításokkal](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Győződjön meg arról, hogy az Azure PowerShell-modul telepítve van a kiszolgálón. Ha a kiszolgáló egy feladatátvevő fürt tagja, a fürt minden csomópontjára szükség lesz az Az modulra. Az Az modul telepítésével kapcsolatos további részletek az [Azure PowerShell telepítése és konfigurálása című](https://docs.microsoft.com/powershell/azure/install-Az-ps)témakörben találhatók.

    > [!Note]  
    > Javasoljuk, hogy az Az PowerShell modul legújabb verzióját használja a kiszolgáló regisztrálásához/regisztrációjának leiratkozásához. Ha az Az csomagot korábban telepítették erre a kiszolgálóra (és a PowerShell-verzió `Update-Module` ezen a kiszolgálón 5.* vagy nagyobb), a parancsmag segítségével frissítheti ezt a csomagot. 
* Ha a környezetben hálózati proxykiszolgálót használ, konfigurálja a kiszolgáló proxybeállításait a szinkronizálási ügynök számára.
    1. A proxy IP-címének és portszámának meghatározása
    2. A két fájl szerkesztése:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Adja hozzá az 1.
    4. A WinHTTP-proxybeállítások beállítása parancssorból:
        * A proxy megjelenítése: netsh winhttp show proxy
        * Állítsa be a proxy: netsh winhttp set proxy 127.0.0.1:8888
        * Állítsa alaphelyzetbe a proxyt: netsh winhttp reset proxy
        * ha ez a beállítás után az ügynök telepítése, majd indítsa újra a szinkronizálási ügynök: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Kiszolgáló regisztrálása a Storage Sync Szolgáltatással
Ahhoz, hogy egy kiszolgáló *t szolgálvégpontként* használható legyen egy Azure File Sync *szinkronizálási csoportban,* regisztrálnia kell a *Storage Sync Szolgáltatásban.* A kiszolgáló egyszerre csak egy tárolószinkronizálási szolgáltatással regisztrálható.

#### <a name="install-the-azure-file-sync-agent"></a>Az Azure File Sync-ügynök telepítése
1. [Töltse le az Azure File Sync ügynököt.](https://go.microsoft.com/fwlink/?linkid=858257)
2. Indítsa el az Azure File Sync ügynök telepítője.
    
    ![Az Azure File Sync ügynök telepítőjének első ablaktáblája](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Győződjön meg arról, hogy engedélyezi az Azure File Sync ügynök frissítéseit a Microsoft Update használatával. Ez azért fontos, mert a kiszolgálócsomag kritikus biztonsági javításait és funkciófejlesztéseit a Microsoft Update webhelyen keresztül szállítják.

    ![Annak ellenőrzése, hogy a Microsoft Update engedélyezve van-e az Azure File Sync ügynöktelepítő Microsoft Update ablaktáblájában](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Ha a kiszolgáló még nincs regisztrálva, a kiszolgáló regisztrációs felhasználói felülete a telepítés befejezése után azonnal megjelenik.

> [!Important]  
> Ha a kiszolgáló egy feladatátvevő fürt tagja, az Azure File Sync ügynök kell telepíteni a fürt minden csomópontjára.

#### <a name="register-the-server-using-the-server-registration-ui"></a>A kiszolgáló regisztrálása a kiszolgáló regisztrációs felhasználói felületén
> [!Important]  
> A felhőszolgáltató (CSP) előfizetései nem használhatják a kiszolgáló regisztrációs felhasználói felületét. Ehelyett használja a PowerShellt (ebben a szakaszban).

1. Ha a kiszolgáló regisztrációs felhasználói felülete nem indult el közvetlenül az Azure File Sync `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`ügynök telepítésének befejezése után, manuálisan is elindítható a végrehajtásával.
2. Az *Azure-előfizetés* eléréséhez kattintson a Bejelentkezés gombra. 

    ![A kiszolgáló regisztrációs felhasználói felületének megnyitása](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Válassza ki a megfelelő előfizetést, erőforráscsoportot és tárolószinkronizálási szolgáltatást a párbeszédpanelen.

    ![A tárolószinkronizálási szolgáltatás adatai](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. Előzetes verzióban még egy bejelentkezés szükséges a folyamat befejezéséhez. 

    ![Bejelentkezési párbeszédpanel](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Ha a kiszolgáló egy feladatátvevő fürt tagja, minden kiszolgálónak futtatnia kell a kiszolgáló regisztrációját. Amikor megtekinti a regisztrált kiszolgálókat az Azure Portalon, az Azure File Sync automatikusan felismeri az egyes csomópontokat ugyanannak a feladatátvételi fürtnek a tagjaként, és megfelelően csoportosítja őket.

#### <a name="register-the-server-with-powershell"></a>A kiszolgáló regisztrálása a PowerShell segítségével
A kiszolgáló regisztrációját a PowerShellen keresztül is végrehajthatja. Ez az egyetlen támogatott módja a kiszolgálóregisztrációnak a felhőszolgáltatói (CSP) előfizetésekhez:

```powershell
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>A kiszolgáló regisztrációjának megszüntetése a Storage Sync Szolgáltatással
A kiszolgáló tárolószinkronizálási szolgáltatással való regisztrációjának levételének megszüntetése több lépésből áll. Vessünk egy pillantást, hogyan lehet megfelelően törölni a szerver.

> [!Warning]  
> Ne próbálja meg elhárítani a szinkronizálási, felhőszintű rétegezéssel vagy az Azure File Sync bármely más aspektusával kapcsolatos problémákat a kiszolgáló regisztrációjának megszüntetésével és regisztrálásával, illetve a kiszolgálóvégpontok eltávolításával és újbóli létrehozásával, kivéve, ha a Microsoft egyik mérnöke kifejezetten erre utasítja. A kiszolgáló regisztrációjának megszüntetése és a kiszolgálóvégpontok eltávolítása romboló művelet, és a kiszolgálói végpontokat tartalmazó köteteken lévő rétegzett fájlok nem lesznek "újracsatlakoztatva" az Azure-fájlmegosztáson lévő helyükre, miután a regisztrált kiszolgáló- és kiszolgálóvégpontok újra létre, ami szinkronizálási hibákat eredményez. Azt is vegye figyelembe, hogy a kiszolgálóvégpont-névtéren kívül létező rétegzett fájlok véglegesen elveszhetnek. Rétegzett fájlok létezhetnek a kiszolgálóvégpontokon belül, még akkor is, ha a felhőrétegezés soha nem volt engedélyezve.

#### <a name="optional-recall-all-tiered-data"></a>(Nem kötelező) Az összes rétegzett adat visszahívása
Ha azt szeretné, hogy az Azure File Sync eltávolítása után a jelenleg rétegzett fájlok elérhetők legyenek (azaz ez egy éles, nem pedig teszt, környezet), hívja vissza az egyes köteteken lévő összes fájlt, amely kiszolgálói végpontokat tartalmaz. Tiltsa le az összes kiszolgálóvégpont felhőrétegezését, majd futtassa a következő PowerShell-parancsmackét:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Ha a kiszolgálóvégpontot tároló helyi kötetnek nincs elég szabad területe az `Invoke-StorageSyncFileRecall` összes rétegzett adat visszahívásához, a parancsmag sikertelen lesz.  

#### <a name="remove-the-server-from-all-sync-groups"></a>A kiszolgáló eltávolítása az összes szinkronizálási csoportból
A tárolószinkronizálási szolgáltatás kiszolgálójának regisztrációjának megszüntetése előtt a kiszolgáló összes végpontját el kell távolítani. Ez az Azure portalon keresztül végezhető el:

1. Nyissa meg azt a tárolószinkronizálási szolgáltatást, ahol a kiszolgáló regisztrálva van.
2. Távolítsa el a kiszolgáló összes végpontját a Storage Sync Szolgáltatás minden szinkronizálási csoportjában. Ez úgy valósítható meg, hogy a szinkronizálási csoport ablaktáblájában a jobb gombbal a megfelelő kiszolgálóvégpontra kattint.

    ![Kiszolgálóvégpont eltávolítása szinkronizálási csoportból](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Ez egy egyszerű PowerShell-parancsfájllal is elvégezhető:

```powershell
Connect-AzAccount

$storageSyncServiceName = "<your-storage-sync-service>"
$resourceGroup = "<your-resource-group>"

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $storageSyncServiceName | ForEach-Object { 
    $syncGroup = $_; 
    Get-AzStorageSyncServerEndpoint -ParentObject $syncGroup | Where-Object { $_.ServerEndpointName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzStorageSyncServerEndpoint -InputObject $_ 
    } 
}
```

#### <a name="unregister-the-server"></a>A kiszolgáló regisztrációjának megszüntetése
Most, hogy az összes adatot visszahívták, és a kiszolgálót eltávolították az összes szinkronizálási csoportból, a kiszolgáló nem regisztrálható. 

1. Az Azure Portalon keresse meg a Storage Sync Szolgáltatás *Regisztrált kiszolgálók* szakaszát.
2. Kattintson a jobb gombbal a regisztrációról törölni kívánt kiszolgálóra, és kattintson a "Kiszolgáló regisztrációjának megszüntetése" parancsra.

    ![Kiszolgáló regisztrációjának megszüntetése](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Annak biztosítása, hogy az Azure File Sync jó szomszéd legyen az adatközpontban 
Mivel az Azure File Sync ritkán lesz az egyetlen szolgáltatás fut az adatközpontban, érdemes korlátozni az Azure File Sync hálózati és tárolási használatát.

> [!Important]  
> A korlátok túl alacsony beállítása hatással lesz az Azure File Sync szinkronizálási és visszahívási teljesítményére.

### <a name="set-azure-file-sync-network-limits"></a>Az Azure File Sync hálózati korlátainak beállítása
Az Azure File Sync hálózati kihasználtságát a `StorageSyncNetworkLimit` parancsmagok használatával szabályozhatja.

> [!Note]  
> A hálózati korlátok nem érvényesek, ha rétegzett fájlhoz ér keresztül, vagy az Invoke-StorageSyncFileRecall parancsmag használatos.

Létrehozhat például egy új szabályozási korlátot annak biztosítására, hogy az Azure File Sync ne használjon 10 Mb/s-nál többet 9 és 17:00 óra között a munkahét során: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

A korlátot a következő parancsmag segítségével láthatja:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

A hálózati korlátok `Remove-StorageSyncNetworkLimit`eltávolításához használja a használatát. A következő parancs például eltávolítja az összes hálózati korlátot:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>A Windows Server storage QoS használata 
Ha az Azure File Sync egy Windows Server virtualizálási gazdagépen futó virtuális gépen található, a Storage QoS (a szolgáltatás tárolási minősége) használatával szabályozhatja a tárolási I/O-felhasználást. A Storage QoS-házirend beállítható maximumként (vagy korlátként, például a storageSyncnetwork korlát fenti kényszerítésének módjában), vagy legalább (vagy foglalásként). A minimum beállítása a maximális érték helyett lehetővé teszi, hogy az Azure File Sync burst a rendelkezésre álló tárolási sávszélesség, ha más számítási feladatok nem használják. További információ: [Storage Quality of Service](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Lásd még
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
- [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
- [Az Azure File Sync hibaelhárítása](storage-sync-files-troubleshoot.md)
