## <a name="transfer-local-files-to-cloud-shell"></a>Helyi fájlok átvitele a felhő rendszerhéj
A `clouddrive` directory szinkronizál az Azure portál tárolás panel. Ezen a panelen vihet át a helyi fájlokat vagy a fájlmegosztásból. Felhő rendszerhéj található fájlokat frissítése is megjelenik a file storage grafikus felhasználói Felülettel amikor frissíteni a panelt.

### <a name="download-files"></a>Fájlok letöltése

![A helyi fájlok listája](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. Az Azure-portálon lépjen a csatlakoztatott fájlmegosztáshoz.
2. Válassza ki a cél-fájlt.
3. Válassza ki a **letöltése** gombra.

### <a name="upload-files"></a>Fájlok feltöltése

![Fel kell tölteni a helyi fájlok](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. Nyissa meg a csatlakoztatott fájlmegosztáshoz.
2. Válassza ki a **feltöltése** gombra.
3. Válassza ki a fájl vagy a feltölteni kívánt fájlokat.
4. Erősítse meg a feltöltést.

Most látnia kell, hogy elérhető-e a fájlokat a `clouddrive` felhő rendszerhéj könyvtárába.