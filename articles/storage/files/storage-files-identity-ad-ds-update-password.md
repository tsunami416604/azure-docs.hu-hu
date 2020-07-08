---
title: AD DS Storage-fiók jelszavának frissítése
description: Megtudhatja, hogyan frissítheti a Storage-fiókját képviselő Active Directory tartományi szolgáltatások-fiók jelszavát. Ez megakadályozza, hogy a Storage-fiók törölve legyen a jelszó lejárta után, ami megakadályozza a hitelesítési hibákat.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: d16f11a85c6b370b0187975cce965bf3e1b5ba3f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510254"
---
# <a name="update-the-password-of-your-storage-account-identity-in-ad-ds"></a>A Storage-fiók identitásának jelszavának frissítése AD DS

Ha regisztrálta azt a Active Directory tartományi szolgáltatások (AD DS) identitást/fiókot, amely egy olyan szervezeti egységben vagy tartományban található meg, amely a jelszó lejárati idejét érvényesíti, a jelszó maximális kora előtt módosítania kell a jelszót. A szervezet olyan automatizált karbantartási parancsfájlokat futtathat, amelyek a jelszavuk lejárta után törlik a fiókokat. Ezért ha nem módosítja a jelszavát, mielőtt lejár, a fiókját törölheti, ami miatt elveszíti az Azure-fájlmegosztás elérését.

A jelszó elforgatásának elindításához futtassa a `Update-AzStorageAccountADObjectPassword` parancsot a [AzFilesHybrid modulból](https://github.com/Azure-Samples/azure-files-samples/releases). Ezt a parancsot helyszíni AD DS csatlakoztatott környezetben kell futtatni egy olyan hibrid felhasználóval, aki tulajdonosi engedéllyel rendelkezik a Storage-fiókhoz, és AD DS engedélyeket a Storage-fiókot jelképező identitás jelszavának módosítására. A parancs a Storage-fiók kulcsának elforgatásához hasonló műveleteket hajt végre. Pontosabban lekéri a Storage-fiók második Kerberos-kulcsát, és felhasználja a regisztrált fiók jelszavának frissítésére AD DSban. Ezután újralétrehozza a Storage-fiók cél Kerberos-kulcsát, és frissíti a regisztrált fiók jelszavát AD DSban. Ezt a parancsot helyszíni AD DS csatlakoztatott környezetben kell futtatnia.

```PowerShell
# Update the password of the AD DS account registered for the storage account
# You may use either kerb1 or kerb2
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```
