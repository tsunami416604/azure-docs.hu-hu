# <a name="persist-files-in-azure-cloud-shell"></a>Azure Cloud rendszerhéj fájlok megőrzése
Felhő rendszerhéj használja az Azure File storage fájlok megőrizni a munkamenetek között.

## <a name="set-up-a-clouddrive-file-share"></a>Clouddrive fájlmegosztás beállítása
A kezdeti kezdőlapon a felhő rendszerhéj kéri, hogy társítson egy új vagy meglévő fájlmegosztást fájlok megőrizni a munkamenetek között.

> [!NOTE]
> Bash és PowerShell ossza meg ugyanazt a fájlmegosztást. Lehet, hogy csak egy fájlmegosztás automatikus csatlakoztatását a felhő rendszerhéj társítani.

### <a name="create-new-storage"></a>Új tároló létrehozása

Ha alapszintű beállításokat alkalmazza, és válassza ki az előfizetés csak, felhőalapú rendszerhéj az Ön nevében, akkor a legközelebb esik a támogatott régióban hoz létre három erőforrásokat:
* Erőforráscsoport:`cloud-shell-storage-<region>`
* Storage-fiók:`cs<uniqueGuid>`
* Fájlmegosztás:`cs-<user>-<domain>-com-<uniqueGuid>`

![Az előfizetési beállítás](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

A fájlmegosztás csatlakoztatja `clouddrive` a a `$Home` könyvtár. Ez egy egyszeri művelet, és a további munkamenetekhez automatikusan csatlakoztatja a fájlmegosztást. 

A Bash, a fájlmegosztás is tartalmaz egy 5 GB-os lemezképnek, amely automatikusan jön létre, amelyek továbbra is fennáll az adatok a `$Home` könyvtár. 

### <a name="use-existing-resources"></a>Létező erőforrásokból

A speciális beállítás használatával társíthatja a meglévő erőforrásokat. Ha a tárolási telepítő parancssor jelenik meg, válassza ki a **megjelenítése speciális beállítások** további beállítások. A legördülő menüből a hozzárendelt felhő rendszerhéj-régió, valamint helyileg redundáns tárolás és georedundáns tárolás fiókok szűrve.

A Bash, meglévő fájlmegosztások megjelenik egy 5 GB-os lemezképet hozott létre az Ön megőrizni a `$Home` könyvtár.

![Az erőforrás-csoport beállítás](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Erőforrás létrehozása az Azure-erőforrás házirendjével korlátozása
A felhő rendszerhéj létrehozott tárfiókok vannak látva `ms-resource-usage:azure-cloud-shell`. Ha azt szeretné, hogy a storage-fiókok létrehozása a felhő rendszerhéj letilthatja, hozzon létre egy [Azure-erőforrás házirend címkék](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags) , amely váltja ki ezt a címkét.

## <a name="supported-storage-regions"></a>Támogatott tárolási régiók
Kapcsolódó az Azure storage-fiókok és a felhő rendszerhéj gépet, hogy van-e csatlakoztatni őket ugyanabban a régióban kell lennie.

A hozzárendelt régióban található, lehetséges, hogy:
* A "speciális tárolási beállítások" párbeszédpanelen feljegyzés megtekintése
* Tekintse meg a tárfiókot hozott létre az Ön nevét (pl.: `cloud-shell-storage-westus`)
* Futtatás `env` , és keresse meg a változó`ACC_LOCATION`

Felhő rendszerhéj gép létezik a következő régióban:
|Terület|Régió|
|---|---|
|Amerika|USA keleti régiója, USA déli középső RÉGIÓJA, USA nyugati régiója|
|Európa|Észak-Európa, Nyugat-Európa|
|Ázsia és a Csendes-óceáni térség|India központi, Délkelet-Ázsia|

