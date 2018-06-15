---
title: Teljesítmény az Azure Diagnostics Virtuálisgép-bővítmény Windows |} Microsoft Docs
description: A Windows vezet be a teljesítmény az Azure Diagnostics Virtuálisgép-bővítmény.
services: virtual-machines-windows'
documentationcenter: ''
author: genlin
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 9ea7f4652aff07282c9c106f3894db807f341210
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072538"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Windows Azure teljesítmény Diagnostics Virtuálisgép-bővítménnyel

Az Azure teljesítménye Diagnostics Virtuálisgép-bővítmény segít a teljesítmény diagnosztikai adatainak összegyűjtése a Windows virtuális gépek. A bővítmény analysis hajt végre, és készít egy jelentést az eredményekről és ajánlásokat, melyekkel a virtuális gépet a teljesítmény problémáinak beazonosítását és megoldását. A bővítmény telepítését nevű hibaelhárítási eszköz [PerfInsights](http://aka.ms/perfinsights).

## <a name="prerequisites"></a>Előfeltételek

Ezt a bővítményt a Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 és Windows Server 2016 is telepíthető. Is telepíthető a Windows 8.1 és Windows 10-es.

## <a name="extension-schema"></a>Bővítményséma
A következő JSON Azure teljesítmény Diagnostics Virtuálisgép-bővítmény jelennek meg. a séma. Ezt a bővítményt a nevet és a kulcsot a tárfiók a diagnosztikai kimenetet és a jelentés tárolásához szükséges. Ezek az értékek érzékenyek. Tárfiók kulcsának egy védett beállítások konfigurálása belül kell tárolni. Az Azure virtuális gép beállítás bővítmény védett adatok titkosítva legyenek, és csak visszafejtése a cél virtuális gépen. Vegye figyelembe, hogy **storageAccountName** és **storageAccountKey** -és nagybetűk. Más kötelező paraméter a következő szakaszban találhatók.

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
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
```

### <a name="property-values"></a>A tulajdonság értékek

|   **Name (Név)**   |**Érték / – példa**|       **Leírás**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|Az API verziója.
|publisher|Microsoft.Azure.Performance.Diagnostics|A bővítmény publisher névterét.
|type|AzurePerformanceDiagnostics|A Virtuálisgép-bővítmény típusú.
|typeHandlerVersion|1.0|A bővítmény kezelő verzióját.
|performanceScenario|alapszintű|A teljesítmény-forgatókönyvet az adatok rögzítéséhez. Érvényes értékek a következők: **alapvető**, **vmslow**, **azurefiles**, és **egyéni**.
|traceDurationInSeconds|300|A nyomkövetési adatokat, ha a nyomkövetési beállítások vannak kiválasztva időtartama.
|perfCounterTrace|p|Teljesítmény-számláló nyomkövetési lehetővé teszi. Érvényes értékek a következők **p** vagy üres értékkel. Ha nem szeretné rögzíteni a, hagyja meg az értéket, mert üres.
|networkTrace|n|Hálózati nyomkövetés engedélyezése lehetőséget. Érvényes értékek a következők **n** vagy üres értékkel. Ha nem szeretné rögzíteni a, hagyja meg az értéket, mert üres.
|xperfTrace|x|Engedélyezze a következő helyen nyomkövetési beállítás. Érvényes értékek a következők **x** vagy üres értékkel. Ha nem szeretné rögzíteni a, hagyja meg az értéket, mert üres.
|storPortTrace|s|StorPort-nyomkövetés engedélyezése beállítás. Érvényes értékek a következők **s** vagy üres értékkel. Ha nem szeretné rögzíteni a, hagyja meg az értéket, mert üres.
|srNumber|123452016365929|A támogatási jegy száma, ha elérhető. Hagyja meg az értéket, mert üres, ha még nem rendelkezik.
|requestTimeUtc|2017-09-28T22:08:53.736Z|Aktuális ideje UTC formátumban. Ha a portál segítségével telepítse ezt a bővítményt, nem kell ezt az értéket adja meg.
|storageAccountName|mystorageaccount|A diagnosztikai naplók és az eredmények tárolásához a tárfiók neve.
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|A tárfiók kulcsa.

## <a name="install-the-extension"></a>A bővítmény telepítése

Kövesse az alábbi utasításokat a Windows virtuális gépek a bővítmény telepítéséhez:

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Válassza ki a virtuális gépet, ahol ezt a bővítményt telepíteni szeretné.

    ![Képernyőfelvétel az Azure portálon, a kijelölt virtuális gépekkel](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Válassza ki a **bővítmények** panelt, és válassza ki **Hozzáadás**.

    ![Képernyőfelvétel a bővítmények panelről, a kijelölt hozzáadása](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Válassza ki **Azure teljesítmény diagnosztikai**, tekintse át a használati feltételeket, és válassza ki **létrehozása**.

    ![Képernyőfelvétel: új erőforrás képernyőn, a kijelölt Azure teljesítmény diagnosztika](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Adja meg a paraméterértékeket a telepítés, és válassza ki **OK** a bővítmény telepítéséhez. Támogatott forgatókönyvekkel kapcsolatos további információkért lásd: [PerfInsights használata](how-to-use-perfInsights.md#supported-troubleshooting-scenarios). 

    ![Képernyőfelvétel a telepítése bővítmény párbeszédpanel](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Ha a telepítés sikeres, megjelenik egy üzenet, ezt az állapotot jelzi.

    ![Képernyőfelvétel a kiépítés sikeres volt. üzenet](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > A bővítmény fut, amikor a kiépítés sikeres. Két percet vesz igénybe vagy kevesebb az alapvető forgatókönyv végrehajtásához. Az egyéb forgatókönyvek futtatja a telepítés során megadott időtartam keresztül.

## <a name="remove-the-extension"></a>Távolítsa el a kiterjesztés
A bővítmény virtuális gépről eltávolításához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure-portálon](http://portal.azure.com), válassza ki a virtuális gépet, amelyből el kívánja eltávolítani ezt a bővítményt, és jelölje ki a **bővítmények** panelen. 
2. Válassza ki a (**...** ) csoportot a listából, és válassza ki a teljesítmény diagnosztika bővítmény bejegyzést **Eltávolítás**.

    ![Képernyőfelvétel a bővítmények panelen, a rendszer eltávolítja a kijelölt](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Is válassza ki a bővítmény bejegyzést, és válassza ki a **Eltávolítás** lehetőséget.

## <a name="template-deployment"></a>Sablonalapú telepítés
Az Azure virtuálisgép-bővítmények az Azure Resource Manager-sablonok is telepíthető. A JSON-séma az előző szakaszban ismertetett Azure Resource Manager-sablonokban használható. Az Azure teljesítménye Diagnostics Virtuálisgép-bővítmény ez fut az Azure Resource Manager sablon üzembe helyezése során. Íme egy minta sablont:

````
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
        "protectedSettings": {            
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
  ]
}
````

## <a name="powershell-deployment"></a>PowerShell telepítése
A `Set-AzureRmVMExtension` parancs segítségével Azure teljesítmény Diagnostics Virtuálisgép-bővítmény telepítése egy meglévő virtuális gépre.

PowerShell

````
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z" }
$ProtectedSettings = @{"storageAccountKey"="mystoragekey" }

Set-AzureRmVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS
````

## <a name="information-on-the-data-captured"></a>A rögzített adatok információk
A PerfInsights eszköz különböző naplókat, konfigurálási és diagnosztikai adatokat, attól függően, hogy a választott forgatókönyv gyűjti. További információkért lásd: a [PerfInsights dokumentáció](http://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Megjelenítheti és megoszthatja az eredmények

A bővítmény kimenete egy zip-fájl feltöltése a tárfiókba, a telepítés során megadott és 30 napig történő megosztása található [megosztott hozzáférési aláírásokkal (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md). A zip-fájl a diagnosztikai naplók és egy jelentést az eredményekről és javaslatokat tartalmaz. A kimeneti zip-fájl egy SAS hivatkozást nevű belül található *zipfilename*mappában lévő _saslink.txt **C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics \\ \<verzió >**. Bárki, aki ezt a hivatkozást tartalmaz, akkor képes a zip-fájl letöltésére.

Segít a támogatási szakember, a támogatási jegy dolgozik, a Microsoft a ezen SAS hivatkozás segítségével lehet, hogy a diagnosztikai adatok letöltése.

A jelentés megtekintéséhez bontsa ki a zip-fájl, és nyissa meg a **PerfInsights diagram jelentés.HTML** fájlt.

Is kell tudni a portálról közvetlenül a zip-fájl letöltésére jelölje ki a bővítményt.

![Képernyőfelvétel a teljesítmény diagnosztika részletes állapota](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> A SAS-hivatkozás megjelenik a portálon néha nem működik. Ezt okozhatja egy hibás URL-címet a kódolási és dekódolási műveletek során. Ehelyett letölthető a hivatkozás közvetlenül a *_saslink.txt fájlt a virtuális gépről.

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

- Bővítmény telepítési állapota (az értesítési területen) lehet, hogy megjelenítése "Folyamatban lévő telepítés", annak ellenére, hogy a bővítmény sikeresen lett kiépítve.

    A probléma biztonságosan figyelmen kívül hagyható, mindaddig, amíg a bővítmény állapotát jelzi, hogy a bővítmény sikeresen lett kiépítve.
- Telepítés közben bizonyos problémák a bővítmény naplók segítségével lehet oldani. A következő könyvtárban található fájlok kerül a bővítmény végrehajtás kimenetének:

        C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a a [MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/), és válassza ki **segítségre van szüksége**. Az Azure támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).
