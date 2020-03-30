---
title: Az alkalmazás forrásának titkosítása nyugalmi
description: Titkosítsa az alkalmazásadatokat az Azure Storage-ban, és telepítse csomagfájlként.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 62179e900ace0d6d7b8b1f07e8f0ab685508f991
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408724"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>Titkosítás inaktív ként az ügyfél által kezelt kulcsok használatával

A függvényalkalmazás inaktív alkalmazásadatainak titkosításához egy Azure Storage-fiókra és egy Azure Key Vaultra van szükség. Ezek a szolgáltatások akkor használatosak, amikor az alkalmazást egy központi telepítési csomagból futtatja.

  - [Az Azure Storage nyugalmi titkosítást biztosít.](../storage/common/storage-service-encryption.md) Használhatja a rendszer által biztosított kulcsokat vagy a saját, ügyfél által felügyelt kulcsokat. Ez az a hely, ahol az alkalmazásadatok tárolása, ha nem fut egy függvényalkalmazásban az Azure-ban.
  - [Központi telepítési csomagból való futtatás] ((run-functions-from-deployment-package.md) az App Service központi telepítési szolgáltatása. Lehetővé teszi, hogy a webhely tartalmát egy Azure Storage-fiókból egy SAS-URL-cím használatával telepítse.
  - [A Key Vault-hivatkozások](../app-service/app-service-key-vault-references.md) az App Service biztonsági szolgáltatása. Ez lehetővé teszi, hogy a titkos kulcsok importálása futásidőben alkalmazásbeállításokként. Ezzel titkosíthatja az Azure Storage-fiók SAS URL-címét.

## <a name="set-up-encryption-at-rest"></a>Titkosítás beállítása inaktív helyen

### <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

Először [hozzon létre egy Azure Storage-fiókot,](../storage/common/storage-account-create.md) és [titkosítsa azt az ügyfél által felügyelt kulcsokkal.](../storage/common/encryption-customer-managed-keys.md) A tárfiók létrehozása után az [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) segítségével töltsön fel csomagfájlokat.

Ezután a Storage Explorer segítségével [hozzon létre egy SAS.Next,](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer)use the Storage Explorer to generate a SAS . 

> [!NOTE]
> Mentse ezt a SAS URL-címet, ezt később a központi telepítési csomag futásidőben történő biztonságos elérésének engedélyezésére használjuk.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>A tárfiókból származó csomagból történő futtatás konfigurálása
  
Miután feltöltötte a fájlt a Blob storage-ba, és `WEBSITE_RUN_FROM_PACKAGE` rendelkezik a fájl SAS URL-címével, állítsa be az alkalmazás beállítást a SAS URL-címre. A következő példa az Azure CLI használatával történik:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

Az alkalmazásbeállítás hozzáadása a függvényalkalmazás újraindítását eredményezi. Az alkalmazás újraindítása után tallózással keresse meg, és győződjön meg arról, hogy az alkalmazás megfelelően kezdte el a központi telepítési csomag használatát. Ha az alkalmazás nem indult el megfelelően, olvassa el a [Futtatás a csomagból hibaelhárítási útmutatót.](run-functions-from-deployment-package.md#troubleshooting)

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Az alkalmazásbeállítás titkosítása Key Vault-hivatkozásokkal

Most már lecserélheti `WEBSITE_RUN_FROM_PACKAGE` az alkalmazásbeállítás értékét a SAS-kódolású URL-re mutató Key Vault-hivatkozással. Ez titkosítva tartja a SAS URL-címét a Key Vaultban, amely további biztonsági réteget biztosít.

1. A következő [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) paranccsal hozzon létre egy Key Vault-példányt.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Az [alábbi utasításokat követve hozzáférést adhat az alkalmazásnak](../app-service/app-service-key-vault-references.md#granting-your-app-access-to-key-vault) a kulcstartóhoz:

1. A következő [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) paranccsal titkos kulcsként adhat hozzá külső URL-címet a kulcstartóhoz:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  A következő [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) paranccsal `WEBSITE_RUN_FROM_PACKAGE` hozza létre az alkalmazásbeállítást a külső URL-címre mutató Key Vault-hivatkozásként szereplő értékkel:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    A `<secret-version>` lesz az előző `az keyvault secret set` parancs kimenetében.

Az alkalmazásbeállítás frissítése a függvényalkalmazás újraindítását eredményezi. Az alkalmazás újraindítása után keresse meg, hogy megfelelően kezdte-e el a Key Vault-hivatkozást.

## <a name="how-to-rotate-the-access-token"></a>A hozzáférési jogkivonat elforgatása

Ajánlott rendszeresen forgatni a sas-kulcs a tárfiók. Annak érdekében, hogy a függvényalkalmazás véletlenül ne legyen laza hozzáféréssel, a Key Vaultban frissítenie kell a SAS URL-címét is.

1. Forgassa el a SAS-kulcsot az Azure Portalon a tárfiókra való navigálással. A **Beállítások elérése** > **gombok csoportban**kattintson az ikonra a SAS-kulcs elforgatásához.

1. Másolja az új SAS-URL-címet, és a következő paranccsal állítsa be a frissített SAS-URL-címet a kulcstartóban:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Frissítse a key vault hivatkozás az alkalmazás beállítás az új titkos verzió:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    A `<secret-version>` lesz az előző `az keyvault secret set` parancs kimenetében.

## <a name="how-to-revoke-the-function-apps-data-access"></a>A függvényalkalmazás adathozzáférésének visszavonása

A függvényalkalmazás hozzáférését a tárfiókhoz való hozzáférésének visszavonásához két módszer létezik. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Az Azure Storage-fiók SAS-kulcsának elforgatása

Ha a tárfiók SAS-kulcsa el van forgatva, a függvényalkalmazás már nem lesz hozzáférése a tárfiókhoz, de továbbra is futni fog a csomagfájl utolsó letöltött verziójával. Indítsa újra a függvényalkalmazást az utoljára letöltött verzió törléséhez.

### <a name="remove-the-function-apps-access-to-key-vault"></a>A függvényalkalmazás hozzáférésének eltávolítása a Key Vaulthoz

Visszavonhatja a függvényalkalmazás hozzáférését a hely adatokhoz a függvényalkalmazás hozzáférésének letiltásával a Key Vaulthoz. Ehhez távolítsa el a függvényalkalmazás identitásának hozzáférési szabályzatát. Ez ugyanaz az identitás, amelyet korábban létrehozott a key vault-hivatkozások konfigurálása kor.

## <a name="summary"></a>Összefoglalás

Az alkalmazásfájlok mostantól titkosítva vannak a tárfiókban. Amikor a függvényalkalmazás elindul, lekéri a SAS URL-címét a key vaultból. Végül a függvényalkalmazás betölti az alkalmazásfájlokat a tárfiókból. 

Ha vissza kell vonnia a függvényalkalmazás hozzáférését a tárfiókhoz, visszavonhatja a hozzáférést a key vaulthoz, vagy elforgathatja a tárfiók kulcsait, ami érvényteleníti a SAS URL-címét.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="is-there-any-additional-charge-for-running-my-function-app-from-the-deployment-package"></a>Van-e további díj a függvényalkalmazás futtatásáért a központi telepítési csomagból?

Csak az Azure Storage-fiókhoz kapcsolódó költségeket és a vonatkozó kimenő forgalom díjakat.

### <a name="how-does-running-from-the-deployment-package-affect-my-function-app"></a>Hogyan befolyásolja a központi telepítési csomagról való futás a függvényalkalmazást?

- Ha az alkalmazást a `wwwroot/` központi telepítési csomagból futtatja, írásvédett. Az alkalmazás hibaüzenetet kap, amikor megpróbál írni ebbe a könyvtárba.
- A TAR és a GZIP formátumok nem támogatottak.
- Ez a szolgáltatás nem kompatibilis a helyi gyorsítótárral.

## <a name="next-steps"></a>További lépések

- [Key Vault-hivatkozások az App Service-hez](../app-service/app-service-key-vault-references.md)
- [Az Azure Storage titkosítása az inaktív adatokhoz](../storage/common/storage-service-encryption.md)
