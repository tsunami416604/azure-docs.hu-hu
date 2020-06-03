---
title: Azure CLI-referenciák az Azure-adatmegosztáshoz
description: Azure CLI-hivatkozás kezdőlapja Azure-beli adatmegosztáshoz
services: data-share
author: dbradish-microsoft
manager: barbkess
ms.service: data-share
ms.devlang: azurecli
ms.topic: reference
ms.date: 05/27/2020
ms.author: dbradish
ms.openlocfilehash: 404022b13f44174e4b647f0430a58fac5eeb81df
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300618"
---
# <a name="azure-cli-for-azure-data-share"></a>Azure CLI az Azure-beli adatmegosztáshoz

Az Azure parancssori felülete ([Azure CLI](/cli/azure/what-is-azure-cli)) az Azure-erőforrások létrehozásához és kezeléséhez használt parancsok összessége.  Számos Azure-szolgáltatásban elérhető, beleértve az Azure-adatmegosztást is.  Több mint 65 különböző parancs létezik az adatmegosztáshoz.  Ezek a parancsok lehetővé teszi, hogy a parancssorból hatékonyan működjenek együtt a szolgáltatással.

## <a name="references-for-data-share"></a>Adatmegosztásra mutató hivatkozások

Az Azure-beli adatmegosztás összes Azure CLI-parancsa jelenleg az Azure CLI-hez készült.  A bővítmények lehetővé teszi a kísérleti és az előzetes kiadású parancsok elérését.  További információ a bővítmények [használatáról az Azure CLI-vel](/cli/azure/azure-cli-extensions-overview).

|Azure CLI-dokumentáció |Leírás
|-|-|-|
| [az DataShare](/cli/azure/ext/datashare/datashare) | Minden, az Azure-adatmegosztást kezelő parancs.
| [az DataShare Account](/cli/azure/ext/datashare/datashare/account) | Az Azure-beli adatmegosztási fiókok kezelésére szolgáló parancsok.
| [az DataShare Consumer](/cli/azure/ext/datashare/datashare/consumer) | Parancsok a felhasználók számára az Azure-adatmegosztás kezeléséhez.
| [az DataShare DataSet](/cli/azure/ext/datashare/datashare/dataset) | Parancsok a szolgáltatók számára az Azure-beli adatmegosztási adatkészletek kezeléséhez.
| [az DataShare meghívás](/cli/azure/ext/datashare/datashare/invitation) | Parancsok a felhasználók számára az Azure-beli adatmegosztási meghívások kezeléséhez.
| [az DataShare Provider-Share-előfizetés](/cli/azure/ext/datashare/datashare/provider-share-subscription) | Parancsok a szolgáltatók számára az Azure-beli adatmegosztás-előfizetések kezeléséhez.
| [az DataShare synchronization](/cli/azure/ext/datashare/datashare/synchronization)  | Az Azure-beli adatmegosztások szinkronizálását kezelő parancsok.
| [az DataShare synchronization-Setting](/cli/azure/ext/datashare/datashare/synchronization-setting)  | Parancsok a szolgáltatók számára az Azure-adatmegosztás szinkronizálási beállításainak kezeléséhez.

## <a name="reference-examples"></a>Példák a hivatkozásokra

A példák minden Azure CLI-referenciával rendelkeznek. Bár ezeket a feladatokat a Azure Portalon is elvégezheti, az Azure CLI-vel egyetlen parancssort kell használni.  Íme néhány kód, amelyből megtudhatja, milyen egyszerűen használhatja az Azure CLI-t.

Az Azure-beli adatmegosztással való együttműködéshez először szüksége lesz egy erőforráscsoporthoz.  Az Azure-erőforráscsoportok egyszerűen létrehozhatók és kezelhetők az Azure CLI-vel.  

```azurecli
#create a resource group
az group create -location westus -name MyResourceGroup
```

```azurecli
#get a list of resource groups for a subscription
az group list --subscription MySubscription --output table
```

Az adatmegosztási fiók létrehozása egyszerű.

```azurecli
#create a data share account
az datashare account create --location "West US 2" --tags tag1=Red tag2=White --name MyAccount --resource-group MyResourceGroup
```

## <a name="see-also"></a>Lásd még

* Ismerkedés [Az Azure CLI-vel](/cli/azure/get-started-with-azure-cli) a telepítés és a bejelentkezés megismeréséhez.

* Ismerje meg az Azure CLI dokumentációjának további [alapvető](/cli/azure/reference-index) és [kiterjesztési](/cli/azure/azure-cli-extensions-list) hivatkozásait.
