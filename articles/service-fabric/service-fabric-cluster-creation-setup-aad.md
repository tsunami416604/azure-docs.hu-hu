---
title: Az Azure Active Directory beállítása az ügyfélhitelesítéshez
description: Megtudhatja, hogyan állíthatja be az Azure Active Directoryt (Azure AD) a Service Fabric-fürtök ügyfeleinek hitelesítéséhez.
ms.topic: conceptual
ms.date: 6/28/2019
ms.openlocfilehash: 28c4c65cfcc77607dfe9a463a09ecd10389a6eca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78193380"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Az Azure Active Directory beállítása az ügyfélhitelesítéshez

Az Azure-on futó fürtök esetében az Azure Active Directory (Azure AD) ajánlott a felügyeleti végpontokhoz való biztonságos hozzáférés hez. Ez a cikk ismerteti, hogyan kell beállítani az Azure AD-t a Service Fabric-fürt ügyfelek hitelesítéséhez.

Ebben a cikkben az "alkalmazás" kifejezés az [Azure Active Directory-alkalmazásokra,](../active-directory/develop/developer-glossary.md#client-application)nem a Service Fabric-alkalmazásokra vonatkozik; szükség esetén különbséget kell tenni. Az Azure AD lehetővé teszi a szervezetek (más néven bérlők) az alkalmazások felhasználói hozzáférésének kezelése.

A Service Fabric-fürt számos belépési pontot kínál a felügyeleti funkcióihoz, beleértve a webalapú [Service Fabric Explorert][service-fabric-visualizing-your-cluster] és a [Visual Studio-t.][service-fabric-manage-application-in-visual-studio] Ennek eredményeképpen két Azure AD-alkalmazást hoz létre a fürthöz való hozzáférés szabályozásához: egy webalkalmazást és egy natív alkalmazást. Az alkalmazások létrehozása után a felhasználókat írásvédett és rendszergazdai szerepkörökhöz rendeli.

> [!NOTE]
> Linux on a fürt létrehozása előtt végre kell hajtsa végre a következő lépéseket. A Windows rendszerben az [Azure AD-hitelesítést is konfigurálhatja egy meglévő fürthöz.](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/Configure%20Azure%20Active%20Directory%20Authentication%20for%20Existing%20Cluster.md)

> [!NOTE]
> Ismert [probléma,](https://github.com/microsoft/service-fabric/issues/399) hogy a Linux AAD-kompatibilis fürtök alkalmazásai és csomópontjai nem tekinthetők meg az Azure Portalon.



## <a name="prerequisites"></a>Előfeltételek
Ebben a cikkben feltételezzük, hogy már létrehozott egy bérlőt. Ha még nem, kezdje el olvasni [Az Azure Active Directory-bérlő beolvasása][active-directory-howto-tenant].

Az Azure AD Service Fabric-fürttel történő konfigurálása néhány lépés ének egyszerűsítése érdekében létrehoztunk egy Windows PowerShell-parancsfájlkészletet.

1. [Klónozza a tárta](https://github.com/Azure-Samples/service-fabric-aad-helpers) a számítógépre.
2. [Győződjön meg arról, hogy a parancsfájlok összes előfeltétele](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) telepítve van.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Azure AD-alkalmazások létrehozása és felhasználók hozzárendelése szerepkörökhöz

A parancsfájlok használatával két Azure AD-alkalmazást hozunk létre a fürthöz való hozzáférés szabályozásához: egy webalkalmazást és egy natív alkalmazást. Miután létrehozta a fürtet képviselő alkalmazásokat, felhasználókat hoz létre a [Service Fabric által támogatott szerepkörökhöz:](service-fabric-cluster-security-roles.md)csak olvasható és rendszergazdai.

Futtassa `SetupApplications.ps1`a , és adja meg a bérlőazonosítót, a fürt nevét és a webalkalmazás válaszÁNAK URL-címét paraméterekként.  Adja meg a felhasználók felhasználónevét és jelszavát is. Példa:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> A nemzeti felhők (például az Azure Government, az Azure `-Location` China, az Azure Germany), meg kell adnia a paramétert.

A *TenantId* a PowerShell parancs `Get-AzureSubscription`végrehajtásával kereshető. A parancs végrehajtása minden előfizetéshez megjeleníti a TenantId azonosítót.

*A ClusterName* a parancsfájl által létrehozott Azure AD-alkalmazások előtagozására szolgál. Nem kell pontosan egyeznie a tényleges fürtnevével. Célja, hogy megkönnyítse az Azure AD-összetevők leképezése a Service Fabric-fürthöz, amely hez használatosak.

*A WebApplicationReplyUrl* az az alapértelmezett végpont, amelyet az Azure AD a bejelentkezés befejezése után visszaküld a felhasználóknak. Állítsa be ezt a végpontot a fürt Service Fabric Explorer végpontjaként. Ha egy meglévő fürt képviseletére azure AD-alkalmazásokat hoz létre, győződjön meg arról, hogy ez az URL-cím megfelel a meglévő fürt végpontjának. Ha új fürthöz hoz létre alkalmazásokat, tervezze meg a fürt végpontját, és győződjön meg arról, hogy nem használja egy meglévő fürt végpontját. Alapértelmezés szerint a Service Fabric Explorer végpontja:

https://&lt;&gt;cluster_domain :19080/Explorer

A rendszer kéri, hogy jelentkezzen be egy olyan fiókba, amely rendszergazdai jogosultságokkal rendelkezik az Azure AD-bérlőhöz. Miután bejelentkezik, a parancsfájl létrehozza a webes és natív alkalmazásokat a Service Fabric-fürt képviseletére. Ha megnézi a bérlői alkalmazásokat az [Azure Portalon,][azure-portal]két új bejegyzést kell látnia:

   * *Fürtnév*\_fürt
   * *Fürtnév-ügyfél*\_

A parancsfájl kinyomtatja az Azure Resource Manager sablon által igényelt JSON-t [az AAD-kompatibilis fürt létrehozásakor,](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access)ezért célszerű nyitva tartani a PowerShell-ablakot.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Hibaelhárítási súgó az Azure Active Directory konfigurálásához
Az Azure AD beállítása és használata kihívást jelenthet, ezért az alábbiakban néhány mutatót olvashat a probléma hibakereséséhez.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>A Service Fabric Explorer kéri, hogy válasszon ki egy tanúsítványt
#### <a name="problem"></a>Probléma
Miután sikeresen bejelentkezett az Azure AD szolgáltatásfabric-kezelőben, a böngésző visszatér a kezdőlapra, de egy üzenet kéri, hogy válasszon ki egy tanúsítványt.

![SFX tanúsítvány párbeszédpanel][sfx-select-certificate-dialog]

#### <a name="reason"></a>Ok
A felhasználó nincs hozzárendelve egy szerepkör az Azure AD fürtalkalmazásban. Így az Azure AD-hitelesítés sikertelen a Service Fabric-fürtön. A Service Fabric Explorer visszaáll a tanúsítvány hitelesítésére.

#### <a name="solution"></a>Megoldás
Kövesse az Azure AD beállítására vonatkozó utasításokat, és rendeljen hozzá felhasználói szerepköröket. Azt is javasoljuk, hogy kapcsolja be a "Felhasználói `SetupApplications.ps1` hozzárendelés szükséges az alkalmazás eléréséhez", ahogy azt is.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>A PowerShellnel való kapcsolat a következő hibával sikertelen: "A megadott hitelesítő adatok érvénytelenek"
#### <a name="problem"></a>Probléma
Ha a PowerShell használatával csatlakozik a fürthöz az "AzureActiveDirectory" biztonsági mód használatával, miután sikeresen bejelentkezett az Azure AD-be, a kapcsolat a következő hibával sikertelen: "A megadott hitelesítő adatok érvénytelenek."

#### <a name="solution"></a>Megoldás
Ez a megoldás megegyezik az előzővel.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>A Service Fabric Explorer hibát ad vissza, amikor bejelentkezik: "AADSTS50011"
#### <a name="problem"></a>Probléma
Amikor megpróbál bejelentkezni az Azure AD szolgáltatásfabric-kezelőben, a lap hibát ad vissza: "AADSTS50011: &lt;A válaszcím url&gt; nem egyezik meg az alkalmazáshoz beállított válaszcímek: &lt;guid."&gt;

![Az SFX válaszcíme nem egyezik][sfx-reply-address-not-match]

#### <a name="reason"></a>Ok
A fürt (webes) alkalmazás, amely a Service Fabric Explorer hitelesítési azure AD-vel, és a kérelem részeként biztosítja az átirányítási visszaadott URL-címet. De az URL-cím nem szerepel az Azure AD alkalmazás **VÁLASZ URL-címlistájában.**

#### <a name="solution"></a>Megoldás
A fürthöz az Azure AD alkalmazás regisztrációs lapján válassza a **Hitelesítés**lehetőséget, és az **Irányított URI-k átirányítása** szakaszban adja hozzá a Service Fabric Explorer URL-címét a listához. Mentse el a visszajárót.

![Webalkalmazás válaszának URL-címe][web-application-reply-url]

### <a name="connecting-to-the-cluster-using-azure-ad-authentication-via-powershell-gives-an-error-when-you-sign-in-aadsts50011"></a>A fürthöz való csatlakozás az Azure AD-hitelesítés használatával PowerShell-en keresztül hibaüzenetet ad, amikor bejelentkezik: "AADSTS50011"
#### <a name="problem"></a>Probléma
Amikor az Azure AD használatával próbál csatlakozni egy Service Fabric-fürthöz a PowerShellen keresztül, a bejelentkezési lap hibát ad vissza: "AADSTS50011: A kérelemben megadott válasz url-cím nem egyezik meg az alkalmazáshoz beállított válasz URL-címekkel: &lt;guid."&gt;

#### <a name="reason"></a>Ok
Az előző problémához hasonlóan a PowerShell megkísérli a hitelesítést az Azure AD-vel szemben, amely egy átirányítási URL-címet biztosít, amely nem szerepel az Azure AD-alkalmazás **Válasz URL-címek** listájában.  

#### <a name="solution"></a>Megoldás
Használja ugyanazt a folyamatot, mint az előző `urn:ietf:wg:oauth:2.0:oob`kiadásban, de az URL-címet a , a parancssori hitelesítéshez speciális átirányításra kell állítani.

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>A fürt csatlakoztatása az Azure AD-hitelesítés használatával a PowerShellen keresztül
A Service Fabric-fürt csatlakoztatásához használja a következő PowerShell-parancspéldát:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

További információ: [Connect-ServiceFabricCluster parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Felhasználhatom újra ugyanazt az Azure AD-bérlőt több fürtben?
Igen. De ne feledje, hogy adja hozzá a Service Fabric Explorer URL-címét a fürt (web) alkalmazáshoz. Ellenkező esetben a Service Fabric Explorer nem működik.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Miért van szükség továbbra is kiszolgálói tanúsítványra, amíg az Azure AD engedélyezve van?
A FabricClient és a FabricGateway kölcsönös hitelesítést hajt végre. Az Azure AD-hitelesítés során az Azure AD-integráció ügyfélidentitást biztosít a kiszolgálószámára, és a kiszolgáló tanúsítványát az ügyfél a kiszolgáló identitásának ellenőrzésére használja. A Service Fabric-tanúsítványokról további információt az [X.509-es tanúsítványok és a Service Fabric című témakörben talál.][x509-certificates-and-service-fabric]

## <a name="next-steps"></a>További lépések
Az Azure Active Directory-alkalmazások beállítása és a szerepkörök beállítása után [konfiguráljon és telepítsen egy fürtöt.](service-fabric-cluster-creation-via-arm.md)


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
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
