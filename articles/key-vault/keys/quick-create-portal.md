---
title: Azure-gyorsútmutató – Kulcs beállítása és beolvasása a Key Vaultból az Azure Portal használatával | Microsoft dokumentumok
description: Rövid útmutató, amely bemutatja, hogyan állíthat be és kérhet be egy kulcsot az Azure Key Vaultból az Azure Portalon
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2020
ms.author: mbaldwin
ms.openlocfilehash: 41f3d60d91b7418d6e9733b8351d4830b31dbace
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424194"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-the-azure-portal"></a>Rövid útmutató: Kulcs beállítása és lekérése az Azure Key Vaultból az Azure Portalon

Az Azure Key Vault egy olyan felhőszolgáltatás, amely a titkos kulcsok biztonságos tárolására szolgál. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebben a rövid útmutatóban hozzon létre egy key vault, majd használja a kulcs tárolására. A Key Vaulttal kapcsolatosan további információt az [Áttekintés](../general/overview.md) szakaszban talál.

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

![Kimenet a Key Vault létrehozási parancsának befejeződése után](../media/keys/quick-create-portal/vault-properties.png)

## <a name="add-a-key-to-key-vault"></a>Kulcs hozzáadása a Key Vaulthoz

Kulcs hozzáadása a tárolóhoz, csak néhány további lépést kell tennie. Ebben az esetben hozzáadunk egy kulcsot, amelyet egy alkalmazás használhat. A kulcs neve **ExampleKey**.

1. A Key Vault tulajdonságlapjain válassza a **Kulcsok**lehetőséget.
2. Kattintson a **Létrehozás/Importálás** gombra.
3. A **Kulcs létrehozása** képernyőn válassza a következő értékeket:
    - **Beállítások**: Létrehozás.
    - **Név**: ExampleKey.
    - A többi értéket hagyja az alapértelmezett értéken. Kattintson **a Létrehozás gombra.**

Miután megkapta az üzenetet, hogy a kulcs sikeresen létrejött, akkor kattintson rá a listán. Ezután megjelenik néhány tulajdonság. Ha a jelenlegi verzióra kattint, láthatja az előző lépésben megadott értéket.

![Fő tulajdonságok](../media/keys/quick-create-portal/current-version-hidden.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erre a rövid útmutatóra egyéb Key Vault-útmutatók és oktatóanyagok is épülnek. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat is. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.


## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault ot, és egy kulcsot tárolt benne. Ha többet szeretne megtudni a Key Vaultról és arról, hogyan integrálhatja azt az alkalmazásokkal, folytassa az alábbi cikkekkel.

- Az [Azure Key Vault áttekintésének elolvasása](../general/overview.md)
- Tekintse meg az [Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- Tekintse át az [Azure Key Vault gyakorlati tanácsait](../general/best-practices.md)