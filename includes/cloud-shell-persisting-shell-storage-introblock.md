# <a name="persist-files-in-azure-cloud-shell"></a>Az Azure Cloud Shellben fájlok megtartása
A cloud Shell az Azure File storage segítségével a fájlok munkamenetek közötti megtartása használja.

## <a name="set-up-a-clouddrive-file-share"></a>Clouddrive fájlmegosztás beállítása
Az első indítás a Cloud Shell kéri, hogy társíthatja egy új vagy meglévő fájlmegosztást a fájlok munkamenetek közötti megtartása.

> [!NOTE]
> A bash és a PowerShell ossza meg ugyanazt a fájlmegosztást. Csak egy fájlmegosztás társítható automatikus csatlakoztatását a Cloud shellben.

### <a name="create-new-storage"></a>Új tároló létrehozása

Ha alapvető beállításokkal, és válasszon ki egy előfizetést csak, a Cloud Shell három erőforrást hoz létre az Ön nevében a legközelebbi támogatott régióban:
* Erőforráscsoport: `cloud-shell-storage-<region>`
* Tárfiók: `cs<uniqueGuid>`
* Fájlmegosztás: `cs-<user>-<domain>-com-<uniqueGuid>`

![Az előfizetés beállítása](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

A fájlmegosztás csatlakoztatja `clouddrive` a a `$Home` könyvtár. Ez egy egyszeri művelet, és minden későbbi munkamenet során az automatikusan csatlakoztatja a fájlmegosztást. 

> [!NOTE]
> A biztonság érdekében minden felhasználó a saját tárfiók kell kiépítenie.  A szerepköralapú hozzáférés-vezérlés (RBAC) a felhasználók kell közreműködői hozzáférés vagy újabb, a storage-fiók szint.

A Bash, a fájlmegosztást is tartalmaz egy 5 GB-os rendszerképet, amely automatikusan jön létre, amely továbbra is fennáll az adatok a `$Home` könyvtár. 

### <a name="use-existing-resources"></a>Meglévő erőforrások használata

A speciális lehetőség használatával társíthatja a meglévő erőforrások. A storage-telepítő használatával megjelenésekor válassza **speciális beállítások megjelenítése** további beállítások megtekintéséhez. A legördülő menükben a hozzárendelt Cloud Shell-régióra és a helyileg redundáns tárolás és a georedundáns tárfiókok vannak szűrve.

Fájlmegosztások kap egy 5 GB-os rendszerképet, megőrizheti a létrehozott a `$Home` könyvtár.

![Az erőforrás-csoport beállításai](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Erőforrás-létrehozás az Azure-erőforrás szabályzatával korlátozása
Storage-fiókokat, akkor a Cloud Shellben hozzon létre megfelelő címkékkel `ms-resource-usage:azure-cloud-shell`. Ha azt szeretné, letilthatja a Cloud shellben hozzon létre a storage-fiókok, hozzon létre egy [Azure erőforrás-szabályzat a címkék](../articles/azure-policy/json-samples.md) , amely az adott címkével által aktivált.

## <a name="supported-storage-regions"></a>Támogatott storage-régiók
Kapcsolódó Azure storage-fiókok és a Cloud Shell-gép, hogy van-e csatlakoztatni őket ugyanabban a régióban kell lennie.

A hozzárendelt régióban található, lehetséges, hogy:
* A megjegyzés megtekintéséhez a "speciális tárolási beállítások" párbeszédpanelen
* Tekintse meg a létrehozott tárfiók neve (például: `cloud-shell-storage-westus`)
* Futtatás `env` , és keresse meg a változó `ACC_LOCATION`

Cloud Shell gépek létezik a következő régióban:
|Terület|Régió|
|---|---|
|Amerika|USA keleti RÉGIÓJA, USA déli középső RÉGIÓJA, USA nyugati RÉGIÓJA|
|Európa|Észak-Európa, Nyugat-Európa|
|Ázsia és a Csendes-óceáni térség|India középső, Délkelet-Ázsia|

