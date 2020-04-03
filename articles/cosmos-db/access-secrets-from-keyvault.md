---
title: Az Azure Cosmos DB-kulcsok tárolása és elérése a Key Vault használatával
description: Az Azure Key Vault használatával tárolhatja és elérheti az Azure Cosmos DB kapcsolati karakterláncot, kulcsokat és végpontokat.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 225221635f978e3d70cec4ce7e9d78d6b100b4fd
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618760"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Azure Cosmos-kulcsok védelme az Azure Key Vaulttal 

>[!IMPORTANT]
> Az Azure Cosmos [DB-kulcsok](managed-identity-based-authentication.md)elérésének ajánlott megoldása a rendszer által hozzárendelt felügyelt identitás használata. Ha a szolgáltatás nem tudja kihasználni a felügyelt identitások előnyeit, használja a [tanúsítvány alapú megoldást.](certificate-based-authentication.md) Ha a felügyelt identitásmegoldás és a tanúsítványalapú megoldás nem felel meg az igényeinek, használja az alábbi key vault megoldást.

Az Azure Cosmos DB alkalmazások használatakor hozzáférhet az adatbázishoz, gyűjtemények, dokumentumok segítségével a végpont és a kulcs az alkalmazás konfigurációs fájljában.  Azonban nem biztonságos a kulcsokat és az URL-t közvetlenül az alkalmazáskódjába helyezni, mert azok tiszta szöveges formátumban érhetők el az összes felhasználó számára. Azt szeretné, hogy a végpont és a kulcsok biztonságos mechanizmuson keresztül legyenek elérhetők. Az Azure Key Vault segít a titkos alkalmazáskulcsok biztonságos kezelésében és tárolásában.

Az Azure Cosmos DB hozzáférési kulcsok key vaultból történő tárolásához és olvasásához a következő lépések szükségesek:

* Kulcstartó létrehozása  
* Azure Cosmos DB hozzáférési kulcsok hozzáadása a Key Vaulthoz  
* Azure-webalkalmazás létrehozása  
* Regisztrálja az alkalmazást, & engedélyeket adjon a Key Vault olvasásához  


## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).  
2. Válassza **az Erőforrás létrehozása > biztonság > a Key Vault lehetőséget.**  
3. A **Kulcstartó létrehozása** szakaszban adja meg a következő információkat:  
   * **Név:** Adjon meg egyedi nevet a Key Vaultnak.  
   * **Előfizetés:** Válassza ki a használni kívánt előfizetést.  
   * Az **Erőforráscsoport** területen válassza az **Új létrehozása** lehetőséget, és írja be az erőforráscsoport nevét.  
   * A Hely legördülő menüből válassza ki a helyet.  
   * Hagyja a többi beállítást az alapértelmezett beállításokra.  
4. A fenti adatok megadása után válassza a **Létrehozás** elemet.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Adja hozzá az Azure Cosmos DB hozzáférési kulcsokat a Key Vaulthoz.
1. Nyissa meg az előző lépésben létrehozott Key Vaultot, nyissa meg a **Titkoskulcsok** lapot.  
2. Válassza a **+Létrehozás/importálás**lehetőséget, 

   * Válassza a **Manuális** **feltöltési beállítások lehetőséget.**
   * Adja meg a titkos **adatnevét**
   * Adja meg a Cosmos DB-fiók kapcsolati karakterláncát az **Érték** mezőben. Ezután válassza **a Create (Létrehozás) lehetőséget.**

   ![Titkos kulcs létrehozása](./media/access-secrets-from-keyvault/create-a-secret.png)

4. A titkos titok létrehozása után nyissa meg, és másolja a következő formátumú **titkos azonosítót. Ezt az azonosítót a következő szakaszban fogja használni. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Azure-webalkalmazás létrehozása

1. Hozzon létre egy Azure-webalkalmazást, vagy töltse le az alkalmazást a [GitHub-tárházból.](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo) Ez egy egyszerű MVC alkalmazás.  

2. Csomagolja ki a letöltött alkalmazást, és nyissa meg a **HomeController.cs** fájlt. Frissítse a titkos azonosítót a következő sorban:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Mentse** a fájlt, **készítse el** a megoldást.  
4. Ezután telepítse az alkalmazást az Azure-ba. Kattintson a jobb gombbal a projektre, és válassza **a közzététel**lehetőséget. Hozzon létre egy új alkalmazásszolgáltatás-profilt (elnevezheti az alkalmazást WebAppKeyVault1 néven), és válassza a **Közzététel**lehetőséget.   

5. Az alkalmazás üzembe helyezése után. Az Azure Portalon keresse meg a telepített webalkalmazást, és kapcsolja be az alkalmazás **felügyelt szolgáltatásidentitását.**  

   ![Felügyelt szolgáltatás identitása](./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png)

Ha most futtatja az alkalmazást, a következő hibaüzenet jelenik meg, mivel nem adott engedélyt az alkalmazásnak a Key Vaultban.

![Hozzáférés nélkül telepített alkalmazás](./media/access-secrets-from-keyvault/app-deployed-without-access.png)

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Regisztrálja az alkalmazást, & engedélyeket adjon a Key Vault olvasásához

Ebben a szakaszban regisztrálja az alkalmazást az Azure Active Directoryval, és engedélyeket ad az alkalmazásnak a Key Vault olvasásához. 

1. Keresse meg az Azure Portalon, nyissa meg az előző szakaszban létrehozott **Key Vault.**  

2. **Nyissa meg a Hozzáférési házirendeket,** válassza az **+Új hozzáadása** a telepített webalkalmazás megkeresése lehetőséget, válassza az engedélyeket, és válassza az **OK**gombot.  

   ![Hozzáférési házirend hozzáadása](./media/access-secrets-from-keyvault/add-access-policy.png)

Most, ha futtatja az alkalmazást, elolvashatja a titkos kulcsot a Key Vaultból.

![Titkos alkalmazás telepítve](./media/access-secrets-from-keyvault/app-deployed-with-access.png)
 
Hasonlóképpen hozzáadhat egy felhasználót a key vault eléréséhez. Hozzá kell adnia magát a Key Vaulthoz a **Hozzáférési házirendek** kiválasztásával, majd adja meg az alkalmazás Visual Studio-ból való futtatásához szükséges összes engedélyt. Amikor ez az alkalmazás fut az asztalról, akkor veszi a személyazonosságát.

## <a name="next-steps"></a>További lépések

* Az Azure Cosmos DB tűzfalának konfigurálásához tekintse meg a [tűzfal támogatási](firewall-support.md) cikkét.
* A virtuális hálózati szolgáltatás végpontjának konfigurálása, lásd: [biztonságos hozzáférés a VNet szolgáltatás végponti](vnet-service-endpoint.md) cikk használatával.
