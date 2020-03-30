---
title: Azure Performance Diagnostics VM Extension for Windows| Microsoft dokumentumok
description: Bemutatja az Azure Performance Diagnostics VM extension for Windows.Introduces Azure Performance Diagnostics Vm Extension for Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 6f104fc6513874bfef5f4bf9fe7f536c3e3d69cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71057543"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Azure teljesítménydiagnosztikai virtuálisgép-bővítmény Windowsra

Az Azure Performance Diagnostics virtuálisgép-bővítmény segít a teljesítménydiagnosztikai adatok gyűjtésében a Windows virtuális gépekről. A bővítmény elemzést végez, és jelentést készít a virtuális gépen a teljesítményproblémák azonosításához és megoldásához. Ez a bővítmény egy [PerfInsights](https://aka.ms/perfinsights)nevű hibaelhárító eszközt telepít.

> [!NOTE]
> Ha azt szeretné, hogy a virtuális gép a nem klasszikus virtuális gépek, nem klasszikus virtuális gépek, ajánlott az új felület használata. További információ: [Performance Diagnostics for Azure virtual machines](performance-diagnostics.md) 

## <a name="prerequisites"></a>Előfeltételek

Ez a bővítmény telepíthető Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 és Windows Server 2016 rendszerre. Windows 8.1-re és Windows 10-re is telepíthető.

## <a name="extension-schema"></a>Bővítményséma
A következő JSON az Azure Performance Diagnostics vm-bővítmény sémáját mutatja be. Ez a bővítmény megköveteli a nevét és kulcsát egy tárfiókot a diagnosztikai kimenet és a jelentés tárolására. Ezek az értékek érzékenyek. A tárfiók kulcsát egy védett beállítási konfigurációban kell tárolni. Az Azure VM-bővítmény védett beállítási adatai titkosítva vannak, és csak a cél virtuális gépen fejtik vissza. Vegye figyelembe, hogy **a storageAccountName** és **a storageAccountKey** a kis- és nagybetűket is figyelembe veszi. A többi szükséges paraméter a következő szakaszban található.

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]",
            "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
```

### <a name="property-values"></a>Tulajdonság értékek

|   **Név**   |**Érték / Példa**|       **Leírás**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|Az API verziója.
|közzétevő|Microsoft.Azure.Performance.Diagnosztika|A bővítmény közzétevői névtere.
|type|AzurePerformanceDiagnostics|A virtuális gép bővítményének típusa.
|typeHandlerVersion|1.0|A bővítménykezelő verziója.
|performanceScenario (teljesítményforgatókönyv)|Alapvető|Az a teljesítményforgatókönyv, amelynek az adatok rögzítésére szolgál. Érvényes értékek: **alap**, **vmslow**, **azurefiles**és **egyéni**.
|traceDurationInSeconds|300|A nyomkövetések időtartama, ha a nyomkövetési beállítások bármelyike be van jelölve.
|perfCounterTrace|P|A Teljesítményszámláló-követés engedélyezésének lehetősége. Az érvényes értékek **p** vagy üresértékek. Ha nem szeretné rögzíteni ezt a nyomkövetést, hagyja üresen az értéket.
|networkTrace|p|Lehetőség a Hálózatkövetés engedélyezéséhez. Az érvényes értékek **n** vagy üresek. Ha nem szeretné rögzíteni ezt a nyomkövetést, hagyja üresen az értéket.
|xperfTrace|x|Az XPerf Trace engedélyezésének lehetősége. Az érvényes értékek **x** vagy üresek. Ha nem szeretné rögzíteni ezt a nyomkövetést, hagyja üresen az értéket.
|storPortTrace|s|A StorPort Trace engedélyezésének lehetősége. Az érvényes értékek **s** vagy üresek. Ha nem szeretné rögzíteni ezt a nyomkövetést, hagyja üresen az értéket.
|srNumber|123452016365929|A támogatási jegy száma, ha rendelkezésre áll. Ha nincs meg az érték, hagyja üresen az értéket.
|requestTimeUtc|2017-09-28T22:08:53.736z|Aktuális dátum dátum a utc. Ha a portálsegítségével telepíti ezt a bővítményt, nem kell megadnia ezt az értéket.
|resourceId|/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}|A virtuális gép egyedi azonosítója.
|storageAccountName|mystorageaccount|A diagnosztikai naplók és eredmények tárolására szolgáló tárfiók neve.
|storageAccountKey|lDuVvxuZB28NNP... hAiRF3voADxLBTcc==|A tárfiók kulcsa.

## <a name="install-the-extension"></a>A bővítmény telepítése

A bővítmény Windows virtuális gépekre történő telepítéséhez kövesse az alábbi utasításokat:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza ki azt a virtuális gépet, ahová telepíteni szeretné ezt a bővítményt.

    ![Képernyőkép az Azure Portalról, kiemelve a virtuális gépekről](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Jelölje ki a **Bővítmények** panelt, majd a **Hozzáadás gombot.**

    ![Képernyőkép a Bővítmények panelről, kiemelt Hozzáadás lehetőséggel](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Válassza az **Azure Performance Diagnostics**lehetőséget, tekintse át a feltételeket, és válassza a **Létrehozás lehetőséget.**

    ![Képernyőkép az Új erőforrás képernyőről, kiemelve az Azure Performance Diagnostics képernyőjét](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Adja meg a telepítés paraméterértékeit, és a bővítmény telepítéséhez kattintson az **OK gombra.** A támogatott forgatókönyvekről a [PerfInsights használata](how-to-use-perfinsights.md#supported-troubleshooting-scenarios)című témakörben talál további információt. 

    ![Képernyőkép: Bővítmény telepítése párbeszédpanel](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Ha a telepítés sikeres, megjelenik egy üzenet, amely ezt az állapotot jelzi.

    ![A következő képernyőképe: A sikeres kiépítés üzenet](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > A bővítmény akkor fut, ha a kiépítés sikeres volt. Az alapforgatókönyv höz két percet vagy annál kevesebbet vesz igénybe. Más esetekben a telepítés során megadott időtartamon fut végig.

## <a name="remove-the-extension"></a>A bővítmény eltávolítása
A bővítmény virtuális gépről való eltávolításához hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)válassza ki azt a virtuális gépet, amelyről el szeretné távolítani ezt a bővítményt, majd válassza ki a **Bővítmények panelt.** 
2. A listából válassza ki a (**...**) lehetőséget a Teljesítménydiagnosztika bővítmény bejegyzéshez, majd válassza az **Eltávolítás**lehetőséget.

    ![Képernyőkép a Bővítmények panelről, kiemelve az Eltávolítás lehetőséggel](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Kiválaszthatja a bővítménybejegyzést is, és **kiválaszthatja** az Eltávolítás lehetőséget.

## <a name="template-deployment"></a>Sablonalapú telepítés
Az Azure virtuálisgép-bővítmények az Azure Resource Manager-sablonokkal telepíthetők. Az előző szakaszban részletezett JSON-séma azure Resource Manager-sablonban használható. Ez futtatja az Azure Performance Diagnostics virtuálisgép-bővítményt az Azure Resource Manager-sablon üzembe helyezése során. Íme egy mintasablon:

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
    "traceDurationInSeconds": {
      "type": "int",
    "defaultValue": 300
    },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]",
            "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

## <a name="powershell-deployment"></a>PowerShell-telepítés
A `Set-AzVMExtension` parancs segítségével üzembe helyezheti az Azure Performance Diagnostics virtuálisgép-bővítményt egy meglévő virtuális gépen.

PowerShell

```
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z";"resourceId"="VMResourceId" }
$ProtectedSettings = @{"storageAccountKey"="mystoragekey" }

Set-AzVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS
```

## <a name="information-on-the-data-captured"></a>A rögzített adatokra vonatkozó információk
A PerfInsights eszköz a kiválasztott forgatókönyvtől függően különböző naplókat, konfigurációs és diagnosztikai adatokat gyűjt. További információt a [PerfInsights dokumentációjában](https://aka.ms/perfinsights)talál.

## <a name="view-and-share-the-results"></a>Az eredmények megtekintése és megosztása

A bővítmény kimenete a telepítés során megadott tárfiókba feltöltött zip-fájlban található, és 30 napig megosztott a [megosztott hozzáférési aláírások (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)használatával. Ez a zip fájl diagnosztikai naplókat és egy jelentést tartalmaz a megállapításokkal és javaslatokkal. A kimeneti zip-fájlra mutató SAS-hivatkozás *zipfilename*a _saslink.txt nevű szövegfájlban található a **C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\\\<>verzió alatt. ** Bárki, aki ezt a linket tudja letölteni a zip fájlt.

A támogatási jegyen dolgozó támogatási szakember segítése érdekében a Microsoft használhatja ezt a SAS-hivatkozást a diagnosztikai adatok letöltéséhez.

A jelentés megtekintéséhez bontsa ki a zip fájlt, és nyissa meg a **PerfInsights Report.html** fájlt.

Azt is meg kell tudni, hogy töltse le a zip fájlt közvetlenül a portálkiválasztásával a kiterjesztés.

![Képernyőkép a Teljesítménydiagnosztika részletes állapotáról](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> Előfordulhat, hogy a portálon megjelenő SAS-hivatkozás néha nem működik. Ezt okozhatja egy hibás URL a kódolási és dekódolási műveletek során. Ehelyett a kapcsolatot közvetlenül a *_saslink.txt fájlból szerezheti be a virtuális gépről.

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

- A bővítmény üzembe helyezésének állapota (az értesítési területen) "Üzembe helyezés folyamatban" jelenhet meg, még akkor is, ha a bővítmény kivan építve.

    Ez a probléma biztonságosan figyelmen kívül hagyható, feltéve, hogy a bővítmény állapota azt jelzi, hogy a bővítmény sikeresen kivan építve.
- A telepítés során a bővítménynaplók segítségével orvosolhat bizonyos problémákat. A bővítmény-végrehajtási kimenet a következő könyvtárban található fájlokba kerül:

        C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/)és válassza **a Támogatás beszereznie**lehetőséget. Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.
