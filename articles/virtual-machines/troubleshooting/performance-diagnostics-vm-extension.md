---
title: A teljesítmény az Azure Diagnostics Virtuálisgép-bővítmény Windows |} A Microsoft Docs
description: A Windows vezet be az Azure teljesítménydiagnosztikai Virtuálisgép-bővítmény.
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
ms.openlocfilehash: 0482a882b2dea47752eb38eadbaaa72e36ae2eae
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414027"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>A teljesítmény az Azure Diagnostics Virtuálisgép-bővítmény Windows

Az Azure teljesítménydiagnosztikai Virtuálisgép-bővítmény segít a teljesítmény diagnosztikai adatok gyűjtését a Windows virtuális gépek. A bővítmény elemzési hajt végre, és egy jelentésre az eredményeket és meglátásokat bemutató javaslatok azonosításához, és a virtuális gépen a teljesítménnyel kapcsolatos problémák megoldásához nyújt. Ez a bővítmény telepítését nevű hibaelhárítási eszköz [PerfInsights](http://aka.ms/perfinsights).

> [!NOTE]
    > Ha meg szeretné diagnosztika futtatása a virtuális Gépen az Azure Portalról a nem klasszikus virtuális gépek, az új felhasználói felület használata ajánlott. További információkért lásd: [teljesítménybeli problémák diagnosztizálása az Azure-beli virtuális gépek](performance-diagnostics.md) 

## <a name="prerequisites"></a>Előfeltételek

Ez a bővítmény telepíthető a Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 és Windows Server 2016-ban. Is telepíthető a Windows 8.1 és Windows 10-es.

## <a name="extension-schema"></a>Bővítményséma
A következő JSON-ra a séma Azure teljesítménydiagnosztikai Virtuálisgép-bővítmény mutatja be. Ez a bővítmény nevét és kulcsát egy tárfiókot a diagnosztikai kimenetet és a jelentés tárolásához szükséges. Ezek az értékek érzékenyek. Tárfiók kulcsa egy védett beállítás konfigurációjának belül kell tárolni. Azure-beli virtuális gép bővítmény védett beállítás adat titkosítva van, és csak a cél virtuális gépen a rendszer visszafejti. Vegye figyelembe, hogy **storageAccountName** és **storageAccountKey** kis-és nagybetűket. A következő szakaszban felsorolt más szükséges paramétereket.

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

### <a name="property-values"></a>Tulajdonságok értékei

|   **Name (Név)**   |**Érték és példa**|       **Leírás**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|API-verzió.
|publisher|Microsoft.Azure.Performance.Diagnostics|A bővítmény közzétevő névterét.
|type|AzurePerformanceDiagnostics|A Virtuálisgép-bővítmény típusa.
|typeHandlerVersion|1.0|A kiterjesztés kezelő verziója.
|performanceScenario|alapszintű|A teljesítmény eset, amelyekre kapcsolatos adatok rögzítéséhez. Érvényes értékek a következők: **alapszintű**, **vmslow**, **Azure filesba –**, és **egyéni**.
|traceDurationInSeconds|300|A nyomkövetési adatok, a nyomkövetési beállítások választásakor időtartama.
|perfCounterTrace|p|Teljesítmény-számláló nyomkövetési lehetővé teszi. Érvényes értékek a következők **p** vagy üres értékkel. Ha nem szeretné rögzíteni a, hagyja meg az üres érték.
|networkTrace|n|Hálózati nyomkövetés engedélyezése lehetőséget. Érvényes értékek a következők **n** vagy üres értékkel. Ha nem szeretné rögzíteni a, hagyja meg az üres érték.
|xperfTrace|x|Lehetőség van a következő helyen nyomkövetés engedélyezése. Érvényes értékek a következők **x** vagy üres értékkel. Ha nem szeretné rögzíteni a, hagyja meg az üres érték.
|storPortTrace|s|StorPort-nyomkövetés engedélyezése lehetőséget. Érvényes értékek a következők **s** vagy üres értékkel. Ha nem szeretné rögzíteni a, hagyja meg az üres érték.
|srNumber|123452016365929|A támogatási jegy száma, ha elérhető. Hagyja meg az értéket, ha üres, ha nem rendelkezik.
|requestTimeUtc|2017-09-28T22:08:53.736Z|Aktuális dátum és időpont (UTC). Ha a portálon a bővítmény telepítéséhez használ, nem kell ezt az értéket adja meg.
|resourceId|/Subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /szolgáltatók/ {resourceProviderNamespace} / {resourceType} / {resourceName}|A virtuális gép egyedi azonosítója.
|storageAccountName|mystorageaccount|A diagnosztikai naplók és az eredmények tárolásához a tárfiók neve.
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|A tárfiók kulcsa.

## <a name="install-the-extension"></a>A bővítmény telepítése

Kövesse ezeket az utasításokat a bővítmény telepíthető Windows virtuális gépek:

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Válassza ki a virtuális gép, ahol szeretné telepíteni a bővítményt.

    ![Képernyőkép az Azure portal, a virtuális gépek vannak kiemelve](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Válassza ki a **bővítmények** panelen, és válassza ki **Hozzáadás**.

    ![Képernyőkép a bővítmények panelen, a kiemelt Hozzáadás](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Válassza ki **Azure teljesítménybeli problémák diagnosztizálása**, tekintse át a feltételeket és kikötéseket, és válassza ki **létrehozás**.

    ![Képernyőfelvétel az új képernyő, a kiemelt Azure teljesítménybeli problémák diagnosztizálása](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Adja meg a paraméterértékeket a telepítés, és válassza ki **OK** a bővítmény telepítésére. Támogatott forgatókönyvekkel kapcsolatos további információkért lásd: [a PerfInsights használata](how-to-use-perfInsights.md#supported-troubleshooting-scenarios). 

    ![Bővítmény párbeszédpanel telepítés képernyőképe](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Ha a telepítés sikeres, megjelenik egy üzenet, ezt az állapotot jelzi.

    ![Képernyőkép a kiépítés sikeres üzenetek](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > A bővítmény akkor fut, amikor a kiépítés sikeres volt. Két percet vesz igénybe, vagy kevesebb, az alapszintű forgatókönyv végrehajtásához. Más esetekben futtatja az időtartam a telepítés során megadott keresztül.

## <a name="remove-the-extension"></a>Távolítsa el a bővítményt
Távolítsa el a bővítményt a virtuális gépről, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [az Azure portal](http://portal.azure.com), válassza ki a virtuális gépet, ahonnan a távolítsa el a bővítményt, majd válassza a **bővítmények** panelen. 
2. Válassza ki a (**...** ) a teljesítmény a diagnosztikai bővítmény bejegyzést a listából, és válassza ki a **Eltávolítás**.

    ![Képernyőkép a bővítmények panelről, eltávolítás kiemelésével](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Is válassza ki a bővítmény bejegyzést, és válassza ki a **Eltávolítás** lehetőséget.

## <a name="template-deployment"></a>Sablonalapú telepítés
Az Azure virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. A JSON-sémájában az előző szakaszban ismertetett Azure Resource Manager-sablon használható. Ez futtatja az Azure teljesítményét diagnosztikai Virtuálisgép-bővítmény egy Azure Resource Manager-sablon telepítése során. Itt látható egy minta sablont:

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
````

## <a name="powershell-deployment"></a>PowerShell környezetben végzett telepítés
A `Set-AzureRmVMExtension` parancs használható egy már meglévő virtuális gépek üzembe helyezéséhez Azure teljesítménydiagnosztikai Virtuálisgép-bővítmény.

PowerShell

````
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z";"resourceId"="VMResourceId" }
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

## <a name="information-on-the-data-captured"></a>Információ a rögzített adatokat
A PerfInsights eszköz különböző naplókat, konfiguráció és diagnosztikai adatokat, attól függően, a választott forgatókönyv gyűjti. További információkért lásd: a [PerfInsights dokumentáció](http://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Megjelenítheti és megoszthatja az eredmények

A bővítmény kimenetében található egy zip-fájl feltöltése a tárfiókba, a telepítés során megadott, és 30 napig használatával megosztott [közös hozzáférésű Jogosultságkódok (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md). A zip-fájl tartalmazza a diagnosztikai naplók és a egy jelentés eredményeket és meglátásokat bemutató javaslatok. Kimeneti zip-fájl egy SAS-hivatkozás nevű szöveges fájlban található *zipfilename*a mappában _saslink.txt **C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics \\ \<verzió >**. Bárki, aki ezt a hivatkozást a rendszer a zip-fájl letöltéséhez.

Segítse a támogatási szakember, a támogatási jegy dolgozik, a Microsoft előfordulhat, hogy használja a SAS-hivatkozás a diagnosztikai adatok letöltéséhez.

A jelentés megtekintéséhez bontsa ki a zip-fájlt, és nyissa meg a **PerfInsights diagram jelentés.HTML** fájlt.

Azt is tudnia kell, töltse le a zip-fájlt a bővítmény kiválasztásával közvetlenül a portálról.

![Képernyőkép a teljesítménybeli problémák diagnosztizálása részletes állapota](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> A SAS-hivatkozásra a portálon megjelenő egyes esetekben nem működik. Ezt okozhatja egy hibás formátumú URL-címet a kódolási és dekódolási műveletek során. Ehelyett kérheti le a hivatkozást közvetlenül a fájlból *_saslink.txt a virtuális gépről.

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

- Bővítmény telepítési állapota (az értesítési területen) előfordulhat, hogy megjelenítése "Üzembe helyezés folyamatban", annak ellenére, hogy a bővítmény sikeresen van kiépítve.

    A probléma biztonságosan figyelmen kívül hagyható, mindaddig, amíg a bővítmény állapota azt jelzi, hogy a bővítmény sikeresen van kiépítve.
- A telepítés során problémák kezelheti a bővítmény-naplók használatával. Bővítmény végrehajtás kimenetének a rendszer naplózza a következő könyvtárban található fájlok:

        C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/), és válassza ki **támogatás**. Az Azure-támogatás használatáról további információért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).
