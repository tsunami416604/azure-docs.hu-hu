---
title: Az Azure Automationben Python 2-csomagok kezelése
description: Ez a cikk ismerteti, hogyan kezelheti a Python 2-csomagok az Azure Automationben.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: de0998dffeac54db5311bbcde1c9499488b23556
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434972"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Az Azure Automationben Python 2-csomagok kezelése

Az Azure Automation lehetővé teszi, hogy a Python 2-runbookok futtatása az Azure-ban, és a hibrid Runbook-feldolgozók Linux. A runbookok egyszerűsítése érdekében a Python-csomagok segítségével a szükséges modulok importálásához. Ez a cikk bemutatja, hogyan kezelheti és Python-csomagok használata az Azure Automationben.

## <a name="import-packages"></a>Csomagok importálása

Válassza ki az Automation-fiók **Python 2 csomagok** alatt **megosztott erőforrások**. Kattintson a **+ Python 2 csomag hozzáadása**.

![Python-csomag hozzáadása](media/python-packages/add-python-package.png)

Az a **Python 2 csomag hozzáadása** lapon, válassza ki a feltölteni kívánt helyi csomagot. A csomag is lehet egy `.whl` fájlt vagy `.tar.gz` fájlt. Kijelölése után kattintson **OK** a csomag feltöltése.

![Python-csomag hozzáadása](media/python-packages/upload-package.png)

A csomag importálása után megtalálható az **Python 2 csomagok** az Automation-fiók oldalán. Ha szeretne eltávolítani a csomagot, válassza ki a csomagot, és válassza **törlése** a csomag lapon.

![Csomag listája](media/python-packages/package-list.png)

## <a name="use-a-package-in-a-runbook"></a>A runbook a csomag használatára

A csomag importálását követően most már használhatja a runbookban. Az alábbi példában a [ Azure Automation segédprogram csomag](https://github.com/azureautomation/azure_automation_utility). Ez a csomag megkönnyíti az Azure Automation szolgáltatással a Python használata. A csomag használatára, kövesse a GitHub-adattárat, és adja hozzá a runbook használatával `from azure_automation_utility import get_automation_runas_credential` például importálása a funkció lekéréséhez a futtató fiókot.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>Fejlesztés és tesztelés a kapcsolat nélküli forgatókönyvek

A Python 2-runbookok offline fejlesztéséhez és teszteléséhez használhatja a [Azure Automation python-eszközök emulált](https://github.com/azureautomation/python_emulated_assets) modul a Githubon. Ez a modul lehetővé teszi, hogy a közös erőforrások – például a hitelesítő adatokat, változókat, kapcsolatok és tanúsítványok használatával.

## <a name="next-steps"></a>További lépések

Python 2-forgatókönyvekkel való ismerkedéshez, lásd: [az első Python 2-forgatókönyvem](automation-first-runbook-textual-python2.md)
