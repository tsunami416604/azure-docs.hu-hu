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
ms.date: 01/24/2019
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 15354cd7472e7cffb7a40ca431bc23eb65b9a9a9
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845894"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>API-verzióprofilok használata az Azure CLI-vel az Azure Stackben

A lépésekkel állíthatja be az Azure parancssori felület (CLI) Azure Stack Development Kit-erőforrások kezelése a Linux, Mac és Windows-ügyfélplatformokon ebben a cikkben.

## <a name="install-cli"></a>A CLI telepítése

Jelentkezzen be a fejlesztő munkaállomás, és telepítse a parancssori felület. Az Azure Stack 2.0 vagy az Azure CLI-vel újabb verziója szükséges. Ismertetett lépéseket követve telepítheti azt a verziót a [az Azure CLI telepítése](/cli/azure/install-azure-cli) cikk. Annak ellenőrzéséhez, hogy a telepítés sikeres volt-e, nyisson meg egy terminált vagy parancssort, és futtassa a következő parancsot:

```azurecli
az --version
```

Az Azure CLI és a számítógépen telepített függő könyvtárak verzióját kell megjelennie.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Az Azure Stack hitelesítésszolgáltató főtanúsítványát a megbízható

1. Az Azure Stack hitelesítésszolgáltató főtanúsítványát a GET [az Azure Stack-operátorokról](../azure-stack-cli-admin.md#export-the-azure-stack-ca-root-certificate) és megbízzon benne. Az Azure Stack hitelesítésszolgáltató főtanúsítványát megbízhatónak, hozzáfűzése a meglévő Python-tanúsítványt.

1. Keresse meg a tanúsítvány helye a gépen. A hely eltérőek lehetnek attól függően, hol telepítette a Python. Rendelkeznie kell [pip](https://pip.pypa.io) és a [certifi](https://pypi.org/project/certifi/) modul telepítve van. A bash használatával a következő Python-parancsot használhatja:

    ```bash  
    python -c "import certifi; print(certifi.where())"
    ```

    Jegyezze fel a tanúsítvány helye; Ha például `~/lib/python3.5/site-packages/certifi/cacert.pem`. A megadott elérési út attól függ, az operációs rendszer és a Python telepített verzióját.

### <a name="set-the-path-for-a-development-machine-inside-the-cloud"></a>Állítsa be az elérési út egy fejlesztési gépen belül a felhőben

Ha a CLI az Azure Stack-környezeten belül létrehozott Linux rendszerű gépen futtatja, futtassa az alábbi bash-parancs az elérési útját a tanúsítvány.

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
```

### <a name="set-the-path-for-a-development-machine-outside-the-cloud"></a>Állítsa be az elérési út egy fejlesztési gépen kívül a felhőben

Ha futtatja a parancssori felület kívül az Azure Stack-környezet gépről:  

1. Állítsa be a [VPN-kapcsolat Azure stackhez](azure-stack-connect-azure-stack.md).
1. Másolja a helyről az Azure Stack-operátorokról PEM-tanúsítványt, és jegyezze fel a fájlt (PATH_TO_PEM_FILE) helyét.
1. Futtassa a parancsokat a következő szakaszokban a fejlesztői munkaállomáson operációs rendszertől függően.

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

Write-Host "Extracting required information from the cert file"
$md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
$sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
$sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

$issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
$subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
$labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
$serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
$md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
$sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
$sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
```

## <a name="get-the-virtual-machine-aliases-endpoint"></a>A virtuális gép aliasok végpontjának beszerzése

Virtuális gépek létrehozása CLI-vel, előtt kell lépjen kapcsolatba az Azure Stack-operátorokról és a virtuális gép aliasok végpont URI azonosítója. Például az Azure használja a következő URI Azonosítót: `https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json`. A felhő rendszergazdájához kell beállítania a hasonló végpont az Azure Stack a képekkel, amely az Azure Stack piactéren érhető el. Át kell adnia a végpont URI-t, a `endpoint-vm-image-alias-doc` paramétert a `az cloud register` parancsot, a következő szakaszban látható módon. 
  
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
    c. Regisztrálja a *felhasználói* egy több-Bérlős környezetben használni:

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases> \
        --endpoint-active-directory-resource-id=<URI of the ActiveDirectoryServiceEndpointResourceID> \
        --profile 2018-03-01-hybrid
      ```
    d. Felhasználó regisztrálása az AD FS-környezetben, használja:

      ```azurecli
      az cloud register \
        -n AzureStack  \
        --endpoint-resource-manager "https://management.local.azurestack.external" \
        --suffix-storage-endpoint "local.azurestack.external" \
        --suffix-keyvault-dns ".vault.local.azurestack.external"\
        --endpoint-active-directory-resource-id "https://management.adfs.azurestack.local/<tenantID>" \
        --endpoint-active-directory-graph-resource-id "https://graph.local.azurestack.external/"\
        --endpoint-active-directory "https://adfs.local.azurestack.external/adfs/"\
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases> \
        --profile "2018-03-01-hybrid"
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
      --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Ha egy Azure Stack verziója előtt a 1808 build futtat, az API-verzióprofil kell használnia **2017-03-09-profile** ahelyett, hogy az API-verzióprofil **2018-03-01-hibrid**.

1. Jelentkezzen be az Azure Stack-környezet használatával a `az login` parancsot. Bejelentkezhet az Azure Stack-környezet vagy egy felhasználó vagy egy [szolgáltatásnév](../../active-directory/develop/app-objects-and-service-principals.md). 

    * Az Azure AD-környezet
      * Jelentkezzen be egy *felhasználói*: Megadhatja a felhasználónevet és jelszót közvetlenül belül a `az login` parancsot, vagy hitelesíteni tudja a webböngésző használatával. Az utóbbi kell tennie, ha a fiók rendelkezik-e többtényezős hitelesítés engedélyezve:

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > Ha a felhasználói fiók rendelkezik a többtényezős hitelesítés engedélyezve van, használhatja a `az login command` anélkül, hogy a `-u` paraméter. A következő parancs futtatásával lehetővé teszi egy URL-CÍMÉT és a egy kódot, amely hitelesítést kell használnia.
   
      * Jelentkezzen be egy *szolgáltatásnév*: Amikor bejelentkezik, [hozzon létre egy egyszerű szolgáltatást az Azure Portalon keresztül](azure-stack-create-service-principals.md) vagy a parancssori felület, és rendelje hozzá egy szerepkörhöz. Most jelentkezzen be a következő paranccsal:

      ```azurecli  
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```
    * Az AD FS-környezetek

        * Jelentkezzen be a webböngészővel rendelkező eszköz felhasználóként:  
           ```azurecli  
           az login --use-device-code
           ```

           > [!NOTE]  
           >A következő parancs futtatásával lehetővé teszi egy URL-CÍMÉT és a egy kódot, amely hitelesítést kell használnia.

        * Jelentkezzen be egy egyszerű szolgáltatást:
        
          1. Készítse elő a .pem-fájlt a szolgáltatás egyszerű bejelentkezéshez használandó.

            * Az ügyfélszámítógépen, ahol a rendszerbiztonsági tag lett létrehozva, exportálja a szolgáltatásnév helyén található a pfx titkos kulccsal `cert:\CurrentUser\My`; a tanúsítvány neve rendelkezik az egyszerű azonos néven.
        
            * A pfx konvertálható pem (az OpenSSL segédprogramot használja).

          2.  Jelentkezzen be a parancssori felület:
            ```azurecli  
            az login --service-principal \
              -u <Client ID from the Service Principal details> \
              -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
              --tenant <Tenant ID> \
              --debug 
            ```

## <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Mindent, a belül az Azure Stack-erőforrások létrehozása a CLI használatával. Például hozzon létre egy erőforráscsoportot egy alkalmazáshoz, és adjon hozzá egy virtuális gépet. A következő paranccsal hozzon létre egy erőforráscsoportot "MyResourceGroup" nevű:

```azurecli
az group create \
  -n MyResourceGroup -l local
```

Ha az erőforráscsoport létrehozása sikeresen megtörtént, az előző parancs megjeleníti az újonnan létrehozott erőforrás következő tulajdonságai:

![Erőforráscsoport létrehozása a kimenet](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Ismert problémák

Vannak ismert problémák az Azure Stack parancssori felület használata esetén:

 - A CLI interaktív módban Ha például a `az interactive` paranccsal, az Azure Stackben még nem támogatott.
 - Az Azure Stackben elérhető virtuálisgép-rendszerképek listájának lekéréséhez használja a `az vm image list --all` parancs helyett a `az vm image list` parancsot. Adja meg a `--all` beállítással biztosíthatja, hogy a választ adja vissza az érhetők el az Azure Stack környezettel lemezképeket.
 - Az Azure-ban rendelkezésre álló virtuális gép rendszerkép aliasok nem vonatkoznak az Azure Stackhez. Virtuálisgép-lemezképek használata esetén a teljes URN paramétert kell használnia (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) a rendszerkép alias helyett. Ez URN egyeznie kell a lemezkép előírásoknak, származtatva a `az vm images list` parancsot.

## <a name="next-steps"></a>További lépések

- [Az Azure CLI-vel sablonok üzembe helyezése](azure-stack-deploy-template-command-line.md)
- [Azure CLI-vel engedélyezése az Azure Stack felhasználói (operátor)](../azure-stack-cli-admin.md)
- [Felhasználói engedélyek kezelése](azure-stack-manage-permissions.md) 