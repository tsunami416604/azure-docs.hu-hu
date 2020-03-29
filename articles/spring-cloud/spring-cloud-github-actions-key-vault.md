---
title: Az Azure Spring Cloud hitelesítése a Key Vault segítségével a GitHub-műveletekben
description: A kulcstartó használata CI/CD-munkafolyamattal az Azure Spring Cloud szolgáltatáshoz a GitHub-műveletekkel
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/20/2019
ms.openlocfilehash: 78cd5945e394219be0551bbe97afef07f18b61f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945465"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Az Azure Spring Cloud hitelesítése a Key Vault segítségével a GitHub-műveletekben
A kulcstartó biztonságos hely a kulcsok tárolására. A vállalati felhasználóknak az általuk szabályozható hatókörben kell tárolniuk a CI/CD-környezetek hitelesítő adatait. A kulcs a kulcs a key vault ban kell korlátozni az erőforrás-hatókört.  Csak a key vault hatóköréhez rendelkezik, a teljes Azure-hatókörhöz nem. Olyan, mint egy kulcs, ami csak egy erős dobozt tud kinyitni, nem pedig egy mesterkulcsot, ami kinyitja az épület összes ajtaját. Ez egy módja annak, hogy egy kulcsot egy másik kulccsal, ami hasznos a CICD munkafolyamat. 

## <a name="generate-credential"></a>Hitelesítő adatok létrehozása
A kulcstároló eléréséhez kulcs létrehozásához hajtsa végre az alábbi parancsot a helyi számítógépen:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
A `--scopes` paraméter által megadott hatókör korlátozza az erőforrás kulcshoz való hozzáférését.  Csak az erős dobozhoz fér hozzá.

Eredményekkel:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Ezután mentse az eredményeket a **GitHub-titokakba** a [GitHub-tárház beállítása és az Azure-ral való hitelesítés eszerint.](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate)

## <a name="add-access-policies-for-the-credential"></a>Access-házirendek hozzáadása a hitelesítő adatokhoz
A fent létrehozott hitelesítő adatok csak általános információkat kaphatnak a Key Vaultról, az általa tárolott tartalomról nem.  A Key Vaultban tárolt titkos kulcsok letiltásához hozzáférési szabályzatokat kell beállítania a hitelesítő adatokhoz.

Nyissa meg a Key **Vault** irányítópultját az Azure Portalon, kattintson a **Apps** **Hozzáférés-vezérlés** **scope**menüre, majd nyissa meg a **Szerepkör-hozzárendelések** lapot. **Type** `This resource`  Az előző lépésben létrehozott hitelesítő adatoknak meg kell jelennie:

 ![Hozzáférési házirend beállítása](./media/github-actions/key-vault1.png)

Másolja a hitelesítő adatok `azure-cli-2020-01-19-04-39-02`nevét, például . Nyissa meg az **Access házirendek menüjét,** és kattintson a **+Hozzáférési házirend hozzáadása** hivatkozásra.  Válassza `Secret Management` a **Sablon**lehetőséget, majd válassza a **Tag**lehetőséget. A hitelesítő adatok nevének beillesztése az **Egyszerű**/**mezőben:**

 ![Válassza ezt:](./media/github-actions/key-vault2.png)

 Kattintson a **Hozzáadás** gombra a **Hozzáférési házirend hozzáadása** párbeszédpanelen, majd a Mentés **gombra.**

## <a name="generate-full-scope-azure-credential"></a>Teljes körű Azure-hitelesítő adatok létrehozása
Ez a fő kulcs, hogy kinyissunk minden ajtót az épületben. Az eljárás hasonló az előző lépéshez, de itt megváltoztatjuk a főkulcs létrehozásának hatókörét:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Ismét, eredmények:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Másolja a teljes JSON-karakterláncot.  Bo vissza a **Key Vault** műszerfalára. Nyissa meg a **Titkos kulcsok menüt,** majd kattintson a **Létrehozás/importálás** gombra. Adja meg a titkos `AZURE-CRENDENTIALS-FOR-SPRING`nevet, például . Illessze be a JSON hitelesítő karakterláncot az **Érték** beviteli mezőbe. Előfordulhat, hogy az értékbeviteli mező inkább egysoros szövegmező, mint többsoros szövegterület.  A teljes JSON-karakterláncot beillesztheti.

 ![Teljes hatókör-hitelesítő adatok](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Hitelesítő adatok egyesítése a GitHub-műveletekben
A CICD-folyamat végrehajtásakor használt hitelesítő adatok beállítása:

```
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "zlhe-test"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>További lépések
* [Tavaszi Felhőbeli GitHub-műveletek](./spring-cloud-howto-github-actions.md)