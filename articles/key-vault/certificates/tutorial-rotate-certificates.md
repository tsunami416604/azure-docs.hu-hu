---
title: Oktatóanyag – a tanúsítvány automatikus rotációs gyakoriságának frissítése Key Vaultban | Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan lehet frissíteni a tanúsítvány automatikus rotációs gyakoriságát Azure Key Vault a Azure Portal használatával
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 2e6c250a0bcb9d73e7c572dfe8138c31269993e8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82106803"
---
# <a name="tutorial-configuring-certificates-auto-rotation-in-key-vault"></a>Oktatóanyag: a tanúsítvány automatikus rotációjának konfigurálása Key Vaultban

A Azure Key Vault lehetővé teszi digitális tanúsítványok kiépítését, kezelését és üzembe helyezését. Lehetnek nyilvános és magánhálózati SSL/TLS-tanúsítványok, amelyek a hitelesítésszolgáltató vagy egy önaláírt tanúsítvány alá tartoznak. A Key Vault a tanúsítványokkal kapcsolatos partneri kapcsolatokon keresztül is kérheti és megújíthatja a tanúsítványokat, így robusztus megoldást biztosít a tanúsítványok életciklusának kezelésére. Ebben az oktatóanyagban frissíteni fogja a tanúsítvány attribútumainak érvényességi időtartamát, az automatikus elforgatás gyakoriságát, a CA-t. A Key Vaulttal kapcsolatosan további információt az [Áttekintés](../general/overview.md) szakaszban talál.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Tanúsítvány kezelése Azure Portal használatával
> * Hitelesítésszolgáltató-szolgáltató fiókjának hozzáadása
> * Tanúsítvány érvényességi idejének frissítése
> * A tanúsítvány automatikus elforgatási gyakoriságának frissítése
> * Tanúsítvány attribútumainak frissítése az Azure PowerShell-lel


Mielőtt elkezdené, olvassa el [Key Vault alapvető fogalmakat](../general/basic-concepts.md). 

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-vault"></a>Tároló létrehozása

Hozzon létre vagy válasszon ki egy meglévő Key Vault a műveletek végrehajtásához. [(A Key Vault létrehozásának lépései).](../quick-create-portal.md) A példában a tár neve **például a-Vault**. 

![Kimenet a Key Vault létrehozási parancsának befejeződése után](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Tanúsítvány létrehozása Key Vaultban

Tanúsítvány létrehozása vagy importálása a tárolóban. [(A tanúsítvány létrehozásához szükséges lépések a Key vaultban).](../quick-create-portal.md) Ebben az esetben a **ExampleCertificate**nevű tanúsítványon dolgozunk.

> [!NOTE]
> Azure Key Vault a tanúsítvány életciklus-attribútumai a tanúsítvány létrehozásakor és a létrehozásuk után is frissíthetők. 
## <a name="updating-certificates-life-cycle-attributes"></a>A tanúsítvány életciklus-attribútumainak frissítése

A Key Vaultban létrehozott tanúsítvány lehet 
- önaláírt tanúsítvány
- hitelesítésszolgáltatóval (CA) létrehozott, Key Vault-mel rendelkező tanúsítvány
- olyan hitelesítésszolgáltató, amely nem Key Vault partnerrel

A következő hitelesítésszolgáltatók jelenleg a Key Vaultkal rendelkező partneri szolgáltatók:
- A DigiCert-Key Vault OV TLS/SSL-tanúsítványokat kínál a DigiCert.
- A GlobalSign-Key Vault OV TLS/SSL-tanúsítványokat kínál a GlobalSign.

Azure Key Vault automatikusan elforgatja a tanúsítványokat a hitelesítésszolgáltatókkal létesített partnerekkel. A létrehozott partneri kapcsolaton keresztül Key Vault automatikusan kéri és megújítja a tanúsítványokat. Ezért az **automatikus elforgatási képesség nem alkalmazható olyan hitelesítésszolgáltatókkal létrehozott tanúsítványokra, amelyek nem a Key Vault-mel vannak** társítva. 

> [!NOTE]
> A HITELESÍTÉSSZOLGÁLTATÓI szolgáltatói fiók rendszergazdája olyan hitelesítő adatokat hoz létre, amelyeket a Key Vault a TLS/SSL-tanúsítványok Key Vault használatával történő létrehozásához, megújításához és használatához használ.
![Hitelesítésszolgáltató](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
> 


### <a name="updating-certificates-life-cycle-attributes-at-the-time-of-certificate-creation"></a>A tanúsítvány életciklus-attribútumainak frissítése a tanúsítvány létrehozásakor

1. A Key Vault tulajdonságok lapon válassza a **tanúsítványok**lehetőséget.
2. Kattintson a **Létrehozás/Importálás** gombra.
3. A **tanúsítvány létrehozása** képernyőn frissítse a következő értékeket:
    

    - **Érvényességi időszak**: adja meg az értéket (hónapokban). A rövid életű tanúsítványok létrehozása ajánlott biztonsági eljárás. Egy újonnan létrehozott tanúsítvány alapértelmezett érvényességi értéke 12 hónap.
    - **Élettartam művelet típusa**: válassza a tanúsítvány automatikus megújítása és a riasztási művelet lehetőséget. A kiválasztást követően frissítse a százalékos élettartamot, vagy a lejárat előtti napok számát. Alapértelmezés szerint a tanúsítvány automatikus megújítása az élettartamának 80%-ában van beállítva.<br> A legördülő menüben válassza a következő lehetőséget:

    |  Automatikus megújítás egy adott időpontban| Minden Névjegy küldése egy adott időpontban |
    |-----------|------|
    |Ha ezt a lehetőséget választja, bekapcsolja az autorotációt | Ha ezt a beállítást választja, a rendszer nem automatikusan elforgatja a névjegyeket.|
        


4. Kattintson a **Létrehozás** lehetőségre.

![Tanúsítvány életciklusa](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="updating-life-cycle-attributes-of-stored-certificate"></a>Tárolt tanúsítvány életciklus-attribútumainak frissítése

1. Válassza ki a Key Vault.
2. A Key Vault tulajdonságok lapon válassza a **tanúsítványok**lehetőséget.
3. Válassza ki a frissíteni kívánt tanúsítványt. Ebben az esetben a **ExampleCertificate**nevű tanúsítványon fogunk dolgozni.
4. Válassza ki a **kiállítási szabályzat** elemet a felső menüsorban.

![Tanúsítvány tulajdonságai](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)
5. A **kiállítási szabályzat** képernyőn frissítse a következő értékeket:
- **Érvényességi időszak**: az érték frissítése (hónap)
- **Élettartam művelet típusa**: válassza a tanúsítvány automatikus megújítása és a riasztási művelet lehetőséget. A kiválasztást követően frissítse a "százalék élettartama" vagy a "lejárat előtti napok száma" értéket. 

![Tanúsítvány tulajdonságai](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)
6. Kattintson a **Save (Mentés**) gombra.

> [!IMPORTANT]
> Ha módosítja egy tanúsítvány élettartamának típusát, a rendszer azonnal rögzíti a meglévő tanúsítványok módosításait.


### <a name="updating-certificates-attributes-using-powershell"></a>Tanúsítvány attribútumainak frissítése a PowerShell használatával

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Ha módosítani szeretné a megújítási szabályzatot a tanúsítványok listájának módosításához, a VaultName, CertName tartalmazó input file. csv fájlt. <br/>
>  vault1,Cert1 <br/>
>  vault2, Cert2
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
További információ [a paraméterekről](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erre a rövid útmutatóra egyéb Key Vault-útmutatók és oktatóanyagok is épülnek. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat is. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban frissítette a tanúsítvány életciklusát. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

További információ a [tanúsítvány-létrehozás kezeléséről Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)
- Tekintse át a [Key Vault áttekintését](../general/overview.md)