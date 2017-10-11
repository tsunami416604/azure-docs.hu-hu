## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Az IoT hubhoz Azure Storage-fiók társítása

Mivel a szimulált eszköz alkalmazásának feltölt egy fájlt egy blobba, rendelkeznie kell egy [Azure Storage](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account) az IoT hubhoz tartozó fiókot. Egy Azure Storage-fiók társítása az IoT-központ, az IoT hub hoz létre egy SAS URI-t. Egy eszköz a SAS URI segítségével biztonságosan fájl feltöltése a blob-tároló. Az IoT-központ szolgáltatás és az eszköz SDK-k koordinálja a folyamat, amely a SAS URI-t állít elő, és lehetővé teszi az eszközöket, hogy a fájl feltöltéséhez használja.

Kövesse az utasításokat a [fájlfeltöltések konfigurálása az Azure portál használatával](../articles/iot-hub/iot-hub-configure-file-upload.md) rendelje hozzá a az IoT hub az Azure Storage-fiók. Győződjön meg arról, hogy az IoT hub társítva-e a blob-tároló, és hogy engedélyezve vannak-e a fájl értesítések.

![A portál fájl értesítések engedélyezése](media/iot-hub-associate-storage/enable-file-notifications.png)