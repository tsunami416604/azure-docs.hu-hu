---
title: Az első Python-runbook Azure Automation
description: Oktatóanyag, amely végigvezeti egy egyszerű Python-runbook létrehozásán, tesztelésén és közzétételén.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/19/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 626f446c18acf1f07f458fb1b4238f182546e479
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596926"
---
# <a name="my-first-python-runbook"></a>Az első Python-runbook

> [!div class="op_single_selector"]
> - [Grafikus](automation-first-runbook-graphical.md)
> - [PowerShell](automation-first-runbook-textual-powershell.md)
> - [PowerShell-munkafolyamat](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

Ez az oktatóanyag végigvezeti egy [Python-runbook](automation-runbook-types.md#python-runbooks) létrehozásán Azure Automationban. Első lépésként egy egyszerű runbook tesztelheti és közzéteheti. Ezután módosítja a runbookot, hogy ténylegesen kezeljen Azure-erőforrásokat, ebben az esetben elindítson egy Azure-beli virtuális gépet. Végül pedig runbook paraméterek hozzáadásával hatékonyabbá teheti a runbook.

> [!NOTE]
> Egy webhook használata a Python-runbook elindításához nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

- Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Automation-fiók](automation-offering-get-started.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
- Egy Azure virtuális gép. Ezt a gépet leállítja és elindítja, tehát ne olyan virtuális gépet használjon, amely élesben működik.

## <a name="create-a-new-runbook"></a>Új runbook létrehozása

Első lépésként hozzon létre egy egyszerű runbook, amely kiírja a *"Helló világ!" alkalmazás*szöveget.

1. Az Azure Portalon nyissa meg az Automation-fiókját.

    Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. Ezek nagy része az új Automation-fiókhoz automatikusan hozzáadott modul. Rendelkeznie kell az [előfeltételek](#prerequisites) között említett hitelesítő adategységgel is.<br>

1. Kattintson a **Runbookok** elemre a **FOLYAMATKEZELÉS** területen a runbookok listájának megnyitásához.
1. Válassza a **+ Runbook hozzáadása** lehetőséget egy új runbook létrehozásához.
1. Adja meg a runbook a *MyFirstRunbook-Python*nevet.
1. Ebben az esetben létre fog hozni egy [Python-runbook](automation-runbook-types.md#python-runbooks) , ezért válassza a **Python 2** lehetőséget a **runbook típusnál**.
1. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="add-code-to-the-runbook"></a>Kód hozzáadása a runbook

Most vegyen fel egy egyszerű parancsot a ""Helló világ!"alkalmazás" szöveg kinyomtatásához:

```python
print("Hello World!")
```

A runbook mentéséhez kattintson a **Save (Mentés** ) gombra.

## <a name="test-the-runbook"></a>A runbook tesztelése

Mielőtt közzéteszi a runbookot, hogy éles üzemben is elérhető legyen, tesztelnie kell, hogy biztosan jól működik-e. Egy forgatókönyv tesztelésekor a **Piszkozat** verziót futtatja, és interaktív módon megtekinti a kimenetét.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.
1. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.
1. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), és megjelenik annak állapota.
   A feladat állapota kezdetben *Várólistán*. Ez azt jelöli, hogy egy felhőben lévő runbook-feldolgozó elérhetővé válására vár. A művelet akkor *indul* el, amikor egy feldolgozó kiállítja a feladatot, majd *fut* , amikor a runbook ténylegesen elindul.
1. Amikor a forgatókönyv feladat befejeződik, megjelenik a kimenete. Ebben az esetben a *"Helló világ!" alkalmazást*kell látnia.
1. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="publish-and-start-the-runbook"></a>A runbook közzététele és elindítása

A létrehozott runbook még mindig Piszkozat módban van. Az éles környezetben való futtatás előtt közzé kell tennie.
Amikor közzétesz egy runbook, felülírja a meglévő közzétett verziót a Piszkozat verziójával.
Ebben az esetben még nem rendelkezik közzétett verzióval, mert most létrehozta a runbook.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.
1. Ha balra görgetve megtekinti a runbook a **runbookok** panelen, a **közzétett** **szerzői állapotot** jeleníti meg.
1. Görgessen vissza jobbra a **MyFirstRunbook-Python**ablaktáblájának megtekintéséhez.
   A felső beállítások lehetővé teszik, hogy elindítsa a runbook, megtekintse a runbook, vagy ütemezze azt úgy, hogy később elkezdődjön.
2. El szeretné indítani a runbook, kattintson a **Start** gombra, majd kattintson az **OK** gombra a runbook indítása panel megnyitásakor.
3. Megnyílik a létrehozott runbook-feladatokhoz tartozó feladatok ablaktábla. lezárhatja ezt a panelt, de ebben az esetben hagyja megnyitva, hogy megtekintse a feladatok állapotát.
1. A feladatok állapota megjelenik a **feladatok összegzése** területen, és megfelel a runbook tesztelésekor megtekintett állapotoknak.
2. Ha a forgatókönyv a *Befejezve* állapotot mutatja, kattintson a **Kimenet** lehetőségre. Megnyílik a kimenet panel, és megtekintheti a *"Helló világ!" alkalmazás*.
3. Zárja be a Kimenet panelt.
4. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. A kimeneti streamben csak a *Hello World* eredményt látja, de itt megjelenhetnek egyéb streamek is egy runbook-feladatból, mint például a Részletes vagy a Hiba, ha a forgatókönyv ezekbe ír.
5. A MyFirstRunbook-Python panelre való visszatéréshez zárjuk be a streamek panelt és a feladatok panelt.
6. A forgatókönyv Feladatok paneljének megnyitásához kattintson a **Feladatok** lehetőségre. Ez felsorolja az összes, a forgatókönyv által létrehozott feladatot. Egy feladat csak egyszer szerepel a listán, mert csak egyszer futtatta a feladatot.
7. Erre a feladatra kattintva megnyithatja ugyanazt a Feladat panelt, amelyet már látott a runbook elindításakor. Ez lehetővé teszi, hogy az időben visszamenve megtekintse egy adott forgatókönyvhöz létrehozott összes feladat részleteit.

## <a name="add-authentication-to-manage-azure-resources"></a>Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez

Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen.
Az Azure-erőforrások kezeléséhez a szkriptnek hitelesítenie kell az Automation-fiókja hitelesítő adataival. Ha segítségre van szüksége, használhatja a [Azure Automation segédprogram-csomagot](https://github.com/azureautomation/azure_automation_utility) , hogy könnyebb legyen az Azure-erőforrások hitelesítése és használata.

> [!NOTE]
> Az Automation-fióknak létre kell hoznia az egyszerű szolgáltatásnév szolgáltatással, hogy egy futtató tanúsítvány legyen.
> Ha az Automation-fiókja nem az egyszerű szolgáltatással lett létrehozva, akkor a hitelesítéshez használja a [hitelesítés az Azure felügyeleti kódtárak a Pythonhoz](/azure/python/python-sdk-azure-authenticate)című cikkben ismertetett módon.

1. A MyFirstRunbook-Python panel **Szerkesztés** gombjára kattintva nyissa meg a szöveges szerkesztőt.

2. Adja hozzá a következő kódot az Azure-hitelesítéshez:

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

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Kód hozzáadása a Python számítási ügyfél létrehozásához és a virtuális gép elindításához

Az Azure-beli virtuális gépekkel való együttműködéshez hozzon létre egy példányt a [Pythonhoz készült Azure számítási ügyfélből](https://docs.microsoft.com/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient).

A virtuális gép elindításához használja a számítási ügyfelet. Adja hozzá a következő kódot a runbook:

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

Ahol a _MyResourceGroup_ a virtuális gépet tartalmazó erőforráscsoport neve, a _TestVM_ pedig annak a virtuális gépnek a neve, amelyet el szeretne indítani.

Tesztelje és futtassa újra a runbook, és ellenőrizze, hogy elindul-e a virtuális gép.

## <a name="use-input-parameters"></a>Bemeneti paraméterek használata

A runbook jelenleg rögzített értékeket használ az erőforráscsoport és a virtuális gép neveihez.
Most vegyünk fel egy kódot, amely beolvassa ezeket az értékeket a bemeneti paraméterekből.

A paraméterek értékeinek lekéréséhez használja a `sys.argv` változót.
Adja hozzá a következő kódot a runbook közvetlenül a többi `import` utasítás után:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Ezzel importálja a `sys` modult, és két változót hoz létre az erőforráscsoport és a virtuális gépek neveinek tárolására.
Figyelje meg, hogy az argumentumok listájának (`sys.argv[0]`) eleme a parancsfájl neve, és a felhasználó nem adja meg a bemenetet.

Most módosíthatja a runbook utolsó két sorát úgy, hogy a bemeneti paraméterek értékeit a rögzített értékek használata helyett használja:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Amikor elindít egy Python-runbook (akár a **teszt** oldalon, akár közzétett runbook), megadhatja a paraméterek értékeit a **Start runbook** ( **Paraméterek**) lapon.

Miután megkezdte egy érték beírását az első mezőbe, egy másodperc jelenik meg, és így tovább, így tetszőleges számú paraméter-értéket adhat meg.

Az értékek a parancsfájlban `sys.argv` tömbként érhetők el, mint az imént hozzáadott kódban.

Adja meg az erőforráscsoport nevét az első paraméter értékeként, valamint a második paraméter értékeként elindítani kívánt virtuális gép nevét.

![Adja meg a paraméter értékeit](media/automation-first-runbook-textual-python/runbook-python-params.png)

A runbook elindításához kattintson **az OK** gombra. A runbook fut, és elindítja a megadott virtuális gépet.

## <a name="error-handling-in-python"></a>Hibakezelés a Pythonban

A következő konvenciókat is használhatja a különböző streamek lekéréséhez a Python-runbookok, beleértve a **figyelmeztetéseket**, a **hibákat**és a **hibakeresési** adatfolyamokat.

```python
print("Hello World output") 
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

A következő példa egy `try...except` blokkban használt egyezményt mutatja be.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print 'ERROR: Handling run-time error:', detail
```

> [!NOTE]
> a **sys. stderr** Azure Automation nem támogatott.

## <a name="next-steps"></a>Következő lépések

- A PowerShell-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-forgatókönyvem](automation-first-runbook-textual-powershell.md).
- A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
- A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)
- További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
- Az Azure Python-fejlesztésével kapcsolatos további információkért lásd: az [Azure Python-fejlesztőknek](/azure/python/)
- A Python 2 runbookok megtekintéséhez tekintse meg a következőt: [Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)
