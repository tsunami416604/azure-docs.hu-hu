---
title: Az első Python runbook az Azure Automationben
description: Oktatóanyag, amely végigvezeti egy egyszerű Python-runbook létrehozásán, tesztelésén és közzétételén.
services: automation
ms.subservice: process-automation
ms.date: 03/19/2019
ms.topic: conceptual
ms.openlocfilehash: b17a0403a3b2a3ff8c3586ed26a4b833db54922d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75365937"
---
# <a name="my-first-python-runbook"></a>Az első Python-runbookom

> [!div class="op_single_selector"]
> - [Grafikus](automation-first-runbook-graphical.md)
> - [Powershell](automation-first-runbook-textual-powershell.md)
> - [PowerShell-munkafolyamat](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

Ez az oktatóanyag bemutatja egy [Python runbook](automation-runbook-types.md#python-runbooks) létrehozását az Azure Automationben. Egy egyszerű runbook, amely tesztelése és közzététele egy egyszerű runbook. Ezután módosítja a runbookot, hogy ténylegesen kezeljen Azure-erőforrásokat, ebben az esetben elindítson egy Azure-beli virtuális gépet. Végül a runbook-paraméterek hozzáadásával robusztusabbá teszi a runbook-paramétereket.

> [!NOTE]
> Egy webhook használata a Python runbook indításához nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

- Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Automation-fiók](automation-offering-get-started.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
- Egy Azure virtuális gép. Ezt a gépet leállítja és elindítja, tehát ne olyan virtuális gépet használjon, amely élesben működik.

## <a name="create-a-new-runbook"></a>Új runbook létrehozása

Először hozzon létre egy egyszerű runbook, amely kiírja a szöveget *Hello World*.

1. Az Azure Portalon nyissa meg az Automation-fiókját.

    Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. Ezek nagy része az új Automation-fiókhoz automatikusan hozzáadott modul. Rendelkeznie kell az [előfeltételek](#prerequisites) között említett hitelesítő adategységgel is.<br>

1. Kattintson a **Runbookok** elemre a **FOLYAMATKEZELÉS** területen a runbookok listájának megnyitásához.
1. Válassza a + Runbook hozzáadása új runbook létrehozásához válassza a **+ Runbook hozzáadása** lehetőséget.
1. Adja meg a runbook a név *MyFirstRunbook-Python*.
1. Ebben az esetben egy [Python runbookot](automation-runbook-types.md#python-runbooks) fog létrehozni, ezért válassza a **Python 2-t** a **Runbook típushoz.**
1. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="add-code-to-the-runbook"></a>Kód hozzáadása a runbookhoz

Most adjunk hozzá egy egyszerű parancsot, hogy nyomtassa ki a szöveget "Hello World":

```python
print("Hello World!")
```

A runbook mentéséhez kattintson a **Mentés** gombra.

## <a name="test-the-runbook"></a>A runbook tesztelése

Mielőtt közzéteszi a runbookot, hogy éles üzemben is elérhető legyen, tesztelnie kell, hogy biztosan jól működik-e. Egy forgatókönyv tesztelésekor a **Piszkozat** verziót futtatja, és interaktív módon megtekinti a kimenetét.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.
1. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.
1. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), és megjelenik annak állapota.
   A feladat állapota kezdetben *Várólistán*. Ez azt jelöli, hogy egy felhőben lévő runbook-feldolgozó elérhetővé válására vár. *A kezdő,* amikor egy dolgozó azt állítja, a feladat, majd *fut,* amikor a runbook ténylegesen elindul.
1. Amikor a forgatókönyv feladat befejeződik, megjelenik a kimenete. Ebben az esetben látnia kell a *Hello World*-
1. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="publish-and-start-the-runbook"></a>A runbook közzététele és indítása

A létrehozott runbook továbbra is vázlat módban van. Közzé kell tennie, mielőtt éles környezetben futtathatja volna.
Runbook közzétételekor felülírja a meglévő közzétett verziót a Piszkozat verzióval.
Ebben az esetben még nem rendelkezik közzétett verzióval, mert most hozta létre a runbookot.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.
1. Ha balra görgetve tekinti meg a runbookot a **Runbooks** ablaktáblán, akkor megjelenik a **Közzétett** **szerző állapota.**
1. Görgessen vissza jobbra a **MyFirstRunbook-Python**ablaktáblájának megtekintéséhez.
   A lehetőségek a felső lehetővé teszi számunkra, hogy indítsa el a runbook, tekintse meg a runbook, vagy ütemezése, hogy elindulval egy időben a jövőben.
2. El szeretné indítani a runbookot, ezért kattintson a **Start** gombra, majd kattintson az Ok **gombra,** amikor megnyílik a Runbook indítása panel.
3. Meg van nyitva egy feladatablaktábla a létrehozott runbook-feladathoz. Bezárhatja ezt az ablaktáblát, de ebben az esetben nyitva hagyja, hogy megnézhesse a feladat előrehaladását.
1. A feladat állapota megjelenik a **Feladat összegzése,** és megfelel az állapotok, hogy látta, amikor tesztelte a runbook.
2. Ha a forgatókönyv a *Befejezve* állapotot mutatja, kattintson a **Kimenet** lehetőségre. Megnyílik a Kimenet ablaktábla, és láthatja a *Hello World*.
3. Zárja be a Kimenet panelt.
4. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. A kimeneti streamben csak a *Hello World* eredményt látja, de itt megjelenhetnek egyéb streamek is egy runbook-feladatból, mint például a Részletes vagy a Hiba, ha a forgatókönyv ezekbe ír.
5. Zárja be a Streams és a Feladat ablaktáblát a MyFirstRunbook-Python ablaktáblára való visszatéréshez.
6. A forgatókönyv Feladatok paneljének megnyitásához kattintson a **Feladatok** lehetőségre. Ez felsorolja az összes, a forgatókönyv által létrehozott feladatot. Egy feladat csak egyszer szerepel a listán, mert csak egyszer futtatta a feladatot.
7. Erre a feladatra kattintva megnyithatja ugyanazt a Feladat panelt, amelyet már látott a runbook elindításakor. Ez lehetővé teszi, hogy az időben visszamenve megtekintse egy adott forgatókönyvhöz létrehozott összes feladat részleteit.

## <a name="add-authentication-to-manage-azure-resources"></a>Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez

Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen.
Az Azure-erőforrások kezeléséhez a parancsfájlnak hitelesítenie kell az Automation-fiók hitelesítő adatait. Az [Azure Automation segédprogramcsomag](https://github.com/azureautomation/azure_automation_utility) segítségével egyszerűbben hitelesítheti és kezelheti az Azure-erőforrásokat.

> [!NOTE]
> Az Automation-fióknak létre kell hoznia a szolgáltatásegyszerű funkcióval ahhoz, hogy a Futtatás tanúsítványként legyen.
> Ha az automatizálási fiók nem jött létre az egyszerű szolgáltatással, hitelesítheti magát az Azure Management Libraries for Python hitelesítése című helyen leírt [módszerrel.](/azure/python/python-sdk-azure-authenticate)

1. Nyissa meg a szöveges szerkesztőt a MyFirstRunbook-Python ablaktábla **Szerkesztés** gombjára kattintva.

2. Adja hozzá a következő kódot az Azure-hoz való hitelesítéshez:

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

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Kód hozzáadása a Python Compute-ügyfél létrehozásához és a virtuális gép elindításához

Az Azure-beli virtuális gépekkel való munkához hozzon létre egy példányt az [Azure Compute-ügyfél pythonhoz.](https://docs.microsoft.com/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient)

A számítási ügyfél a virtuális gép elindításához. Adja hozzá a következő kódot a runbookhoz:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start(
    "MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Ahol _a MyResourceGroup_ a virtuális gép- és _TestVM-et_ tartalmazó erőforráscsoport neve, és a TestVM az elindítani kívánt virtuális gép neve.

Tesztelje és futtassa újra a runbookot, hogy lássa, hogy elindítja-e a virtuális gép.

## <a name="use-input-parameters"></a>Bemeneti paraméterek használata

A runbook jelenleg az erőforráscsoport és a virtuális gép nevéhez kódolható értékeket használ.
Most adjunk hozzá kódot, amely beszerzi ezeket az értékeket a bemeneti paraméterekből.

A `sys.argv` változó segítségével lekell adni a paraméterértékeket.
Adja hozzá a következő kódot a runbookhoz közvetlenül a többi `import` utasítás után:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Ez importálja a `sys` modult, és két változót hoz létre az erőforráscsoport és a virtuális gép nevének tárolására.
Figyelje meg, `sys.argv[0]`hogy az argumentumlista eleme a parancsfájl neve, és a felhasználó nem adja meg.

Most módosíthatja a runbook utolsó két sorát, hogy a bemeneti paraméter értékeket használja a kódolt értékek helyett:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Amikor elindít egy Python-runbookot (akár a **Teszt** lapon, akár közzétett runbookként), megadhatja a paraméterek értékeit a **Runbook indítása** lap **Parameters (Runbook indítása)** területén.

Miután elkezd beírni egy értéket az első mezőbe, egy második jelenik meg, és így tovább, így annyi paraméterértéket adhat meg, amennyi szükséges.

Az értékek az imént hozzáadott kódhoz képest tömbként érhetők el a `sys.argv` parancsfájl számára.

Adja meg az erőforráscsoport nevét az első paraméter értékeként, és a virtuális gép nevét a második paraméter értékeként.

![Paraméterértékek megadása](media/automation-first-runbook-textual-python/runbook-python-params.png)

Kattintson az **OK** gombra a runbook elindításához. A runbook fut, és elindítja a megadott virtuális gép.

## <a name="error-handling-in-python"></a>Hibakezelés pythonban

A következő konvenciók segítségével is lekérheti a különböző adatfolyamokat a Python runbookok, beleértve **a FIGYELEM**, **HIBA**és **DEBUG** streamek.

```python
print("Hello World output") 
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

A következő példa egy blokkban használt konvenciót `try...except` mutatja be.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print 'ERROR: Handling run-time error:', detail
```

> [!NOTE]
> **a sys.stderr** nem támogatott az Azure Automation ben.

## <a name="next-steps"></a>További lépések

- A PowerShell runbookok első lépései: [Az első PowerShell-runbook](automation-first-runbook-textual-powershell.md)
- A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
- A PowerShell-munkafolyamat-runbookok első lépései: [Az első PowerShell-munkafolyamat-runbook](automation-first-runbook-textual.md)
- További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
- Ha többet szeretne tudni az Azure és a Python azure-beli fejlesztéséről, olvassa el [az Azure Python-fejlesztőknek](/azure/python/)
- A Python 2 runbookok mintájának megtekintéséhez tekintse meg az [Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)
