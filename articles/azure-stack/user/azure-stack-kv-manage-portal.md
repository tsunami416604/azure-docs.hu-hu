---
title: "A verem Azure Key Vault kezeléséhez a portál használatával |} Microsoft Docs"
description: "Megtudhatja, hogyan kezelheti a verem Azure Key Vault a portál használatával"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: D4300668-461F-45F6-BF3B-33B502C39D17
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: mabrigg
ms.openlocfilehash: d76a1e188c5a5bf008ac2fba9b43741a6a8d97b1
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>A verem Azure Key Vault kezeléséhez a portál használatával

Kezelheti a verem Azure Key Vault a verem Azure portál használatával. Ez a cikk segít létrehozása és kezelése az Azure-készletben kulcstároló első lépései. 

## <a name="prerequisites"></a>Előfeltételek  

Az ajánlat, amely tartalmazza az Azure Key Vault szolgáltatás elő kell fizetnie.
 
## <a name="create-a-key-vault"></a>Kulcstartó létrehozása 

1. Jelentkezzen be a [felhasználói portál](https://portal.local.azurestack.external).  

2. Válassza ki az irányítópultról **új** > **biztonság + identitás szakaszában** > **Key Vault**.  

    ![Key Vault képernyő](media/azure-stack-kv-manage-portal/image1.png)  

3. Az a **kulcstároló létrehozása** ablaktáblán, rendelje hozzá egy **neve** a tároló számára. Tároló neve csak alfanumerikus karaktereket és a speciális karakterből álló kötőjel (-) tartalmazhat. Ezek nem kezdődhet számmal.  

4. Válasszon egy **előfizetés** az elérhető előfizetések listájából. A Key Vault szolgáltatás kínáló előfizetéseket a legördülő listában jelennek meg.  

5. Válasszon ki egy létező **erőforráscsoport** vagy hozzon létre egy újat.  

6. Válassza ki a **tarifacsomag**.  
    >[!NOTE]
    > Azure verem szoftverfejlesztői készlet támogatásában tárolók kulcs **szabványos** csak a termékváltozat.

7. Válasszon egyet a meglévő **hozzáférési házirendek** vagy hozzon létre egy újat. Hozzáférési házirend lehetővé teszi a felhasználó, az alkalmazás vagy a biztonsági csoport a jelen tárolóban való műveletek elvégzésére vonatkozó engedélyeket.  

8. Másik lehetőségként válasszon egy **speciális hozzáférési házirend** ahhoz, hogy a szolgáltatások, például a virtuális gépek (VM) központi telepítés elérésére el a Resource Manager sablon-üzembehelyezés, és az Azure Disk Encryption kötettitkosítást hozzáférési. 
  
9.  A beállítások konfigurálása után válassza ki **OK**, majd válassza ki **létrehozása**. Ekkor elindul a kulcstároló-telepítésben. 

## <a name="manage-keys-and-secrets"></a>Kulcsok és titkos kulcsok kezelése

Tároló létrehozása után a következő lépésekkel hozhatja létre és kezelheti a kulcsok és titkos belül a tárolóban.

### <a name="create-a-key"></a>Kulcs létrehozása

1. Jelentkezzen be a [felhasználói portál](https://portal.local.azurestack.external).  

2. Az irányítópulton, válassza ki a **összes erőforrás**, válassza ki a korábban létrehozott kulcstartó, majd a **kulcsok** csempére.  

3. Az a **kulcsok** ablaktáblán válassza előbb **Hozzáadás**. 

4. Az a **hozható létre kulcs** ablaktáblában közül **beállítások**, válassza ki a kulcs létrehozásához használni kívánt módszert. Is **Generate** egy új kulcsot, **feltöltése** egy meglévő kulcs, vagy használjon **biztonsági másolat visszaállítása** egy kulcs biztonsági másolatának kiválasztásához.  

5. Adjon meg egy **neve** a kulcshoz. A kulcs neve csak alfanumerikus karaktereket és a speciális karakterek kötőjel (-) tartalmazhat.  

6. Nem kötelező lépésként beállíthatja az **aktiválási dátuma** és **lejárati dátuma** a kulcs értékeit.  

7. Válassza ki **létrehozása** a telepítés elindításához.  

A kulcs sikeres létrehozása után kiválaszthatja azt a **kulcsok** és tulajdonságainak megtekintése vagy módosítása a. A Tulajdonságok szakaszának tartalmazza a **kulcsazonosító**, amelyen egy egységes erőforrás-azonosító (URI) amely külső alkalmazások férhetnek hozzá ezt a kulcsot. A kulcs műveletek korlátozása érdekében adja meg a beállításokat a **engedélyezett műveletek**.

![URI-kulcs](media/azure-stack-kv-manage-portal/image4.png)  

### <a name="create-a-secret"></a>Titkos kulcs létrehozása 

1. Jelentkezzen be a [felhasználói portál](https://portal.local.azurestack.external).  
2. Az irányítópulton, válassza ki a **összes erőforrás**, válassza ki a korábban létrehozott kulcstartó, majd a **titkok** csempére.  

3. A **titkok**, jelölje be **Hozzáadás**.  

4. A **titkos kulcs létrehozása**, közül **feltöltési beállításokat**, válasszon egy lehetőséget, amellyel titkos kulcs létrehozásához kíván. A titkos kulcsot is létrehozhat **manuálisan** Ha megad egy értéket a titkos kulcs vagy feltöltés egy **tanúsítvány** a helyi számítógépről.  

5. Adjon meg egy **neve** a a titkos kulcsot. A titkos név csak alfanumerikus karaktereket és a speciális karakterek kötőjel (-) tartalmazhat.  

6. Opcionálisan meghatározhatja a **tartalomtípus**, és konfigurálja az értékeket **aktiválási dátuma** és **lejárati dátuma** a titkos kulcsot a.  

7. Válassza ki **létrehozása** a telepítés elindításához.  

A titkos kulcs sikeres létrehozása után kiválaszthatja azt a **titkok** és tulajdonságainak megtekintése vagy módosítása a. A Tulajdonságok szakaszának tartalmaz egy **titkos kulcs azonosítója**, amely, amely külső alkalmazások férhetnek hozzá a titkos kódok URI. 

![URI titkos kulcs](media/azure-stack-kv-manage-portal/image5.png) 


## <a name="next-steps"></a>Következő lépések
* [A Key Vault tárolt jelszó lekérésével egy virtuális gép üzembe helyezése](azure-stack-kv-deploy-vm-with-secret.md) 
* [A Key Vault tárolt tanúsítvány egy virtuális gép üzembe helyezése](azure-stack-kv-push-secret-into-vm.md)     


