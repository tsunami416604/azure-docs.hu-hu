---
title: Az Azure Automationben Python 2-csomagok kezelése
description: Ez a cikk ismerteti, hogyan kezelheti a Python 2-csomagok az Azure Automationben.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 28ddecb20944893b23b54775e22f19644f0afbf0
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56816504"
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

## <a name="import-packages-with-dependencies"></a>Importálja a függőségeket tartalmazó csomagok

Az Azure automation az importálási folyamat során nem oldja meg függőségek, a python-csomagokat. Kétféleképpen minden függőségével csomag importálása. Csak a következő lépések egyikét kell használni a csomagok importálásához az Automation-fiókba.

### <a name="manually-download"></a>Manuális letöltés

A Windows 64 bites gép [python2.7](https://www.python.org/download/releases/2.7/) és [pip](https://pip.pypa.io/stable/installing/) telepítve, a következő parancsot egy csomagot és annak összes függőségét letöltéséhez:

```
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

A csomagok a letöltést követően importálhatja azokat az automation-fiókba.

### <a name="runbook"></a>Forgatókönyv

A python-runbook importálása [importálás Python 2-csomagot az Azure Automation-fiókba pypi](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) a katalógusból az Automation-fiókba. Ellenőrizze, hogy a Futtatás beállítása **Azure** és indítsa el a runbook paraméterekkel. A runbook egy futtató fiókot a működéséhez az Automation-fiók szükséges. Ellenőrizze, hogy minden paraméter számára, indítsa el a kapcsoló a következő lista és a képen látható módon:

* -s \<előfizetés-azonosító\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

![Csomag listája](media/python-packages/import-python-runbook.png)

A runbook lehetővé teszi, hogy adja meg, milyen csomag letöltéséhez például `Azure` (a negyedik paraméter) tölti le az összes Azure-modulokat, és annak függőségeit, azaz KB 105.

A forgatókönyv befejezése után ellenőrizheti a **Python 2 csomagok** lap **megosztott erőforrások** az Automation-fiókban, győződjön meg arról, hogy azok a csomag importálása megfelelően.

## <a name="use-a-package-in-a-runbook"></a>A runbook a csomag használatára

A csomag importálása után most már használhatja a runbookban. Az alábbi példában a [ Azure Automation segédprogram csomag](https://github.com/azureautomation/azure_automation_utility). Ez a csomag megkönnyíti az Azure Automation szolgáltatással a Python használata. A csomag használatára, kövesse a GitHub-adattárat, és adja hozzá a runbook használatával `from azure_automation_utility import get_automation_runas_credential` például importálása a funkció lekéréséhez a futtató fiókot.

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
