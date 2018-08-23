---
title: Az Azure Active Directory beállítása a Service Fabric ügyfél-hitelesítéshez |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be az Azure Active Directory (Azure AD) a Service Fabric-fürtök ügyfelek hitelesítéséhez.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/15/2018
ms.author: aljo
ms.openlocfilehash: 9b00f6e5c4dbd2fe2c6aab4c62cecc2f2e2640f0
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "42058745"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Az Azure Active Directory beállítása az ügyfél-hitelesítéshez

Az Azure-ban futó fürtök az Azure Active Directory (Azure AD) ajánlott felügyeleti végpontok való biztonságos hozzáférést.  Ez a cikk bemutatja, hogyan és a Service Fabric-fürt-ügyfelek hitelesítésére az Azure AD beállítása, amely kell elvégezni, mielőtt [a fürt létrehozása](service-fabric-cluster-creation-via-arm.md).  Azure ad-ben alkalmazásokhoz való felhasználói hozzáférés kezelése lehetővé teszi a szervezetek (más néven bérlők). Alkalmazások vannak osztva a webalapú bejelentkezési felhasználói felület és a egy natív ügyfél felhasználói élményét. Ez a cikk feltételezzük, hogy már létrehozott egy bérlőt. Ha nem rendelkezik, először olvassa el [Azure Active Directory-bérlő beszerzése][active-directory-howto-tenant].

## <a name="create-azure-ad-applications"></a>Az Azure AD-alkalmazások létrehozása
Service Fabric-fürt kínál a különböző belépési pontok annak felügyeleti funkciójához, beleértve a webalapú [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] és [Visual Studio] [ service-fabric-manage-application-in-visual-studio]. Ennek eredményeképpen hoz létre, ki férhet hozzá a fürt két Azure AD-alkalmazások: egy webalkalmazás és a egy natív alkalmazást.  Az alkalmazások létrehozása után felhasználók hozzárendelése csak olvasható, és rendszergazdai szerepkörök.

Egyes lépéseit az Azure AD konfigurálása a Service Fabric-fürt leegyszerűsítése hoztunk létre egy Windows PowerShell-parancsprogramok halmaza.

> [!NOTE]
> A következő lépéseket kell elvégeznie, a fürt létrehozása előtt. A parancsfájlok várhatóan a fürt nevét és a végpontok, mert az értékeket meg kell tervezni, és nem az, hogy már létrehozott értékeket.

1. [Töltse le a parancsfájlok] [ sf-aad-ps-script-download] a számítógépre.
2. Kattintson a jobb gombbal a zip-fájlt, jelölje be **tulajdonságok**, jelölje be a **feloldása** jelölőnégyzetet, majd kattintson a **alkalmaz**.
3. Csomagolja ki a tömörített fájlt.
4. Futtatás `SetupApplications.ps1`, és adja meg a TenantId ClusterName és WebApplicationReplyUrl paraméterekként. Példa:

```PowerShell
.\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
```

> [!NOTE]
> Az országos felhők (Azure Government, Azure China, az Azure Germany), meg kell adnia a `-Location` paraméter.

A Bérlőazonosító találhatja meg a PowerShell-parancs végrehajtása `Get-AzureSubscription`. Ez a parancs végrehajtása a TenantId minden előfizetés jeleníti meg.

Az Azure AD-alkalmazások, a parancsfájl által létrehozott előtag ClusterName szolgál. Ez nem pontosan egyeznie kell a tényleges fürt neve. Célja, hogy csak az, hogy egyszerűbb legyen az Azure AD-összetevők leképezése, amelyet éppen használ a Service Fabric-fürthöz.

WebApplicationReplyUrl az alapértelmezett végpont, amely az Azure AD a felhasználóknak ad vissza, miután a bejelentkezés befejezéséhez. Állítsa be ezt a végpontot a Service Fabric Explorert a fürtben, amely alapértelmezés szerint az átemelt:

https://&lt;cluster_domain&gt;:19080/Explorer

Jelentkezzen be az Azure AD-bérlői rendszergazdai jogosultságokkal rendelkező fiók kéri. Miután bejelentkezett, a parancsfájl a webes és natív alkalmazások, amelyek a Service Fabric-fürtöt hoz létre. Ha megtekinti a bérlő alkalmazások a [az Azure portal][azure-portal], két új bejegyzést kell megjelennie:

   * *ClusterName*\_fürt
   * *ClusterName*\_ügyfél

A parancsfájl a JSON az Azure Resource Manager-sablon által igényelt, a fürt létrehozásakor a következő szakaszban, így célszerű, hogy ne zárja be a PowerShell-ablakot jelenít meg.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Felhasználók szerepkörökhöz rendelése
Az alkalmazások, amelyek a fürt létrehozása után a felhasználók hozzárendelése a Service Fabric által támogatott szerepkörök: csak olvasható és a rendszergazdával. A szerepkörök használatával rendelhet a [az Azure portal][azure-portal].

1. Az Azure Portalon válassza ki a bérlő, a jobb felső sarokban.

    ![Válassza ki a bérlő gomb][select-tenant-button]
2. Válassza ki **Azure Active Directory** a bal oldali lapon, és a "nagyvállalati alkalmazások" válassza.
3. Válassza ki az "Összes alkalmazás", majd keresse meg és válassza ki a webalkalmazás, melynek neve például `myTestCluster_Cluster`.
4. Kattintson a **felhasználók és csoportok** fülre.

    ![Felhasználók és csoportok lapon][users-and-groups-tab]
5. Kattintson a **felhasználó hozzáadása** az új oldalon gombra, válassza ki a felhasználó és a szerepkör hozzárendelése, és kattintson a **kiválasztása** gombra a lap alján.

    ![Felhasználók hozzárendelése szerepkörök oldal][assign-users-to-roles-page]
6. Kattintson a **hozzárendelése** gombra a lap alján.

    ![Adja hozzá a megerősítési hozzárendelés][assign-users-to-roles-confirm]

> [!NOTE]
> További információ a Service Fabric-szerepkörök: [szerepköralapú hozzáférés-vezérlés a Service Fabric-ügyfelek](service-fabric-cluster-security-roles.md).
>
>

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Elhárításában nyújtanak segítséget az Azure Active Directory beállítása
Az Azure AD beállításához, és használja azt kihívást jelenthet, így az alábbiakban további információt a mi mindent a hibaelhárításhoz.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>A Service Fabric Explorer kéri, hogy válasszon ki egy tanúsítványt
#### <a name="problem"></a>Probléma
Bejelentkezés után sikeresen az Azure AD a Service Fabric Explorerben, a böngésző adja vissza a kezdőlapra, de egy üzenetben kéri, hogy válasszon ki egy tanúsítványt.

![SFX tanúsítvány párbeszédpanel][sfx-select-certificate-dialog]

#### <a name="reason"></a>Ok
A felhasználó nincs hozzárendelve egy szerepkörhöz az az Azure AD-fürt alkalmazást. Így az Azure AD-hitelesítés sikertelen lesz, Service Fabric-fürtön. A Service Fabric Explorer tanúsítványhitelesítés áll vissza.

#### <a name="solution"></a>Megoldás
Kövesse az utasításokat az Azure AD beállításához, és rendelje hozzá a felhasználói szerepköröket. Ezenkívül azt javasoljuk, hogy kapcsolja be "Az alkalmazás eléréséhez szükséges felhasználói kiosztása" `SetupApplications.ps1` does.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>A PowerShell-lel létesített kapcsolat megszakad egy hiba miatt: "a megadott hitelesítő adatok érvénytelenek"
#### <a name="problem"></a>Probléma
Bejelentkezés után sikeresen az Azure AD "AzureActiveDirectory" biztonsági üzemmódban használatával csatlakozhat a fürthöz PowerShell használatával történik, ha a kapcsolat egy hibaüzenettel meghiúsul: "a megadott hitelesítő adatok érvénytelenek."

#### <a name="solution"></a>Megoldás
Ez a megoldás megegyezik az előző egy.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>A Service Fabric Explorer hibát ad vissza, ha bejelentkezik: "AADSTS50011"
#### <a name="problem"></a>Probléma
Próbáljon meg bejelentkezni az Azure AD a Service Fabric Explorerben, ha az oldal adja vissza a hiba: "AADSTS50011: A válaszcímet &lt;URL-cím&gt; nem egyezik meg a az alkalmazáshoz konfigurált válaszcímekkel: &lt;guid&gt;."

![SFX a válaszcím nem egyezik.][sfx-reply-address-not-match]

#### <a name="reason"></a>Ok
A fürt (web) alkalmazás, amely a Service Fabric Explorer próbál meg Azure AD-val, és a kérés részeként az átirányítási visszatérési URL-címet biztosít. Az URL-cím nem szerepel az Azure AD-alkalmazás, de **válasz URL-cím** listája.

#### <a name="solution"></a>Megoldás
Válassza ki az "Alkalmazásregisztrációk" AAD lapot, válassza ki a fürt alkalmazást, és válassza a **válasz URL-címek** gombra. A "Válasz URL-címek" lapon a Service Fabric Explorer URL-cím hozzáadása a listához, vagy cserélje le a listán szereplő elemek közül. Ha ezzel végzett, mentse a módosítást.

![Webes alkalmazás válasz URL-címe][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Csatlakozás a fürthöz PowerShell-lel az Azure AD-hitelesítés használatával
A Service Fabric-fürthöz csatlakozhat, használja az alábbi PowerShell-példa:

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

További tudnivalókért lásd: [Connect-ServiceFabricCluster parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Felhasználhatja az Azure AD-bérlőhöz több fürtön?
Igen. De ne felejtse el a Service Fabric Explorer URL-cím hozzáadása a fürt (web) alkalmazáshoz. A Service Fabric Explorer nem működik.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Miért is meg kell egy kiszolgálói tanúsítványt engedélyezve van az Azure ad-ben?
FabricClient és FabricGateway hajtsa végre a kölcsönös hitelesítés. Az Azure AD-hitelesítés során az Azure AD-integrációs biztosít egy ügyfél identitást a kiszolgálónak, és a tanúsítvány a kiszolgáló identitásának ellenőrzésére szolgál. A Service Fabric-tanúsítványokkal kapcsolatos további információkért lásd: [X.509-tanúsítványokat és a Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>További lépések
Miután beállította az Azure Active Directory-alkalmazások és a felhasználók számára, beállítás szerepkörök [konfigurálása és üzembe helyezése egy fürtön](service-fabric-cluster-creation-via-arm.md).


<!-- Links -->
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[select-tenant-button]: ./media/service-fabric-cluster-creation-setup-aad/select-tenant-button.png
[users-and-groups-tab]: ./media/service-fabric-cluster-creation-setup-aad/users-and-groups-tab.png
[assign-users-to-roles-page]: ./media/service-fabric-cluster-creation-setup-aad/assign-users-to-roles-page.png
[assign-users-to-roles-confirm]: ./media/service-fabric-cluster-creation-setup-aad/assign-users-to-roles-confirm.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
