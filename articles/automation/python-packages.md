---
title: Python 2 csomagok kezelése az Azure Automationben
description: Ez a cikk ismerteti, hogyan kezelheti a Python 2-csomagok az Azure Automationben.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.openlocfilehash: 9f52dfd92d430abffe5857d231898dd4b0e7745e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679922"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Python 2 csomagok kezelése az Azure Automationben

Az Azure Automation lehetővé teszi a Python 2 runbookok futtatását az Azure-on és a Linux hibrid runbook-feldolgozókon. A runbookok egyszerűsítése érdekében python-csomagok segítségével importálhatja a szükséges modulokat. Ez a cikk ismerteti, hogyan kezelheti és használhatja a Python-csomagok at Azure Automation.

## <a name="import-packages"></a>Csomagok importálása

Az Automation-fiókban válassza a **Python 2-csomagok at** **Megosztott erőforrások**területen. Kattintson **a + Python 2 csomag hozzáadása gombra.**

![Python-csomag hozzáadása](media/python-packages/add-python-package.png)

A Python 2 csomag hozzáadása lapon válassza ki a feltöltendő helyi csomagot. A csomag lehet **.whl** vagy **.tar.gz** fájl. Ha a csomag ki van jelölve, a feltöltéshez kattintson az **OK** gombra.

![Python-csomag hozzáadása](media/python-packages/upload-package.png)

A csomag importálása után megjelenik az Automation-fiók Python 2 csomagok lapján. Ha el kell távolítania egy csomagot, jelölje ki a csomagot, és kattintson a **Törlés gombra.**

![Csomaglista](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Függőségekkel rendelkező csomagok importálása

Az Azure automation nem oldja fel a python-csomagok függőségeit az importálási folyamat során. A csomagok at kétféleképpen importálhatja az összes függőségével. Az alábbi lépések közül csak egyet kell használni a csomagok automation-fiókba történő importálásához.

### <a name="manually-download"></a>Manuális letöltés

[Python2.7-es](https://www.python.org/downloads/release/latest/python2) és [pip-vel](https://pip.pypa.io/en/stable/) felszerelt 64 bites windowsos gépeken futtassa a következő parancsot egy csomag és annak függőségei letöltéséhez:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

A csomagok letöltése után importálhatja őket az automatizálási fiókba.

### <a name="runbook"></a>Forgatókönyv

Importálja a python runbook [Import Python 2 csomagok pypi-ből az Azure Automation-fiók](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) a katalógusból az Automation-fiókba. Győződjön meg arról, hogy a futtatási beállítások az **Azure-ra** vannak állítva, és indítsa el a runbookot a paraméterekkel. A runbook működéséhez az Automation-fiók működéséhez futtatási mint fiók szükséges. Minden paraméternél győződjön meg róla, hogy a kapcsolóval kezdi, ahogy az az alábbi listában és képen látható:

* -s \<előfizetésId\>
* -g \<erőforrásCsoport\>
* -automationAccount \<\>
* -m \<modulePackage\>

![Csomaglista](media/python-packages/import-python-runbook.png)

A runbook lehetővé teszi, hogy adja meg, milyen csomagot kell letölteni. Például a paraméter `Azure` használata letölti az összes Azure-modult és az összes függőséget (kb. 105).

Miután a runbook befejeződött, ellenőrizheti a **Python 2 csomagok** az Automation-fiók megosztott **erőforrások** alatt, hogy ellenőrizze, hogy a csomag megfelelően lett-e importálva.

## <a name="use-a-package-in-a-runbook"></a>Csomag használata runbookban

Az importált csomag esetén használhatja azt egy runbookban. A következő példa az [Azure Automation segédprogramcsomagját](https://github.com/azureautomation/azure_automation_utility)használja. Ez a csomag megkönnyíti a Python használatát az Azure Automation használatával. A csomag használatához kövesse a GitHub-tárházban található utasításokat, és adja hozzá a runbookhoz. Például importálhatja `from azure_automation_utility import get_automation_runas_credential` a futtatási mint fiók beolvasásához használt függvényt.

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

## <a name="develop-and-test-runbooks-offline"></a>Runbookok fejlesztése és tesztelése offline módban

A Python 2 runbookok offline fejlesztéséhez és teszteléséhez használhatja az [Azure Automation python emulált eszközök](https://github.com/azureautomation/python_emulated_assets) modult a GitHubon. Ez a modul lehetővé teszi, hogy a megosztott erőforrások, például a hitelesítő adatok, változók, kapcsolatok és tanúsítványok.

## <a name="next-steps"></a>További lépések

A Python 2 runbookokkal való ismerkedésről az [Első Python 2 runbook](automation-first-runbook-textual-python2.md)című témakörben van.
