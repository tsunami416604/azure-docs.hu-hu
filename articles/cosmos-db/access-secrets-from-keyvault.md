---
title: A Key Vault használatával tárolhatja és érheti el az Azure Cosmos DB-kulcsok
description: Az Azure Key Vault használatával tárolhatja és érheti el az Azure Cosmos DB kapcsolati sztring, a kulcsokat, a végpontok.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 36b0a2f18cf2917251a87405456980811af1bc3d
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242192"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Biztonságos Azure Key Vault használatával az Azure Cosmos-kulcsok 

Amikor az alkalmazások Azure Cosmos DB használatával, az adatbázis, a gyűjtemények, dokumentumok hozzáférhet az alkalmazás konfigurációs fájljában a végpont és a kulcs használatával.  Azonban akkor sem biztonságos kulcsokat és URL-címet közvetlenül a helyezi az alkalmazáskód, mert a tiszta szöveges formátumban mindenki számára elérhetők. Győződjön meg arról, hogy a végpont és a kulcsok nem érhető el, de biztonságos mechanizmus segítségével szeretné. Ez az, ahol az Azure Key Vault segítségével biztonságosan tárolhatja, és a titkos alkalmazáskulcsok kezelése.

A következő lépések szükségesek, tárolására, és olvassa el az Azure Cosmos DB tárelérési kulcsok a Key Vaultból:

* Kulcstartó létrehozása  
* Azure Cosmos DB hozzáférési kulcs hozzáadása a Key Vault  
* Azure-alapú webes alkalmazás létrehozása  
* Regisztrálnia kell az alkalmazást, és olvassa el a Key Vault szükséges engedélyek biztosítása  


## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

1. Jelentkezzen be a [az Azure Portal](https://portal.azure.com/).  
2. Válassza ki **erőforrás létrehozása > Biztonság > a Key Vault**.  
3. A **Kulcstartó létrehozása** szakaszban adja meg a következő információkat:  
   * **név:** Adjon meg egy egyedi nevet a Key Vaulthoz.  
   * **Előfizetés:** Válassza ki a használni kívánt előfizetést.  
   * Az **Erőforráscsoport** területen válassza az **Új létrehozása** lehetőséget, és írja be az erőforráscsoport nevét.  
   * A hely legördülő menüben válassza ki a helyet.  
   * Egyéb beállításokat hagyja alapértelmezett értéken.  
4. A fenti adatok megadása után válassza a **Létrehozás** elemet.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Adja hozzá az Azure Cosmos DB tárelérési kulcsok a Key Vaultban.
1. Keresse meg az előző lépésben, nyissa meg a létrehozott Key Vault a **titkok** fülre.  
2. Válassza ki **+ létrehozás/importálás**, 

   * Válassza ki **manuális** a **feltöltési beállítások**.
   * Adjon meg egy **neve** számára a titkos kód
   * Adja meg a kapcsolati karakterláncot a Cosmos DB-fiók be a **érték** mező. Majd **létrehozás**.

   ![Titkos kulcs létrehozása](./media/access-secrets-from-keyvault/create-a-secret.png)

4. A titkos kulcs létrehozása után nyissa meg azt, és másolja a ** titkos kód azonosítója, amely a következő formátumban van. A következő szakaszban ezt az azonosítót fogja használni. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Azure-alapú webes alkalmazás létrehozása

1. Azure-webalkalmazások létrehozása, vagy letöltheti az alkalmazásból a [GitHub-adattár](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo). Egy egyszerű MVC alkalmazást.  

2. Csomagolja ki a letöltött alkalmazást, és nyissa meg a **HomeController.cs** fájlt. Frissítse a titkos kód azonosítója a következő sorban:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Mentés** a fájl **összeállítása** a megoldás.  
4. Ezután telepítse az alkalmazást az Azure-bA. Kattintson jobb gombbal a projektre, és válassza a **közzététele**. Hozzon létre egy új app service profilt (az alkalmazás WebAppKeyVault1 is neve), és válassza ki **közzététel**.   

5. Miután a rendszer telepíti az alkalmazást. Az Azure Portalon lépjen a üzembe helyezett Azure web App, és kapcsolja be a **felügyeltszolgáltatás-identitás** az alkalmazás.  

   ![Managed Service Identity](./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png)

Most az alkalmazást szeretné futtatni, látni fogja a következő hibát, ahogy bármilyen engedéllyel ehhez az alkalmazáshoz a Key Vault nem ad.

![Alkalmazás-hozzáférés nélküli telepítése](./media/access-secrets-from-keyvault/app-deployed-without-access.png)

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Regisztrálnia kell az alkalmazást, és olvassa el a Key Vault szükséges engedélyek biztosítása

Ebben a szakaszban az alkalmazás regisztrálása az Azure Active Directoryval, és az alkalmazás olvassa el a Key Vault-engedélyt. 

1. Keresse meg az Azure Portalra, nyissa meg a **Key Vault** az előző szakaszban létrehozott.  

2. Nyissa meg **hozzáférési házirendek**válassza **+ új hozzáadása** keresse meg az üzembe helyezett webalkalmazás, válassza ki az engedélyeket, válassza ki **OK**.  

   ![Hozzáférési szabályzat hozzáadása](./media/access-secrets-from-keyvault/add-access-policy.png)

Most futtassa az alkalmazást, a titkos kulcsot a Key vaultból érheti el.

![Titkos kulcs használatával üzembe helyezett alkalmazás](./media/access-secrets-from-keyvault/app-deployed-with-access.png)
 
Hasonlóképpen a felhasználók számára a key Vault is hozzáadhat. Válassza a Key Vault hozzá saját magának kell **hozzáférési szabályzatok** és majd az összes szükséges engedélyek biztosítása a Visual studióból az alkalmazás futtatásához szükséges. Ha az alkalmazás fut, az asztalon, az identitás vesz igénybe.

## <a name="next-steps"></a>További lépések

* Az Azure Cosmos DB-hez lásd: a tűzfal konfigurálása [tűzfal-támogatás](firewall-support.md) cikk.
* Virtuális hálózati szolgáltatásvégpont beállítása: [biztonságos hozzáférés a virtuális hálózati szolgáltatásvégpont](vnet-service-endpoint.md) cikk.
