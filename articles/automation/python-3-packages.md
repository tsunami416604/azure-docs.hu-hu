---
title: Python 3 csomagok kezelése Azure Automation
description: Ez a cikk azt ismerteti, hogyan kezelhető a Python 3 csomag (előzetes verzió) a Azure Automationban.
services: automation
ms.subservice: process-automation
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 3f39f49ff47b935da7ffc777ee45bd219f5740b5
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734301"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Python 3 csomag (előzetes verzió) kezelése Azure Automation

Azure Automation lehetővé teszi a Python 3 runbookok (előzetes verzió) futtatását az Azure-ban és a Linux hibrid Runbook-feldolgozókon. A runbookok egyszerűsítése érdekében a Python-csomagokat használhatja a szükséges modulok importálásához. Ez a cikk bemutatja, hogyan kezelheti és használhatja a Python 3 csomagokat (előzetes verzió) a Azure Automationban.

## <a name="import-packages"></a>Csomagok importálása

Az Automation-fiókban válassza a **Python-csomagok** elemet a **megosztott erőforrások** területen. válassza **a + Python-csomag hozzáadása** elemet.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="A Python 3 csomagok oldalának képernyőképe a bal oldali menüben a Python 3 csomagot jeleníti meg, és egy kiemelt Python 2 csomagot ad hozzá.":::

A **Python-csomag hozzáadása** lapon válassza a Python 3 elemet a **verzióhoz**, és válassza ki a feltölteni kívánt helyi csomagot. A csomag lehet egy **. WHL** vagy **. tar. gz** fájl. Ha a csomag ki van választva, kattintson az **OK gombra** a feltöltéshez.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="A képernyőképen a Python 3 csomag hozzáadása oldal jelenik meg, amelyhez feltöltött tar. gz fájl van kijelölve.":::

A csomag importálása után a rendszer az Automation-fiók Python-csomagok lapján, a **Python 3 csomagok (előzetes verzió)** lapon jelenik meg. Ha el kell távolítania egy csomagot, válassza ki a csomagot, és kattintson a **Törlés** gombra.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="A csomag importálása után a képernyőkép a Python 3 csomagok oldalát jeleníti meg.":::

## <a name="import-packages-with-dependencies"></a>Csomagok importálása függőségekkel

Azure Automation nem oldja meg a Python-csomagok függőségeit az importálási folyamat során. Azonban lehetőség van egy csomag importálására az összes függőségével.

### <a name="manually-download"></a>Manuális Letöltés

A [Python 3,8](https://www.python.org/downloads/release/python-380/) és a [pip](https://pip.pypa.io/en/stable/) rendszerű Windows 64 bites gépen a következő parancs futtatásával töltse le a csomagot és annak összes függőségét:

```cmd
C:\Python38\Scripts\pip3.8.exe download -d <output dir> <package name>
```

A csomagok letöltése után importálhatja őket az Automation-fiókjába.

## <a name="use-a-package-in-a-runbook"></a>Csomag használata runbook

Az importált csomaggal használhatja azt egy runbook. Adja hozzá a következő kódot az Azure-előfizetésben található összes erőforráscsoport listázásához.

```python
import os  
import azure.mgmt.resource  
import automationassets  

def get_automation_runas_credential(runas_connection):  
    from OpenSSL import crypto  
    import binascii  
    from msrestazure import azure_active_directory  
    import adal 

    # Get the Azure Automation RunAs service principal certificate  
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")  
    pks12_cert = crypto.load_pkcs12(cert)  
    pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())  

    # Get run as connection information for the Azure Automation service principal 
    application_id = runas_connection["ApplicationId"]  
    thumbprint = runas_connection["CertificateThumbprint"]  
    tenant_id = runas_connection["TenantId"]  

    # Authenticate with service principal certificate  
    resource ="https://management.core.windows.net/"  
    authority_url = ("https://login.microsoftonline.com/"+tenant_id)  
    context = adal.AuthenticationContext(authority_url)  
    return azure_active_directory.AdalAuthentication(  
    lambda: context.acquire_token_with_client_certificate(  
            resource,  
            application_id,  
            pem_pkey,  
            thumbprint) 
    ) 

# Authenticate to Azure using the Azure Automation RunAs service principal  
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")  
azure_credential = get_automation_runas_credential(runas_connection)  

# Intialize the resource management client with the RunAs credential and subscription  
resource_client = azure.mgmt.resource.ResourceManagementClient(  
    azure_credential,  
    str(runas_connection["SubscriptionId"]))  

# Get list of resource groups and print them out  
groups = resource_client.resource_groups.list()  
for group in groups:  
    print(group.name) 
```

## <a name="next-steps"></a>További lépések

Python-runbook előkészítéséhez tekintse meg [a Python-Runbook létrehozása](learn/automation-tutorial-runbook-textual-python-3.md)című témakört.
