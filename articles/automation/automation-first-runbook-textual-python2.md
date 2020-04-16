---
title: Az első Python runbook az Azure Automationben
description: Oktatóanyag, amely végigvezeti egy egyszerű Python-runbook létrehozásán, tesztelésén és közzétételén.
services: automation
ms.subservice: process-automation
ms.date: 03/19/2019
ms.topic: conceptual
ms.openlocfilehash: b788e5bda65702305ed6f6b001b57c28939aa875
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405909"
---
# <a name="my-first-python-runbook"></a>Az első Python-runbookom

> [!div class="op_single_selector"]
> - [Grafikus](automation-first-runbook-graphical.md)
> - [PowerShell](automation-first-runbook-textual-powershell.md)
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

1. Válassza **a Runbookok** **a Folyamatautomatizálás** csoportban a runbookok listájának megnyitásához.
1. Új runbook létrehozásához válassza **a Runbook hozzáadása** lehetőséget.
1. Adja meg a runbook a név **MyFirstRunbook-Python**.
1. Válassza a **Python 2 lehetőséget** a **Runbook-típushoz.**
1. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="add-code-to-the-runbook"></a>Kód hozzáadása a runbookhoz

Most egy egyszerű parancsot ad hozzá a szöveg `Hello World`nyomtatásához .

```python
print("Hello World!")
```

A runbook mentéséhez kattintson a **Mentés** gombra.

## <a name="test-the-runbook"></a>A runbook tesztelése

Mielőtt közzéteszi a runbookot, hogy éles üzemben is elérhető legyen, tesztelnie kell, hogy biztosan jól működik-e. Egy forgatókönyv tesztelésekor a Piszkozat verziót futtatja, és interaktív módon megtekinti a kimenetét.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.
1. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.
1. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), és megjelenik annak állapota.
   A feladat állapota várólistára kerülése, jelezve, hogy a felhőben egy runbook-dolgozó elérhetővé válik. A kezdő, amikor egy dolgozó azt állítja, a feladat, majd fut, amikor a runbook ténylegesen elindul.
1. Amikor a forgatókönyv feladat befejeződik, megjelenik a kimenete. Ebben az esetben látnia kell a . `Hello World`
1. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="publish-and-start-the-runbook"></a>A runbook közzététele és indítása

A létrehozott runbook még mindig Piszkozat módban van. Közzé kell tennie, mielőtt éles környezetben futtathatja volna.
Runbook közzétételekor felülírja a meglévő közzétett verziót a vázlatverzióval.
Ebben az esetben még nem rendelkezik közzétett verzióval, mert most hozta létre a runbookot.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.
1. Ha balra görgetve megtekinti a runbookot a **Runbookok** lapon, meg kell jelennie a **Közzétett** **szerzői állapotnak.**
1. Görgessen vissza jobbra a **MyFirstRunbook-Python**ablaktáblájának megtekintéséhez.
   A beállítások a felső lehetővé teszi, hogy indítsa el a runbook, tekintse meg a runbookot, vagy ütemezése, hogy elindulval egy időben a jövőben.
2. Kattintson a **Start** gombra, majd az **OK** gombra, amikor megnyílik a Runbook indítása panel.
3. Meg van nyitva egy feladat ablaktábla a létrehozott runbook-feladathoz. Bezárhatja ezt az ablaktáblát, de hagyjuk nyitva, hogy megnézhesse a feladat előrehaladását.
1. A feladat állapota megjelenik a **Feladat összegzése,** és megfelel az állapotok, hogy látta, amikor tesztelte a runbook.
2. Miután a runbook állapota látható, kattintson **a Kimenet**gombra. Megnyílik a Kimenet ablaktábla, `Hello World`ahol látható.
3. Zárja be a Kimenet panelt.
4. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. Csak a `Hello World` kimeneti adatfolyamban jelenik meg. Ez az ablaktábla azonban más streameket is megjeleníthet egy runbook-feladathoz, például a részletes és a hiba, ha a runbook ír nekik.
5. Zárja be a Streams és a Feladat ablaktáblát a MyFirstRunbook-Python ablaktáblára való visszatéréshez.
6. Kattintson **a Feladatok** gombra a runbook Feladatok lapjának megnyitásához. Ez a lap a runbook által létrehozott összes feladatot megjeleníti. Egy feladat csak egyszer szerepel a listán, mert csak egyszer futtatta a feladatot.
7. Erre a feladatra kattintva megnyithatja ugyanazt a Feladat panelt, amelyet már látott a runbook elindításakor. Ez az ablaktábla lehetővé teszi, hogy visszatérjen az időben, és tekintse meg az adott runbookhoz létrehozott feladat részleteit.

## <a name="add-authentication-to-manage-azure-resources"></a>Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez

Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen.
Ehhez a parancsfájlnak hitelesítenie kell az Automation-fiók hitelesítő adatait. Az [Azure Automation segédprogramcsomag](https://github.com/azureautomation/azure_automation_utility) segítségével egyszerűbben hitelesítheti és kezelheti az Azure-erőforrásokat.

> [!NOTE]
> Az Automation-fióknak létre kell hoznia a szolgáltatásegyszerű szolgáltatással ahhoz, hogy futtasson tanúsítványt.
> Ha az Automation-fiók nem az egyszerű szolgáltatással lett létrehozva, hitelesítheti a Hitelesítés az Azure Management Libraries for Python használatával című dokumentumban [leírtak](/azure/python/python-sdk-azure-authenticate)szerint.

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

A számítási ügyfél segítségével indítsa el a virtuális gép. Adja hozzá a következő kódot a runbookhoz:

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

Hol `MyResourceGroup` található a virtuális gép tartalmazza az erőforráscsoport neve, és `TestVM` a virtuális gép nevét, amelyet el szeretne indítani.

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

Amikor elindít egy Python-runbookot (akár a Teszt ablaktáblán, akár közzétett runbookként), megadhatja a paraméterek értékeit a Runbook indítása lap **Parameters (Runbook indítása)** területén.

Miután elkezd beírni egy értéket az első mezőbe, egy másodperc jelenik meg, és így tovább, így annyi paraméterértéket adhat meg, amennyi szükséges.

Az értékek az imént hozzáadott kódhoz képest a `sys.argv` tömb parancsfájlja számára érhetők el.

Adja meg az erőforráscsoport nevét az első paraméter értékeként, és a virtuális gép nevét a második paraméter értékeként.

![Paraméterértékek megadása](media/automation-first-runbook-textual-python/runbook-python-params.png)

Kattintson az **OK** gombra a runbook elindításához. A runbook fut, és elindítja a megadott virtuális gép.

## <a name="error-handling-in-python"></a>Hibakezelés pythonban

A következő konvenciók segítségével is lekérheti a python runbookok különböző adatfolyamait, beleértve a FIGYELMEZTETÉST, a HIBÁT és a DEBUG-adatfolyamokat.

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
> Az Azure Automation `sys.stderr`nem támogatja a .

## <a name="next-steps"></a>További lépések

- A PowerShell-forgatókönyvek használatának megismeréséhez tekintse meg a következőt: [Az első PowerShell-runbookom](automation-first-runbook-textual-powershell.md).
- A grafikus runbookok első lépései [Az Első grafikus runbook című témakörben](automation-first-runbook-graphical.md)látható.
- A PowerShell-alapú munkafolyamat-runbookok első lépéseit [Az első PowerShell-alapú munkafolyamat-runbookom](automation-first-runbook-textual.md) című témakör ismerteti.
- Ha többet szeretne tudni a runbook-típusokról, azok előnyeiről és korlátairól, olvassa el az [Azure Automation runbook-típusok című témakört.](automation-runbook-types.md)
- Ha többet szeretne tudni az Azure-beli Pythonnal való fejlesztésről, olvassa el [az Azure python-fejlesztőknek témakört.](/azure/python/)
- A Python 2 runbookok mintájának megtekintéséhez tekintse meg az [Azure Automation GitHub.](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)
