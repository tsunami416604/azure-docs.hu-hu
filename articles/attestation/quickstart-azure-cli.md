---
title: Az Azure-igazolás beállítása az Azure CLI-vel
description: Igazolási szolgáltató beállítása és konfigurálása az Azure CLI használatával.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: dee9e7596c0a30301d9e0453ef22a6dfe9541522
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2020
ms.locfileid: "96020942"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Gyors útmutató: Azure-igazolás beállítása az Azure CLI-vel

Az Azure-igazolások használatának első lépései az Azure CLI-vel az igazolás beállításához.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="get-started"></a>Bevezetés

1. Az Azure-ba való bejelentkezéshez használja a következő parancsot:

   ```azurecli
   az login
   ```

1. Ha szükséges, váltson az Azure-tanúsítványra vonatkozó előfizetésre:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Regisztrálja a Microsoft. tanúsító erőforrás-szolgáltatót az előfizetésben az az [Provider Register](/cli/azure/provider#az_provider_register) paranccsal:

   ```azurecli
   az provider register --name Microsoft.Attestation
   ```

   Az Azure erőforrás-szolgáltatókkal és azok konfigurálásával és kezelésével kapcsolatos további információkért lásd: [Azure Resource Providers és types](../azure-resource-manager/management/resource-providers-and-types.md).

   > [!NOTE]
   > Csak egyszer kell regisztrálnia egy erőforrás-szolgáltatót az előfizetéshez.

1. Hozzon létre egy erőforráscsoportot az igazolási szolgáltatóhoz. A többi Azure-erőforrást ugyanabban az erőforráscsoporthoz helyezheti el, beleértve az ügyfélalkalmazás-példánnyal rendelkező virtuális gépeket is. Futtassa az az [Group Create](/cli/azure/group#az_group_create) parancsot egy erőforráscsoport létrehozásához, vagy használjon egy meglévő erőforráscsoportot:

   ```azurecli
   az group create --name attestationrg --location uksouth
   ```

## <a name="create-and-manage-an-attestation-provider"></a>Igazolási szolgáltató létrehozása és kezelése

Az alábbi parancsok segítségével hozhatja létre és kezelheti az igazolási szolgáltatót:

1. Futtassa az az [igazolás létrehozása](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) parancsot az igazolási szolgáltató létrehozásához:

   ```azurecli
   az attestation create --resource-group attestationrg --name attestationProvider --location uksouth \
      --attestation-policy SgxDisableDebugMode --certs-input-path C:\test\policySignersCertificates.pem
   ```

   A **--certs-input-Path** paraméter a megbízható aláíró kulcsok készletét határozza meg. Ha a paraméterhez fájlnevet ad meg, az igazolási szolgáltatót csak aláírt JWT formátumban konfigurált szabályzatokkal kell konfigurálni. Ellenkező esetben a házirend szövegben vagy aláíratlan JWT formátumban is konfigurálható. További információ a JWT: [alapvető fogalmak](basic-concepts.md). A tanúsítványok mintáit lásd: [igazolási házirend aláíró tanúsítványának példái](policy-signer-examples.md).

1. Futtassa az az [igazolás megjelenítése](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) parancsot az igazolási szolgáltató tulajdonságainak, például az állapot-és AttestURI lekéréséhez:

   ```azurecli
   az attestation show --resource-group attestationrg --name attestationProvider
   ```

   Ez a parancs az alábbi kimenethez hasonló értékeket jelenít meg:

   ```output
   Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
   Location: MyLocation
   ResourceGroupName: MyResourceGroup
   Name: MyAttestationProvider
   Status: Ready
   TrustModel: AAD
   AttestUri: https://MyAttestationProvider.us.attest.azure.net
   Tags:
   TagsTable:
   ```

A tanúsítvány-szolgáltató törléséhez használja az az [igazolás törlése](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_delete) parancsot:

```azurecli
az attestation delete --resource-group attestationrg --name attestationProvider
```

## <a name="policy-management"></a>Szabályzatkezelés

A házirendek kezeléséhez az Azure AD-felhasználónak a következő engedélyekkel kell rendelkeznie a számára `Actions` :

- `Microsoft.Attestation/attestationProviders/attestation/read`
- `Microsoft.Attestation/attestationProviders/attestation/write`
- `Microsoft.Attestation/attestationProviders/attestation/delete`

Ezek az engedélyek egy Azure AD-felhasználóhoz rendelhetők hozzá egy szerepkör (például `Owner` helyettesítő engedélyek), `Contributor` (helyettesítő engedélyek) vagy `Attestation Contributor` (csak az Azure-igazolásra vonatkozó konkrét engedélyek) használatával.  

A szabályzatok olvasásához az Azure AD-felhasználónak a következő engedélyre van szüksége a következőkhöz `Actions` :

- `Microsoft.Attestation/attestationProviders/attestation/read`

Ez az engedély hozzárendelhető egy Azure AD-felhasználóhoz egy szerepkör (például `Reader` helyettesítő engedélyek) vagy `Attestation Reader` (csak az Azure-hitelesítésre vonatkozó konkrét engedélyek) használatával.

Az itt ismertetett parancsokkal az igazolási szolgáltatóhoz tartozó házirendek kezeléséhez egyszerre egy PÓLÓt kell használni.

Az az [igazolási házirend megjelenítése](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) parancs a megadott Tee aktuális szabályzatát adja vissza:

```azurecli
az attestation policy show --resource-group attestationrg --name attestationProvider --tee SgxEnclave
```

> [!NOTE]
> A parancs szöveg-és JWT formátumban jeleníti meg a szabályzatot.

A következők a támogatott TEE-típusok:

- `CyResComponent`
- `OpenEnclave`
- `SgxEnclave`
- `VSMEnclave`

Az az [igazolási házirend beállítása](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) paranccsal beállíthat egy új házirendet a megadott Tee-hoz.

```azurecli
az attestation policy set --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --new-attestation-policy newAttestationPolicyname
```

A JWT formátumú igazolási szabályzatnak tartalmaznia kell egy nevű jogcímet `AttestationPolicy` . Az aláírt szabályzatot olyan kulccsal kell aláírni, amely megfelel a meglévő házirend-aláíró tanúsítványoknak.

A házirend-minták esetében tekintse meg az [igazolási szabályzat példáit](policy-examples.md).

Az az [igazolási házirend alaphelyzetbe állítása](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_reset) parancs új szabályzatot állít be a megadott pólóra vonatkozóan.

```azurecli
az attestation policy reset --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --policy-jws "eyJhbGciOiJub25lIn0.."
```

## <a name="policy-signer-certificates-management"></a>Házirend-aláíró tanúsítványok kezelése

Az alábbi parancsokkal kezelheti az igazolási szolgáltatóhoz tartozó házirend-aláíró tanúsítványokat:

```azurecli
az attestation signer list --resource-group attestationrg --name attestationProvider

az attestation signer add --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."

az attestation signer remove --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."
```

A házirend-aláíró tanúsítvány egy nevű aláírt JWT `maa-policyCertificate` . A jogcím értéke egy JWK, amely a hozzáadandó megbízható aláírási kulcsot tartalmazza. A JWT olyan titkos kulccsal kell aláírnia, amely megfelel a meglévő házirend-aláíró tanúsítványoknak. A JWT és a JWK kapcsolatos további információkért lásd: [alapfogalmak](basic-concepts.md).

A házirend-aláíró tanúsítvány szemantikai manipulációját az Azure CLI-n kívül kell elvégezni. Az Azure CLI-vel kapcsolatban ez egy egyszerű karakterlánc.

A tanúsítványok mintáit lásd: [igazolási házirend aláíró tanúsítványának példái](policy-signer-examples.md).

## <a name="next-steps"></a>További lépések

- [Igazolási szabályzat létrehozása és aláírása](author-sign-policy.md)
- [Az igazolás megvalósítása SGX ENKLÁVÉHOZ enklávéban, kód minták használatával](/samples/browse/?expanded=azure&terms=attestation)
