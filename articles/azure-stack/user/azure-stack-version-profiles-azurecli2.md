---
title: Csatlakozás az Azure CLI verem |} Microsoft Docs
description: A platformfüggetlen parancssori felület (CLI) használatával kezelheti és telepítheti az Azure-veremben erőforrások használata
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: eb01d31d00177560aca3aa71750cd2d1ec096f8f
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938409"
---
# <a name="use-api-version-profiles-with-azure-cli-20-in-azure-stack"></a>Azure CLI 2.0 Azure verem API-verzió profilok használata

A lépések a cikk beállítása az Azure parancssori felület (CLI) a ügyfélplatformokon Linux, Mac és a Windows Azure verem szoftverfejlesztői készlet-erőforrások kezeléséhez.

## <a name="install-cli"></a>A CLI telepítése

Jelentkezzen be a fejlesztő munkaállomás és parancssori felület telepítése. Az Azure verem Azure CLI 2.0-s verziója szükséges. Ismertetett eljárások segítségével telepítheti, amely a [Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) cikk. Győződjön meg arról, hogy a telepítés sikeres volt-e, nyisson meg egy terminál vagy egy parancssori ablakot, és futtassa a következő parancsot:

```azurecli
az --version
```

Meg kell jelennie az Azure CLI és más függő tárak, a számítógépre telepített verzióját.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Az Azure-verem hitelesítésszolgáltató főtanúsítványát megbízhatóság

1. Az Azure-verem hitelesítésszolgáltató főtanúsítványát az beszerzése [az Azure-verem operátor](..\azure-stack-cli-admin.md#export-the-azure-stack-ca-root-certificate) és megbízhatónak tekinti. Azure verem hitelesítésszolgáltató legfelső szintű tanúsítvány megbízható fűzi azokat hozzá a meglévő Python-tanúsítványt.

2. A tanúsítvány helye a számítógépen található. A hely eltérhetnek attól függően, hogy hol telepítette a Python. Rendelkeznie kell [pip](https://pip.pypa.io) és a [certifi](https://pypi.org/project/certifi/) telepített modulokban. A következő Python parancsot a bash parancssorból használhatja:

  ```bash  
    python -c "import certifi; print(certifi.where())"
  ```

  Jegyezze fel a tanúsítvány helye. Például: `~/lib/python3.5/site-packages/certifi/cacert.pem`. Az elérési utat leíró függ az operációs rendszer és a Python telepített verzióját.

### <a name="set-the-path-for-a-development-machine-inside-the-cloud"></a>A felhő állítható be a fejlesztési számítógépén elérési útja

Ha a CLI Azure verem környezetben létrehozott Linux-gépek futtatja, futtassa a következő bash parancsot a következő elérési a tanúsítványt.

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
```

### <a name="set-the-path-for-a-development-machine-outside-the-cloud"></a>A fejlesztői számítógépén elérési útját a felhő kívüli beállítása

Ha egy gép CLI futtat **kívül** az Azure-verem környezetben:  

1. Be kell állítania [Azure verem VPN-kapcsolat](azure-stack-connect-azure-stack.md).

2. Másolja a portáltól kapott Azure verem operátor PEM-tanúsítványt, és jegyezze fel annak a helynek a fájl (PATH_TO_PEM_FILE).

3. A következő parancsokat, attól függően befejezési a fejlesztő munkaállomás operációs rendszeren.

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

## <a name="get-the-virtual-machine-aliases-endpoint"></a>A virtuális gép aliasok végpontjának lekérése

Felhasználók parancssori felület használatával hozhat létre virtuális gépeket, mielőtt kell lépjen kapcsolatba az Azure-verem operátor és a virtuális gép aliasok végpont URI beolvasása. Például az Azure használja a következő URI Azonosítót: https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json. A felhő rendszergazdájához kell állítani egy hasonló végpont Azure verem a a verem Azure piactéren elérhető képek. Felhasználók kell átadni a végpont URI számára a `endpoint-vm-image-alias-doc` paramétert a `az cloud register` parancsot a következő szakaszban ismertetett módon. 
   

## <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

Azure verem való csatlakozáshoz tegye a következőket:

1. A verem Azure környezetben futtatásával regisztrálja a `az cloud register` parancsot.
   
   a. Regisztrálja a *felügyeleti felhő* környezetben használja:

      ```azurecli
      az cloud register \ 
        -n AzureStackAdmin \ 
        --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

   b. Regisztrálja a *felhasználói* környezetben használja:

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

2. Állítsa be az aktív környezet az alábbi parancsok használatával.

   a. Az a *felügyeleti felhő* környezetben használja:

      ```azurecli
      az cloud set \
        -n AzureStackAdmin
      ```

   b. Az a *felhasználói* környezetben használja:

      ```azurecli
      az cloud set \
        -n AzureStackUser
      ```

3. Frissítse a környezet konfigurációját az Azure verem meghatározott API verziója profilt kell használnia. A konfigurációs frissítéséhez futtassa a következő parancsot:

   ```azurecli
   az cloud update \
     --profile 2017-03-09-profile
   ```

4. Jelentkezzen be a Azure verem környezet használatával a `az login` parancsot. Bejelentkezhet az Azure-verem környezet vagy a felhasználó vagy egy [egyszerű](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-objects). 

   * Jelentkezzen be egy *felhasználói*: megadhatja a felhasználónevet és jelszót közvetlenül belül a `az login` parancsot, vagy egy böngésző segítségével hitelesíteni. Ehhez az utóbbi, ha a fiók rendelkezik-e többtényezős hitelesítés engedélyezve van.

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > Ha a felhasználói fiók rendelkezik a többtényezős hitelesítés engedélyezve van, használhatja a `az login command` anélkül, hogy a `-u` paraméter. A parancs futtatása lehetővé teszi egy URL-címet és egy kódot, amely hitelesíteni kell használnia.
   
   * Jelentkezzen be a egy *egyszerű*: amikor bejelentkezik a, [hozzon létre egy egyszerű szolgáltatást az Azure portálon keresztül](azure-stack-create-service-principals.md) vagy a CLI-t, és rendelje hozzá egy szerepkört. Most jelentkezzen be a következő paranccsal:

      ```azurecli
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```

## <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Most, hogy minden van beállítani, most használja a CLI Azure verem erőforrások létrehozásához. Például hozzon létre egy erőforráscsoportot az alkalmazáshoz, és adja hozzá a virtuális gépet. Az alábbi parancs segítségével hozzon létre egy erőforráscsoportot a "Contoso.com" nevű:

```azurecli
az group create \
  -n MyResourceGroup -l local
```

Az erőforráscsoport létrehozása sikeres, ha az előző parancs kimenete az újonnan létrehozott erőforrás következő tulajdonságai:

![Erőforráscsoport létrehozása kimeneti](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Ismert problémák
Nincsenek ismert problémák a kell ügyelnie, ha a CLI Azure-készletben:

 - A parancssori felület interaktív módban Egytényezős a `az interactive` parancs még nem támogatott Azure-készletben.
 - Azure verem használható virtuálisgép-rendszerképek listájának lekéréséhez használja a `az vm images list --all` parancs helyett a `az vm image list` parancsot. Adja meg a `--all` beállítás gondoskodik arról, hogy a válasz csak az Azure-verem környezetben elérhető képek adja vissza.
 - Az Azure-ban rendelkezésre álló virtuális gép lemezképének aliasok nem lehet Azure verem használható. Virtuálisgép-rendszerképek használata esetén a teljes URN paramétert kell használnia (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) helyett a kép alias. Ez URN meg kell egyeznie a kép specifikációk származtatva a `az vm images list` parancsot.

## <a name="next-steps"></a>További lépések

[Sablonok az Azure parancssori felület telepítése](azure-stack-deploy-template-command-line.md)

[Azure CLI Azure verem felhasználók (operátor) engedélyezése](..\azure-stack-cli-admin.md)

[Felhasználói engedélyek kezelése](azure-stack-manage-permissions.md)