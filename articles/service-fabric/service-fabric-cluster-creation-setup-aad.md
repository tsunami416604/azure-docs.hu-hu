---
title: Azure Active Directory beállítása Service Fabric ügyfél-hitelesítéshez | Microsoft Docs
description: Megtudhatja, hogyan állíthatja be Azure Active Directory (Azure AD) a Service Fabric-fürtökhöz tartozó ügyfelek hitelesítéséhez.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2019
ms.author: atsenthi
ms.openlocfilehash: 6c195357c4a037534307571a53589b2ae861d88b
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "67486012"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Azure Active Directory beállítása az ügyfél-hitelesítéshez

Az Azure-on futó fürtök esetében a Azure Active Directory (Azure AD) használata ajánlott a felügyeleti végpontokhoz való hozzáférés biztosításához.  Ez a cikk azt ismerteti, hogyan telepítheti az Azure AD-t egy Service Fabric-fürt ügyfeleinek hitelesítésére, amelyet [a fürt létrehozása](service-fabric-cluster-creation-via-arm.md)előtt kell elvégezni.  Az Azure AD lehetővé teszi, hogy a szervezetek (más néven bérlők) az alkalmazásokhoz való felhasználói hozzáférést kezeljék. Az alkalmazások a webes bejelentkezési kezelőfelülettel és a natív ügyfél-felhasználói felülettel rendelkezők számára vannak osztva. 

A Service Fabric-fürtök több belépési pontot biztosítanak a felügyeleti funkcióihoz, beleértve a webalapú [Service Fabric Explorer][service-fabric-visualizing-your-cluster] és a [Visual studiót][service-fabric-manage-application-in-visual-studio]is. Ennek eredményeképpen két Azure AD-alkalmazást hoz létre a fürt elérésének vezérléséhez: egy webalkalmazást és egy natív alkalmazást.  Az alkalmazások létrehozása után a felhasználókat a csak olvasási és rendszergazdai szerepkörökhöz rendelheti hozzá.

> [!NOTE]
> A fürt létrehozása előtt végre kell hajtania a következő lépéseket. Mivel a parancsfájlok a fürtök és a végpontok számára is várnak, az értékeket meg kell tervezni, és nem a már létrehozott értékeket.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy már létrehozott egy bérlőt. Ha nem, először olvassa el [Azure Active Directory bérlő beszerzését ismertető témakört][active-directory-howto-tenant].

Az Azure AD Service Fabric-fürttel való konfigurálásának néhány lépésének leegyszerűsítése érdekében létrehoztunk egy Windows PowerShell-szkriptet.

1. [A](https://github.com/Azure-Samples/service-fabric-aad-helpers) tárház klónozása a számítógépre.
2. [Győződjön meg arról, hogy rendelkezik](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) a telepített parancsfájlokra vonatkozó összes előfeltételsel.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Azure AD-alkalmazások létrehozása és felhasználók szerepkörökhöz rendelése

A szkriptek segítségével két Azure AD-alkalmazást hozhat létre a fürthöz való hozzáférés vezérléséhez: egy webalkalmazást és egy natív alkalmazást. Miután létrehozta az alkalmazásokat a fürt képviseletére, a [Service Fabric által támogatott szerepkörökhöz](service-fabric-cluster-security-roles.md)hozza létre a felhasználókat: csak olvasható és rendszergazda.

Futtassa `SetupApplications.ps1`a parancsot, és adja meg a bérlő azonosítóját, a fürt nevét és a webalkalmazás válaszának URL-címét paraméterként.  Felhasználóneveket és jelszavakat is meg kell adnia a felhasználók számára. Példa:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> A nemzeti felhők (például Azure Government, Azure China, Azure Germany) esetében meg kell adnia a `-Location` paramétert is.

A *TenantId* a PowerShell-parancs `Get-AzureSubscription`végrehajtásával találhatja meg. A parancs végrehajtása az összes előfizetés TenantId jeleníti meg.

A *ClusterName* a parancsfájl által létrehozott Azure ad-alkalmazások előtagját használja. Nem kell pontosan megegyeznie a fürt tényleges nevével. Kizárólag arra szolgál, hogy megkönnyítse az Azure AD-összetevők leképezését arra a Service Fabric-fürtre, amelyen a használatban van.

A *WebApplicationReplyUrl* az az alapértelmezett végpont, amelyet az Azure ad visszaküld a felhasználóknak a bejelentkezés befejezését követően. Állítsa ezt a végpontot a fürt Service Fabric Explorer végpontjának, amely alapértelmezés szerint a következő:

https://&lt;cluster_domain&gt;:19080/Explorer

A rendszer felszólítja, hogy jelentkezzen be egy olyan fiókba, amely rendszergazdai jogosultságokkal rendelkezik az Azure AD-bérlőhöz. A bejelentkezést követően a parancsfájl létrehozza a webes és natív alkalmazásokat, hogy az Service Fabric-fürtöt képviseljék. Ha megtekinti a bérlő alkalmazásait a [Azure Portalban][azure-portal], két új bejegyzést kell látnia:

   * ClusterName\_-fürt
   * ClusterName\_-ügyfél

A parancsfájl kinyomtatja a Azure Resource Manager sablon által a [fürt létrehozásakor](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access)szükséges JSON-t, ezért érdemes megnyitnia a PowerShell ablakát.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Hibaelhárítási segítség a Azure Active Directory beállításához
Az Azure AD beállítása és használata kihívást jelenthet, így a probléma megoldásához szükséges néhány mutatót láthat.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer kéri a tanúsítvány kiválasztását
#### <a name="problem"></a>Probléma
Miután Service Fabric Explorer sikeresen bejelentkezett az Azure AD-be, a böngésző visszatér a kezdőlapra, de egy üzenet arra kéri, hogy válasszon ki egy tanúsítványt.

![SFX tanúsítvány párbeszédpanel][sfx-select-certificate-dialog]

#### <a name="reason"></a>Reason
A felhasználó nem rendel hozzá szerepkört az Azure AD-fürt alkalmazásban. Így az Azure AD-hitelesítés meghiúsul Service Fabric fürtön. Service Fabric Explorer visszatér a tanúsítványalapú hitelesítéshez.

#### <a name="solution"></a>Megoldás
Kövesse az Azure AD beállításával és a felhasználói szerepkörök hozzárendelésével kapcsolatos utasításokat. Azt is javasoljuk, hogy kapcsolja be a "felhasználói hozzárendelés szükséges az alkalmazás eléréséhez" `SetupApplications.ps1` beállítást.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>A PowerShell-lel való kapcsolat hibával meghiúsul: "A megadott hitelesítő adatok érvénytelenek"
#### <a name="problem"></a>Probléma
Ha a PowerShell használatával csatlakozik a fürthöz a "AzureActiveDirectory" biztonsági mód használatával, miután sikeresen bejelentkezett az Azure AD-be, a kapcsolat meghiúsul a következő hibával: "A megadott hitelesítő adatok érvénytelenek."

#### <a name="solution"></a>Megoldás
Ez a megoldás megegyezik az előzővel.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>A Service Fabric Explorer a bejelentkezéskor hibát ad vissza: "AADSTS50011"
#### <a name="problem"></a>Probléma
Amikor az Service Fabric Explorerba próbál bejelentkezni az Azure AD-be, az oldal hibát ad vissza: "AADSTS50011: A válasz &lt;címének&gt; URL-címe nem egyezik az alkalmazáshoz konfigurált válasz címével&gt;: &lt;GUID.

![Az SFX-válasz címe nem egyezik][sfx-reply-address-not-match]

#### <a name="reason"></a>Reason
A fürt (web) alkalmazás, amely Service Fabric Explorer az Azure AD-vel való hitelesítésre irányuló kísérleteket jelképezi, és a kérelem részeként az átirányítási visszaküldési URL-címet adja meg. Az URL-cím azonban nem szerepel az Azure AD **-alkalmazás válaszának URL-címe** listán.

#### <a name="solution"></a>Megoldás
A HRE lapon válassza a "Alkalmazásregisztrációk" lehetőséget, válassza ki a fürtöt, majd kattintson a **Válasz URL-címek** gombra. A válasz URL-címek lapon adja hozzá Service Fabric Explorer URL-címét a listához, vagy cserélje le a lista egyik elemét. Ha elkészült, mentse a módosítást.

![Webalkalmazás válaszának URL-címe][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>A fürt összekötése az Azure AD-hitelesítéssel a PowerShell segítségével
A Service Fabric-fürt összekapcsolásához használja a következő PowerShell-parancsot:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

További információ: a [ServiceFabricCluster parancsmag](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Felhasználhatom ugyanazt az Azure AD-bérlőt több fürtben?
Igen. Azonban ne felejtse el felvenni a Service Fabric Explorer URL-címét a fürt (webes) alkalmazásba. Ellenkező esetben a Service Fabric Explorer nem működik.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Miért van szükség a kiszolgálói tanúsítványra, amíg az Azure AD engedélyezve van?
A FabricClient és a FabricGateway kölcsönös hitelesítést hajt végre. Az Azure AD-hitelesítés során az Azure AD-integráció egy ügyfél-identitást biztosít a kiszolgálónak, és a kiszolgálói tanúsítvány a kiszolgáló identitásának ellenőrzésére szolgál. További információ a Service Fabric tanúsítványokról: [X. 509 tanúsítványok és Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>További lépések
Azure Active Directory alkalmazások beállítása és szerepkörök beállítása a felhasználók számára, [a fürt konfigurálása és üzembe helyezése](service-fabric-cluster-creation-via-arm.md).


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
