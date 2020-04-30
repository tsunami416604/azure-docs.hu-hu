---
title: 'Gyors útmutató: tanúsítvány beállítása és lekérése Azure Key Vault'
description: Gyors útmutató, amely bemutatja, hogyan kell beállítani és beolvasni a tanúsítványokat a Azure Key Vault használatával Azure PowerShell
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: ecb9262a96d1c9857283de00224950d9bc7a583f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424712"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-powershell"></a>Gyors útmutató: tanúsítvány beállítása és lekérése Azure Key Vault használatával Azure PowerShell

Ebben a rövid útmutatóban egy kulcstartót hoz létre Azure Key Vaultban Azure PowerShell. Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. További információ a Key Vaultről: [Áttekintés](../general/overview.md). Azure PowerShell az Azure-erőforrások parancsok vagy parancsfájlok használatával történő létrehozására és kezelésére szolgál. Ha ezt elvégezte, egy tanúsítványt fog tárolni.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz a Azure PowerShell-modul 1.0.0-as vagy újabb verziójára lesz szükség. A `$PSVersionTable.PSVersion` verzió megkereséséhez írja be a következőt:. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Következő lépésként létrehoz egy kulcstartót. Ehhez a lépéshez tudnia kell néhány dolgot:

Bár a jelen rövid útmutatóban a "contoso KeyVault2" nevet használjuk a Key Vaultnak, egyedi nevet kell használnia.

- **Tároló neve**: Contoso-Vault2.
- **Erőforráscsoport neve**: ContosoResourceGroup.
- **Tartózkodási hely** USA keleti régiója.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

Ezzel a parancsmaggal megjelenítheti az újonnan létrehozott kulcstartó tulajdonságait. Jegyezze fel az alábbi két tulajdonságot:

* **Tároló neve**: A példában ez **Contoso-Vault2**. Ezt a nevet fogja majd más Key Vault parancsmagokban is megadni.
* **A tároló URI-ja**: Ebben a példában ez https://Contoso-Vault2.vault.azure.net/. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

A tároló létrehozása után az Azure-fiókja az egyetlen fiók, amely bármit végrehajthat az új tárolón.

![Kimenet a Key Vault létrehozási parancsának a befejeződése után](../media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="add-a-certificate-to-key-vault"></a>Tanúsítvány hozzáadása a Key Vaulthoz

Ha tanúsítványt szeretne hozzáadni a tárolóhoz, mindössze néhány további lépést kell elvégeznie. Ezt a tanúsítványt egy alkalmazás használhatja. 

Írja be az alábbi parancsokat egy önaláírt tanúsítvány létrehozásához a **ExampleCertificate** nevű szabályzattal:

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal
Add-AzKeyVaultCertificate -VaultName "Contoso-Vault2" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Most már hivatkozhat erre a tanúsítványra, amelyet a Azure Key Vaulthoz adott hozzá az URI használatával. Az **https://Contoso-Vault2.vault.azure.net/certificates/ExampleCertificate** aktuális verzió beszerzéséhez használja a következőt:. 

A korábban tárolt tanúsítvány megtekintése:

```azurepowershell-interactive
Get-AzKeyVaultCertificate -VaultName "Contoso-Vault2" -Name "ExampleCertificate"
```

Most létrehozott egy Key Vault, tárolt egy tanúsítványt, és beolvasta azt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal távolíthatja el az erőforráscsoportot és az összes kapcsolódó erőforrást. Az erőforrásokat a következőképpen törölheti:

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, és tárolt benne egy tanúsítványt. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](../general/overview.md)
- Tekintse meg a [Azure PowerShell Key Vault-parancsmagok](/powershell/module/az.keyvault/) referenciáját
- [Azure Key Vault ajánlott eljárások](../general/best-practices.md) áttekintése
