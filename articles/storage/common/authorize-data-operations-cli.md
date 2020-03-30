---
title: Blob- vagy várólista-adatokhoz való hozzáférés engedélyezése az Azure CLI-vel
titleSuffix: Azure Storage
description: Adja meg, hogyan engedélyezheti az adatműveleteket blob- vagy várólista-adatok ellen az Azure CLI-vel. Engedélyezheti az adatok műveletek et az Azure AD hitelesítő adatok, a fiók hozzáférési kulcs, vagy egy közös hozzáférési aláírás (SAS) jogkivonatot.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c7091592f8806b6f6655315ae1faace286c2c1f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207692"
---
# <a name="authorize-access-to-blob-or-queue-data-with-azure-cli"></a>Blob- vagy várólista-adatokhoz való hozzáférés engedélyezése az Azure CLI-vel

Az Azure Storage bővítményeket biztosít az Azure CLI számára, amelyek lehetővé teszik, hogy megadja, hogyan szeretné engedélyezni a blob- vagy várólista-adatokon végzett műveleteket. Az adatműveleteket az alábbi módokon engedélyezheti:

- Egy Azure Active Directory (Azure AD) rendszerbiztonsági tag. A Microsoft azt javasolja, hogy az Azure AD hitelesítő adatok használata a jobb biztonság és a könnyű használat érdekében.
- A fiók hozzáférési kulccsal vagy egy közös hozzáférésű aláírás (SAS) jogkivonattal.

## <a name="specify-how-data-operations-are-authorized"></a>Az adatműveletek engedélyezésének megadása

Az Azure CLI parancsok olvasására és írására blob és a várólista-adatok tartalmazzák a választható `--auth-mode` paraméter. Adja meg ezt a paramétert az adatműveletek engedélyezésének jelzésére:

- Állítsa `--auth-mode` be `login` a paramétert, hogy jelentkezzen be egy Azure AD rendszerbiztonsági tag (ajánlott).
- Állítsa `--auth-mode` be a `key` paramétert az örökölt értékre, hogy megpróbálja beolvasni az engedélyezéshez használt fiókhozzáférési kulcsot. Ha kihagyja a `--auth-mode` paramétert, majd az Azure CLI is megpróbálja beolvasni a hozzáférési kulcsot.

A paraméter `--auth-mode` használatához győződjön meg arról, hogy telepítette az Azure CLI 2.0.46-os vagy újabb verzióját. Futtassa `az --version` a telepített verzió ellenőrzéséhez.

> [!IMPORTANT]
> Ha kihagyja a `--auth-mode` `key`paramétert, vagy állítsa a, majd az Azure CLI megpróbálja használni a fiók hozzáférési kulcs engedélyezési. Ebben az esetben a Microsoft azt javasolja, hogy adja meg a hozzáférési kulcsot a parancson vagy a **AZURE_STORAGE_KEY** környezeti változóban. A környezeti változókról további információt a [Környezeti változók beállítása az engedélyezési paraméterekhez](#set-environment-variables-for-authorization-parameters)című szakaszban talál.
>
> Ha nem adja meg a hozzáférési kulcsot, majd az Azure CLI megpróbálja felhívni az Azure Storage-erőforrás-szolgáltató t minden művelethez. Számos olyan adatművelet végrehajtása, amelyek az erőforrás-szolgáltató hívását igénylik, szabályozást eredményezhet. Az erőforrás-szolgáltatói korlátokról további információt [az Azure Storage-erőforrás-szolgáltató méretezhetőségi és teljesítménycéljai](scalability-targets-resource-provider.md)című témakörben talál.

## <a name="authorize-with-azure-ad-credentials"></a>Engedélyezés Az Azure AD hitelesítő adataival

Amikor bejelentkezik az Azure CLI-be az Azure AD-hitelesítő adatokkal, egy OAuth 2.0-s hozzáférési jogkivonatot ad vissza. Ezt a jogkivonatot az Azure CLI automatikusan használja a Blob vagy a Queue storage elleni további adatműveletek engedélyezéséhez. A támogatott műveletek, már nem kell átadni egy fiókkulcsot vagy SAS-jogkivonatot a parancsot.

Szerepköralapú hozzáférés-vezérlés (RBAC) keresztül engedélyeket rendelhet a blob- és várólista-adatokhoz egy Azure AD rendszerbiztonsági taghoz. Az RBAC-szerepkörökről az Azure Storage-ban további információt az [Azure Storage-adatokhoz való hozzáférési jogok kezelése az RBAC-mal című](storage-auth-aad-rbac.md)témakörben talál.

### <a name="permissions-for-calling-data-operations"></a>Engedélyek az adatkezelési műveletek hívásához

Az Azure Storage-bővítmények blob- és várólista-adatokon végzett műveletekhez támogatottak. Mely műveleteket hívhatja meg, az Azure AD rendszerbiztonsági tagnak adott engedélyektől függ, amellyel bejelentkezik az Azure CLI-be. Az Azure Storage-tárolókhoz vagy várólistákhoz az RBAC-on keresztül vannak hozzárendelve. Ha például a Blob **Data Reader** szerepkört rendeli hozzá, futtathatja azadatokat egy tárolóból vagy várólistából adatokat olvasó parancsfájl-parancsokat. Ha a Blob **Data Contributor** szerepkör t, akkor futtathatja a parancsfájlkezelési parancsokat, amelyek olvasni, írni vagy törölni egy tároló vagy várólista vagy a benne lévő adatokat.

Az egyes Azure Storage-műveletekhez egy tárolón vagy várólistán szükséges engedélyekről az [OAuth-jogkivonatokkal végzett tárolási műveletek hívása](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)című témakörben talál.  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>Példa: Engedélyezés egy azure AD-hitelesítő adatokkal rendelkező tároló létrehozásához

A következő példa bemutatja, hogyan hozhat létre egy tárolót az Azure CLI-ből az Azure AD hitelesítő adataival. A tároló létrehozásához be kell jelentkeznie az Azure CLI-be, és szüksége lesz egy erőforráscsoportra és egy tárfiókra. Az erőforrások létrehozásáról a [Rövid útmutató: Blobok létrehozása, letöltése és listázása az Azure CLI-vel](../blobs/storage-quickstart-blobs-cli.md)című témakörben olvashat.

1. A tároló létrehozása előtt rendelje hozzá a [Storage Blob Data Contributor](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) szerepkört saját magához. Annak ellenére, hogy Ön a fiók tulajdonosa, explicit engedélyekre van szüksége az adatműveletek a tárfiókon való végrehajtásához. Az RBAC-szerepkörök hozzárendeléséről az [Azure blob- és várólista-adatok hoz való hozzáférés megadása az RBAC-mal című témakörben talál további információt az Azure Portalon.](storage-auth-aad-rbac.md)

    > [!IMPORTANT]
    > Az RBAC szerepkör-hozzárendelések propagálása néhány percet is igénybe vehet.

1. Hívja meg az az storage `--auth-mode` container `login` [create](/cli/azure/storage/container#az-storage-container-create) parancsot a paraméter készlettel a tároló létrehozásához az Azure AD hitelesítő adatait. Ne felejtse el a szögletes zárójelekben lévő helyőrző értékeket a saját értékeire cserélni:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Engedélyezés a fiókhozzáférési kulccsal

Ha rendelkezik a fiókkulcs, hívhatja az Azure Storage-adatműveleteket. A fiókkulcs használata általában kevésbé biztonságos. Ha a fiókkulcs biztonsága sérül, a fiókjában lévő összes adat biztonsága sérülhet.

A következő példa bemutatja, hogyan hozhat létre egy tárolót a fiók hozzáférési kulcshasználatával. Adja meg a számlakulcsot, és adja meg a `--auth-mode` paramétert a `key` következő értékkel:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Engedélyezés SAS-jogkivonattal

Ha rendelkezik egy SAS-jogkivonattal, meghívhat a SAS által engedélyezett adatműveleteket. A következő példa bemutatja, hogyan hozhat létre egy tárolót egy SAS-jogkivonat használatával:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Környezeti változók beállítása az engedélyezési paraméterekhez

Engedélyezési paramétereket adhat meg a környezeti változókban, hogy elkerülje az Azure Storage-adatműveletek minden hívásában való felkerülésüket. Az alábbi táblázat a rendelkezésre álló környezeti változókat ismerteti.

| Környezeti változó                  | Leírás                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    A tárfiók neve. Ezt a változót a tárfiók kulcsával vagy egy SAS-jogkivonattal együtt kell használni. Ha egyik sem van jelen, az Azure CLI megpróbálja lekérni a tárfiók hozzáférési kulcs a hitelesített Azure AD-fiók használatával. Ha egyszerre nagy számú parancsot hajt végre, az Azure Storage-erőforrás-szolgáltató szabályozási korlátja elérhető. Az erőforrás-szolgáltatói korlátokról további információt [az Azure Storage-erőforrás-szolgáltató méretezhetőségi és teljesítménycéljai](scalability-targets-resource-provider.md)című témakörben talál.             |
|    AZURE_STORAGE_KEY                  |    A tárfiókkulcs. Ezt a változót a tárfiók nevével együtt kell használni.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    A kapcsolati karakterlánc, amely tartalmazza a tárfiók kulcsvagy egy SAS-jogkivonat. Ezt a változót a tárfiók nevével együtt kell használni.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    Egy közös hozzáférésű aláírás (SAS) jogkivonat. Ezt a változót a tárfiók nevével együtt kell használni.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    Az engedélyezési mód, amellyel a parancsot futtatni szeretné. Az engedélyezett `login` értékek (ajánlott) vagy `key`. Ha megadja, `login`az Azure CLI az Azure AD hitelesítő adatait használja az adatművelet engedélyezéséhez. Ha megadja az `key` örökölt módot, az Azure CLI megpróbálja lekérdezni a fiók hozzáférési kulcsát, és engedélyezni a parancsot a kulccsal.    |

## <a name="next-steps"></a>További lépések

- [Az Azure CLI használatával RBAC szerepkörhozzárendelése blob- és várólista-adatokhoz való hozzáféréshez](storage-auth-aad-rbac-cli.md)
- [Blob- és várólista-adatokhoz való hozzáférés engedélyezése felügyelt identitásokkal az Azure-erőforrásokhoz](storage-auth-aad-msi.md)
