---
title: Oktatóanyag – Tanúsítvány importálása a Key Vaultban az Azure Portal használatával | Microsoft dokumentumok
description: Oktatóanyag, amely bemutatja, hogyan importálhat tanúsítványt az Azure Key Vaultban
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/03/2020
ms.author: sebansal
ms.openlocfilehash: 754f30f7931f9fad6a95328cbf8ab34f70cb75a0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423109"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>Oktatóanyag: Tanúsítvány importálása az Azure Key Vaultban

Az Azure Key Vault egy olyan felhőszolgáltatás, amely a titkos kulcsok biztonságos tárolására szolgál. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebben az oktatóanyagban hozzon létre egy key vault, majd használja a tanúsítvány importálásához. A Key Vaulttal kapcsolatosan további információt az [Áttekintés](../general/overview.md) szakaszban talál.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Kulcstartó létrehozása.
> * Importáljon egy tanúsítványt a kulcstárolóban a portál használatával.
> * Importáljon egy tanúsítványt a Kulcstárolóban a CLI használatával.


Mielőtt elkezdené, olvassa el [a Key Vault alapfogalmait.](../general/basic-concepts.md) 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-vault"></a>Tároló létrehozása

1. Az Azure Portal menüjében vagy a **kezdőlapon** válassza az **Erőforrás létrehozása lehetőséget.**
2. A keresőmezőbe írja be a **Key Vault** kifejezést.
3. Az eredmények listájában válassza a **Key Vault** lehetőséget.
4. A Key Vault szakaszban kattintson a **Létrehozás** gombra.
5. A **Kulcstartó létrehozása** szakaszban adja meg a következő információkat:
    - **Név**: Egy egyedi nevet kell megadnia. Ehhez a rövid útmutatóhoz **az Example-Vault**használatát használjuk. 
    - **Előfizetés**: Válassza ki az előfizetést.
    - Az **Erőforráscsoport**csoportban válassza **az Új létrehozása lehetőséget,** és adjon meg egy erőforráscsoport nevét.
    - A **Hely** legördülő menüből válassza ki a helyet.
    - A többi beállítást hagyja az alapértelmezett értéken.
6. A fenti adatok megadása után válassza a **Létrehozás** elemet.

Jegyezze fel az alábbi két tulajdonságot:

* **Vault neve:** A példában ez a **Példa-Vault**. Ezt a nevet fogja majd más lépésekben is használni.
* **Tár URI-ja**: A példában ez a https://example-vault.vault.azure.net/. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Jelenleg csak az Azure-fiókja jogosult arra, hogy műveleteket végezzen ezen az új kulcstartón.

![Kimenet a Key Vault létrehozási parancsának befejeződése után](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>Tanúsítvány importálása a Key Vaultba

Ahhoz, hogy egy tanúsítványt importáljon a tárolóba, rendelkeznie kell egy PEM- vagy PFX-tanúsítványfájllal ahhoz, hogy a lemezen legyen. Ebben az esetben egy **ExampleCertificate**nevű fájlnevű tanúsítványt importálunk.

> [!IMPORTANT]
> Az Azure Key Vault ban a támogatott tanúsítványformátumok a PFX és a PEM. 
> - A .pem fájlformátum egy vagy több X509 tanúsítványfájlt tartalmaz.
> - A .pfx fájlformátum egy archív fájlformátum, amely több kriptográfiai objektumot tárol egyetlen fájlban, azaz kiszolgálói tanúsítványt (a tartományához), egy megfelelő személyes kulcsot, és opcionálisan köztes hitelesítésszolgáltatót is tartalmazhat.  

1. A Key Vault tulajdonságlapjain válassza a **Tanúsítványok**lehetőséget.
2. Kattintson a **Létrehozás/Importálás** gombra.
3. A **Tanúsítvány létrehozása** képernyőn válassza a következő értékeket:
    - **A tanúsítvány létrehozásának módja**: Import.
    - **Tanúsítvány neve**: ExampleCertificate.
    - **Tanúsítványfájl feltöltése**: a tanúsítványfájl kiválasztása lemezről
    - A többi értéket hagyja az alapértelmezett értéken. Kattintson **a Létrehozás gombra.**

![Tanúsítvány tulajdonságai](../media/certificates/tutorial-import-cert/cert-import.png)

Miután megkapta az üzenetet, hogy a tanúsítvány importálása sikeresen megtörtént, rákattinthat a listára. Ezután láthatja néhány tulajdonságát. 

![Tanúsítvány tulajdonságai](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>Tanúsítvány importálása az Azure CLI használatával

Importáljon egy tanúsítványt egy megadott kulcstartóba. Egy meglévő érvényes tanúsítvány, amely egy személyes kulcsot tartalmazó, az Azure Key Vault, az importálandó fájl lehet PFX vagy PEM formátumban. Ha a tanúsítvány PEM formátumban van, a PEM fájlnak tartalmaznia kell a kulcsot és az x509 tanúsítványokat is. Ehhez a művelethez tanúsítványok/importálási engedély szükséges.

```azurecli
az keyvault certificate import --file
                               --name
                               --vault-name
                               [--disabled {false, true}]
                               [--only-show-errors]
                               [--password]
                               [--policy]
                               [--subscription]
                               [--tags]
```
Tudjon meg többet a [paraméterekről itt](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erre a rövid útmutatóra egyéb Key Vault-útmutatók és oktatóanyagok is épülnek. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat is. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy Key Vaultot, és importált benne egy tanúsítványt. Ha többet szeretne megtudni a Key Vaultról és arról, hogyan integrálhatja azt az alkalmazásokkal, folytassa az alábbi cikkekkel.

- További információ [a tanúsítványok kezeléséről az Azure Key Vaultban](/archive/blogs/kv/manage-certificates-via-azure-key-vault)
- Példák [a tanúsítványok REST API-k használatával történő importálására](/rest/api/keyvault/importcertificate/importcertificate)
- Tekintse át az [Azure Key Vault gyakorlati tanácsait](../general/best-practices.md)