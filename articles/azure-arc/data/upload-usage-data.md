---
title: Használati adatok feltöltése a Azure Monitorba
description: Azure arc-kompatibilis adatszolgáltatási adatok feltöltése a Azure Monitorba
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 578f0d1ca742fe4445b8aeed6876d1a73fd3f79e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92376206"
---
# <a name="upload-usage-data-to-azure-monitor"></a>Használati adatok feltöltése a Azure Monitorba

Rendszeres időközönként exportálhatja a használati adatokat. Ezeknek az adatoknak az exportálásával és feltöltésével létrehozhatja és frissítheti az adatkezelőt, az SQL felügyelt példányát és a PostgreSQL nagy kapacitású Server Group-erőforrásait az Azure-ban.

> [!NOTE] 
> Az előzetes verzió ideje alatt az Azure arc-kompatibilis adatszolgáltatások használata díjmentes.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


> [!NOTE]
> Várjon legalább 24 órát az Azure arc-adatkezelő létrehozása után a használati adatok feltöltése előtt.

## <a name="create-service-principal-and-assign-roles"></a>Egyszerű szolgáltatásnév létrehozása és szerepkörök társítása

A folytatás előtt győződjön meg arról, hogy létrehozta a szükséges szolgáltatásnevet, és hozzárendelte azt egy megfelelő szerepkörhöz. Részletes információ:
* [Egyszerű szolgáltatásnév létrehozása](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [Szerepkörök társítása az egyszerű szolgáltatáshoz](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-usage-data"></a>Használati adatok feltöltése

A következő kétlépéses módon töltheti fel az Azure-ba a használati adatokat, például a leltárt és az erőforrás-használatot:

1. Jelentkezzen be az adatvezérlőbe. Adja meg az értékeket a parancssorban. 

   ```console
   azdata login
   ```

1. Exportálja a használati adatokat a `azdata arc dc export` paranccsal a következő módon:

   ```console
   azdata arc dc export --type usage --path usage.json
   ```
 
   Ez a parancs létrehoz egy `usage.json` fájlt az összes Azure arc-kompatibilis adaterőforrással, például az SQL által felügyelt példányokkal és a PostgreSQL nagy kapacitású-példányokkal, amelyek az adatvezérlőn jönnek létre.

2. A használati adatok feltöltése a ```azdata upload``` parancs használatával

   ```console
   azdata arc dc upload --path usage.json
   ```

## <a name="automating-uploads-optional"></a>Feltöltések automatizálása (nem kötelező)

Ha a mérőszámokat és a naplókat ütemezett alapon szeretné feltölteni, létrehozhat egy parancsfájlt, és néhány percenként elvégezheti az időzítő futtatását. Az alábbi példa a feltöltések automatizálását mutatja be egy linuxos rendszerhéj-parancsfájl használatával.

A kedvenc szöveg/kód szerkesztőjében adja hozzá a következő parancsfájlt a fájlhoz, és mentse parancsfájlként végrehajtható fájlként, például. sh (Linux/Mac) vagy. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

A parancsfájl végrehajtható fájljának elkészítése

```console
chmod +x myuploadscript.sh
```

Futtassa a szkriptet 20 percenként:

```console
watch -n 1200 ./myuploadscript.sh
```

Olyan Feladatütemezőt is használhat, mint például a cron vagy a Windows Feladatütemező, vagy egy Orchestrator, például a Ansible, a Puppet vagy a Chef.

## <a name="next-steps"></a>Következő lépések

[Metrikák és naplók feltöltése a Azure Monitorba](upload-metrics.md)

[Naplók feltöltése a Azure Monitorba](upload-logs.md)

[Számlázási adatok feltöltése az Azure-ba, és megtekintés a Azure Portal](view-billing-data-in-azure.md)

[Az Azure arc adatkezelő erőforrásának megtekintése Azure Portal](view-data-controller-in-azure-portal.md)
