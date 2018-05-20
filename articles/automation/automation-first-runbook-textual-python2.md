---
title: Az első Python-runbook az Azure Automationben
description: Az oktatóanyag végigvezeti a létrehozása, tesztelése és egy egyszerű Python runbook közzététele.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cfeef05df63cd08db48d555d7ca6f12969b533cb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="my-first-python-runbook"></a>Az első Python-forgatókönyv

> [!div class="op_single_selector"]
> * [Grafikus](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-munkafolyamat](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

Ez az oktatóanyag bemutatja, hogyan kell létrehozni egy [Python runbook](automation-runbook-types.md#python-runbooks) az Azure Automationben. Először egy egyszerű runbookot, tesztelése és tehetők közzé. Ezután módosítja a runbookot, hogy ténylegesen kezeljen Azure-erőforrásokat, ebben az esetben elindítson egy Azure-beli virtuális gépet. Végül akkor a runbook robusztusabb runbook paraméterek hozzáadásával.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-offering-get-started.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. Ezt a gépet leállítja és elindítja, tehát ne olyan virtuális gépet használjon, amely élesben működik.

## <a name="create-a-new-runbook"></a>Hozzon létre egy új runbookot

Ön először hozzon létre egy egyszerű runbookot, amely a szöveg *Hello World*.

1. Az Azure Portalon nyissa meg az Automation-fiókját.

    Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. Ezek nagy része az új Automation-fiókhoz automatikusan hozzáadott modul. Rendelkeznie kell az [előfeltételek](#prerequisites) között említett hitelesítő adategységgel is.<br>

1. Kattintson a **Runbookok** elemre a **FOLYAMATKEZELÉS** területen a runbookok listájának megnyitásához.
2. Válassza ki **+ Hozzáadás runbook** számára hozzon létre egy új runbookot.
3. Nevezze el a runbook a *MyFirstRunbook-Python*.
4. Ebben az esetben fog létrehozni egy [Python runbook](automation-runbook-types.md#python-runbooks) ezért **Python 2** a **runbooktípusba**.
5. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="add-code-to-the-runbook"></a>Kód hozzáadása a runbookhoz

Most adja hozzá a "Hello, World" szöveg nyomtatás egyszerű parancsot:

```python
print("Hello World!")
```

Kattintson a **mentése** menteni a runbookot.

## <a name="test-the-runbook"></a>A runbook tesztelése

Mielőtt közzéteszi a runbookot, hogy éles üzemben is elérhető legyen, tesztelnie kell, hogy biztosan jól működik-e. Egy forgatókönyv tesztelésekor a **Piszkozat** verziót futtatja, és interaktív módon megtekinti a kimenetét.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.
2. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.
3. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), és megjelenik annak állapota.
   A feladat állapota kezdetben *Várólistán*. Ez azt jelöli, hogy egy felhőben lévő runbook-feldolgozó elérhetővé válására vár. Kerül át *indítása* dolgozó jogcímeket a feladatot, amikor, majd *futtató* amikor a runbook ténylegesen elindul.
4. Amikor a forgatókönyv feladat befejeződik, megjelenik a kimenete. Ebben az esetben kell megjelennie *Hello World*.
5. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="publish-and-start-the-runbook"></a>Közzététele, és elindítja a runbookot

A runbook létrehozott még mindig vázlat módban van. Éles környezetben futtatása előtt tegye közzé kell.
Egy runbook közzétételekor a vázlatként megjelölt verziót a felülírja a meglévő közzétett verziót.
Ebben az esetben még nem rendelkezik közzétett verzióval, mert a runbook újonnan létrehozott.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.
2. Ha a runbook megtekintése balra a **Runbookok** ablaktáblán, akkor megjelenik egy **szerzői állapot** a **közzétett**.
1. Vissza a ablaktábla megtekintéséhez görgessen **MyFirstRunbook-Python**.
   A felül látható lehetőségekkel elindíthatjuk és megtekinthetjük a runbookot, ütemezhetjük egy későbbi időpontban való indításra, vagy létrehozhatunk egy [webhookot](automation-webhooks.md), amely segítségével elindítható a runbook egy HTTP-hívással.
2. A runbook indításához, kattintson a kívánt **Start** , majd **Ok** a runbook meghívása panel megnyitásakor.
3. A feladatok ablaktábla a runbook-feladat létrehozott van megnyitva. Zárja be az ezen az ablaktáblán, de ebben az esetben hagyja nyitva, figyelheti a feladat előrehaladását.
4. A feladat állapota látható **feladat összegzése** és az állapot, amikor Ön tesztelése a runbook látott egyezik.
5. Ha a forgatókönyv a *Befejezve* állapotot mutatja, kattintson a **Kimenet** lehetőségre. A kimeneti ablaktábla már meg van nyitva, és megtekintheti a *Hello World*.
6. Zárja be a Kimenet panelt.
7. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. A kimeneti streamben csak a *Hello World* eredményt látja, de itt megjelenhetnek egyéb streamek is egy runbook-feladatból, mint például a Részletes vagy a Hiba, ha a forgatókönyv ezekbe ír.
8. Zárja be a adatfolyamok és a feladatok ablaktábla a MyFirstRunbook-Python tartalmazó ablaktáblájához való visszatéréshez.
9. A forgatókönyv Feladatok paneljének megnyitásához kattintson a **Feladatok** lehetőségre. Ez felsorolja az összes, a forgatókönyv által létrehozott feladatot. Egy feladat csak egyszer szerepel a listán, mert csak egyszer futtatta a feladatot.
10. Erre a feladatra kattintva megnyithatja ugyanazt a Feladat panelt, amelyet már látott a runbook elindításakor. Ez lehetővé teszi, hogy az időben visszamenve megtekintse egy adott forgatókönyvhöz létrehozott összes feladat részleteit.

## <a name="add-authentication-to-manage-azure-resources"></a>Hitelesítés az Azure-erőforrások kezeléséhez hozzáadása

Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen.
Az Azure-erőforrások kezeléséhez a parancsfájlnak a hitelesítést a hitelesítő adatokat a [Automation-fiók](automation-offering-get-started.md).

> [!NOTE]
> Az Automation-fiók létrehozása az egyszerű szolgáltatást, hogy a runas tanúsítványt.
> Ha az automation-fiók létrehozása nem a szolgáltatás egyszerű, képes hitelesíteni című részben ismertetett módon [hitelesítés az Azure felügyeleti könyvtárakkal Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate).

1. A szöveges-szerkesztő megnyitásához **szerkesztése** a MyFirstRunbook-Python panelen.
1. Adja hozzá a következő kódot a hitelesítéshez az Azure-bA:
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

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Adja hozzá a kódot, hogy a Python számítási ügyfél létrehozása és a virtuális gép elindítása

Azure virtuális gépek használatához hozzon létre egy példányát a [Azure számítás-ügyfél Python](https://docs.microsoft.com/python/api/azure.mgmt.compute.computemanagementclient?view=azure-python).

A számítási ügyfél használatával indítsa el a virtuális Gépet. A runbook adja hozzá a következő kódot:

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

Ha _MyResourceGroup_ neve, a virtuális Gépet tartalmazó erőforráscsoportot és _TestVM_ elindítja a virtuális gép neve. 

Tesztelése, és futtassa a runbook ismételt használatával ellenőrizheti, hogy a virtuális gép elindul.

## <a name="use-input-parameters"></a>Bemeneti paramétereket használni

A runbook jelenleg használ változtatható értékek a virtuális gép és az erőforráscsoport nevét.
Most adjuk hozzá a kódot, amely lekérdezi ezeket az értékeket a bemeneti paramétereket.

Használja a `sys.argv` -változó lekérdezésével a paraméterértékek.
Közvetlenül a másik után adja hozzá a következő kódot a runbook `import` utasításokat:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Ez a importálja a `sys` modul, és hoz létre két változót ahhoz, hogy az erőforráscsoport és a virtuális gép neve.
Figyelje meg, hogy az elem az argumentumlista `sys.argv[0]`, a parancsfájl nevét, és nem a felhasználó által.

Az utolsó két sort a runbook értékeket szeretné használni a bemeneti paraméter nem változtatható értékek helyett most már módosíthatja:

```python
async_vm_start = compute_client.virtual_machines.start(resource_group_name, vm_name)
async_vm_start.wait()
```

Python runbook indításakor (vagy a **teszt** lap, vagy a közzétett runbookok), adhatja meg az értékeket a paraméterek a **runbook meghívása** lapon az **paraméterek** .

Írjon be egy értéket az első mezőben elindítása után egy második fog jelenik meg, és így tovább, úgy, hogy a lehető legtöbb paraméter értékeket szükség szerint adhat meg.

Értékei elérhetők, a parancsfájl a `sys.argv` tömb, mint az előzőekben adott hozzá a kódot.

Adja meg az első paraméter értékeként az erőforráscsoport nevét, és a virtuális gép elindításához, a második paraméter neve.

![Adja meg a paramétert értékek](media/automation-first-runbook-textual-python/runbook-python-params.png)

Kattintson a **OK** a runbook elindításához. A runbook fut, és a megadott virtuális gép elindul.

## <a name="next-steps"></a>További lépések

* A PowerShell-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-forgatókönyvem](automation-first-runbook-textual-powershell.md).
* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)
* További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
* Az Azure Python fejlesztésével kapcsolatos további tudnivalókért lásd: [Azure Python-fejlesztőknek](https://docs.microsoft.com/python/azure/?view=azure-python).
* Példa Python 2 runbookok megtekintése: a [Azure Automation GitHub](https://docs.microsoft.com/python/azure/?view=azure-python).
