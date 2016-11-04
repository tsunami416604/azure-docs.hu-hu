## <a name="overview"></a>Áttekintés
Az Azure File storage egy felhőalapú fájlmegosztást kínáló, [SMB protokollt](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) használó szolgáltatás. Az SMB 2.1 és az SMB 3.0 protokollt is támogatja. Az Azure File Storage szolgáltatással költséges újraírások nélkül, gyorsan megoldható a fájlmegosztásra támaszkodó, régi típusú alkalmazások áttelepítése az Azure-ra. Az Azure virtuális gépeken vagy felhőszolgáltatásában, esetleg helyszíni ügyfeleken üzemelő alkalmazások fájlmegosztást csatlakoztathatnak a felhőben. Ez a megosztás hasonló ahhoz a csatlakoztatott SMB-megosztáshoz, amelyet az asztali alkalmazások használnak. Ezután bármennyi alkalmazás-összetevő egyszerre csatlakoztathatja a File Storage-megosztást, és hozzá is férhet.

Mivel a File Storage-megosztás egy szabványos SMB-fájlmegosztás, az Azure-ban futó alkalmazások a fájlrendszer adatátviteli API-jain keresztül férhetnek hozzá a megosztás adataihoz. A fejlesztők épp ezért meglévő kódjaik és képességeik felhasználásával áttelepíthetik az alkalmazásokat. Az informatikai szakemberek az Azure-alkalmazások rendszergazdai feladatainak részeként PowerShell-parancsmagokkal hozhatják létre, csatlakoztathatják és kezelhetik a File Storage-megosztásokat.

Azure fájlmegosztásokat létrehozhat az [Azure Portalon](https://portal.azure.com), az Azure Storage PowerShell parancsmagjainak segítségével, illetve az Azure Storage ügyfélkódtáraival vagy az Azure Storage REST API-val. Emellett, mivel a fájlmegosztások SMB-megosztások, azokhoz szabványos, ismert fájlrendszer API-kkal is hozzáférhet.

<!--HONumber=Oct16_HO3-->


