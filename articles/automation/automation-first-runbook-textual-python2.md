---
title: Az Azure Automationben az első Python-forgatókönyvem
description: Ez az oktatóanyag végigvezeti a létrehozását, tesztelését és közzétételét egy egyszerű Python-forgatókönyv.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 13bb12c2c624bfd50933b624a28145172f521747
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427678"
---
# <a name="my-first-python-runbook"></a>Az első Python-runbookom

> [!div class="op_single_selector"]
> - [Grafikus](automation-first-runbook-graphical.md)
> - [PowerShell](automation-first-runbook-textual-powershell.md)
> - [PowerShell-munkafolyamat](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

Ez az oktatóanyag végigvezeti a létrehozása egy [Python-alapú runbook](automation-runbook-types.md#python-runbooks) az Azure Automationben. Először egy egyszerű runbookot, tesztelése és közzététele. Ezután módosítja a runbookot, hogy ténylegesen kezeljen Azure-erőforrásokat, ebben az esetben elindítson egy Azure-beli virtuális gépet. Végül akkor a runbook még robusztusabbá runbook-paramétereket adunk hozzá.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

- Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Automation-fiók](automation-offering-get-started.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
- Egy Azure virtuális gép. Ezt a gépet leállítja és elindítja, tehát ne olyan virtuális gépet használjon, amely élesben működik.

## <a name="create-a-new-runbook"></a>Új runbook létrehozása

Először hozzon létre egy egyszerű runbookot, amely szöveget adja vissza *Hello World*.

1. Az Azure Portalon nyissa meg az Automation-fiókját.

    Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. Ezek nagy része az új Automation-fiókhoz automatikusan hozzáadott modul. Rendelkeznie kell az [előfeltételek](#prerequisites) között említett hitelesítő adategységgel is.<br>

1. Kattintson a **Runbookok** elemre a **FOLYAMATKEZELÉS** területen a runbookok listájának megnyitásához.
1. Válassza ki **+ forgatókönyv hozzáadása** hozhat létre egy új runbookot.
1. Adja a forgatókönyvnek a név *MyFirstRunbook-Python*.
1. Ebben az esetben fog létrehozni egy [Python-alapú runbook](automation-runbook-types.md#python-runbooks) ezért válassza **Python 2** a **Runbook típusa**.
1. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="add-code-to-the-runbook"></a>Adja hozzá a kódot a runbookba

Most adja hozzá a "Hello World" szöveg nyomtatása egy egyszerű parancs:

```python
print("Hello World!")
```

Kattintson a **mentése** , mentse a forgatókönyvet.

## <a name="test-the-runbook"></a>A runbook tesztelése

Mielőtt közzéteszi a runbookot, hogy éles üzemben is elérhető legyen, tesztelnie kell, hogy biztosan jól működik-e. Egy forgatókönyv tesztelésekor a **Piszkozat** verziót futtatja, és interaktív módon megtekinti a kimenetét.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.
1. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.
1. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), és megjelenik annak állapota.
   A feladat állapota kezdetben *Várólistán*. Ez azt jelöli, hogy egy felhőben lévő runbook-feldolgozó elérhetővé válására vár. Elérésűből *kezdő* Ha egy feldolgozó elvállalja a feladatot, majd *futó* amikor a runbook elkezd futni.
1. Amikor a forgatókönyv feladat befejeződik, megjelenik a kimenete. Ebben az esetben kell megjelennie *Hello World*.
1. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="publish-and-start-the-runbook"></a>Közzététel és a forgatókönyv indítása

A létrehozott runbook még mindig piszkozat módban van. Üzemi környezetben való futtatás előtt közzé kell.
Amikor közzétesz egy runbookot, írja felül a meglévő közzétett verziót a piszkozattal.
Ebben az esetben még nem rendelkezik egy közzétett verzió, mert az újonnan létrehozott, a runbookot.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.
1. Ha balra görgetve megtekinti a forgatókönyvet a a **Runbookok** panelen most megjelenít egy **szerzői állapot** , **közzétett**.
1. Görgessen vissza jobbra a panel megtekintéséhez **MyFirstRunbook-Python**.
   A felül látható lehetőségekkel elindíthatjuk és megtekinthetjük a runbookot, ütemezhetjük egy későbbi időpontban való indításra, vagy létrehozhatunk egy [webhookot](automation-webhooks.md), amely segítségével elindítható a runbook egy HTTP-hívással.
1. El szeretné indítani a runbookot, tehát kattintson **Start** majd **Ok** amikor a Runbook indítása panel megnyílik.
1. Egy feladatpanel a létrehozott runbook-feladathoz. Zárja be ezt a panelt, de ebben az esetben, nyitva hagyja, így megtekintheti a feladat előrehaladását.
1. A feladat állapota a **feladat összegzése** és megegyezik-e az állapot, a tesztelt, amikor a runbook vonatkozott.
1. Ha a forgatókönyv a *Befejezve* állapotot mutatja, kattintson a **Kimenet** lehetőségre. A Kimenet panel megnyílik, és láthatja a *Hello World*.
1. Zárja be a Kimenet panelt.
1. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. A kimeneti streamben csak a *Hello World* eredményt látja, de itt megjelenhetnek egyéb streamek is egy runbook-feladatból, mint például a Részletes vagy a Hiba, ha a forgatókönyv ezekbe ír.
1. Zárja be a Streamek és a feladat panel, térjen vissza a MyFirstRunbook-Python panelen.
1. A forgatókönyv Feladatok paneljének megnyitásához kattintson a **Feladatok** lehetőségre. Ez felsorolja az összes, a forgatókönyv által létrehozott feladatot. Egy feladat csak egyszer szerepel a listán, mert csak egyszer futtatta a feladatot.
1. Erre a feladatra kattintva megnyithatja ugyanazt a Feladat panelt, amelyet már látott a runbook elindításakor. Ez lehetővé teszi, hogy az időben visszamenve megtekintse egy adott forgatókönyvhöz létrehozott összes feladat részleteit.

## <a name="add-authentication-to-manage-azure-resources"></a>Hitelesítés hozzáadása Azure-erőforrások kezeléséhez

Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen.
Az Azure-erőforrások kezeléséhez, a szkript rendelkezik, az Automation-fiók a hitelesítő adatok használatával történő hitelesítéshez. Könnyebben, használhatja a [Azure Automation segédprogram csomag](https://github.com/azureautomation/azure_automation_utility) hitelesítéséhez, és használhassa az Azure-erőforrások könnyebb.

> [!NOTE]
> Az Automation-fiók létrehozása az egyszerű szolgáltatást, hogy a Futtatás mint tanúsítvány.
> Az automation-fiók nem jött létre a szolgáltatásnevet az, ha Ön ismertetett módon hitelesítheti [a Pythonhoz készült Azure kezelési Kódtáraival való hitelesítéssel](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate).

1. A szöveges szerkesztő megnyitásához **szerkesztése** a MyFirstRunbook-Python panelen.

2. Adja hozzá a következő kódot a hitelesítéshez az Azure-bA:

   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
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
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Adja hozzá a kódot Python-Compute-ügyfél létrehozása és a virtuális gép indítása

Azure virtuális gépek dolgozni, hozzon létre egy példányt a [Pythonhoz készült Azure Compute ügyfél](https://docs.microsoft.com/python/api/azure.mgmt.compute.computemanagementclient?view=azure-python).

A Compute-ügyfél használatával indítsa el a virtuális Gépet. Adja hozzá a következő kódot a runbookba:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
azure_credential,
  str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start("MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Ahol _MyResourceGroup_ a virtuális Gépet tartalmazó erőforráscsoport neve és _TestVM_ elindítja a virtuális gép neve.

Tesztelje, és futtatja a runbookot ismételt használatával ellenőrizheti, hogy elindítja a virtuális Gépet.

## <a name="use-input-parameters"></a>Bemeneti paraméterek használata

A runbook jelenleg használ változtatható értékek az erőforráscsoportot és a virtuális gép nevét.
Most adjuk hozzá a kódot, amely lekérdezi ezeket az értékeket a bemeneti paraméterekből.

Használja a `sys.argv` változó a paraméterértékeket.
Adja hozzá a következő kódot a runbook egymás után azonnal `import` utasításokat:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Ezzel importálja a `sys` modult, és létrehoz két változót, amely tárolja az erőforráscsoportot és a virtuális gép neve.
Figyelje meg, hogy az elem az argumentumlista `sys.argv[0]`, a parancsfájl nevét, és nem a felhasználó által megadott.

Most a runbookot, hogy a bemeneti paraméter értékét használja változtatható értékek helyett az utolsó két sornyi módosíthatja:

```python
async_vm_start = compute_client.virtual_machines.start(resource_group_name, vm_name)
async_vm_start.wait()
```

Python-runbook indításakor (vagy a **teszt** lap vagy egy közzétett runbookban), adhatja meg az értékeket a paraméterek a **Runbook indítása** lap **paraméterek** .

Írjon be egy értéket az első mezőt elindítása után egy második fog jelenik meg, és így tovább, úgy, hogy annyi paraméter értékeket szükség szerint adhat meg.

Az értékek a következők érhető el, mint a parancsfájlt a `sys.argv` tömb, mint az imént hozzáadott kódot.

Adja meg az első paraméter értékeként az erőforráscsoport nevét, és a virtuális gép indult el, mert a második paraméter neve.

![Adja meg a paraméterértékek](media/automation-first-runbook-textual-python/runbook-python-params.png)

Kattintson a **OK** a runbook elindításához. A runbook fut, és a megadott virtuális gép elindul.

## <a name="next-steps"></a>További lépések

- A PowerShell-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-forgatókönyvem](automation-first-runbook-textual-powershell.md).
- A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
- A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)
- További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
- Azure-ban a Python fejlesztésével kapcsolatos tudnivalókért lásd: [Azure Python-fejlesztőknek](https://docs.microsoft.com/python/azure/?view=azure-python)
- Minta Python 2-runbookok megtekintéséhez lásd a [Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)
