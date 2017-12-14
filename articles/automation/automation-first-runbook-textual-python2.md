---
title: "Az első Python-runbook az Azure Automationben |} Microsoft Docs"
description: "Az oktatóanyag végigvezeti a létrehozása, tesztelése és egy egyszerű Python runbook közzététele."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: gwallace
ms.openlocfilehash: 8a7c03f10d4310bcdb13ef76b598c22912707c08
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="my-first-python-runbook"></a>Az első Python-forgatókönyv

> [!div class="op_single_selector"]
> * [Grafikus](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-munkafolyamat](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

Ez az oktatóanyag bemutatja, hogyan kell létrehozni egy [Python runbook](automation-runbook-types.md#python-runbooks) az Azure Automationben. Egy egyszerű runbookkal kezdünk, amelyet tesztelünk és közzé is teszünk, és bemutatjuk a runbook állapotának nyomon követését is. Ezután módosítjuk a runbookot, hogy ténylegesen kezeljen Azure-erőforrásokat, ebben az esetben elindítson egy Azure-beli virtuális gépet. Végül még robusztusabbá tesszük a runbookot, és runbook-paramétereket adunk hozzá.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-offering-get-started.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez.  Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. Ezt a gépet leállítjuk és elindítjuk, tehát ne olyan virtuális gépet használjon, amely élesben működik.

## <a name="create-a-new-runbook"></a>Hozzon létre egy új runbookot

Először egy egyszerű runbookot hozunk létre, amely a *Hello World* szöveget adja vissza.

1. Az Azure Portalon nyissa meg az Automation-fiókját.
   Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. Ezek nagy része az új Automation-fiókhoz automatikusan hozzáadott modul. Rendelkeznie kell az [előfeltételek](#prerequisites) között említett hitelesítő adategységgel is.
1. Kattintson a **Runbookok** csempére a forgatókönyvek listájának megnyitásához.
   ![RunbooksControl](media/automation-first-runbook-textual-python/runbooks-control-tiles.png)
1. Hozzon létre egy új runbookot a **Forgatókönyv hozzáadása**, majd az **Új forgatókönyv létrehozása** elemre kattintva.
1. Nevezze el a runbook a *MyFirstRunbook-Python*.
1. Ebben az esetben lesz, hozzon létre egy [Python runbook](automation-runbook-types.md#python-runbooks) ezért válassza **Python 2** a **runbooktípusba**.
1. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="add-code-to-the-runbook"></a>Kód hozzáadása a runbookhoz

Most azt egy egyszerű parancs a "Hello, World" szöveg Nyomtatás hozzáadása:

```python
print("Hello World!")
```

Kattintson a **mentése** menteni a runbookot.

## <a name="test-the-runbook"></a>A runbook tesztelése

Mielőtt közzétesszük a forgatókönyvet, hogy éles üzemben is elérhető legyen, tesztelnünk kell, hogy biztosan jól működik-e. Egy forgatókönyv tesztelésekor a **Piszkozat** verziót futtatja, és interaktív módon megtekinti a kimenetét.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.
   ![Teszt panel](media/automation-first-runbook-textual-python/automation-runbook-edit-controls-test.png)
1. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.
1. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), és megjelenik annak állapota.
   A feladat állapota kezdetben *Várólistán*. Ez azt jelöli, hogy egy felhőben lévő runbook-feldolgozó elérhetővé válására vár. Ezután *Indítás* állapotúra változik, ha egy feldolgozó elvállalja a feladatot, majd *Fut* állapotúra, amikor a forgatókönyv elkezd futni.
1. Amikor a forgatókönyv feladat befejeződik, megjelenik a kimenete. A mi esetünkben ez a következő: *Hello World*.
1. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="publish-and-start-the-runbook"></a>Közzététele, és elindítja a runbookot

A runbook létrehozott még mindig vázlat módban van. Az üzemi környezetben való futtatás előtt közzé kell tennünk.
Egy runbook közzétételekor a vázlatként megjelölt verziót a felülírja a meglévő közzétett verziót.
Ebben az esetben azt még nem rendelkezik közzétett verzióval, mert az imént létrehozott a runbookot.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.
   ![Közzététel gomb](media/automation-first-runbook-textual-python/automation-runbook-edit-controls-publish.png)
1. Ha most balra görgetve megtekinti a forgatókönyvet a **Forgatókönyvek** panelen, a **Közzétett** **Szerzői állapot** jelenik meg.
1. Vissza a ablaktábla megtekintéséhez görgessen **MyFirstRunbook-Python**.
   A felül látható lehetőségekkel elindíthatjuk és megtekinthetjük a runbookot, ütemezhetjük egy későbbi időpontban való indításra, vagy létrehozhatunk egy [webhookot](automation-webhooks.md), amely segítségével elindítható a runbook egy HTTP-hívással.
1. Most csak el szeretnénk indítani a runbookot, tehát kattintson az **Indítás**, majd, amikor a Runbook indítása panel megnyílik, kattintson az **OK** gombra.
1. Megnyílik egy feladatpanel a létrehozott runbook-feladathoz. Ezt a panelt bezárhatjuk, de ebben az esetben nyitva hagyjuk, hogy lássuk a feladat előrehaladását.
1. A feladat állapota a **Feladat összegzése** területen látszik, és megfelel a korábban, a forgatókönyv tesztelésekor látott állapotoknak.
1. Ha a forgatókönyv a *Befejezve* állapotot mutatja, kattintson a **Kimenet** lehetőségre. A Kimenet panel megnyílik, és megjelenik a *Hello World* feliratunk.
1. Zárja be a Kimenet panelt.
1. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. A kimeneti streamben csak a *Hello World* eredményt látjuk, de ez megjeleníthet egyéb streameket is egy forgatókönyv feladatból, mint a Részletes vagy a Hiba, ha a forgatókönyv ezekbe ír.
1. Zárja be a adatfolyamok és a feladatok ablaktábla a MyFirstRunbook-Python tartalmazó ablaktáblájához való visszatéréshez.
1. A forgatókönyv Feladatok paneljének megnyitásához kattintson a **Feladatok** lehetőségre. Ez felsorolja az összes, a forgatókönyv által létrehozott feladatot. Egy feladat csak egyszer szerepel a listán, mert csak egyszer futtattuk a feladatot.
1. Erre a feladatra kattintva megnyithatja ugyanazt a Feladat panelt, amelyet már láttunk a runbook elindításakor. Ez lehetővé teszi, hogy az időben visszamenve megtekintse egy adott forgatókönyvhöz létrehozott összes feladat részleteit.

## <a name="add-authentication-to-manage-azure-resources"></a>Hitelesítés az Azure-erőforrások kezeléséhez hozzáadása

Most már teszteltük és közzétettük a forgatókönyvet, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen.
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

Azure virtuális gépek használatához hozzon létre egy példányát a [Azure számítás-ügyfél Python](https://docs.microsoft.com/python/api/azure.mgmt.compute.compute.computemanagementclient?view=azure-python).

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

Használjuk a `sys.argv` -változó lekérdezésével a paraméterértékek.
Közvetlenül a másik után adja hozzá a következő kódot a runbook `import` utasításokat:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Ez a importálja a `sys` modul, és hoz létre két változót ahhoz, hogy az erőforráscsoport és a virtuális gép neve.
Figyelje meg, hogy az elem az argumentumlista `sys.argv[0]`, a parancsfájl nevét, és nem a felhasználó által.

Most azt módosíthatja az értékeket szeretné használni a bemeneti paraméter nem változtatható értékek helyett a runbook utolsó két sort:

```python
async_vm_start = compute_client.virtual_machines.start(resource_group_name, vm_name)
async_vm_start.wait()
```

Python runbook indításakor (vagy a **teszt** panelen, vagy a közzétett runbookok), adhatja meg az értékeket a paraméterek a **runbook meghívása** részen **paraméterek**.

![A paraméter érték mezőbe](media/automation-first-runbook-textual-python/runbook-python-param-highlight.png)

Írjon be egy értéket az első mezőben elindítása után egy második fog jelenik meg, és így tovább, úgy, hogy a lehető legtöbb paraméter értékeket szükség szerint adhat meg.

Értékei elérhetők, a parancsfájl a `sys.argv` tömb hasonlóan a most hozzáadott kódot.

Adja meg az első paraméter értékeként az erőforráscsoport nevét, és a virtuális gép elindításához, a második paraméter neve.

![Adja meg a paramétert értékek](media/automation-first-runbook-textual-python/runbook-python-params.png)

Kattintson a **OK** a runbook elindításához. A runbook fut, és a megadott virtuális gép elindul.

## <a name="next-steps"></a>Következő lépések

* A PowerShell-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-forgatókönyvem](automation-first-runbook-textual-powershell.md).
* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)
* További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
* Az Azure Python fejlesztésével kapcsolatos további tudnivalókért lásd: [Azure Python-fejlesztőknek](https://docs.microsoft.com/python/azure/?view=azure-python).
* Példa Python 2 runbookok megtekintése: a [Azure Automation GitHub](https://docs.microsoft.com/python/azure/?view=azure-python).
