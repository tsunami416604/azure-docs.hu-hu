---
title: "Az Azure-Felhőszolgáltatás a távoli asztal engedélyezése |} Microsoft Docs"
description: "Az azure cloud service alkalmazás távoli asztali kapcsolatok lehetővé tételéhez konfigurálása"
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: d3110ee8-6526-4585-aba5-d0bc9a713e9b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: 413e72e9a39fcde84f56bfc61a6bc72dbadf1c97
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Engedélyezze a távoli asztali kapcsolat egy szerepkör esetén az Azure Cloud Services csomag

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [klasszikus Azure portál](cloud-services-role-enable-remote-desktop.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)

Engedélyezheti a távoli asztali kapcsolat létrehozása a szerepkörben fejlesztése során a távoli asztal modulok belefoglalja a szolgáltatás definíciós, vagy választhatja azt is, a távoli asztal bővítményével távoli asztal engedélyezése. Az előnyben részesített megoldás, a távoli asztali bővítmény használatára, mert a távoli asztal az alkalmazás nem kell újratelepíteni az alkalmazás központi telepítése után is engedélyezheti.

## <a name="configure-remote-desktop-from-the-azure-classic-portal"></a>A távoli asztal konfigurálása a klasszikus Azure portálon
A klasszikus Azure portálra a távoli asztali bővítmény megközelítést használ, így a távoli asztal az alkalmazás központi telepítése után is engedélyezheti. A **konfigurálása** a felhőszolgáltatás lap lehetővé teszi a távoli asztal engedélyezéséhez módosítsa a helyi rendszergazda fiók használatával kapcsolódik a virtuális gépek, a tanúsítvány-hitelesítésben használt, és a lejárati dátuma.

1. Kattintson a **Felhőszolgáltatások**, kattintson a nevére, a felhőalapú szolgáltatás, és kattintson a **konfigurálása**.
2. Kattintson a **távoli** panel alján.

    ![Távoli cloud services csomag](./media/cloud-services-role-enable-remote-desktop/CloudServices_Remote.png)

   > [!WARNING]
   > Összes szerepkörpéldányt újra kell indítani, amikor először a távoli asztal engedélyezése és kattintson az OK gombra (jelölő). Újraindítás megakadályozása érdekében a jelszó titkosításához használt tanúsítványról telepítenie kell a szerepkört. Egy újraindítás érdekében [töltse fel a tanúsítványt a felhőalapú szolgáltatáshoz](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) és térjen vissza ezt a párbeszédpanelt.

3. A **szerepkörök**, válassza ki a frissíteni, vagy válassza ki a szerepkör **összes** összes szerepköre tekintetében.
4. Ellenőrizze a következő módosításokat:

   * A távoli asztal engedélyezéséhez jelölje be a **távoli asztal engedélyezése** jelölőnégyzetet. A távoli asztal letiltásához törölje a jelölőnégyzet jelölését.
   * A távoli asztal kapcsolatokat a szerepkörpéldányok a használandó fiók létrehozása.
   * Frissítse a meglévő fiók jelszavát.
   * A hitelesítéshez használandó feltöltött tanúsítvány kiválasztása (töltse fel a tanúsítvány használatával **feltöltése** a a **tanúsítványok** oldalon), vagy hozzon létre egy új tanúsítványt.
   * Módosítsa a lejárati dátumot a távoli asztal konfigurációjának.

5. A konfigurációs módosítások befejeztével kattintson **OK** (jelölő).

## <a name="remote-into-role-instances"></a>A szerepkörpéldányok távoli
A távoli asztal engedélyezve van a szerepkörök a következőket teheti, miután távoli olyan szerepkörpéldányt, különböző eszközök használatával történő.

A szerepkör példánya csatlakoztatja a klasszikus Azure-portálon:

1. Kattintson a **példányok** megnyitásához a **példányok** lap.
2. Válassza ki a szerepkör példánya, amely a távoli asztal konfigurálva van.
3. Kattintson a **Connect**, és kövesse az utasításokat megnyitásához az asztalon.
4. Kattintson a **nyitott** , majd **Connect** a távoli asztali kapcsolat elindításához.

### <a name="use-visual-studio-to-remote-into-a-role-instance"></a>Visual Studio használata a távoli be a szerepkör példánya
A Visual Studio Server Explorer:

1. Bontsa ki a **Azure** > **Felhőszolgáltatások** > **[felhőszolgáltatás neve]** csomópont.
2. Bontsa ki **átmeneti** vagy **éles**.
3. Bontsa ki az egyes szerepkör.
4. Kattintson a jobb gombbal a szerepkörpéldányt beállítani, kattintson a **csatlakozzon a távoli asztali kapcsolattal...** , majd adja meg a felhasználónevet és jelszót.

![Server explorer távoli asztal](./media/cloud-services-role-enable-remote-desktop/ServerExplorer_RemoteDesktop.png)

### <a name="use-powershell-to-get-the-rdp-file"></a>Az RDP-fájl lekérése a PowerShell használatával
Használhatja a [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) parancsmag segítségével kérje le a RDP-fájlt. Ezután használhatja az RDP-fájlt a távoli asztali kapcsolat a felhőalapú szolgáltatás eléréséhez.

### <a name="programmatically-download-the-rdp-file-through-the-service-management-rest-api"></a>A Service Management REST API-n keresztül az RDP-fájl letöltése
Használhatja a [RDP-fájl letöltése](https://msdn.microsoft.com/library/jj157183.aspx) REST-művelet az RDP-fájl letöltésére.

## <a name="to-configure-remote-desktop-in-the-service-definition-file"></a>A távoli asztal konfigurálása a szolgáltatásdefiníciós fájlban
Ez a módszer lehetővé teszi a távoli asztal engedélyezése az alkalmazás fejlesztése során. Ez a megközelítés titkosított jelszavakat igényel tárolható szolgáltatás konfigurációs fájl- és a távoli asztal konfigurálásának frissítéseit az alkalmazás egy újratelepítés igényelnének. Ha el szeretné kerülni a downsides a fent leírt-alapú távoli asztali bővítmény megközelítést kell használnia.  

Használhatja a Visual Studio [engedélyezése a távoli asztali kapcsolat](../vs-azure-tools-remote-desktop-roles.md) a szolgáltatás definíciós fájl módszer használatával.  
A következő lépések leírják a távoli asztal engedélyezése a szolgáltatásmodell-fájlokból szükséges módosításokat. A Visual Studio automatikusan fog teszi ezeket a módosításokat, közzétételekor.

### <a name="set-up-the-connection-in-the-service-model"></a>A szolgáltatásmodell a kapcsolat beállítása
Használja a **importálja** elem importálásához a **RemoteAccess** modul és a **RemoteForwarder** modult a [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef) fájl.

A szolgáltatásdefiníciós fájlban az alábbi példához hasonló legyen a `<Imports>` -eleme.

```xml
<ServiceDefinition name="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2013-03.2.0">
    <WebRole name="WebRole1" vmsize="Small">
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="Endpoint1" endpointName="Endpoint1" />
                </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
            <Import moduleName="Diagnostics" />
            <Import moduleName="RemoteAccess" />
            <Import moduleName="RemoteForwarder" />
        </Imports>
    </WebRole>
</ServiceDefinition>
```
A [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg) fájl kell az alábbi példához hasonló, vegye figyelembe a `<ConfigurationSettings>` és `<Certificates>` elemeket. A megadott tanúsítványt kell [feltöltve felhőszolgáltatás](cloud-services-how-to-create-deploy.md#how-to-upload-a-certificate-for-a-cloud-service).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2013-03.2.0">
    <Role name="WebRole1">
        <Instances count="2" />
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="[name-of-user-account]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="[base-64-encrypted-user-password]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="[certificate-expiration]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
        </ConfigurationSettings>
        <Certificates>
            <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="[certificate-thumbprint]" thumbprintAlgorithm="sha1" />
        </Certificates>
    </Role>
</ServiceConfiguration>
```


## <a name="additional-resources"></a>További források
[Felhőalapú szolgáltatások konfigurálása](cloud-services-how-to-configure.md)
[Cloud services – gyakori kérdések – a távoli asztal](cloud-services-faq.md)
