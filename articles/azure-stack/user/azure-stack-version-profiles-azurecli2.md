---
title: Csatlakozás az Azure Stack parancssori felülettel |} A Microsoft Docs
description: Az Azure Stacken erőforrásokat üzembe helyezheti és kezelheti a többplatformos parancssori felület (CLI) használata
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: f57a7f1cc255f9c4553384a7568beee1c2ed1752
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42058110"
---
# <a name="use-api-version-profiles-with-azure-cli-20-in-azure-stack"></a>API-verzióprofilok használata az Azure CLI 2.0 az Azure Stackben

A lépésekkel állíthatja be az Azure parancssori felület (CLI) Azure Stack Development Kit-erőforrások kezelése a Linux, Mac és Windows-ügyfélplatformokon ebben a cikkben.

## <a name="install-cli"></a>A CLI telepítése

Jelentkezzen be a fejlesztő munkaállomás, és telepítse a parancssori felület. Az Azure Stack az Azure CLI 2.0-s verziója szükséges. Az ismertetett lépéseket követve telepítheti, amely a [Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) cikk. Győződjön meg arról, ha a telepítés sikeres volt-e, nyisson meg egy terminált vagy egy parancssori ablakot, és futtassa a következő parancsot:

```azurecli
az --version
```

Az Azure CLI és a számítógépen telepített függő könyvtárak verzióját kell megjelennie.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Az Azure Stack hitelesítésszolgáltató főtanúsítványát a megbízható

1. Az Azure Stack hitelesítésszolgáltató főtanúsítványát a GET [az Azure Stack-operátorokról](..\azure-stack-cli-admin.md#export-the-azure-stack-ca-root-certificate) és megbízzon benne. Az Azure Stack hitelesítésszolgáltató főtanúsítványát megbízhatónak, hozzáfűzése a meglévő Python-tanúsítványt.

1. Keresse meg a tanúsítvány helye a gépen. A hely eltérőek lehetnek attól függően, hol telepítette a Python. Rendelkeznie kell [pip](https://pip.pypa.io) és a [certifi](https://pypi.org/project/certifi/) modul telepítve van. A bash használatával a következő Python-parancsot használhatja:

  ```bash  
    python -c "import certifi; print(certifi.where())"
  ```

  Jegyezze fel a tanúsítvány helye. Például: `~/lib/python3.5/site-packages/certifi/cacert.pem`. Az operációs rendszer és a Python telepített verzióját az adott elérési út függ.

### <a name="set-the-path-for-a-development-machine-inside-the-cloud"></a>Állítsa be az elérési út egy fejlesztési gépen belül a felhőben

Ha a CLI az Azure Stack-környezeten belül létrehozott Linux rendszerű gépen futtatja, futtassa az alábbi bash-parancs az elérési útját a tanúsítvány.

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
```

### <a name="set-the-path-for-a-development-machine-outside-the-cloud"></a>Állítsa be az elérési út egy fejlesztési gépen kívül a felhőben

Ha egy gép CLI futtatja **kívül** az Azure Stack-környezet:  

1. Be kell állítania [VPN-kapcsolat Azure stackhez](azure-stack-connect-azure-stack.md).

1. Másolja a portáltól kapott Azure Stack-operátorokról PEM-tanúsítványt, és jegyezze fel a fájlt (PATH_TO_PEM_FILE) helyét.

1. Futtassa a következő parancsokat, a fejlesztő munkaállomás operációs rendszereken befejezési függően.

#### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
```

#### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
```

#### <a name="windows"></a>Windows

```powershell
$pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

$root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$root.Import($pemFile)

Write-Host "Extracting needed information from the cert file"
$md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
$sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
$sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

$issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
$subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
$labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
$serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
$md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
$sha1Entry    = [string]::Format("# SHA1 Finterprint: {0}", $sha1Hash)
$sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated for allowing the azure stack CA root certificate"
```

## <a name="get-the-virtual-machine-aliases-endpoint"></a>A virtuális gép aliasok végpontjának beszerzése

Felhasználók parancssori felület használatával hozhat létre virtuális gépeket, mielőtt azok lépjen kapcsolatba az Azure Stack-operátorokról, és a virtuális gép aliasok végpont URI azonosítója. Például az Azure használja a következő URI Azonosítót: https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json. A felhő rendszergazdájához kell beállítania a hasonló végpont az Azure Stack a képekkel, amely az Azure Stack piactéren érhető el. Felhasználók kell átadni a végpont URI-t, a `endpoint-vm-image-alias-doc` paramétert a `az cloud register` parancsot a következő szakaszban látható módon. 
   

## <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

A következő lépések segítségével csatlakozhat az Azure Stack:

1. Regisztrálja az Azure Stack környezettel futtatásával a `az cloud register` parancsot.
   
   a. Regisztrálja a *felhőalapú felügyeleti* környezet használja:

      ```azurecli
      az cloud register \ 
        -n AzureStackAdmin \ 
        --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

   b. Regisztrálja a *felhasználói* környezet használja:

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

1. Állítsa be az aktív környezetet az alábbi parancsok használatával.

   a. Az a *felhőalapú felügyeleti* környezet használja:

      ```azurecli
      az cloud set \
        -n AzureStackAdmin
      ```

   b. Az a *felhasználói* környezet használja:

      ```azurecli
      az cloud set \
        -n AzureStackUser
      ```

1. Frissítés az Ön környezetének konfigurációját az Azure Stack meghatározott API verzió profil használatára. Frissítse a konfigurációt, futtassa a következő parancsot:

   ```azurecli
   az cloud update \
     --profile 2017-03-09-profile
   ```

1. Jelentkezzen be az Azure Stack-környezet használatával a `az login` parancsot. Bejelentkezhet az Azure Stack-környezet vagy egy felhasználó vagy egy [szolgáltatásnév](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-objects). 

   * Jelentkezzen be egy *felhasználói*: megadhatja a felhasználónevet és jelszót közvetlenül belül a `az login` parancsot, vagy hitelesíteni tudja a webböngésző használatával. Ehhez az utóbbi, ha a fiók rendelkezik-e többtényezős hitelesítés engedélyezve van.

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > Ha a felhasználói fiók rendelkezik a többtényezős hitelesítés engedélyezve van, használhatja a `az login command` anélkül, hogy a `-u` paraméter. A következő parancs futtatásával lehetővé teszi egy URL-CÍMÉT és a egy kódot, amely hitelesítést kell használnia.
   
   * Jelentkezzen be egy *szolgáltatásnév*: jelentkezik be, mielőtt [hozzon létre egy egyszerű szolgáltatást az Azure Portalon keresztül](azure-stack-create-service-principals.md) vagy a parancssori felület, és rendelje hozzá egy szerepkörhöz. Most jelentkezzen be a következő paranccsal:

      ```azurecli
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```

## <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Most, hogy mindent beállítottunk beállítása, hozzunk létre erőforrásokat az Azure Stack belül használatával CLI. Például hozzon létre egy erőforráscsoportot egy alkalmazáshoz, és adjon hozzá egy virtuális gépet. A következő paranccsal hozzon létre egy erőforráscsoportot "MyResourceGroup" nevű:

```azurecli
az group create \
  -n MyResourceGroup -l local
```

Ha az erőforráscsoport létrehozása sikeresen megtörtént, az előző parancs megjeleníti az újonnan létrehozott erőforrás következő tulajdonságai:

![Erőforráscsoport létrehozása a kimenet](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Ismert problémák
Ismert problémák, amelyet be kell ügyelnie, ha az Azure Stack parancssori felület használatával vannak:

 - A CLI interaktív mód azaz a `az interactive` parancs még nem támogatott az Azure Stackben.
 - Az Azure Stackben elérhető virtuálisgép-rendszerképek listájának lekéréséhez használja a `az vm images list --all` parancs helyett a `az vm image list` parancsot. Adja meg a `--all` beállítás gondoskodik arról, hogy a válasz csak a lemezképbe érhetők el az Azure Stack környezettel adja vissza.
 - Az Azure-ban rendelkezésre álló virtuális gép rendszerkép aliasok nem vonatkoznak az Azure Stackhez. Virtuálisgép-lemezképek használata esetén a teljes URN paramétert kell használnia (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) a rendszerkép alias helyett. Ez URN egyeznie kell a lemezkép előírásoknak, származtatva a `az vm images list` parancsot.

## <a name="next-steps"></a>További lépések

[Az Azure CLI-vel sablonok üzembe helyezése](azure-stack-deploy-template-command-line.md)

[Azure CLI-vel engedélyezése az Azure Stack felhasználói (operátor)](..\azure-stack-cli-admin.md)

[Felhasználói engedélyek kezelése](azure-stack-manage-permissions.md)