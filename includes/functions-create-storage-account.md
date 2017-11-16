## <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

Funkciók egy általános célú fiókot használja az Azure Storage állapot-és egyéb adatait a funkciók. Hozzon létre egy általános célú tárfiókot az Ön által létrehozott erőforráscsoport a [az storage-fiók létrehozása](/cli/azure/storage/account#create) parancsot.

Az alábbi parancs helyettesítse a globálisan egyedi tárfiók neve válaszoknál láthatja a `<storage_name>` helyőrző. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

A tárfiók létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```