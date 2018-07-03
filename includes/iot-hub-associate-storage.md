## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Társítsa Azure Storage-fiókot, az IoT hubhoz

Mivel a szimulált eszközalkalmazás feltölt egy fájlt egy blobba, rendelkeznie kell egy [Azure Storage](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account) az IoT hubhoz tartozó fiókot. Ha egy Azure Storage-fiókot társítja az IoT hub, az IoT hub egy SAS URI-t hoz létre. Egy eszköz a SAS URI használatával biztonságosan fájl feltöltése a blob-tárolóba. Az IoT Hub szolgáltatás és az eszköz SDK-kat koordinálja a folyamat, amely létrehozza az SAS URI-t, és lehetővé teszi az eszköz egy fájlt tölthet fel.

Kövesse a [fájlfeltöltések konfigurálása az Azure portal használatával](../articles/iot-hub/iot-hub-configure-file-upload.md) rendelni az Azure Storage-fiókot, az IoT hubnak. Győződjön meg arról, hogy az IoT hub társítva-e a blobtárolót és, hogy a fájl értesítések engedélyezve vannak-e.

![Fájl értesítések engedélyezése a portálon](media/iot-hub-associate-storage/enable-file-notifications.png)