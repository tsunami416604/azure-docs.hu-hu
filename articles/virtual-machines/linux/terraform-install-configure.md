---
title: "Telepítse és konfigurálja a virtuális gépek és más Azure-infrastruktúra kiépítése Terraform |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítse és konfigurálja az Azure-erőforrások létrehozásához Terraform"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/14/2017
ms.author: echuvyrov
ms.openlocfilehash: da567097be38ac649c6bf1de1508de24d21cb877
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Telepítse és konfigurálja az Azure virtuális gépek és egyéb infrastruktúra kiépítéséhez Terraform 
Ez a cikk ismerteti a telepítése és konfigurálása a Terraform rendelkezés erőforrások, például virtuális gépek az Azure szükséges lépéseket. Megtudhatja, hogyan létrehozása és engedélyezése a felhőalapú erőforrások kiépítése biztonságos módon Terraform Azure hitelesítő adatait használja.

HashiCorp Terraform könnyedén definiálására és központi telepítésére a felhőalapú infrastruktúra egy HashiCorp konfigurációs nyelvi (Hardverkompatibilitási) nevű egyéni templating nyelv használatával. Az egyéni nyelv [könnyen írási és megértse](terraform-create-complete-vm.md). Továbbá használatával a `terraform plan` paranccsal jelenítheti meg a módosításokat a infrastruktúra előtt véglegesítse azokat. Kövesse az alábbi lépéseket az Azure-ral Terraform használatának megkezdéséhez.

## <a name="install-terraform"></a>Terraform telepítése
Terraform, telepítendő [letöltése](https://www.terraform.io/downloads.html) egy különálló, az operációs rendszerhez megfelelő csomag telepítési könyvtár. A letöltés egy egyetlen végrehajtható fájlt, amelynek meg kell is meg a globális elérési utat tartalmaz. Hogyan kell beállítani az elérési utat a Linux és Mac útmutatásra van szüksége, látogasson el [a weblap](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Hogyan lehet beállítani az elérési út a Windows útmutatásra van szüksége, látogasson el [a weblap](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows). Ellenőrizze a telepítést, futtassa a `terraform` parancsot. Az elérhető Terraform listáját output típusúként kell megjelennie.

Ezt követően kell az Azure-előfizetéshez infrastruktúra létesítéséhez Terraform eléréséhez.

## <a name="set-up-terraform-access-to-azure"></a>Az Azure-bA Terraform hozzáférés beállítása
Az Azure Active Directory (Azure AD) két olyan entitásra létrehozásához szükséges engedélyezéséhez Terraform rendelkezés erőforrások az Azure: egy Azure AD-alkalmazást és az Azure AD szolgáltatás egyszerű. Ezt követően ezeket az entitásokat azonosítók használhatja a Terraform parancsfájlokat. Egy egyszerű egy helyi példányát egy globális Azure AD-alkalmazást. Egy egyszerű szolgáltatás lehetővé teszi, hogy részletes helyi hozzáférés-vezérlést az globális erőforrások.

Több módon is hozzon létre egy Azure AD-alkalmazást és az Azure AD szolgáltatás egyszerű. A legegyszerűbb leggyorsabb módon ma pedig úgy, hogy használja az Azure CLI 2.0, amely [, töltse le és telepítse a Windows, Linux és Mac](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). Is használhatja PowerShell vagy Azure CLI 1.0 a szükséges biztonsági infrastruktúra létrehozásához. Az alábbi utasításokat konfigurálása az Azure-Terraform összes ezek a módszerek használatával megjelenítése.

### <a name="use-azure-cli-20-for-windows-linux-or-mac-users"></a>Azure CLI 2.0 használja (a Windows, Linux és Mac-felhasználók számára) 
Követően töltse le és telepítse a [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli), jelentkezzen be az Azure-előfizetéshez felügyeletéhez a következő parancsot:

```
az login
```

>[!NOTE]
>Ha a kínai, a Németországi Azure vagy az Azure Government felhők használja, először konfiguráljon az adott felhő használható az Azure parancssori felület szeretné. Ehhez futtassa a következő:

```
az cloud set --name AzureChinaCloud|AzureGermanCloud|AzureUSGovernment
```

Ha több Azure-előfizetéssel rendelkezik, azok adatai által visszaadott a `az login` parancsot. Állítsa be a `SUBSCRIPTION_ID` ahhoz, hogy a visszaadott érték környezeti változó `id` mező szeretné használni az előfizetésből. 

Állítsa be az ehhez a munkamenethez használni kívánt előfizetést.

```
az account set --subscription="${SUBSCRIPTION_ID}"
```

A lekérdezés a fiókot az előfizetés-azonosító és a bérlői azonosító értékek lekérése.

```
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Következő lépésként hozzon létre külön hitelesítő adatokat Terraform.

```
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Visszaadja a appId, a jelszót, a sp_name és a bérlői. Jegyezze fel az appId és a jelszót.

Erősítse meg a hitelesítő adatait (egyszerű szolgáltatásnév), nyissa meg az új rendszerhéjat, és futtassa a következő parancsokat. Helyettesítse be sp_name, jelszót és bérlői által visszaadott érték:

```
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

### <a name="use-powershell-for-windows-users"></a>Használja a Powershellt (Windows-felhasználó) 
Használata Windows írási és végrehajtási a Terraform parancsfájlok és a konfigurációs feladatok PowerShell használatával, konfigurálja a gép a megfelelő PowerShell-eszközökkel. 

1. PowerShell-eszközök lépéseit követve telepítse [telepítse és konfigurálja az Azure Powershellt](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps). 

2. Letöltés és végrehajtás a [azure-setup.ps1 parancsfájl](https://github.com/echuvyrov/terraform101/blob/master/azureSetup.ps1) a PowerShell-konzolon.

3. Az azure-setup.ps1 parancsfájl futtatásához, töltse le, és hajtsa végre a `./azure-setup.ps1 setup` parancsot a PowerShell-konzolon. Majd jelentkezzen be rendszergazdai jogosultságokkal rendelkező Azure-előfizetése.

4. Adjon meg egy alkalmazásnevet (tetszőleges karakterlánc, kötelező) Ha a rendszer kéri. Ha szükséges adjon meg egy erős jelszót, amikor a rendszer kéri. Ha nem ad meg egy jelszót, egy erős jelszót hozza létre az Ön .NET biztonsági könyvtárak segítségével.

### <a name="use-azure-cli-10-for-linux-or-mac-users"></a>Azure CLI 1.0 használata (a Linux- vagy Mac-felhasználók számára)
Megkezdéséhez Terraform Linux-gépek vagy az Azure CLI 1.0 Mac számítógépekhez, telepítse a megfelelő könyvtárak a számítógépen.  

1. Azure xPlat parancssori felület teszttelepítése lépéseit követve telepítse [Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli). 

2. Töltse le és telepítse a JSON processzor utasításait követve [jq letöltése](https://stedolan.github.io/jq/download/).

3. Letöltés és végrehajtás a [azure-setup.sh parancsfájl](https://github.com/mitchellh/packer/blob/master/contrib/azure-setup.sh) bash parancsfájl a konzolról.

4. Az azure-setup.sh parancsfájl futtatásához, töltse le, és hajtsa végre a `./azure-setup setup` parancsot a konzolról. Majd jelentkezzen be rendszergazdai jogosultságokkal rendelkező Azure-előfizetése.
 
5. Adjon meg egy alkalmazásnevet (tetszőleges karakterlánc, kötelező) Ha a rendszer kéri. Ha szükséges adjon meg egy erős jelszót, amikor a rendszer kéri. Ha nem ad meg egy jelszót, egy erős jelszót hozza létre az Ön .NET biztonsági könyvtárak segítségével.

A korábbi parancsfájlok hozzon létre egy Azure AD-alkalmazást és a szolgáltatás egyszerű. Az egyszerű szolgáltatás lekérdezi a közreműködői vagy a tulajdonos szintű hozzáférés az előfizetésben. Hozzáférést biztosít a magas szintű miatt mindig a parancsfájlok által létrehozott biztonsági információ védelme érdekében. Jegyezze fel az összes négy adatra biztonsági a parancsfájlok által biztosított: appId, jelszó ELŐFIZETÉS_AZONOSÍTÓJA és tenant_id.

## <a name="set-environment-variables"></a>Környezeti változók beállítása
Létrehozott, és konfigurálja az Azure AD szolgáltatás egyszerű, kell ahhoz, hogy ismeri a bérlő azonosítója, előfizetés-azonosító, ügyfél-Azonosítót és titkos ügyfélkulcsot használandó Terraform. Ezt megteheti a Terraform parancsfájlokban ágyaz be ezeket az értékeket a [létrehozása alapvető infrastruktúra Terraform használatával](terraform-create-complete-vm.md). Másik lehetőségként beállíthatja a következő környezeti változók (és így elkerülhető a véletlenül ellenőrzés, vagy a hitelesítő adatok megosztása):

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

Ez a parancsfájlpélda rendszerhéj segítségével állítsa be ezeket a változókat:

```
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id
```

Emellett ha Terraform vagy kínai, vagy Azure Government az Azure vagy a Németországi Azure használ, be kell a környezeti változó megfelelően.

## <a name="next-steps"></a>Következő lépések
Ezzel Terraform telepített és konfigurált Azure hitelesítő adataival, hogy elindíthatja az Azure-előfizetéséhez olyan infrastruktúra üzembe. A következő megtudhatja, hogyan [hozzon létre infrastruktúra Terraform](terraform-create-complete-vm.md).
