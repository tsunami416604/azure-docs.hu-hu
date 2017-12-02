---
title: "Teljesítmény az Azure Diagnostics Virtuálisgép-bővítmény Windows |} Microsoft Docs"
description: "A Windows vezet be a teljesítmény az Azure Diagnostics Virtuálisgép-bővítmény."
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/29/2017
ms.author: genli
ms.openlocfilehash: d9384af2cf1d8b3f55f9ec2316046536634c124e
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2017
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Windows Azure teljesítmény Diagnostics Virtuálisgép-bővítménnyel

## <a name="summary"></a>Összefoglalás
Azure teljesítmény Diagnostics Virtuálisgép-bővítmény segít a teljesítmény diagnosztikai adatainak összegyűjtése a Windows virtuális gépek, elemzés hajt végre, és készít egy jelentést az eredményekről és ajánlásokat, melyekkel a virtuális gépet a teljesítmény problémáinak beazonosítását és megoldását. A bővítmény telepítését nevű hibaelhárítási eszköz [PerfInsights](http://aka.ms/perfinsights).

## <a name="prerequisites"></a>Előfeltételek
### <a name="operating-systems"></a>Operációs rendszerek
A bővítmény telepíthető Windows Server 2008 R2, 2012, 2012 R2-ben 2016; Windows 8.1 és Windows 10 operációs rendszer.

## <a name="extension-schema"></a>A séma kiterjesztése
A következő JSON a séma az Azure-teljesítmény diagnosztika bővítmény jelennek meg. Ezt a bővítményt a nevet és a Storage a diagnosztikai kimenetet a Tárfiók kulcsa és a jelentés igényel. Ezek az értékek bizalmas és védett beállítások konfigurálása belül kell tárolni. Az Azure Virtuálisgép-bővítmény védett beállítás adatokat titkosít, és csak visszafejti a cél virtuális gépen. Vegye figyelembe, hogy storageAccountName és storageAccountKey kis-és nagybetűket. Más kötelező paraméter a következő szakaszban találhatók.

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
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
```

### <a name="property-values"></a>A tulajdonság értékek

|   **Name (Név)**   |**Érték / – példa**|       **Leírás**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|Az API verziója
|Közzétevő|Microsoft.Azure.Performance.Diagnostics|A bővítmény Publisher névtér
|type|AzurePerformanceDiagnostics|A Virtuálisgép-bővítmény típusú
|typeHandlerVersion|1.0|A bővítmény kezelő verziója
|performanceScenario|alapszintű|Teljesítmény a forgatókönyvben az adatok rögzítéséhez. Érvényes értékek a következők: **alapvető**, **vmslow**, **azurefiles**, és **egyéni**.
|traceDurationInSeconds|300|A nyomkövetési adatokat, ha a nyomkövetési beállítások vannak kiválasztva időtartama.
|perfCounterTrace|P|Teljesítmény-számláló nyomkövetési lehetővé teszi. Érvényes értékek a következők **p** vagy üres értékkel. Ha nem szeretné rögzíteni a, ne változtassa meg az érték üres.
|networkTrace|n|A Hálózatfigyelő nyomkövetési lehetővé teszi. Érvényes értékek a következők  **n**  vagy üres értékkel. Ha nem szeretné rögzíteni a, ne változtassa meg az érték üres.
|xperfTrace|x|Engedélyezze a következő helyen nyomkövetési beállítás. Érvényes értékek a következők **x** vagy üres értékkel. Ha nem szeretné rögzíteni a, ne változtassa meg az érték üres.
|storPortTrace|s|StorPort-nyomkövetés engedélyezése beállítás. Érvényes értékek: s vagy üres érték. Ha nem szeretné rögzíteni a, ne változtassa meg az érték üres.
|srNumber|123452016365929|Támogatja a jegy számát, ha elérhető. Hagyja meg az üres, ha még nem rendelkezik.
|storageAccountName|mystorageaccount|A diagnosztikai naplók és az eredmények tárolásához a tárfiók nevére.
|storageAccountKey|lDuVvxuZB28NNP... hAiRF3voADxLBTcc ==|A tárfiók kulcsa.

## <a name="install-the-extension"></a>A bővítmény telepítése

Kövesse az alábbi lépéseket a Virtuálisgép-bővítmény telepítése a Windows virtuális gépek:

1. Jelentkezzen be az [Azure Portal](http://portal.azure.com).
2. Válassza ki a virtuális gépet, ahol ezt a bővítményt telepíteni szeretné.

    ![Válassza ki a virtuális gépet](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Válassza ki **bővítmények** panel megnyitásához, és kattintson a **Hozzáadás** gombra.

    ![Bővítmények kiválasztása](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Azure teljesítmény diagnosztika bővítmény kiválasztása, tekintse át a használati feltételeket, és kattintson a **létrehozása** gombra.

    ![Az Azure teljesítménye diagnosztika bővítmény létrehozása](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Adja meg a telepítést, majd kattintson a paraméterértékek **OK** a bővítmény telepítéséhez. További információ a támogatott hibaelhárítási forgatókönyveket található [Itt](how-to-use-perfInsights.md#supported-troubleshooting-scenarios). 

    ![A bővítmény telepítése](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Ha a telepítés sikeres, megjelenik egy hibaüzenet, amely a kiépítés sikeres volt.

    ![Üzenet kiépítés sikeres volt.](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > A bővítmény végrehajtását követően a kiépítés sikeres van, és néhány perc alatt vagy kevesebb, a végrehajtás alapvető forgatókönyv végrehajtásához szükséges indul. Az egyéb forgatókönyvek futtatja a telepítés során megadott időtartam keresztül.

## <a name="remove-the-extension"></a>Távolítsa el a kiterjesztés
A bővítmény virtuális gépről eltávolításához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure-portálon](http://portal.azure.com), válassza ki a virtuális gépet, ahol szeretné eltávolítani ezt a bővítményt, és válassza a bővítmények panelen. 
2. Kattintson a (**...** ) a teljesítmény diagnosztika bővítmény bejegyzést a listából, és az eszközt.

    ![A kiterjesztés eltávolítása](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Előfordulhat, hogy válassza ki a bővítmény bejegyzést, és távolítsa el a beállítást.

## <a name="template-deployment"></a>Sablon-üzembehelyezés
Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonok is telepíthető. Az előző szakaszban ismertetett JSON-séma segítségével az Azure Resource Manager-sablonok az Azure teljesítménye diagnosztikai bővítmény futtatása az Azure Resource Manager sablon üzembe helyezése során. Íme egy minta-sablont, amely sablon-üzembehelyezés használható:

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
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
  ]
}
````

## <a name="powershell-deployment"></a>PowerShell telepítése
A `Set-AzureRmVMExtension` parancs segítségével Azure teljesítmény Diagnostics virtuálisgép-bővítmény telepítése egy meglévő virtuális gépre. A parancs futtatása előtt a nyilvános és titkos konfigurációk kell egy kivonattáblát a PowerShell tárolódnak.

PowerShell

````
$PublicSettings = @{ "performanceScenario":"basic","traceDurationInSeconds":300,"perfCounterTrace":"p","networkTrace":"","xperfTrace":"","storPortTrace":"","srNumber":"","requestTimeUtc":"2017-09-28T22:08:53.736Z" }
$ProtectedSettings = @{"storageAccountName":"mystorageaccount","storageAccountKey":"mystoragekey"}

Set-AzureRmVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
````

## <a name="information-on-the-data-captured"></a>A rögzített adatok információk
PerfInsights eszköz különböző naplókat, konfigurálásához, attól függően, hogy a választott forgatókönyv stb. diagnosztikai adatokat gyűjt. A forgatókönyv / gyűjtött adatokról további tájékoztatásért keresse fel [PerfInsights dokumentáció](http://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Megjelenítheti és megoszthatja az eredmények

A bővítmény kimeneti tárolja egy alapértelmezés szerint a Temp lemezmeghajtó (általában D:\log_collection) log_collection nevű mappába. Ebben a mappában tekintheti meg a diagnosztikai naplók és egy jelentést az eredményekről és javaslatokat tartalmazó zip-fájl.

A zip-fájl létrehozása a telepítés során megadott tárfiók is fel van töltve, valamint használatával 30 napig megosztott [megosztott hozzáférési aláírásokkal (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md). Nevű *zipfilename*_saslink.txt is a log_collection mappában jön létre. Ez a fájl a zip-fájl letöltésére létrehozott SAS-hivatkozást tartalmaz. Bárki, aki ezt a hivatkozást tartalmaz, akkor képes a zip-fájl letöltésére.

Microsoft a SAS-hivatkozás segítségével töltse le a diagnosztikai adatainak további vizsgálatok által a támogatja a visszafejtés, a támogatási jegy dolgozik.

A jelentés megtekintéséhez csupán bontsa ki a zip-fájl, és nyissa meg a **PerfInsights diagram jelentés.HTML** fájlt.

Bizonyos is képes a portálról közvetlenül a zip-fájl letöltésére jelölje ki a bővítményt.

![Az állapot](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> A SAS-hivatkozás megjelenik a portálon nem feltétlenül néha helytelenül formázott URL-cím (a speciális karakterek okozta) miatt kódolási és dekódolási művelet során. Kerülő megoldás lehet a hivatkozás közvetlenül lekérni a *_saslink.txt fájlt a virtuális gépről.

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás
### <a name="troubleshoot"></a>Hibaelhárítás

- Bővítmény telepítési állapota (az értesítési területen) lehet, hogy megjelenítése "Folyamatban lévő telepítés", annak ellenére, hogy a bővítmény sikeresen lett kiépítve.

    A probléma biztonságosan figyelmen kívül hagyható, amíg a bővítmény állapotát jelzi, hogy a bővítmény sikeresen lett kiépítve.
- Telepítés közben bizonyos problémák elhárításához a bővítmény-naplók segítségével. A következő könyvtárban található fájlok kerül a bővítmény végrehajtás kimenetének:

        C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics

### <a name="support"></a>Támogatás

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a a [MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza ki a Get-támogatási szolgálathoz. Támogatja az Azure használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).
