---
title: Az alkalmazás futtatása ZIP-csomagból
description: Telepítse az alkalmazás ZIP-csomagját atomisággal. Javítsa az alkalmazás viselkedésének kiszámíthatóságát és megbízhatóságát a ZIP-telepítési folyamat során.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 5cc909d79b3f5ea2b4c6a3da12bc7250addbe00c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920722"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Az alkalmazás futtatása az Azure App Service-ben közvetlenül egy ZIP-csomagból

Az [Azure App Service-ben](overview.md)közvetlenül futtathatja az alkalmazásokat egy központi üzembe helyezési ZIP-csomagfájlból. Ez a cikk bemutatja, hogyan engedélyezheti ezt a funkciót az alkalmazásban.

Az App Service összes többi telepítési módszerében van valami közös: a fájlok a *D:\home\site\wwwroot* mappába vannak telepítve az alkalmazásban (vagy */home/site/wwwroot* Linux-alkalmazásokhoz). Mivel ugyanazt a könyvtárat használja az alkalmazás futásidőben, lehetséges, hogy a telepítés sikertelen lesz a fájlzárolási ütközések miatt, és hogy az alkalmazás kiszámíthatatlanul viselkedjen, mert egyes fájlok még nem frissültek.

Ezzel szemben, ha közvetlenül egy csomagból futtatja, a csomagban lévő fájlok nem kerülnek a *wwwroot* könyvtárba. Ehelyett maga a ZIP-csomag közvetlenül az írásvédett *wwwroot* könyvtárként lesz csatlakoztatva. Számos előnye van, hogy közvetlenül a csomagból fut:

- Kiküszöböli a fájlzárolási ütközéseket az üzembe helyezés és a futásidő között.
- Biztosítja, hogy csak a teljes telepített alkalmazások futnak bármikor.
- Éles alkalmazásba telepíthető (újraindítással).
- Javítja az Azure Resource Manager-telepítések teljesítményét.
- Csökkentheti a hidegindítási időt, különösen a nagy npm csomagfákkal rendelkező JavaScript függvények esetében.

> [!NOTE]
> Jelenleg csak a ZIP csomagfájlok támogatottak.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Futó csomagból való futtatás engedélyezése

Az `WEBSITE_RUN_FROM_PACKAGE` alkalmazásbeállítás lehetővé teszi a csomagból történő futtatást. A beállításához futtassa a következő parancsot az Azure CLI segítségével.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"`lehetővé teszi az alkalmazás futtatását helyi csomagról az alkalmazásra. [Távoli csomagból](#run-from-external-url-instead)is futtatható.

## <a name="run-the-package"></a>A csomag futtatása

Az App Service-ben a csomag futtatásának legegyszerűbb módja az Azure CLI [az webapp telepítési forrás config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) parancs. Példa:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Mivel `WEBSITE_RUN_FROM_PACKAGE` az alkalmazásbeállítása be van állítva, ez a parancs nem bontja ki a csomag tartalmát az alkalmazás *D:\home\site\wwwroot* könyvtárába. Ehelyett a ZIP-fájlt a *D:\home\data\SitePackages*mappába tölti fel, és ugyanabban a könyvtárban létrehoz egy *packagename.txt* fájlt, amely tartalmazza a futásidőben betöltendő ZIP-csomag nevét. Ha a ZIP-csomagot más módon tölti fel (például [FTP),](deploy-ftp.md)akkor a *D:\home\data\SitePackages könyvtárat* és a *packagename.txt* fájlt manuálisan kell létrehoznia.

A parancs újraindítja az alkalmazást is. Mivel `WEBSITE_RUN_FROM_PACKAGE` be van állítva, az App Service a feltöltött csomagot csak olvasható *wwwroot* könyvtárként csatlakoztatja, és közvetlenül a csatlakoztatott könyvtárból futtatja az alkalmazást.

## <a name="run-from-external-url-instead"></a>Futtatás külső URL-címről

Egy csomagot is futtathat egy külső URL-címről, például az Azure Blob Storage-ból. Az Azure [Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) segítségével csomagfájlokat tölthet fel a Blob storage-fiókjába. A [közös hozzáférésű aláírással (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) rendelkező magántárolót kell használnia, hogy az App Service futásidejű biztonságos anammindához férjen hozzá. 

Miután feltöltötte a fájlt a Blob storage-ba, és `WEBSITE_RUN_FROM_PACKAGE` rendelkezik a fájl SAS URL-címével, állítsa be az alkalmazás beállítást az URL-címre. A következő példa az Azure CLI használatával történik:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Ha egy frissített csomagot tesz közzé ugyanazzal a névvel a Blob storage-ban, újra kell indítania az alkalmazást, hogy a frissített csomag betöltődjön az App Service-be.

## <a name="troubleshooting"></a>Hibaelhárítás

- Ha közvetlenül egy `wwwroot` csomagból fut, írásvédett. Az alkalmazás hibaüzenetet kap, ha fájlokat próbál írni ebbe a könyvtárba.
- A TAR és a GZIP formátumok nem támogatottak.
- Ez a szolgáltatás nem kompatibilis a [helyi gyorsítótárral.](overview-local-cache.md)
- A jobb hidegindítási teljesítmény érdekében használja`WEBSITE_RUN_FROM_PACKAGE`a helyi Zip opciót ( =1).

## <a name="more-resources"></a>További erőforrások

- [Folyamatos üzembe helyezés az Azure App Service-hez](deploy-continuous-deployment.md)
- [Kód telepítése ZIP- vagy WAR-fájllal](deploy-zip.md)
