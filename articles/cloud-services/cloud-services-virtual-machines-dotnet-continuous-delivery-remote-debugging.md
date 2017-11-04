---
title: "Folyamatos kézbesítésével távoli hibakeresésének engedélyezése |} Microsoft Docs"
description: "Útmutató: távoli hibakeresés, ha a folyamatos kézbesítési használatával telepítse az Azure engedélyezése"
services: cloud-services
documentationcenter: .net
author: mikejo
manager: ghogen
editor: 
ms.assetid: 7d423639-3b2f-4ca5-ac5a-9ac19a217c29
ms.service: cloud-services
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/18/2016
ms.author: mikejo
ms.openlocfilehash: c2bd67afc0c289de94019497e57b57f97a759f3a
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="enable-remote-debugging-when-using-continuous-delivery-to-publish-to-azure"></a>Távoli hibakeresés folyamatos készregyártással történő azure-os közzététel során
Engedélyezheti a távoli hibakeresés Azure felhőszolgáltatások vagy a virtuális gépek használatakor [folyamatos kézbesítési](cloud-services-dotnet-continuous-delivery.md) közzététele az Azure-bA az alábbiak szerint.

## <a name="enabling-remote-debugging-for-cloud-services"></a>A felhőszolgáltatások távoli hibakeresés engedélyezése
1. A build Agent, a kezdeti környezet beállítása az Azure-bA a [parancssori létrehozása az Azure-](http://msdn.microsoft.com/library/hh535755.aspx).
2. Telepítse a távoli hibakeresési futásidejű (msvsmon.exe) szükség, a csomag, mert a **távoli Tools for Visual Studio**.

    * [A Visual Studio 2017 távoli eszközök](https://go.microsoft.com/fwlink/?LinkId=746570)
    * [A Visual Studio 2015 távoli eszközök](https://go.microsoft.com/fwlink/?LinkId=615470)
    * [Távoli Tools for Visual Studio 2013 Update 5](https://www.microsoft.com/download/details.aspx?id=48156)
    
    Alternatív megoldásként átmásolhatja a távoli hibakeresési bináris fájlok fordítását egy rendszer, amely a Visual Studio telepítve van.

3. Hozzon létre egy tanúsítványt, a [tanúsítványok áttekintése Azure-szolgáltatásokhoz](cloud-services-certs-create.md). A .pfx és RDP tanúsítvány-ujjlenyomatot, majd töltse fel a tanúsítvány a cél felhőalapú szolgáltatás.
4. Az MSBuild parancssorban a következő beállítások segítségével hozza létre, és engedélyezve van a távoli hibakereséssel csomag. (Helyettesítő útvonalakat a rendszer és a projekt fájlt az a szög zárójeles elemek.)
   
        msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"
   
    `VSX64RemoteDebuggerPath`az a mappa elérési útját tartalmazó msvsmon.exe a távoli eszközök Visual Studio.
    `RemoteDebuggerConnectorVersion`a felhőalapú szolgáltatás az Azure SDK-verzió van. Azt is illeszkednie kell a Visual Studio telepített verzióval.
5. A célszolgáltatás felhő közzétételét az előző lépésben hozott létre a csomag és a .cscfg fájl használatával.
6. Importálja a tanúsítványt (.pfx-fájl) telepített .NET-keretrendszerhez készült Azure SDK-val Visual Studio üzemeltető számítógépet. Ügyeljen arra, hogy importálja a `CurrentUser\My` tanúsítványtárolójába, ellenkező esetben a Visual Studio hibakereső csatolása sikertelen lesz.

## <a name="enabling-remote-debugging-for-virtual-machines"></a>A virtuális gépek távoli hibakeresés engedélyezése
1. Hozzon létre egy Azure virtuális gépen. Lásd: [Windows Server rendszerű virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vagy [a Visual Studio Azure virtuális gépek létrehozására és kezelésére](../virtual-machines/windows/classic/manage-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
2. Az a [klasszikus Azure portálon](http://go.microsoft.com/fwlink/p/?LinkID=269851), hogy a virtuális gép a virtuális gép irányítópult megtekintése **RDP tanúsítvány UJJLENYOMATA**. Ez az érték szolgál a `ServerThumbprint` bővítménykonfiguráció értéket.
3. Hozzon létre egy ügyféltanúsítványt, a [tanúsítványok áttekintése Azure-szolgáltatásokhoz](cloud-services-certs-create.md) (megtartja a .pfx és tanúsítvány-ujjlenyomat RDP).
4. Azure Powershell telepítése (0.7.4 verzió vagy újabb) a [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).
5. Futtassa a következő parancsfájlt a RemoteDebug bővítmény engedélyezéséhez. Cserélje le a elérési útja és a személyes adatokat a saját, például a előfizetés nevét, a szolgáltatás neve és az ujjlenyomat.
   
   > [!NOTE]
   > Ezt a parancsfájlt a Visual Studio 2015 van konfigurálva. Ha a Visual Studio 2013 vagy Visual Studio 2017 használ, módosítsa a `$referenceName` és `$extensionName` hozzárendeléseket alatt `RemoteDebugVS2013` vagy `RemoteDebugVS2017`.

    ```powershell   
    Add-AzureAccount

    Select-AzureSubscription "My Microsoft Subscription"

    $vm = Get-AzureVM -ServiceName "mytestvm1" -Name "mytestvm1"

    $endpoints = @(
                    ,@{Name="RDConnVS2013"; PublicPort=30400; PrivatePort=30398}
                    ,@{Name="RDFwdrVS2013"; PublicPort=31400; PrivatePort=31398}
                )

    foreach($endpoint in $endpoints)
    {
        Add-AzureEndpoint -VM $vm -Name $endpoint.Name -Protocol tcp -PublicPort $endpoint.PublicPort -LocalPort $endpoint.PrivatePort
    }

    $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015"
    $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
    $extensionName = "RemoteDebugVS2015"
    $version = "1.*"
    $publicConfiguration = "<PublicConfig><Connector.Enabled>true</Connector.Enabled><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"

    $vm | Set-AzureVMExtension -ReferenceName $referenceName -Publisher $publisher -ExtensionName $extensionName -Version $version -PublicConfiguration $publicConfiguration

    foreach($extension in $vm.VM.ResourceExtensionReferences)
    {
        if(($extension.ReferenceName -eq $referenceName) `
        -and ($extension.Publisher -eq $publisher) `
        -and ($extension.Name -eq $extensionName) `
        -and ($extension.Version -eq $version))
        {
            $extension.ResourceExtensionParameterValues[0].Key = 'config.txt'
            break
        }
    }

    $vm | Update-AzureVM
    ```

6. Importálja a tanúsítványt (.pfx) a gép, amely rendelkezik a Visual Studio Azure SDK-val telepített .NET-keretrendszerhez készült.

