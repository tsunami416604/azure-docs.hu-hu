---
title: Azure Performance Diagnostics virtuálisgép-bővítmény a Windowshoz | Microsoft Docs
description: Bemutatja az Azure Performance Diagnostics virtuálisgép-bővítményét a Windows rendszerhez.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "71057543"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Azure teljesítménydiagnosztikai virtuálisgép-bővítmény Windowsra

Az Azure Performance Diagnostics VM-bővítmény segít a Windows rendszerű virtuális gépekről származó teljesítmény-diagnosztikai adatok gyűjtésében. A bővítmény elemzést végez, és jelentést készít a virtuális gépen a teljesítménnyel kapcsolatos problémák azonosításához és megoldásához szükséges eredményekről és javaslatokról. Ez a bővítmény egy [PerfInsights](https://aka.ms/perfinsights)nevű hibaelhárítási eszközt telepít.

> [!NOTE]
> Ha a virtuális gépen lévő diagnosztikát szeretné futtatni a Azure Portal a nem klasszikus virtuális gépek számára, ajánlott az új felület használata. További információ: [Az Azure-beli virtuális gépek teljesítmény-diagnosztikája](performance-diagnostics.md) 

## <a name="prerequisites"></a>Előfeltételek

Ez a bővítmény a Windows Server 2008 R2, a Windows Server 2012, a Windows Server 2012 R2 és a Windows Server 2016 rendszerre telepíthető. A Windows 8,1 és a Windows 10 rendszerre is telepíthető.

## <a name="extension-schema"></a>Bővítményséma
A következő JSON az Azure Performance Diagnostics virtuálisgép-bővítményének sémáját mutatja be. Ehhez a bővítményhez meg kell adni egy Storage-fiók nevét és kulcsát a diagnosztika kimenetének és jelentésének tárolásához. Ezek az értékek érzékenyek. A Storage-fiók kulcsát védett beállítási konfigurációban kell tárolni. Az Azure virtuálisgép-bővítmény védett beállítási adatvédelme titkosítva van, és csak a cél virtuális gépen van visszafejtve. Vegye figyelembe, hogy a **storageAccountName** és a **storageAccountKey** a kis-és nagybetűk megkülönböztetése. Az egyéb szükséges paraméterek a következő szakaszban vannak felsorolva.

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

### <a name="property-values"></a>Tulajdonságértékek

|   **Name (Név)**   |**Érték/példa**|       **Leírás**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|Az API verziója.
|közzétevő|Microsoft. Azure. Performance. Diagnostics|A bővítmény közzétevői névterét.
|típus|AzurePerformanceDiagnostics|A virtuálisgép-bővítmény típusa.
|typeHandlerVersion|1.0|A bővítmény kezelőjének verziója.
|performanceScenario|alapvető|Az a teljesítménnyel kapcsolatos forgatókönyv, amelynek az adatai rögzítésére kerül. Az érvényes értékek a következők: **alapszintű**, **vmslow**, **azurefiles**és **Custom**.
|traceDurationInSeconds|300|A Nyomkövetések időtartama, ha a nyomkövetési beállítások bármelyike ki van választva.
|perfCounterTrace|P|A teljesítményszámláló nyomkövetésének engedélyezése lehetőség. Az érvényes értékek: **p** vagy üres érték. Ha nem szeretné rögzíteni ezt a nyomkövetést, hagyja üresen az értéket.
|networkTrace|p|A hálózati nyomkövetés engedélyezésének lehetősége. Az érvényes értékek: **n** vagy üres érték. Ha nem szeretné rögzíteni ezt a nyomkövetést, hagyja üresen az értéket.
|xperfTrace|x|A XPerf nyomkövetés engedélyezésének lehetősége. Az érvényes értékek **x** vagy Empty értékűek. Ha nem szeretné rögzíteni ezt a nyomkövetést, hagyja üresen az értéket.
|storPortTrace|s|A StorPort nyomkövetés engedélyezésének lehetősége. Az érvényes értékek: **s** vagy üres érték. Ha nem szeretné rögzíteni ezt a nyomkövetést, hagyja üresen az értéket.
|srNumber|123452016365929|A támogatási jegy száma, ha elérhető. Ha nincs, hagyja üresen az értéket.
|requestTimeUtc|2017-09-28T22:08:53.736 Z|Az aktuális dátum időpontja (UTC). Ha a portált használja a bővítmény telepítéséhez, nem kell megadnia ezt az értéket.
|resourceId|/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}|Egy virtuális gép egyedi azonosítója.
|storageAccountName|mystorageaccount|A diagnosztikai naplók és eredmények tárolására szolgáló Storage-fiók neve.
|storageAccountKey|lDuVvxuZB28NNP... hAiRF3voADxLBTcc = =|A Storage-fiók kulcsa.

## <a name="install-the-extension"></a>A bővítmény telepítése

Az alábbi utasításokat követve telepítse a bővítményt a Windows rendszerű virtuális gépekre:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki azt a virtuális gépet, amelyre telepíteni kívánja ezt a bővítményt.

    ![Képernyőkép a Azure Portalről, a virtuális gépek kiemelésével](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Válassza ki a **bővítmények** panelt, és válassza a **Hozzáadás**lehetőséget.

    ![Képernyőkép a bővítmények panelről, a Hozzáadás Kiemelt](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Válassza az **Azure Performance Diagnostics**elemet, tekintse át a használati feltételeket, majd válassza a **Létrehozás**lehetőséget.

    ![Képernyőkép az új erőforrás-képernyőről, az Azure Performance Diagnostics kiemelve](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Adja meg a telepítés paramétereinek értékeit, majd kattintson az **OK** gombra a bővítmény telepítéséhez. További információ a támogatott forgatókönyvekről: [a PerfInsights használata](how-to-use-perfinsights.md#supported-troubleshooting-scenarios). 

    ![A bővítmény telepítése párbeszédpanel képernyőképe](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Ha a telepítés sikeres, megjelenik egy üzenet, amely jelzi, hogy ez az állapot jelenik meg.

    ![A kiépítés sikeres üzenetének képernyőképe](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > A bővítmény a kiépítés sikerességét követően fut. Az alapszintű forgatókönyvhöz legalább két percet vesz igénybe. Más esetekben a telepítés során megadott időtartamon keresztül fut.

## <a name="remove-the-extension"></a>A bővítmény eltávolítása
A bővítmény virtuális gépről való eltávolításához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), válassza ki azt a virtuális gépet, amelyről el szeretné távolítani ezt a bővítményt, majd válassza a **bővítmények** panelt. 
2. Válassza a (**..**.) lehetőséget a teljesítmény diagnosztikai bővítmény bejegyzéséhez a listából, majd válassza az **Eltávolítás**lehetőséget.

    ![Képernyőkép a bővítmények panelről, az Eltávolítás kiemelve](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Kiválaszthatja a bővítmény bejegyzését is, és az **Eltávolítás** lehetőséget is kiválaszthatja.

## <a name="template-deployment"></a>Sablonalapú telepítés
Az Azure-beli virtuálisgép-bővítmények Azure Resource Manager-sablonokkal is üzembe helyezhetők. Az előző szakaszban részletezett JSON-sémát Azure Resource Manager sablonban lehet használni. Ezzel futtatja az Azure Performance Diagnostics virtuálisgép-bővítményt egy Azure Resource Manager sablon központi telepítése során. Példa a sablonra:

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
A `Set-AzVMExtension` parancs használatával üzembe helyezhető az Azure Performance Diagnostics virtuálisgép-bővítmény egy meglévő virtuális gépre.

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

## <a name="information-on-the-data-captured"></a>Információk a rögzített adatokról
A PerfInsights eszköz különböző naplókat, konfigurációkat és diagnosztikai adatokat gyűjt a kiválasztott forgatókönyvtől függően. További információkért tekintse meg a [PerfInsights dokumentációját](https://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Az eredmények megtekintése és megosztása

A bővítmény kimenete olyan zip-fájlban található, amely fel van töltve a telepítés során megadott Storage-fiókba, és a megosztott [hozzáférési aláírások (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)használatával 30 napig meg van osztva. Ez a zip-fájl tartalmazza a diagnosztikai naplókat, valamint az eredményeket és javaslatokat tartalmazó jelentést. A kimeneti zip-fájlra mutató SAS-hivatkozás egy *zipfilename*_saslink.txt nevű szövegfájlban található a **C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics \\ \<version> **mappa alatt. Bárki, aki rendelkezik ezzel a hivatkozással, letöltheti a zip-fájlt.

Ha segítségre van szükség a támogatási szakembernek a támogatási jegyén való munkához, a Microsoft ezt az SAS-hivatkozást használja a diagnosztikai adatai letöltéséhez.

A jelentés megtekintéséhez bontsa ki a zip-fájlt, és nyissa meg a **PerfInsights Report.html** fájlt.

A zip-fájlt közvetlenül a portálról is letöltheti a bővítmény kiválasztásával.

![A teljesítmény-diagnosztika részletes állapotának képernyőképe](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> Előfordulhat, hogy a portálon megjelenő SAS-hivatkozás néha nem működik. Ezt egy helytelen formátumú URL-cím okozhatja a kódolási és a dekódolási műveletek során. Ehelyett közvetlenül a * _saslink.txt fájlból kérheti le a hivatkozást a virtuális gépről.

## <a name="troubleshoot-and-support"></a>Hibakeresés és támogatás

- A bővítmény központi telepítési állapota (az értesítési régióban) "központi telepítés folyamatban" állapotú lehet, noha a bővítmény sikeresen kiépítve.

    Ez a probléma nyugodtan figyelmen kívül hagyható, ha a bővítmény állapota azt jelzi, hogy a bővítmény sikeresen kiépítve.
- A telepítés során bizonyos hibákat a bővítményi naplók használatával lehet kezelni. A bővítmény-végrehajtás kimenete a következő könyvtárban található fájlokra van naplózva:

        C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/), és válassza a **támogatás kérése**lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
