## <a name="what-is-blob-storage"></a>Mi az a Blob storage?
Az Azure Blob Storage szolgáltatás strukturálatlan adatok, például szövegek vagy bináris adatok nagy mennyiségben történő tárolására szolgál, amelyek HTTP- vagy HTTPS-kapcsolaton keresztül a világon bárhonnan elérhetők. A Blob Storage segítségével bárki számára nyilvánosan elérhetővé tehet adatokat, vagy privát módon tárolhat alkalmazásadatokat.

A Blob Storage gyakori használati módjai többek között:

* Képek vagy dokumentumok közvetlenül egy böngésző kiszolgáló.
* Megosztott hozzáférésre fájlok tárolásához.
* Adatfolyam-video- és.
* Adattárolás biztonsági mentés és helyreállításhoz, vészhelyreállításhoz és archiváláshoz.
* Adattárolás elemzés által egy helyszíni vagy Azure által üzemeltetett szolgáltatás.

## <a name="blob-service-concepts"></a>A Blob szolgáltatással kapcsolatos fogalmak
A Blob szolgáltatás az alábbi összetevőkből áll:

![Blob szolgáltatás architektúra ábrája](./media/storage-blob-concepts-include/blob1.png)

* **A tárfiók:** Azure Storage minden hozzáférés a storage-fiók segítségével történik. Ez a tárfiók lehet egy **általános célú tárfiók** vagy egy **Blob storage-fiók**, amely kifejezetten objektumok vagy blobok tárolására. További információk: [Tudnivalók az Azure Storage-fiókokról](../articles/storage/common/storage-create-storage-account.md).
* **Tároló:** A tárolók blobkészletek csoportosítását biztosítják. Az összes blobnak tárolóban kell lennie. Egy fiók korlátlan számú tárolót tartalmazhat. Egy tároló korlátlan számú blob tárolására használható. Vegye figyelembe, hogy a tároló neve csak kisbetűket tartalmazhat.
* **Blob:** Bármilyen típusú és bármekkora méretű fájl. Az Azure Storage háromféle blobot biztosít: blokkblobokat, hozzáfűző blobokat és lapblobokat.
  
    A *blokkblobok* ideálisak szövegek és bináris fájlok, például dokumentumok és médiafájlok tárolására. Egyetlen blokkblob akár 50 000, egyenként 100 MB méretű blokkot is tartalmazhat, a maximális méretük ezért valamivel több mint 4,75 TB (100 MB X 50 000 blokk). 

    A *hozzáfűző blobok* a blokkblobokhoz hasonlóan blokkokból épülnek fel, de műveletek hozzáfűzésére vannak optimalizálva, ezért hasznosak lehetnek a naplózási forgatókönyvekben. Egyetlen hozzáfűző blob akár 50 000, egyenként 4 MB méretű blokkot is tartalmazhat, a maximális méretük ezért valamivel több mint 195 GB (4 MB X 50 000 blokk).
  
    A *lapblobok* akár 1 TB méretűek is lehetnek, és hatékonyabbak a gyakori írási/olvasási műveletek esetén. Az Azure virtuális gépek lapblobokat használnak operációs rendszer és adatlemezek.
  
    Tárolók és blobok elnevezésével kapcsolatos részletekért lásd: [elnevezési és a tárolók, blobok és metaadatok hivatkozó](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).

