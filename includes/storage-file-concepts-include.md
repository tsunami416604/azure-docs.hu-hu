## <a name="what-is-azure-file-storage"></a>Mi az Azure File Storage?
A File Storage közös tárterületet biztosít a standard SMB 2.1 vagy SMB 3.0 protokollt használó alkalmazások számára. A Microsoft Azure virtuális gépek és a felhőalapú szolgáltatások csatlakoztatott megosztásokon keresztül adatokat oszthatnak meg az alkalmazások összetevői között, a helyszíni alkalmazások pedig a File storage API-ján keresztül hozzáférhetnek a megosztott fájladatokhoz.

Az Azure Virtual Machines és a Cloud Services modulban üzemelő alkalmazások File Storage-megosztás csatlakoztatásával hozzáférhetnek a fájlokban tárolt adatokhoz, hasonló módon az asztali alkalmazások által használt jellemző SMB-megosztásokhoz. A File Storage-megosztást tetszőleges számú Azure virtuális gép vagy szerepkör csatlakoztathatja és érheti el egyszerre.

Mivel a File Storage-megosztás egy szabványos, SMB protokollt használó Azure-fájlmegosztás, az Azure-ban futó alkalmazások a fájl adatátviteli API-jain keresztül férhetnek hozzá a megosztás adataihoz. A fejlesztők épp ezért meglévő kódjaik és képességeik felhasználásával áttelepíthetik az alkalmazásokat. Az informatikai szakemberek az Azure-alkalmazások rendszergazdai feladatainak részeként PowerShell-parancsmagokkal hozhatják létre, csatlakoztathatják és kezelhetik a File Storage-megosztásokat. Ez az útmutató mindkét példát bemutatja.

A File Storage gyakori használati módjai többek között:

* A fájlmegosztáson alapuló helyszíni alkalmazások költséges újraírások nélküli áttelepítése az Azure virtuális gépeken vagy felhőszolgáltatásokon való futtatásukhoz
* Megosztott alkalmazásbeállítások tárolása, például konfigurációs fájlokban
* Diagnosztikai adatok, például naplók, metrikák és összeomlási memóriaképek tárolása egy megosztott helyen 
* Azure virtuális gépek vagy felhőszolgáltatások fejlesztéséhez vagy adminisztrációjához szükséges eszközök és segédeszközök tárolása

## <a name="file-storage-concepts"></a>A fájltárolóval kapcsolatos fogalmak
A File Storage a következő összetevőkből áll:

![files-concepts][files-concepts]

* **Tárfiók:** Minden Azure Storage-hozzáférés tárfiókon keresztül valósítható meg. A tárfiókok kapacitásával kapcsolatos további információkért lásd: [Azure Storage Scalability and Performance Targets](../articles/storage/storage-scalability-targets.md) (Az Azure Storage méretezhetőségi és teljesítménycéljai).
* **Megosztás:** A File Storage-megosztás egy SMB-fájlmegosztás az Azure-ban. 
  Minden könyvtárnak és fájlnak egy szülőmegosztásban kell létrejönnie. Egy fiók korlátlan számú megosztást tartalmazhat, egy megosztás pedig korlátlan számú fájl tárolására használható, egészen a fájlmegosztás maximális 5 TB-os kapacitásáig.
* **Könyvtár:** Egy választható könyvtár-hierarchia. 
* **Fájl:** A megosztásban található fájl. Egy fájl akár 1 TB méretű is lehet.
* **URL-formátum:** A fájlok az alábbi URL-formátummal érhetők el:   
  https://`<storage
  account>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`  
  
  Az alábbi minta URL segítségével elérheti a fenti diagram egyik fájlját:  
  `http://samples.file.core.windows.net/logs/CustomLogs/Log1.txt`

A megosztások, könyvtárak és fájlok elnevezésére vonatkozó részleteket lásd: [Naming and Referencing Shares, Directories, Files, and Metadata](http://msdn.microsoft.com/library/azure/dn167011.aspx) (Megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása).

[files-concepts]: ./media/storage-file-concepts-include/files-concepts.png

<!--HONumber=Nov16_HO2-->


